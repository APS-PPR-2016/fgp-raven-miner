# FGP Raven Miner

**High-Performance Distributed KAWPOW (Ravencoin) Mining Suite**  
*Next-Generation C++20 Distributed Mining Infrastructure for Linux and Windows*

---

> [!IMPORTANT]
> ### 🚧 Project Status: Active Development & Testing
> **This project is currently under active development and internal testing.**  
> **The full source code will be released soon.**  
> 
> Pre-compiled standalone binaries for **Linux (x86_64)** and **Windows (x64)** are provided in this repository for early testing, performance benchmarking, and community evaluation.

---

## ⚡ Overview

**FGP Raven Miner** is an ultra-low-overhead, high-performance distributed mining suite built from the ground up in modern C++20 for the **KAWPOW** (Ravencoin / RVN) algorithm. 

Unlike traditional monolithic miners, FGP Raven Miner separates mining operations into an **Intelligent Coordinator Server** and **Lightweight Worker Clients**:
- **Coordinator Server (`raven_mining_server`)**: Manages the Stratum pool connection, automatically synthesizes or downloads the KAWPOW DAG, dispatches jobs over TCP to remote or local worker rigs, and provides a rich real-time Web Portal dashboard.
- **Worker Client (`raven_mining_client`)**: Ultra-fast GPU compute worker that streams jobs from the coordinator, executes KAWPOW search kernels using CUDA, monitors thermal governor states, and returns valid nonces instantaneously.

---

## 🌟 Key Features

- **Distributed Architecture**: One coordinator server handles Stratum communication and DAG synthesis. Multiple worker rigs (local or remote across Cloud/Vast.ai/RunPod) connect via TCP and mine collaboratively.
- **Real-Time Web Dashboard**: Built-in HTTP web dashboard (default: `http://localhost:8484`) featuring live hashrate telemetry, per-GPU thermals, fan speeds, power consumption, overclock status, and DAG generation progress.
- **Thermal Auto-Governor**: Dynamic workload throttling and cooldown management to prevent hardware degradation and protect high-density mining rigs.
- **Multi-Language Localization**: Full CLI support for **11 languages** using `--lang`:
  - English (`en_US`), Spanish (`es_ES`), Dutch (`nl_NL`), Italian (`it_IT`), Portuguese (`pt_BR`)
  - Czech (`cs_CZ`), Russian (`ru_RU`), Chinese (`zh_CN`), Hindi (`hi_IN`), Urdu (`ur_PK`), Arabic (`ar_SA`)
- **Transparent 1% Dev Fee Architecture**:
  - Employs an industry-standard **time-sliced dual-connection** mechanism (36 seconds per 1-hour cycle = exactly 1.00%).
  - Zero DAG regeneration during fee switching.
  - Dedicated secondary Stratum connection ensures 0% share rejection and zero impact on user mining.

---

## 📂 Executable Binaries

Pre-compiled standalone binaries are available in the [`bin/`](./bin/) directory:

| Platform | Component | Binary Path | Description |
| :--- | :--- | :--- | :--- |
| **Linux (x86_64)** | Coordinator Server | [`bin/linux-x86_64/raven_mining_server`](./bin/linux-x86_64/raven_mining_server) | Standalone Linux coordinator server |
| **Linux (x86_64)** | Worker Client | [`bin/linux-x86_64/raven_mining_client`](./bin/linux-x86_64/raven_mining_client) | Standalone Linux GPU worker client |
| **Windows (x64)** | Coordinator Server | [`bin/windows-x64/raven_mining_server.exe`](./bin/windows-x64/raven_mining_server.exe) | Standalone Windows coordinator server |
| **Windows (x64)** | Worker Client | [`bin/windows-x64/raven_mining_client.exe`](./bin/windows-x64/raven_mining_client.exe) | Standalone Windows GPU worker client |

---

## 🚀 Quick Start Guide

### 1. Launch the Coordinator Server

Run the coordinator on your main machine or server:

#### Linux:
```bash
./bin/linux-x86_64/raven_mining_server \
  --pool rvn.2miners.com:6060 \
  --wallet YOUR_RAVENCOIN_WALLET_ADDRESS \
  --worker rig1 \
  --web-port 8484
```

#### Windows:
```cmd
bin\windows-x64\raven_mining_server.exe ^
  --pool rvn.2miners.com:6060 ^
  --wallet YOUR_RAVENCOIN_WALLET_ADDRESS ^
  --worker rig1 ^
  --web-port 8484
```

> Open `http://localhost:8484` in any web browser to view the real-time coordinator dashboard.

---

### 2. Connect Mining Worker Clients

Run the worker client on any machine equipped with NVIDIA GPUs:

#### Linux:
```bash
./bin/linux-x86_64/raven_mining_client \
  --server 127.0.0.1:8088 \
  --http-server http://127.0.0.1:8080 \
  --wallet YOUR_RAVENCOIN_WALLET_ADDRESS \
  --worker client_rig_1 \
  --gpus 0,1
```

#### Windows:
```cmd
bin\windows-x64\raven_mining_client.exe ^
  --server 127.0.0.1:8088 ^
  --http-server http://127.0.0.1:8080 ^
  --wallet YOUR_RAVENCOIN_WALLET_ADDRESS ^
  --worker client_rig_1 ^
  --gpus 0,1
```

---

## ⚙️ CLI Reference

### Coordinator Server Options (`raven_mining_server`)

```
Options:
  --pool <host:port>     Stratum pool host:port (default: rvn.2miners.com:6060)
  --wallet <address>     Ravencoin wallet address
  --worker <name>        Worker rig name (default: rig1)
  --http-port <port>     HTTP port for DAG file serving (default: 8080)
  --client-port <port>   TCP port for mining client coordinator (default: 8088)
  --web-port <port>      HTTP port for Web Portal dashboard (default: 8484)
  --gpu <id>             GPU device ID for local DAG generation (default: 0)
  --cache-dir <path>     Directory for DAG binary cache files (default: .)
  --lang <code/locale>   Set user language (e.g. en_US, es_ES, ru_RU, zh_CN, etc.)
  --help, -h             Show help message
```

### Worker Client Options (`raven_mining_client`)

```
Options:
  --server <host:port|url>  Coordinator server (default: 127.0.0.1:8088)
  --http-server <url>       HTTP/HTTPS server URL for DAG download (default: http://127.0.0.1:8080)
  --wallet <address>        Ravencoin wallet address
  --worker <name>           Worker rig name (default: client_node1)
  --batch-size <size>       Nonces per batch (default: 524288)
  --gpus <id1,id2,...>      Comma-separated GPU indices (default: 0)
  --cache-dir <path>        Directory for DAG binary cache files (default: .)
  --target-temp <celsius>   Target GPU temperature for dynamic scaling (default: 75.0)
  --critical-temp <celsius> Critical GPU temperature where mining halts (default: 85.0)
  --cooldown-temp <celsius> Cooldown temperature before resuming (default: 75.0)
  --min-batch-size <size>   Minimum batch size floor during throttling (default: 65536)
  --power-limit <watts>     Optional hardware power limit in Watts
  --lock-cclock <mhz>       Optional locked core clock in MHz
  --lang <code/locale>      Set user language (e.g. en_US, es_ES, ru_RU, zh_CN, etc.)
  --help, -h                Show help message
```

---

## 🖥️ System Requirements

- **Operating System**: Linux x86_64 (glibc 2.31+) or Windows 10/11 x64
- **Hardware**: NVIDIA GPU with Compute Capability 6.0 or higher (Pascal, Turing, Ampere, Ada Lovelace, Blackwell)
- **VRAM**: Minimum 4 GB VRAM (6 GB+ recommended for KAWPOW DAG)
- **NVIDIA Drivers**: R520 or newer recommended

---

## 🔒 Source Code Release Notice

Source code release is currently scheduled following the completion of the multi-GPU stress-testing phase and external security audit. Please watch and star this repository for updates!

---

## 📄 License & Disclaimer

Pre-release binaries provided for testing and evaluation purposes. Use at your own risk. Always ensure adequate GPU cooling and power delivery when operating cryptocurrency mining hardware.
