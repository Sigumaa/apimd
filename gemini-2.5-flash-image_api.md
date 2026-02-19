# Gemini 2.5 Flash Image API specification

## Model identifiers

### Gemini API (Google AI for Developers)
- Model code (stable): `gemini-2.5-flash-image`.
- Model code (deprecated): `gemini-2.5-flash-image-preview`.
- Supported inputs: images and text.
- Supported outputs: images and text.
- Token limits:
  - Maximum input tokens: 65,536.
  - Maximum output tokens: 32,768.
- Capability flags (Gemini API):
  - Image generation: supported.
  - Batch API: supported.
  - Caching: supported.
  - Structured outputs: supported.
  - Search grounding: not supported.
  - Thinking: not supported.
  - Function calling: not supported.
  - Code execution: not supported.
  - Live API: not supported.
  - URL context: not supported.

### Vertex AI (Generative AI on Vertex AI)
- Model name: Gemini 2.5 Flash Image.
- Technical specifications (images):
  - Maximum images per prompt: 3.
  - Maximum file size per file for inline data or direct uploads through the console: 7 MB.
  - Maximum file size per file from Google Cloud Storage: 30 MB.
  - Maximum number of output images per prompt: 10.
  - Supported aspect ratios: `1:1`, `3:2`, `2:3`, `3:4`, `4:3`, `4:5`, `5:4`, `9:16`, `16:9`, `21:9`.
  - Supported MIME types: `image/png`, `image/jpeg`, `image/webp`, `image/heic`, `image/heif`.
- Parameter defaults (Vertex AI):
  - `temperature`: 0.0–2.0 (default 1.0).
  - `topP`: 0.0–1.0 (default 0.95).
  - `topK`: 64 (fixed).
  - `candidateCount`: 1.

## Common image-generation notes

- Nano Banana refers to Gemini native image generation capabilities and includes Gemini 2.5 Flash Image (`gemini-2.5-flash-image`).
- All generated images include a SynthID watermark.

## Gemini API (Google AI for Developers) — REST

### Authentication
- Requests must include an API key in the `x-goog-api-key` header.

### Endpoints
- Non-streaming:
  - `POST https://generativelanguage.googleapis.com/v1beta/{model=models/*}:generateContent`
- Streaming (SSE):
  - `POST https://generativelanguage.googleapis.com/v1beta/{model=models/*}:streamGenerateContent`

### Path parameter
- `model` (string, required):
  - Format: `models/{model}`.
  - Example for this model: `models/gemini-2.5-flash-image`.

### Request body (relevant fields for image generation)
- `contents[]` (required): conversation content.
  - Each item is a `Content` object with:
    - `role` (optional): e.g. `user`, `model`.
    - `parts[]` (required): ordered list of `Part` objects.
- `generationConfig` (optional): generation configuration.
  - For image generation, examples use:
    - `responseModalities`: e.g. `["TEXT","IMAGE"]` or `["Image"]`.
    - `imageConfig`:
      - `aspectRatio`: e.g. `"16:9"`.
- `tools[]` and `toolConfig` exist in the API, but this model is documented as not supporting Search grounding.

### Part types used for images
- Text:
  - `{"text": "..."}`
- Inline image bytes (base64):
  - `{"inline_data": {"mime_type": "image/png", "data": "<base64>"}}`

### Response body (image parts)
- `GenerateContentResponse.candidates[]` contains model outputs.
- Image bytes are returned as inline data on parts (SDK examples read `part.inlineData.data` and decode base64).

### Example: text-to-image
```bash
curl -s -X POST \
  "https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash-image:generateContent" \
  -H "x-goog-api-key: $GEMINI_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "contents": [{
      "parts": [{"text": "Create a picture of a nano banana dish in a fancy restaurant with a Gemini theme"}]
    }]
  }'
```

### Example: request image-only modality
```bash
curl -s -X POST \
  "https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash-image:generateContent" \
  -H "x-goog-api-key: $GEMINI_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "contents": [{
      "parts": [{"text": "Create a picture of a nano banana dish in a fancy restaurant with a Gemini theme"}]
    }],
    "generationConfig": {
      "responseModalities": ["Image"]
    }
  }'
```

### Example: set aspect ratio
- Default behavior documented: output image size matches the input image size (if an input image is provided), otherwise it generates 1:1 squares.
```bash
curl -s -X POST \
  "https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash-image:generateContent" \
  -H "x-goog-api-key: $GEMINI_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "contents": [{
      "parts": [{"text": "Create a picture of a nano banana dish in a fancy restaurant with a Gemini theme"}]
    }],
    "generationConfig": {
      "imageConfig": {
        "aspectRatio": "16:9"
      }
    }
  }'
```

### Example: image editing / transformation (provide an input image)
```bash
curl -s -X POST \
  "https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash-image:generateContent" \
  -H "x-goog-api-key: $GEMINI_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "contents": [{
      "parts": [
        {"text": "Update this infographic to be in Spanish. Do not change any other elements of the image."},
        {"inline_data": {"mime_type": "image/png", "data": "<BASE64_IMAGE>"}}
      ]
    }]
  }'
```

### Streaming notes (SSE)
- Endpoint: `https://generativelanguage.googleapis.com/v1beta/{model=models/*}:streamGenerateContent`.
- The method documentation provides shell examples using `?alt=sse&key=$GEMINI_API_KEY`.

## Image aspect ratios, output resolution, and tokens (Gemini API)

- Gemini 2.5 Flash Image output table:

| Aspect ratio | Resolution | Tokens |
|---|---:|---:|
| 1:1 | 1024×1024 | 1290 |
| 2:3 | 832×1248 | 1290 |
| 3:2 | 1248×832 | 1290 |
| 3:4 | 864×1184 | 1290 |
| 4:3 | 1184×864 | 1290 |
| 4:5 | 896×1152 | 1290 |
| 5:4 | 1152×896 | 1290 |
| 9:16 | 768×1344 | 1290 |
| 16:9 | 1344×768 | 1290 |
| 21:9 | 1536×672 | 1290 |

## Vertex AI — REST (generateContent)

### Endpoint
- `POST https://aiplatform.googleapis.com/v1/{model}:generateContent`
- `{model}` is a full resource name:
  - `projects/{PROJECT}/locations/{LOCATION}/publishers/google/models/{MODEL}`

### Request body (field list from Vertex AI reference)
- `contents[]` (required)
- `cachedContent` (optional)
- `tools[]` (optional)
- `toolConfig` (optional)
- `labels` (optional)
- `safetySettings[]` (optional)
- `modelArmorConfig` (optional)
- `generationConfig` (optional)
- `systemInstruction` (optional)

### Image input (Vertex AI JSON field names)
- Inline image bytes use `inlineData` with `mimeType` and base64 `data`:
```json
{
  "contents": [
    {
      "role": "user",
      "parts": [
        { "text": "Describe this image." },
        { "inlineData": { "mimeType": "image/png", "data": "<BASE64_IMAGE>" } }
      ]
    }
  ]
}
```

## Sources
```text
https://ai.google.dev/gemini-api/docs/models/gemini-2.5-flash-image
https://ai.google.dev/gemini-api/docs/image-generation
https://ai.google.dev/api/generate-content
https://docs.cloud.google.com/vertex-ai/generative-ai/docs/models/gemini/2-5-flash-image
https://cloud.google.com/vertex-ai/generative-ai/docs/model-reference/inference
https://cloud.google.com/vertex-ai/generative-ai/docs/model-reference/gemini
```
