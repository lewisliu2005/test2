# 41243221
# 41243250

### 組員名單
- 林榆傑 41243226
- 劉宗修 41243250

## Max / min Heap 作業 2_1

## 題目要求

撰寫一個 C++ 抽象類，類似於 ADT 5.2，來定義最小優先佇列（MinPQ）。然後撰寫一個 C++ 類 MinHeap，繼承這個抽象類，並實現 MinPQ 中的所有虛擬函數。每個函數的時間複雜度應與對應的最大堆（MaxHeap）函數相同。

## 解題說明

### 架構

首先，我們先將ADT 5.2 圖中的函式都實做出來:

- MinPQ() 建構子
- ~MinPQ() 解構子
- IsEmpty()
- Top()
- Push()
- Pop()

還多加了一些保護函數如:heap、heapSize、capacity

以下是MinPQ的框架定義
```cpp
template <class T>
class MinPQ {
public:
    MinPQ(int theCapacity = 10) {}
    virtual ~MinPQ() {}
    virtual bool IsEmpty() const = 0;
    virtual const T& Top() const = 0;
    virtual void Push(const T&) = 0;
    virtual void Pop() = 0;
protected:
    T* heap = nullptr;
    int heapSize = 0;
    int capacity = 0;
};
```
以下是MaxPQ的框架定義
```cpp
template <class T>
class MaxPQ {
public:
    MaxPQ(int theCapacity = 10) {}
    virtual ~MaxPQ() {}
    virtual bool IsEmpty() const = 0;
    virtual const T& Top() const = 0;
    virtual void Push(const T&) = 0;
    virtual void Pop() = 0;
protected:
    T* heap = nullptr;
    int heapSize = 0;
    int capacity = 0;
};
```
## 程式實作&說明
### MinPQ(建構子)
```cpp
    MinHeap(int theCapacity = 10) {
            if (theCapacity < 1) throw "Capacity must be >= 1";
            this->capacity = theCapacity;
            this->heapSize = 0;
            this->heap = new T[this->capacity + 1];
        }
```
建構函式 MinHeap(int theCapacity = 10)

- 檢查容量是否小於 1，若是則output異常。
- 初始化 capacity、heapSize = 0，並動態分配大小為 capacity + 1 的陣列。

### ~MinPQ(解構子)
```cpp
    ~MinHeap() override {
            delete[] this->heap;
        }
```
解構函式 ~MinHeap()

- 釋放動態分配的 heap 陣列記憶體。

### IsEmpty
```cpp
      bool IsEmpty() const override {
        return this->heapSize == 0;
    }
```
函式 IsEmpty()

- return heapSize == 0，檢查Heap是否為空。

### Top
```cpp
      const T& Top() const override {
        if (IsEmpty()) throw "Heap is empty. Cannot get top.";
        return this->heap[1];
    }
```
函式 Top

- 若堆空則丟出異常，否則返回根節點（最小值）。

### Push(MinHeap)

```cpp
void Push(const T& e) override {
        if (this->heapSize == this->capacity) {
            ChangeSize1D(this->heap, this->capacity, 2 * this->capacity);
            this->capacity *= 2;
        }
        int currentNode = ++this->heapSize;
        while (currentNode != 1 && this->heap[currentNode / 2] > e) {
            this->heap[currentNode] = this->heap[currentNode / 2];
            currentNode /= 2;
        }
        this->heap[currentNode] = e;
    }
```
這段程式碼是 `MinHeap` 類中的 `Push` 方法，用於將元素 `e` 插入最小堆（Min Heap）。

- **功能**：將新元素插入Heap中，保持最小堆性質（父節點小於子節點）。
- **步驟**：
  1. 如果堆滿（`heapSize == capacity`），呼叫 `ChangeSize1D` 擴展陣列容量至兩倍。
  2. 將新元素插入heap最底位置（`++heapSize`），從底部開始上浮。
  3. 比較當前節點與父節點（`heap[currentNode / 2]`），若父節點大於新元素（`> e`），則交換，繼續上浮。
  4. 最後將新元素放入正確位置。
- **時間複雜度**：O(log n)，因為最壞情況下需要從底部上浮到根部。

這是實現MinHeap Push 的核心邏輯。
### Pop(MinHeap)

```cpp
void Pop() override {
        if (IsEmpty()) throw "Heap is empty. Cannot delete.";
        this->heap[1] = this->heap[this->heapSize--];
        int currentNode = 1;
        int child = 2;
        T lastE = this->heap[this->heapSize + 1];
        while (child <= this->heapSize) {
            if (child < this->heapSize && this->heap[child] > this->heap[child + 1]) child++;
            if (lastE <= this->heap[child]) break;
            this->heap[currentNode] = this->heap[child];
            currentNode = child;
            child *= 2;
        }
        this->heap[currentNode] = lastE;
    }
```
這段程式碼是 `MinHeap` 類中的 `Pop` 方法，用於從最小堆中移除並返回最小元素（根節點）。

- **功能**：刪除堆頂最小元素，並保持最小堆性質（父節點小於子節點）。
- **步驟**：
  1. 如果堆空，丟出異常。
  2. 將最後一個元素移至根節點（索引 1），並減少 `heapSize`。
  3. 從根節點開始下沉，比較與較小子節點（`child`），若大於子節點則交換。
  4. 繼續下沉直到滿足最小堆條件或到達葉節點。
- **時間複雜度**：O(log n)，因為最壞情況下需要從根部下沉到葉節點。

這是實現MinHeap Pop的核心邏輯。

以上是MinHeap的實作，以下再來快速介紹MaxHeap的程式實作(建構子、解構子、IsEmpty、Top函式和MinHeap相同故不再次解釋):
### Push(MaxHeap)
```cpp
void Push(const T& e) override {
        if (this->heapSize == this->capacity) {
            ChangeSize1D(this->heap, this->capacity, 2 * this->capacity);
            this->capacity *= 2;
        }
        int currentNode = ++this->heapSize;
        while (currentNode != 1 && this->heap[currentNode / 2] < e) {
            this->heap[currentNode] = this->heap[currentNode / 2];
            currentNode /= 2;
        }
        this->heap[currentNode] = e;
    }
```
這段程式碼是 MaxHeap 的 Push 方法，與 MinHeap 的 Push 方法的主要差異在於
    
```cpp
while (currentNode != 1 && this->heap[currentNode / 2] < e)
```
中的比較運算符從 <（MaxHeap）改為 >（MinHeap）
這決定了堆的排序方向。這導致 MaxHeap 將較大元素上浮，而 MinHeap 將較小元素上浮。
### Pop(MaxHeap)
```cpp
void Pop() override {
        if (IsEmpty()) throw "Heap is empty. Cannot delete.";
        this->heap[1] = this->heap[this->heapSize--];
        int currentNode = 1;
        int child = 2;
        T lastE = this->heap[this->heapSize + 1];
        while (child <= this->heapSize) {
            if (child < this->heapSize && this->heap[child] < this->heap[child + 1]) child++;
            if (lastE >= this->heap[child]) break;
            this->heap[currentNode] = this->heap[child];
            currentNode = child;
            child *= 2;
        }
        this->heap[currentNode] = lastE;
    }
```
這段程式碼是 MaxHeap 的 Pop 方法，與 MinHeap 的 Pop 方法的主要差異在於：
```cpp
if (child < this->heapSize && this->heap[child] < this->heap[child + 1]) child++;
if (lastE >= this->heap[child]) break;
```
中的比較運算符從 >（MaxHeap，選較大子節點）改為 <（MinHeap，選較小子節點），以及從 <= 改為 >=，決定了堆的排序方向。
這導致 MaxHeap 下沉時選擇較大子節點，而 MinHeap 選擇較小子節點。

## 測試與驗證
### MaxHeap
![chart1](https://github.com/lewisliu2005/test2/blob/main/src/image/Maxheap1.png)

首先先決定要輸入多少數字(變數num)，並設定堆的初始陣列大小。

接著cmd便會顯示樹(樹的根在最左邊，左樹根->右樹底)

![chart1](https://github.com/lewisliu2005/test2/blob/main/src/image/Max2.png)

第一次Pop，Pop完後會再輸出一次現在樹的樣子

![chart1](https://github.com/lewisliu2005/test2/blob/main/src/image/Max3.png)

第二、三次Pop

![chart1](https://github.com/lewisliu2005/test2/blob/main/src/image/Max4.png)

第四、五、六次Pop

![chart1](https://github.com/lewisliu2005/test2/blob/main/src/image/Max5.png)

第七、八、九次Pop

### MinHeap

![chart1](https://github.com/lewisliu2005/test2/blob/main/src/image/Min1.png)

一樣先決定要輸入多少數字(變數num)，並設定堆的初始陣列大小。

接著cmd便會顯示樹(樹的根在最左邊，左樹根->右樹底)

![chart1](https://github.com/lewisliu2005/test2/blob/main/src/image/Min2.png)

第一、二次Pop，Pop完後會再輸出一次現在樹的樣子

![chart1](https://github.com/lewisliu2005/test2/blob/main/src/image/Min3.png)

第三、四、五次Pop

![chart1](https://github.com/lewisliu2005/test2/blob/main/src/image/Min4.png)

第六、七、八、九次Pop















