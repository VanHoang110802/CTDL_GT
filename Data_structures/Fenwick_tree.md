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

Lưu ý: có thể triển khai một cây Fenwick có thể xử lý các truy vấn giá trị nhỏ nhất trong khoảng tùy ý và các cập nhật tùy ý.
Bài viết [Efficient Range Minimum Queries using Binary Indexed Trees](http://ioinformatics.org/oi/pdf/v9_2015_39_44.pdf) mô tả một cách tiếp cận như vậy.
Tuy nhiên, với cách tiếp cận đó, bạn cần duy trì một cây chỉ số nhị phân thứ hai trên dữ liệu, với một cấu trúc hơi khác, vì một cây không đủ để lưu trữ các giá trị của tất cả các phần tử trong mảng. 
Việc triển khai cũng khó khăn hơn rất nhiều so với triển khai thông thường cho các phép toán tổng.

### Finding sum in two-dimensional array

Như đã nói trước đó, việc triển khai cây Fenwick cho mảng đa chiều là rất dễ dàng.

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

Đối với phương pháp này, chúng ta thay đổi một chút yêu cầu và định nghĩa cho $T[]$ và $g()$ a little bit.
Chúng ta muốn $T[i]$ lưu trữ tổng của $[g(i)+1; i]$.
Điều này thay đổi một chút cách triển khai, và cho phép một định nghĩa tương tự đẹp cho $g(i)$:

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

Việc tính toán $g(i)$ được định nghĩa là:
đảo ngược bit $1$ cuối cùng đã được thiết lập trong biểu diễn nhị phân của $i$.

$$\begin{align}
g(7) = g(111_2) = 110_2 &= 6 \\\\
g(6) = g(110_2) = 100_2 &= 4 \\\\
g(4) = g(100_2) = 000_2 &= 0 \\\\
\end{align}$$

Bit 1 cuối cùng đã được thiết lập có thể được trích xuất bằng cách sử dụng i & (-i), vì vậy phép toán có thể được biểu diễn như sau:

${g(i) = i - (i}$ & ${(-i))}$

Và không khó để nhận thấy, rằng bạn cần phải thay đổi tất cả các giá trị $T[j]$ trong chuỗi (sequence) $i,~ h(i),~ h(h(i)),~ \dots$ khi bạn muốn cập nhật $A[j]$, trong đó $h(i)$ được định nghĩa là:

${h(i) = i + (i}$ & ${(-i))}$

Như bạn có thể thấy, lợi ích chính của phương pháp này là các phép toán nhị phân bổ sung cho nhau một cách rất đẹp.

Triển khai dưới đây có thể được sử dụng giống như các triển khai khác, tuy nhiên nó sử dụng chỉ số bắt đầu từ 1 bên trong.

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

Cây Fenwick có thể hỗ trợ các phép toán trên khoảng sau:

1. Cập nhật điểm và truy vấn khoảng
2. Cập nhật khoảng và truy vấn điểm
3. Cập nhật khoảng và truy vấn khoảng

### 1. Point Update and Range Query

Đây chỉ là cây Fenwick thông thường như đã giải thích ở trên.

### 2. Range Update and Point Query

Dùng những mẹo đơn giản, chúng ta cũng có thể thực hiện các phép toán ngược lại: tăng giá trị trong khoảng và truy vấn các giá trị đơn lẻ.

Giả sử cây Fenwick được khởi tạo với giá trị bằng không.
Giả sử chúng ta muốn tăng khoảng $[l, r]$ lên (by trong trường hợp này hiểu là lên) $x$.
Chúng ta thực hiện hai phép toán cập nhật điểm trên cây Fenwick, đó là `add(l, x)` và `add(r+1, -x)`.

Nếu chúng ta muốn lấy giá trị của $A[i]$, chỉ cần lấy tổng tiền tố sử dụng phương pháp tổng khoảng thông thường.
Để hiểu tại sao điều này đúng, chúng ta chỉ cần tập trung vào phép toán tăng giá trị trước đó.
Nếu $i < l$, thì hai phép toán cập nhật không có tác động lên truy vấn và chúng ta nhận được tổng là $0$.
Nếu $i \in [l, r]$, thì chúng ta nhận được giá trị $x$ nhờ phép toán cập nhật đầu tiên.
Và nếu $i > r$, thì phép toán cập nhật thứ hai sẽ hủy bỏ tác động của phép toán đầu tiên.

Triển khai dưới đây sử dụng chỉ số bắt đầu từ 1.

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

Lưu ý: tất nhiên, cũng có thể tăng một điểm duy nhất $A[i]$ với `range_add(i, i, val)`.

### 3. Range Update and Range Query

TĐể hỗ trợ cả cập nhật khoảng và truy vấn khoảng, chúng ta sẽ sử dụng hai cây chỉ số nhị phân (BIT), đó là $B_1[]$ và $B_2[]$, được khởi tạo với giá trị bằng không.

Giả sử chúng ta muốn tăng khoảng $[l, r]$ lên giá trị $x$.
Tương tự như phương pháp trước, chúng ta thực hiện hai phép toán cập nhật điểm trên $B_1$: `add(B1, l, x)` và `add(B1, r+1, -x)`.
Và chúng ta cũng cập nhật $B_2$. Chi tiết sẽ được giải thích sau.

```python
def range_add(l, r, x):
    add(B1, l, x)
    add(B1, r+1, -x)
    add(B2, l, x*(l-1))
    add(B2, r+1, -x*r))
```
Sau khi cập nhật khoảng $(l, r, x)$ truy vấn tổng khoảng nên trả về các giá trị sau:

$$
sum[0, i]=
\begin{cases}
0 & i < l \\\\
x \cdot (i-(l-1)) & l \le i \le r \\\\
x \cdot (r-l+1) & i > r \\\\
\end{cases}
$$

Chúng ta có thể viết tổng khoảng như hiệu của hai hạng tử, trong đó chúng ta sử dụng $B_1$ cho hạng tử đầu tiên và $B_2$ cho hạng tử thứ hai.
Hiệu của các truy vấn sẽ cho chúng ta tổng tiền tố trên $[0, i]$.

$$\begin{align}
sum[0, i] &= sum(B_1, i) \cdot i - sum(B_2, i) \\\\
&= \begin{cases}
0 \cdot i - 0 & i < l\\\\
x \cdot i - x \cdot (l-1) & l \le i \le r \\\\
0 \cdot i - (x \cdot (l-1) - x \cdot r) & i > r \\\\
\end{cases}
\end{align}
$$

Biểu thức cuối cùng hoàn toàn bằng với các hạng tử yêu cầu.
Do đó, chúng ta có thể sử dụng $B_2$ để loại bỏ các hạng tử thừa khi nhân $B_1[i]\times i$.

Chúng ta có thể tìm tổng khoảng bất kỳ bằng cách tính tổng tiền tố cho $l-1$ và $r$ sau đó lấy hiệu của chúng.

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
