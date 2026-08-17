# Edge AI Vision Model Benchmarking (Quantization)

## What is this project?

This project checks what happens when you "shrink" an AI vision model to make it run faster and use less memory — like it would need to on a small, battery-powered device (a drone, a small robot, etc).

We shrink the model using a trick called **quantization**. Then we compare the shrunk model to the original one and measure:

- How much **faster** it runs (FPS = frames per second)
- How much **smaller** it is (file size and memory used)
- How much **accuracy it loses** (does it still detect objects correctly?)
- How it behaves when it runs **non-stop for a while** (a stand-in for how a device would heat up and slow down)

## Why does this matter?

Small devices (like drones or robots) don't have powerful chips or big batteries. A big, accurate AI model might be too slow or too heavy to run on them. Quantization makes the model smaller and faster, but usually a little less accurate. This project measures that trade-off with real numbers.

## What tools are used?

| Tool | What it's for |
|---|---|
| **YOLOv8n** | The AI vision model we test (it detects objects in images) |
| **COCO128** | A small set of 128 sample images used to test accuracy |
| **ONNX** | A universal format that lets the model run on many kinds of hardware |
| **ONNX Runtime Quantization** | The tool that shrinks the model (FP32 → INT8) |
| **Google Colab** | Free online computer we run everything on (no special hardware needed) |

## What do FP32 and INT8 mean?

- **FP32** = the original model. Very precise numbers, but bigger and slower.
- **INT8** = the shrunk model. Less precise numbers, but smaller and faster.

Think of it like a photo: FP32 is the full-quality photo, INT8 is a compressed version — smaller file, loads faster, but a little less sharp.

## How to run this project

1. Open [Google Colab](https://colab.research.google.com)
2. Upload the file `edge_ai_quantization_benchmark.ipynb`
3. Go to **Runtime → Change runtime type → CPU** (no GPU needed)
4. Click **Runtime → Run all**
5. Wait about 20–40 minutes
6. Download the results zip file from the Files panel on the left

## What comes out at the end?

- `benchmark_results.csv` — a table comparing FP32 vs INT8 (speed, size, accuracy)
- `fp32_sustained_load.csv` / `int8_sustained_load.csv` — how speed changes during a long, non-stop test run
- `benchmark_plots.png` — charts showing all the comparisons
- `results_summary.txt` — a plain-text summary you can copy into your report


## Quick summary of the whole idea

1. Take a normal AI model → **shrink it** (quantize)
2. **Test both versions** — speed, size, memory, accuracy
3. **Run it non-stop** to imitate a small device that overheats
4. **Compare the numbers** and explain the trade-offs
5. Present it as a mini research report with charts and honest limitations
