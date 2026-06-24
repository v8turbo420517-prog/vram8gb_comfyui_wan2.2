# Hi there, I'm a Local AI Creator / Architect 👋

I focus on creating 5-second silent cinematic videos using local AI, emphasizing the beauty of subtraction and subtle atmosphere ("ma"). 
This profile serves as a technical portfolio for optimizing heavy AI models on a mid-range hardware setup.

https://github.com/user-attachments/assets/20111eb2-aa5a-4886-83c6-aa9378e0e757

---

## 🛠️ Machine Specifications & Environment
* **OS:** Windows 11 (Optimized for resource management)
* **CPU:** Intel Core i5-12400F
* **GPU:** NVIDIA GeForce RTX 3060 Ti (VRAM 8GB)
* **RAM:** 32GB
* **Display:** Dual monitor setup (including EIZO ColorEdge CG248-4K for professional color management)

## 🚀 Local AI Workflows & Benchmarks
### 🎥 Wan2.2 / Wan2.1 (ComfyUI)
Currently running heavy text-to-video / image-to-video models under strict **VRAM 8GB limitations**.

* **Quantization:** Utilizing Q4 GGUF / NF4 models to bypass OOM (Out of Memory) and maintain stability.
* **VRAM Optimization:** Custom node management and strict VRAM caching rules in ComfyUI (Forge-based optimization logic).
* **Cinematic Video Pipeline:** 
  1. Low-VRAM Generation via ComfyUI (GGUF).
  2. High-fidelity upscaling via **Topaz Video AI**.
  3. Final editing, color management, and pacing adjustments via **Adobe Premiere Pro**.

### 🚀 Optimized Workflows
You can find my custom production workflow in the `workflows/` directory.

#### Wan2.2 Image-to-Video (14B GGUF Split-Sampling)
A highly optimized I2V workflow designed to bypass OOM on 8GB VRAM while retaining high-fidelity motion. It features a dual-KSampler setup that switches between different diffusion models for early and late denoising steps.

### 🔄 2-Stage K-Sampler & Dynamic Model Swapping

To completely bypass OOM (Out of Memory) errors on 8GB VRAM while retaining the high-fidelity motion of the 14B model, this workflow splits the generation into two sequential K-Samplers. 

It dynamically switches between two different GGUF quantization models: a **HighNoise** model for building the initial cinematic structure (early denoising steps), and a **LowNoise** model for refining fine details (late denoising steps).

#### 📊 2-Stage K-Sampler Flow & VRAM Profile

```text
 ── [Total Denoise Steps: 100%] ───────────────────────────────────────┐
 └── Step 0% ─── (HighNoise GGUF) ───► Step 50% ─── (LowNoise GGUF) ───► Step 100% ┘
      [VRAM Optimizations: Model Swap triggers mid-way via Custom Node]
```

* Peak VRAM Usage: < 7.0 GB (Strictly stays within dedicated VRAM)
* Shared System Memory: 0.0 GB (Avoids slow system memory fallback entirely)
* Temporary Cache: Max ~0.3 GB during model swapping, released immediately.
[!NOTE]
When loading the two GGUF models (HighNoise / LowNoise), a temporary 0.3 GB cache is allocated and immediately garbage-collected before the first K-Sampler stage begins its main processing. This avoids the slow Shared System Memory fallback entirely, maintaining peak performance under strict 8GB VRAM limits while completing generation with the 14B model.

* **File (JSON):** `workflows/i2v_wan2.2_14b_lightweight.json`
* **`end_of_the_day.png`** (Input Source Image)<br>
  👉 This is the source image for the Image-to-Video generation. After loading the JSON workflow, select/load this image into the `Load Image` node to generate the video.node to generate the video.
* **Sample Clips:** Located in the `examples/` directory (e.g., `1_columbo_cat.mp4`)
* **Workflow Architecture:**
  ![Workflow Preview](workflows/i2v_wan2.2_14b_lightweight.png)

##### 🧩 Required Custom Nodes
* `ComfyUI-WanVideoWrapper`
* `ComfyUI-GGUF`
* `ComfyUI-VideoHelperSuite`

##### 💾 Required Models & Paths
* **Diffusion Models (GGUF):** `ComfyUI/models/diffusion_models/`
  * `Wan2.2-I2V-14B-HighNoise-Q4_K_M.gguf` (For initial steps)
  * `Wan2.2-I2V-14B-LowNoise-Q4_K_M.gguf` (For refinement steps)
* **Text Encoder:** `ComfyUI/models/text_encoders/umt5_xxl_fp8_e4m3fn_scaled.safetensors` (Type: `wan`)
* **VAE:** `ComfyUI/models/vae/Wan2.1_VAE.safetensors` (Recommended for this setup)
* **CLIP Vision:** `ComfyUI/models/clip_vision/clip-vision_vit-h.safetensors`
* **LoRA** `ComfyUI/models/loras/`
  * `Wan2.2-Lightning_I2V-A14B-4steps-lora_HIGH_fp16.safetensors`
  * `Wan2.2-Lightning_I2V-A14B-4steps-lora_LOW_fp16.safetensors`

---

🛠️ External Tools & Post-Production Settings
1. ComfyUI (Wan2.2) — Generation
* Role: Generates the core motion, composition, and cinematic atmosphere based on the prompt.

* Philosophy: Prioritizes generation speed and VRAM efficiency. It focuses purely on outputting high-potential "raw material" without forced upscaling or frame interpolation at this stage.

2. Topaz Video AI — Enhancement & Upscaling
* AI Model: Artemis (Medium Quality)

* Resolution: 200% (2x) Upscale

* Why this setting: Proteus (Auto) automatically cleans up AI-generated artifacts, flickers, and blurs for each unique clip. A steady 2x upscale enhances cinematic details (like animal fur) naturally without pushing the AI to a breaking point.

3. Premiere Pro — Final Color Grading & Export
* Sequence: Full HD (1920x1080 or 1080x1920) — Apply "Set to Frame Size" (or Match Sequence Settings) to fit the upscaled Topaz source into the Full HD timeline, compressing the pixel density for a crisper look.

* Lumetri Color Profile: Fixes the typical "washed-out" look of AI raw videos by enhancing presence and clarity with these precise values:

  * Exposure: +0.2

  * Contrast: +5

  * Vibrance: +10

  * Saturation: 105

  * Sharpening: +7

* Export Setting: H.264 / Match Source - Adaptive High Bitrate (Ensures reliable high quality and optimal file size for video platforms).

---

## Future Plan & Roadmap
The core workflow is fully completed and optimized. The 3-video gallery is updated weekly to showcase our latest creations. For the full collection of videos, please check our YouTube channel.<br>
▢ Update the 3-video gallery with new character archetypes (Stationmaster, Railway Worker, etc.) every week.

## 🔗 Links
* **Gallery:** [https://v8turbo420517-prog.github.io/vram8gb_comfyui_wan2.2/] (3-Video)
* **YouTube:** [http://www.youtube.com/@VRAM8GB_ComfyUI_Wan2.2] (Cinematic Shorts Archive)

---

## 🇯🇵 プロフィール（日本語）
ローカルAIを活用し、無駄を削ぎ落とした5秒のサイレントシネマ（猫や日常の静寂、間をテーマにした映像作品）を制作しています。

このGitHubでは、「VRAM 8GB」という限られたミドルクラス環境で、最新の重量級動画生成モデル（Wan2.2等）をいかに枯渇（OOM）させず、かつ高品質に回すかという技術的なアプローチとワークフローの挙動を記録しています。

* **主なツール:** ComfyUI, Wan2.2 / Wan2.1, Topaz Video AI, Adobe Premiere Pro

### 🚀 公開ワークフロー
実際の制作に使用しているワークフローファイルを `workflows/` フォルダ内で公開しています。

#### Wan2.2 Image-to-Video (14B GGUF 前後段切り替え軽量化版)
VRAM 8GB環境でのOOMを回避しつつ、妥協のない挙動を得るために設計したI2Vワークフローです。2段構えのKサンプラー（高度）を使用し、動画の骨組みを作る前半（HighNoise）と、ディテールを整える後半（LowNoise）で異なるGGUFモデルを動的に切り替える玄人向けの設計になっています。

#### 🔄 2段階 K-Sampler 処理フローイメージ

```text
 ── [Total Denoise Steps: 100%] ───────────────────────────────────────┐
 └── Step 0% ─── (HighNoise GGUF) ───► Step 50% ─── (LowNoise GGUF) ───► Step 100% ┘
      [VRAM Optimizations: Model Swap triggers mid-way via Custom Node]
```

📊 VRAM & メモリプロファイル (RTX 3060 Ti 8GB 実測値)
* ピーク時VRAM使用量: 7.0 GB 未満 (専用VRAM内に完全に収まります)
* 共有システムメモリ: 0.0 GB (速度低下の原因となるメインメモリへの退避を完全回避)
* 一時キャッシュ: モデル切り替え時に最大約 0.3 GB (処理後、即座に自動解放)
[!NOTE]
2つのGGUFモデル（HighNoise / LowNoise）を読み込む際、一時的に約0.3GBのキャッシュが確保されますが、前段のKサンプラーによる本処理が始まる前にガベージコレクションにより即座に解放されます。これにより、共有システムメモリ（Shared Memory）への低速なフォールバックを完全に回避し、8GB VRAMの限界性能を維持したまま14Bモデルの生成を完走させます。

---

* **配布ファイル (JSON):** `workflows/i2v_wan2.2_14b_lightweight.json`
* **`end_of_the_day.png`** (生成元画像)<br>
  👉 Image-to-Video（動画生成）のベースとなる元画像です。JSONワークフローを読み込んだ後、`Load Image` ノードにこの画像を読み込ませて動画を生成します。
* **生成サンプル動画:** `examples/` フォルダ内に格納しています（例: `1_columbo_cat.mp4`）
* **必要カスタムノード:** `ComfyUI-WanVideoWrapper`, `ComfyUI-GGUF`, `ComfyUI-VideoHelperSuite`
* **使用モデル配置:**
  * 前半用GGUF: `Wan2.2-I2V-14B-HighNoise-Q4_K_M.gguf`
  * 後半用GGUF: `Wan2.2-I2V-14B-LowNoise-Q4_K_M.gguf`
* **Text Encoder:** `ComfyUI/models/text_encoders/umt5_xxl_fp8_e4m3fn_scaled.safetensors` (タイプ: `wan`)
* **VAE:** `ComfyUI/models/vae/Wan2.1_VAE.safetensors` (Recommended for this setup)
* **CLIP Vision:** `ComfyUI/models/clip_vision/clip-vision_vit-h.safetensors`
* **LoRA** `ComfyUI/models/loras/`
  * `Wan2.2-Lightning_I2V-A14B-4steps-lora_HIGH_fp16.safetensors`
  * `Wan2.2-Lightning_I2V-A14B-4steps-lora_LOW_fp16.safetensors`

---

🛠️ 外部ツールの役割と詳細設定
1. ComfyUI (Wan2.2) — Generation（生成）
* 役割: プロンプトに忠実な構図、キャラクターの動き、空気感のベース生成。

* 設計思想: 生成速度とVRAM効率を最優先し、この段階では無理な高解像度化や補間は行いません。ポテンシャルの高い「ピュアな生素材」を出力することに特化させています。

2. Topaz Video AI — Enhancement & Upscaling（高画質化）
* AIモデル: Artemis (Medium Quality)

* 解像度: 200% (2倍) アップスケール

* 選定理由: AI生成動画はカットごとにノイズやボケ具合が異なります。Proteusのオート機能を使うことで、動画全体のフリッカー（ちらつき）や破綻を自動で抑えつつ、被写体（動物の毛並みなど）の微細なディテールを自然に引き出すことができます。

3. Premiere Pro — Final Color Grading & Export（編集・書き出し）
*シーケンス設定: フルHD (1920x1080または1080x1920)

* タイムライン配置後、「フレームサイズに合わせる」を適用。Topazで2倍にした高密度な映像をフルHDに凝縮させることで、よりくっきりとしたディテール感を生み出します。

* Lumetriカラー設定: AI動画特有の「眠い（コントラストや発色が弱い）印象」を解消し、映像の存在感を決定づける基本プロファイルです。

  * 露出量: +0.2 (透明感と明るさをプラス)

  * コントラスト: +5 (立体感を強調)

  * 自然な彩度: +10 (くすんだ色味を自然に底上げ)

  * 彩度: 105 (全体のトーンを鮮やかに固定)

  * シャープネス: +7 (輪郭線をキュッと引き締める)

* 書き出し設定: H.264 / ソースの一致 - アダプティブビットレート（高） (各種プラットフォームに最適化された、破綻のない高画質書き出し)

---

## 今後の展開 / ロードマップ
コアとなるワークフローはこれで完全に完成・最適化されています。3つの動画ギャラリーは週1回の頻度で更新されます。過去のアーカイブやその他の動画は、YouTubeチャンネル側でご覧いただけます。<br>
▢ 3つの動画ギャラリーを、新しいキャラクターアーキタイプ（駅長・鉄道員など）に毎週入れ替えて公開していきます。
  
## 🔗 リンク
* **ギャラリー:** [https://v8turbo420517-prog.github.io/vram8gb_comfyui_wan2.2/] (3本の動画)
* **YouTube:** [http://www.youtube.com/@VRAM8GB_ComfyUI_Wan2.2] (Cinematic Shorts Archive)
