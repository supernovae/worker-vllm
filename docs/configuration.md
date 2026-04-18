# Configuration Reference

Complete guide to all environment variables and configuration options for worker-vllm.

## LLM Settings

| Variable                       | Default             | Type/Choices                                                | Description                                                                     |
| ------------------------------ | ------------------- | ----------------------------------------------------------- | ------------------------------------------------------------------------------- |
| `MODEL_NAME`                   | 'facebook/opt-125m' | `str`                                                       | Name or path of the Hugging Face model to use.                                  |
| `MODEL_REVISION`               | 'main'              | `str`                                                       | Model revision to load (default: main).                                         |
| `TOKENIZER`                    | None                | `str`                                                       | Name or path of the Hugging Face tokenizer to use.                              |
| `SKIP_TOKENIZER_INIT`          | False               | `bool`                                                      | Skip initialization of tokenizer and detokenizer.                               |
| `TOKENIZER_MODE`               | 'auto'              | ['auto', 'slow']                                            | The tokenizer mode.                                                             |
| `TRUST_REMOTE_CODE`            | `False`             | `bool`                                                      | Trust remote code from Hugging Face.                                            |
| `DOWNLOAD_DIR`                 | None                | `str`                                                       | Directory to download and load the weights.                                     |
| `LOAD_FORMAT`                  | 'auto'              | `str`                                                       | The format of the model weights to load.                                        |
| `HF_TOKEN`                     | -                   | `str`                                                       | Hugging Face token for private and gated models.                                |
| `DTYPE`                        | 'auto'              | ['auto', 'half', 'float16', 'bfloat16', 'float', 'float32'] | Data type for model weights and activations.                                    |
| `KV_CACHE_DTYPE`               | 'auto'              | ['auto', 'fp8']                                             | Data type for KV cache storage.                                                 |
| `QUANTIZATION_PARAM_PATH`      | None                | `str`                                                       | Path to the JSON file containing the KV cache scaling factors.                  |
| `MAX_MODEL_LEN`                | None                | `int`                                                       | Model context length.                                                           |
| `GUIDED_DECODING_BACKEND`      | 'outlines'          | ['outlines', 'lm-format-enforcer']                          | Which engine will be used for guided decoding by default.                       |
| `DISTRIBUTED_EXECUTOR_BACKEND` | None                | ['ray', 'mp']                                               | Backend to use for distributed serving.                                         |
| `WORKER_USE_RAY`               | False               | `bool`                                                      | Deprecated, use --distributed-executor-backend=ray.                             |
| `PIPELINE_PARALLEL_SIZE`       | 1                   | `int`                                                       | Number of pipeline stages.                                                      |
| `TENSOR_PARALLEL_SIZE`         | 1                   | `int`                                                       | Number of tensor parallel replicas.                                             |
| `MAX_PARALLEL_LOADING_WORKERS` | None                | `int`                                                       | Load model sequentially in multiple batches.                                    |
| `RAY_WORKERS_USE_NSIGHT`       | False               | `bool`                                                      | If specified, use nsight to profile Ray workers.                                |
| `ENABLE_PREFIX_CACHING`        | False               | `bool`                                                      | Enables automatic prefix caching.                                               |
| `DISABLE_SLIDING_WINDOW`       | False               | `bool`                                                      | Disables sliding window, capping to sliding window size.                        |
| `NUM_LOOKAHEAD_SLOTS`          | 0                   | `int`                                                       | Experimental scheduling config necessary for speculative decoding.              |
| `SEED`                         | 0                   | `int`                                                       | Random seed for operations.                                                     |
| `NUM_GPU_BLOCKS_OVERRIDE`      | None                | `int`                                                       | If specified, ignore GPU profiling result and use this number of GPU blocks.    |
| `MAX_NUM_BATCHED_TOKENS`       | None                | `int`                                                       | Maximum number of batched tokens per iteration.                                 |
| `MAX_NUM_SEQS`                 | 256                 | `int`                                                       | Maximum number of sequences per iteration.                                      |
| `MAX_LOGPROBS`                 | 20                  | `int`                                                       | Max number of log probs to return when logprobs is specified in SamplingParams. |
| `DISABLE_LOG_STATS`            | False               | `bool`                                                      | Disable logging statistics.                                                     |
| `QUANTIZATION`                 | None                | ['awq', 'squeezellm', 'gptq', 'bitsandbytes']               | Method used to quantize the weights.                                            |
| `ROPE_SCALING`                 | None                | `dict`                                                      | RoPE scaling configuration in JSON format.                                      |
| `ROPE_THETA`                   | None                | `float`                                                     | RoPE theta. Use with rope_scaling.                                              |
| `TOKENIZER_POOL_SIZE`          | 0                   | `int`                                                       | Size of tokenizer pool to use for asynchronous tokenization.                    |
| `TOKENIZER_POOL_TYPE`          | 'ray'               | `str`                                                       | Type of tokenizer pool to use for asynchronous tokenization.                    |
| `TOKENIZER_POOL_EXTRA_CONFIG`  | None                | `dict`                                                      | Extra config for tokenizer pool.                                                |

## LoRA (Low-Rank Adaptation) Settings

| Variable                    | Default | Type                                       | Description                                                                                               |
| --------------------------- | ------- | ------------------------------------------ | --------------------------------------------------------------------------------------------------------- |
| `ENABLE_LORA`               | False   | `bool`                                     | If True, enable handling of LoRA adapters.                                                                |
| `MAX_LORAS`                 | 1       | `int`                                      | Max number of LoRAs in a single batch.                                                                    |
| `MAX_LORA_RANK`             | 16      | `int`                                      | Max LoRA rank.                                                                                            |
| `LORA_EXTRA_VOCAB_SIZE`     | 256     | `int`                                      | Maximum size of extra vocabulary for LoRA adapters.                                                       |
| `LORA_DTYPE`                | 'auto'  | ['auto', 'float16', 'bfloat16', 'float32'] | Data type for LoRA.                                                                                       |
| `LONG_LORA_SCALING_FACTORS` | None    | `tuple`                                    | Specify multiple scaling factors for LoRA adapters.                                                       |
| `MAX_CPU_LORAS`             | None    | `int`                                      | Maximum number of LoRAs to store in CPU memory.                                                           |
| `FULLY_SHARDED_LORAS`       | False   | `bool`                                     | Enable fully sharded LoRA layers.                                                                         |
| `LORA_MODULES`              | `[]`    | `list[dict]`                               | Add lora adapters from Hugging Face `[{"name": "xx", "path": "xxx/xxxx", "base_model_name": "xxx/xxxx"}]` |

> **Note (Serverless)**: When LoRA adapters are configured via `LORA_MODULES`, initialization is deferred to the first request to ensure compatibility with RunPod Serverless. This means the first request will include LoRA loading time. Subsequent requests are unaffected. Check logs for "LoRA mode: X adapter(s) will load on first request" at startup.

## Speculative Decoding Settings

Speculative decoding can be configured in two ways:

### Option 1: JSON Configuration

Set `SPECULATIVE_CONFIG` to a JSON string with your full speculative decoding configuration:

```bash
SPECULATIVE_CONFIG='{"method": "ngram", "num_speculative_tokens": 5, "prompt_lookup_max": 4}'
```

### Option 2: Individual Environment Variables

| Variable                                 | Default | Type/Choices                                                       | Description                                                                               |
| ---------------------------------------- | ------- | ------------------------------------------------------------------ | ----------------------------------------------------------------------------------------- |
| `SPECULATIVE_METHOD`                     | None    | ['draft_model', 'ngram', 'eagle', 'eagle3', 'medusa', 'mlp_speculator', 'suffix'] | Speculative decoding method to use.                                                       |
| `SPECULATIVE_MODEL`                      | None    | `str`                                                              | The name of the draft model to be used in speculative decoding.                           |
| `NUM_SPECULATIVE_TOKENS`                 | None    | `int`                                                              | The number of speculative tokens to sample from the draft model.                          |
| `SPECULATIVE_DRAFT_TENSOR_PARALLEL_SIZE` | None    | `int`                                                              | Number of tensor parallel replicas for the draft model.                                   |
| `SPECULATIVE_MAX_MODEL_LEN`              | None    | `int`                                                              | The maximum sequence length supported by the draft model.                                 |
| `SPECULATIVE_DISABLE_BY_BATCH_SIZE`      | None    | `int`                                                              | Disable speculative decoding if the number of enqueue requests is larger than this value. |
| `NGRAM_PROMPT_LOOKUP_MAX`                | None    | `int`                                                              | Max size of window for ngram prompt lookup in speculative decoding.                       |
| `NGRAM_PROMPT_LOOKUP_MIN`                | None    | `int`                                                              | Min size of window for ngram prompt lookup in speculative decoding.                       |

If `SPECULATIVE_CONFIG` is set, it takes priority over individual env vars. When using individual env vars without `SPECULATIVE_METHOD`, the method is auto-detected from the model name or configuration.

> **Suffix Decoding**: The `suffix` method is model-free — it does not require a `SPECULATIVE_MODEL`. It uses suffix trees to pattern-match repeating sequences from past generations, making it especially effective for code-editing and agentic workloads. Requires the `arctic-inference` package (included in the container). When `NUM_SPECULATIVE_TOKENS` is not set, it defaults to 32.

## Scheduling & Performance Settings

| Variable                       | Default | Type/Choices    | Description                                                                                                                         |
| ------------------------------ | ------- | --------------- | ----------------------------------------------------------------------------------------------------------------------------------- |
| `GPU_MEMORY_UTILIZATION`       | `0.95`  | `float`         | Sets GPU VRAM utilization.                                                                                                          |
| `MAX_PARALLEL_LOADING_WORKERS` | `None`  | `int`           | Load model sequentially in multiple batches, to avoid RAM OOM when using tensor parallel and large models.                          |
| `BLOCK_SIZE`                   | `16`    | `8`, `16`, `32` | Token block size for contiguous chunks of tokens.                                                                                   |
| `SWAP_SPACE`                   | `4`     | `int`           | CPU swap space size (GiB) per GPU.                                                                                                  |
| `ENFORCE_EAGER`                | False   | `bool`          | Always use eager-mode PyTorch. If False(`0`), will use eager mode and CUDA graph in hybrid for maximal performance and flexibility. |
| `MAX_SEQ_LEN_TO_CAPTURE`       | `8192`  | `int`           | Maximum context length covered by CUDA graphs. When a sequence has context length larger than this, we fall back to eager mode.     |
| `DISABLE_CUSTOM_ALL_REDUCE`    | `0`     | `int`           | Enables or disables custom all reduce.                                                                                              |
| `ENABLE_EXPERT_PARALLEL`       | `False` | `bool`          | Enable Expert Parallel for MoE models.                                                                                              |
| `ATTENTION_BACKEND`            | `None`  | `str`           | Attention backend to use (e.g., `FLASH_ATTN`, `FLASHINFER`, `TRITON_FLASH_ATTN`). Replaces deprecated `VLLM_ATTENTION_BACKEND`.     |
| `ASYNC_SCHEDULING`             | `None`  | `bool`          | Enable async scheduling (overlaps engine scheduling with GPU execution). Default: enabled in vLLM 0.14.0+. Set to `false` to disable. |
| `STREAM_INTERVAL`              | `1`     | `int`           | Controls how often to yield streaming results. Lower = more frequent updates.                                                        |

## Tokenizer Settings

| Variable               | Default | Type/Choices                        | Description                                                                                       |
| ---------------------- | ------- | ----------------------------------- | ------------------------------------------------------------------------------------------------- |
| `TOKENIZER_NAME`       | `None`  | `str`                               | Tokenizer repository to use a different tokenizer than the model's default.                       |
| `TOKENIZER_REVISION`   | `None`  | `str`                               | Tokenizer revision to load.                                                                       |
| `CUSTOM_CHAT_TEMPLATE` | `None`  | `str` of single-line jinja template | Custom chat jinja template. [More Info](https://huggingface.co/docs/transformers/chat_templating) |

## Streaming & Batch Settings

The way this works is that the first request will have a batch size of `DEFAULT_MIN_BATCH_SIZE`, and each subsequent request will have a batch size of `previous_batch_size * DEFAULT_BATCH_SIZE_GROWTH_FACTOR`. This will continue until the batch size reaches `DEFAULT_BATCH_SIZE`. E.g. for the default values, the batch sizes will be `1, 3, 9, 27, 50, 50, 50, ...`. You can also specify this per request, with inputs `max_batch_size`, `min_batch_size`, and `batch_size_growth_factor`. This has nothing to do with vLLM's internal batching, but rather the number of tokens sent in each HTTP request from the worker.

| Variable                           | Default | Type/Choices | Description                                                                                               |
| ---------------------------------- | ------- | ------------ | --------------------------------------------------------------------------------------------------------- |
| `DEFAULT_BATCH_SIZE`               | `50`    | `int`        | Default and Maximum batch size for token streaming to reduce HTTP calls.                                  |
| `DEFAULT_MIN_BATCH_SIZE`           | `1`     | `int`        | Batch size for the first request, which will be multiplied by the growth factor every subsequent request. |
| `DEFAULT_BATCH_SIZE_GROWTH_FACTOR` | `3`     | `float`      | Growth factor for dynamic batch size.                                                                     |

## OpenAI Compatibility Settings

| Variable                            | Default     | Type/Choices     | Description                                                                                                                                                                                                       |
| ----------------------------------- | ----------- | ---------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `RAW_OPENAI_OUTPUT`                 | `1`         | boolean as `int` | Enables raw OpenAI SSE format string output when streaming. **Required** to be enabled (which it is by default) for OpenAI compatibility.                                                                         |
| `OPENAI_SERVED_MODEL_NAME_OVERRIDE` | `None`      | `str`            | Overrides the name of the served model from model repo/path to specified name, which you will then be able to use the value for the `model` parameter when making OpenAI requests                                 |
| `OPENAI_RESPONSE_ROLE`              | `assistant` | `str`            | Role of the LLM's Response in OpenAI Chat Completions.                                                                                                                                                            |
| `ENABLE_AUTO_TOOL_CHOICE`           | `false`     | `bool`           | Enables automatic tool selection for supported models. Set to `true` to activate.                                                                                                                                 |
| `TOOL_CALL_PARSER`                  | `None`      | `str`            | Specifies the parser for tool calls. Examples: `mistral`, `hermes`, `llama3_json`, `llama4_json`, `llama4_pythonic`, `granite`, `granite-20b-fc`, `deepseek_v3`, `internlm`, `jamba`, `phi4_mini_json`, `pythonic`, `qwen3_xml` (if supported by your vLLM build). |
| `REASONING_PARSER`                  | `None`      | `str`            | Parser for reasoning-capable models (enables reasoning mode). Examples: `deepseek_r1`, `qwen3`, `granite`, `hunyuan_a13b`. Leave unset to disable.                                                                |
| `TRUST_REQUEST_CHAT_TEMPLATE`       | `false`     | `bool`           | Allow clients to send custom chat templates in API requests. **Security consideration:** Only enable if you trust your API clients.                                                                               |
| `RETURN_TOKENS_AS_TOKEN_IDS`        | `false`     | `bool`           | Return token IDs instead of decoded text strings in responses.                                                                                                                                                     |
| `EXCLUDE_TOOLS_WHEN_TOOL_CHOICE_NONE` | `false`   | `bool`           | Exclude tool definitions from the prompt when `tool_choice` is set to `none`.                                                                                                                                      |
| `ENABLE_PROMPT_TOKENS_DETAILS`      | `false`     | `bool`           | Include detailed prompt token information in API responses.                                                                                                                                                        |
| `ENABLE_FORCE_INCLUDE_USAGE`        | `false`     | `bool`           | Always include usage statistics in API responses, even when not requested.                                                                                                                                         |
| `ENABLE_LOG_OUTPUTS`                | `false`     | `bool`           | Log model outputs for debugging purposes.                                                                                                                                                                          |
| `LOG_ERROR_STACK`                   | `false`     | `bool`           | Include full stack traces in error responses for debugging.                                                                                                                                                        |

## Serverless & Concurrency Settings

| Variable               | Default | Type/Choices | Description                                                                                                                                                                |
| ---------------------- | ------- | ------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `MAX_CONCURRENCY`      | `30`    | `int`        | Max concurrent requests per worker. vLLM has an internal queue, so you don't have to worry about limiting by VRAM, this is for improving scaling/load balancing efficiency |
| `DISABLE_LOG_STATS`    | False   | `bool`       | Enables or disables vLLM stats logging.                                                                                                                                    |
| `ENABLE_LOG_REQUESTS`  | False   | `bool`       | Enables vLLM request logging. (Replaces deprecated `DISABLE_LOG_REQUESTS` in vLLM 0.15.0)                                                                                  |

## Advanced Settings

| Variable                    | Default | Type    | Description                                                                                                                                            |
| --------------------------- | ------- | ------- | ------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `MODEL_LOADER_EXTRA_CONFIG` | None    | `dict`  | Extra config for model loader.                                                                                                                         |
| `PREEMPTION_MODE`           | None    | `str`   | If 'recompute', the engine performs preemption-aware recomputation. If 'save', the engine saves activations into the CPU memory as preemption happens. |
| `PREEMPTION_CHECK_PERIOD`   | 1.0     | `float` | How frequently the engine checks if a preemption happens.                                                                                              |
| `PREEMPTION_CPU_CAPACITY`   | 2       | `float` | The percentage of CPU memory used for the saved activations.                                                                                           |
| `DISABLE_LOGGING_REQUEST`   | False   | `bool`  | Disable logging requests.                                                                                                                              |
| `MAX_LOG_LEN`               | None    | `int`   | Max number of prompt characters or prompt ID numbers being printed in log.                                                                             |

## UPPERCASED env vars: Pass any engine arg

Any vLLM `AsyncEngineArgs` field can be set via an environment variable using the **UPPERCASED** field name (the same names vLLM uses). The worker auto-discovers all fields from env — no prefix.

**Format:** `<FIELD_NAME_UPPERCASED>=<value>` (e.g. `MAX_MODEL_LEN=4096`)

**Examples:**

| Environment Variable     | vLLM Engine Arg          | Value Example |
| ------------------------ | ------------------------ | ------------- |
| `MAX_MODEL_LEN`          | `max_model_len`          | `4096`        |
| `ENFORCE_EAGER`          | `enforce_eager`          | `true`        |
| `ENABLE_CHUNKED_PREFILL` | `enable_chunked_prefill` | `true`        |
| `NUM_SCHEDULER_STEPS`    | `num_scheduler_steps`    | `8`           |
| `TOKENIZER_POOL_SIZE`    | `tokenizer_pool_size`   | `4`           |

**Backward-compat aliases:** `MODEL_NAME` → `model`, `TOKENIZER_NAME` → `tokenizer`, `MAX_CONTEXT_LEN_TO_CAPTURE` → `max_seq_len_to_capture`, `MODEL_REVISION` → `revision`.

**Notes:**
- Only valid `AsyncEngineArgs` fields are applied. Unknown keys are silently ignored.
- Values are automatically cast to the correct type (`int`, `float`, `bool`, `str`, or JSON for `dict`/`list`/`tuple`).
- For a full list of available engine args, see the [vLLM AsyncEngineArgs documentation](https://docs.vllm.ai/en/latest/configuration/engine_args/).

## CLI Flag to Env Var Mapping (RunPod Serverless)

If you are coming from `vllm serve ...` flags, use these env vars in RunPod endpoint configuration:

| vLLM CLI flag | Worker env var | Notes |
| --- | --- | --- |
| `--max-model-len` | `MAX_MODEL_LEN` | Same numeric value. |
| `--gpu-memory-utilization` | `GPU_MEMORY_UTILIZATION` | Float between `0.0` and `1.0`. |
| `--tensor-parallel-size` | `TENSOR_PARALLEL_SIZE` | On multi-GPU workers this repo may auto-set tensor parallel to GPU count. |
| `--enforce-eager` | `ENFORCE_EAGER` | Set to `true` or `false`. |
| `--enable-prefix-caching` | `ENABLE_PREFIX_CACHING` | Enables vLLM prefix caching. |
| `--enable-auto-tool-choice` | `ENABLE_AUTO_TOOL_CHOICE` | OpenAI-compatible tool auto-selection. |
| `--tool-call-parser` | `TOOL_CALL_PARSER` | Free-form parser name, e.g. `qwen3_xml` when supported. |
| `--enable-prompt-tokens-details` | `ENABLE_PROMPT_TOKENS_DETAILS` | Adds prompt token details in OpenAI responses. |
| `--speculative_draft_model` | `SPECULATIVE_MODEL` | Uses speculative decoding draft model wiring in this worker. |

`--host` and `--port` do not apply to this serverless worker. RunPod manages ingress; configure the endpoint via env vars.

## Qwen Serverless Example

Example endpoint env configuration equivalent to your requested setup:

```bash
MODEL_NAME=RedHatAI/Qwen3-Coder-Next-NVFP4
TENSOR_PARALLEL_SIZE=1
GPU_MEMORY_UTILIZATION=0.70
MAX_MODEL_LEN=262144
ENFORCE_EAGER=true
ENABLE_AUTO_TOOL_CHOICE=true
TOOL_CALL_PARSER=qwen3_xml
ENABLE_PROMPT_TOKENS_DETAILS=true
ENABLE_PREFIX_CACHING=true
SPECULATIVE_METHOD=draft_model
SPECULATIVE_MODEL=Qwen/qwen2.5-coder-1.5b
```

## Recommended GPU Memory Utilization (Serverless)

`GPU_MEMORY_UTILIZATION` controls how much VRAM vLLM reserves for weights, activations, and KV cache. On serverless, leaving some headroom usually improves startup and runtime stability.

Suggested starting points:

| Workload profile | Recommended start |
| --- | --- |
| Long context or speculative decoding enabled | `0.70` to `0.80` |
| General chat/instruct workloads | `0.80` to `0.90` |
| Maximum throughput after stability is confirmed | `0.90` to `0.95` |

Tuning guidance:

- If you see OOMs during warmup or first large prompts, lower by `0.05`.
- If stable under production prompt lengths, increase gradually by `0.02` to `0.03`.
- Prefix caching and long contexts increase KV cache pressure; keep extra headroom for those workloads.
- For your Qwen config (`MAX_MODEL_LEN=262144`, prefix caching, speculative decoding), `0.70` is a good conservative default.

## Docker Build Arguments

These variables are used when building custom Docker images with models baked in:

| Variable              | Default          | Type  | Description                                       |
| --------------------- | ---------------- | ----- | ------------------------------------------------- |
| `BASE_PATH`           | `/runpod-volume` | `str` | Storage directory for huggingface cache and model |
| `WORKER_CUDA_VERSION` | `13.0.2`         | `str` | CUDA version for the worker image                 |

## Deprecated Variables

⚠️ **The following variables are deprecated and will be removed in future versions:**

| Old Variable                 | New Variable             | Note                                                                 |
| ---------------------------- | ------------------------ | -------------------------------------------------------------------- |
| `MAX_CONTEXT_LEN_TO_CAPTURE` | `MAX_SEQ_LEN_TO_CAPTURE` | Use new variable name                                                |
| `kv_cache_dtype=fp8_e5m2`    | `kv_cache_dtype=fp8`     | Simplified fp8 format                                                |
| `USE_V2_BLOCK_MANAGER`       | *(removed)*              | V2 block manager is now the default in vLLM 0.13.0, setting ignored  |
| `VLLM_ATTENTION_BACKEND`     | `ATTENTION_BACKEND`      | Use new env var name (old still works with deprecation warning)      |
| `DISABLE_LOG_REQUESTS`       | `ENABLE_LOG_REQUESTS`    | Inverted logic in vLLM 0.15.0 (old still works with deprecation warning) |

