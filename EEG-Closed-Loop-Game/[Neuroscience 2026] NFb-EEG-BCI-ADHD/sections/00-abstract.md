# 00 - Abstract

## 原文

> The integration of game-based cognitive training with electroencephalography (EEG)-based brain-computer interaction (BCI) has demonstrated potential for enhancing attention among individuals with attention-deficit hyperactivity disorder (ADHD). However, existing systems often lack adaptive difficulty regulation and rely solely on single-modal assessments, thereby limiting personalization and sustained engagement. This study developed and assessed an adaptive, multi-task EEG–BCI training system that combines real-time neurofeedback with machine learning-driven customization to bolster attentional capabilities. Fifty participants (25 with ADHD and 25 controls) completed attention-enhancement sessions utilizing SkiSport, a Unity-based skiing game that adjusts difficulty levels according to EEG-derived attention metrics obtained from the NeuroSky TGAM sensor. Support Vector Regression, XGBoost, and Multi-Layer Perceptron models were trained on behavioral and EEG data to predict optimal difficulty parameters. Attention and behavioural metrics were compared before and after personalisation. The findings indicated that EEG attention scores increased by an average of 15% (7.85% in controls, 21.5% in ADHD participants). The adaptive multi-task games yielded an additional 10% increase following personalization. Behavioral indices on reaction accuracy, game score, and completion time showed an overall improvement of 19%. XGBoost achieved the highest predictive accuracy on a held-out test set (R² value of 0.9826, RMSE of 0.8560, and MAE of 0.6417) for within-subject, window-level attention prediction. The proposed EEG–BCI game facilitated short-term enhancements in attention-related metrics among individuals with ADHD. The incorporation of machine learning-driven personalization into serious games offers a scalable, non-pharmacological strategy for short-term cognitive training and attentional modulation.

## 批注

### 👍 优点
- 结构完整，问题-方法-结果-结论都有覆盖
- 末尾用了 "within-subject, window-level" 限定 R² 的适用范围，算是做了 disclaimer

### ⚠️ 问题

1. **R² = 0.9826 放在摘要里有误导性**
   - 这是 window-level within-subject 的结果，不是跨被试泛化能力
   - 时间相邻的 5 秒窗口高度自相关，导致 R² 被高估
   - 读者如果不仔细看方法部分，很容易以为模型泛化能力很强

2. **"15% increase in EEG attention scores" 基于黑盒指标**
   - NeuroSky eSense 注意力指数是专有算法，不可独立验证
   - 摘要里说得好像是标准脑电指标，实际上科学价值有限

3. **"short-term enhancements" 是准确的**
   - 至少没有过度宣称长期康复效果，这一点值得肯定
