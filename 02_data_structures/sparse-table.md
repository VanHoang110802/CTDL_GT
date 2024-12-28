> Bài viết được dịch lại của trang: [cp-algorithm](https://cp-algorithms.com/data_structures/sparse-table.html)

# Sparse Table (Bảng thưa)

Sparse Table is a data structure, that allows answering range queries.
It can answer most range queries in $O(\log n)$, but its true power is answering range minimum queries (or equivalent range maximum queries).
For those queries it can compute the answer in $O(1)$ time.
> Sparse Table là một cấu trúc dữ liệu, cho phép trả lời các truy vấn phạm vi.
Nó có thể trả lời hầu hết các truy vấn phạm vi trong thời gian $O(\log n)$, nhưng sức mạnh thực sự của nó là trả lời các truy vấn giá trị nhỏ nhất trong phạm vi (hoặc các truy vấn giá trị lớn nhất tương đương).
Đối với những truy vấn này, nó có thể tính toán kết quả trong thời gian $O(1)$.

The only drawback of this data structure is, that it can only be used on _immutable_ arrays.
This means, that the array cannot be changed between two queries.
If any element in the array changes, the complete data structure has to be recomputed.
> Nhược điểm duy nhất của cấu trúc dữ liệu này là nó chỉ có thể được sử dụng trên các mảng bất biến ( _immutable arrays_ ).
Điều này có nghĩa là mảng không thể thay đổi giữa hai truy vấn.
Nếu bất kỳ phần tử nào trong mảng thay đổi, toàn bộ cấu trúc dữ liệu phải được tính toán lại.

## Intuition

Any non-negative number can be uniquely represented as a sum of decreasing powers of two.
This is just a variant of the binary representation of a number.
E.g. $13 = (1101)_2 = 8 + 4 + 1$.
For a number $x$ there can be at most $\lceil \log_2 x \rceil$ summands.
> Mọi số không âm có thể được biểu diễn duy nhất dưới dạng tổng của các lũy thừa giảm dần của số hai.
Đây thực chất là một biến thể của biểu diễn nhị phân của một số.
Ví dụ: $13 = (1101)_2 = 8 + 4 + 1$.
Với một số $x$ có thể có tối đa $\lceil \log_2 x \rceil$ hạng tử.

By the same reasoning any interval can be uniquely represented as a union of intervals with lengths that are decreasing powers of two.
E.g. $[2, 14] = [2, 9] \cup [10, 13] \cup [14, 14]$, where the complete interval has length 13, and the individual intervals have the lengths 8, 4 and 1 respectively.
And also here the union consists of at most $\lceil \log_2(\text{length of interval}) \rceil$ many intervals.
> Dựa trên lý luận tương tự, bất kỳ khoảng nào cũng có thể được biểu diễn duy nhất dưới dạng hợp của các khoảng có độ dài là các lũy thừa giảm dần của số hai.
Ví dụ: $[2, 14] = [2, 9] \cup [10, 13] \cup [14, 14]$, trong đó khoảng đầy đủ có độ dài 13, và các khoảng con có độ dài lần lượt là 8, 4 và 1.
Và ở đây, hợp của các khoảng cũng có tối đa $\lceil \log_2(\text{độ dài khoảng}) \rceil$ khoảng.

The main idea behind Sparse Tables is to precompute all answers for range queries with power of two length.
Afterwards a different range query can be answered by splitting the range into ranges with power of two lengths, looking up the precomputed answers, and combining them to receive a complete answer.
> Ý tưởng chính đằng sau Sparse Tables là tính toán trước tất cả các câu trả lời cho các truy vấn phạm vi có độ dài là các lũy thừa của hai.
Sau đó, một truy vấn phạm vi khác có thể được trả lời bằng cách chia phạm vi thành các phạm vi có độ dài là các lũy thừa của hai, tra cứu các câu trả lời đã tính toán trước, và kết hợp chúng lại để nhận được câu trả lời hoàn chỉnh.

## Precomputation (Tính toán trước)

We will use a 2-dimensional array for storing the answers to the precomputed queries.
$\text{st}[i][j]$ will store the answer for the range $[j, j + 2^i - 1]$ of length $2^i$.
The size of the 2-dimensional array will be $(K + 1) \times \text{MAXN}$, where $\text{MAXN}$ is the biggest possible array length.
$\text{K}$ has to satisfy $\text{K} \ge \lfloor \log_2 \text{MAXN} \rfloor$, because $2^{\lfloor \log_2 \text{MAXN} \rfloor}$ is the biggest power of two range, that we have to support.
For arrays with reasonable length ($\le 10^7$ elements), $K = 25$ is a good value.
> Chúng ta sẽ sử dụng một mảng 2 chiều để lưu trữ các câu trả lời cho các truy vấn đã được tính toán trước.
$\text{st}[i][j]$ sẽ lưu trữ câu trả lời cho khoảng $[j, j + 2^i - 1]$ có độ dài $2^i$.
Kích thước của mảng 2 chiều sẽ là $(K + 1) \times \text{MAXN}$, trong đó $\text{MAXN}$ là độ dài mảng lớn nhất có thể.
$\text{K}$ phải thỏa mãn $\text{K} \ge \lfloor \log_2 \text{MAXN} \rfloor$, bởi vì $2^{\lfloor \log_2 \text{MAXN} \rfloor}$ là lũy thừa của hai lớn nhất mà chúng ta phải hỗ trợ.
Đối với các mảng có độ dài hợp lý ($\le 10^7$ phần tử), giá trị $K = 25$ là một lựa chọn tốt.

The $\text{MAXN}$ dimension is second to allow (cache friendly) consecutive memory accesses.
> Kích thước $\text{MAXN}$ được đặt ở chiều thứ hai để cho phép truy cập bộ nhớ liên tiếp (cache friendly - thân thiện với bộ nhớ cache).

```{.cpp file=sparsetable_definition}
int st[K + 1][MAXN];
```

Because the range $[j, j + 2^i - 1]$ of length $2^i$ splits nicely into the ranges $[j, j + 2^{i - 1} - 1]$ and $[j + 2^{i - 1}, j + 2^i - 1]$, both of length $2^{i - 1}$, we can generate the table efficiently using dynamic programming:
> Vì khoảng $[j, j + 2^i - 1]$ có độ dài $2^i$ chia đẹp thành hai khoảng $[j, j + 2^{i - 1} - 1]$ và $[j + 2^{i - 1}, j + 2^i - 1]$, cả hai đều có độ dài $2^{i - 1}$, chúng ta có thể tạo bảng một cách hiệu quả bằng cách sử dụng quy hoạch động:


```{.cpp file=sparsetable_generation}
std::copy(array.begin(), array.end(), st[0]);

for (int i = 1; i <= K; i++)
    for (int j = 0; j + (1 << i) <= N; j++)
        st[i][j] = f(st[i - 1][j], st[i - 1][j + (1 << (i - 1))]);
```

The function $f$ will depend on the type of query.
For range sum queries it will compute the sum, for range minimum queries it will compute the minimum.
> Hàm $f$ sẽ phụ thuộc vào loại truy vấn.
Đối với các truy vấn tổng trong phạm vi, nó sẽ tính tổng, còn đối với các truy vấn tìm giá trị nhỏ nhất trong phạm vi, nó sẽ tính giá trị nhỏ nhất.


The time complexity of the precomputation is $O(\text{N} \log \text{N})$.
> Độ phức tạp thời gian của việc tính toán trước là $O(\text{N} \log \text{N})$.

## Range Sum Queries

For this type of queries, we want to find the sum of all values in a range.
Therefore the natural definition of the function $f$ is $f(x, y) = x + y$.
We can construct the data structure with:
> Đối với loại truy vấn này, chúng ta cần tìm tổng của tất cả các giá trị trong một khoảng.
Do đó, định nghĩa tự nhiên của hàm $f$ is $f(x, y) = x + y$.
Chúng ta có thể xây dựng cấu trúc dữ liệu với:


```{.cpp file=sparsetable_sum_generation}
long long st[K + 1][MAXN];

std::copy(array.begin(), array.end(), st[0]);

for (int i = 1; i <= K; i++)
    for (int j = 0; j + (1 << i) <= N; j++)
        st[i][j] = st[i - 1][j] + st[i - 1][j + (1 << (i - 1))];
```

To answer the sum query for the range $[L, R]$, we iterate over all powers of two, starting from the biggest one.
As soon as a power of two $2^i$ is smaller or equal to the length of the range ($= R - L + 1$), we process the first part of range $[L, L + 2^i - 1]$, and continue with the remaining range $[L + 2^i, R]$.
> Để trả lời truy vấn tổng cho khoảng $[L, R]$, chúng ta lặp qua tất cả các lũy thừa của hai, bắt đầu từ lũy thừa lớn nhất.
Ngay khi một lũy thừa của hai $2^i$ nhỏ hơn hoặc bằng độ dài của khoảng ($= R - L + 1$), chúng ta xử lý phần đầu của khoảng $[L, L + 2^i - 1]$, và tiếp tục với phần còn lại của khoảng $[L + 2^i, R]$.


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
> Độ phức tạp thời gian cho một truy vấn tổng trong phạm vi là $O(K) = O(\log \text{MAXN})$.

## Range Minimum Queries (RMQ)

These are the queries where the Sparse Table shines.
When computing the minimum of a range, it doesn't matter if we process a value in the range once or twice.
Therefore instead of splitting a range into multiple ranges, we can also split the range into only two overlapping ranges with power of two length.
E.g. we can split the range $[1, 6]$ into the ranges $[1, 4]$ and $[3, 6]$.
The range minimum of $[1, 6]$ is clearly the same as the minimum of the range minimum of $[1, 4]$ and the range minimum of $[3, 6]$.
So we can compute the minimum of the range $[L, R]$ with:
> Đây là các truy vấn mà Sparse Table tỏ ra hiệu quả.
Khi tính toán giá trị nhỏ nhất trong một khoảng, không quan trọng là chúng ta xử lý một giá trị trong khoảng đó một lần hay hai lần.
Do đó, thay vì chia một khoảng thành nhiều khoảng con, chúng ta cũng có thể chia khoảng đó thành chỉ hai khoảng chồng lên nhau với độ dài là các lũy thừa của hai.
Ví dụ, chúng ta có thể chia khoảng $[1, 6]$ thành các khoảng $[1, 4]$ and $[3, 6]$.
Giá trị nhỏ nhất trong khoảng $[1, 6]$ rõ ràng là giống như giá trị nhỏ nhất của giá trị nhỏ nhất trong khoảng $[1, 4]$ và giá trị nhỏ nhất trong khoảng $[3, 6]$.
Vậy chúng ta có thể tính toán giá trị nhỏ nhất trong khoảng $[L, R]$ với:

$$\min(\text{st}[i][L], \text{st}[i][R - 2^i + 1]) \quad \text{ where } i = \log_2(R - L + 1)$$

This requires that we are able to compute $\log_2(R - L + 1)$ fast.
You can accomplish that by precomputing all logarithms:
> Điều này yêu cầu chúng ta phải tính toán nhanh $\log_2(R - L + 1)$.
Bạn có thể đạt được điều này bằng cách tính toán trước tất cả các logarithm:

```{.cpp file=sparse_table_log_table}
int lg[MAXN+1];
lg[1] = 0;
for (int i = 2; i <= MAXN; i++)
    lg[i] = lg[i/2] + 1;
```
Alternatively, log can be computed on the fly in constant space and time:
> Hoặc, log có thể được tính toán ngay lập tức trong không gian và thời gian hằng số:

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
> Thử nghiệm này cho thấy việc sử dụng mảng `lg` chậm hơn do lỗi bộ nhớ cache.

Afterwards we need to precompute the Sparse Table structure. This time we define $f$ with $f(x, y) = \min(x, y)$.
> Sau đó, chúng ta cần tính toán trước cấu trúc Sparse Table. Lần này, chúng ta định nghĩa $f$ với $f(x, y) = \min(x, y)$.


```{.cpp file=sparse_table_minimum_generation}
int st[K + 1][MAXN];

std::copy(array.begin(), array.end(), st[0]);

for (int i = 1; i <= K; i++)
    for (int j = 0; j + (1 << i) <= N; j++)
        st[i][j] = min(st[i - 1][j], st[i - 1][j + (1 << (i - 1))]);
```

And the minimum of a range $[L, R]$ can be computed with:
> Và giá trị nhỏ nhất trong một khoảng $[L, R]$ có thể được tính toán với:


```{.cpp file=sparse_table_minimum_query}
int i = lg[R - L + 1];
int minimum = min(st[i][L], st[i][R - (1 << i) + 1]);
```

Time complexity for a Range Minimum Query is $O(1)$.
> Độ phức tạp thời gian cho một truy vấn tìm giá trị nhỏ nhất trong phạm vi là $O(1)$.

## Similar data structures supporting more types of queries (Các cấu trúc dữ liệu tương tự hỗ trợ nhiều loại truy vấn hơn)

One of the main weakness of the $O(1)$ approach discussed in the previous section is, that this approach only supports queries of [idempotent functions](https://en.wikipedia.org/wiki/Idempotence).
I.e. it works great for range minimum queries, but it is not possible to answer range sum queries using this approach.
> Một trong những điểm yếu chính của phương pháp $O(1)$ được thảo luận trong phần trước là phương pháp này chỉ hỗ trợ các truy vấn với idempotent functions.
Tức là, nó hoạt động rất tốt cho các truy vấn tìm giá trị nhỏ nhất trong phạm vi, nhưng không thể trả lời các truy vấn tổng trong phạm vi bằng phương pháp này.

There are similar data structures that can handle any type of associative functions and answer range queries in $O(1)$.
One of them is called [Disjoint Sparse Table](https://discuss.codechef.com/questions/117696/tutorial-disjoint-sparse-table).
Another one would be the [Sqrt Tree](https://cp-algorithms.com/data_structures/sqrt-tree.html).
> Có những cấu trúc dữ liệu tương tự có thể xử lý bất kỳ loại hàm kết hợp nào và trả lời các truy vấn phạm vi trong thời gian $O(1)$.
Một trong số đó được gọi là Disjoint Sparse Table.
Một cấu trúc khác là Sqrt Tree.

## Practice Problems

* [SPOJ - RMQSQ](http://www.spoj.com/problems/RMQSQ/)
* [SPOJ - THRBL](http://www.spoj.com/problems/THRBL/)
* [Codechef - MSTICK](https://www.codechef.com/problems/MSTICK)
* [Codechef - SEAD](https://www.codechef.com/problems/SEAD)
* [Codeforces - CGCDSSQ](http://codeforces.com/contest/475/problem/D)
* [Codeforces - R2D2 and Droid Army](http://codeforces.com/problemset/problem/514/D)
* [Codeforces - Maximum of Maximums of Minimums](http://codeforces.com/problemset/problem/872/B)
* [SPOJ - Miraculous](http://www.spoj.com/problems/TNVFC1M/)
* [DevSkill - Multiplication Interval (archived)](http://web.archive.org/web/20200922003506/https://devskill.com/CodingProblems/ViewProblem/19)
* [Codeforces - Animals and Puzzles](http://codeforces.com/contest/713/problem/D)
* [Codeforces - Trains and Statistics](http://codeforces.com/contest/675/problem/E)
* [SPOJ - Postering](http://www.spoj.com/problems/POSTERIN/)
* [SPOJ - Negative Score](http://www.spoj.com/problems/RPLN/)
* [SPOJ - A Famous City](http://www.spoj.com/problems/CITY2/)
* [SPOJ - Diferencija](http://www.spoj.com/problems/DIFERENC/)
* [Codeforces - Turn off the TV](http://codeforces.com/contest/863/problem/E)
* [Codeforces - Map](http://codeforces.com/contest/15/problem/D)
* [Codeforces - Awards for Contestants](http://codeforces.com/contest/873/problem/E)
* [Codeforces - Longest Regular Bracket Sequence](http://codeforces.com/contest/5/problem/C)
* [Codeforces - Array Stabilization (GCD version)](http://codeforces.com/problemset/problem/1547/F)
