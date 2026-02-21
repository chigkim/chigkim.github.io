---
layout: post
title: "Interesting Observation from a Simple Multi-Agent Experiment with 10 Different Models"
date: 2026-02-21
---

I ran a small personal experiment to autonomously summarize 10 transcripts using a multi-agent workflow on Codex.

<!--more-->

The following sub-100B models failed to complete this simple task reliably:

* qwen3-coder-next
* glm-4.7-flash
* Devstral-Small-2
* gpt-oss-20b

A lot of times they struggled to used the tools correctly, sometimes they processed a few transcripts and then stopped, and sometimes they got stuck in infinite loops.

However, the following models > 100b were able to consistently complete the task:

* gpt-oss:120b
* minimax-m2.5
* qwen3.5
* deepseek-v3.2
* glm-5
* kimi-k2.5

There was one twist. When I increased reasoning effort from medium to high, often (but not always) gpt-oss-20b was also able to complete the task!

Here is my test if anyone wants to try with your own setup.

https://github.com/chigkim/collaborative-agent

Observation: To get reliable results from an agentic workflow, it seem necessary to use models > 100b like gpt-oss-120b at least.

---

If you are still reading, here is additional background with detailed.

I needed a model to handle a task involving analyzing, organizing, and processing about 50 articles, but the local models I tried really struggled seriously.

Gemini-cli with gemini-2.5-pro, claude-code with Opus 4.6, and Codex with gpt-5.3-codex were able to complete the same task and produce decent quality output.

So I stripped the original workflow down to the bare minimum and turned it into a much much simpler challenge to test whether a local model can reliably run a multi agent workflow.

In this challenge, an orchestrator agent is instructed to spawn one sub-agent a time and hand one  file to each worker to summarize in specific format. Then it is asked to review their work and retry when a worker agent fails to produce output that meets the work specs.

To keep it short and simple, there are only total 10 speech transcripts from Ted Talk, about 4K tokens per file.

Despite the simplification, I still wasn't able to get the local models to reliably complete the task via Codex.

I know this can be easily done and get much better quality by making a script to feed one article at a time, but I wanted to test instruction following, multi agent, and tool call capability for local models.

The repo just has prompts for agents and files to process. There's no code involved. Feel free to modify the prompts to fit your setup if necessary.

There is a README, but the basic idea IS to  use any local agentic setup that can:

1. launch a sub agent,
2. support autonomous (AKA YOLO) mode,
3. and read AGENTS.md at startup.

To test:

1. Configure your LLM engine to handle at least 2 parallel requests.
2. Configure your agentic CLI to use your local LLM engine.
3. Start your agentic CLI in yolo mode and tell it to perform the task as the orchestrator agent.

If you are using Codex, update to the latest version and enable multi_agent by adding the following to ~/.codex/config.toml.

    [features]
    multi_agent = true

You might also want to add `stream_idle_timeout_ms = 10000000` under your model_providers setting if your model takes a while to respond.

Here is my setup:

I used the flags for llama.cpp that unsloth recommended for each model. Interestingly models running on Ollama sometimes went little further.

* Agentic CLI: Codex
* Model Engine: llama.cpp and Ollama
* Local models tested:
    * ggml-org/gpt-oss-20b-mxfp4.gguf
    * unsloth/Qwen3-Coder-Next-Q4_K_M.gguf
    * unsloth/GLM-4.7-Flash-Q8_0.gguf
    * unsloth/Devstral-Small-2-24B-Instruct-2512-Q8_0.gguf
* Context size allocated: 64k

I also tested the smaller models via OpenRouter to rule out local setup issues.

I tested the following larger models with openrouter:

* gpt-oss-120b
* minimax-m2.5
* qwen3.5
* deepseek-v3.2
* glm-5
* kimi-k2.5
