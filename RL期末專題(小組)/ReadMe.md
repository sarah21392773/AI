# 113學年度上學期 強化學習 DQN 期末專題
---

## 壹、問題敘述
- **環境**：`gymnasium CarRacing-v3`
- **遊戲目標**：駕駛汽車在灰色賽道格子上行駛並避免偏離賽道
- **狀態空間**：RGB 影像，大小 (96, 96, 3)
- **動作空間**  
  - **連續動作**：方向盤(-1~+1)、油門、煞車  
  - **離散動作**：不動、左轉、右轉、加速、煞車
- **獎勵機制**：  
  - 每幀：-0.1  
  - 踩到賽道格子：+1000/N  
  - 偏離賽道：-100（遊戲結束）
- **環境參數**：
  - `lap_complete_percent=y`：需通過 y% 格子才算完成一圈
  - `domain_randomize`：背景與賽道顏色是否隨機
  - `continuous=False`：切換至離散動作空間

---

## 貳、DQN 原理介紹
- **探索與利用 (Exploration vs Exploitation)**：透過 ε-greedy 在探索與利用間取得平衡
- **DQN (Deep Q-Network)**：以深度神經網路近似 Q 函數，解決 Q-Table 無法處理高維狀態的問題
- **Replay Buffer**：存儲過往經驗，隨機抽樣以避免樣本相關性
- **Fixed Q Target**：分為當前 Q 網路與目標 Q 網路，提升學習穩定性
- **Bootstrapping**：使用現有估計值結合未來回報來更新策略
- **Double DQN**：使用兩個網路避免 Q 值高估
- **Dueling DQN**：分解 Q 值為「狀態價值」與「優勢函數」，提升效率
- **Nature DQN**：採用 CNN 結構，並使用兩個獨立 Q 網路（PredictQ、TargetQ）

---

## 參、研究方法
- **演算法實作**  
  - DQN：以 CNN 作為 Q 網路（兩層卷積＋兩層全連接），並加入 Replay Buffer 與 Fixed Q Target  
  - Double DQN：將動作選擇與價值評估拆分為 Predict Q、Target Q  
  - Dueling DQN：將 Q 網路分為狀態價值函數 V(s) 與優勢函數 A(s,a)  
  - Nature DQN：使用三層卷積與兩層全連接，損失函數改為 MSELoss
- **參數設定**  
  - 折扣因子 γ = 0.95  
  - 探索率下限 ε_low = 0.05  
  - 學習率 lr = 0.00025  
  - 訓練回合數 N_EPISODES = 1000

---

## 肆、研究結果與分析
1. **探索率衰減實驗**  
   - 探索率下降越快，初期收斂越快，但後期震盪也較大
   - x=12 的收斂速度最快，但 4000 回合後差異趨緩
2. **不同演算法訓練比較**  
   - 效果排序：**Dueling DQN > DQN > Nature DQN > Double DQN**
   - DQN 在 1000 回合後出現 Overfitting
   - Double DQN 表現不佳，可能是訓練不足或不適合此環境
3. **不同演算法測試比較**  
   - 平均獎勵最佳：Dueling DQN  
   - 獎勵穩定性最佳：Double DQN（標準差最低）  
   - Nature DQN 表現中等，但震盪持續  
   - 總結：Dueling DQN 整體效果最佳，但波動較大

---

## 伍、討論與心得
- 更深入理解了 **DQN 及其改良演算法** 的運作機制
- 實驗中看見 **探索率衰減對訓練收斂的影響**
- 比較不同 DQN 演算法在 CarRacing 環境中的表現，了解其優缺點
- 目前僅嘗試基礎版本，未來可延伸至：
  - Prioritized Replay DQN
  - Rainbow DQN
  - 其他強化學習改進演算法

---

## 柒、參考資料
- Car Racing 環境：  
  [Gymnasium Car Racing](https://gymnasium.farama.org/environments/box2d/car_racing/)  
- Replay Buffer：  
  [Mini-Batches in RL - StackOverflow](https://stackoverflow.com/questions/53864434/mini-batches-in-rl)  
- Bootstrapping：  
  [N-Step Bootstrapping in RL - Medium](https://medium.com/@amit25173/n-step-bootstrapping-in-reinforcement-learning-e4f70f264933)  
- Nature DQN 論文：  
  [DeepMind DQN Paper](https://storage.googleapis.com/deepmind-media/dqn/DQNNaturePaper.pdf)  
- DQN 系列：  
  [博客文章 - DQN 系列介紹](https://www.cnblogs.com/jiangxinyang/p/10112381.html)  

---
📊 **結論**：  
在 CarRacing-v3 環境中，**Dueling DQN** 整體表現最佳，但仍需調整以減少後期震盪；**Double DQN** 表現最差；探索率衰減對訓練前期影響較大，但後期差異不明顯。
---
## 分工表

| 座號 | 姓名   | 負責工作                                                                 |
|------|--------|--------------------------------------------------------------------------|
| 05   | 吳苡柔 | 訓練 Nature DQN 模型、研究結果與分析、繪製圖表、研究方法、討論與心得      |
| 17   | 郭倢妤 | 撰寫問題敘述、DQN 原理介紹                                               |
| 21   | 黃品薰 | 訓練 DQN、Double DQN、Dueling DQN 模型、研究結果與分析、研究方法、討論與心得 |
