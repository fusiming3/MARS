## <div align="center"> <i>MARS</i>: Mixture of Auto-Regressive Models for Fine-grained Text-to-image Synthesis </div>


---

## Overview

![example](image/intro-1-page-1.jpg)

This repository contains the official implementation of the paper "MARS: Mixture of Auto-Regressive Models for Fine-grained Text-to-image Synthesis".

Auto-regressive models have made significant progress in the realm of language generation, yet do not perform on par with diffusion models in the domain of image synthesis. In this work, we introduce \textbf{MARS}, a novel framework for T2I generation that incorporates a specially designed Semantic Vision-Language Integration Expert (SemVIE). This innovative component integrates pre-trained LLMs by independently processing linguistic and visual information—freezing the textual component while fine-tuning the visual component. This methodology preserves the NLP capabilities of LLMs while imbuing them with exceptional visual understanding. Building upon the powerful base of the pre-trained Qwen-7B, MARS stands out with its bilingual generative capabilities corresponding to both English and Chinese language prompts and the capacity for joint image and text generation.   The flexibility of this framework lends itself to migration towards \textbf{any-to-any} task adaptability. Furthermore, MARS employs a multi-stage training strategy that first establishes robust image-text alignment through complementary bidirectional tasks and subsequently concentrates on refining the T2I generation process, significantly augmenting text-image synchrony and the granularity of image details.  Notably, MARS requires only \textbf{9\%} of the GPU days needed by SD1.5, yet it achieves remarkable results across a variety of benchmarks, illustrating the training efficiency and the potential for swift deployment in various applications.

![model](image/framwork-matri-X.jpg)

## Getting Started

### Setup

```bash
git clone git@github.com:MS-Diffusion/MS-Diffusion.git
cd msdiffusion
pip install -r requirements.txt
```

### Model

Download the pretrained base models from [SDXL-base-1.0](https://huggingface.co/stabilityai/stable-diffusion-xl-base-1.0) and [CLIP-G](https://huggingface.co/laion/CLIP-ViT-bigG-14-laion2B-39B-b160k).

Download our checkpoint from [MS-Diffusion](https://huggingface.co/doge1516/MS-Diffusion).

### Inference

```bash
python inference.py
```


## Todo List

- [x] paper
- [x] inference code
- [ ] model weights
- [ ] training code

## New Features

### Auto Layout Guidance

You may find the layout prior is hard to determine in some cases. MS-Diffusion now supports using the text cross-attention maps as the psuedo layout guidance. Specifically, since the text cross-attention maps can reflect the area of each text token, we can replace the layout prior with them during the inference. You can enable this feature easily by:

```python
images = ms_model.generate(..., mask_threshold=0.5, start_step=5)
```

Here, `mask_threshold` is used to filter the text cross-attention maps, and `start_step` is the step to start using the psuedo layout guidance. In this way, you can provide a rough box input. MS-Diffusion still uses the layout prior before the `start_step` and then switches to the areas where the activation value in the text cross-attention maps is larger than the `mask_threshold`. Note that you have to ensure the `phrases` can be found in the text when you use this feature. The generation quality depends on the accuracy of the text cross-attention maps.

This feature is found to be useful when the subject boxes are overlapping, such as "object+scene". With the auto layout guidance, you could also increase the `scale` for higher image fidelity.

You can also completely disable the layout prior by setting `boxes` to zeros and `drop_grounding_tokens` to 1. However, we have found a degradation in the generation quality when using this setting. We recommend using the layout prior at early steps.

### Multi-Subject Scales

Now MS-Diffusion supports different scales for different subjects during the inference. You can set the `subject_scales` when calling the `generate()` function:

```python
subject_scales = [0.6, 0.8]
images = ms_model.generate(..., subject_scales=subject_scales)
```

When using `subject_scales`, `scale` will have no effect on the model.

## Acknowledgement

This repository is built based on the fancy, robust, and extensible work of [Qwen](https://github.com/QwenLM/Qwen) and [muse](https://github.com/huggingface/open-muse). We also thank HuggingFace for their contribution to the open source community.

