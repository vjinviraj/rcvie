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


## Results

Here's what we found when we ran the test (YOLOv8n model, COCO128 images, Google Colab CPU):

| Metric | FP32 (original) | INT8 (shrunk) | Change |
|---|---|---|---|
| Speed (FPS) | 4.39 | 3.72 | **15% slower** |
| Avg. time per image | 227.7 ms | 268.6 ms | slower |
| File size | 12.85 MB | 3.50 MB | **72.7% smaller** |
| Memory used (RAM) | 717.9 MB | 718.1 MB | basically the same |
| Accuracy (mAP50-95) | 0.4454 | 0.4342 | **2.5% drop** |

### What this actually means

- **The file got much smaller** — shrinking it from ~13 MB to ~3.5 MB. That's a real win if you're trying to fit a model onto a small device with limited storage.
- **Accuracy barely changed** — only about a 2.5% drop. So the shrunk model is still almost as good at detecting objects.
- **But it did NOT get faster — it actually got a bit slower.** This is the most interesting finding of the project.

### Why did the "shrunk" model run slower?

This might seem backwards, but it's a real and well-known effect, not a mistake:

- The type of quantization we used (**dynamic INT8**) makes the file smaller, but the CPU still has to do extra work converting numbers back and forth between low-precision (INT8) and high-precision (FP32) during every calculation.
- **Real speed gains from quantization usually need special hardware** (like an NVIDIA GPU with TensorRT, an Intel chip with OpenVINO optimizations, or a dedicated AI chip) that can run INT8 math natively and fast. A plain CPU in Google Colab doesn't have that hardware advantage, so we get the smaller file size without the speed boost.
- This is actually a useful, honest research finding: **quantization isn't automatically faster everywhere — it depends heavily on the hardware you run it on.** On the actual embedded chips this project is meant to simulate (which often *do* have INT8-optimized hardware), the speed result would likely look very different — smaller AND faster. That's a great point to raise in your report's discussion section.

### Limitations 

- We used ONNX Runtime's dynamic INT8 quantization instead of a hardware-specific tool like TensorRT, because TensorRT needs an NVIDIA GPU which wasn't available.
- We tested on COCO128 (128 images) instead of the full COCO dataset (5,000+ images) so the test could finish in a reasonable time on a CPU. Treat the accuracy numbers as a general trend, not a lab-grade benchmark.

## Quick summary of the whole idea

1. Take a normal AI model → **shrink it** (quantize)
2. **Test both versions** — speed, size, memory, accuracy
3. **Run it non-stop** to imitate a small device that overheats
4. **Compare the numbers** and explain the trade-offs
5. Present it as a mini research report with charts and honest limitations
