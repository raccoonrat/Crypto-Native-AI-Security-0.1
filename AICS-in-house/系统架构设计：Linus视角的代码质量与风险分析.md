# **系统架构设计：Linus视角的代码质量与风险分析**

**作者视角：** Linus Torvalds  
**日期：** 2025年1月  
**目标：** 构建支持 A/B Test 的 In-house 内容安全平台

---

## **执行摘要：我的第一反应**

看完这两个文档，我的第一反应是：**"这太复杂了，你们在解决不存在的问题。"**

让我直接说重点：

1. **L0-L3 四层架构？** 这是典型的"过度工程化"。如果第一层（Regex/Bloom Filter）能拦截 90% 的垃圾，那就让它拦截 90%。不要为了"理论完美"而增加不必要的复杂性。

2. **策略编排引擎 + OPA + DSL？** 如果你们的策略需要 DSL 才能表达，说明策略本身就有问题。好的代码应该让特殊情况消失，而不是用复杂的规则引擎来处理它们。

3. **Shadow Mode → Canary → A/B Test 三阶段？** 这是对的，但实现方式可以更简单。不要为了"科学"而科学。

**我的核心建议：** 先做一个能工作的简单版本，然后根据实际需求迭代。不要一开始就设计一个"完美"的系统。

---

## **1. 架构风险分析：我的"好品味"测试**

### **1.1 过度复杂：四层漏斗架构的问题**

**现有设计：**
```
L0: Regex/Bloom Filter (<1ms)
L1: FastText/DistilBERT (<20ms)  
L2: BERT-Large/ViT (<200ms)
L3: LLM/Vendor API (<2s)
```

**我的问题：**

1. **为什么需要四层？** 如果 L0 能拦截 90%，L1 能拦截 95%，那么 L2 和 L3 处理的是 5% 的流量。这 5% 值得增加两倍的复杂度吗？

2. **边界情况爆炸：** 每一层都需要判断"是否确定"，这意味着：
   - L0 不确定 → 调用 L1
   - L1 不确定 → 调用 L2  
   - L2 不确定 → 调用 L3
   
   这是典型的"特殊情况处理"，而不是"好品味"。

**我的建议：**

```c
// 好品味：让特殊情况消失
struct safety_result {
    bool blocked;
    float confidence;
    enum { FAST, MEDIUM, SLOW } path;
};

// 不要这样：
if (regex_match()) return BLOCK;
if (!regex_match() && distilbert_confidence > 0.9) return BLOCK;
if (distilbert_confidence < 0.5 && bert_confidence > 0.8) return BLOCK;
// ... 更多 if

// 应该这样：
struct safety_result check_safety(const char *text) {
    // 统一接口，内部决定路径
    return safety_check_unified(text);
}
```

**核心原则：** 如果用户不需要知道是 L0 还是 L3 拦截的，那就不要让这个区别暴露在接口中。

---

### **1.2 策略引擎：DSL 是代码腐烂的信号**

**现有设计：**
- OPA (Open Policy Agent)
- Rego DSL 配置
- 动态策略下发

**我的问题：**

1. **为什么需要 DSL？** 如果策略可以用 Rego 表达，为什么不能用代码表达？DSL 只是把问题推迟了，并没有解决它。

2. **"动态策略"是伪需求：** 如果真的需要"小时级生效"，说明策略本身就不稳定。不稳定的策略应该用代码版本控制，而不是运行时配置。

**我的建议：**

```c
// 不要 DSL，直接用代码
struct safety_policy {
    float block_threshold;
    float vip_threshold;
    bool strict_mode;
};

// 策略变更 = 代码变更 = Git 提交 = 可追溯
// 不要试图在运行时"热更新"策略，这是危险的
```

**核心原则：** 如果策略需要"动态更新"，说明策略设计有问题。好的策略应该是稳定的、可测试的、可版本控制的。

---

### **1.3 A/B Test：不要为了测试而测试**

**现有设计：**
- Shadow Mode (100% 流量复制)
- Canary Release (1-5% 流量)
- User Bucket A/B (50/50 分流)

**我的问题：**

1. **Shadow Mode 的成本：** 复制 100% 流量意味着双倍的计算成本。如果只是为了"对比"，为什么不直接用历史数据？

2. **统计显著性陷阱：** 如果 A/B Test 需要运行几周才能得出"统计显著"的结论，那这个结论可能已经过时了。

**我的建议：**

```c
// 简单版本：直接对比
struct ab_test_config {
    float experiment_ratio;  // 0.0 = 全量旧版, 1.0 = 全量新版
    uint64_t user_id_hash;
};

bool should_use_experiment(const struct ab_test_config *cfg, uint64_t user_id) {
    // 基于用户ID哈希，确保同一用户始终走同一路径
    // 不要随机，要确定性
    return (hash64(user_id) % 10000) < (cfg->experiment_ratio * 10000);
}
```

**核心原则：** A/B Test 的目的是验证假设，不是展示"科学方法"。如果假设不明确，就不要做 A/B Test。

---

## **2. 简化架构设计：我的"实用主义"版本**

### **2.1 核心组件：三个，不多不少**

```
┌─────────────────┐
│  Safety Gateway │  ← 统一入口，流量路由
└────────┬────────┘
         │
    ┌────┴────┐
    │         │
┌───▼───┐ ┌──▼────┐
│ Fast  │ │ Deep  │  ← 两个模型，不是四个
│ Model │ │ Model │
└───┬───┘ └──┬────┘
    │        │
    └───┬────┘
        │
┌───────▼────────┐
│ Decision Logic │  ← 简单决策，不是 DSL
└────────────────┘
```

**设计原则：**

1. **Fast Model：** 处理 90% 的明显案例（违规或正常）
   - 如果 confidence > 0.95 → 直接决策，不调用 Deep Model
   - 如果 confidence < 0.95 → 调用 Deep Model

2. **Deep Model：** 处理 10% 的边界案例
   - 如果 Deep Model 也不确定 → 默认拒绝（安全优先）

3. **Decision Logic：** 简单的阈值判断，不是规则引擎
   - 代码实现，不是配置
   - 版本控制，不是热更新

---

### **2.2 数据流：消除边界情况**

**现有设计的边界情况：**
- "如果 L1 不确定，调用 L2"
- "如果 L2 不确定，调用 L3"
- "如果 L3 超时，降级到 Vendor"

**我的设计：让不确定性成为正常情况**

```c
struct safety_check {
    enum result_type {
        SAFE,           // 确定安全
        UNSAFE,         // 确定不安全
        UNCERTAIN       // 不确定（这是正常情况，不是异常）
    } result;
    float confidence;
    uint64_t model_version;
};

// 统一接口，内部处理所有路径
struct safety_check check_content(const char *text, uint64_t user_id) {
    // 先试 Fast Model
    struct safety_check fast = fast_model_check(text);
    
    if (fast.result != UNCERTAIN) {
        return fast;  // 确定结果，直接返回
    }
    
    // 不确定，调用 Deep Model
    struct safety_check deep = deep_model_check(text);
    return deep;  // Deep Model 的结果就是最终结果
    // 如果 Deep Model 也不确定，那就返回 UNCERTAIN
    // 让调用方决定如何处理（通常是拒绝）
}
```

**关键洞察：** "不确定"不是异常，是正常情况。不要用复杂的级联来处理它。

---

### **2.3 A/B Test：简单到极致**

**我的设计：**

```c
struct experiment {
    uint64_t experiment_id;
    float ratio;              // 实验组比例 (0.0 - 1.0)
    uint64_t model_version_a; // 对照组模型版本
    uint64_t model_version_b; // 实验组模型版本
    time_t start_time;
    time_t end_time;
};

// 基于用户ID的确定性路由
bool is_in_experiment(const struct experiment *exp, uint64_t user_id) {
    if (time(NULL) < exp->start_time || time(NULL) > exp->end_time) {
        return false;  // 实验未开始或已结束
    }
    
    // 确定性哈希，确保同一用户始终在同一组
    uint32_t hash = hash32(user_id ^ exp->experiment_id);
    return (hash % 10000) < (exp->ratio * 10000);
}

// 使用
struct safety_check result;
if (is_in_experiment(&current_experiment, user_id)) {
    result = check_content_with_model(text, current_experiment.model_version_b);
} else {
    result = check_content_with_model(text, current_experiment.model_version_a);
}
```

**关键原则：**

1. **确定性路由：** 同一用户始终在同一组，避免"用户看到不同结果"的混乱
2. **简单指标：** 只关注核心指标（误杀率、漏判率），不要过度分析
3. **快速决策：** 如果实验组明显更好，立即切换；如果明显更差，立即回滚

---

## **3. 代码质量风险：我的"Never Break Userspace"检查**

### **3.1 向后兼容性：切换过程中的用户空间保护**

**风险点：**
- 从 Vendor API 切换到 In-house 模型时，可能改变现有行为
- A/B Test 可能导致同一用户在不同时间看到不同结果

**我的要求：**

```c
// 必须保证：旧行为 = 新行为的超集
// 即：如果旧系统拒绝，新系统必须也拒绝（或更严格）

bool is_backward_compatible(
    const struct safety_result *old_result,
    const struct safety_result *new_result
) {
    // 如果旧系统拒绝，新系统必须拒绝
    if (old_result->blocked && !new_result->blocked) {
        return false;  // 这是破坏性变更！
    }
    
    // 如果旧系统通过，新系统可以更严格（拒绝）
    // 这是允许的，因为安全优先
    
    return true;
}
```

**核心原则：** 在切换过程中，新系统必须"至少和旧系统一样严格"。可以更严格，但不能更宽松。

---

### **3.2 错误处理：失败时的降级策略**

**现有设计的问题：**
- 如果 In-house 模型失败，降级到 Vendor
- 如果 Vendor 也失败，怎么办？

**我的设计：**

```c
struct safety_result check_with_fallback(const char *text) {
    // 先试 In-house
    struct safety_result result = inhouse_check(text);
    
    if (result.status == SUCCESS) {
        return result;
    }
    
    // In-house 失败，降级到 Vendor
    result = vendor_check(text);
    
    if (result.status == SUCCESS) {
        return result;
    }
    
    // 都失败了，默认拒绝（安全优先）
    return (struct safety_result) {
        .blocked = true,
        .confidence = 0.0,
        .reason = "All safety checks failed, defaulting to block"
    };
}
```

**关键原则：** 失败时默认拒绝，不要默认通过。这是安全系统的铁律。

---

## **4. 实施路线图：我的"实用主义"版本**

### **阶段一：替换，不是增强（1-2个月）**

**目标：** 用简单的 In-house 模型替换 Vendor API

**动作：**
1. 部署一个 Fast Model（DistilBERT 级别）
2. 部署一个 Deep Model（BERT-Large 级别）
3. 实现简单的决策逻辑（阈值判断）
4. **直接替换，不要并行运行**

**为什么？** 并行运行（Shadow Mode）增加复杂度，但不解决实际问题。如果模型不够好，直接替换会立即暴露问题，然后快速修复。

---

### **阶段二：优化，不是重构（2-3个月）**

**目标：** 根据实际数据优化模型和阈值

**动作：**
1. 收集误判样本（False Positive）
2. 收集漏判样本（False Negative）
3. 微调模型或调整阈值
4. **不要增加新层，优化现有层**

**为什么？** 如果 Fast Model 准确率不够，优化 Fast Model，不要增加 L0 层。如果 Deep Model 太慢，优化 Deep Model，不要增加 L3 层。

---

### **阶段三：A/B Test，如果需要（3-4个月）**

**目标：** 对比不同模型版本或策略

**动作：**
1. 实现简单的用户ID哈希路由
2. 对比两个版本的指标
3. **快速决策（1-2周），不要拖几个月**

**为什么？** 如果 A/B Test 需要运行几个月才能得出结论，说明差异太小，不值得关注。

---

## **5. 代码审查清单：我的"简洁执念"**

### **5.1 函数长度**

**我的规则：** 如果函数超过 50 行，就需要重构。

**检查点：**
- [ ] 每个函数只做一件事
- [ ] 函数名清楚表达意图
- [ ] 没有超过 3 层嵌套

---

### **5.2 错误处理**

**我的规则：** 错误处理是正常流程，不是特殊情况。

**检查点：**
- [ ] 所有错误路径都有明确处理
- [ ] 失败时默认拒绝，不是默认通过
- [ ] 错误信息对调试有用

---

### **5.3 配置 vs 代码**

**我的规则：** 如果可以用代码表达，就不要用配置。

**检查点：**
- [ ] 策略逻辑在代码中，不在配置文件中
- [ ] 配置只用于"真正的配置"（如模型路径、阈值）
- [ ] 没有 DSL，没有规则引擎

---

### **5.4 测试**

**我的规则：** 测试应该简单、快速、可重复。

**检查点：**
- [ ] 单元测试覆盖核心逻辑
- [ ] 集成测试覆盖主要路径
- [ ] 没有"需要人工判断"的测试

---

## **6. 总结：我的最终建议**

### **6.1 核心原则**

1. **简单优于复杂：** 如果两层够用，就不要四层
2. **代码优于配置：** 如果可以用代码表达，就不要用 DSL
3. **确定性优于随机：** A/B Test 路由应该是确定性的
4. **安全优先：** 失败时默认拒绝，切换时不能更宽松

---

### **6.2 必须避免的陷阱**

1. **过度工程化：** 不要为了"理论完美"而增加不必要的复杂性
2. **配置驱动：** 不要试图用配置解决所有问题，有些问题需要代码
3. **并行运行：** 不要长期并行运行两套系统，增加复杂度但不解决实际问题
4. **统计陷阱：** 不要为了"统计显著性"而运行过长的 A/B Test

---

### **6.3 成功标准**

**好的架构应该：**
- 新员工能在 1 天内理解核心逻辑
- 修改策略只需要改代码，不需要改配置
- 失败时行为可预测（默认拒绝）
- 切换过程中不破坏现有行为

**如果做不到这些，说明架构太复杂了。**

---

## **附录：代码示例**

### **A. 统一安全检查接口**

```c
// safety_check.h
#ifndef SAFETY_CHECK_H
#define SAFETY_CHECK_H

#include <stdbool.h>
#include <stdint.h>

struct safety_result {
    bool blocked;
    float confidence;
    uint64_t model_version;
    const char *reason;
};

struct safety_result check_content(const char *text, uint64_t user_id);

#endif
```

```c
// safety_check.c
#include "safety_check.h"
#include "fast_model.h"
#include "deep_model.h"

struct safety_result check_content(const char *text, uint64_t user_id) {
    // 先试 Fast Model
    struct safety_result fast = fast_model_check(text);
    
    // 如果 Fast Model 确定（高置信度），直接返回
    if (fast.confidence > 0.95f) {
        return fast;
    }
    
    // 不确定，调用 Deep Model
    struct safety_result deep = deep_model_check(text);
    
    // Deep Model 的结果就是最终结果
    // 如果也不确定（confidence < 0.5），默认拒绝
    if (deep.confidence < 0.5f) {
        deep.blocked = true;
        deep.reason = "Low confidence, defaulting to block";
    }
    
    return deep;
}
```

### **B. A/B Test 路由**

```c
// ab_test.h
#ifndef AB_TEST_H
#define AB_TEST_H

#include <stdbool.h>
#include <stdint.h>
#include <time.h>

struct experiment {
    uint64_t experiment_id;
    float ratio;
    uint64_t model_version_a;
    uint64_t model_version_b;
    time_t start_time;
    time_t end_time;
};

bool is_in_experiment(const struct experiment *exp, uint64_t user_id);
uint64_t get_model_version(const struct experiment *exp, uint64_t user_id);

#endif
```

```c
// ab_test.c
#include "ab_test.h"

static uint32_t hash32(uint64_t x) {
    x ^= x >> 33;
    x *= 0xff51afd7ed558ccdULL;
    x ^= x >> 33;
    x *= 0xc4ceb9fe1a85ec53ULL;
    x ^= x >> 33;
    return (uint32_t)x;
}

bool is_in_experiment(const struct experiment *exp, uint64_t user_id) {
    time_t now = time(NULL);
    if (now < exp->start_time || now > exp->end_time) {
        return false;
    }
    
    uint32_t hash = hash32(user_id ^ exp->experiment_id);
    return (hash % 10000) < (uint32_t)(exp->ratio * 10000);
}

uint64_t get_model_version(const struct experiment *exp, uint64_t user_id) {
    if (is_in_experiment(exp, user_id)) {
        return exp->model_version_b;
    }
    return exp->model_version_a;
}
```

---

**记住：好的代码不是写出来的，是删出来的。如果某个功能让你觉得"可能需要"，那就不要实现它。等真正需要的时候再实现。**

**— Linus Torvalds**

