# ERAS Failure Prediction Model - Technical Parameters Summary
## 急性小肠梗阻急诊手术ERAS失败预测模型 - 技术参数汇总

---

## 一、多因素Logistic回归模型系数

### 完整系数表
| 变量 | 回归系数 (β) | 标准误 (SE) | 95% CI | p值 |
|------|-------------|------------|--------|-----|
| **(Intercept)** | **-9.659714** | 1.212576 | — | 1.64e-15 |
| Age (years) | 0.0506770685 | 0.0082289335 | 0.0349277112, 0.0672548539 | <0.001 |
| ASA Grade III-IV (vs I-II) | 1.7789876534 | 0.2906921172 | 1.2187167688, 2.3608829468 | <0.001 |
| aCCI | 0.3350471249 | 0.0665380626 | 0.2075785619, 0.4690629808 | <0.001 |
| Serum albumin (g/L) | -0.0500661185 | 0.0192289776 | -0.0883793090, -0.0128293254 | 0.009 |
| Decreased mural enhancement (Yes vs No) | 2.3110615562 | 0.3039600878 | 1.7319406439, 2.9264163728 | <0.001 |
| Shock index | 1.9642234967 | 0.6176920930 | 0.7692756624, 3.1962100081 | 0.001 |
| Serum lactate (mmol/L) | 1.1585602154 | 0.1829326302 | 0.8120578391, 1.5305987633 | <0.001 |
| Electrolyte disturbance (Yes vs No) | **0.9360609543** | 0.2482747960 | 0.4544622182, 1.4298595901 | <0.001 |

### ✅ 截距值确认（已完成）
- **截距已确认**: -9.659714（来自原始R输出，训练集N=640多因素Logistic回归）
- **标准误**: 1.212576，**z值**: -7.966，**p值**: 1.64e-15
- 模型拟合于640例训练集，7个自由度，AIC=453.54

---

## 二、风险分层阈值（基于训练集失败率分布 ~35-40%）

| 风险分层 | 预测概率范围 | 临床建议 |
|---------|-------------|---------|
| **Low（低风险）** | < 20% | 标准ERAS方案 |
| **Intermediate（中风险）** | 20% - 40% | 加强监测的改良ERAS方案 |
| **High（高风险）** | ≥ 40% | 强化管理方案 |

### 阈值选择依据
- 基于Table 6（训练集分类准确率表）：
  - ≥20%阈值：灵敏度93.3%，特异度66.8%，PPV 67.2%，NPV 93.2%
  - ≥40%阈值：灵敏度85.6%，特异度83.0%，PPV 78.6%，NPV 88.7%
- 训练集ERAS失败基线率：42.2%
- 分层逻辑：
  - Low (<20%)：显著低于基线失败率，预计ERAS成功概率高
  - Intermediate (20-40%)：接近或略低于基线，需要加强监测
  - High (≥40%)：高于基线，需要强化干预

---

## 三、ABCDE筛查工具截断值

### 基于Youden指数优化的单因素ROC分析

| 字母 | 领域 | 变量 | 截断值 | 灵敏度 | 特异度 | Youden指数 | AUC |
|------|------|------|--------|--------|--------|-----------|-----|
| **A** | Age / Albumin | Age ≥ 71 years | 71岁 | 39.1% | 82.9% | 0.22 | 0.622 |
| | | Albumin ≤ 38 g/L | 38 g/L | 58.2% | 47.3% | 0.06 | 0.521 |
| **B** | Biochemistry | Lactate ≥ 2.0 mmol/L | 2.05 mmol/L | 73.0% | 60.9% | 0.34 | 0.710 |
| **C** | Comorbidity | ASA Grade III-IV | — | — | — | — | 0.736 |
| | | aCCI ≥ 6 | 5.5 (取整6) | 64.0% | 74.6% | 0.39 | 0.751 |
| **D** | Disturbance | Shock index ≥ 0.915 | 0.915 | 63.8% | 53.7% | 0.17 | 0.607 |
| | | Electrolyte disturbance (Yes) | — | — | — | — | 0.613 |
| **E** | Enhancement | Decreased mural enhancement (Yes) | — | — | — | — | 0.694 |

### 评分规则
- 每个字母内任一指标异常 = 该字母得1分
- 总分范围：0-5分
- **0-1分**：低风险（预计失败率 <20%）
- **2-3分**：中风险（预计失败率 20-40%）
- **4-5分**：高风险（预计失败率 ≥40%）

---

## 四、变量重要性排序（按 |β| 绝对值）

| 排名 | 变量 | |β| | 临床意义 |
|------|------|-----|---------|
| 1 | Decreased mural enhancement | 2.311 | CT影像提示肠壁缺血/坏死，最强预测因子 |
| 2 | Shock index | 1.964 | 血流动力学不稳定，提示组织灌注不足 |
| 3 | ASA Grade III-IV | 1.779 | 全身状况差，合并症负担重 |
| 4 | Serum lactate | 1.159 | 代谢紊乱，组织缺氧标志物 |
| 5 | Electrolyte disturbance | 0.936 | 内环境紊乱，影响术后恢复 |
| 6 | aCCI | 0.335 | 合并症累积负担 |
| 7 | Age | 0.051 | 年龄因素（每增加1岁风险微增） |
| 8 | Serum albumin | 0.050 | 营养状态（每增加1g/L风险微降） |

---

## 五、模型性能指标

### 区分度 (Discrimination)
| 队列 | AUC | 95% CI |
|------|-----|--------|
| 训练集 (Training) | 0.924 | (0.904-0.945) |
| 内部验证集 (Internal Validation) | 0.935 | (0.908-0.962) |
| 外部验证集 (External Validation) | 0.907 | (0.873-0.941) |

### 校准度 (Calibration)
- Hosmer-Lemeshow 检验 p > 0.05（所有队列）
- 校准曲线显示预测概率与实际概率一致性良好

### 拟合优度
- Deviance: 435.5425
- AIC: 453.5425
- Nagelkerke's R²: 0.6641787

---

## 六、在线计算器与ABCDE工具技术实现

### 部署平台
- **GitHub Pages**（免费静态托管）
- URL格式：`https://USERNAME.github.io/REPO_NAME/`

### 技术栈
- 纯前端实现：HTML5 + CSS3 + JavaScript（ Vanilla JS）
- 无需后端服务器或数据库
- 响应式设计，支持桌面端和移动端

### 文件结构
```
REPO/
├── index.html          # 精准预测模型计算器（入口文件）
├── abcde-tool.html     # ABCDE筛查工具
└── README.md           # 部署说明与技术文档
```

### 计算器核心公式
```
Logit(p) = β₀ + β₁×Age + β₂×ASA + β₃×aCCI + β₄×Albumin + β₅×DME + β₆×ShockIndex + β₇×Lactate + β₈×Electrolyte

Probability = 1 / (1 + exp(-Logit(p)))
```

### ABCDE评分逻辑
```
Score = 0
IF (Age ≥ 71 OR Albumin ≤ 38) THEN Score += 1    // A
IF (Lactate ≥ 2.0) THEN Score += 1               // B
IF (ASA III-IV OR aCCI ≥ 6) THEN Score += 1      // C
IF (ShockIndex ≥ 0.915 OR Electrolyte Yes) THEN Score += 1  // D
IF (DecreasedMuralEnhancement Yes) THEN Score += 1  // E
```

---

## 七、已完成确认事项

1. **✅ 截距值确认**: 已确认为 -9.659714（训练集N=640）
2. **✅ 风险分层阈值**: 已确认采用 <20% / 20-40% / ≥40%
3. **✅ ABCDE截断值**: 已基于完整数据集（1227例）Youden指数计算完成
4. **✅ 队列规模**: 训练集640例 + 内部验证274例 + 外部验证313例 = 1227例总计

---

## 八、JAMA Surgery投稿用图表建议

### 正文图表 (Main Text)
1. **Figure 1**: 模型开发流程图（TRIPOD规范）
2. **Figure 2**: Nomogram或在线计算器截图
3. **Figure 3**: 校准曲线（3个队列合并或分开展示）
4. **Figure 4**: DCA决策曲线分析

### 补充材料 (Supplementary Materials)
- **eTable 1**: LASSO变量筛选过程图
- **eTable 2**: 单因素ROC分析结果
- **eTable 3**: 多因素Logistic回归完整系数表
- **eTable 4**: 敏感性/特异性/PPV/NPV分类准确率表
- **eTable 5**: 变量重要性排序
- **eFigure 1**: ABCDE筛查工具示意图

---

**文档生成时间**: 2026-05-27
**版本**: V1.0
**目标期刊**: JAMA Surgery
