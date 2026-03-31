# A Neurofeedback-Guided EEG and BCI Framework for Personalized Attention Rehabilitation in ADHD

**作者**: Wenyang Yang et al.  
**期刊**: Neuroscience, 602:67-80 (2026)  
**DOI**: 10.1016/j.neuroscience.2026.03.010  
**PMID**: 41831590

## 一句话总结

开发自适应多任务 EEG-BCI 训练系统（Unity 滑雪游戏 SkiSport），结合 NeuroSky 单通道 EEG 实时神经反馈 + ML 驱动个性化难度调整，ADHD 组注意力提升 21.5%，XGBoost 难度预测 R²=0.98。

## 核心贡献

1. **自适应闭环框架**: EEG 实时采集 → 注意力指标解码 → 游戏难度自动调整
2. **ML 驱动个性化**: SVR / XGBoost / MLP 预测最优难度参数，XGBoost 最优 (R²=0.98, RMSE=0.86)
3. **多任务游戏设计**: Unity SkiSport 滑雪游戏，多种任务模式
4. **双群体验证**: 25 ADHD + 25 对照，个性化后注意力额外提升 10%

## 📖 批读导航

| Section | 内容 |
|---------|------|
| [00 - Abstract](sections/00-abstract.md) | 待完成 |
| [01 - Introduction](sections/01-introduction.md) | 待完成 |

## 关键数字

| 指标 | 数值 |
|------|------|
| EEG 注意力提升 (ADHD) | +21.5% |
| EEG 注意力提升 (对照) | +7.85% |
| 行为指标整体提升 | +19% |
| XGBoost R² | 0.9826 |
| 被试数 | 50 (25 ADHD + 25 Control) |
| EEG 设备 | NeuroSky TGAM (单通道) |

---

> 📝 批读笔记待添加
