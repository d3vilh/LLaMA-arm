# LLaMA-arm
Run [FB LLaMA](https://github.com/facebookresearch/llama/) model on ARM CPUs (Raspberry or Apple silicon)

Be aware that LlaMA doesn't have restrictive rules. It will, on occasion, be sexist, racist, homophobic, and very wrong.

### Installation
Clone C++ port of LLaMA FB model:
```shell
git clone https://github.com/ggerganov/llama.cpp
cd llama.cpp
```
**For Raspberry-pi:** install g++ compiler:
```shell
sudo apt-get install g++
```
Compile LLaMA:
```shell
make
```

Install dependencies:
```shell
python3 -m pip install torch numpy sentencepiece
```

Upgrade numpy to latest version:
```shell
pip install numpy --upgrade
```

Create directory structure for the 7B model:
```shell
mkdir -p ./models/7B
cd ./models/7B
```
Download LLaMA **7B model** into this directory following by this magnet link ([SOURCE](https://github.com/facebookresearch/llama/pull/73)):

```shell
magnet:?xt=urn:btih:ZXXDAUWYLRUXXBHUYEMS6Q5CE5WA3LVA&dn=LLaMA
```

Convert model to gmml FP17 format:
```shell
python3 convert-pth-to-ggml.py ./models/7B/ 1
```

Quantize the model to 4 bits to reduce size (13GB to 4GB):
```shell
./quantize.sh 7B
```

### Running the model
Run the model:
```shell
./main -m ./models/7B/ggml-model-q4_0.bin -t 4 -n 128 -p "What is DSLU?"
```

### Interactive mode:
If you want a more ChatGPT-like experience, you can run in interactive mode by passing -i as a parameter. In this mode, you can always interrupt generation by pressing `Ctrl+C` and enter one or more lines of text which will be converted into tokens and appended to the current context. You can also specify a reverse prompt with the parameter `-r "reverse prompt string"`. This will result in user input being prompted whenever the exact tokens of the reverse prompt string are encountered in the generation. A typical use is to use a prompt which makes LLaMa emulate a chat between multiple users, say Alice and Bob, and pass `-r "Philipp:"`.


Default arguments using default 7B model
```shell
./chat.sh
```
Custom arguments using 13B model
```shell
./main -m ./models/13B/ggml-model-q4_0.bin -n 256 --repeat_penalty 1.0 --color -i -r "Philipp:" -f prompts/chat-with-bob.txt
```

