---
title: Basic Data Structure with Python 3
tag: data-structure
---

## List, Tuple, Set, Dict
### Giới thiệu
Đây là 4 kiểu dữ liệu cơ bản trong python  
1. **list**: kiểu danh sách động, có thể thêm, xoá, thay đổi phần tử bất kì trong list
2. **tuple**: kiểu danh sách tĩnh, không thể thêm, xoá hay thay đổi các phần tử trong tuple, độ dài tuple là cố định
3. **set**: kiểu tập hợp, các phần tử trong set khác nhau đôi một, hỗ trợ các phép toán trên tập hợp
4. **dict**: kiểu từ điển, dạng key-value, tương ứng với cấu trúc dữ liệu *hash*

### Stack và Queue với list
Có thể dùng list như một stact với 2 hàm:
* **append(x)**: thêm phần tử x vào đỉnh stack
* **pop()**: lấy phần tử ở đỉnh stack

Sử dụng list như một queue:
* **insert(ix, x)**: thêm phần tử x vào vị trí ix, sử dụng ix=0 để thêm x vào head của queue
* **pop()**: lấy phần tử ở tail của queue

**Chú ý** Không nên sử dụng list như một queue vì thao tác insert có độ phức tạp **O(n)**, thay vào đó nên dùng `dequeue` (Double-Ended Queue)

## Double-End Queue
### Giới thiệu
`dequeue` nằm trong thư viện `collections`. 
Có thể hiểu đây là một queue 2 đầu (giống với double-head linked list), có thể dùng làm stack hoặc queue. `dequeue` bao gồm các thao tác lên 2 đầu như chèn hoặc xoá ở 2 đầu.  
Một số hàm trong `dequeue`:
1. **append(x)**: thêm vào bên phải (cuối)
2. **appendleft(x)**: thêm vào bên trái (đầu)
3. **pop()**: pop từ bên phải
4. **popleft()**: pop từ bên trái
5. **rotate(n=1)**: nếu n>0 thì xoay phải, n<0 thì xoay trái
6. **reverse()**: nghịch đảo dequeu
7. **insert(i, x)**: chèn x tại vị trí i


## Priority Queue (Heap)
### Giới thiệu
Python cung cấp thư viện `heapq` để thao tác với heap. Với thư viện này ta có thể tạo một **min heap** một cách dễ dàng.
Tuy nhiên `heapq` chỉ có thể thao tác với **min heap**, do đó nếu cần sử dụng **max heap**, cần phải inverse key nên khá bất tiện.
Về cơ bản, `heapq` không phải là một cấu trúc dữ liệu mà nó cung cấp các hàm để thao tác trên **min heap**. Các hàm trong `heapq` thường có 2 tham số gồm:  
* heap: đây thường là một list để lưu trữ các phần tử của heap  
* item: thường là một tuple, các phần tử trong tuple phải có tính comparable, thứ tự ưu tiên sắp xếp là từ trái qua phải  

Dưới đây là một số hàm trong `heapq`  
1. **heapify(x)**: hàm này dùng để build heap từ một list x với độ phức tạp **O(n)**  
2. **heappush(heap, item)**: thêm item vào heap  
3. **heappop(heap)**: lấy phần tử nhỏ nhất ra khỏi heap  

### Ví dụ
```python
In [1]: import heapq

In [2]: heap = [(4,2), (3,1), (4,5), (7,3), (2,6), (3,5), (9,7), (6,3)]

In [3]: heapq.heapify(heap)

In [4]: heap
Out[4]: [(2, 6), (3, 1), (3, 5), (6, 3), (4, 2), (4, 5), (9, 7), (7, 3)]

In [5]: heapq.heappop(heap)
Out[5]: (2, 6)

In [6]: heap
Out[6]: [(3, 1), (4, 2), (3, 5), (6, 3), (7, 3), (4, 5), (9, 7)]

In [7]: heapq.heappush(heap, (0,9))

In [8]: heap
Out[8]: [(0, 9), (3, 1), (3, 5), (4, 2), (7, 3), (4, 5), (9, 7), (6, 3)]
```


## Bisection Array
### Giới thiệu
Đây là cấu trúc dữ liệu kiểu mảng, có thứ tự và có thể thêm động phần tử vào mảng mà không cần phải sắp xếp lại mạng sau khi thêm phần tử mới. Thư viện mà python cung cấp cho kiểu dữ liệu này là `bisect`.  
Các hàm được cung cấp là:  
1. **bisect_left(a, x, lo=0, hi=len(a))**: Tìm vị trí *ix* để thêm phần tử x vào mảng a sao cho a[lo:ix] < x <= a[ix:hi]
2. **bisect_right(a, x, lo=0, hi=len(a))**: Hàm này không thấy mô tả 
3. **bisect(a, x, lo=0, hi=len(a))**: Tìm vị trí *ix* để thêm phần tử x vào mảng a sao cho a[lo:ix] <= x < a[ix:hi]
4. **insort_left(a, x, lo=0, hi=len(a))**: Thêm x và a với vị trí được tìm bởi bisect_left, độ phức tạp cho thao tác insert là **O(n)**
5. **insort_right(a, x, lo=0, hi=len(a))**: Thêm x và a với vị trí được tìm bởi bisect_right
6. **insort(a, x, lo=0, hi=len(a))**: Thêm x và a với vị trí được tìm bởi bisect

### Một số hàm tìm kiếm với sorted list
```python
def index(a, x):
    'Locate the leftmost value exactly equal to x'
    i = bisect_left(a, x)
    if i != len(a) and a[i] == x:
        return i
    raise ValueError

def find_lt(a, x):
    'Find rightmost value less than x'
    i = bisect_left(a, x)
    if i:
        return a[i-1]
    raise ValueError

def find_le(a, x):
    'Find rightmost value less than or equal to x'
    i = bisect_right(a, x)
    if i:
        return a[i-1]
    raise ValueError

def find_gt(a, x):
    'Find leftmost value greater than x'
    i = bisect_right(a, x)
    if i != len(a):
        return a[i]
    raise ValueError

def find_ge(a, x):
    'Find leftmost item greater than or equal to x'
    i = bisect_left(a, x)
    if i != len(a):
        return a[i]
    raise ValueError
```

### Ví dụ
```python
In [1]: import bisect

In [2]: arr = [5,8,6,2,3,9,5,3,1,8,5]

In [3]: arr.sort()

In [4]: arr
Out[4]: [1, 2, 3, 3, 5, 5, 5, 6, 8, 8, 9]

In [5]: bisect.bisect_left(arr, 5)
Out[5]: 4

In [6]: bisect.bisect(arr, 5)
Out[6]: 7

In [7]: bisect.bisect_right(arr, 5)
Out[7]: 7

In [8]: bisect.insort_left(arr, 4)

In [9]: arr
Out[9]: [1, 2, 3, 3, 4, 5, 5, 5, 6, 8, 8, 9]

In [10]: bisect.insort_right(arr, 7)

In [11]: arr
Out[11]: [1, 2, 3, 3, 4, 5, 5, 5, 6, 7, 8, 8, 9]
```

### Note
Độ phức tạp của một số thư viện trên có thể xem tại đây: [https://wiki.python.org/moin/TimeComplexity](https://wiki.python.org/moin/TimeComplexity)