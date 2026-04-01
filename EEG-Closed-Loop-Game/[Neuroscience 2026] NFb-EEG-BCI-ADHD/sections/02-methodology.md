# 02 - Methodology

## 2.1 Game Development (SkiSport)

### 原文概要
- Unity 2022 开发的双任务滑雪游戏
- 玩家控制滑雪者下坡 + 收集金币（每个10分）+ 按屏幕显示数字对应的键
- 每 5 秒根据 EEG 注意力指数调整难度
- 4 个难度参数：障碍物频率、有效重力、转弯速度、滑动加速度
- 初始参数：速度=0, 加速度=50, 转弯速度=100, 障碍物频率=10, 重力=4

### 批注
- ✅ Dual-task 设计（运动控制 + 数字按键反应），能同时测持续注意力和分配注意力
- ⚠️ 难度参数只有 4 个维度，空间不大
- ⚠️ Session 时长、每人总游戏轮数等关键信息分散在后续章节，方法部分组织不够紧凑

---

## 2.2 EEG-based BCI System

### 原文概要
- 设备：NeuroSky ThinkGear ASIC Module (TGAM)，干电极，FP1 单通道 + 耳夹参考
- 信号传输：ThinkGear Connector → Unity TCP
- 输出指标：eSense 注意力指数（实时）
- 片上处理：放大、滤波、50Hz 陷波

### 批注

#### 🔴 核心问题：NeuroSky eSense 黑盒指标
- eSense 注意力指数是 NeuroSky 的**专有算法**，底层大概是 beta/alpha 功率比，但具体公式不公开
- 文中所有"注意力提升"结论都建立在这个**不可验证、不可复现的指标**上
- 如果有原始 EEG 波形数据，至少应该独立计算 theta/beta ratio 或 alpha power 作为补充验证
- 作者在 Limitations 里承认了这一点，但这本质上削弱了所有 EEG 相关结论的科学价值

#### ⚠️ 单通道限制
- FP1 位于前额，容易受到眼动伪迹（EOG）和肌电（EMG）污染
- 没有提到伪迹去除方法（因为只有一个通道也没法做 ICA）
- 信号质量完全依赖设备自带的 POOR_SIGNAL flag

---

## 2.3 Signal-Gameplay Synchronization

### 原文概要
- 同一台机器运行 EEG 采集和游戏，统一系统时钟
- EEG 数据 1 Hz 采样率（eSense 固件限制）
- Unity 端用 `Time.realtimeSinceStartup` 毫秒级时间戳
- 循环缓冲区存储最近 10 秒 EEG 数据
- 线性插值对齐 EEG 和游戏帧时间戳
- 5 秒非重叠窗口计算平均注意力值
- 窗口内 >20% 无效样本则丢弃
- 连续丢失 >100ms 的段也丢弃
- 最终丢弃率 <5%，两组间无显著差异
- SQLite 存储：[t_ms, frame_id, attention_idx, signal_quality, obstacle_rate, gravity, turn_speed, accel, event_code]

### 批注
- ✅ **这部分是全文方法学写得最扎实的**
- ✅ 数据质量控制规则明确，丢弃率报告透明
- ✅ 存储 schema 清晰，支持后续分析
- ℹ️ 1 Hz 注意力指标做线性插值是合理的（低频平滑信号）
- ⚠️ 100ms gap 阈值的选择依据没有说明

---

## 2.4 Participants

### 原文概要
- N = 50（25 ADHD + 25 对照）
- 29 男 21 女，12-39 岁
- ADHD 组：均 age=20.1, baseline attention=33.6
- 对照组：均 age=21.7, baseline attention=51.6
- 标准：身体健康，经过熟悉训练
- 伦理审批：西安石油大学（2023KF05-0102）

### 批注

#### 🔴 ADHD 诊断标准缺失
- 没有说明 ADHD 是如何诊断的 — 临床诊断？DSM-5 标准？自评量表（如 ASRS）？
- 这是分组设计的基础，不能含糊

#### ⚠️ 其他问题
- **样本量偏小**（25+25），统计效力有限
- **年龄跨度大**（12-39），青少年和成年人的注意力基线差异显著，但没做年龄亚组分析
- **排除标准过于简单**：只要求停 12h 咖啡因，没有排除其他精神药物、睡眠质量等混杂因素

---

## 2.5 Machine Learning

### 原文概要
- 三种模型：SVR、XGBoost、MLP（3 层 hidden layer，ReLU + Adam）
- 输入：4 个游戏难度参数（转弯速度、滑动加速度、障碍物数量、重力）
- 输出：EEG 注意力指数（5 秒窗口均值）
- 数据划分：**70/30 随机 split，窗口级别**
- 没有 subject-level 交叉验证（无 LOSO 或 GroupKFold）
- 超参数优化：5-fold CV grid search（在训练集上）
- 评估指标：R², MAE, RMSE

### 批注

#### 🔴 这是全文最大的方法学缺陷

1. **窗口级随机划分导致信息泄漏**
   - 同一被试的连续 5 秒窗口在游戏参数和注意力值上高度自相关
   - 70/30 随机 split 意味着训练集中有大量与测试集时间相邻的窗口
   - 模型本质上是在"插值"时间序列上的邻近点，R² 高不意外

2. **为什么不做 LOSO？**
   - 作者在 Discussion 中说"数据少，获取困难"
   - 但 50 个被试完全可以做 leave-one-subject-out
   - 甚至不需要 LOSO —— 至少做 **session-level split**（Round 1 训练，Round 2 测试）也比窗口级好得多

3. **XGBoost R² = 0.9826 几乎肯定被高估**
   - 在有时间自相关的数据上做随机 split，gradient boosting 类模型尤其容易拟合局部时间模式
   - 如果做 subject-level hold-out，R² 预计会大幅下降

4. **预处理泄漏？**
   - 文中说"All preprocessing was fitted exclusively on the training split" — 好
   - 但 feature standardization 是在 split 之后还是之前做的，描述不够明确

#### ℹ️ 模型选择
- SVR/XGBoost/MLP 三选一的对比还算合理
- MLP 的深度搜索（2-4层，3层最优）有做 ablation，OK
