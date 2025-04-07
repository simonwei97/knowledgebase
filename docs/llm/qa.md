

## LLM为什么大多都是 Decoder-only 架构

LLM是 `Large Language Model` 的简写，目前一般指百亿参数以上的语言模型，主要面向文本生成任务。

1. **Encoder 低秩问题**：Encoder的双向注意力会存在低秩问题，可能削弱模型表达能力。对于生成任务而言，引入双向注意力并无实质好处
2. 有更好的 **Zero-Shot**(零样本学习) 性能，更适合大语料的自监督学习：
3. **Decoder-only 支持一直复用 KV-Cache**，对多轮对话更友好，因为每个Token的表示之和它之前的输入有关，而encoder-decoder和PrefixLM就难以做到。


> [苏剑林: 为什么现在的LLM都是Decoder-only的架构？](https://kexue.fm/archives/9529)