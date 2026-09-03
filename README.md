# MsLW: A Unified Multi-step Watermark Embedding Framework for Latent Diffusion Models

[![Paper](https://img.shields.io/badge/IEEE%20T--CSVT-Paper-blue)](https://doi.org/10.1109/TCSVT.2026.3724144)
[![License](https://img.shields.io/badge/License-BSD--3--Clause--Clear-green.svg)](LICENSE)

This repository provides the official implementation of **MsLW: A Unified Multi-step Watermark Embedding Framework for Latent Diffusion Models**.

> 📄 **Publication:** IEEE Transactions on Circuits and Systems for Video Technology (T-CSVT), **Early Access**, 2026. [https://doi.org/10.1109/TCSVT.2026.3724144](https://doi.org/10.1109/TCSVT.2026.3724144)

## 🖼️ Visual Results

![Watermark Quality](assets/visual_results.jpg)

*From left to right: the original image and watermarked images with different payload sizes. Stable Diffusion v1.4 is used as the backbone.*

## 🛠️ Getting Started

### Environment

The code has been tested with the following configuration:

- NVIDIA A100 PCIe 40 GB
- Python 3.12
- PyTorch 2.5.1
- CUDA 12.4
- Diffusers 0.37.1

### Stable Diffusion Backbones

MSLW has been evaluated with the following Diffusers-format checkpoints:

- [Stable Diffusion v1.4](https://huggingface.co/CompVis/stable-diffusion-v1-4)
- [Stable Diffusion v2.1 Base](https://huggingface.co/sd2-community/stable-diffusion-2-1-base)

Install the Hugging Face Hub CLI and download either model to a local directory:

```bash
pip install -U "huggingface_hub[cli]"

hf download CompVis/stable-diffusion-v1-4 \
  --exclude "*.ckpt" "*.bin" "*.msgpack" \
  --local-dir /path/to/stable-diffusion-v1-4

hf download sd2-community/stable-diffusion-2-1-base \
  --exclude "*.ckpt" "*.bin" "*.msgpack" "v2-1_512-*.safetensors" \
  --local-dir /path/to/stable-diffusion-v2-1-base
```

For users in mainland China, [ModelScope](https://www.modelscope.cn/) may provide more reliable downloads:

```bash
pip install modelscope

modelscope download \
  --model "AI-ModelScope/stable-diffusion-v1-4" \
  --exclude "*.bin" \
  --local_dir "/path/to/stable-diffusion-v1-4"

modelscope download \
  --model "AI-ModelScope/stable-diffusion-v2-1" \
  --exclude "*.bin" \
    "*.ckpt" \
    "v2-1_768-ema-pruned.safetensors" \
    "v2-1_768-nonema-pruned.safetensors" \
  --local_dir "/path/to/stable-diffusion-v2-1"
```

### Dataset

The models reported in the paper were trained on the [Flickr8k dataset](https://github.com/Avaneesh40585/Flickr8k-Dataset). 

The evaluation prompts used in our experiments are sourced from the [Stable Diffusion Prompts dataset](https://huggingface.co/datasets/Gustavosta/Stable-Diffusion-Prompts).

## 🚀 Usage

### Training

Configure `train.sh`. In the paper, `--max-epochs` is set to 6, 12, and 15 for payloads of 48, 96, and 128 bits, respectively. For all payload sizes, the noise attack layer is enabled starting from the third epoch, so set `--attack-start-epoch` to `2`. Then run:

```bash
bash train.sh
```

The generator and extractor weights produced during training are saved under `--checkpoint-dir`:

| Files | Selection criterion |
| --- | --- |
| `generator_last.pth`, `extractor_last.pth` | Most recently validated weights |
| `generator_bestbce.pth`, `extractor_bestbce.pth` | Lowest validation BCE loss |
| `generator.pth`, `extractor.pth` | Lowest total validation loss |

### Watermarked Image Generation

You can use either a trained `generator*.pth` checkpoint or a pretrained encoder checkpoint from [`checkpoints/`](checkpoints/). In the pretrained checkpoint filenames, `v1` denotes Stable Diffusion v1.4 with 48-, 96-, or 128-bit payloads, while `v2` denotes Stable Diffusion v2.1 Base with a 48-bit payload. Ensure that the payload size indicated by the filename matches `--bit-length`.

Configure `generate.sh`, then run:

```bash
bash generate.sh
```

The script writes watermarked images as `img_<index>.png` and their randomly generated binary messages as `msg_<index>.npy` to the configured directories.

### Watermark Extraction

Use the extractor checkpoint produced during training or the pretrained decoder checkpoint from [`checkpoints/`](checkpoints/).

Configure `decode.sh`, then run:

```bash
bash decode.sh
```

The decoder matches each `img_<index>.png` with `msg_<index>.npy`, computes bit accuracy, and appends the following record to `--result-path`:

## 📖 Citation

If you find this repository useful, please cite the paper and give the repository a star:

```text
@ARTICLE{MsLW,
  author={Chen, Peian and Su, Wenkang and Zhang, Jian and Duan, Rui and Zhu, Tong and Zhou, Zhili},
  journal={IEEE Transactions on Circuits and Systems for Video Technology}, 
  title={MsLW: A Unified Multi-step Watermarking Framework for Latent Diffusion Models}, 
  year={2026},
  volume={},
  number={},
  pages={1-1},
  doi={10.1109/TCSVT.2026.3724144}
}
```
