# A Neurofeedback-Guided EEG and BCI Framework for Personalized Attention Rehabilitation in ADHD

**作者**: Wenyang Yang, Jingrui Yuan, Lin Ding, Steven Kwok Keung Chow  
**期刊**: Neuroscience, 602:67-80 (2026)  
**DOI**: [10.1016/j.neuroscience.2026.03.010](https://doi.org/10.1016/j.neuroscience.2026.03.010)  
**PMID**: 41831590  
**Open Access**: CC BY 4.0

## 一句话总结

开发自适应多任务 EEG-BCI 训练系统（Unity 滑雪游戏 SkiSport），结合 NeuroSky 单通道 EEG 实时神经反馈 + ML 驱动个性化难度调整，ADHD 组注意力提升 21.5%，XGBoost 难度预测 R²=0.98（窗口级 within-subject）。

## 核心贡献

1. **自适应闭环框架**: EEG 实时采集 → 注意力指标解码 → 游戏难度自动调整（每 5 秒）
2. **ML 驱动个性化**: SVR / XGBoost / MLP 预测最优难度参数，XGBoost 最优
3. **回归式难度调整**: 将难度适配建模为回归问题，替代传统阶梯式规则
4. **双群体验证**: 25 ADHD + 25 对照，个性化后注意力额外提升 10%

## 📖 批读导航

| Section | 内容 | 关键批注 |
|---------|------|---------|
| [00 - Abstract](sections/00-abstract.md) | 摘要 | R² 误导性、eSense 黑盒 |
| [01 - Introduction](sections/01-introduction.md) | 引言 | 占位符残留(SXDEWQ)、文献堆砌 |
| [02 - Methodology](sections/02-methodology.md) | 方法 | **window-level split 信息泄漏**（最大缺陷）、ADHD 诊断标准缺失 |
| [03 - Results](sections/03-results.md) | 结果 | MLP 指标自相矛盾、练习效应未控制 |
| [04 - Discussion](sections/04-discussion.md) | 讨论 & 总评 | 过度解读神经机制、6个局限叠加 |

## 关键数字

| 指标 | 数值 |
|------|------|
| EEG 注意力提升 (ADHD) | +21.5% |
| EEG 注意力提升 (对照) | +7.85% |
| 个性化额外提升 | +10.05% |
| 行为指标整体提升 | +19% |
| XGBoost R² | 0.9826 (window-level, within-subject) |
| 被试数 | 50 (25 ADHD + 25 Control) |
| EEG 设备 | NeuroSky TGAM (单通道 FP1) |
| 期刊 IF | ~3.3 |

## 总体评价

| 维度 | 评分 | 说明 |
|------|------|------|
| 创新性 | ⭐⭐⭐ | 回归式难度调整 vs 阶梯式，有改进但非突破 |
| 方法严谨度 | ⭐⭐ | window-level split 信息泄漏 + 黑盒指标 |
| 写作质量 | ⭐⭐⭐ | 整体通顺，占位符残留(SXDEWQ) |
| 实验设计 | ⭐⭐ | 无随机化、无 sham 对照、样本小 |
| 临床意义 | ⭐⭐ | 短期状态改变 ≠ 康复效果 |
| 可复现性 | ⭐⭐ | 黑盒指标 + 未开源 |

## 🔑 核心 Takeaway

- **可借鉴**: 自适应闭环设计思路、多模态（EEG+行为）评估框架
- **需警惕**: 消费级单通道 EEG 的科学价值有限；window-level 评估会严重高估模型性能
- **对 MI 解码研究的启示**: 本文做的是 neurofeedback（注意力调节），非 motor imagery decoding，技术路线不同但闭环框架可参考
