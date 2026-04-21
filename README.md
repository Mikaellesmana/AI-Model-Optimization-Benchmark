# AI Model Optimization Benchmark Suite

A Python-based framework for benchmarking deep learning model inference across **PyTorch**, **ONNX Runtime**, and **OpenVINO** — with a FastAPI backend and a React dashboard.

---

## Project Files

```
D:\Project_\
├── Benchmark engine.py        # Core engine: load model, export ONNX, run all backends
├── metrics_collector.py       # Collect latency, throughput, memory, CPU, warmup metrics
├── benchmark_api.py           # FastAPI server (connects Python backend to React UI)
├── benchmark_results.json     # Output from Benchmark engine.py
├── metrics_results.json       # Output from metrics_collector.py
├── resnet18.onnx              # Auto-generated ONNX model file
│
└── benchmark-ui\              # React dashboard
    └── src\
        ├── App.js
        └── BenchmarkDashboard.jsx
```

---

## Requirements

- Python 3.12+
- Node.js 18+
- Windows PowerShell

---

## Installation

### 1. Install Python dependencies

```powershell
python -m pip install torch torchvision onnx onnxruntime openvino onnxscript psutil fastapi uvicorn httpx
```

### 2. Install React dependencies

```powershell
cd D:\Project_\benchmark-ui
npm install chart.js react-chartjs-2
```

---

## How to Run

You need **two PowerShell windows** open at the same time.

### Window 1 — Start the API server

```powershell
cd D:\Project_
python benchmark_api.py
```

You should see:
```
Uvicorn running on http://0.0.0.0:8000
```

### Window 2 — Start the React dashboard

```powershell
cd D:\Project_\benchmark-ui
npm start
```

Then open your browser at **http://localhost:3000**

---

## Running Benchmarks from the Command Line

### Run the core benchmark engine

```powershell
cd D:\Project_
python "Benchmark engine.py"
```

Outputs: `resnet18.onnx` and `benchmark_results.json`

### Run the metrics collector

> Run the engine first — metrics collector needs `resnet18.onnx`

```powershell
python metrics_collector.py
```

Outputs: `metrics_results.json`

---

## API Endpoints

Base URL: `http://localhost:8000`

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/` | API info |
| GET | `/health` | Health check |
| GET | `/models` | List supported models |
| GET | `/runtimes` | List supported runtimes |
| POST | `/benchmark` | Run single benchmark |
| POST | `/benchmark/compare` | Compare all 3 runtimes |
| POST | `/benchmark/batch-scaling` | Batch scaling test (1, 4, 8, 16) |
| POST | `/benchmark/batch-scaling/all-runtimes` | Batch scaling across all runtimes |

Interactive API docs: **http://localhost:8000/docs**

### Example request

```powershell
$body = '{"model": "resnet18", "batch_size": 1, "runtime": "onnxruntime"}'
Invoke-RestMethod -Uri "http://localhost:8000/benchmark" -Method POST -Body $body -ContentType "application/json"
```

---

## Supported Models

| Model | Parameters |
|-------|-----------|
| resnet18 | 11.7M |
| resnet50 | 25.6M |
| mobilenet_v2 | 3.4M |
| efficientnet_b0 | 5.3M |

## Supported Runtimes

| Runtime | Description |
|---------|-------------|
| pytorch | Native PyTorch CPU inference (baseline) |
| onnxruntime | Graph-optimized ONNX Runtime (CPUExecutionProvider) |
| openvino | Intel OpenVINO CPU (hardware-aware) |

---

## Metrics Collected

| Metric | Description |
|--------|-------------|
| Avg / Median Latency | Mean and median inference time (ms) |
| P95 / P99 Latency | Tail latency percentiles |
| Throughput | Samples processed per second |
| Memory Usage | Peak RSS memory during inference (MB) |
| CPU Usage | Mean and peak CPU % during inference |
| Warmup Time | Cold-start cost of first inference (ms) |

---

## Troubleshooting

| Error | Fix |
|-------|-----|
| `python` not found | Install Python from python.org, check "Add to PATH" |
| `pip` not recognized | Use `python -m pip install ...` |
| `ModuleNotFoundError` | Run `python -m pip install <module>` |
| `resnet18.onnx not found` | Run `Benchmark engine.py` first |
| File not found with spaces | Use quotes: `python "Benchmark engine.py"` |
| API offline in dashboard | Start `python benchmark_api.py` in a separate window |
| `Legend already declared` | Re-download `BenchmarkDashboard.jsx` (fixed version) |

---

## Dashboard Usage

1. Start both servers (see above)
2. Open **http://localhost:3000**
3. Select **Model**, **Runtime**, **Batch size**, and **Metric** from the dropdowns
4. Click **Run** for a single backend result
5. Click **Compare all** to benchmark all 3 runtimes at once
6. Click **Export JSON** to download all results

---

*AI Model Optimization Benchmark Suite — v1.0 — April 2026*
