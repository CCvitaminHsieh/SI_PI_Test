在 HFSS 裡，**Wave Port、Lumped Port、Terminal Port** 不是「三種做同樣事情的按鈕」，而是 **三種完全不同的電磁邊界條件 (EM boundary conditions)**，對應不同的物理情境。  
要做封裝互連、BGA via、微帶線、stripline、connector transition，必須非常清楚三者的差異，否則 S11/S21 會錯、模態會錯、阻抗會錯。

下面用你最熟悉的「電磁波沿導體傳遞」的角度，把三種 port 的物理意義、使用場景、優缺點一次講清楚。

---

# Wave Port（模態埠）

### 適用：**傳輸線結構、有明確 cross-section 的線路**

例如：

- 微帶線（microstrip）
- 帶狀線（stripline）
- 差分對 ( differential pair)
- 同軸線 (coaxial cable)
- 封裝 substrate 上的 trace (trace embedded in substrate)
- PCB 上的走線 (trace on PCB)

### 物理意義

Wave Port 會在 port 面積上求解 **電磁模態（E/H 場型）**。  
它會找到：

- TE/TM/TEM 模態
- 差分模態 / 共模模態
- characteristic impedance
- propagation constant

也就是說：

> Wave Port 是真正的「電磁波入口」，會求解 Maxwell 方程式的模態解。

### 何時使用

- 你有一段「傳輸線」
- 你希望 HFSS 自動求出 Z0 (characteristic impedance)
- 你要做 S11/S21 的準確模態分析
- 你要分析差分對（odd/even mode）

### 優點

- 最準確的傳輸線模態
- 自動求阻抗
- 適合高頻（GHz）
- 適合封裝互連、PCB trace

### 缺點

- Port 面積必須包含完整 cross-section
- Port 不能太小
- 幾何複雜時不易放置

---

# Lumped Port（集總埠）

### 適用：**小尺寸、局部結構、via、晶片封裝、transition**

例如：

- BGA via
- Die bump
- IC pad
- 小型 transition（coax → microstrip）
- 走線中斷、gap、電容、電感
- 任何「幾何太小，無法放 wave port」的地方

### 物理意義

Lumped Port 是一個 **電壓源 + 電流路徑** 的邊界條件。  
它不求模態，而是：

- 在兩個導體之間施加電壓
- 讓 HFSS 求解電流
- 由此推算阻抗與 S 參數

也就是說：

> Lumped Port 是「電壓源」，不是「模態場」。

### 何時使用

- 幾何太小，wave port 放不下
- 你只需要局部激發
- via、pad、bump、chip-level interconnect
- 你要做封裝互連（package interconnect）

### 優點

- 可以放在很小的地方
- 適合封裝、via、晶片
- 不需要完整 cross-section

### 缺點

- 不會自動求模態
- 阻抗必須自己定義
- 不適合長傳輸線
- 不適合差分對模態分析

---

# Terminal Port（端子埠）

### 適用：**多導體傳輸線、connector、coax、cable**

例如：

- 多芯同軸線
- 多 pin connector（如 USB、HDMI、SATA）
- Ribbon cable
- Twinax
- 多導體封裝走線

### 物理意義

Terminal Port 是 Wave Port 的延伸：

- 仍然求模態
- 但允許你指定「哪兩個導體之間」是端子
- 適合多導體結構（multi-conductor）

也就是說：

> Terminal Port = Wave Port + 多導體端子定義能力。

### 何時使用

- 多導體傳輸線
- 需要定義特定 conductor pair
- Connector pin field
- Twinax / coax transition

### 優點

- 適合多導體
- 可定義端子組合
- 模態求解比 lumped port 更準確

### 缺點

- 幾何仍需足夠大
- 設定較複雜
- 不適合非常小的封裝 via

---

# 三種 Port 的比較（你做封裝互連時最重要的表格）

|Port 類型|求解方式|適用結構|阻抗來源|優點|缺點|
|---|---|---|---|---|---|
|**Wave Port**|模態求解|微帶線、stripline、差分對|自動求 Z0|最準確|需要完整 cross-section|
|**Lumped Port**|電壓源|via、pad、bump、小 transition|使用者定義|可放很小|不求模態、阻抗不精準|
|**Terminal Port**|多導體模態|coax、connector、twinax|自動求 Z0|適合多導體|幾何仍需足夠大|

---

# 封裝互連（package interconnect）該用哪一種？

你的工作內容（BGA → substrate → via → trace）通常是：

- **trace → Wave Port**
- **via / bump → Lumped Port**
- **connector / coax transition → Terminal Port**

這是業界標準做法。

---

如果你願意，我可以用一個「BGA via + microstrip trace」的例子，示範三種 port 要怎麼放、放在哪裡、會得到什麼樣的 S11/S21。