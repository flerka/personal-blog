---
layout: post
title: Free Options for Running and Training Language Models in 2025
subtitle: 
tags: [llm, machine-learning]
comments: true
---
After doing some research and finding free alternatives to running your machine learning model on a home machine, I decided to post this short write-up of my findings. It is relevant as of April 2025 but will probably change in the future.

## Services that Provide Inference
Services that provide some limited model inference (calling a model to generate output) for free include huggingface.co and openrouter.ai. Both of them provide a UI and API inference access to specific models with some rate limits (i.e., the number of requests per day). While this can be limiting, it is also useful if you want to quickly check out a model before investing more time into it. For example, I took a quick peek at Phi-3 Medium's output to compare it with Phi-3 Mini, which I had previously worked with, on OpenRouter.

## Services that Provide Inference and Fine-Tuning Options
Both Google Colab and Kaggle provide access to Jupyter Notebooks through which you can access computational resources and execute your machine learning code. Both services offer access for a specific number of hours per day (Google Colab) or week (Kaggle) to CPU and GPU resources. They support machine learning inference and fine-tuning for smaller LLMs. I used both Google Colab and Kaggle to fine-tune models from Phi 3 family with good results. At the moment, Kaggle provides, in my opinion, better resources on the free tier: one P100 GPU or two T4 GPUs, compared to one T4 GPU provided with Google Colab. Although two T4 GPUs provide more video memory, allowing training of larger LLMs, your framework needs to support multi-GPU setups — and in general, this approach makes the code more complicated. 

## What to Read
- https://huggingface.co/docs/inference-providers/index
- https://openrouter.ai/models?q=free
- https://colab.research.google.com/
- https://www.kaggle.com/