# LLaMA-arm
Run [FB LLaMA](https://github.com/facebookresearch/llama/) model on ARM CPUs (Raspberry or Apple silicon or rescently become obsolete x86 arch)

Meta (formerly known as Facebook) announced LLaMA in February 2023, a new language model boasting parameter ranges from 7 billion to 65 billion. LLaMA was trained using publicly available datasets and its code is open source, allowing for easy adaptation and use by anyone. The model's 'weights' or parameters were posted on the project's [GitHub page](https://github.com/facebookresearch/llama/) as torrents and [magnet links](https://github.com/facebookresearch/llama/pull/73).

In March 2023, developer [Georgi Gerganov](https://github.com/ggerganov) released [llama.cpp](https://github.com/ggerganov/llama.cpp), a code that can run LLaMA on various hardware. The code operates locally, ensuring that no data is transmitted to Meta.


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

Upgrade numpy to the latest version:
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
> NOTE: Beaware that magnet above provides 7B, 13B, 30B and 65B models all togeather.

Convert model to proprietary Green AI gmml FP17 format (to be beneficial if you are planning to deploy the model on low-power devices):
```shell
python3 convert-pth-to-ggml.py ./models/7B/ 1
```
> NOTE: this is extremely memory intensive step, uses at least 16GB RAM. It's also super slow and prone to failure.

Quantize the model to 4 bits to reduce size (13GB to 4GB):
```shell
./quantize.sh 7B
```

### Running the model
Run the model:
```shell
./main -m ./models/7B/ggml-model-q4_0.bin -t 4 -n 128 -p "What is DSLU?"
```
`-m` is to direct llama.cpp to the model you want it to use; 
`-t` is the number of threads; 
`-n` is the number of tokens to predict; 
`-p` is your prompt.


### Interactive mode:
If you want a more ChatGPT-like experience, you can run in interactive mode by passing `-i` as a parameter. In this mode, you can always interrupt generation by pressing `Ctrl+C` and enter one or more lines of text which will be converted into tokens and appended to the current context. You can also specify a reverse prompt with the parameter `-r "reverse prompt string"`. This will result in user input being prompted whenever the exact tokens of the reverse prompt string are encountered in the generation. A typical use is to use a prompt which makes LLaMa emulate a chat between multiple users, say Alice and Bob, and pass `-r "Philipp:"`.


Default arguments using default 7B model
```shell
./chat.sh
```
Custom arguments using 13B model
```shell
./main -m ./models/13B/ggml-model-q4_0.bin -n 256 --repeat_penalty 1.0 --color -i -r "Philipp:" -f prompts/chat-with-bob.txt
```
