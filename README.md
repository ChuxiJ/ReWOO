# ReWOO
Official implementation for paper: _ReWOO: Decoupling Reasoning from Observations for Efficient Augmented Language Models._
This is a tool-augmented LM paradigm, leveraging _foreseeable reasoning_ ability of language models to improve system parameter and prompt efficiency.

## Overview

Augmented Language Models (ALMs) blend the reasoning capabilities of Large Language Models (LLMs) with tools that allow for knowledge retrieval and action execution. Existing ALM systems trigger LLM thought processes while pulling observations from these tools in an interleaved fashion. Specifically, an LLM reasons to call an external tool, gets halted to fetch the tool’s response, and then decides the next action based on all preceding response tokens. Such a paradigm, though straightforward and easy to implement, often leads to huge computation complexity from redundant prompts and repeated execution. This study addresses such challenges for the first time, proposing a modular paradigm ReWOO (Reasoning WithOut Observation) that detaches the reasoning process from external observations, thus significantly reducing token consumption. Comprehensive evaluations across six public NLP benchmarks and a curated dataset reveal consistent performance enhancements with our proposed methodology. Notably, ReWOO achieves 5× token efficiency and 4% accuracy improvement on HotpotQA, a multi-step reasoning benchmark. Furthermore, ReWOO demonstrates robustness under tool-failure scenarios. Beyond prompt efficiency, decoupling parametric modules from nonparametric tool calls enables instruction fine-tuning to offload LLMs into smaller language models, thus substantially reducing model parameters. Our illustrative work offloads reasoning ability from 175B GPT3.5 into 7B LLaMA, demonstrating the significant potential for truly efficient and scalable ALM systems. 

<p align="center">
  <img width="300" height="300" src="./AGI.png">
</p>

## Installation
```
pip install -r requirements.txt
```
Generate API keys from [OpenAI](https://openai.com/blog/openai-api), [WolframAlpha](https://products.wolframalpha.com/api) and [SerpApi](https://serpapi.com/). Then save the keys to `./keys/openai.key`, `./keys/wolfram.key` and `./keys/serpapi.key` respectively.


## Single Run
```
 python run.py --method rewoo --toolset Google LLM --base_lm text-davinci-003 
```
Use `--method` to choose your prompt paradigm among `'direct', 'cot', 'react', 'rewoo'`

Use `--toolset` to provide available tools, including `'Google', 'Wikipedia', 'WolframAlpha', 'LLM', 'Calculator', 'SearchSOTU'`

Use `--base_lm` to choose a base language model, can be either `gpt-3.5-turbo`, `text-davinci-003` or `directory_to_alpca-lora_adapter`. You can also individually assign `--planner_lm` and `--solver_lm` for `rewoo`. 

Add `--print_trajectory` to print intermediate reasoning.


## Batch Evaluation on Benchmarks
```
python run_eval.py --method rewoo --dataset hotpot_qa --sample_size 10 --toolset Wikipedia LLM --base_lm gpt-3.5-turbo --save_result`
```

Use `--sample_size` to specify number of samples to evaluate.

Use `--save_result` to save evaluation results to `./results/`.

## Data & Model

Except public NLP benchmarks, we also evaluate ReWOO on a curated QA dataset over SOTU2023 [here](https://huggingface.co/datasets/rewoo/sotu_qa_2023). It is especially useful for ALMs to examine the model's tool-augmented ability in answering over private document.

We uploaded our instruction-tuned Planner 7B model (based on Alpaca 7B) [here](https://huggingface.co/rewoo/planner_7B). The instruction dataset (available [here](https://huggingface.co/datasets/rewoo/planner_instruction_tuning_2k/blob/main/README.md)) is a mixture of "correct" HotpotQA and TriviaQA task planning trajectories in ReWOO Framework.
