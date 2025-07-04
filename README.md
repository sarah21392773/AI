# RL 期末專題
---

## 壹、問題敘述

### ● 選用環境

使用 Pygame 自製的「貪吃蛇」遊戲，改造成類似 gymnasium 格式的強化學習環境，新增了 `reset()`、`play_step(action)`、`is_collision()` 等函式。

### ● 遊戲目標

操控貪吃蛇在不碰撞的情況下吃紅點以獲高分。

### ● 狀態空間

11 維布林值向量（使用 one-hot encoding）代表：

* 危險判斷：`danger_straight`, `danger_right`, `danger_left`
* 當前方向：`move_left`, `move_right`, `move_up`, `move_down`
* 食物方向：`food_left`, `food_right`, `food_up`, `food_down`

例如：\[0, 0, 0, 0, 1, 0, 0, 0, 1, 0, 1]

### ● 動作空間

使用 one-hot encoding 表示三種離散動作：

* \[1, 0, 0]：直行（什麼都不做）
* \[0, 1, 0]：向右轉（順時針）
* \[0, 0, 1]：向左轉（逆時針）

### ● 獎勵機制

| 情況    | 獎勵值 | 說明      |
| ----- | --- | ------- |
| 吃到食物  | +10 | 鼓勵      |
| 撞牆或自撞 | -10 | 處罰      |
| 其他    | 0   | 正常移動不加分 |

### ● 死亡條件

* 撞牆
* 撞自己
* 時間 > 100 x 蛇長

---

## 貳、DQN原理介紹

### ● 探索與利用

使用 \$ε\$-greedy 策略平衡探索與利用。

### ● DQN

深度 Q 網路（Deep Q Network）使用神經網路近似 Q 函數，取代傳統 Q-Table。

### ● Replay Buffer

儲存 `(state, action, reward, next_state)` 經驗以提升訓練效率，打亂相關性避免過擬合。

### ● Fixed Q Target

使用兩套網路（current, target）穩定 Q 值學習。

### ● Bootstrapping

用估計去更新同類估計，結合當下與未來的資訊改進策略。

### ● Double DQN

分離動作選擇與評估，降低 Q 值過高估計問題。

### ● Dueling DQN

將 Q 值分為：

* 狀態價值 V(s)
* 優勢值 A(s,a)
  使用 \$Q(s, a) = V(s) + (A(s,a) - \frac{1}{|A|} \sum A(s,a'))\$

### ● Prioritized Experience Replay (PER)

根據 TD 誤差進行經驗抽樣，強化學習效率。

### ● Rainbow-lite DQN

結合 Double DQN、Dueling DQN 和 PER。

---

## 參、研究方法

### 1. 各 DQN 架構

#### ● DQN

* 全連接層：11 → 128 → 64 → 3（對應 3 個動作）
* 使用 Replay Buffer 與 Fixed Q Target

#### ● Double DQN

* 架構同 DQN
* 使用 model 選擇動作，target\_model 計算 Q 值

#### ● Dueling DQN

* 架構分為：Value 分支（輸出 V）、Advantage 分支（輸出 A）
* 合併公式如前所述

#### ● PER DQN

* 架構同 DQN
* 使用 TD error 作為抽樣依據，加上 IS 修正權重

#### ● Rainbow-lite DQN

* 架構綜合上述三者

### 2. 模型訓練比較

* 所有模型共通參數：

  * \$γ=0.9\$
  * lr=0.001
  * eps\_low=0.05
  * N\_EPISODES=1000

---

## 肆、研究結果與分析

| 演算法          | 最高分   | 最終收斂  | 特性描述             |
| ------------ | ----- | ----- | ---------------- |
| DQN          | \~150 | \~125 | 緩慢穩定             |
| Double DQN   | \~150 | \~125 | 初期佳，後期不穩         |
| Dueling DQN  | \~135 | \~125 | 變異大，效果最差         |
| PER DQN      | \~160 | \~140 | 穩定最佳模型           |
| Rainbow-lite | \~150 | \~125 | 初期成長快，後期與 DQN 相近 |

* 所有模型在 600 回合後表現下降，疑似過擬合。

---

## 伍、討論與心得

1. 更具體理解 DQN 相關理論與實作。
2. 環境設計需避免讓 agent 原地打轉，但過於限制可能造成後期困難。
3. 仿 gymnasium 設計仍待補全（如 render 函式）。
4. Rainbow-lite 效果不如預期，未來可試完整 Rainbow DQN 或調參。

---

## 陸、參考資料

* StackOverflow: Mini-Batches in RL. [https://stackoverflow.com/questions/53864434/mini-batches-in-rl](https://stackoverflow.com/questions/53864434/mini-batches-in-rl)
* Medium: N-Step Bootstrapping in RL. [https://medium.com/@amit25173/n-step-bootstrapping-in-reinforcement-learning-e4f70f264933](https://medium.com/@amit25173/n-step-bootstrapping-in-reinforcement-learning-e4f70f264933)
* 博客園: Prioritized Replay DQN. [https://www.cnblogs.com/pinard/p/9797695.html](https://www.cnblogs.com/pinard/p/9797695.html)
* 博客園: DQN 系列介紹. [https://www.cnblogs.com/jiangxinyang/p/10112381.html](https://www.cnblogs.com/jiangxinyang/p/10112381.html)
* 掘金: Rainbow DQN. [https://juejin.cn/post/7327723045287559205](https://juejin.cn/post/7327723045287559205)
* CSDN: 自定義強化學習環境. [https://blog.csdn.net/weixin\_41434829/article/details/139204435](https://blog.csdn.net/weixin_41434829/article/details/139204435)
* 成果影片: [https://drive.google.com/file/d/14i73H42KfBccfzgJtc4\_VZZU\_HrZJ6r3/view?usp=sharing](https://drive.google.com/file/d/14i73H42KfBccfzgJtc4_VZZU_HrZJ6r3/view?usp=sharing)
