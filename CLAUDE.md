# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Important: Temporary Directory Usage

This `/Volumes/develop/tmp` directory is used for temporarily cloning repositories. **CRITICAL**: Always commit any changes before this path gets deleted.

## Workflow for Repository Work

1. Clone repository to this temporary directory
2. Make necessary changes 
3. **ALWAYS commit changes before finishing work**
4. Push commits to remote repository
5. Directory contents will be deleted after session

## Key Reminders

- This is a temporary workspace - nothing persists after deletion
- Always ensure work is committed and pushed to remote
- Verify git status shows clean working tree before finishing
- If working on forks, ensure changes are pushed to the correct remote

## LTX Video Project Architecture

This is a Hugging Face Space for LTX Video 0.9.8 13B Distilled, a transformer-based video diffusion model optimized for ZeroGPU deployment.

### Core Components

**Main Entry Points:**
- `app.py` - Gradio interface with ZeroGPU optimization and smart model caching
- `inference.py` - Core pipeline creation and inference logic for standalone use

**Configuration:**
- `configs/ltxv-13b-0.9.8-distilled.yaml` - Primary model configuration (multi-scale pipeline)
- `requirements.txt` - Dependencies including diffusers from main branch

**LTX Video Module (`ltx_video/`):**
- `models/autoencoders/` - VAE models including causal video autoencoder and spatial upsampler
- `models/transformers/` - Transformer3D model and attention mechanisms
- `pipelines/` - LTXVideoPipeline and LTXMultiScalePipeline for generation
- `schedulers/` - Rectified Flow scheduler
- `utils/` - Skip layer strategies, prompt enhancement, and utility functions

### ZeroGPU Optimization Architecture

**Critical Pattern:** Models are loaded inside `@spaces.GPU` decorator using intelligent caching system:

```python
# Smart caching prevents repeated model loads in /data-nvme/
pipeline, upsampler = get_or_create_pipeline(config, device)
```

**Key Functions:**
- `get_or_create_pipeline()` - Caches models and reuses if config unchanged
- `seed_everything()` - Ensures reproducible generation (note: fixed typo from seed_everething)
- `calculate_new_dimensions()` - Auto-adjusts resolution based on input media

### Generation Modes

1. **Text-to-Video** - Generate from text prompts
2. **Image-to-Video** - Animate static images  
3. **Video-to-Video** - Transform existing videos (hidden in UI)

### Development Commands

**Run the Gradio interface:**
```bash
python app.py
```

**Standalone inference:**
```bash
python inference.py --prompt "your prompt" --pipeline_config configs/ltxv-13b-0.9.8-distilled.yaml
```

**Install dependencies:**
```bash
pip install -r requirements.txt
```

### Configuration Management

**Model Configurations:** Located in `configs/` directory with different model sizes and versions:
- `ltxv-13b-0.9.8-distilled.yaml` - Primary 13B distilled model
- `ltxv-2b-*` - Smaller 2B variants
- Supports multi-scale pipeline with spatial upscaling

**Critical Settings:**
- `pipeline_type: multi-scale` - Enables two-pass generation
- `stg_mode: attention_values` - Skip layer strategy
- `decode_timestep: 0.05` - VAE decoding parameter

### ZeroGPU Memory Management

**Key Patterns to Follow:**
1. Never load models globally - use `get_or_create_pipeline()` inside `@spaces.GPU`
2. Always call `gc.collect()` and `torch.cuda.empty_cache()` when cleaning up
3. Check config hash before reloading models
4. Use `GIT_LFS_SKIP_SMUDGE=1` when pushing to avoid LFS issues

**Common Issues:**
- `/data-nvme/` disk full from repeated model loads
- Memory leaks from not cleaning up old models
- Model loading outside GPU decorator

### Remote Configuration

**GitHub Remote:** `origin` - Public repository
**HF Space Remote:** `hf-space` - Private Hugging Face Space deployment

**Deploy to HF Space:**
```bash
GIT_LFS_SKIP_SMUDGE=1 git push hf-space main
```

### UI Components

**Hidden Controls Made Visible:**
- `guidance_scale_input` - CFG scale control
- `improve_texture` - Multi-scale generation toggle

**Dynamic Duration Calculation:**
- GPU duration automatically calculated based on video length
- Longer videos (>7s) get more GPU time allocation

### Important Implementation Notes

- All model paths are downloaded but not loaded until needed (ZeroGPU compatibility)
- Frame counts must be N*8+1 format for proper processing
- Dimensions must be divisible by 32
- Uses smart padding and cropping for non-standard resolutions
- Implements proper error handling for missing media files