<h1 align="center">InduOCRBench</h1>

<div align="center">
<a href="./README.md">English</a> | 简体中文
</div>

[[📜 arXiv]](https://arxiv.org/abs/2605.00911) | [[Dataset (🤗Hugging Face)]](https://huggingface.co/datasets/qihoo360/InduOCRBench)

---

## News

- **[2026-04]** InduOCRBench 论文被 ACL 2026 Industry Track 接收，数据集正式发布。

---
<p align="center">

![](assets/InduOCRBench_overview.png)

</p>

---

## 📖 项目简介

**InduOCRBench** 是一个面向工业级 RAG 系统的 OCR Benchmark，覆盖真实企业场景中常见的 11 类高挑战文档类型。

该 Benchmark 聚焦于传统字符级 OCR 指标与真实下游 RAG 效果之间的鸿沟，从：

- OCR 识别保真度（OCR Fidelity）
- 端到端 RAG 检索与问答效果（RAG Impact）

两个维度系统评估 OCR 的真实鲁棒性。

---

## ✨ 核心特点

- **真实工业场景**
  - 数据来源于 12 个行业、1 万份真实文档。

- **大规模高多样性**
  - 包含 **570** 份 PDF 文档、共 **3,402** 页。
  - 覆盖 **11 类 OCR 挑战场景 + 1 类 Normal 场景**。

- **高质量标注**
  - 使用细粒度 Hybrid Markdown 标注：
    - Markdown
    - HTML 表格
    - LaTeX 公式
    - Style Tags
  - 采用 3-stage human-in-the-loop 质检流程，标注准确率达到 98%。

- **双评测体系**
  - OCR Fidelity（字符 / 结构级评测）
  - RAG Impact（端到端检索与生成评测）

---

## 🔍 关键发现

- 在标准 Benchmark（如 OmniDocBench）上接近满分的模型，在 InduOCRBench 上出现明显性能下降：
  - PP-StructureV3 ↓ 26.4 pts
  - PaddleOCR-VL ↓ 14.7 pts

- **高 OCR 准确率并不意味着高 RAG 效果。**
  - `VisualStyle` 文档 OCR Accuracy 达到 82.9%
  - 但 RAG Accuracy 仅有 52.8%
  - 两者存在 30.1 pts 的巨大差距。

- OCR 引起的信息缺失，是所有 OCR-first RAG 架构中的稳定上游瓶颈。

---

## 📦 Benchmark 包含两个评测任务

### 1. OCR Fidelity Evaluation

基于 Ground-truth Markdown，对 OCR 输出进行字符级与结构级评测。

对应目录：

```text
ocr_data/
```

---

### 2. RAG Impact Evaluation

评估 OCR 质量对端到端：

- Retrieval
- Generation
- QA Accuracy

的影响。

对应目录：

```text
RAG_eval/
```

---

# 📊 数据集统计

| 统计项 | 数值 |
|---|---|
| 文档数量 | 570 |
| 页面数量 | 3,402 |
| 文档类型 | 11 类挑战场景 + 1 类 Normal |
| QA 数量（RAG） | 2071 |
| 标注格式 | Hybrid Markdown |

---

## 11 类 OCR 挑战文档类型

- ComplexBackground
- HighPixel
- UltraLong
- MultiColumn
- UltraWide
- HistoryBooks
- Handwriting
- MultiFont
- VisualStyle
- Watermark
- CrosspageTable

---

# 📂 数据集结构

```text
InduOCRBench/
├── ocr_data/
│   ├── pdf.zip           # 原始 PDF 文档（570份，3402页）
│   ├── md.zip            # 【推荐】OCR 评测 Ground Truth
│   └── md_original.zip   # 保留完整视觉样式信息的高保真标注
│
├── RAG_eval/
│   ├── QA_pairs.jsonl    # RAG 评测 QA 数据
│   └── doc_md/           # QA 对应的 Ground Truth Markdown
│
├── README.md
└── README_zh-CN.md
```

---

## 各类 Markdown 文件说明

### md_original

高保真 Markdown 标注版本，保留：

- 字体
- 颜色
- 对齐方式
- 布局
- 视觉 style tags

适用于：

- 文档重建
- 高保真 OCR
- 文档视觉理解研究

---

### md

移除了视觉样式，仅保留文本内容。

该版本作为：

> OCR Fidelity Evaluation 标准 Ground Truth

用于保证评测公平性。

---

### doc_md

用于 RAG 构建的 Hybrid Markdown。

其中：

- `VisualStyle` 文档保留 style 信息
- 其他类型移除 style 信息

该版本作为：

> RAG Indexing 与 QA Evaluation 的标准 Ground Truth

---

# 🚀 OCR Evaluation

本 Benchmark 使用 **OmniDocBench** 中的 `md2md` 评测方法。

详情参考：

https://github.com/opendatalab/OmniDocBench/tree/main

---

## Evaluation Result

<div align="center">

Table 1. OCR fidelity evaluation on InduOCRBench using md2md metrics

</div>

<table style="width:100%; border-collapse: collapse;">
    <thead>
        <tr>
            <th>Model Type</th>
            <th>Methods</th>
            <th>Size</th>
            <th>Overall&#x2191;</th>
            <th>Text<sup>EDS</sup>&#x2191;</th>
            <th>Formula<sup>CDM</sup>&#x2191;</th>
            <th>Table<sup>TEDS</sup>&#x2191;</th>
            <th>Table<sup>TEDS-S</sup>&#x2191;</th>
            <th>Read Order<sup>EDS</sup>&#x2191;</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td rowspan="9"><strong>Specialized</strong><br><strong>VLMs</strong></td>
            <td>PaddleOCR-VL-1.5</td>
            <td>0.9B</td>
            <td><strong>79.01</strong></td>
            <td><strong>88.33</strong></td>
            <td>75.3</td>
            <td><strong>73.41</strong></td>
            <td><strong>77.27</strong></td>
            <td>85.3</td>
        </tr>
        <tr>
            <td>PaddleOCR-VL</td>
            <td>0.9B</td>
            <td><ins>78.24</ins></td>
            <td><ins>88.1</ins></td>
            <td>74.6</td>
            <td><ins>72.03</ins></td>
            <td>75.87</td>
            <td>85.6</td>
        </tr>
        <tr>
            <td>Logics-Parsing-v2</td>
            <td>4B</td>
            <td>75.71</td>
            <td>84.94</td>
            <td>72.3</td>
            <td>69.90</td>
            <td>76.17</td>
            <td><strong>88.9</strong></td>
        </tr>
        <tr>
            <td>MinerU2.5-Pro</td>
            <td>1.2B</td>
            <td>74.47</td>
            <td>81.63</td>
            <td><ins>75.8</ins></td>
            <td>65.99</td>
            <td>70.46</td>
            <td>79.1</td>
        </tr>
        <tr>
            <td>FireRed-OCR</td>
            <td>2B</td>
            <td>74.09</td>
            <td>87.9</td>
            <td>72.4</td>
            <td>61.98</td>
            <td>66.45</td>
            <td><ins>85.8</ins></td>
        </tr>
        <tr>
            <td>MinerU2.5</td>
            <td>1.2B</td>
            <td>72.50</td>
            <td>81.8</td>
            <td>75.4</td>
            <td>60.31</td>
            <td>63.10</td>
            <td>84.4</td>
        </tr>
        <tr>
            <td>GLM-OCR</td>
            <td>0.9B</td>
            <td>68.64</td>
            <td>63.18</td>
            <td>72.1</td>
            <td>70.64</td>
            <td><ins>76.72</ins></td>
            <td>77.2</td>
        </tr>
        <tr>
            <td>hunyuan-ocr</td>
            <td>0.9B</td>
            <td>68.08</td>
            <td>86.1</td>
            <td>65.6</td>
            <td>52.53</td>
            <td>58.34</td>
            <td>85.7</td>
        </tr>
        <tr>
            <td>deepseek-ocr</td>
            <td>1.2B</td>
            <td>61.46</td>
            <td>75.5</td>
            <td>61.8</td>
            <td>47.07</td>
            <td>49.31</td>
            <td>81.8</td>
        </tr>
        <tr>
            <td rowspan="4"><strong>General</strong><br><strong>VLMs</strong></td>
            <td>Gemini-2.5 Pro</td>
            <td>-</td>
            <td>74.53</td>
            <td>83.1</td>
            <td><strong>77.2</strong></td>
            <td>63.29</td>
            <td>67.28</td>
            <td>81.1</td>
        </tr>
        <tr>
            <td>Qwen3-VL-235B</td>
            <td>235B</td>
            <td>70.91</td>
            <td>83.3</td>
            <td>74.8</td>
            <td>54.63</td>
            <td>59.43</td>
            <td>82.1</td>
        </tr>
        <tr>
            <td>Ovis2.6-30B-A3B</td>
            <td>30B</td>
            <td>59.34</td>
            <td>60.2</td>
            <td>65.8</td>
            <td>52.03</td>
            <td>57.00</td>
            <td>64.4</td>
        </tr>
        <tr>
            <td>GPT-4o</td>
            <td>-</td>
            <td>52.01</td>
            <td>60.8</td>
            <td>58.1</td>
            <td>37.15</td>
            <td>43.83</td>
            <td>70.0</td>
        </tr>
        <tr>
            <td rowspan="2"><strong>Pipeline</strong><br><strong>Tools</strong></td>
            <td>Mineru2-pipeline</td>
            <td>-</td>
            <td>66.54</td>
            <td>80.1</td>
            <td>63.2</td>
            <td>56.32</td>
            <td>62.05</td>
            <td>81.3</td>
        </tr>
        <tr>
            <td>PP-StructureV3</td>
            <td>-</td>
            <td>60.32</td>
            <td>78.2</td>
            <td>53.7</td>
            <td>49.07</td>
            <td>62.06</td>
            <td>79.1</td>
        </tr>
    </tbody>
</table>


---

## Evaluation Setup

为保证评测公平性，请遵循以下设置：

1. **Ground Truth**
   - 使用 `ocr_data/md.zip` 中的 Markdown 文件。

2. **Metric**
   - 使用 `md2md` 评测指标。

> 注意：
> 虽然提供了 `md_original`，
> 但排行榜与标准评测请统一使用 `md/` 目录下的数据。

---

# 📝 使用方式

## 1. 下载并解压数据

```bash
cd ocr_data
unzip pdf.zip
unzip md.zip
```

---

## 2. 运行 OCR 模型

对 `pdf/` 中的文档进行解析，并输出 Markdown 结果。

---

## 3. 执行评测

将模型输出与 `md/` Ground Truth 进行比较。

---

# 🔎 RAG Impact Evaluation

RAG Evaluation 用于评估 OCR 对端到端：

- Retrieval
- Generation
- QA

效果的影响。

相比字符级 OCR 指标，它更关注：

- 结构保留
- 语义保留
- 下游可用性

---

## RAG Evaluation Data

`RAG_eval/` 目录包含：

- `QA_pairs.jsonl`
  - 共 2,071 条 QA 数据

- `doc_md/`
  - 用于 RAG indexing 的 Ground Truth Markdown

---

## QA 数据格式

```json
{
  "doc_type": "cross_page_table",
  "filename": "cross_page_table_1.md",
  "title": "Document title",
  "file_path": "RAG_eval/doc_md/cross_page_table_1.md",
  "question_category": "...",
  "question": "...",
  "answer": "...",
  "evidence": "..."
}
```

---

# ⚙️ RAG Pipeline Setup

我们采用：

[FlashRAG](https://github.com/RUC-NLPIR/FlashRAG)

中的 Naive Pipeline。

配置如下：

| 模块 | 配置 |
|---|---|
| Embedding | BGE-M3 |
| Retrieval | Dense, Flat index, top-100 |
| Reranking | BGE-Rerank-V2-M3, top-10 |
| Generation | ChatGPT-5 |
| Chunking | HTML Tree Structure |
| Evaluation | RAGAS（GPT-OSS-120B Judge） |

---

# 📈 RAG Evaluation Metrics

### Context Recall

评估检索结果是否包含 Ground Truth 证据。

---

### Answer Accuracy

评估最终生成答案的正确性。

---

# 🔥 关键 RAG 发现

| 文档类型 | OCR Accuracy | RAG Accuracy | Gap |
|---|---|---|---|
| VisualStyle | 82.9% | 52.8% | -30.1 pts |
| CrosspageTbl | 40.7% | 63.8% | +23.1 pts |
| UltraWide | 28.1% | 49.1% | low-low |
| MultiFont | 97.2% | 97.5% | ≈0 |

---

**高 OCR Accuracy 并不意味着高 RAG 效果。**

例如：

`VisualStyle` 文档虽然 OCR Accuracy 高达 82.9%，

但由于 OCR 丢失：

- 删除线
- 颜色强调
- 视觉样式语义

最终 RAG Accuracy 仅为 52.8%。

---

# 📄 License

本项目遵循开源协议发布。

请在合法合规范围内使用本数据集。

本数据集仅用于：

- 学术研究
- 非商业研究用途

---

# 🙏 Acknowledgement

- 感谢 [OmniDocBench](https://github.com/opendatalab/OmniDocBench) 提供 OCR 评测代码。
- 感谢 [FlashRAG](https://github.com/RUC-NLPIR/FlashRAG) 提供 RAG Pipeline 框架。
- 感谢 [ragas](https://github.com/vibrantlabsai/ragas) 提供 RAG 评估代码。

---

# 🖊️ Citation

如果你使用了 InduOCRBench，请考虑引用：

```bibtex
@misc{induocrbench,
  title={When Good OCR Is Not Enough: Benchmarking OCR Robustness for Retrieval-Augmented Generation},
  author={Lin Sun and Wangdexian and Jingang Huang and Linglin Zhang and Change Jia and Zhengwei Cheng and Xiangzheng Zhang},
  year={2026},
  eprint={2605.00911},
  archivePrefix={arXiv},
  primaryClass={cs.CV},
  url={https://github.com/Qihoo360/InduOCRBench},
}
```
