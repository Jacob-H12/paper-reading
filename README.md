# 📚 Paper Reading Notes

论文阅读笔记，按课题组织。每篇论文都有「批读格式」阅读笔记：原文完整保留 + 内嵌批注。

## EEG 闭环游戏实验 — 基于脑电的自适应认知训练与神经反馈

| 论文 | 期刊 | 方法特点 |
|------|------|---------|
| [NFb-EEG-BCI-ADHD](EEG-Closed-Loop-Game/[Neuroscience%202026]%20NFb-EEG-BCI-ADHD) | Neuroscience 2026 | Unity滑雪游戏 + NeuroSky EEG + ML自适应难度, ADHD注意力↑21.5% |
| [Single-Channel-NFb-ADHD](EEG-Closed-Loop-Game/[J%20Neuroeng%20Rehabil%202026]%20Single-Channel-NFb-ADHD) | J Neuroeng Rehabil 2026 | 单通道额叶EEG + 平板游戏, NFb组 > 无反馈组 (浙大) |
| [IRF-Attention-NFb](EEG-Closed-Loop-Game/[J%20Healthc%20Eng%202021]%20IRF-Attention-NFb) | J Healthc Eng 2021 | 改进随机森林5级注意力分类 + 3种严肃游戏闭环 |
| [P300-BCI-Attention](EEG-Closed-Loop-Game/[Front%20Hum%20Neurosci%202019]%20P300-BCI-Attention) | Front Hum Neurosci 2019 | P300自适应难度神经反馈, 30min训练→注意力RT显著缩短 |
| [BCI-CIPN-RCT](EEG-Closed-Loop-Game/[Cancer%202024]%20BCI-CIPN-RCT) | Cancer 2024 | 双盲RCT, 20次BCI游戏训练, 效应量1.07, 1月后持续改善 |
| [Closed-Loop-Memory](EEG-Closed-Loop-Game/[J%20Neurosci%20Methods%202024]%20Closed-Loop-Memory) | J Neurosci Methods 2024 | 实时EEG解码记忆状态, 闭环优化记忆编码时机 |
| [Co-Adaptive-Neural-Interface](EEG-Closed-Loop-Game/[NMI%202026]%20Co-Adaptive-Neural-Interface) | Nature Mach Intell 2026 | 控制论+博弈论建模人-机共适应, 14人EMG接口, 预测并塑造用户行为 |

---

## 仓库结构

```
paper-reading/
├── EEG-Closed-Loop-Game/              # EEG 闭环游戏实验 (6 篇)
│   ├── [Neuroscience 2026] NFb-EEG-BCI-ADHD/
│   ├── [J Neuroeng Rehabil 2026] Single-Channel-NFb-ADHD/
│   ├── [J Healthc Eng 2021] IRF-Attention-NFb/
│   ├── [Front Hum Neurosci 2019] P300-BCI-Attention/
│   ├── [Cancer 2024] BCI-CIPN-RCT/
│   ├── [J Neurosci Methods 2024] Closed-Loop-Memory/
│   └── [NMI 2026] Co-Adaptive-Neural-Interface/
│
└── README.md                          # 本文件
```

## 批读格式

每篇论文包含：

```
[期刊 年份] 论文名/
├── README.md          # 论文概览 + Section 导航
└── sections/          # 批读笔记（原文 + 内嵌批注）
    ├── 00-abstract.md
    ├── 01-introduction.md
    └── ...
```

文件夹命名: `[期刊 年份] 论文名`

---

由 Jacob 协助整理 📚 | 更新: 2026-04-02 | 共 7 篇论文
