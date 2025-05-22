# 🧠 Exploring Qwen LLM Models

<div align="center">

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Python](https://img.shields.io/badge/Python-3.8+-blue.svg)](https://python.org)
[![Jupyter](https://img.shields.io/badge/Jupyter-Notebook-orange.svg)](https://jupyter.org)
[![Hugging Face](https://img.shields.io/badge/🤗-Hugging%20Face-yellow)](https://huggingface.co)

*A comprehensive comparative analysis of Qwen Large Language Models for structured data extraction*

[Features](#-features) • [Quick Start](#-quick-start) • [Results](#-key-findings) • [Documentation](#-documentation)

</div>

---

## 📋 Overview

This repository provides an in-depth comparative exploration of **Qwen Large Language Models** from Alibaba Cloud, specifically focusing on their capabilities for **Named Entity Recognition (NER)** with strict JSON output formatting. The project demonstrates two distinct deployment approaches and analyzes their performance differences.

### 🎯 Primary Objectives
- **Structured Data Extraction**: Evaluate NER capabilities on driver's license text
- **JSON Output Adherence**: Test strict formatting compliance
- **Deployment Comparison**: Compare Hugging Face vs. GGUF quantized models
- **Performance Analysis**: Benchmark inference times and output quality

---

## ✨ Features

<table>
<tr>
<td width="50%">

### 🤗 Hugging Face Integration
- Standard `transformers` library implementation
- Multiple model sizes (0.5B, 1.5B)
- Direct model loading and inference
- Compatible with various hardware setups

</td>
<td width="50%">

### ⚡ GGUF Quantized Models
- Optimized `llama-cpp-python` implementation
- Multiple quantization levels (fp16, q8_0, q4_k_m, q4_0)
- Efficient CPU inference
- Reduced memory footprint

</td>
</tr>
</table>

---

## 📊 Key Findings

### 🔍 Output Quality Comparison

<details>
<summary><strong>🤗 Hugging Face Transformers Results</strong></summary>

**❌ Issues Identified:**
- Failed to produce JSON-only output
- Included conversational text and markdown formatting
- Wrapped JSON in code blocks (```json ```)
- Required post-processing for reliable parsing

**Example Output:**
```markdown
```json
{
    "address": {
        "license": "B1231241",
        "Address": "California",
        "Sex": "Male",
        // ... additional fields with formatting issues
    }
}
```
```

**⏱️ Performance:** ~128 seconds (0.5B-Instruct on CPU)

</details>

<details>
<summary><strong>⚡ GGUF Models Results</strong></summary>

**✅ Superior Performance:**
- Clean, parseable JSON output
- Strict adherence to formatting rules
- No extraneous text or markdown
- Ready for programmatic consumption

**Example Output:**
```json
{
    "address": {
        "license": "B1231241",
        "Address": "2570 24TH STREET ANYTOWN, CA 95818",
        "Sex": "Female",
        "Weight": "125",
        "Height": "5'05\""
    }
}
```

**⏱️ Performance Benchmarks:**

| Model | Quantization | Inference Time | Quality |
|-------|-------------|----------------|---------|
| Qwen2.5-0.5B | q4_k_m | ~15s | ⭐⭐⭐ |
| Qwen2.5-0.5B | fp16 | ~19s | ⭐⭐⭐ |
| Qwen2.5-1.5B | q4_k_m | ~44s | ⭐⭐⭐⭐ |
| Qwen2.5-1.5B | q8_0 | ~49s | ⭐⭐⭐⭐ |
| Qwen2.5-3B | q4_0 | ~91s | ⭐⭐⭐⭐⭐ |
| Qwen2.5-3B | q8_0 | ~131s | ⭐⭐⭐⭐⭐ |

</details>

### 🏆 Winner: GGUF Models with llama-cpp-python
**Superior for tasks requiring strict JSON output and efficient local inference**

---

## 🚀 Quick Start

### 📥 Installation

1. **Clone the repository**
   ```bash
   git clone https://github.com/SakibAhmedShuva/Exploring-Qwen-LLM-Models.git
   cd Exploring-Qwen-LLM-Models
   ```

2. **Install dependencies**

   <table>
   <tr>
   <td width="50%">
   
   **For Hugging Face approach:**
   ```bash
   pip install transformers torch
   ```
   
   </td>
   <td width="50%">
   
   **For GGUF approach:**
   ```bash
   pip install -U llama-cpp-python
   ```
   
   </td>
   </tr>
   </table>

3. **Run the notebooks**
   ```bash
   jupyter notebook
   # Open either Qwen.ipynb or Qwen_gguf.ipynb
   ```

### 🔧 System Requirements

- **Python**: 3.8+
- **Memory**: 8GB+ RAM recommended
- **Storage**: 2-10GB depending on model size
- **Compute**: CPU sufficient (GPU acceleration optional)

---

## 📚 Documentation

### 📖 Notebooks Overview

<table>
<tr>
<th width="50%">📓 Qwen.ipynb</th>
<th width="50%">📗 Qwen_gguf.ipynb</th>
</tr>
<tr>
<td>

**Hugging Face Transformers**
- Standard implementation
- Multiple model variants
- Detailed error analysis
- Performance benchmarking

**Models Tested:**
- `Qwen/Qwen2.5-0.5B-Instruct`
- `Qwen/Qwen2.5-1.5B`

</td>
<td>

**GGUF Quantized Models**
- Optimized inference
- Multiple quantization levels
- Memory-efficient deployment
- Speed vs. quality analysis

**Models Tested:**
- 0.5B, 1.5B, 3B parameter models
- fp16, q8_0, q4_k_m, q4_0 quantizations

</td>
</tr>
</table>

### 🎯 Test Configuration

<details>
<summary><strong>📝 System Prompt</strong></summary>

```python
system_prompt = """
You are a JSON-only response system. Follow these rules absolutely:
1. ONLY output valid, parseable JSON
2. NEVER include text before or after the JSON
3. NEVER include markdown code blocks or formatting
4. NEVER include explanations
5. NEVER extract dates
6. If you can't fulfill a request, return {"error": "error message"}
7. Output should always be a single JSON object

For address requests, use this format:
{
    "address": {
        "license": "B1231241",
        "Address": "X City",
        "Sex": "Male",
        "Weight": "X",
        "Height": "X"
    }
}
"""
```

</details>

<details>
<summary><strong>📄 Test Data</strong></summary>

```python
test_instruction = '''extract NER:
        California
        DRIVER LICENSe
        dl 11234568
        CLASS C
        EXP 08/31/2014
        END NONE
        LNCARDHOLDER FNIMA
        2570 24TH STREET ANYTOWN, CA 95818
        doB 08/31/1977 RSTR NONE
        08311977
        VETERAN
        Cordhslde
        SEX F HGT 5'-05"
        HAIR BRN WGT 125 lb
        EYES BRN
        DD 00/00/0000NNNAN/ANFD/YY
        ISS 08/31/2009
'''
```

</details>

---

## 🎯 Use Cases

<div align="center">

| Use Case | Recommended Approach | Model Size | Quantization |
|----------|---------------------|------------|--------------|
| **🏃‍♂️ Fast Prototyping** | GGUF | 0.5B | q4_k_m |
| **⚖️ Balanced Performance** | GGUF | 1.5B | q8_0 |
| **🎯 High Accuracy** | GGUF | 3B | q8_0 |
| **💾 Memory Constrained** | GGUF | 0.5B | q4_0 |
| **🖥️ Production Deployment** | GGUF | 1.5B-3B | q4_k_m |

</div>

---

## 🤝 Contributing

We welcome contributions! Here are some ways you can help:

- 🐛 **Bug Reports**: Found an issue? Let us know!
- 💡 **Feature Requests**: Have ideas for improvements?
- 📝 **Documentation**: Help improve our docs
- 🧪 **Testing**: Try different models or configurations

---

## 📄 License

This project is licensed under the **MIT License** - see the [LICENSE](LICENSE) file for details.

---

## 👤 Author

<div align="center">

**Sakib Ahmed Shuva**

[![GitHub](https://img.shields.io/badge/GitHub-SakibAhmedShuva-black?style=flat&logo=github)](https://github.com/SakibAhmedShuva)

*Feel free to reach out for questions, suggestions, or collaborations!*

</div>

---

<div align="center">

### ⭐ Star this repository if you found it helpful!

*Made with ❤️ for the AI community*

</div>
