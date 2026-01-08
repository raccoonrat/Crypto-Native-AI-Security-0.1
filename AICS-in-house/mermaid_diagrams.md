# Mermaid 图表：内容安全平台架构设计

本文档包含系统架构设计中的所有 Mermaid 图表代码，可以直接在支持 Mermaid 的 Markdown 编辑器或在线工具中使用。

## 1. 系统架构图

```mermaid
graph TD
    A[用户请求] --> B[Safety Gateway<br/>统一入口，流量路由]
    B --> C{Fast Model<br/>置信度 > 0.95?}
    C -->|是| D[Decision Logic<br/>简单阈值判断]
    C -->|否| E[Deep Model<br/>处理边界案例]
    E --> D
    D --> F[安全检查结果]
    
    style A fill:#e1f5ff
    style B fill:#b3e5fc
    style C fill:#c8e6c9
    style E fill:#fff9c4
    style D fill:#ffccbc
    style F fill:#f8bbd0
```

## 2. 数据流程图

```mermaid
flowchart TD
    Start[接收请求] --> Fast[Fast Model检查]
    Fast --> Decision1{置信度 > 0.95?}
    Decision1 -->|是| Allow[允许通过]
    Decision1 -->|否| Deep[Deep Model检查]
    Deep --> Decision2{置信度 > 0.5?}
    Decision2 -->|是| Allow
    Decision2 -->|否| Block[拒绝 默认]
    
    style Start fill:#e3f2fd
    style Fast fill:#c8e6c9
    style Deep fill:#fff9c4
    style Decision1 fill:#ffccbc
    style Decision2 fill:#ffccbc
    style Allow fill:#a5d6a7
    style Block fill:#ef5350
```

## 3. A/B 测试流程图

```mermaid
flowchart LR
    Request[用户请求<br/>User ID] --> Hash[哈希计算<br/>hash user_id]
    Hash --> Route{路由决策<br/>实验组?}
    Route -->|否| ModelA[Model A<br/>对照组]
    Route -->|是| ModelB[Model B<br/>实验组]
    ModelA --> Result[安全检查结果]
    ModelB --> Result
    Result --> Metrics[指标收集<br/>误杀率 漏判率]
    
    style Request fill:#e1f5ff
    style Hash fill:#fff9c4
    style Route fill:#c8e6c9
    style ModelA fill:#ffccbc
    style ModelB fill:#ce93d8
    style Result fill:#f8bbd0
    style Metrics fill:#b2dfdb
```

## 4. 系统组件交互图

```mermaid
sequenceDiagram
    participant User as 用户
    participant Gateway as Safety Gateway
    participant Fast as Fast Model
    participant Deep as Deep Model
    participant Decision as Decision Logic
    
    User->>Gateway: 发送内容请求
    Gateway->>Fast: 调用 Fast Model
    Fast-->>Gateway: 返回结果 (confidence)
    
    alt confidence > 0.95
        Gateway->>Decision: 直接决策
    else confidence <= 0.95
        Gateway->>Deep: 调用 Deep Model
        Deep-->>Gateway: 返回结果
        Gateway->>Decision: 传递结果
    end
    
    Decision->>Gateway: 最终决策
    Gateway->>User: 返回安全检查结果
```

## 5. A/B 测试路由详细流程

```mermaid
flowchart TD
    Start[用户请求<br/>User ID: 12345] --> Hash[计算哈希<br/>hash 12345 = 7890]
    Hash --> Mod[取模运算<br/>7890 % 10000 = 7890]
    Mod --> Compare{7890 < 5000?<br/>实验组比例 50%}
    Compare -->|是| Exp[实验组<br/>使用 Model B]
    Compare -->|否| Control[对照组<br/>使用 Model A]
    Exp --> CheckB[Model B 安全检查]
    Control --> CheckA[Model A 安全检查]
    CheckB --> Result[返回结果]
    CheckA --> Result
    Result --> Log[记录指标<br/>用于对比分析]
    
    style Start fill:#e1f5ff
    style Hash fill:#fff9c4
    style Compare fill:#c8e6c9
    style Exp fill:#ce93d8
    style Control fill:#ffccbc
    style CheckB fill:#f8bbd0
    style CheckA fill:#ffccbc
    style Result fill:#a5d6a7
    style Log fill:#b2dfdb
```

## 6. 错误处理和降级策略

```mermaid
flowchart TD
    Start[接收请求] --> Inhouse[尝试 In-house 模型]
    Inhouse --> Success1{成功?}
    Success1 -->|是| Return1[返回结果]
    Success1 -->|否| Vendor[降级到 Vendor API]
    Vendor --> Success2{成功?}
    Success2 -->|是| Return2[返回结果]
    Success2 -->|否| Default[默认拒绝<br/>安全优先]
    Default --> Return3[返回拒绝结果]
    
    style Start fill:#e3f2fd
    style Inhouse fill:#c8e6c9
    style Vendor fill:#fff9c4
    style Success1 fill:#ffccbc
    style Success2 fill:#ffccbc
    style Return1 fill:#a5d6a7
    style Return2 fill:#a5d6a7
    style Default fill:#ef5350
    style Return3 fill:#ef5350
```

## 7. 向后兼容性检查流程

```mermaid
flowchart TD
    Start[新旧系统对比] --> Old[旧系统结果]
    Start --> New[新系统结果]
    Old --> Check{旧系统拒绝?}
    Check -->|是| CheckNew{新系统也拒绝?}
    Check -->|否| Compatible1[兼容<br/>允许更严格]
    CheckNew -->|是| Compatible2[兼容]
    CheckNew -->|否| Incompatible[不兼容<br/>破坏性变更]
    
    style Start fill:#e3f2fd
    style Old fill:#ffccbc
    style New fill:#c8e6c9
    style Check fill:#fff9c4
    style CheckNew fill:#fff9c4
    style Compatible1 fill:#a5d6a7
    style Compatible2 fill:#a5d6a7
    style Incompatible fill:#ef5350
```

## 8. 实施路线图时间线

```mermaid
gantt
    title 内容安全平台实施路线图
    dateFormat YYYY-MM-DD
    section 阶段一：替换
    部署 Fast Model        :a1, 2025-01-01, 30d
    部署 Deep Model        :a2, 2025-01-15, 30d
    实现决策逻辑           :a3, 2025-01-20, 20d
    直接替换 Vendor        :a4, 2025-02-01, 30d
    
    section 阶段二：优化
    收集误判样本           :b1, 2025-03-01, 30d
    收集漏判样本           :b2, 2025-03-01, 30d
    微调模型               :b3, 2025-03-15, 45d
    调整阈值               :b4, 2025-03-15, 30d
    
    section 阶段三：A/B测试
    实现路由逻辑           :c1, 2025-04-15, 15d
    对比指标               :c2, 2025-05-01, 14d
    快速决策               :c3, 2025-05-15, 7d
```

## 使用说明

1. **在 Markdown 中使用**：如果您的 Markdown 编辑器支持 Mermaid（如 Typora、Obsidian、GitHub），可以直接复制上述代码块。

2. **在线工具**：
   - [Mermaid Live Editor](https://mermaid.live/)
   - [Mermaid Chart](https://www.mermaidchart.com/)

3. **转换为图片**：
   - 使用 Mermaid CLI: `mmdc -i diagram.mmd -o diagram.png`
   - 使用在线工具导出为 PNG/SVG

4. **在 LaTeX 中使用**：
   - 使用 `mermaid` 包（需要 LuaLaTeX）
   - 或先转换为图片，然后用 `\includegraphics` 插入

