# 大模型微调实验报告：Qwen2-1.5B 物联网领域指令微调

## 1. 实验目标
使 Qwen2-1.5B 基座模型能够更专业、准确地回答物联网（IoT）相关的问题，验证 QLoRA 微调在垂直领域的效果。

## 2. 数据集
- **来源**：人工构造 + AI 生成（DeepSeek）
- **格式**：Alpaca 风格 `{instruction, input, output}`
- **规模**：30 条（示例见附录）
- **示例**：
- 
## 3. 环境与模型
- **基座模型**：Qwen/Qwen2-1.5B-Instruct
- **微调框架**：Unsloth + HuggingFace transformers
- **硬件**：Google Colab T4 GPU (16GB 显存)
- **显存占用**：约 8GB（4-bit QLoRA）

## 4. 微调配置（QLoRA）
| 参数 | 值 |
|------|----|
| r (rank) | 16 |
| lora_alpha | 16 |
| target_modules | q_proj, k_proj, v_proj, o_proj, gate_proj, up_proj, down_proj |
| 训练轮数 | 10 |
| 学习率 | 2e-4 |
| 批次大小 | 2 |
| 量化 | 4-bit (load_in_4bit=True) |

## 5. 训练结果
- **最终 loss**：0.32（附 loss 曲线截图，可在 Colab 中截图或从 logs 提取）
- **可训练参数量**：18.46M / 1.56B（约 1.18%）

## 6. 微调前后对比（5 个测试问题）
| 问题 | 微调前回答 | 微调后回答 |
|------|-----------|-----------|
| 什么是物联网？ | （通用定义） | （更详细、更结构化） |
| 物联网如何用于农业？ | （泛泛而谈） | （具体例子：土壤传感器、自动灌溉） |
| ... | ... | ... |

> 你可以自己运行对比：加载基座模型和 LoRA 模型，对同一问题生成答案。

## 7. Badcase 分析
- 模型仍然会偶尔重复回答或偏离主题，可能是因为训练数据量较少（仅 30 条）。
- 对于未在数据中出现的复杂技术问题（如“MQTT 协议与 CoAP 的区别”），模型无法给出准确答案，需要扩充数据或使用 RAG。

## 8. 模型发布
已上传至 HuggingFace Hub：  
[wojushuai/qwen2-1.5b-iot-lora](https://huggingface.co/wojushuai/qwen2-1.5b-iot-lora)

## 9. 结论与下一步
QLoRA 微调在极小数据量下仍能有效改变模型回答风格和部分事实准确性。下一步可扩充至 200+ 条数据，并尝试多轮对话微调。

## 附录：部分训练数据
（粘贴 3-5 条原始 JSON）
