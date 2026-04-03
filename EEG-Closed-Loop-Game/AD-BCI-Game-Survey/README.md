# AD 脑控游戏项目 — 系统调研报告

> 目标：面向阿尔茨海默（AD）患者的 EEG 闭环游戏干预系统
> 调研时间：2026-04-03

---

## 目录

1. [AD 患者稳定 EEG 指标](#1-ad-患者稳定-eeg-指标)
2. [游戏设计框架](#2-游戏设计框架)
3. [闭环控制架构](#3-闭环控制架构)

---

## 1. AD 患者稳定 EEG 指标

### 原则

选取标准：
- **可靠性**：test-retest reliability 高（ICC > 0.7 或多次重复稳定）
- **区分度**：AD vs 健康对照效应量大（Cohen's d > 0.8）
- **实时可算**：适合闭环系统在线提取（延迟 < 2s）
- **与游戏行为有天然联系**：能映射到可玩的游戏机制

---

### ✅ 3-5 个确定指标（文献反复验证，高可靠性）

#### 1. Alpha 功率下降 + Theta 功率增加（频谱慢化）
- **现象**：AD 患者静息态后部 Alpha (8-13Hz) 功率显著降低，Theta (4-8Hz) 功率增加，整体频谱向慢波偏移
- **状态**：静息态（闭眼 / 睁眼均可）
- **稳定性**：⭐⭐⭐⭐⭐ — 是 AD EEG 领域最经典、最稳定的发现，从1980s 复现至今，meta-analysis 一致（Babiloni et al., 2020; Cassani et al., 2018）
- **效应量**：Cohen's d = 1.0-1.5（Alpha 下降），0.8-1.2（Theta 增加）
- **关键通道**：P3, P4, O1, O2, Pz（后部）
- **与游戏的联系**：
  - Alpha 反映**警觉度和注意力资源**——可作为游戏"能量条"
  - Theta/Alpha 比值（TAR）升高 = 认知资源耗竭 → 游戏自动降低难度
  - 神经反馈目标：训练增强 Alpha、抑制过多 Theta
- **实时提取**：FFT/Welch，1-2秒窗口，延迟极低

#### 2. Theta/Alpha Ratio (TAR)
- **现象**：Theta 功率 / Alpha 功率的比值在 AD 中显著升高
- **状态**：静息态 + 任务态均稳定
- **稳定性**：⭐⭐⭐⭐⭐ — 综合指标，比单频段更鲁棒，test-retest ICC > 0.8
- **效应量**：d = 1.2-1.8（AD vs HC），且与 MMSE 评分显著负相关（r ≈ -0.5 to -0.7）
- **与游戏的联系**：
  - 直接映射为"认知负荷指数"——TAR 越高=认知越吃力
  - 游戏可根据 TAR 实时调整难度（核心闭环变量）
  - 训练目标：降低 TAR → 改善认知效率
- **实时提取**：两个频段功率比，计算简单

#### 3. P300 潜伏期延长 + 振幅下降
- **现象**：Oddball 任务诱发的 P300 在 AD 中潜伏期延长 30-80ms，振幅降低 2-5μV
- **状态**：任务态（需 oddball 范式）
- **稳定性**：⭐⭐⭐⭐⭐ — 最经典的认知 ERP 指标，几十年文献一致（Polich, 2007; Parra et al., 2012）
- **效应量**：潜伏期 d = 1.0-1.5，振幅 d = 0.8-1.2
- **关键通道**：Pz, Cz（中央-顶叶）
- **与游戏的联系**：
  - P300 本质是"检测到新异/目标刺激"的脑反应 → 天然适配注意力游戏
  - 游戏中嵌入目标检测任务（如"找到不同的物品"），实时测量 P300
  - P300 改善 = 注意力分配能力提升，可作为游戏通关/升级条件
- **实时提取**：需 epoch 平均，单试次 P300 检测需机器学习辅助（xDAWN 等），延迟 ~1-3s

#### 4. Alpha Peak Frequency (APF) 减慢
- **现象**：AD 患者的个体 Alpha 峰频率从正常的 ~10Hz 下降到 ~8Hz 甚至更低
- **状态**：静息态（闭眼最佳）
- **稳定性**：⭐⭐⭐⭐ — test-retest ICC = 0.85-0.92，是 EEG 中最稳定的个体特征之一
- **效应量**：d = 0.9-1.3
- **与游戏的联系**：
  - APF 与认知处理速度强相关 → 游戏速度/节奏可绑定 APF
  - 长期训练如果 APF 回升 → 标志认知改善
  - 可作为个体化难度校准的基线参数
- **实时提取**：Welch PSD → 8-13Hz 峰值检测，非常稳定

#### 5. EEG 复杂度降低（Lempel-Ziv / 排列熵）
- **现象**：AD 患者 EEG 信号的非线性复杂度降低，反映神经动力学"简单化"
- **状态**：静息态
- **稳定性**：⭐⭐⭐⭐ — 多篇独立复现（Abasolo et al., 2006; Simons et al., 2018），不受 alpha reactivity 影响
- **效应量**：d = 0.8-1.2
- **与游戏的联系**：
  - 复杂度 ↔ 大脑信息处理多样性 → 可映射为游戏中的"策略多样性奖励"
  - 鼓励患者尝试多种游戏策略（而非重复单一模式），如果脑复杂度提升则给予奖励
  - 作为长期疗效评估的辅助指标
- **实时提取**：Lempel-Ziv 可在 2s 窗口内在线计算，排列熵也很快

---

### 🔶 2-3 个大概率指标（文献较充分，但稳定性或效应量稍弱）

#### 6. 功能连接降低（EEG Coherence / PLV）
- **现象**：AD 患者脑区间的 Alpha/Beta 频段相干性（coherence）和相位锁定值（PLV）降低，尤其前-后连接
- **状态**：静息态
- **稳定性**：⭐⭐⭐ — 方向一致但数值受参考电极、头皮传导影响，变异较大
- **效应量**：d = 0.6-1.0
- **与游戏的联系**：
  - 功能连接 ↔ 脑区协同 → 可设计需要"多感官整合"的游戏任务
  - 如：视觉+听觉同步任务，只有脑区协同良好才能完成
  - 连接改善 = 网络恢复，有临床意义
- **实时提取**：PLV 计算需 ~2-4s 窗口，计算量中等

#### 7. Mismatch Negativity (MMN) 减弱
- **现象**：被动 oddball 中 MMN 振幅降低，反映前注意自动变化检测能力下降
- **状态**：任务态（被动听觉 oddball，不需主动反应）
- **稳定性**：⭐⭐⭐ — 作为被动指标稳定性不错，但效应量小于 P300
- **效应量**：d = 0.5-0.8
- **与游戏的联系**：
  - MMN 可在游戏背景音中嵌入偏差音来被动监测（患者无需额外操作）
  - 作为背景监控指标，不干扰游戏流程
  - 反映"自动预测能力"——对 AD 的早期变化敏感
- **实时提取**：需足够 oddball 试次平均（~50-100），延迟较大，适合阶段性评估

#### 8. Delta 功率增加（严重期标志）
- **现象**：中晚期 AD 患者 Delta (1-4Hz) 功率显著增加
- **状态**：静息态
- **稳定性**：⭐⭐⭐ — 在中晚期 AD 非常稳定，但早期 AD/MCI 阶段不够敏感
- **效应量**：d = 0.5-0.8（MCI），d = 1.0+（中期 AD）
- **与游戏的联系**：
  - Delta 过多 → 皮层抑制/嗜睡 → 游戏检测到后降低强度或暂停
  - 作为安全/疲劳监控指标（"该休息了"）
- **实时提取**：FFT，非常简单

---

### 指标汇总表

| # | 指标 | 状态 | 可靠度 | 效应量(d) | 实时性 | 游戏映射 | 推荐级别 |
|---|------|------|--------|-----------|--------|----------|----------|
| 1 | Alpha↓ + Theta↑ | 静息 | ⭐⭐⭐⭐⭐ | 1.0-1.5 | ⚡即时 | 能量/警觉 | ✅ 确定 |
| 2 | TAR | 静息+任务 | ⭐⭐⭐⭐⭐ | 1.2-1.8 | ⚡即时 | 认知负荷 | ✅ 确定 |
| 3 | P300 延迟+↓ | 任务 | ⭐⭐⭐⭐⭐ | 1.0-1.5 | ⏱ 1-3s | 目标检测 | ✅ 确定 |
| 4 | APF 减慢 | 静息 | ⭐⭐⭐⭐ | 0.9-1.3 | ⚡即时 | 节奏/速度 | ✅ 确定 |
| 5 | 复杂度↓ | 静息 | ⭐⭐⭐⭐ | 0.8-1.2 | ⚡即时 | 策略多样性 | ✅ 确定 |
| 6 | Coherence/PLV↓ | 静息 | ⭐⭐⭐ | 0.6-1.0 | ⏱ 2-4s | 多感官协同 | 🔶 大概率 |
| 7 | MMN↓ | 任务(被动) | ⭐⭐⭐ | 0.5-0.8 | ⏱ 阶段性 | 背景监控 | 🔶 大概率 |
| 8 | Delta↑ | 静息 | ⭐⭐⭐ | 0.5-1.0 | ⚡即时 | 疲劳/安全 | 🔶 大概率 |

---

## 2. 游戏设计框架

### 2.1 游戏类型选择

AD 患者的核心认知缺陷：**记忆 > 注意力 > 执行功能 > 语言 > 视空间**

建议设计一个**模块化游戏平台**，包含 3-4 个子游戏，每个针对不同认知域：

| 子游戏 | 认知目标 | 游戏原型 | EEG 驱动指标 |
|--------|---------|----------|-------------|
| 🏠 记忆花园 | 情景记忆 | 配对翻牌/物品记忆 | P300 + TAR |
| 🎵 节奏之路 | 注意力/处理速度 | 节奏/音乐追踪 | Alpha + APF |
| 🧩 模式拼图 | 执行功能/工作记忆 | 模式匹配/序列记忆 | TAR + Coherence |
| 🌊 放松港湾 | 情绪调节/休息 | 冥想/呼吸引导 | Alpha + Delta |

### 2.2 可调参数（游戏侧）

```
游戏参数空间:
├── 难度维度
│   ├── 项目数量（记忆牌数：4/6/8/12/16）
│   ├── 时间压力（无限/宽松/适中/紧张）
│   ├── 干扰物数量（0/1/2/3）
│   ├── 相似度（高辨识/中/低辨识）
│   └── 序列长度（1/2/3/4/5 步）
│
├── 感官维度
│   ├── 视觉对比度（高/中/低）
│   ├── 音频音量（自适应）
│   ├── 动画速度（0.5x/1x/1.5x/2x）
│   ├── 颜色饱和度
│   └── 字体大小
│
├── 反馈维度
│   ├── 奖励频率（每次操作/完成任务/阶段性）
│   ├── 奖励类型（视觉特效/音效/分数/虚拟物品）
│   ├── 提示等级（无提示/微提示/明确提示/答案展示）
│   └── 错误反馈（无/柔和/明确）
│
└── 时间维度
    ├── 单次游戏时长（5/10/15/20 分钟）
    ├── 休息间隔（自适应/固定）
    └── 总训练时长
```

### 2.3 技术框架

```
推荐技术栈:
├── 游戏引擎: Unity (C#)
│   ├── 跨平台（PC/平板/手机）
│   ├── 生态成熟，素材丰富
│   └── 与 EEG SDK 对接方便（NeuroSky/OpenBCI/Emotiv 均有 Unity 插件）
│
├── EEG 采集 & 处理:
│   ├── 设备: OpenBCI Cyton (8ch) 或 Emotiv EPOC X (14ch)
│   │   └── 消费级中最适合研究的选择
│   ├── 实时处理: Python (MNE-Realtime / BCI2000 / LSL)
│   │   ├── Lab Streaming Layer (LSL) 做数据流
│   │   ├── MNE-Python 做特征提取
│   │   └── 或 BrainFlow（跨设备统一 API）
│   └── Unity ↔ Python 通信: LSL / UDP / WebSocket
│
├── 闭环控制器: Python
│   ├── 特征提取模块
│   ├── 状态估计模块
│   ├── 难度调整策略
│   └── 数据记录
│
└── 数据存储 & 分析:
    ├── SQLite（单机）/ PostgreSQL（多站点）
    ├── 原始 EEG: EDF+ 格式
    └── 分析: Python (MNE + scikit-learn + XGBoost)
```

### 2.4 架构图

```
┌─────────────────────────────────────────────────────────────┐
│                     Unity 游戏端                              │
│                                                               │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐    │
│  │ 记忆花园  │  │ 节奏之路  │  │ 模式拼图  │  │ 放松港湾  │    │
│  └─────┬────┘  └─────┬────┘  └─────┬────┘  └─────┬────┘    │
│        └──────┬──────┴──────┬──────┘              │         │
│               ▼             ▼                      ▼         │
│         ┌──────────────────────────────────────┐            │
│         │        游戏参数控制接口 (API)          │            │
│         │  difficulty / speed / hints / reward   │            │
│         └──────────────┬───────────────────────┘            │
│                        │ LSL / WebSocket                     │
└────────────────────────┼─────────────────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────────────┐
│                   闭环控制器 (Python)                         │
│                                                               │
│  ┌──────────┐    ┌──────────────┐    ┌────────────────┐     │
│  │ EEG 特征  │───▶│ 状态估计器    │───▶│ 难度调整策略    │     │
│  │ 提取模块  │    │ (认知状态)    │    │ (控制逻辑)     │     │
│  └─────┬────┘    └──────────────┘    └───────┬────────┘     │
│        │                                       │              │
│        │    ┌──────────────────┐               │              │
│        │    │  数据记录 & 日志  │               │              │
│        │    └──────────────────┘               │              │
│        │                                       ▼              │
│  ┌─────┴──────────────────────────────────────────┐         │
│  │           Lab Streaming Layer (LSL)             │         │
│  └─────┬──────────────────────────────────────────┘         │
│        │                                                      │
└────────┼──────────────────────────────────────────────────────┘
         │
         ▼
┌─────────────────────┐
│   EEG 硬件采集端     │
│  OpenBCI / Emotiv    │
│  BrainFlow API       │
└─────────────────────┘
```

---

## 3. 闭环控制架构

### 3.1 神经接口：EEG → 认知状态

**实时特征提取 Pipeline：**

```
原始 EEG (250Hz)
    │
    ▼
预处理 (在线)
├── 带通滤波 1-45Hz (Butterworth 4阶)
├── 陷波 50Hz (工频)
├── 坏道检测 (方差阈值)
└── 基线校正 (滑动窗口减均值)
    │
    ▼
特征提取 (2秒滑动窗口, 0.5秒步进)
├── 频域特征
│   ├── Alpha 功率 (O1/O2/P3/P4)
│   ├── Theta 功率 (F3/F4/Fz)
│   ├── TAR = Theta/Alpha
│   ├── APF (Alpha 峰频率)
│   ├── Delta 功率 (全脑)
│   └── Beta 功率 (F3/F4)
├── 时域特征
│   ├── P300 振幅/潜伏期 (Pz, 若有 oddball 事件)
│   └── ERP 模板匹配分数
├── 非线性特征
│   ├── Lempel-Ziv 复杂度
│   └── 排列熵
└── 连接性特征 (可选)
    └── Alpha-band PLV (前-后)
    │
    ▼
认知状态估计
├── 实时指标融合 → 综合认知状态分 (0-100)
├── 分维度评分:
│   ├── 警觉度 = f(Alpha, APF)
│   ├── 认知负荷 = f(TAR, Theta)
│   ├── 疲劳度 = f(Delta, Alpha下降速率)
│   └── 注意力 = f(P300, TAR, Beta/Theta)
└── 滑动平均 + 变化趋势检测
```

### 3.2 控制逻辑：认知状态 → 游戏参数

采用 **三层控制策略**：

#### 第一层：实时安全守护（秒级）
```python
# 每 0.5 秒检查
if fatigue_score > 0.8:
    game.pause("建议休息一下")
    game.show_breathing_guide()
elif delta_power > threshold_high:
    game.reduce_speed(0.5)    # 防止嗜睡期继续
elif engagement < 0.2:
    game.trigger_attention_cue()  # 闪烁/音效唤醒
```

#### 第二层：自适应难度调节（分钟级，核心）

**策略 A — 基于规则的 PID 式控制：**

```python
class DifficultyController:
    """
    目标：维持认知状态在 "最优挑战区间"（Flow Zone）
    原理：类 PID 控制，TAR 为主控变量
    """
    def __init__(self):
        self.target_tar = None  # 个体化校准
        self.tar_history = []
        self.difficulty = 0.5   # 0-1 范围
        
        # PID 参数（需校准）
        self.Kp = 0.3   # 比例增益
        self.Ki = 0.05   # 积分增益
        self.Kd = 0.1    # 微分增益
        self.integral = 0
        self.prev_error = 0
    
    def calibrate(self, baseline_tar):
        """用静息态 baseline 确定个体目标 TAR"""
        self.target_tar = baseline_tar * 1.1  # 略高于基线 = 轻度挑战
    
    def update(self, current_tar, dt=2.0):
        """每 2 秒调用一次"""
        error = current_tar - self.target_tar  # 正 = 太难，负 = 太简单
        
        self.integral += error * dt
        self.integral = np.clip(self.integral, -5, 5)  # 防积分饱和
        
        derivative = (error - self.prev_error) / dt
        self.prev_error = error
        
        # PID 输出：正 = 应降低难度
        adjustment = -(self.Kp * error + self.Ki * self.integral + self.Kd * derivative)
        
        self.difficulty = np.clip(self.difficulty + adjustment * 0.1, 0.05, 1.0)
        return self.difficulty
```

**策略 B — ML 驱动自适应（数据积累后）：**

```python
class MLDifficultyPredictor:
    """
    输入: 当前认知状态特征 + 近期表现 + 个体基线
    输出: 预测最优难度参数
    
    训练数据来源: 规则控制阶段积累的 (state, difficulty, performance) 三元组
    """
    def __init__(self):
        self.model = XGBRegressor(
            n_estimators=100,
            max_depth=5,
            learning_rate=0.1
        )
        self.is_trained = False
    
    def predict_optimal_difficulty(self, cognitive_features, recent_performance):
        if not self.is_trained:
            return None  # 退回规则策略
        
        features = np.concatenate([
            cognitive_features,        # [alpha, theta, tar, apf, complexity, ...]
            recent_performance,        # [accuracy, rt, engagement_score]
            self.patient_baseline      # [baseline_tar, baseline_alpha, age, mmse]
        ])
        return self.model.predict(features.reshape(1, -1))[0]
```

#### 第三层：跨会话个性化（天/周级）

```python
class LongTermAdapter:
    """
    跟踪患者跨多次训练的进步，调整训练计划
    """
    def update_patient_profile(self, session_data):
        """每次训练结束后调用"""
        self.sessions.append(session_data)
        
        # 更新基线（指标可能随训练改善）
        self.recalibrate_baseline()
        
        # 检测进步趋势
        if self.detect_improvement_trend(metric='tar', window=5):
            self.raise_target_zone()      # 提升目标区间
            self.unlock_harder_games()    # 解锁更难的游戏模块
        
        # 检测平台期
        if self.detect_plateau(window=10):
            self.switch_game_type()       # 换个游戏类型
            self.adjust_reward_schedule() # 调整奖励策略
        
        # 生成下次训练方案
        return self.generate_next_session_plan()
```

### 3.3 闭环时序图

```
时间 ──────────────────────────────────────────────────▶

患者:   [佩戴EEG] → [静息校准3min] → [游戏开始] → ... → [游戏结束] → [静息后测]
                                        │
EEG:    ────────── 持续采集 ──────────────────────────────────────
                                        │
特征:            │ baseline │  ←─ 2s窗口 0.5s步进 ─→  │
                     │                  │
状态:                │ 校准    │  ←─ 认知状态实时更新 ─→ │
                     │ target  │        │
                                        │
控制器:                        │ 安全守护 (0.5s) ─────────────── │
                               │ 难度调节 (5-10s) ───────────── │
                               │                                 │
游戏:                          │←─ 参数实时调整 ─→│
                               │  difficulty ↕    │
                               │  speed ↕         │
                               │  hints ↕         │

会话后:                                                    │ 数据存储 │
                                                           │ 模型更新 │
                                                           │ 报告生成 │
```

### 3.4 数据流协议

```
EEG 设备 ──(BrainFlow/LSL)──▶ Python 处理端
    │                              │
    │                         特征提取
    │                              │
    │                         状态估计
    │                              │
    │                         控制决策
    │                              │
    ▼                              ▼
  EDF+ 存储              ──(LSL/UDP)──▶ Unity 游戏
                                            │
                                       参数更新
                                            │
                                       行为数据
                                            │
                                   ──(LSL/UDP)──▶ Python
                                                     │
                                                 数据融合
                                                     │
                                                 SQLite/CSV
```

### 3.5 关键设计决策

| 决策点 | 推荐方案 | 替代方案 | 理由 |
|--------|---------|---------|------|
| EEG 设备 | OpenBCI Cyton 8ch | Emotiv EPOC X 14ch | 开源、可定制、研究级信号质量 |
| 游戏引擎 | Unity | Pygame / Godot | 生态最成熟，跨平台，EEG 插件多 |
| 通信协议 | LSL | UDP/WebSocket | LSL 是神经科学标准，时间同步精确 |
| 闭环频率 | 2Hz (500ms) | 4Hz | 够快且不增加计算负担 |
| 初期控制策略 | PID 规则 | RL/ML | 先积累数据，后期切 ML |
| 难度主控变量 | TAR | Alpha alone | TAR 更鲁棒，抗个体差异 |
| 校准方式 | 每次 3min 静息 | 首次校准 | AD 患者状态波动大，每次校准更安全 |

---

## 4. 下一步行动

- [ ] 确定目标 AD 人群（MCI? 轻度? 中度?）
- [ ] 确定 EEG 设备选型
- [ ] 原型游戏设计（先做一个子游戏验证闭环）
- [ ] 伦理审查准备
- [ ] 文献补充搜索（AD + neurofeedback game 的 RCT）

---

## 参考文献（核心）

1. Babiloni C, et al. (2020). What electrophysiology tells us about Alzheimer's disease: a window into the synchronization and connectivity of brain neurons. *Neurobiol Aging*, 85:58-73.
2. Cassani R, et al. (2018). Systematic review on resting-state EEG for Alzheimer's disease diagnosis and progression assessment. *Dis Markers*, 2018:5174815.
3. Polich J. (2007). Updating P300: an integrative theory of P3a and P3b. *Clin Neurophysiol*, 118(10):2128-2148.
4. Abasolo D, et al. (2006). Entropy analysis of the EEG background activity in Alzheimer's disease patients. *Physiol Meas*, 27(3):241-253.
5. Simons S, et al. (2018). Fuzzy entropy analysis of the electroencephalogram in patients with Alzheimer's disease. *Physiol Meas*, 39(5):055001.
6. Prinsloo S, et al. (2024). Brain-computer interface relieves chronic chemotherapy-induced peripheral neuropathy. *Cancer*, 130(2):300-311.
7. Madduri MM, et al. (2026). Computational framework for co-adaptive neural interfaces. *Nat Mach Intell*, 8:372-387.
8. Wang B, et al. (2021). EEG-based closed-loop neurofeedback for attention. *J Healthc Eng*, 2021:5535810.
