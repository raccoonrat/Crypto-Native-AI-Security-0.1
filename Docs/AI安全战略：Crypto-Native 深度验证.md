# **Crypto-Native AI Security：面向智能体经济与后量子时代的战略白皮书**

## **执行摘要**

随着2025年的临近，人工智能（AI）与密码学原语的深度融合已不再是理论探索，而是企业构建下一代数字基础设施的必然选择。传统的网络安全边界在生成式AI、自主智能体（Agentic AI）和边缘计算的冲击下日益脆弱。模型本身已成为核心资产，同时也成为了新的攻击面。本报告基于“Crypto-Native AI Security”（加密原生AI安全）战略框架，深度整合了区块链算子、格密码学（Lattice-based Cryptography）、安全多方计算（SMPC）以及硬件级威胁检测技术，旨在为企业CXO提供一份详尽的技术验证与战略实施指南。

本报告的核心发现指出，随着中国国家标准GB 45438-2025的强制执行以及NIST后量子密码标准的落地，企业必须从被动防御转向“原声嵌入”的安全范式。通过ERC-6551标准赋予智能体主权身份、利用格密码实现可逆水印以保护模型产权、以及通过SMPC优化算法突破密态推理的延迟瓶颈，构建一个去中心化、可验证且具备数据主权的AI生态系统。报告详细验证了PrivLLMSwarm在边缘蜂群中的毫秒级推理能力，Intel TDT在端侧NPU上的勒索软件防御效能，以及RouteMark技术在混合专家模型（MoE）中的版权归因机制，为2026年的企业安全战略提供了坚实的技术锚点。

## ---

**1\. 战略背景：信任的数学重构与合规奇点**

在数字化转型的深水区，AI系统的安全性不再仅仅关乎数据的机密性，更关乎智能决策的可解释性、模型资产的所有权以及自主经济行为的责任归属。2025年标志着“合规奇点”的到来，技术演进与监管压力迫使企业重新思考安全架构。

### **1.1 威胁演进：从数据窃取到模型剽窃**

当前的威胁环境已发生了质的改变。攻击者的目标从传统的数据库渗透转向了对高价值AI模型的权重窃取、微调后门植入以及对抗性样本攻击。特别是在混合专家模型（MoE）日益普及的背景下，如何证明一个大型模型中包含了特定企业的专有“专家”模块，成为了知识产权保护的深水区。此外，随着勒索软件进化为利用AI优化加密策略的形态，传统的基于签名的防御手段已完全失效，必须依赖基于行为遥测的硬件级防御 1。

### **1.2 监管风暴：GB 45438-2025 与机器遗忘权**

2025年9月1日正式实施的中国国家标准《网络安全技术 人工智能生成内容标识方法》（GB 45438-2025），对全球企业在华业务提出了严苛的合规要求。该标准强制要求在AI生成内容中嵌入显式（可见）和隐式（数字水印/元数据）标识 4。与此同时，GDPR框架下的“被遗忘权”延伸至AI领域，催生了“机器遗忘”（Machine Unlearning）的技术需求，要求企业能够从已训练的模型中数学性地剔除特定数据的影响，而不破坏模型的整体性能 5。

### **1.3 Crypto-Native 范式转移**

Crypto-Native AI Security 并不是简单的“区块链+AI”，而是指将密码学原理作为AI系统的“物理法则”。

* **身份主权化**：智能体不再是隶属于服务器的进程，而是拥有链上钱包（ERC-6551）和声誉凭证（ERC-8004）的独立经济实体 7。  
* **隐私计算化**：数据不再明文流转，而是在SMPC或同态加密的密态下进行计算，实现“可用不可见” 9。  
* **防御物理化**：利用CPU/NPU的底层遥测数据（Telemetry），通过时序卷积网络（TCN）实时识别异常指令流 11。

## ---

**2\. 后量子时代的数学护盾：格密码与全同态加密**

随着量子计算能力的逼近，基于整数分解和离散对数问题的传统公钥密码体系（RSA, ECC）面临崩溃风险。格密码（Lattice-based Cryptography）因其抗量子特性及独特的代数结构，成为了后量子密码（PQC）时代的基石，并为AI模型保护提供了新的技术路径。

### **2.1 NIST 标准化与格密码的工程落地**

美国国家标准与技术研究院（NIST）在2024-2025年间完成了后量子密码算法的标准化工作，确立了Kyber（ML-KEM）作为密钥封装机制标准，Dilithium（ML-DSA）作为数字签名标准 13。这些算法基于格上的容错学习问题（LWE），不仅具有理论上的抗量子安全性，在工程实现上也取得了突破。

#### **2.1.1 AVX-512 指令集加速验证**

针对企业级AI系统高并发的特点，2025年的技术验证显示，利用AVX-512向量指令集对Dilithium算法进行深度优化，可显著降低计算开销。实验数据显示，相比于AVX2指令集，基于AVX-512的Dilithium实现展现了惊人的性能提升。这种性能飞跃使得在每一次AI推理请求中嵌入抗量子签名成为可能，确保了指令的不可篡改性和来源可信 14。

| 操作类型 | 性能提升倍数 (AVX-512 vs AVX2) | 验证来源 |
| :---- | :---- | :---- |
| **密钥生成 (KeyGen)** | 2.25x | 14 |
| **签名生成 (Signing)** | 2.13x | 14 |
| **签名验证 (Verification)** | 2.36x | 14 |

### **2.2 深度技术验证：基于格的可逆水印技术**

除了加密通信，格密码的几何特性在AI模型版权保护中展现了独特的价值。传统的静态水印技术往往会永久性地修改模型权重，导致模型推理精度的不可逆下降。针对高价值的基础大模型，基于格的\*\*可逆数据隐藏（Reversible Data Hiding, RDH）\*\*技术应运而生。

#### **2.2.1 可逆量化索引调制（R-QIM）**

**Reversible Quantization Index Modulation (R-QIM)** 是一种针对浮点型深度神经网络（DNN）权重的创新水印方案。

* **技术原理**：该技术将模型权重视为连续空间中的点，利用格量化器将其映射到可数的格点集上。通过一种“中间相遇”（Meet-in-the-Middle）的嵌入策略，发送方在量化后的宿主信号中加入经过缩放的量化误差 15。  
* **战略价值**：与传统方法不同，R-QIM 允许验证者在提取水印后，完全恢复原始的模型权重。这意味着企业可以在分发模型时嵌入水印以追踪泄露源，而在进行高精度科学计算或医疗诊断等对误差零容忍的场景下，可以还原出无损的原始模型。这种“按需还原”的能力解决了安全与性能之间的二律背反 15。

#### **2.2.2 音频生成的格基嵌入（MME）**

在生成式音频领域，为了应对GB 45438-2025等法规对合成语音标识的要求，**Meet-in-the-Middle Embedding (MME)** 方案被证明具有极高的鲁棒性。

* **技术验证**：MME通过在音频的DCT（离散余弦变换）系数中嵌入基于格的量化误差，能够在保证听觉不可感知性的同时，抵抗各种去同步攻击（Desynchronization Attacks）。实验表明，该方案在多种攻击模式下仍能保持平均25dB以上的信噪比（SNR）和极低的误码率（BER），是实现合规隐式水印的理想技术栈 17。

## ---

**3\. 可验证的隐私计算：SMPC与ZKML的工程化突破**

数据孤岛与隐私泄露是制约企业间AI协作的最大障碍。安全多方计算（SMPC）和零知识机器学习（ZKML）虽然理论完备，但长期受困于计算延迟和通信开销。2025年的技术突破主要集中在通过算法优化和专用编译器来消除这些工程瓶颈。

### **3.1 突破密态推理的延迟瓶颈：PrivLLMSwarm 与 MPCache**

SMPC 允许参与方在不泄露各自输入的前提下联合计算函数，但Transformer架构中的非线性激活函数（如GELU、Softmax）通常需要大量的通信轮次。

#### **3.1.1 PrivLLMSwarm：边缘侧的实时协同**

针对UAV蜂群等边缘计算场景，**PrivLLMSwarm** 框架通过引入MPC友好的多项式近似算法，成功将SMPC应用于即时推理。

* **技术细节**：该框架使用分段GELU和多项式Softmax替代标准函数，大幅减少了秘密分享（Secret Sharing）过程中的通信交互。  
* **性能基准**：在4架UAV组成的SMPC网络中，该系统实现了单张图像推理延迟约 **417.69毫秒**，文本指令处理延迟仅为 **15.42毫秒** 10。这一毫秒级的响应速度证明了SMPC已具备支撑实时战术边缘计算的能力。

#### **3.1.2 MPCache：解决长上下文的KV缓存难题**

在大语言模型（LLM）推理中，Key-Value (KV) Cache 的增长会导致密态计算下的通信量呈线性甚至指数级爆炸。**MPCache** 框架的提出是2025年的一项关键创新。

* **机制**：MPCache 结合了静态驱逐（Static Eviction）和动态选择（Dynamic Selection）策略。它利用“看一次”（Look-once）算法在预填充阶段丢弃不重要的KV对，并在注意力计算时仅激活相关的KV子集。  
* **效能验证**：实验数据显示，MPCache 在不同序列长度下，相比基线方案实现了 **1.8倍至2.01倍** 的解码延迟降低，以及 **3.39倍至8.37倍** 的通信量削减 18。这直接降低了企业部署私有化LLM推理的带宽成本。

### **3.2 ZKML奇点：从理论到生产力**

零知识证明（ZKP）允许一方证明其不仅拥有数据，而且正确执行了特定的AI模型推理，而无需披露模型权重。

* **DeepProve-1 与 ZKML Singularity**：2025年底被称为“ZKML奇点”，标志性事件是Lagrange Labs发布的 **DeepProve-1**。这是首个能为GPT-2级别模型生成全推理加密证明的生产级环境。  
* **核心优化**：DeepProve-1 通过“可证明Softmax”（Provable Softmax）优化了浮点精度敏感性，并利用共享查找表（Shared Lookup Tables）处理量化，从而在不导致电路约束爆炸的前提下实现了对Transformer架构的完全覆盖 21。  
* **ZKTorch**：为了降低开发门槛，开源工具 **ZKTorch** 将PyTorch模型编译为由61个基础加密块组成的有向无环图（DAG），实现了权重的隐私保护推理。这使得AI服务商可以在保护模型IP的同时，向用户提供算力可信证明 22。

## ---

**4\. 智能体经济：基于区块链的身份与归因体系**

随着AI从单纯的“工具”进化为具备自主规划能力的“智能体”（Agents），它们需要独立的身份、资产账户以及与其他智能体进行无信任协作的能力。

### **4.1 ERC-6551：智能体的链上人格与钱包**

**ERC-6551（Token Bound Accounts）** 标准是构建Crypto-Native Agent的核心基础设施。它允许每一个NFT（非同质化代币）直接拥有一个智能合约账户。

* **应用场景**：在Virtuals Protocol等生态中，每个AI智能体被铸造为一个NFT，该NFT自动关联一个ERC-6551钱包。这意味着智能体可以像人类一样持有资产（加密货币、API密钥、数据所有权凭证），并通过智能合约自主支付算力费用或收取服务费 7。  
* **经济闭环**：这种架构解决了“人-机代理”问题。智能体的收入流直接归属于其链上账户，可以根据预设的Tokenomics（代币经济学）自动分发给开发者、算力提供商和数据贡献者，形成透明的价值流转闭环。

### **4.2 ERC-8004：无信任的智能体发现机制**

在一个开放的智能体市场中，如何验证一个陌生智能体的能力？**ERC-8004** 标准提供了一套去中心化的注册与验证协议。

* **机制**：该标准引入了“无信任代理”（Trustless Agents）的概念，通过链上注册表记录智能体的身份、声誉和验证历史。智能体可以通过提交ZKP来证明其完成了特定任务（如代码审计、数据分析），从而积累不可篡改的声誉分 8。  
* **战略意义**：这为机器对机器（M2M）的自动化雇佣奠定了基础，使得企业可以动态组建由第三方智能体构成的虚拟劳动力团队，而无需预先签订繁琐的法律合同。

### **4.3 RouteMark：混合专家模型（MoE）的IP指纹**

在模型融合（Model Merging）日益流行的背景下，一个大型MoE模型可能由来自不同开发者的多个微调模型（Expert）组合而成。如何界定各方的知识产权（IP）贡献？

* **技术挑战**：传统的权重指纹在模型融合后往往失效，因为专家模块的参数被稀疏化或重组。  
* **RouteMark 解决方案**：2025年提出的 **RouteMark** 框架创新性地利用“路由行为”作为指纹。  
  * **路由分数指纹（RSF）**：量化专家在特定输入下的激活强度。  
  * **路由偏好指纹（RPF）**：表征优先激活该专家的输入分布特征。  
* **验证结果**：实验表明，即使经过微调、剪枝或专家置换，RouteMark 仍能准确识别出MoE模型中是否复用了特定的专家模块，验证成功率在MNIST和ImageNet数据集上均接近100% 24。这为开源模型社区的贡献归因和商业化授权提供了技术依据。

## ---

**5\. 硬件级防御：端侧算力的最后一道防线**

在软件层面之外，Crypto-Native AI Security 强调利用底层硬件的不可篡改性来构建防御纵深。Intel等芯片厂商在2025年推出的技术，将威胁检测下沉到了硅片层。

### **5.1 Intel TDT 与 NPU 卸载：物理层的零信任**

Intel **Threat Detection Technology (TDT)** 利用CPU的底层遥测数据（Telemetry）来识别恶意行为，其核心优势在于能够绕过软件层的混淆和反调试技术。

* **PMU与LBR的应用**：TDT 通过监控性能监控单元（PMU）和最后分支记录（LBR），分析程序的微架构行为（如缓存未命中率、分支预测失败率）。勒索软件在进行大规模文件加密时，会表现出极高熵值的算术运算和特定的内存读写模式，这些特征在硬件层面是无法伪装的 3。  
* **NPU 算力卸载**：随着AI PC的普及，2025年的安全软件（如CrowdStrike, Microsoft Defender）开始将繁重的内存扫描和行为分析推理任务从CPU/GPU卸载到 **NPU（神经网络处理单元）** 上。这不仅降低了安全软件对用户体验的影响，还实现了“Always-on”的实时监控 11。

### **5.2 时序卷积网络（TCN）在遥测分析中的应用**

为了处理高频、嘈杂的硬件遥测数据，**时序卷积网络（TCN）** 被证明比传统的RNN/LSTM更具优势。

* **算法优势**：TCN 具有并行计算能力和灵活的感受野，能够更高效地捕捉遥测数据中的长期依赖关系。  
* **检测效能**：在针对勒索软件和DDoS攻击的检测实验中，基于TCN的模型在处理硬件性能计数器（HPC）数据时，达到了 **99.9%** 的分类准确率 28。特别是 **HiSeq-TCN** 架构，通过将高维特征向量转换为伪时间序列，极大地提升了对未知威胁（Zero-day）的检出率 12。

## ---

**6\. 合规工程：数据主权与全生命周期管理**

技术必须服务于合规。面对日益复杂的全球法律环境，企业需要将合规要求转化为具体的工程指标。

### **6.1 GB 45438-2025 标识合规实战**

中国 GB 45438-2025 标准的实施，要求企业建立双层水印机制：

1. **显式标识**：在用户交互界面和生成内容中，必须包含肉眼可见或可听的提示（如“AI生成”字样），且需覆盖图像面积的特定比例或视频的首尾帧 30。  
2. **隐式标识**：必须在文件元数据中嵌入服务提供者的名称、内容ID等信息，并鼓励使用抗篡改的数字水印技术 4。基于前文所述的 **格密码MME技术**，企业可以实现高鲁棒性的隐式水印，确保在经过压缩、转码后仍可溯源，满足监管对“可追溯性”的要求。

### **6.2 机器遗忘（Machine Unlearning）的工程挑战**

GDPR 等法规赋予用户的“删除权”要求模型能够“遗忘”特定数据。

* **精确遗忘（SISA）**：为了满足最严格的合规要求，**SISA (Sharded, Isolated, Sliced, and Aggregated)** 架构将训练数据分片。当需要遗忘某条数据时，仅需重新训练该数据所在的分片模型。这种方法虽然牺牲了部分存储效率，但提供了数学层面上的“精确遗忘”保证，避免了近似算法可能带来的隐私残留风险 31。  
* **去中心化遗忘（HDUS）**：针对联邦学习场景，**HDUS** 框架利用蒸馏的种子模型构建可擦除的集成。这使得在分布式网络中，单个客户端的贡献可以被快速剔除，而无需全局重训，极大地降低了合规成本 31。

## ---

**7\. 战略路线图与建议**

基于上述技术验证与市场分析，我们为企业CXO提出以下实施路线图：

### **7.1 近期行动（6-12个月）**

* **构建双层水印流水线**：立即升级内容生成系统，集成符合 GB 45438-2025 标准的显式标识生成器和基于格密码的 MME 隐式水印模块。  
* **部署硬件级遥测防御**：采购及更新支持 Intel vPro 及 NPU 的终端设备，并配置 EDR 策略以启用 TDT 硬件辅助检测功能，防范新型勒索软件。  
* **PQC 迁移准备**：在密钥管理系统（KMS）中引入 Kyber/Dilithium 算法库，启动混合加密模式的试点。

### **7.2 中期规划（1-2年）**

* **私有化推理去中心化**：在涉及敏感数据的业务单元（如财务、研发），试点部署基于 **MPCache** 和 **PrivLLMSwarm** 的 SMPC 推理集群，打破数据孤岛。  
* **智能体身份标准化**：为企业内部的 AI Agent 颁发 ERC-6551 链上身份，建立内部的“智能体注册表”（参考 ERC-8004），实现对 AI 资产的精细化审计与权限控制。

### **7.3 长期愿景（3年以上）**

* **建立“Crypto-Native”企业架构**：最终实现从“信任人”到“信任代码”的转变。所有的模型调用均附带 ZKML 算力证明，所有的数据流转均在密态下进行，所有的 AI 资产均确权于区块链。

---

**结论**：Crypto-Native AI Security 不仅仅是防御技术的堆砌，它是对 AI 生产关系的重塑。通过格密码保护资产、SMPC 释放数据价值、区块链确立智能体主权、硬件遥测兜底物理安全，企业将构建起坚不可摧的数字护城河，在 Agentic AI 的浪潮中掌握主动权。

#### **Works cited**

1. A Novel Hybrid Deep Learning Model for Attack Detection in IoT Environment: Convolutional Neural Network with Transformer Approach \- JoVE, accessed December 22, 2025, [https://www.jove.com/ru/v/68750/a-novel-hybrid-deep-learning-model-for-attack-detection-iot](https://www.jove.com/ru/v/68750/a-novel-hybrid-deep-learning-model-for-attack-detection-iot)  
2. Temporal Analysis Framework for Intrusion Detection Systems: A Novel Taxonomy for Time-Aware Cybersecurity \- arXiv, accessed December 22, 2025, [https://arxiv.org/pdf/2511.03799](https://arxiv.org/pdf/2511.03799)  
3. Detecting Process Hijacking and Software Supply Chain Attacks Using Intel® Threat Detection Technology, accessed December 22, 2025, [https://www.intel.la/content/dam/www/central-libraries/us/en/documents/white-paper-inteltdt-abd.pdf](https://www.intel.la/content/dam/www/central-libraries/us/en/documents/white-paper-inteltdt-abd.pdf)  
4. China Releases New Labeling Requirements for AI-Generated ..., accessed December 22, 2025, [https://www.insideprivacy.com/international/china/china-releases-new-labeling-requirements-for-ai-generated-content/](https://www.insideprivacy.com/international/china/china-releases-new-labeling-requirements-for-ai-generated-content/)  
5. Machine Unlearning: Solutions and Challenges \- arXiv, accessed December 22, 2025, [https://arxiv.org/html/2308.07061v3](https://arxiv.org/html/2308.07061v3)  
6. Open Problems in Machine Unlearning for AI Safety, accessed December 22, 2025, [https://oms-www.files.svdcdn.com/production/downloads/academic/Open\_Problems\_in\_Machine\_Unlearning\_for\_AI\_Safety\_-7.pdf?dm=1737376521](https://oms-www.files.svdcdn.com/production/downloads/academic/Open_Problems_in_Machine_Unlearning_for_AI_Safety_-7.pdf?dm=1737376521)  
7. Virtuals Protocol: A Decentralized Protocol Empowering Co-creation and On-chain Commerce for AI Agents | Virtuals Protocol Whitepaper \- Bitget, accessed December 22, 2025, [https://www.bitget.com/price/virtuals-protocol/whitepaper](https://www.bitget.com/price/virtuals-protocol/whitepaper)  
8. ERC-8004 and the Ethereum AI Agent Economy: Technical, Economic, and Policy Analysis, accessed December 22, 2025, [https://medium.com/@gwrx2005/erc-8004-and-the-ethereum-ai-agent-economy-technical-economic-and-policy-analysis-3134290b24d1](https://medium.com/@gwrx2005/erc-8004-and-the-ethereum-ai-agent-economy-technical-economic-and-policy-analysis-3134290b24d1)  
9. Secure multi-party computation \- Wikipedia, accessed December 22, 2025, [https://en.wikipedia.org/wiki/Secure\_multi-party\_computation](https://en.wikipedia.org/wiki/Secure_multi-party_computation)  
10. PrivLLMSwarm: Secure LLM Coordination \- Emergent Mind, accessed December 22, 2025, [https://www.emergentmind.com/topics/privllmswarm](https://www.emergentmind.com/topics/privllmswarm)  
11. Today's standard for business PCs \- Intel, accessed December 22, 2025, [https://www.intel.com/content/dam/www/central-libraries/us/en/documents/2025-09/todays-standard-for-business-pcs-ebook.pdf](https://www.intel.com/content/dam/www/central-libraries/us/en/documents/2025-09/todays-standard-for-business-pcs-ebook.pdf)  
12. HiSeq-TCN: High-Dimensional Feature Sequence Modeling and Few-Shot Reinforcement Learning for Intrusion Detection \- MDPI, accessed December 22, 2025, [https://www.mdpi.com/2079-9292/14/21/4168](https://www.mdpi.com/2079-9292/14/21/4168)  
13. Security in Post-Quantum Era: A Comprehensive Survey on Lattice-Based Algorithms \- okayama university scientific achievement repository, accessed December 22, 2025, [https://ousar.lib.okayama-u.ac.jp/files/public/6/69306/20250904142800798556/fulltext.pdf](https://ousar.lib.okayama-u.ac.jp/files/public/6/69306/20250904142800798556/fulltext.pdf)  
14. Faster Implementation of Ideal Lattice-Based Cryptography Using AVX512 | Request PDF, accessed December 22, 2025, [https://www.researchgate.net/publication/372384509\_Faster\_Implementation\_of\_Ideal\_Lattice-based\_Cryptography\_Using\_AVX512](https://www.researchgate.net/publication/372384509_Faster_Implementation_of_Ideal_Lattice-based_Cryptography_Using_AVX512)  
15. (PDF) Reversible Deep Neural Network Watermarking:Matching the Floating-point Weights, accessed December 22, 2025, [https://www.researchgate.net/publication/371136451\_Reversible\_Deep\_Neural\_Network\_WatermarkingMatching\_the\_Floating-point\_Weights](https://www.researchgate.net/publication/371136451_Reversible_Deep_Neural_Network_WatermarkingMatching_the_Floating-point_Weights)  
16. Reversible Quantization Index Modulation for Static Deep Neural Network Watermarking \- arXiv, accessed December 22, 2025, [https://arxiv.org/pdf/2305.17879](https://arxiv.org/pdf/2305.17879)  
17. A Lattice-Based Embedding Method for Reversible Audio Watermarking \- ResearchGate, accessed December 22, 2025, [https://www.researchgate.net/publication/373889147\_A\_Lattice-Based\_Embedding\_Method\_for\_Reversible\_Audio\_Watermarking](https://www.researchgate.net/publication/373889147_A_Lattice-Based_Embedding_Method_for_Reversible_Audio_Watermarking)  
18. \[2501.06807\] MPCache: MPC-Friendly KV Cache Eviction for Efficient Private LLM Inference, accessed December 22, 2025, [https://arxiv.org/abs/2501.06807](https://arxiv.org/abs/2501.06807)  
19. MPCache: MPC-Friendly KV Cache Eviction for Efficient Private LLM Inference, accessed December 22, 2025, [https://openreview.net/forum?id=kd6hcHUl9C\&referrer=%5Bthe%20profile%20of%20Lei%20Wang%5D(%2Fprofile%3Fid%3D\~Lei\_Wang30)](https://openreview.net/forum?id=kd6hcHUl9C&referrer=%5Bthe+profile+of+Lei+Wang%5D\(/profile?id%3D~Lei_Wang30\))  
20. MPCache: MPC-Friendly KV Cache Eviction For Efficient Private LLM Inference \- arXiv, accessed December 22, 2025, [https://arxiv.org/html/2501.06807v2](https://arxiv.org/html/2501.06807v2)  
21. The zkML Singularity: A Comprehensive Analysis of the 2025 ..., accessed December 22, 2025, [https://academy.extropy.io/pages/articles/zkml-singularity.html](https://academy.extropy.io/pages/articles/zkml-singularity.html)  
22. ZKTorch: Efficient ML Zero-Knowledge Proofs \- Emergent Mind, accessed December 22, 2025, [https://www.emergentmind.com/topics/zktorch](https://www.emergentmind.com/topics/zktorch)  
23. Virtual Protocol, create co-ownership AI agents | AccessDenied, accessed December 22, 2025, [https://rya-sge.github.io/access-denied/2024/12/05/virtual-protocol-architecture/](https://rya-sge.github.io/access-denied/2024/12/05/virtual-protocol-architecture/)  
24. \[2508.01784\] RouteMark: A Fingerprint for Intellectual Property Attribution in Routing-based Model Merging \- arXiv, accessed December 22, 2025, [https://www.arxiv.org/abs/2508.01784](https://www.arxiv.org/abs/2508.01784)  
25. RouteMark: A Fingerprint for Intellectual Property Attribution in Routing-based Model Merging \- ChatPaper, accessed December 22, 2025, [https://chatpaper.com/paper/172778](https://chatpaper.com/paper/172778)  
26. Intel® Threat Detection Technology (Intel® TDT), accessed December 22, 2025, [https://www.intel.com/content/www/us/en/architecture-and-technology/vpro/vpro-security/threat-detection-technology.html](https://www.intel.com/content/www/us/en/architecture-and-technology/vpro/vpro-security/threat-detection-technology.html)  
27. Hardware acceleration and Microsoft Defender Antivirus. \- Microsoft Defender for Endpoint, accessed December 22, 2025, [https://learn.microsoft.com/en-us/defender-endpoint/hardware-acceleration-and-mdav](https://learn.microsoft.com/en-us/defender-endpoint/hardware-acceleration-and-mdav)  
28. TCN-Based DDoS Detection and Mitigation in 5G Healthcare-IoT: A Frequency Monitoring and Dynamic Threshold Approach \- IEEE Xplore, accessed December 22, 2025, [https://ieeexplore.ieee.org/iel8/6287639/10820123/10845749.pdf](https://ieeexplore.ieee.org/iel8/6287639/10820123/10845749.pdf)  
29. An intelligent ransomware attack detection and classification using dual vision transformer with Mantis Search Split Attention Network | Request PDF \- ResearchGate, accessed December 22, 2025, [https://www.researchgate.net/publication/384512541\_An\_intelligent\_ransomware\_attack\_detection\_and\_classification\_using\_dual\_vision\_transformer\_with\_Mantis\_Search\_Split\_Attention\_Network](https://www.researchgate.net/publication/384512541_An_intelligent_ransomware_attack_detection_and_classification_using_dual_vision_transformer_with_Mantis_Search_Split_Attention_Network)  
30. China's AI Content Labeling Law Enforced September 2025 \- ASO World, accessed December 22, 2025, [https://marketingtrending.asoworld.com/en/news/china-enforces-new-ai-content-labeling-law-in-september-2025/](https://marketingtrending.asoworld.com/en/news/china-enforces-new-ai-content-labeling-law-in-september-2025/)  
31. Heterogeneous decentralised machine unlearning with seed model distillation \- Griffith Research Online, accessed December 22, 2025, [https://research-repository.griffith.edu.au/server/api/core/bitstreams/a931e5ba-a878-4f2b-a77b-77579ab5c342/content](https://research-repository.griffith.edu.au/server/api/core/bitstreams/a931e5ba-a878-4f2b-a77b-77579ab5c342/content)