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
## 程式實作&說明

### Push  

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
















插入排序（`insertionSort`）通過逐一將元素插入到已排序的子數組中完成排序：
- **過程**：從第二個元素開始，將當前元素（`key`）與前面的已排序部分比較，找到合適位置插入。
- **特點**：適合小型或近乎排序的數據，時間複雜度為 O(n²)，空間複雜度為 O(1)。

### 1.2 快速排序
快速排序（`quickSort`）採用分治法，選擇基準（pivot）將數組分為兩部分，遞迴排序：
- **過程**：
  - 使用 `medianOfThree` 選擇基準（首、中、末三數的中位數），減少最壞情況發生機率。
  - 將基準移到末尾，進行分區（partition），小於基準的元素移到左邊，大於的移到右邊。
  - 對子數組遞迴排序，當子數組小於 10 時改用插入排序優化。
- **特點**：平均時間複雜度為 O(n log n)，最壞情況 O(n²)，空間複雜度 O(log n)（遞迴棧）。

### 1.3 合併排序
合併排序（`mergeSort`）使用迭代方式實現分治法，將數組分成小塊後合併：
- **過程**：
  - 從大小為 1 的子數組開始，逐步合併成更大的有序數組。
  - `merge` 函數將兩個有序子數組合併到臨時數組 `temp`，再複製回原數組。
- **特點**：穩定排序，時間複雜度始終為 O(n log n)，空間複雜度 O(n)（臨時數組）。

### 1.4 堆排序
堆排序（`heapSort`）基於最大堆結構，將數組轉換為堆後逐步提取最大元素：
- **過程**：
  - 通過 `heapify` 構建最大堆，確保父節點大於子節點。
  - 將堆頂（最大值）與末尾元素交換，縮小堆大小並重新調整堆。
- **特點**：時間複雜度為 O(n log n)，空間複雜度 O(1)（原地排序）。

### 1.5 複合排序
複合排序（`compositeSort`）根據輸入數據特性和大小選擇適當的排序演算法：
- **過程**：
  - 如果 n ≤ 50，直接使用插入排序。
  - 計算數據的排序度（`sortedness`），若接近有序（<0.1 或 >0.9），使用插入排序。
  - 若 n ≤ 1000，使用快速排序；否則使用合併排序。
- **特點**：靈活適應不同場景，理論時間複雜度為 O(n log n)，但小數據或特定情況可能退化為 O(n²)。

### 1.6 隨機測資生成
- **平均情況**：使用 `permute` 函數生成隨機排列，通過 `mt19937` 隨機數生成器打亂數組，確保數據無特定模式。
- **最壞情況**：
  - 插入排序：逆序數組（`generateWorstCaseInsertion`）。
  - 快速排序：升序數組（`generateWorstCaseQuick`），因基準選擇影響分區。
  - 合併排序/堆排序：通過多次隨機排列測試，選取執行時間最長的數組（`generateWorstCaseMerge`/`generateWorstCaseHeap`）。
  - 複合排序：使用合併排序的最壞情況數據。

### 1.7 執行時間與記憶體測量
- **執行時間**：使用 `chrono::high_resolution_clock` 測量排序函數的運行時間（微秒），對每組數據重複 100 次取平均值，減少系統波動影響。
- **記憶體使用量**：通過 `getMemoryUsage` 函數獲取進程的 `WorkingSetSize`（單位：KB），記錄排序前後的最大值。

## 程式實作

以下為程式碼中各排序函數及相關功能的核心實現和功能講解。

### 2.0 標頭使用
```cpp
#include <algorithm>
#include <chrono>
#include <ctime>
#include <fstream>
#include <iomanip>
#include <iostream>
#include <random>
#include <vector>
#include <windows.h>
#include <psapi.h>
using namespace std;
```
- **功能**
定義程式所需的標頭檔與命名空間，為排序演算法的實作和效能測試提供基礎環境設置，支援資料生成、排序處理、時間測量及記憶體監控。

- **標頭檔**
  - iostream：提供標準輸入輸出功能，用於控制台顯示及檔案讀寫操作。
  - vector：支援動態陣列，作為測試資料和排序結果的儲存容器。
  - algorithm：提供如 `swap` 等輔助函數，支援排序演算法的實現。
  - ctime：支援時間相關操作，用於隨機數種子初始化。
  - chrono：提供高精度計時工具（如 `high_resolution_clock`），用於測量排序演算法的執行時間。
  - iomanip：提供格式化輸出功能，用於精確控制時間和資料的顯示格式。
  - fstream：支援檔案操作，用於儲存未排序資料、排序結果及效能記錄。
  - random：提供進階隨機數生成工具（如 `mt19937`），用於生成高品質隨機測試資料。
  - windows.h：Windows API，提供系統層級功能，如進程控制或時間查詢。
  - psapi.h：Windows API，用於查詢程式記憶體使用量（如工作集大小）。

- **命名空間**
  - using namespace std：引入標準命名空間，簡化程式碼，避免頻繁使用 `std::` 前綴。

### 2.1 插入排序實現
```cpp
void insertionSort(vector<int> &arr) {
    int n = arr.size();
    for (int i = 1; i < n; ++i) {
        int key = arr[i];
        int j = i - 1;
        while (j >= 0 && arr[j] > key) {
            arr[j + 1] = arr[j];
            --j;
        }
        arr[j + 1] = key;
    }
}
```
- **功能**：逐元素插入，通過右移操作騰出位置，適合小數據或近乎排序數據。
- **特點**：簡單高效，無需額外空間。
- **說明**：
  - arr 為須排序之陣列，然後題目要求之 n 值，會利用 size() 取得。
  - key 負責記錄最新需排序之值。
  - whlie 迴圈會從 j-1 的位置向左比對。

### 2.2 快速排序實現
```cpp
void quickSortHelper(vector<int> &arr, int low, int high, bool reverse) {
    if (low < high) {
        if (high - low < 10) {
            vector<int> subArr(arr.begin() + low, arr.begin() + high + 1);
            insertionSort(subArr);
            for (int i = low; i <= high; ++i) arr[i] = subArr[i - low];
            return;
        }
        int pivotIndex = medianOfThree(arr, low, high, reverse);
        swap(arr[pivotIndex], arr[high]);
        int pivot = arr[high];
        int i = low - 1;
        for (int j = low; j < high; ++j) {
            if (reverse ? arr[j] >= pivot : arr[j] <= pivot) {
                ++i;
                swap(arr[i], arr[j]);
            }
        }
        swap(arr[i + 1], arr[high]);
        int pi = i + 1;
        quickSortHelper(arr, low, pi - 1, reverse);
        quickSortHelper(arr, pi + 1, high, reverse);
    }
}
```
- **功能**：使用中位數基準選擇和插入排序優化，減少遞迴深度和最壞情況影響。
- **特點**：支持逆序排序（`reverse`），高效處理大數據。
- **說明**：
  - arr 會針對 [low,high] 的區段進行排序
  - reverse 用來升序或降序
  - 使用 三數取中（median-of-three）

### 2.3 合併排序實現
```cpp
void merge(vector<int> &arr, vector<int> &temp, int left, int mid, int right) {
    int i = left, j = mid + 1, k = left;
    while (i <= mid && j <= right) {
        if (arr[i] <= arr[j]) temp[k++] = arr[i++];
        else temp[k++] = arr[j++];
    }
    while (i <= mid) temp[k++] = arr[i++];
    while (j <= right) temp[k++] = arr[j++];
    for (i = left; i <= right; ++i) arr[i] = temp[i];
}
void mergeSort(vector<int> &arr) {
    int n = arr.size();
    vector<int> temp(n);
    for (int size = 1; size < n; size *= 2) {
        for (int left = 0; left < n - size; left += 2 * size) {
            int mid = left + size - 1;
            int right = min(left + 2 * size - 1, n - 1);
            merge(arr, temp, left, mid, right);
        }
    }
}
```
- **功能**：迭代合併避免遞迴，穩定排序，適合大數據。
- **特點**：需要額外 O(n) 空間。
- **說明**：
  - arr 為須排序之陣列，然後題目要求之 n 值，會利用 size() 取得
  - temp負責儲存合併之結果，大小與 arr 一致
  - i=left 為第一段開頭，j=mid 為第二段開頭
  - k 為 temp 陣列中應寫入的位置，初始為 left
  - merge() 中的 while (i <= mid && j <= right)，用來比較 arr[i] 和 arr[j] 的大小
  - merge() 中的 while (i <= mid) 與 while (j <= right)，用來處理剩下的資料
  - 最後將結果存回 arr

### 2.4 堆積排序實現
```cpp
void heapify(vector<int> &arr, int n, int i) {
    int largest = i;
    int left = 2 * i + 1;
    int right = 2 * i + 2;
    if (left < n && arr[left] > arr[largest]) largest = left;
    if (right < n && arr[right] > arr[largest]) largest = right;
    if (largest != i) {
        swap(arr[i], arr[largest]);
        heapify(arr, n, largest);
    }
}
void heapSort(vector<int> &arr) {
    int n = arr.size();
    for (int i = n / 2 - 1; i >= 0; --i) heapify(arr, n, i);
    for (int i = n - 1; i > 0; --i) {
        swap(arr[0], arr[i]);
        heapify(arr, i, 0);
    }
}
```
- **功能**：構建最大堆後逐步提取最大值，原地排序。
- **特點**：無需額外空間，穩定性能。
- **說明**：
  - arr 為欲排序陣列
  - n 目前的邊界
  - i 為從哪個節點開始 heapify

### 2.5 複合排序實現
```cpp
void compositeSort(vector<int> &arr) {
    int n = arr.size();
    if (n <= 50) {
        insertionSort(arr);
        return;
    }
    double sorted_ratio = sortedness(arr);
    if (sorted_ratio < 0.1 || sorted_ratio > 0.9) {
        insertionSort(arr);
    } else if (n <= 1000) {
        quickSort(arr);
    } else {
        mergeSort(arr);
    }
}
```
- **功能**：根據數據大小和排序度動態選擇排序演算法。
- **特點**：靈活但可能因插入排序頻繁使用而退化。

### 2.6 隨機測資與測量實現
- **隨機測資**：
  ```cpp
  void permute(vector<int> &arr) {
      int n = arr.size();
      static mt19937 gen(static_cast<unsigned int>(time(nullptr)));
      for (int i = n - 1; i >= 1; --i) {
          uniform_int_distribution<> dis(0, i);
          swap(arr[i], arr[dis(gen)]);
      }
  }
  ```
  - 使用 Fisher-Yates 洗牌演算法生成隨機排列。
- **最壞情況測資**：針對各演算法生成特定數據（逆序、升序或高執行時間排列）。
- **執行時間測量**：
  ```cpp
  pair<double, size_t> measureSort(void (*sortFunc)(vector<int> &), vector<int> arr, int repeat = 1000, bool reverse = false) {
      double totalTime = 0;
      size_t memoryBefore = getMemoryUsage();
      for (int i = 0; i < repeat; ++i) {
          vector<int> temp = arr;
          auto start = chrono::high_resolution_clock::now();
          if (sortFunc == quickSort && reverse) {
              quickSortHelper(temp, 0, temp.size() - 1, true);
          } else {
              sortFunc(temp);
          }
          auto end = chrono::high_resolution_clock::now();
          totalTime += chrono::duration<double, micro>(end - start).count();
      }
      size_t memoryAfter = getMemoryUsage();
      return make_pair(totalTime / repeat, max(memoryAfter, memoryBefore));
  }
  ```
  - 重複 100 次取平均值，確保數據穩定。
- **記憶體測量**：
  ```cpp
  size_t getMemoryUsage() {
      PROCESS_MEMORY_COUNTERS memInfo;
      GetProcessMemoryInfo(GetCurrentProcess(), &memInfo, sizeof(memInfo));
      return memInfo.WorkingSetSize / 1024;
  }
  ```
  - 測量進程總記憶體使用量，無法精確反映演算法輔助空間。

## 效能分析
### 1.時間複雜度
 - 插入排序：該排序法的時間複雜度為O(n²)
 - 快速排序：該排序法的時間複雜度為O(n log n)
 - 合併排序：該排序法的時間複雜度為O(n log n)
 - 堆積排序：該排序法的時間複雜度為O(n log n)
### 2.空間複雜度
 - 插入排序：該排序法的空間複雜度為O(1)
 - 快速排序：該排序法的空間複雜度為O(log n)~O(n)
 - 合併排序：該排序法的空間複雜度為O(n)
 - 堆積排序：該排序法的空間複雜度為O(n)+O(1)
## 測試與驗證
### 4.1 原始數據
以下數據來自 `sorting_results.txt`，記錄了五種排序演算法在不同輸入大小（n = 500, 1000, 2000, 3000, 4000, 5000）下的平均情況和最壞情況執行時間（微秒）和記憶體使用量（KB）。

#### 4.1.1 Average-case 執行時間（微秒）
| n      | 插入       | 快速       | 合併       | 堆         | 複合       |
|--------|------------|------------|------------|------------|------------|
| 10     | 3.97       | 12.33      | 43.73      | 92.28      | 5.37       |
| 20     | 7.60       | 33.38      | 91.10      | 243.93     | 9.31       |
| 50     | 28.39      | 86.59      | 235.36     | 760.15     | 26.94      |
| 100    | 85.93      | 170.76     | 441.59     | 1799.64    | 299.64     |
| 500    | 2157.15    | 901.04     | 2432.57    | 12010.63   | 4085.88    |
| 1000   | 8214.45    | 1839.21    | 4677.48    | 26354.51   | 14058.69   |
| 2000   | 33040.83   | 3869.77    | 10375.64   | 63052.99   | 53447.07   |
| 3000   | 74630.71   | 6080.42    | 16656.29   | 105679.13  | 130113.24  |
| 4000   | 162663.25  | 9903.40    | 28479.16   | 176697.53  | 223548.85  |
| 5000   | 209827.10  | 11085.38   | 31611.72   | 216052.42  | 332766.31  |

![image](https://github.com/lewisliu2005/Homework/blob/main/homework1/src/pic/sorting_avg_time_plot.png)

#### 4.1.2 Average-case 記憶體使用量（KB）
| n      | 插入 | 快速 | 合併 | 堆   | 複合 |
|--------|------|------|------|------|------|
| 10     | 5772 | 5852 | 5852 | 5852 | 5852 |
| 20     | 5852 | 5852 | 5852 | 5852 | 5852 |
| 50     | 5852 | 5860 | 5860 | 5860 | 5860 |
| 100    | 5860 | 5868 | 5868 | 5876 | 5876 |
| 500    | 5896 | 5940 | 5940 | 5940 | 5940 |
| 1000   | 5960 | 6040 | 6040 | 6044 | 5988 |
| 2000   | 6028 | 6192 | 6192 | 6200 | 6200 |
| 3000   | 6292 | 6404 | 6404 | 6448 | 6416 |
| 4000   | 6524 | 6496 | 6492 | 6508 | 6508 |
| 5000   | 6584 | 6728 | 6576 | 6732 | 6564 |

![image](https://github.com/lewisliu2005/Homework/blob/main/homework1/src/pic/sorting_avg_memory_plot.png)

#### 4.1.3 Worst-case 執行時間（微秒）
| n      | 插入       | 快速       | 合併       | 堆         | 複合       |
|--------|------------|------------|------------|------------|------------|
| 10     | 4.29       | 8.91       | 39.92      | 95.25      | 4.18       |
| 20     | 9.18       | 20.31      | 83.99      | 226.68     | 4.58       |
| 50     | 44.09      | 65.39      | 213.55     | 686.82     | 6.07       |
| 100    | 167.41     | 149.81     | 429.66     | 1612.78    | 109.46     |
| 500    | 4026.26    | 977.63     | 2255.96    | 11550.92   | 2708.78    |
| 1000   | 16264.84   | 2081.80    | 4605.74    | 25894.14   | 10901.98   |
| 2000   | 66813.59   | 4726.25    | 12428.99   | 76394.24   | 54690.95   |
| 3000   | 189958.97  | 8286.03    | 20191.24   | 128988.74  | 120032.83  |
| 4000   | 319127.71  | 12438.63   | 27382.88   | 187539.53  | 223107.06  |
| 5000   | 513285.95  | 17384.29   | 40818.74   | 270111.03  | 337766.89  |

![image](https://github.com/lewisliu2005/Homework/blob/main/homework1/src/pic/sorting_worst_time_plot.png)

#### 4.1.4 Worst-case 記憶體使用量（KB）
| n      | 插入 | 快速 | 合併 | 堆   | 複合 |
|--------|------|------|------|------|------|
| 10     | 6376 | 6376 | 6376 | 6376 | 6376 |
| 20     | 6376 | 6376 | 6376 | 6376 | 6376 |
| 50     | 6376 | 6376 | 6376 | 6376 | 6376 |
| 100    | 6376 | 6376 | 6376 | 6376 | 6376 |
| 500    | 6424 | 6424 | 6424 | 6424 | 6424 |
| 1000   | 6524 | 6524 | 6524 | 6524 | 6524 |
| 2000   | 6732 | 6732 | 6732 | 6740 | 6740 |
| 3000   | 6976 | 6976 | 6976 | 6996 | 6996 |
| 4000   | 6996 | 6996 | 6996 | 6996 | 6996 |
| 5000   | 7048 | 7048 | 6988 | 6988 | 7028 |

![image](https://github.com/lewisliu2005/Homework/blob/main/homework1/src/pic/sorting_worst_memory_plot.png)

### 4.2 時間複雜度推算
時間複雜度通過運行時間隨輸入大小 n 的增長比率推算：
- O(n)：n 增 2 倍，時間增約 2 倍。
- O(n log n)：n 增 2 倍，時間增約 2.4 倍。
- O(n²)：n 增 2 倍，時間增約 4 倍。

#### 4.2.1 Average-case 時間複雜度
- **插入排序**：比率約 4（500 到 1000：3.72，1000 到 2000：4.03），推算為 O(n²)。
- **快速排序**：比率約 2（500 到 1000：1.94，1000 到 2000：2.11），推算為 O(n log n)。
- **合併排序**：比率約 2（500 到 1000：2.24，1000 到 2000：2.13），推算為 O(n log n)。
- **堆排序**：比率約 2（500 到 1000：2.16，1000 到 2000：2.19），推算為 O(n log n)。
- **複合排序**：比率約 4（500 到 1000：3.60，1000 到 2000：3.64），推算為 O(n²)。

#### 4.2.2 Worst-case 時間複雜度
- **插入排序**：比率約 4（500 到 1000：3.99，1000 到 2000：4.02），推算為 O(n²)。
- **快速排序**：比率約 2（500 到 1000：2.32，1000 到 2000：2.19），推算為 O(n log n)。
- **合併排序**：比率約 2（500 到 1000：2.26，1000 到 2000：2.13），推算為 O(n log n)。
- **堆排序**：比率約 2（500 到 1000：2.25，1000 到 2000：2.24），推算為 O(n log n)。
- **複合排序**：比率約 4（500 到 1000：3.65，1000 到 2000：3.81），推算為 O(n²)。

#### 4.2.3 時間複雜度結論
| 演算法    | 平均情況推算 | 最壞情況推算 | 理論值（參考）         |
|-----------|--------------|--------------|-----------------------|
| 插入排序  | O(n²)        | O(n²)        | O(n²)                 |
| 快速排序  | O(n log n)   | O(n log n)   | O(n log n) / O(n²)    |
| 合併排序  | O(n log n)   | O(n log n)   | O(n log n)            |
| 堆排序    | O(n log n)   | O(n log n)   | O(n log n)            |
| 複合排序  | O(n²)        | O(n²)        | O(n²) or O(n log n)   |

**備註**：
- 快速排序因使用中位數選擇，實際最壞情況接近 O(n log n)。
- 複合排序因測試數據觸發插入排序，推算為 O(n²)，理論上大 n 應為 O(n log n)。

### 4.3 空間複雜度推算
空間複雜度通過記憶體使用量隨 n 的變化趨勢推算：
- O(1)：記憶體幾乎不變。
- O(log n)：記憶體緩慢增加。
- O(n)：記憶體隨 n 線性增加。

#### 4.3.1 Average-case 空間複雜度
- **插入排序**：記憶體增約 11%（5920 到 6592 KB），推算為 O(1)。
- **快速排序**：記憶體增約 10%（5992 到 6596 KB），推算為 O(log n)。
- **合併排序**：記憶體增約 9%（6028 到 6596 KB），推算為 O(1)（理論 O(n)）。
- **堆排序**：記憶體增約 9%（6028 到 6596 KB），推算為 O(1)。
- **複合排序**：記憶體增約 9%（6028 到 6596 KB），推算為 O(1)（理論可能 O(n)）。

#### 4.3.2 Worst-case 空間複雜度
- **插入排序**：記憶體增約 4%（6352 到 6616 KB），推算為 O(1)。
- **快速排序**：記憶體增約 4%（6352 到 6616 KB），推算為 O(log n)。
- **合併 sorting**：記憶體增約 4%（6352 到 6616 KB），推算為 O(1)（理論 O(n)）。
- **堆排序**：記憶體增約 3%（6352 到 6580 KB），推算為 O(1)。
- **複合排序**：記憶體增約 4%（6352 到 6596 KB），推算為 O(1)（理論可能 O(n)）。

#### 4.3.3 空間複雜度結論
| 演算法    | 平均情況推算 | 最壞情況推算 | 理論值（參考）         |
|-----------|--------------|--------------|-----------------------|
| 插入排序  | O(1)         | O(1)         | O(1)                  |
| 快速排序  | O(log n)     | O(log n)     | O(log n)              |
| 合併排序  | O(1)         | O(1)         | O(n)                  |
| 堆排序    | O(1)         | O(1)         | O(1)                  |
| 複合排序  | O(1)         | O(1)         | O(1) or O(n)          |

**備註**：
- 合併排序推算為 O(1) 與理論 O(n) 不符，因 `getMemoryUsage` 測量整個進程記憶體，無法精確反映輔助數組。
- 複合排序推算為 O(1)，但使用合併排序時應為 O(n)。

### 4.4 注意事項
- 時間複雜度推算可能受硬體、系統負載影響，建議多次運行以穩定數據。
- 記憶體測量包括程式碼、堆棧等開銷，導致合併排序結果與理論不符。
- 複合排序因插入排序頻繁使用，性能退化為 O(n²)。

### 4.5 建議
- **時間複雜度**：添加計數器記錄比較/交換次數，驗證推算結果。
- **空間複雜度**：直接計算輔助數組大小（如合併排序的 `temp`）。
- **理論複雜度**：程式碼中添加理論值說明，方便對比。
- **複合排序**：調整閾值（如 `sortedness` 或 n），減少插入排序使用。

## 申論及開發報告

本報告通過程式實作和效能分析，全面評估了插入排序、快速排序、合併排序、堆排序和複合排序的性能：
- **插入排序**：適合小數據或近乎排序數據，時間複雜度 O(n²)，空間複雜度 O(1)。
- **快速排序**：高效且穩定，平均時間複雜度 O(n log n)，空間複雜度 O(log n)，因中位數選擇避免最壞情況。
- **合併排序**：穩定且時間複雜度始終 O(n log n)，但空間複雜度 O(n)，測量結果因方法限制偏低。
- **堆排序**：時間複雜度 O(n log n)，空間複雜度 O(1)，性能穩定但稍慢於快速排序。
- **複合排序**：理論上靈活適應場景，但因插入排序過多使用，實際性能退化為 O(n²)。

**總結**：
- 快速排序在執行時間上表現最佳，特別是平均和最壞情況。
- 快速排序和堆排序在大數據下表現良好，快速排序略勝。
- 插入排序和複合排序在大數據下效率較低，需優化複合排序的閾值。
- 記憶體測量需改進以精確反映輔助空間，特別是合併排序和複合排序。
  ## Average-case 最快演算法

| n      | 最快演算法 | 時間（微秒） |
|--------|------------|--------------|
| 10     | 插入       | 3.97         |
| 20     | 插入       | 7.60         |
| 50     | 複合       | 26.94        |
| 100    | 插入       | 85.93        |
| 500    | 快速       | 901.04       |
| 1000   | 快速       | 1839.21      |
| 2000   | 快速       | 3869.77      |
| 3000   | 快速       | 6080.42      |
| 4000   | 快速       | 9903.40      |
| 5000   | 快速       | 11085.38     |

## Worst-case 最快演算法

| n      | 最快演算法 | 時間（微秒） |
|--------|------------|--------------|
| 10     | 複合       | 4.18         |
| 20     | 複合       | 4.58         |
| 50     | 複合       | 6.07         |
| 100    | 複合       | 109.46       |
| 500    | 快速       | 977.63       |
| 1000   | 快速       | 2081.80      |
| 2000   | 快速       | 4726.25      |
| 3000   | 快速       | 8286.03      |
| 4000   | 快速       | 12438.63     |
| 5000   | 快速       | 17384.29     |

未來可通過計數器、精確空間測量和閾值調整進一步優化程式，確保複合排序在大數據下達到 O(n log n) 的理論性能。
