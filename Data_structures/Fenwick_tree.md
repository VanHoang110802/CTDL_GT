> Bài viết được dịch lại của trang: [cp-algorithm](https://cp-algorithms.com/data_structures/fenwick.html)

# Fenwick Tree

Giả sử $f$ là một phép toán nhóm (một hàm nhị phân kết hợp trên một tập với phần tử đơn vị và các phần tử nghịch đảo) và $A$ là một mảng các số nguyên có độ dài $N$.
Ký hiệu theo cách ghi chú trung tố của ${f}$ là dấu sao ${*}$; tức là, ${ f(x, y) = x * y }$ đối với các số nguyên tùy ý ${x,y}$.
(Vì phép toán này kết hợp, chúng ta sẽ bỏ qua dấu ngoặc khi biểu thị thứ tự áp dụng $f$ trong cách ghi chú trung tố.)

Cây Fenwick là một cấu trúc dữ liệu có các tính năng:

* Tính giá trị của hàm $f$ trong khoảng đã cho $[l, r]$ (tức là $A_l * A_{l+1} * \dots * A_r$) trong thời gian $O(\log N)$
* Cập nhật giá trị của một phần tử trong mảng $A$ trong thời gian $O(\log N)$
* Cần $O(N)$ bộ nhớ (bằng với lượng bộ nhớ yêu cầu cho $A$)
* Dễ sử dụng và lập trình, đặc biệt trong trường hợp mảng nhiều chiều

Ứng dụng phổ biến nhất của cây Fenwick là _tính tổng của một khoảng (calculating the sum of a range)_.
Ví dụ, sử dụng phép cộng trên tập hợp các số nguyên làm phép toán nhóm, tức là $f(x,y) = x + y$: phép toán nhị phân, $*$, là $+$ trong trường hợp này, vì vậy $A_l * A_{l+1} * \dots * A_r = A_l + A_{l+1} + \dots + A_{r}$.

Cây Fenwick còn được gọi là Cây chỉ số nhị phân (BIT).
Nó lần đầu tiên được mô tả trong một bài báo có tiêu đề "A new data structure for cumulative frequency tables" (Peter M. Fenwick, 1994).

## Description

### Overview

Để cho đơn giản, chúng ta sẽ giả sử rằng hàm $f$ được định nghĩa là $f(x,y) = x + y$ trên các số nguyên.

Giả sử chúng ta được cho một mảng các số nguyên, $A[0 \dots N-1]$.
(Lưu ý rằng chúng ta đang sử dụng chỉ số bắt đầu từ 0.)
Cây Fenwick chỉ là một mảng, $T[0 \dots N-1]$, trong đó mỗi phần tử bằng tổng các phần tử của $A$ trong một khoảng, $[g(i), i]$:

$$T_i = \sum_{j = g(i)}^{i}{A_j}$$

trong đó $g$ là một hàm thoả mãn $0 \le g(i) \le i$.
Chúng ta sẽ định nghĩa $g$ trong các trang đoạn tiếp theo.

Cấu trúc dữ liệu này được gọi là cây vì có một cách biểu diễn đẹp mắt dưới dạng cây, mặc dù chúng ta không cần phải mô phỏng một cây thực sự với các nút và cạnh.
Chúng ta chỉ cần duy trì mảng $T$ để xử lý tất cả các truy vấn.

**Lưu ý:** Cây Fenwick được trình bày ở đây sử dụng chỉ số bắt đầu từ 0.
Nhiều người sử dụng một phiên bản của cây Fenwick với chỉ số bắt đầu từ 1.
Do đó, bạn cũng sẽ tìm thấy một triển khai thay thế sử dụng chỉ số bắt đầu từ 1 trong phần triển khai.
Cả hai phiên bản đều tương đương về độ phức tạp thời gian và bộ nhớ.

Bây giờ chúng ta có thể viết một số mã giả cho hai phép toán đã đề cập ở trên.
Dưới đây, chúng ta tính tổng các phần tử của $A$ trong khoảng $[0, r]$ và cập nhật (tăng) một phần tử $A_i$:

```python
def sum(int r):
    res = 0
    while (r >= 0):
        res += t[r]
        r = g(r) - 1
    return res

def increase(int i, int delta):
    for all j with g(j) <= i <= j:
        t[j] += delta
```

Hàm `sum` hoạt động như sau:

1. Đầu tiên, nó cộng tổng của khoảng $[g(r), r]$ (tức là $T[r]$) vào biến `result`.
2. Sau đó, nó "jumps" đến khoảng $[g(g(r)-1), g(r)-1]$ và cộng tổng của khoảng này vào `result`.
3. Quá trình này tiếp tục cho đến khi nó "jumps" từ $[0, g(g( \dots g(r)-1 \dots -1)-1)]$ đến $[g(-1), -1]$; đây là điểm mà hàm `sum` dừng lại việc nhảy.

Hàm `increase` hoạt động với cùng một phép phân tích, nhưng nó "jumps" theo hướng các chỉ số tăng dần:

1. Tổng của mỗi khoảng có dạng $[g(j), j]$ mà thỏa mãn điều kiện $g(j) \le i \le j$ sẽ được tăng thêm `delta`; tức là, `t[j] += delta`.
Do đó, nó cập nhật tất cả các phần tử trong $T$ tương ứng với các khoảng mà $A_i$ nằm trong đó.

Độ phức tạp của cả hai hàm `sum` và `increase` phụ thuộc vào hàm $g$.
Có nhiều cách để chọn hàm $g$ sao cho $0 \le g(i) \le i$ với mọi $i$.
Ví dụ, hàm $g(i) = i$ hoạt động, dẫn đến $T = A$ (trong trường hợp này, các truy vấn tổng sẽ chậm).
Chúng ta cũng có thể chọn hàm $g(i) = 0$.
Điều này sẽ tương ứng với mảng tổng tiền tố (trong trường hợp này, việc tìm tổng trong khoảng $[0, i]$ chỉ mất thời gian hằng số; tuy nhiên, việc cập nhật sẽ chậm).
Điểm thông minh trong thuật toán của cây Fenwick là cách nó sử dụng một định nghĩa đặc biệt của hàm $g$ giúp xử lý cả hai phép toán trong thời gian $O(\log N)$.

### Definition of $g(i)$

Việc tính toán $g(i)$ được định nghĩa bằng cách sử dụng phép toán đơn giản sau:
chúng ta thay thế tất cả các bit $1$ ở cuối trong biểu diễn nhị phân của $i$ bằng các bit $0$.

Nói cách khác, nếu chữ số ít quan trọng nhất của $i$ trong hệ nhị phân là $0$, thì $g(i) = i$.
Ngược lại, nếu chữ số ít quan trọng nhất là $1$, thì chúng ta lấy chữ số $1$ này và tất cả các chữ số $1$ tiếp theo ở cuối và đảo ngược chúng.

Ví dụ, chúng ta có:

$$\begin{align}
g(11) = g(1011_2) = 1000_2 &= 8 \\\\
g(12) = g(1100_2) = 1100_2 &= 12 \\\\
g(13) = g(1101_2) = 1100_2 &= 12 \\\\
g(14) = g(1110_2) = 1110_2 &= 14 \\\\
g(15) = g(1111_2) = 0000_2 &= 0 \\\\
\end{align}$$

Có một triển khai đơn giản sử dụng các phép toán theo bit cho phép toán không tầm thường được mô tả ở trên:
${g(i) = i}$ & ${(i + 1),}$

Trong đó & là toán tử AND theo bit. Không khó để thuyết phục bản thân rằng giải pháp này thực hiện cùng một phép toán như mô tả ở trên.

Bây giờ, chúng ta chỉ cần tìm một cách để lặp qua tất cả các giá trị $j$, sao cho $g(j) \le i \le j$.

Dễ dàng nhận thấy rằng chúng ta có thể tìm tất cả các $j$ như vậy bằng cách bắt đầu từ $i$ và đảo ngược bit chưa được thiết lập cuối cùng.
Chúng ta sẽ gọi phép toán này là $h(j)$.
Ví dụ, đối với $i = 10$ ta có:

$$\begin{align}
10 &= 0001010_2 \\\\
h(10) = 11 &= 0001011_2 \\\\
h(11) = 15 &= 0001111_2 \\\\
h(15) = 31 &= 0011111_2 \\\\
h(31) = 63 &= 0111111_2 \\\\
\vdots &
\end{align}$$

Không có gì ngạc nhiên khi cũng tồn tại một cách đơn giản để thực hiện $h$ bằng cách sử dụng các phép toán theo bit:

$$h(j) = j ~|~ (j+1),$$

Trong đó $|$ là toán tử OR theo bit.

Hình ảnh dưới đây cho thấy một cách diễn giải có thể của cây Fenwick dưới dạng cây. 
Các nút của cây biểu thị các khoảng mà chúng bao phủ.

![binary_indexed_tree](https://github.com/user-attachments/assets/3d85ed04-f40a-4fdc-a9b1-1e428d573a68)


## Implementation

### Finding sum in one-dimensional array

Ở đây, chúng ta trình bày một triển khai của cây Fenwick cho các truy vấn tổng và cập nhật đơn.

Cây Fenwick thông thường chỉ có thể trả lời các truy vấn tổng kiểu $[0, r]$ thông qua hàm `sum(int r)`, tuy nhiên, chúng ta cũng có thể trả lời các truy vấn khác kiểu $[l, r]$ bằng cách tính hai tổng $[0, r]$ và $[0, l-1]$ sau đó trừ chúng đi.
Điều này được xử lý trong phương thức `sum(int l, int r)` .

Ngoài ra, triển khai này hỗ trợ hai hàm tạo. 
Bạn có thể tạo một cây Fenwick được khởi tạo với giá trị bằng không, hoặc bạn có thể chuyển đổi một mảng hiện có thành dạng cây Fenwick.


```cpp
struct FenwickTree {
    vector<int> bit;  // binary indexed tree
    int n;

    FenwickTree(int n) {
        this->n = n;
        bit.assign(n, 0);
    }

    FenwickTree(vector<int> const &a) : FenwickTree(a.size()) {
        for (size_t i = 0; i < a.size(); i++)
            add(i, a[i]);
    }

    int sum(int r) {
        int ret = 0;
        for (; r >= 0; r = (r & (r + 1)) - 1)
            ret += bit[r];
        return ret;
    }

    int sum(int l, int r) {
        return sum(r) - sum(l - 1);
    }

    void add(int idx, int delta) {
        for (; idx < n; idx = idx | (idx + 1))
            bit[idx] += delta;
    }
};
```

### Linear construction

Triển khai trên yêu cầu thời gian $O(N \log N)$.
Tuy nhiên, có thể cải thiện điều này xuống còn thời gian $O(N)$.

Ý tưởng là, số $a[i]$ tại chỉ số $i$ sẽ đóng góp vào khoảng lưu trữ trong $bit[i]$, và vào tất cả các khoảng mà chỉ số $i | (i + 1)$ đóng góp.
Vì vậy, bằng cách cộng các số theo thứ tự, bạn chỉ cần đẩy tổng hiện tại vào khoảng tiếp theo, nơi nó sẽ tiếp tục được đẩy vào khoảng tiếp theo, và cứ như vậy.

```cpp
FenwickTree(vector<int> const &a) : FenwickTree(a.size()){
    for (int i = 0; i < n; i++) {
        bit[i] += a[i];
        int r = i | (i + 1);
        if (r < n) bit[r] += bit[i];
    }
}
```

### Finding minimum of $[0, r]$ in one-dimensional array

Rõ ràng là không có cách dễ dàng để tìm giá trị nhỏ nhất trong khoảng $[l, r]$ bằng cách sử dụng cây Fenwick, vì cây Fenwick chỉ có thể trả lời các truy vấn kiểu $[0, r]$.
Thêm vào đó, mỗi lần một giá trị được `update` , giá trị mới phải nhỏ hơn giá trị hiện tại.
Cả hai hạn chế quan trọng này là do phép toán $min$ cùng với tập các số nguyên không tạo thành một nhóm, vì không có phần tử nghịch đảo..

```cpp
struct FenwickTreeMin {
    vector<int> bit;
    int n;
    const int INF = (int)1e9;

    FenwickTreeMin(int n) {
        this->n = n;
        bit.assign(n, INF);
    }

    FenwickTreeMin(vector<int> a) : FenwickTreeMin(a.size()) {
        for (size_t i = 0; i < a.size(); i++)
            update(i, a[i]);
    }

    int getmin(int r) {
        int ret = INF;
        for (; r >= 0; r = (r & (r + 1)) - 1)
            ret = min(ret, bit[r]);
        return ret;
    }

    void update(int idx, int val) {
        for (; idx < n; idx = idx | (idx + 1))
            bit[idx] = min(bit[idx], val);
    }
};
```

Note: it is possible to implement a Fenwick tree that can handle arbitrary minimum range queries and arbitrary updates.
The paper [Efficient Range Minimum Queries using Binary Indexed Trees](http://ioinformatics.org/oi/pdf/v9_2015_39_44.pdf) describes such an approach.
However with that approach you need to maintain a second binary indexed tree over the data, with a slightly different structure, since one tree is not enough to store the values of all elements in the array.
The implementation is also a lot harder compared to the normal implementation for sums.

### Finding sum in two-dimensional array

As claimed before, it is very easy to implement Fenwick Tree for multidimensional array.

```cpp
struct FenwickTree2D {
    vector<vector<int>> bit;
    int n, m;

    // init(...) { ... }

    int sum(int x, int y) {
        int ret = 0;
        for (int i = x; i >= 0; i = (i & (i + 1)) - 1)
            for (int j = y; j >= 0; j = (j & (j + 1)) - 1)
                ret += bit[i][j];
        return ret;
    }

    void add(int x, int y, int delta) {
        for (int i = x; i < n; i = i | (i + 1))
            for (int j = y; j < m; j = j | (j + 1))
                bit[i][j] += delta;
    }
};
```

### One-based indexing approach

For this approach we change the requirements and definition for $T[]$ and $g()$ a little bit.
We want $T[i]$ to store the sum of $[g(i)+1; i]$.
This changes the implementation a little bit, and allows for a similar nice definition for $g(i)$:

```python
def sum(int r):
    res = 0
    while (r > 0):
        res += t[r]
        r = g(r)
    return res

def increase(int i, int delta):
    for all j with g(j) < i <= j:
        t[j] += delta
```

The computation of $g(i)$ is defined as:
toggling of the last set $1$ bit in the binary representation of $i$.

$$\begin{align}
g(7) = g(111_2) = 110_2 &= 6 \\\\
g(6) = g(110_2) = 100_2 &= 4 \\\\
g(4) = g(100_2) = 000_2 &= 0 \\\\
\end{align}$$

The last set bit can be extracted using $i ~\&~ (-i)$, so the operation can be expressed as:

$$g(i) = i - (i ~\&~ (-i)).$$

And it's not hard to see, that you need to change all values $T[j]$ in the sequence $i,~ h(i),~ h(h(i)),~ \dots$ when you want to update $A[j]$, where $h(i)$ is defined as:

$$h(i) = i + (i ~\&~ (-i)).$$

As you can see, the main benefit of this approach is that the binary operations complement each other very nicely.

The following implementation can be used like the other implementations, however it uses one-based indexing internally.

```cpp
struct FenwickTreeOneBasedIndexing {
    vector<int> bit;  // binary indexed tree
    int n;

    FenwickTreeOneBasedIndexing(int n) {
        this->n = n + 1;
        bit.assign(n + 1, 0);
    }

    FenwickTreeOneBasedIndexing(vector<int> a)
        : FenwickTreeOneBasedIndexing(a.size()) {
        for (size_t i = 0; i < a.size(); i++)
            add(i, a[i]);
    }

    int sum(int idx) {
        int ret = 0;
        for (++idx; idx > 0; idx -= idx & -idx)
            ret += bit[idx];
        return ret;
    }

    int sum(int l, int r) {
        return sum(r) - sum(l - 1);
    }

    void add(int idx, int delta) {
        for (++idx; idx < n; idx += idx & -idx)
            bit[idx] += delta;
    }
};
```

## Range operations

A Fenwick tree can support the following range operations:

1. Point Update and Range Query
2. Range Update and Point Query
3. Range Update and Range Query

### 1. Point Update and Range Query

This is just the ordinary Fenwick tree as explained above.

### 2. Range Update and Point Query

Using simple tricks we can also do the reverse operations: increasing ranges and querying for single values.

Let the Fenwick tree be initialized with zeros.
Suppose that we want to increment the interval $[l, r]$ by $x$.
We make two point update operations on Fenwick tree which are `add(l, x)` and `add(r+1, -x)`.

If we want to get the value of $A[i]$, we just need to take the prefix sum using the ordinary range sum method.
To see why this is true, we can just focus on the previous increment operation again.
If $i < l$, then the two update operations have no effect on the query and we get the sum $0$.
If $i \in [l, r]$, then we get the answer $x$ because of the first update operation.
And if $i > r$, then the second update operation will cancel the effect of first one.

The following implementation uses one-based indexing.

```cpp
void add(int idx, int val) {
    for (++idx; idx < n; idx += idx & -idx)
        bit[idx] += val;
}

void range_add(int l, int r, int val) {
    add(l, val);
    add(r + 1, -val);
}

int point_query(int idx) {
    int ret = 0;
    for (++idx; idx > 0; idx -= idx & -idx)
        ret += bit[idx];
    return ret;
}
```

Note: of course it is also possible to increase a single point $A[i]$ with `range_add(i, i, val)`.

### 3. Range Update and Range Query

To support both range updates and range queries we will use two BITs namely $B_1[]$ and $B_2[]$, initialized with zeros.

Suppose that we want to increment the interval $[l, r]$ by the value $x$.
Similarly as in the previous method, we perform two point updates on $B_1$: `add(B1, l, x)` and `add(B1, r+1, -x)`.
And we also update $B_2$. The details will be explained later.

```python
def range_add(l, r, x):
    add(B1, l, x)
    add(B1, r+1, -x)
    add(B2, l, x*(l-1))
    add(B2, r+1, -x*r))
```
After the range update $(l, r, x)$ the range sum query should return the following values:

$$
sum[0, i]=
\begin{cases}
0 & i < l \\\\
x \cdot (i-(l-1)) & l \le i \le r \\\\
x \cdot (r-l+1) & i > r \\\\
\end{cases}
$$

We can write the range sum as difference of two terms, where we use $B_1$ for first term and $B_2$ for second term.
The difference of the queries will give us prefix sum over $[0, i]$.

$$\begin{align}
sum[0, i] &= sum(B_1, i) \cdot i - sum(B_2, i) \\\\
&= \begin{cases}
0 \cdot i - 0 & i < l\\\\
x \cdot i - x \cdot (l-1) & l \le i \le r \\\\
0 \cdot i - (x \cdot (l-1) - x \cdot r) & i > r \\\\
\end{cases}
\end{align}
$$

The last expression is exactly equal to the required terms.
Thus we can use $B_2$ for shaving off extra terms when we multiply $B_1[i]\times i$.

We can find arbitrary range sums by computing the prefix sums for $l-1$ and $r$ and taking the difference of them again.

```python
def add(b, idx, x):
    while idx <= N:
        b[idx] += x
        idx += idx & -idx

def range_add(l,r,x):
    add(B1, l, x)
    add(B1, r+1, -x)
    add(B2, l, x*(l-1))
    add(B2, r+1, -x*r)

def sum(b, idx):
    total = 0
    while idx > 0:
        total += b[idx]
        idx -= idx & -idx
    return total

def prefix_sum(idx):
    return sum(B1, idx)*idx -  sum(B2, idx)

def range_sum(l, r):
    return prefix_sum(r) - prefix_sum(l-1)
```
