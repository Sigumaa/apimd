# Gemini 3 Pro Image API specification

## Model identifiers

### Gemini API (Google AI for Developers)
- Model code (preview): `gemini-3-pro-image-preview`.
- Supported inputs: image and text.
- Supported outputs: image and text.
- Token limits:
  - Maximum input tokens: 65,536.
  - Maximum output tokens: 32,768.
- Capability flags (Gemini API):
  - Image generation: supported.
  - Batch API: supported.
  - Caching: not supported.
  - Structured outputs: supported.
  - Search grounding: supported.
  - Thinking: supported.
  - Function calling: not supported.
  - Code execution: not supported.
  - Live API: not supported.
  - URL context: not supported.

### Vertex AI (Generative AI on Vertex AI)
- Model name: Gemini 3 Pro Image.
- Model ID: `gemini-3-pro-image-preview`.
- Supported inputs and outputs:
  - Inputs: text, images.
  - Outputs: text and image.
- Token limits:
  - Maximum input tokens: 65,536.
  - Maximum output tokens: 32,768.

## Common image-generation notes

- Nano Banana refers to Gemini native image generation capabilities and includes Gemini 3 Pro Image Preview (`gemini-3-pro-image-preview`).
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
  - Example for this model: `models/gemini-3-pro-image-preview`.

### Request body (relevant fields for image generation)
- `contents[]` (required): conversation content.
  - Each item is a `Content` object with:
    - `role` (optional): e.g. `user`, `model`.
    - `parts[]` (required): ordered list of `Part` objects.
- `generationConfig` (optional): generation configuration.
  - Examples use:
    - `responseModalities`: `["TEXT","IMAGE"]`.
    - `imageConfig`:
      - `aspectRatio`: e.g. `"16:9"`.
      - `imageSize`: `"1K" | "2K" | "4K"`.
        - Gemini 3 Pro Image generates 1K images by default.
        - `imageSize` requires an uppercase `K` (e.g. `1K`, `2K`, `4K`).

### Part types used for images
- Text:
  - `{"text": "..."}`
- Inline image bytes (base64):
  - `{"inline_data": {"mime_type": "image/png", "data": "<base64>"}}`

### Response body (image parts)
- `GenerateContentResponse.candidates[]` contains model outputs.
- Image bytes are returned as inline data on parts (SDK examples read `part.inlineData.data` and decode base64).

### Example: text-to-image with aspect ratio + image size
```bash
curl -s -X POST \
  "https://generativelanguage.googleapis.com/v1beta/models/gemini-3-pro-image-preview:generateContent" \
  -H "x-goog-api-key: $GEMINI_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "contents": [{
      "parts": [{"text": "Create a picture of a nano banana dish in a fancy restaurant with a Gemini theme"}]
    }],
    "generationConfig": {
      "imageConfig": {
        "aspectRatio": "16:9",
        "imageSize": "2K"
      }
    }
  }'
```

### Example: image editing / transformation (provide an input image)
```bash
curl -s -X POST \
  "https://generativelanguage.googleapis.com/v1beta/models/gemini-3-pro-image-preview:generateContent" \
  -H "x-goog-api-key: $GEMINI_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "contents": [{
      "parts": [
        {"text": "Update this infographic to be in Spanish. Do not change any other elements of the image."},
        {"inline_data": {"mime_type": "image/png", "data": "<BASE64_IMAGE>"}}
      ]
    }],
    "generationConfig": {
      "responseModalities": ["TEXT", "IMAGE"],
      "imageConfig": {
        "aspectRatio": "16:9",
        "imageSize": "2K"
      }
    }
  }'
```

### Search grounding with Google Search tool (Gemini API)
- The image generation guide shows enabling Google Search via:
  - `tools`: `[{"google_search": {}}]`.
- The guide states the response includes `groundingMetadata` with required fields:
  - `searchEntryPoint` (HTML and CSS to render required search suggestions).
  - `groundingChunks` (top 3 web sources used to ground the generated image).

## Image aspect ratios, output resolution, and tokens (Gemini API)

- Gemini 3 Pro Image Preview output table:

| Aspect ratio | 1K resolution | 1K tokens | 2K resolution | 2K tokens | 4K resolution | 4K tokens |
|---|---:|---:|---:|---:|---:|---:|
| 1:1 | 1024×1024 | 1120 | 2048×2048 | 1120 | 4096×4096 | 2000 |
| 2:3 | 848×1264 | 1120 | 1696×2528 | 1120 | 3392×5056 | 2000 |
| 3:2 | 1264×848 | 1120 | 2528×1696 | 1120 | 5056×3392 | 2000 |
| 3:4 | 896×1200 | 1120 | 1792×2400 | 1120 | 3584×4800 | 2000 |
| 4:3 | 1200×896 | 1120 | 2400×1792 | 1120 | 4800×3584 | 2000 |
| 4:5 | 928×1152 | 1120 | 1856×2304 | 1120 | 3712×4608 | 2000 |
| 5:4 | 1152×928 | 1120 | 2304×1856 | 1120 | 4608×3712 | 2000 |
| 9:16 | 768×1376 | 1120 | 1536×2752 | 1120 | 3072×5504 | 2000 |
| 16:9 | 1376×768 | 1120 | 2752×1536 | 1120 | 5504×3072 | 2000 |
| 21:9 | 1584×672 | 1120 | 3168×1344 | 1120 | 6336×2688 | 2000 |

## Vertex AI — REST (generateContent)

### Endpoint
- `POST https://aiplatform.googleapis.com/v1/{model}:generateContent`
- `{model}` is a full resource name:
  - `projects/{PROJECT}/locations/{LOCATION}/publishers/google/models/{MODEL}`
  - For this model, `{MODEL}` is `gemini-3-pro-image-preview`.

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

### generationConfig.imageConfig (Vertex AI)
- `aspectRatio` (string): supported values `1:1`, `9:16`, `16:9`, `3:4`, `4:3`, `2:3`, `3:2`, `4:5`, `5:4`, `21:9`.
- `personGeneration` (string): `DONT_ALLOW`, `ALLOW_ADULT`, `ALLOW_ALL`.
- `imageSize` (string): `1K`, `2K`, `4K` (default `1K` if not specified).
- `imageOutputOptions`:
  - `mimeType` (string): `image/jpeg`, `image/png`.
  - `compressionQuality` (integer): 0–100.

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
https://ai.google.dev/gemini-api/docs/models/gemini-3-pro-image-preview
https://ai.google.dev/gemini-api/docs/image-generation
https://ai.google.dev/api/generate-content
https://docs.cloud.google.com/vertex-ai/generative-ai/docs/models/gemini/3-pro-image
https://cloud.google.com/vertex-ai/generative-ai/docs/model-reference/inference
https://cloud.google.com/vertex-ai/generative-ai/docs/model-reference/gemini
```
