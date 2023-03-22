# LLaMA-arm
Run LLaMA model on ARM CPUs (Raspberry or Apple M)

Be aware that LlaMA doesn't have restrictive rules. It will, on occasion, be sexist, racist, homophobic, and very wrong.

###Installation
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

Create direcroty structure for the 7B model:
```shell
mkdir -p ./models/7B
cd ./models/7B
```
Download LLaMA 7B model into this directory following by this magnet link ([SOURCE](https://github.com/facebookresearch/llama/pull/73)):

```shell
magnet:?xt=urn:btih:ZXXDAUWYLRUXXBHUYEMS6Q5CE5WA3LVA&dn=LLaMA
```

Convert model to gmml FP17 format:
```shell
python3 convert-pth-to-ggml.py ./models/7B/ 1
```

Quantize the model to 4 bits:
```shell
./quantize.sh 7B
```

Run the model:
```shell
./main -m ./models/7B/ggml-model-q4_0.bin -t 4 -n 128 -p "What is DSLU?"
```


Example with dummy model:
```shell
philipp@M1Prou llama.cpp % ./main -m ./models/ggml-vocab.bin -t 4 -n 128 -p "What is DSLU?"
main: seed = 1679511477
llama_model_load: loading model from './models/ggml-vocab.bin' - please wait ...
llama_model_load: n_vocab = 32000
llama_model_load: n_ctx   = 512
llama_model_load: n_embd  = 4096
llama_model_load: n_mult  = 256
llama_model_load: n_head  = 32
llama_model_load: n_layer = 32
llama_model_load: n_rot   = 128
llama_model_load: f16     = 1
llama_model_load: n_ff    = 11008
llama_model_load: n_parts = 1
llama_model_load: ggml ctx size = 13365.09 MB
llama_model_load: memory_size =   512.00 MB, n_mem = 16384
llama_model_load: loading model part 1/1 from './models/ggml-vocab.bin'
llama_model_load:  done
llama_model_load: model size =     0.00 MB / num tensors = 0

system_info: n_threads = 4 / 8 | AVX = 0 | AVX2 = 0 | AVX512 = 0 | FMA = 0 | NEON = 1 | ARM_FMA = 1 | F16C = 0 | FP16_VA = 1 | WASM_SIMD = 0 | BLAS = 1 | SSE3 = 0 | VSX = 0 |

main: prompt: ' What is DSLU?'
main: number of tokens in prompt = 7
     1 -> ''
  1724 -> ' What'
   338 -> ' is'
   360 -> ' D'
 12750 -> 'SL'
 29965 -> 'U'
 29973 -> '?'

sampling parameters: temp = 0.800000, top_k = 40, top_p = 0.950000, repeat_last_n = 64, repeat_penalty = 1.100000


 What is DSLU?
!#
 " ⁇
     !$#  ⁇
            #	$$


 ⁇ "	 [end of text]

llama_print_timings:     load time =     9.97 ms
llama_print_timings:   sample time =   135.71 ms /    79 runs (    1.72 ms per run)
llama_print_timings:     eval time = 261506.19 ms /    78 runs ( 3352.64 ms per run)
llama_print_timings:    total time = 266749.28 ms
philipp@M1Prou llama.cpp %
```