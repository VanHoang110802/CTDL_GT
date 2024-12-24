> Bài viết được dịch lại của trang: [cp-algorithm](https://cp-algorithms.com/data_structures/sparse-table.html)

# Bảng thưa (Sparse Table)

Sparse Table là một cấu trúc dữ liệu, cho phép trả lời các truy vấn phạm vi.
Nó có thể trả lời hầu hết các truy vấn phạm vi trong thời gian $O(\log n)$, nhưng sức mạnh thực sự của nó là trả lời các truy vấn giá trị nhỏ nhất trong phạm vi (hoặc các truy vấn giá trị lớn nhất tương đương).
Đối với những truy vấn này, nó có thể tính toán kết quả trong thời gian $O(1)$.

Nhược điểm duy nhất của cấu trúc dữ liệu này là nó chỉ có thể được sử dụng trên các mảng bất biến ( _immutable arrays_ ).
Điều này có nghĩa là mảng không thể thay đổi giữa hai truy vấn.
Nếu bất kỳ phần tử nào trong mảng thay đổi, toàn bộ cấu trúc dữ liệu phải được tính toán lại.

## Intuition

Mọi số không âm có thể được biểu diễn duy nhất dưới dạng tổng của các lũy thừa giảm dần của số hai.
Đây thực chất là một biến thể của biểu diễn nhị phân của một số.
Ví dụ: $13 = (1101)_2 = 8 + 4 + 1$.
Với một số $x$ có thể có tối đa $\lceil \log_2 x \rceil$ hạng tử.

Dựa trên lý luận tương tự, bất kỳ khoảng nào cũng có thể được biểu diễn duy nhất dưới dạng hợp của các khoảng có độ dài là các lũy thừa giảm dần của số hai.
Ví dụ: $[2, 14] = [2, 9] \cup [10, 13] \cup [14, 14]$, trong đó khoảng đầy đủ có độ dài 13, và các khoảng con có độ dài lần lượt là 8, 4 và 1.
Và ở đây, hợp của các khoảng cũng có tối đa $\lceil \log_2(\text{độ dài khoảng}) \rceil$ khoảng.

Ý tưởng chính đằng sau Sparse Tables là tính toán trước tất cả các câu trả lời cho các truy vấn phạm vi có độ dài là các lũy thừa của hai.
Sau đó, một truy vấn phạm vi khác có thể được trả lời bằng cách chia phạm vi thành các phạm vi có độ dài là các lũy thừa của hai, tra cứu các câu trả lời đã tính toán trước, và kết hợp chúng lại để nhận được câu trả lời hoàn chỉnh.

## Tính toán trước (Precomputation)

Chúng ta sẽ sử dụng một mảng 2 chiều để lưu trữ các câu trả lời cho các truy vấn đã được tính toán trước.
$\text{st}[i][j]$ sẽ lưu trữ câu trả lời cho khoảng $[j, j + 2^i - 1]$ có độ dài $2^i$.
Kích thước của mảng 2 chiều sẽ là $(K + 1) \times \text{MAXN}$, trong đó $\text{MAXN}$ là độ dài mảng lớn nhất có thể.
$\text{K}$ phải thỏa mãn $\text{K} \ge \lfloor \log_2 \text{MAXN} \rfloor$, bởi vì $2^{\lfloor \log_2 \text{MAXN} \rfloor}$ là lũy thừa của hai lớn nhất mà chúng ta phải hỗ trợ.
Đối với các mảng có độ dài hợp lý ($\le 10^7$ phần tử), giá trị $K = 25$ là một lựa chọn tốt.

Kích thước $\text{MAXN}$ được đặt ở chiều thứ hai để cho phép truy cập bộ nhớ liên tiếp (cache friendly).

```cpp
int st[K + 1][MAXN];
```

Because the range $[j, j + 2^i - 1]$ of length $2^i$ splits nicely into the ranges $[j, j + 2^{i - 1} - 1]$ and $[j + 2^{i - 1}, j + 2^i - 1]$, both of length $2^{i - 1}$, we can generate the table efficiently using dynamic programming:

```{.cpp file=sparsetable_generation}
std::copy(array.begin(), array.end(), st[0]);

for (int i = 1; i <= K; i++)
    for (int j = 0; j + (1 << i) <= N; j++)
        st[i][j] = f(st[i - 1][j], st[i - 1][j + (1 << (i - 1))]);
```

The function $f$ will depend on the type of query.
For range sum queries it will compute the sum, for range minimum queries it will compute the minimum.

The time complexity of the precomputation is $O(\text{N} \log \text{N})$.

## Range Sum Queries

For this type of queries, we want to find the sum of all values in a range.
Therefore the natural definition of the function $f$ is $f(x, y) = x + y$.
We can construct the data structure with:

```{.cpp file=sparsetable_sum_generation}
long long st[K + 1][MAXN];

std::copy(array.begin(), array.end(), st[0]);

for (int i = 1; i <= K; i++)
    for (int j = 0; j + (1 << i) <= N; j++)
        st[i][j] = st[i - 1][j] + st[i - 1][j + (1 << (i - 1))];
```

To answer the sum query for the range $[L, R]$, we iterate over all powers of two, starting from the biggest one.
As soon as a power of two $2^i$ is smaller or equal to the length of the range ($= R - L + 1$), we process the first part of range $[L, L + 2^i - 1]$, and continue with the remaining range $[L + 2^i, R]$.

```{.cpp file=sparsetable_sum_query}
long long sum = 0;
for (int i = K; i >= 0; i--) {
    if ((1 << i) <= R - L + 1) {
        sum += st[i][L];
        L += 1 << i;
    }
}
```

Time complexity for a Range Sum Query is $O(K) = O(\log \text{MAXN})$.

## Range Minimum Queries (RMQ)

Đây là các truy vấn mà Sparse Table tỏ ra hiệu quả.
Khi tính toán giá trị nhỏ nhất trong một khoảng, không quan trọng là chúng ta xử lý một giá trị trong khoảng đó một lần hay hai lần.
Do đó, thay vì chia một khoảng thành nhiều khoảng con, chúng ta cũng có thể chia khoảng đó thành chỉ hai khoảng chồng lên nhau với độ dài là các lũy thừa của hai.
Ví dụ, chúng ta có thể chia khoảng $[1, 6]$ thành các khoảng $[1, 4]$ and $[3, 6]$.
Giá trị nhỏ nhất trong khoảng $[1, 6]$ rõ ràng là giống như giá trị nhỏ nhất của giá trị nhỏ nhất trong khoảng $[1, 4]$ và giá trị nhỏ nhất trong khoảng $[3, 6]$.
Vậy chúng ta có thể tính toán giá trị nhỏ nhất trong khoảng $[L, R]$ với:

$$\min(\text{st}[i][L], \text{st}[i][R - 2^i + 1]) \quad \text{ where } i = \log_2(R - L + 1)$$

Điều này yêu cầu chúng ta phải tính toán nhanh $\log_2(R - L + 1)$.
Bạn có thể đạt được điều này bằng cách tính toán trước tất cả các logarithm:

```cpp
int lg[MAXN+1];
lg[1] = 0;
for (int i = 2; i <= MAXN; i++)
    lg[i] = lg[i/2] + 1;
```
Hoặc, log có thể được tính toán ngay lập tức trong không gian và thời gian hằng số:
```c++
// C++20
#include <bit>
int log2_floor(unsigned long i) {
    return std::bit_width(i) - 1;
}

// pre C++20
int log2_floor(unsigned long long i) {
    return i ? __builtin_clzll(1) - __builtin_clzll(i) : -1;
}
```
[This benchmark](https://quick-bench.com/q/Zghbdj_TEkmw4XG2nqOpD3tsJ8U) shows that using `lg` array is slower because of cache misses.

Sau đó, chúng ta cần tính toán trước cấu trúc Sparse Table. Lần này, chúng ta định nghĩa $f$ với $f(x, y) = \min(x, y)$.

```cpp
int st[K + 1][MAXN];

std::copy(array.begin(), array.end(), st[0]);

for (int i = 1; i <= K; i++)
    for (int j = 0; j + (1 << i) <= N; j++)
        st[i][j] = min(st[i - 1][j], st[i - 1][j + (1 << (i - 1))]);
```

Và giá trị nhỏ nhất trong một khoảng $[L, R]$ có thể được tính toán với:

```cpp 
int i = lg[R - L + 1];
int minimum = min(st[i][L], st[i][R - (1 << i) + 1]);
```

Độ phức tạp thời gian cho một truy vấn tìm giá trị nhỏ nhất trong phạm vi là $O(1)$.

## Các cấu trúc dữ liệu tương tự hỗ trợ nhiều loại truy vấn hơn (Similar data structures supporting more types of queries)

Một trong những điểm yếu chính của phương pháp $O(1)$ được thảo luận trong phần trước là phương pháp này chỉ hỗ trợ các truy vấn với [idempotent functions](https://en.wikipedia.org/wiki/Idempotence).
Tức là, nó hoạt động rất tốt cho các truy vấn tìm giá trị nhỏ nhất trong phạm vi, nhưng không thể trả lời các truy vấn tổng trong phạm vi bằng phương pháp này.

Có những cấu trúc dữ liệu tương tự có thể xử lý bất kỳ loại hàm kết hợp nào và trả lời các truy vấn phạm vi trong thời gian $O(1)$.
Một trong số đó được gọi là [Disjoint Sparse Table](https://discuss.codechef.com/questions/117696/tutorial-disjoint-sparse-table).
Một cấu trúc khác là [Sqrt Tree](sqrt-tree.md).
