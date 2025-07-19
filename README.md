---
title: LTX Video 0.9.8 13B Distilled
emoji: 🎬
colorFrom: blue
colorTo: purple
sdk: gradio
sdk_version: "4.44.0"
app_file: app.py
pinned: false
license: apache-2.0
hardware: zero-gpu
suggested_hardware: a100-large
python_version: "3.11"
short_description: Fast high-quality video generation with improved prompt understanding and ZeroGPU optimization
---

# LTX Video 0.9.8 13B Distilled

Fast high-quality video generation with improved prompt understanding and detail generation.

## Features

- **Text-to-Video**: Generate videos from text descriptions
- **Image-to-Video**: Animate static images with motion 
- **Video-to-Video**: Transform existing videos (currently disabled in UI)
- **Multi-scale generation**: Higher quality output with spatial upscaling
- **Optimized for ZeroGPU**: Smart model caching to prevent memory issues

## Model Information

- **Model**: [Lightricks/LTX-Video](https://huggingface.co/Lightricks/LTX-Video)
- **Version**: 0.9.8 13B Distilled
- **Architecture**: Transformer-based video diffusion model
- **Max Resolution**: Up to 1280px
- **Max Duration**: Up to 8.5 seconds

## Usage

1. Select your generation mode (text-to-video or image-to-video)
2. Enter your prompt describing the desired video
3. Adjust settings like duration, resolution, and quality options
4. Click generate and wait for your video!

## Advanced Settings

- **Guidance Scale**: Controls how closely the model follows your prompt
- **Improve Texture**: Uses multi-scale generation for better quality (slower)
- **Seed**: For reproducible results
- **Resolution**: Adjust output video dimensions

## Technical Details

This Space uses ZeroGPU with smart model caching to handle the large 13B parameter model efficiently. Models are loaded on-demand and cached to prevent memory issues common with large models on shared infrastructure.

## Links

- [Model Repository](https://huggingface.co/Lightricks/LTX-Video)
- [GitHub](https://github.com/Lightricks/LTX-Video)
- [Diffusers Integration](https://huggingface.co/Lightricks/LTX-Video-0.9.8-13B-distilled#diffusers-🧨)