# Putting the 7B Model on a Raspberry Pi 4

This statement is the "Can it run Doom" equivalent for AI.

## Introduction

This seems like an exercise in futility, but nonetheless benchmarks the progress of quantization techniques, as well as the portability of AI models to edge devices.

![It works](./7B.png?raw=true "7B quantized to 4-bits running on a Raspberry Pi 4")

## Installation Steps

1. Update and install git:
    ```bash
    sudo apt update && sudo apt install git
    ```

2. Clone the chatbot repository:
    ```bash
    git clone https://github.com/ggerganov/llama.cpp
    ```

3. Install required Python packages:
    ```bash
    python3 -m pip install torch numpy sentencepiece
    ```

    - If the build doesn't go through, run this command beforehand:
        ```bash
        rm /usr/lib/python3.11/EXTERNALLY-MANAGED
        ```

4. Install essential build tools:
    ```bash
    sudo apt install g++ build-essential
    ```

5. Navigate to the cloned directory and build:
    ```bash
    cd llama.cpp
    make
    ```

    > **Note**: This only works on Linux, not Mac. Edit the `MODEL_SIZE` line to choose the model size you want (e.g., 7B, 13B). Otherwise, it will install all of them.

6. Convert the 7B model to GGML FP16 format:
    ```bash
    python3 convert-pth-to-ggml.py models/7B/ 1
    ```

    > **Note**: Ensure `tokenizer.model` and `.consoldidates.00.pth` file/files are present.

7. Quantize the model to 4-bits using the `q4_0` method. You can also try using the `q3_0` model for better performance on the Raspberry Pi.
    ```bash
    ./quantize ./models/7B/ggml-model-f16.bin ./models/7B/ggml-model-q4_0.bin q4_0
    ```

    Read more about quantization [here](https://huggingface.co/blog/4bit-transformers-bitsandbytes).

8. Run the inference in interactive mode, or converse with Bob using the examples argument:
    ```bash
    ./main -m ./models/7B/ggml-model-q4_0.bin -n 128
    ```

    > **Note**: Bob is cool. Ask him nicely, and he'll tell you if you're using the 7B model or the 7B quantized.

9. Test the chat interface:
    ```bash
    ./examples/chat.sh
    ```

10. Transfer the generated files in the `/7B` folder (excluding the pre-transformed files) to the Raspberry Pi. Place these files in the same directory structure `llama.cpp/models/7B/`, and then simply run:
    ```bash
    ./examples/chat.sh
    ```

Happy tinkering!
