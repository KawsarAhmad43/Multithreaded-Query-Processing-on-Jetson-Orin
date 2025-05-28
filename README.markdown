# Multithreaded Query Processing on Jetson Orin

## Overview
This project, **MultiQ**, demonstrates multithreaded query processing on the NVIDIA Jetson Orin NX, leveraging the Ollama 0.5.13 framework with the Gemma2:2b model to handle multiple AI queries concurrently. Developed as a learning exercise, it showcases efficient edge AI processing with clear logging for query status.

## Project Goal
The primary goal was to create a simple, error-free application that processes at least two AI queries simultaneously on the Jetson Orin NX, a powerful edge computing platform. The project aimed to:
- Utilize the Jetson’s GPU capabilities for real-time AI inference.
- Implement concurrent query handling with multithreading.
- Provide detailed logging of query receipt, processing, and completion.
- Use a lightweight model compatible with the Jetson’s 16GB memory.

## What I Wanted to Do
Initially, I intended to use **TensorRT-LLM 0.8.0** to optimize inference performance, integrating it with a model from Ollama (Gemma2:2b). The plan was to:
- Build a Docker container with TensorRT-LLM and dependencies.
- Convert the Gemma2:2b model to TensorRT-LLM format.
- Run an inference server to handle multiple queries concurrently.
- Log query statuses for transparency and debugging.

However, due to persistent issues with TensorRT-LLM (e.g., installation errors, GGUF incompatibility), I pivoted to using Ollama’s API directly for reliability.

## What Has Been Done
The project was implemented in the `~/orin_version/multiq` directory on the Jetson Orin NX with the following steps:
1. **Environment Setup**:
   - Configured the Jetson for maximum performance (`sudo nvpmodel -m 0`, `sudo jetson_clocks`).
   - Installed Ollama 0.5.13 and pulled the Gemma2:2b model.
   - Set up Python dependencies (`requests`) for API interaction.

2. **Project Structure**:
   - Created directories: `~/orin_version/multiq/scripts` for scripts.
   - Developed `multiq_test.py`, a Python script for concurrent query processing.

3. **Implementation**:
   - Used Ollama’s API (`http://localhost:11434/api/generate`) to process queries.
   - Implemented multithreading to send two queries simultaneously.
   - Added logging to track query receipt, processing start, and completion with timestamps and response times.

4. **Testing**:
   - Ran the script with two sample queries: “What is edge AI?” and “How does Jetson Orin work?”.
   - Monitored performance using `tegrastats` to confirm GPU usage and memory consumption.

## What I Achieved
The MultiQ project successfully achieved its core objectives:
- **Concurrent Query Processing**: Processes two queries simultaneously using Python’s `threading` module, with responses generated in ~2–3 seconds each.
- **Reliable Implementation**: Leveraged Ollama 0.5.13 with Gemma2:2b, avoiding TensorRT-LLM’s compatibility issues.
- **Clear Logging**: Outputs detailed logs (e.g., query received, processing started, completed) with timestamps and response times.
- **Edge AI Demonstration**: Runs efficiently on the Jetson Orin NX, using ~2–4GB RAM and active GPU (`GR3D_FREQ` ~50% @729MHz).
- **Educational Value**: Learned to implement multithreaded AI query processing, handle API interactions, and troubleshoot Jetson-specific challenges.

## How It Works
The project operates as follows:
1. **Setup**:
   - Ollama 0.5.13 runs on the Jetson, serving the Gemma2:2b model via its API.
   - The script `multiq_test.py` is executed in the `~/orin_version/multiq/scripts` directory.

2. **Query Processing**:
   - The script defines two queries (e.g., “What is edge AI?”).
   - Each query is sent to Ollama’s API in a separate thread using Python’s `threading.Thread`.
   - The API processes queries concurrently, leveraging the Jetson’s GPU.

3. **Logging**:
   - Logs are generated using Python’s `logging` module, capturing:
     - Query receipt (e.g., “Query 1 received: What is edge AI?”).
     - Processing start (e.g., “Query 1 processing started”).
     - Completion with response and time (e.g., “Time: 2.45s”).
   - Outputs are printed to the console for easy monitoring.

4. **Execution**:
   - Run `ollama serve` in one terminal.
   - Execute `python3 ~/orin_version/multiq/scripts/multiq_test.py` in another.
   - Observe logs and responses, confirming concurrent processing.

## Prerequisites
- **Hardware**: NVIDIA Jetson Orin NX (16GB RAM, JetPack 5.1.1, L4T R35.3.1, CUDA 11.4).
- **Software**:
  - Ollama 0.5.13 (`ollama pull gemma2:2b`).
  - Python 3.8+ with `requests` (`pip3 install requests`).
  - Ubuntu 20.04.

## Installation
1. **Install Ollama**:
   ```bash
   curl https://ollama.ai/install.sh | sh
   ollama pull gemma2:2b
   ```

2. **Set Up Project**:
   ```bash
   mkdir -p ~/orin_version/multiq/scripts
   sudo nvpmodel -m 0
   sudo jetson_clocks
   ```

3. **Install Dependencies**:
   ```bash
   sudo apt-get update
   sudo apt-get install python3-pip
   pip3 install requests
   ```

4. **Add Script**:
   - Copy `multiq_test.py` to `~/orin_version/multiq/scripts/`.
   - Ensure 4-space indentation (use `sed -i 's/\t/    /g' multiq_test.py` if needed).

## Usage
1. Start Ollama:
   ```bash
   ollama serve
   ```

2. Run the script:
   ```bash
   python3 ~/orin_version/multiq/scripts/multiq_test.py
   ```

3. View output:
   - Logs show query status and responses.
   - Example:
     ```
     2025-05-28 18:30:25,789 - INFO - Query 1 completed
     Query 1: What is edge AI?
     Response: Edge AI involves running AI algorithms on edge devices...
     Time: 2.45s
     ```

## Performance
- **Model Size**: ~1–2GB (Gemma2:2b).
- **Latency**: ~2–3s per query.
- **Throughput**: ~10–20 tokens/s for 2 queries.
- **Memory**: ~2–4GB RAM.
- **GPU Usage**: ~50% @729MHz (via `tegrastats`).

## Challenges and Lessons Learned
- **TensorRT-LLM Issues**: Faced errors with `tensorrt_llm==0.8.0` (e.g., “BadAttrs”, PyTorch version mismatches). Pivoted to Ollama for reliability.
- **Indentation Errors**: Resolved Python indentation issues by enforcing 4-space consistency.
- **Jetson Optimization**: Learned to maximize performance with `nvpmodel` and `jetson_clocks`.
- **Multithreading**: Gained experience with Python’s `threading` for concurrent API calls.

## Future Improvements
- Integrate TensorRT-LLM with a compatible model (e.g., TinyLlama) for faster inference.
- Add support for more queries or dynamic query inputs.
- Implement a web interface for user-friendly query submission.

## Contributing
Contributions are welcome! Please fork the repository, make changes, and submit a pull request. Report issues or suggest features via the GitHub Issues page.

## License
This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.

## Acknowledgments
- NVIDIA Jetson Community for hardware support.
- Ollama for providing an accessible AI framework.
- Python community for robust libraries (`requests`, `threading`).