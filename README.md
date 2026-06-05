# Hi there, I'm a Local AI Creator / Architect 👋

I focus on creating 5-second silent cinematic videos using local AI, emphasizing the beauty of subtraction and subtle atmosphere ("ma"). 
This profile serves as a technical portfolio for optimizing heavy AI models on a mid-range hardware setup.

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
 └── Step 0% ─── (HighNoise GGUF) ───► Step 40% ─── (LowNoise GGUF) ───► Step 100% ┘
      [VRAM Optimizations: Model Swap triggers mid-way via Custom Node]
```

* Peak VRAM Usage: < 7.0 GB (Strictly stays within dedicated VRAM)
* Shared System Memory: 0.0 GB (Avoids slow system memory fallback entirely)
* Temporary Cache: Max ~0.3 GB during model swapping, released immediately.
[!NOTE]
By dynamic swapping between the two GGUF models at the 40% mark, this workflow prevents the slow Shared System Memory fallback. A temporary 0.3 GB cache is allocated during the swap but is immediately garbage-collected, maintaining peak performance under strict 8GB VRAM limits.

* **File (JSON):** `workflows/i2v_wan2.2_14b_lightweight.json`
* **File (With Embedded Metadata):** `workflows/i2v_wan2.2_14b_lightweight.png` *(You can drag & drop this image directly into ComfyUI)*
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
* **LoRA** `ComfyUI/models/loras/lightx2v_I2V_14B_480p_cfg_step_distill_rank32_bf16.safetensors`

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
 └── Step 0% ─── (HighNoise GGUF) ───► Step 40% ─── (LowNoise GGUF) ───► Step 100% ┘
      [VRAM Optimizations: Model Swap triggers mid-way via Custom Node]
```

📊 VRAM & メモリプロファイル (RTX 3060 Ti 8GB 実測値)
* ピーク時VRAM使用量: 7.0 GB 未満 (専用VRAM内に完全に収まります)
* 共有システムメモリ: 0.0 GB (速度低下の原因となるメインメモリへの退避を完全回避)
* 一時キャッシュ: モデル切り替え時に最大約 0.3 GB (処理後、即座に自動解放)
[!NOTE]
40%のステップを境に前後段でモデルを動的に切り替える（スワップする）際、一時的に約0.3GBのキャッシュが確保されますが、ガベージコレクションにより即座に解放されます。これにより、共有システムメモリ（Shared Memory）への低速なスワップを完全に回避し、8GB VRAMの限界性能を維持したまま14Bモデルの生成を完走させます。

---

* **配布ファイル (JSON):** `workflows/i2v_wan2.2_14b_lightweight.json`
* **配布ファイル (メタデータ内包PNG):** `workflows/i2v_wan2.2_14b_lightweight.png` *(この画像をComfyUIの画面に直接ドラッグ＆ドロップするだけでワークフローを復元できます)*
* **生成サンプル動画:** `examples/` フォルダ内に格納しています（例: `1_columbo_cat.mp4`）
* **必要カスタムノード:** `ComfyUI-WanVideoWrapper`, `ComfyUI-GGUF`, `ComfyUI-VideoHelperSuite`
* **使用モデル配置:**
  * 前半用GGUF: `Wan2.2-I2V-14B-HighNoise-Q4_K_M.gguf`
  * 後半用GGUF: `Wan2.2-I2V-14B-LowNoise-Q4_K_M.gguf`
  * VAE: `Wan2.1_VAE.safetensors` (Wan2.1版VAEを推奨)

---

## 🔗 Links
* **YouTube:** [http://www.youtube.com/@VRAM8GB_ComfyUI_Wan2.2] (Cinematic Shorts Archive)
