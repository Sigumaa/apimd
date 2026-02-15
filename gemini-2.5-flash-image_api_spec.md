# Gemini 2.5 Flash Image API仕様まとめ（Gemini API / Vertex AI）

最終確認日: 2026-02-15（JST）
対象モデル: `gemini-2.5-flash-image`（別名: Gemini 2.5 Flash Image / Nano Banana）

この文書は、Web検索が使えない環境の実装エージェントが、Gemini 2.5 Flash Image を「HTTPで呼び出して画像を生成・編集し、応答から画像バイト列を取り出す」までに必要な最低限の仕様を、一次情報に寄せて整理したものです。

---

## 1. モデルの概要（できること・できないこと）

Gemini 2.5 Flash Image は、テキストと画像を入力に取り、テキストと画像（画像データを含む）を出力できるモデルです（画像生成と編集が主目的）。  
Vertex AI では「画像理解と生成に最適化」「画像生成・編集を高速に行う」ことが明記されています。  

Vertex AI（Google Cloud）側での主要スペックは次の通りです。

- モデルID: `gemini-2.5-flash-image`
- 入力: Text, Images / 出力: Text and image
- 画像生成コスト: 生成画像 1枚あたり 1290 トークン消費
- トークン上限: 入力 32,768 / 出力 32,768
- 画像入力の上限（推奨/制約）: 1プロンプトあたり最大 3枚
- 出力画像の上限: 1プロンプトあたり最大 10枚
- 対応アスペクト比: 1:1, 3:2, 2:3, 3:4, 4:3, 4:5, 5:4, 9:16, 16:9, 21:9
- 画像入力MIME: `image/png`, `image/jpeg`, `image/webp`, `image/heic`, `image/heif`
- 既定パラメータ: temperature 既定 1.0（0.0–2.0）, topP 既定 0.95（0.0–1.0）, topK 64（固定）, candidateCount 1  
- リリース/ライフサイクル（Vertex AI）: GA、Release date 2025-10-02、Discontinuation date 2026-10-02（ページ最終更新 2026-02-14 UTC）

また Vertex AI では本モデルに対し、少なくとも次の機能が「Not supported」として列挙されています（実装で誤って依存しないこと）。

- Grounding with Google Search
- Code execution
- Function calling
- Gemini Live API
- Thinking
- （Implicit/Explicit）context caching
- Vertex AI RAG Engine
- Chat completions

※ ただし、`generateContent` の `contents[]` に会話履歴を積む「マルチターン」自体は API 仕様として存在し、単発/複数ターンのどちらも `contents[]` の繰り返しで表現します（後述）。

---

## 2. 重要な注意（モデル名の移行）

Vertex AI の「Gemini を使用した画像の生成と編集」ドキュメントでは、次のプレビューモデルが 2025-10-31 に廃止され、停止回避のため `gemini-2.5-flash-image` へ移行するよう記載があります。

- `gemini-2.0-flash-preview-image-generation`
- `gemini-2.5-flash-image-preview`

運用・実装では、原則として `gemini-2.5-flash-image` を指定します。

---

## 3. 出力画像のサイズ（アスペクト比ごとの解像度）

Gemini API（Google AI for Developers）の画像生成ガイドには、Gemini 2.5 Flash Image のアスペクト比と解像度、トークン（1290）が表として掲載されています。  
本モデルは 1024px系（アスペクト比に応じた解像度）で生成されます。

| Aspect ratio | Resolution | Tokens |
|---:|---:|---:|
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



### 3.1 生成画像のウォーターマーク

Gemini API の画像生成ガイドでは、生成されたすべての画像に SynthID のウォーターマークが含まれる旨が記載されています。プロダクト要件（透かし/真正性表示の扱い）に影響するため、利用側で前提にしておくのが安全です。

### 3.2 `aspectRatio` を省略したときの既定

Gemini API のガイドでは、`imageConfig.aspectRatio` を指定しない場合、入力に画像が含まれていれば入力画像のアスペクト比に合わせ、画像が無い場合は 1:1 になる旨が記載されています。


---

## 4. 共通データモデル（リクエスト/レスポンスの骨格）

Gemini API（開発者向け）も Vertex AI も、基本的に「会話＝`contents[]`」として扱います。

### 4.1 `Content` / `Part` の考え方

`contents[]` は会話履歴＋最新の要求を表します。単発なら 1件、複数ターンなら user/model の履歴を順に並べます。  
各 `Content` は `role` と `parts[]` を持ち、`parts[]` の各要素が「テキスト」または「画像などのバイナリ」を持ちます。

`Part` は union（どれか1つ）で、少なくとも次がよく使われます。

- `text`: 文字列
- `inlineData`: `{ mimeType, data }`（Base64エンコードされたバイナリ）
- `fileData`: `{ mimeType, fileUri }`（URI参照。Vertex AI で Cloud Storage URI を使う場合など）

重要: 画像出力は、レスポンスの `candidates[i].content.parts[]` のどこかに `inlineData`（または同等のフィールド名）として含まれます。  
「テキスト部」と「画像部」が混在することがあるので、parts を順に走査して、`text` と `inlineData` の両方を扱う実装にしておくのが安全です。

### 4.2 フィールド名の表記ゆれ（snake_case / camelCase）

公式ドキュメントの例では、同じ概念でも次のように表記が混在します。

- `inlineData` / `inline_data`
- `mimeType` / `mime_type`

SDK（Python/JS）では言語の慣習に合わせた属性名で扱える一方、REST JSON ではどちらの書き方が提示される例もあります。  
実装側で JSON を組み立てる場合は、まず「公式の curl 例に出ている形（`inline_data` / `mime_type` を含む）」に合わせるのが無難です。

---

## 5. Gemini API（開発者向け）での呼び出し仕様（generativelanguage.googleapis.com）

### 5.1 認証（API Key）

Gemini API は API Key を用います。HTTP では少なくとも次のいずれかが利用されます。

- ヘッダ: `x-goog-api-key: <GEMINI_API_KEY>`
- もしくはクエリ: `?key=<GEMINI_API_KEY>`（ページ例にも登場）

### 5.2 エンドポイント

`generateContent` を使います（画像生成もこのメソッドで行います）。

- `POST https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash-image:generateContent`

### 5.3 画像生成（テキスト→画像）の最小リクエスト（REST）

ポイントは `generationConfig.responseModalities` に `Image`（または `IMAGE` 相当）を含めることです。

Gemini API のガイドでは、既定ではテキストと画像の両方を生成し得るとされており、`responseModalities` を指定して画像のみ（または画像+テキスト）に制御できます。画像が必須のワークフローでは、`responseModalities` を明示しておくほうが安定します。  
指定しないとテキストだけ返ることがあるため、画像が欲しい場合は必ず明示します。

```bash
curl -s -X POST \
  "https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash-image:generateContent" \
  -H "x-goog-api-key: $GEMINI_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "contents": [{
      "parts": [
        {"text": "Create a picture of a nano banana dish in a fancy restaurant with a Gemini theme"}
      ]
    }],
    "generationConfig": {
      "responseModalities": ["Image"],
      "imageConfig": {
        "aspectRatio": "3:4"
      }
    }
  }'
```

### 5.4 画像編集（画像+指示テキスト→編集済み画像）のリクエスト（REST）

同じ `generateContent` で、入力 `parts` に画像（Base64）を入れます。  
`inline_data.data` は Base64（バイナリ画像のエンコード）です。

```bash
curl -s -X POST \
  "https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash-image:generateContent" \
  -H "x-goog-api-key: $GEMINI_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "contents": [{
      "parts": [
        { "text": "Edit this image: make the car a convertible, keep the same color." },
        {
          "inline_data": {
            "mime_type": "image/png",
            "data": "'$(base64 -w 0 input.png)'"
          }
        }
      ]
    }],
    "generationConfig": {
      "responseModalities": ["Image"]
    }
  }'
```

### 5.5 レスポンスから画像を取り出す（REST）

レスポンスの代表的な形は次の通りです（概念図）。画像は `inlineData.data`（または `inline_data.data`）に Base64 で入ります。

```json
{
  "candidates": [
    {
      "content": {
        "parts": [
          { "text": "..." },
          {
            "inlineData": {
              "mimeType": "image/png",
              "data": "BASE64..."
            }
          }
        ]
      },
      "finishReason": "STOP"
    }
  ],
  "usageMetadata": { "...": "..." }
}
```

実装例（擬似コード）:

Python（Base64→バイト列→ファイル保存）

```python
import base64

parts = resp["candidates"][0]["content"]["parts"]
for p in parts:
    inline = p.get("inlineData") or p.get("inline_data")
    if not inline:
        continue
    mime = inline.get("mimeType") or inline.get("mime_type") or "image/png"
    data_b64 = inline["data"]
    img_bytes = base64.b64decode(data_b64)
    ext = {"image/png": "png", "image/jpeg": "jpg", "image/webp": "webp"}.get(mime, "bin")
    with open(f"out.{ext}", "wb") as f:
        f.write(img_bytes)
```

Node.js（Base64→Buffer→ファイル保存）

```js
import fs from "node:fs";

const parts = resp.candidates?.[0]?.content?.parts ?? [];
for (const p of parts) {
  const inline = p.inlineData ?? p.inline_data;
  if (!inline) continue;
  const mime = inline.mimeType ?? inline.mime_type ?? "image/png";
  const b64 = inline.data;
  const buf = Buffer.from(b64, "base64");
  const ext = ({ "image/png": "png", "image/jpeg": "jpg", "image/webp": "webp" })[mime] ?? "bin";
  fs.writeFileSync(`out.${ext}`, buf);
}
```


実装上は次の処理が要点です。

- `candidates[0].content.parts` を走査
- `part.inlineData?.data`（または `part.inline_data?.data`）を見つけたら Base64 decode
- `mimeType`（または `mime_type`）を見て拡張子を決めて保存

### 5.6 ブロック/未生成の扱い（安全フィルタ）

Gemini API の `GenerateContentResponse` では、プロンプトがブロックされた場合 `promptFeedback.blockReason` が返ることがあります。  
その場合、`candidates` が無い（または空）になることがあるので、必ず `promptFeedback` と `candidates` の両方をチェックしてください。

---

## 6. Vertex AI（Google Cloud）での呼び出し仕様（aiplatform.googleapis.com）

Vertex AI の Gemini は `projects.locations.publishers.models.generateContent`（`generateContent`）で呼び出します。

### 6.1 認証（ADC / OAuth）

Vertex AI は Google Cloud の認証（ADC / OAuth2）を使うのが基本です。  
HTTP では `Authorization: Bearer <ACCESS_TOKEN>` を付けます（`gcloud auth print-access-token` 等で取得）。

### 6.2 エンドポイント（REST）

Vertex AI REST の `generateContent` は次の形で提示されています。

- `POST https://aiplatform.googleapis.com/v1/{model}:generateContent`

`{model}` にはフルパスを入れます（publisher model の形式）。

- `projects/{project}/locations/{location}/publishers/google/models/gemini-2.5-flash-image`

グローバルエンドポイントを使う場合、ドキュメントでは次のような URL 例が示されています（location を `global` にする）。

```text
https://aiplatform.googleapis.com/v1/projects/test-project/locations/global/publishers/google/models/gemini-2.0-flash-001:generateContent
```

Gemini 2.5 Flash Image を global で呼ぶ場合は、上の `gemini-2.0-flash-001` を `gemini-2.5-flash-image` に置き換えます。

### 6.3 リクエストボディ（Vertex AI）

Vertex AI の「Generate content with the Gemini API in Vertex AI」には、`contents[].parts[]` の union として `text / inlineData / fileData` が掲載されています。  
実装方針は Gemini API とほぼ同じで、画像出力も `inlineData`（Base64）として返ります。

概形:

```json
{
  "contents": [
    {
      "role": "user",
      "parts": [
        { "text": "Generate an image of ..." }
      ]
    }
  ],
  "generationConfig": {
    "candidateCount": 1,
    "temperature": 1.0,
    "topP": 0.95
  }
}
```

画像生成・編集時は、ここに `generationConfig.responseModalities` 相当の指定と、必要ならアスペクト比指定を加えます（Vertex AI Studio でも同種の設定が存在します）。

---

## 7. 実装チェックリスト（オフライン実装でつまずきやすい点）

1) 画像を確実に返してほしい場合は、`responseModalities`（または SDK の同等設定）で画像を明示する。  
2) 応答は「テキストと画像が混在」し得るので、`parts[]` を走査して `text` と `inlineData` をそれぞれ拾う。  
3) 入力画像の上限（Vertex AI）として最大 3枚、ファイルサイズ上限（inline 7MB / GCS 30MB）などがあるため、前処理（リサイズ/圧縮）を準備しておく。  
4) 画像生成 1枚あたり 1290 トークンを消費する。大量生成では予算/クォータに直結する。  
5) ブロック時は `promptFeedback.blockReason` が入り、`candidates` が空になり得るので、成功前提でパースしない。

---

## 8. 参考（一次情報: 公式ドキュメント）

リンクはオフライン環境でクリックできない前提のため、文字列として列挙します。

```text
Gemini API 画像生成（Nano Banana）:
https://ai.google.dev/gemini-api/docs/image-generation

Gemini API GenerateContent（概念・promptFeedback 等）:
https://ai.google.dev/api/generate-content

Vertex AI モデル仕様（Gemini 2.5 Flash Image）:
https://docs.cloud.google.com/vertex-ai/generative-ai/docs/models/gemini/2-5-flash-image

Vertex AI REST: projects.locations.publishers.models.generateContent:
https://docs.cloud.google.com/vertex-ai/generative-ai/docs/reference/rest/v1/projects.locations.publishers.models/generateContent

Vertex AI: Deployments and endpoints（global endpoint URL 例）:
https://docs.cloud.google.com/vertex-ai/generative-ai/docs/learn/locations

Vertex AI: Gemini を使用した画像の生成と編集（プレビュー廃止の注意を含む, 日本語）:
https://docs.cloud.google.com/vertex-ai/generative-ai/docs/multimodal/image-generation?hl=ja
```
