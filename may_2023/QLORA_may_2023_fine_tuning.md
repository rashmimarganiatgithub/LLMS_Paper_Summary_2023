
<img width="446" alt="Screenshot 2023-05-29 at 9 10 17 PM" src="https://github.com/rashmimarganiatgithub/LLMS_in_2023/assets/24475962/d73d27a8-68e2-4fe6-a7ee-88c05eed776d">


## Abstract

QLORA is an efficient method for fine-tuning large language models on limited GPU memory, achieving comparable performance to full precision models. 
Its key innovations include 4-bit quantization, double quantization, and paged optimizers to save memory. 
The resulting Guanaco models outperform previous models on the Vicuna benchmark, reaching 99.3% of ChatGPT's performance with only 24 hours of fine-tuning on a single GPU. 
The researchers also provide an extensive analysis of chatbot performance and release their models and code for further exploration.



## Introduction

We introduce QLORA, an efficient method for fine-tuning large language models on limited GPU memory without compromising performance. 
QLORA utilizes 4-bit quantization and Low-rank Adapters (LoRA) to reduce memory usage while maintaining predictive accuracy. 
Our Guanaco models surpass previous benchmarks, achieving up to 99.3% of ChatGPT's performance on the Vicuna benchmark with just 24 hours of fine-tuning on a single GPU.
QLORA's innovations include 4-bit NormalFloat quantization, double quantization to reduce memory footprint, and paged optimizers to manage memory spikes. 
We train over 1,000 models with QLORA, analyzing instruction finetuning and chatbot performance across various dataset sizes and model architectures. 
Notably, our research demonstrates that data quality is more important than dataset size, and we provide a comprehensive analysis of chatbot performance using both human and GPT-4 
evaluations. All our models, code, and CUDA kernels are released for further exploration.


## Background

In the background, we explain the concept of quantization, which is the process of converting data from a higher bit representation to a 
lower bit representation. To overcome the issue of outliers during quantization, input tensors are divided into blocks that are independently quantized. 
We also introduce Low-rank Adapters (LoRA), a parameter-efficient fine-tuning method that uses a small set of trainable parameters while keeping the majority of 
the model weights fixed.We discuss the memory requirements of LoRA during training and find that the memory footprint mainly comes from activation gradients 
rather than the LoRA parameters themselves. This allows us to use more adapters without significantly increasing the overall memory usage, which is crucial for achieving 
full 16-bit precision performance in large language models.


## QLORA Finetuning

QLORA achieves accurate 4-bit fine-tuning through two techniques: 4-bit NormalFloat (NF4) quantization and Double Quantization. 
To prevent memory spikes and enable fine-tuning on a single machine for large models, QLORA introduces Paged Optimizers. 
With one low-precision storage data type (usually 4-bit) and one computation data type (BFloat16), QLORA dequantizes weight tensors to BFloat16 and performs matrix 
multiplication in 16-bit precision.In 4-bit NormalFloat quantization, quantile estimation is computationally feasible when input tensors 
come from a fixed distribution. By normalizing the weight tensors to fit within a specific range, we achieve an information-theoretically optimal data type for 
zero-centered normal distributions. Double Quantization reduces memory usage by quantizing the quantization constants, resulting in significant memory savings per parameter.
Using Paged Optimizers, QLORA leverages NVIDIA unified memory feature to manage GPU memory efficiently, automatically 
transferring data between CPU RAM and GPU memory when needed. In the QLORA definition, a linear layer in the quantized base model with a single LoRA adapter is 
fine-tuned using the NF4 storage data type, BFloat16 computation data type, and specific block sizes for improved precision and memory conservation.

![b028fa9e-3a23-4259-85b5-0446f5989455_600x439](https://github.com/rashmimarganiatgithub/LLMS_in_2023/assets/24475962/e8f58d42-cbdd-4735-a75b-7103ce978e42)


## QLoRA vs. Standard Finetuning

QLoRA, a memory-efficient method for fine-tuning models, is compared to standard fine-tuning to assess its performance. Experiments on different architectures and datasets reveal that QLoRA with 16-bit adapter-finetuning matches the performance of full-finetuning for models up to 3B parameters. Additionally, using the NormalFloat4 data type in QLoRA provides better accuracy gains compared to regular 4-bit floats. These findings indicate that 4-bit QLoRA tuning reliably produces results comparable to 16-bit methods, offering efficiency benefits without sacrificing performance

Pushing the Chatbot State-of-the-art with QLoRA


# Qualitative Analysis

Qualitative analysis was performed alongside quantitative analysis to address the limitations of relying solely on summary statistics in the evaluation. Representative examples were collected to demonstrate observed patterns in the text generated by the Guanaco model, highlighting its strengths and weaknesses. The study also identified biases in automated evaluation systems and emphasized the need for future research to explore approaches to mitigate these biases and investigate the impact of multilingual training on model performance.

# Limitations and Discussion

Our method, QLORA, demonstrates the ability to replicate the performance of 16-bit full finetuning using a 4-bit base model and Low-rank Adapters (LoRA), but we did not establish whether it can match the performance of full 16-bit finetuning at larger scales. Additionally, while we evaluated QLORA on multiple benchmarks, we acknowledge the need for further evaluation on other benchmarks to ensure generalizability. Furthermore, although we conducted a responsible AI evaluation of Guanaco, its performance in terms of other biases and types of evaluation requires additional investigation in future work.

We did not evaluate the use of different bit-precisions or alternative adapter methods, such as 3-bit base models or Parameter Efficient FineTuning (PEFT) methods. While LoRA has shown robustness, exploring other adapters could potentially yield better performance and enable more aggressive quantization, such as achieving 16-bit full finetuning performance with a 3-bit GPTQ quantization of the basemodel using LoRA.


# Broader Impacts

Our QLORA finetuning method allows the training of 33B parameter models on a single consumer GPU and 65B parameter models on a single professional GPU without sacrificing performance compared to full finetuning. This accessibility breakthrough can benefit researchers with limited resources, bridging the gap between large corporations and small teams. Furthermore, QLORA has the potential to enable finetuning of language models on mobile phones and low-resource settings, opening up novel applications and privacy-preserving usage. While acknowledging the risks associated with LLMs, we believe that widespread access to finetuning technology promotes independent analysis and outweighs the potential harms, making high-quality LLMs more widely accessible and beneficial.

Paper  👉 https://arxiv.org/abs/2305.14314?utm_source=substack&utm_medium=email
Code & demos 👉 [Making LLMs even more accessible with bitsandbytes, 4-bit quantization and QLoRA
](https://huggingface.co/blog/4bit-transformers-bitsandbytes?utm_source=substack&utm_medium=email) 
QLORA github repro 👉 [QLORA](https://github.com/artidoro/qlora)
