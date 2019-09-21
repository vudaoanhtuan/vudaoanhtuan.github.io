---
title: Matrix Exponentiation and Fibonacci
tag: algorithm
mathjax: true
---

## Giới thiệu
Dãy Fibonacci được định nghĩa như sau:  
$$
    F_n =
    \begin{cases}
        0 & \text{$n=0$,} \\
        1 & \text{$n=1$,} \\
        F_{n} = F_{n-1} + F_{n-2} & \text{$n>1$}
    \end{cases}
    \tag{1}
$$

Giải thuật lặp thông thường có độ phức tạp $$O(N)$$ chỉ có thể tính được $$F_n$$ với $$n<10^6$$.  
Bài viết này trình bày giải thuật tính số Fibonacci bằng phương pháp luỹ thừa ma trận với độ phức tạp $$O(log(N))$$, có thể tính được $$F_n$$ khi n rất lớn.

## Lý thuyết 
### Dãy Fibonacci
Từ công thức $$(1)$$ ta có thể viết lại đưới dạng ma trận như sau:  
$$
\begin{bmatrix}
    F_n \\
    F_{n-1}
\end{bmatrix}
=
\begin{bmatrix}
    1 & 1 \\
    1 & 0 
\end{bmatrix}
\begin{bmatrix}
    F_{n-1} \\
    F_{n-2}
\end{bmatrix}
\tag{2}
$$

Từ $$(2)$$ ta khai triển đệ quy được:  
{% raw %}
$$
\begin{align}
\begin{bmatrix}
    F_n \\
    F_{n-1}
\end{bmatrix}
&= 
\begin{bmatrix}
    1 & 1 \\
    1 & 0 
\end{bmatrix}
\begin{bmatrix}
    F_{n-1} \\
    F_{n-2}
\end{bmatrix} \\
&= 
\begin{bmatrix}
    1 & 1 \\
    1 & 0 
\end{bmatrix}^2
\begin{bmatrix}
    F_{n-2} \\
    F_{n-3}
\end{bmatrix} \\
&\dots \\
&=
\begin{bmatrix}
    1 & 1 \\
    1 & 0 
\end{bmatrix}^{n-1}
\begin{bmatrix}
    F_1 \\
    F_0
\end{bmatrix}
\end{align}
$$
{% endraw %}

Đến đây ta chỉ cần tính luỹ thừa bậc n của một ma trận vuông và nhân với vector các giá trị khởi tạo của dãy là tính được $$F_n$$.  

### Trường hợp tổng quát
Trong trường hợp tổng quát, với một dãy số được định nghĩa như sau:  
{% raw %}
$$
F_n = a_1F_{n-1}+a_2F_{n-2}+...+a_{n-(k-1)}F_{n-(k-1)}+a_kF_{n-k} \\
F_i = b_i, i=\overline{0,k-1}
$$
{% endraw %}

Ta có thể viết lại như sau:  
{% raw %}
$$
\begin{align}
\begin{bmatrix}
    F_n \\
    F_{n-1} \\
    F_{n-2} \\
    \dots \\
    F_{n-(k-1)} \\
    F_{n-k}
\end{bmatrix}
=
\begin{bmatrix}
    a_1     & a_2       & \dots     & a_{n-(k-1)}   & a_{n-k} \\
    1       & 0         & \dots     & 0             & 0 \\
    0       & 1         & \dots     & 0             & 0 \\
    \vdots  & \vdots    & \ddots    & \vdots        & \vdots \\
    0       & 0         & \dots     & 1             & 0 \\
    0       & 0         & \dots     & 0             & 0
\end{bmatrix}^{n-1}
\begin{bmatrix}
    F_{n-k} \\
    F_{n-(k-1)} \\
    F_{n-(k-2)} \\
    \dots \\
    F_1 \\
    F_0
\end{bmatrix}
\end{align}
$$
{% endraw %}

## Tính luỹ thừa ma trận
Việc tính luỹ thừa có thể thực hiện với độ phức tạp $$O(log(N))$$ sử thuật toán chặt nhị phân  
```python
def matmul(mat_a, mat_b):
    row = len(mat_a)
    col = len(mat_b[0])
    mat_c = [[0]*col for _ in range(row)]

    hdim = len(mat_b)
    for i in range(row):
        for j in range(col):
            mat_c[i][j] = 0
            for k in range(hdim):
                mat_c[i][j] += mat_a[i][k]*mat_b[k][j]
    return mat_c


def matrix_power(mat, n):
    if n==1:
        return mat
    n2 = int(n/2)
    mat2 = matrix_power(mat, n2)
    mat2 = matmul(mat2, mat2)

    if n%2 == 0:
        mat = mat2
    else:
        mat = matmul(mat, mat2)
    
    return mat
```

## Bonus
### Tính tổng các phần tử đầu của dãy Fibonacci
Gọi $S_n$ là tổng của các số phần tử đầu tiên của dãy Fibonacci:  
$$ 
S_0=F_0 \\
S_n = S_{n-1} + F_n
$$  

Công thức:
{% raw %}
$$
\begin{align}
\begin{bmatrix}
    F_n \\
    F_{n-1} \\
    S_n
\end{bmatrix}
=
\begin{bmatrix}
    1 & 1 & 0 \\
    1 & 0 & 0 \\
    1 & 1 & 1
\end{bmatrix}
\begin{bmatrix}
    F_{n-1} \\
    F_{n-2} \\
    S_{n-1}
\end{bmatrix}
\end{align}
$$
{% endraw %}

## Tham khảo
- [Matrix Exponentiation](https://www.hackerearth.com/practice/notes/matrix-exponentiation-1/)