# Crypto-Native AI Security 学术论文编译说明

## 文件说明

- `crypto_native_ai_security.tex` - 主LaTeX论文文件（英文版）
- `crypto_native_ai_security_zh.tex` - 主LaTeX论文文件（中文版）
- `crypto_native_ai_security.bib` - 参考文献文件
- `icml2025.sty` - ICML 2025样式文件（位于ICML2025_Template目录或Docs根目录）
- `icml2025.bst` - 参考文献样式文件

## 编译步骤

### 方法1：使用pdflatex + bibtex（推荐）

```bash
# 第一次编译
pdflatex crypto_native_ai_security.tex

# 生成参考文献
bibtex crypto_native_ai_security

# 再次编译以包含参考文献
pdflatex crypto_native_ai_security.tex

# 最后一次编译以确保所有引用正确
pdflatex crypto_native_ai_security.tex
```

### 方法2：使用latexmk（自动处理）

```bash
latexmk -pdf crypto_native_ai_security.tex
```

## 中文版编译步骤

**重要：** 中文版必须使用XeLaTeX编译，因为需要支持中文字体。

### 方法1：使用xelatex + bibtex（推荐）

```bash
# 第一次编译
xelatex crypto_native_ai_security_zh.tex

# 生成参考文献
bibtex crypto_native_ai_security_zh

# 再次编译以包含参考文献
xelatex crypto_native_ai_security_zh.tex

# 最后一次编译以确保所有引用正确
xelatex crypto_native_ai_security_zh.tex
```

### 方法2：使用latexmk（自动处理）

```bash
latexmk -xelatex crypto_native_ai_security_zh.tex
```

### 中文版注意事项

1. 必须使用XeLaTeX编译器（`xelatex`），不能使用pdfLaTeX
2. 确保系统已安装中文字体（如宋体、黑体等）
3. 如果编译时出现字体问题，可能需要配置xeCJK字体设置

## 注意事项

1. 确保所有样式文件（.sty, .bst）在编译路径中
2. 如果样式文件在ICML2025_Template子目录中，需要复制到Docs目录或调整路径
3. 论文主文限制为8页（不含参考文献和附录）
4. 最终版本可以增加1页到主文

## 论文结构

1. **摘要** - 4-6句，概述主要贡献
2. **引言** - 背景、动机、贡献
3. **后量子密码学基础** - 格密码、可逆水印
4. **可验证隐私计算** - SMPC、ZKML
5. **智能体经济** - 区块链身份、归因体系
6. **硬件级防御** - Intel TDT、TCN
7. **合规工程** - GB 45438-2025、机器遗忘
8. **实验验证** - 性能评估结果
9. **结论** - 总结与未来工作

## 引用格式

论文使用APA格式引用，通过natbib和icml2025.bst样式文件自动生成。

