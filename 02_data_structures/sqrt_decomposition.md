> Bài viết được dịch lại của trang: [cp-algorithm](https://cp-algorithms.com/data_structures/sqrt_decomposition.html)


# Sqrt Decomposition (Phân rã căn bậc hai)

Sqrt Decomposition is a method (or a data structure) that allows you to perform some common operations (finding sum of the elements of the sub-array, finding the minimal/maximal element, etc.) in $O(\sqrt n)$ operations, which is much faster than $O(n)$ for the trivial algorithm.
> Sqrt Decomposition là một phương pháp (hoặc cấu trúc dữ liệu) cho phép bạn thực hiện một số phép toán phổ biến (như tính tổng các phần tử trong mảng con, tìm phần tử nhỏ nhất/lớn nhất, v.v.) trong thời gian $O(\sqrt n)$ , nhanh hơn rất nhiều so với $O(n)$ của thuật toán thông thường.

First we describe the data structure for one of the simplest applications of this idea, then show how to generalize it to solve some other problems, and finally look at a slightly different use of this idea: splitting the input requests into sqrt blocks.
> Trước tiên, chúng ta sẽ mô tả cấu trúc dữ liệu cho một trong những ứng dụng đơn giản nhất của ý tưởng này, sau đó chỉ ra cách tổng quát hóa nó để giải quyết một số vấn đề khác, và cuối cùng xem xét một cách sử dụng hơi khác của ý tưởng này: phân tách các yêu cầu đầu vào thành các khối căn bậc hai.

## Sqrt-decomposition based data structure

Given an array $a[0 \dots n-1]$, implement a data structure that allows to find the sum of the elements $a[l \dots r]$ for arbitrary $l$ and $r$ in $O(\sqrt n)$ operations.
> Cho một mảng $a[0 \dots n-1]$, hãy triển khai một cấu trúc dữ liệu cho phép tìm tổng các phần tử $a[l \dots r]$ với $l$ và $r$ bất kỳ trong $O(\sqrt n)$ phép toán.

### Description

The basic idea of sqrt decomposition is preprocessing. We'll divide the array $a$ into blocks of length approximately $\sqrt n$, and for each block $i$ we'll precalculate the sum of elements in it $b[i]$.
> Ý tưởng cơ bản của phân rã căn bậc hai là tiền xử lý. Chúng ta sẽ chia mảng $a$ thành các khối có độ dài xấp xỉ $\sqrt n$, và với mỗi khối $i$, chúng ta sẽ tính trước tổng các phần tử trong khối đó, lưu trữ trong $b[i]$.

We can assume that both the size of the block and the number of blocks are equal to $\sqrt n$ rounded up:
> Chúng ta có thể giả sử rằng cả kích thước của mỗi khối và số lượng khối đều bằng $\sqrt n$ làm tròn lên:

$$ s = \lceil \sqrt n \rceil $$

Then the array $a$ is divided into blocks in the following way:
> Sau đó, mảng $a$ được chia thành các khối theo cách sau:

${\underbrace{a[0], a[1], \dots, a[s-1]}_{\text{b[0]}}}$,

${\underbrace{a[s], \dots, a[2s-1]}_{\text{b[1]}}}$, 

${\dots,}$

${\underbrace{a[(s-1) \cdot s], \dots, a[n-1]}_{\text{b[s-1]}}}$

The last block may have fewer elements than the others (if $n$ not a multiple of $s$), it is not important to the discussion (as it can be handled easily).
Thus, for each block $k$, we know the sum of elements on it $b[k]$:
> Khối cuối cùng có thể có ít phần tử hơn các khối khác (nếu $n$ không phải là bội số của $s$ ), điều này không quan trọng đối với cuộc thảo luận (vì nó có thể được xử lý một cách dễ dàng).
Vì vậy, với mỗi khối $k$, chúng ta biết tổng các phần tử trong khối đó là $b[k]$:

$$ b[k] = \sum\limits_{i=k\cdot s}^{\min {(n-1,(k+1)\cdot s - 1})} a[i] $$

So, we have calculated the values of $b[k]$ (this required $O(n)$ operations). How can they help us to answer each query $[l, r]$ ?
Notice that if the interval $[l, r]$ is long enough, it will contain several whole blocks, and for those blocks we can find the sum of elements in them in a single operation. As a result, the interval $[l, r]$ will contain parts of only two blocks, and we'll have to calculate the sum of elements in these parts trivially.
> Vậy là, chúng ta đã tính toán được các giá trị của $b[k]$ (việc này yêu cầu $O(n)$ phép toán). Làm thế nào chúng có thể giúp chúng ta trả lời mỗi truy vấn $[l, r]$ ?
Lưu ý rằng nếu khoảng $[l, r]$ đủ dài, nó sẽ chứa vài khối hoàn chỉnh, và với các khối này, chúng ta có thể tính tổng các phần tử trong chúng chỉ với một phép toán. Kết quả là, khoảng $[l, r]$ sẽ chỉ chứa phần của hai khối, và chúng ta sẽ phải tính tổng các phần tử trong các phần này một cách thông thường.

Thus, in order to calculate the sum of elements on the interval $[l, r]$ we only need to sum the elements of the two "tails":
$[l\dots (k + 1)\cdot s-1]$ and $[p\cdot s\dots r]$ , and sum the values $b[i]$ in all the blocks from $k + 1$ to $p-1$:
> Vì vậy, để tính tổng các phần tử trong khoảng $[l, r]$ chúng ta chỉ cần tính tổng các phần tử của hai "đuôi" (tail):
$[l\dots (k + 1)\cdot s-1]$ và $[p\cdot s\dots r]$, và tính tổng các giá trị $b[i]$ trong tất cả các khối từ $k + 1$ đến $p-1$:

$$ \sum\limits_{i=l}^r a[i] = \sum\limits_{i=l}^{(k+1) \cdot s-1} a[i] + \sum\limits_{i=k+1}^{p-1} b[i] + \sum\limits_{i=p\cdot s}^r a[i] $$

Note: When ${k = p}$, i.e. $l$ and $r$ belong to the same block, the formula can't be applied, and the sum should be calculated trivially.
> Lưu ý: Khi ${k = p}$, tức là $l$ và $r$ thuộc cùng một khối, công thức này không thể áp dụng được, và tổng cần được tính một cách thông thường.

This approach allows us to significantly reduce the number of operations. Indeed, the size of each "tail" does not exceed the block length $s$, and the number of blocks in the sum does not exceed $s$. Since we have chosen $s \approx \sqrt n$, the total number of operations required to find the sum of elements on the interval $[l, r]$ is $O(\sqrt n)$.
> Phương pháp này giúp chúng ta giảm đáng kể số phép toán cần thực hiện. Thật vậy, kích thước của mỗi "đuôi" không vượt quá độ dài khối $s$, và số lượng khối trong tổng không vượt quá $s$. Vì chúng ta đã chọn $s \approx \sqrt n$, tổng số phép toán cần thiết để tính tổng các phần tử trong khoảng $[l, r]$ là $O(\sqrt n)$.

### Implementation

Let's start with the simplest implementation:
> Hãy bắt đầu với việc triển khai đơn giản nhất:

```cpp
// input data
int n;
vector<int> a (n);

// preprocessing
int len = (int) sqrt (n + .0) + 1; // size of the block and the number of blocks
vector<int> b (len);
for (int i=0; i<n; ++i)
    b[i / len] += a[i];

// answering the queries
for (;;) {
    int l, r;
  // read input data for the next query
    int sum = 0;
    for (int i=l; i<=r; )
        if (i % len == 0 && i + len - 1 <= r) {
            // if the whole block starting at i belongs to [l, r]
            sum += b[i / len];
            i += len;
        }
        else {
            sum += a[i];
            ++i;
        }
}
```

This implementation has unreasonably many division operations (which are much slower than other arithmetical operations). Instead, we can calculate the indices of the blocks $c_l$ and $c_r$ which contain indices $l$ and $r$, and loop through blocks $c_l+1 \dots c_r-1$ with separate processing of the "tails" in blocks $c_l$ and $c_r$. This approach corresponds to the last formula in the description, and makes the case $c_l = c_r$ a special case.
> Cài đặt này có quá nhiều phép chia không hợp lý (mà các phép chia này chậm hơn nhiều so với các phép toán số học khác). Thay vào đó, chúng ta có thể tính toán chỉ số của các khối $c_l$ và $c_r$ chứa các chỉ số $l$ và $r$, và lặp qua các khối từ $c_l+1 \dots c_r-1$, với việc xử lý riêng biệt các "đuôi" trong các khối $c_l$ and $c_r$. Phương pháp này tương ứng với công thức cuối cùng trong mô tả, và làm cho trường hợp $c_l = c_r$ trở thành một trường hợp đặc biệt.

```cpp
int sum = 0;
int c_l = l / len,   c_r = r / len;
if (c_l == c_r)
    for (int i=l; i<=r; ++i)
        sum += a[i];
else {
    for (int i=l, end=(c_l+1)*len-1; i<=end; ++i)
        sum += a[i];
    for (int i=c_l+1; i<=c_r-1; ++i)
        sum += b[i];
    for (int i=c_r*len; i<=r; ++i)
        sum += a[i];
}
```

## Other problems

So far we were discussing the problem of finding the sum of elements of a continuous subarray. This problem can be extended to allow to **update individual array elements**. If an element $a[i]$ changes, it's sufficient to update the value of $b[k]$ for the block to which this element belongs ($k = i / s$) in one operation:
> Cho đến nay, chúng ta đã thảo luận về vấn đề tính tổng các phần tử của một mảng con liên tiếp. Vấn đề này có thể được mở rộng để cho phép cập nhật các phần tử riêng lẻ trong mảng. Nếu một phần tử $a[i]$ thay đổi, chỉ cần cập nhật giá trị của $b[k]$ đối với khối mà phần tử này thuộc về ($k = i / s$) trong một phép toán:

$$ b[k] += a_{new}[i] - a_{old}[i] $$

On the other hand, the task of finding the sum of elements can be replaced with the task of finding minimal/maximal element of a subarray. If this problem has to address individual elements' updates as well, updating the value of $b[k]$ is also possible, but it will require iterating through all values of block $k$ in $O(s) = O(\sqrt{n})$ operations.
> Mặt khác, nhiệm vụ tìm tổng các phần tử có thể được thay thế bằng nhiệm vụ tìm phần tử nhỏ nhất/lớn nhất của một mảng con. Nếu bài toán này cũng cần phải xử lý việc cập nhật các phần tử riêng lẻ, việc cập nhật giá trị của $b[k]$ cũng có thể thực hiện được, nhưng nó sẽ yêu cầu phải lặp qua tất cả các giá trị của khối $k$ trong $O(s) = O(\sqrt{n})$ phép toán.

Sqrt decomposition can be applied in a similar way to a whole class of other problems: finding the number of zero elements, finding the first non-zero element, counting elements which satisfy a certain property etc.
> Sqrt decomposition có thể được áp dụng theo cách tương tự cho một loạt các bài toán khác: tìm số lượng phần tử bằng 0, tìm phần tử khác 0 đầu tiên, đếm số phần tử thỏa mãn một thuộc tính nhất định, v.v.

Another class of problems appears when we need to **update array elements on intervals**: increment existing elements or replace them with a given value.
> Một lớp bài toán khác xuất hiện khi chúng ta cần cập nhật các phần tử mảng trên các khoảng: tăng giá trị các phần tử hiện tại hoặc thay thế chúng bằng một giá trị cho trước.

For example, let's say we can do two types of operations on an array: add a given value $\delta$ to all array elements on interval $[l, r]$ or query the value of element $a[i]$. Let's store the value which has to be added to all elements of block $k$ in $b[k]$ (initially all $b[k] = 0$). During each "add" operation we need to add $\delta$ to $b[k]$ for all blocks which belong to interval $[l, r]$ and to add $\delta$ to $a[i]$ for all elements which belong to the "tails" of the interval. The answer to query $i$ is simply $a[i] + b[i/s]$. This way "add" operation has $O(\sqrt{n})$ complexity, and answering a query has $O(1)$ complexity.
> Ví dụ, giả sử chúng ta có thể thực hiện hai loại phép toán trên mảng: cộng một giá trị $\delta$ vào tất cả các phần tử trong mảng trên khoảng $[l, r]$ hoặc truy vấn giá trị của phần tử $a[i]$. Hãy lưu trữ giá trị cần thêm vào tất cả các phần tử của khối $k$ trong $b[k]$ (ban đầu tất cả $b[k] = 0$). Trong mỗi phép toán "cộng", chúng ta cần cộng $\delta$ vào $b[k]$ cho tất cả các khối thuộc khoảng $[l, r]$ và cộng $\delta$ vào $a[i]$ cho tất cả các phần tử thuộc "đuôi" của khoảng. Câu trả lời cho truy vấn $i$ đơn giản là $a[i] + b[i/s]$. Theo cách này, phép toán "cộng" có độ phức tạp $O(\sqrt{n})$, và việc trả lời một truy vấn có độ phức tạp $O(1)$.

Finally, those two classes of problems can be combined if the task requires doing **both** element updates on an interval and queries on an interval. Both operations can be done with $O(\sqrt{n})$ complexity. This will require two block arrays $b$ and $c$: one to keep track of element updates and another to keep track of answers to the query.
> Cuối cùng, hai lớp bài toán này có thể được kết hợp nếu nhiệm vụ yêu cầu thực hiện cả hai thao tác cập nhật phần tử trên một khoảng và truy vấn trên một khoảng. Cả hai phép toán này có thể được thực hiện với độ phức tạp $O(\sqrt{n})$. Điều này sẽ yêu cầu hai mảng khối $b$ và $c$: một để theo dõi các cập nhật phần tử và một để theo dõi các câu trả lời cho truy vấn.

There exist other problems which can be solved using sqrt decomposition, for example, a problem about maintaining a set of numbers which would allow adding/deleting numbers, checking whether a number belongs to the set and finding $k$-th largest number. To solve it one has to store numbers in increasing order, split into several blocks with $\sqrt{n}$ numbers in each. Every time a number is added/deleted, the blocks have to be rebalanced by moving numbers between beginnings and ends of adjacent blocks.
> Cũng có những bài toán khác có thể giải quyết bằng phân rã căn bậc hai, ví dụ, một bài toán về duy trì một tập hợp các số cho phép thêm/xóa số, kiểm tra xem một số có thuộc tập hợp hay không và tìm số lớn thứ $k$. Để giải quyết bài toán này, người ta phải lưu trữ các số theo thứ tự tăng dần, chia chúng thành nhiều khối, mỗi khối có $\sqrt{n}$ số. Mỗi khi một số được thêm vào/xóa, các khối phải được cân bằng lại bằng cách di chuyển các số giữa đầu và cuối của các khối liền kề.

## Mo's algorithm

A similar idea, based on sqrt decomposition, can be used to answer range queries ($Q$) offline in $O((N+Q)\sqrt{N})$.
This might sound like a lot worse than the methods in the previous section, since this is a slightly worse complexity than we had earlier and cannot update values between two queries.
But in a lot of situations this method has advantages.
During a normal sqrt decomposition, we have to precompute the answers for each block, and merge them during answering queries.
In some problems this merging step can be quite problematic.
E.g. when each queries asks to find the **mode** of its range (the number that appears the most often).
For this each block would have to store the count of each number in it in some sort of data structure, and we cannot longer perform the merge step fast enough any more.
**Mo's algorithm** uses a completely different approach, that can answer these kind of queries fast, because it only keeps track of one data structure, and the only operations with it are easy and fast.
> Một ý tưởng tương tự, dựa trên phân rã căn bậc hai, có thể được sử dụng để trả lời các truy vấn theo khoảng (range queries, $Q$) một cách ngoại tuyến trong $O((N+Q)\sqrt{N})$.
Điều này có thể nghe có vẻ tệ hơn nhiều so với các phương pháp trong phần trước, vì đây là độ phức tạp hơi kém hơn so với trước đây và không thể cập nhật giá trị giữa hai truy vấn. Tuy nhiên, trong nhiều tình huống, phương pháp này lại có những ưu điểm. Trong phân rã căn bậc hai thông thường, chúng ta phải tính trước các câu trả lời cho từng khối và kết hợp chúng khi trả lời các truy vấn. Trong một số bài toán, bước kết hợp này có thể gặp khá nhiều vấn đề. Ví dụ, khi mỗi truy vấn yêu cầu tìm mode (số xuất hiện nhiều nhất) trong khoảng của nó. Để làm điều này, mỗi khối sẽ phải lưu trữ số lần xuất hiện của từng số trong nó, sử dụng một cấu trúc dữ liệu nào đó, và lúc này chúng ta không thể thực hiện bước kết hợp một cách nhanh chóng nữa. Thuật toán Mo sử dụng một phương pháp hoàn toàn khác, có thể trả lời các truy vấn kiểu này một cách nhanh chóng, bởi vì nó chỉ theo dõi một cấu trúc dữ liệu duy nhất và các phép toán với nó là dễ dàng và nhanh chóng.

The idea is to answer the queries in a special order based on the indices.
We will first answer all queries which have the left index in block 0, then answer all queries which have left index in block 1 and so on.
And also we will have to answer the queries of a block is a special order, namely sorted by the right index of the queries.
> Ý tưởng là trả lời các truy vấn theo một thứ tự đặc biệt dựa trên chỉ số. Chúng ta sẽ trả lời tất cả các truy vấn có chỉ số trái nằm trong khối 0 trước, sau đó là các truy vấn có chỉ số trái nằm trong khối 1, và cứ thế tiếp tục. Đồng thời, chúng ta cũng sẽ phải trả lời các truy vấn của một khối theo một thứ tự đặc biệt, đó là sắp xếp theo chỉ số phải của các truy vấn.

As already said we will use a single data structure.
This data structure will store information about the range.
At the beginning this range will be empty.
When we want to answer the next query (in the special order), we simply extend or reduce the range, by adding/removing elements on both sides of the current range, until we transformed it into the query range.
This way, we only need to add or remove a single element once at a time, which should be pretty easy operations in our data structure.
> Như đã nói, chúng ta sẽ sử dụng một cấu trúc dữ liệu duy nhất. Cấu trúc dữ liệu này sẽ lưu trữ thông tin về khoảng. Ban đầu, khoảng này sẽ là rỗng. Khi chúng ta muốn trả lời truy vấn tiếp theo (theo thứ tự đặc biệt), chúng ta chỉ cần mở rộng hoặc thu hẹp khoảng, bằng cách thêm/xóa phần tử ở cả hai bên của khoảng hiện tại, cho đến khi chuyển nó thành khoảng truy vấn. Theo cách này, chúng ta chỉ cần thêm hoặc xóa một phần tử một lần tại một thời điểm, điều này sẽ là các phép toán khá dễ dàng trong cấu trúc dữ liệu của chúng ta.

Since we change the order of answering the queries, this is only possible when we are allowed to answer the queries in offline mode.
> Vì chúng ta thay đổi thứ tự trả lời các truy vấn, phương pháp này chỉ khả thi khi chúng ta được phép trả lời các truy vấn ở chế độ ngoại tuyến.

### Implementation

In Mo's algorithm we use two functions for adding an index and for removing an index from the range which we are currently maintaining.
> Trong thuật toán Mo, chúng ta sử dụng hai hàm: một để thêm một chỉ số vào khoảng hiện tại mà chúng ta đang duy trì, và một để loại bỏ chỉ số đó khỏi khoảng.

```cpp
void remove(idx);  // TODO: remove value at idx from data structure
void add(idx);     // TODO: add value at idx from data structure
int get_answer();  // TODO: extract the current answer of the data structure

int block_size;

struct Query {
    int l, r, idx;
    bool operator<(Query other) const
    {
        return make_pair(l / block_size, r) <
               make_pair(other.l / block_size, other.r);
    }
};

vector<int> mo_s_algorithm(vector<Query> queries) {
    vector<int> answers(queries.size());
    sort(queries.begin(), queries.end());

    // TODO: initialize data structure

    int cur_l = 0;
    int cur_r = -1;
    // invariant: data structure will always reflect the range [cur_l, cur_r]
    for (Query q : queries) {
        while (cur_l > q.l) {
            cur_l--;
            add(cur_l);
        }
        while (cur_r < q.r) {
            cur_r++;
            add(cur_r);
        }
        while (cur_l < q.l) {
            remove(cur_l);
            cur_l++;
        }
        while (cur_r > q.r) {
            remove(cur_r);
            cur_r--;
        }
        answers[q.idx] = get_answer();
    }
    return answers;
}
```

Based on the problem we can use a different data structure and modify the `add`/`remove`/`get_answer` functions accordingly.
For example if we are asked to find range sum queries then we use a simple integer as data structure, which is $0$ at the beginning.
The `add` function will simply add the value of the position and subsequently update the answer variable.
On the other hand `remove` function will subtract the value at position and subsequently update the answer variable.
And `get_answer` just returns the integer.
> Tùy vào bài toán, chúng ta có thể sử dụng một cấu trúc dữ liệu khác và điều chỉnh các hàm `add`/`remove`/`get_answer` cho phù hợp.
Ví dụ, nếu yêu cầu là tìm tổng các phần tử trong một khoảng, chúng ta có thể sử dụng một số nguyên đơn giản làm cấu trúc dữ liệu, với giá trị ban đầu là $0$.
Hàm `add` sẽ đơn giản cộng giá trị của vị trí vào và sau đó cập nhật biến đáp án.
Ngược lại, hàm `remove` sẽ trừ giá trị tại vị trí và sau đó cập nhật biến đáp án.
Còn hàm `get_answer` chỉ cần trả về giá trị của số nguyên đó.

For answering mode-queries, we can use a binary search tree (e.g. `map<int, int>`) for storing how often each number appears in the current range, and a second binary search tree (e.g. `set<pair<int, int>>`) for keeping counts of the numbers (e.g. as count-number pairs) in order.
The `add` method removes the current number from the second BST, increases the count in the first one, and inserts the number back into the second one.
`remove` does the same thing, it only decreases the count.
And `get_answer` just looks at second tree and returns the best value in $O(1)$.
> Để trả lời các truy vấn về mode, chúng ta có thể sử dụng một cây tìm kiếm nhị phân (ví dụ: `map<int, int>`) để lưu trữ tần suất xuất hiện của mỗi số trong khoảng hiện tại, và một cây tìm kiếm nhị phân thứ hai (ví dụ: `set<pair<int, int>>`) để giữ các cặp đếm-số theo thứ tự.
Hàm `add` sẽ loại bỏ số hiện tại khỏi cây nhị phân thứ hai, tăng tần suất trong cây nhị phân đầu tiên, và chèn số đó trở lại vào cây nhị phân thứ hai.
Hàm `remove` làm điều tương tự, nhưng chỉ giảm tần suất.
Còn hàm `get_answer` chỉ cần xem cây nhị phân thứ hai và trả về giá trị tốt nhất trong  $O(1)$.

### Complexity


Sorting all queries will take $O(Q \log Q)$.
> Sắp xếp tất cả các truy vấn sẽ tốn $O(Q \log Q)$.

How about the other operations?
How many times will the `add` and `remove` be called?
> Còn các phép toán khác thì sao?
Hàm `add`và `remove` sẽ được gọi bao nhiêu lần?

Let's say the block size is $S$.
> Giả sử kích thước khối là $S$.

If we only look at all queries having the left index in the same block, the queries are sorted by the right index.
Therefore we will call `add(cur_r)` and `remove(cur_r)` only $O(N)$ times for all these queries combined.
This gives $O(\frac{N}{S} N)$ calls for all blocks.
> Nếu chỉ xét tất cả các truy vấn có chỉ số trái nằm trong cùng một khối, các truy vấn sẽ được sắp xếp theo chỉ số phải.
Do đó, chúng ta sẽ gọi `add(cur_r)` và `remove(cur_r)` chỉ $O(N)$ lần cho tất cả các truy vấn này cộng lại.
Điều này mang lại $O(\frac{N}{S} N)$ lần gọi cho tất cả các khối.

The value of `cur_l` can change by at most $O(S)$ during between two queries.
Therefore we have an additional $O(S Q)$ calls of `add(cur_l)` and `remove(cur_l)`.
> Giá trị của `cur_l` có thể thay đổi tối đa $O(S)$ trong suốt quá trình giữa hai truy vấn.
Do đó, chúng ta có thêm $O(S Q)$ lần gọi `add(cur_l)` và `remove(cur_l)`.

For $S \approx \sqrt{N}$ this gives $O((N + Q) \sqrt{N})$ operations in total.
Thus the complexity is $O((N+Q)F\sqrt{N})$ where $O(F)$  is the complexity of `add` and `remove` function.
> Với $S \approx \sqrt{N}$, tổng số phép toán sẽ là $O((N + Q) \sqrt{N})$.
Vì vậy, độ phức tạp là $O((N+Q)F\sqrt{N})$, trong đó $O(F)$ là độ phức tạp của các hàm `add` và `remove`.

### Tips for improving runtime

* Block size of precisely $\sqrt{N}$ doesn't always offer the best runtime.  For example, if $\sqrt{N}=750$ then it may happen that block size of $700$ or $800$ may run better.
More importantly, don't compute the block size at runtime - make it `const`. Division by constants is well optimized by compilers.
* In odd blocks sort the right index in ascending order and in even blocks sort it in descending order. This will minimize the movement of right pointer, as the normal sorting will move the right pointer from the end back to the beginning at the start of every block. With the improved version this resetting is no more necessary.

```cpp
bool cmp(pair<int, int> p, pair<int, int> q) {
    if (p.first / BLOCK_SIZE != q.first / BLOCK_SIZE)
        return p < q;
    return (p.first / BLOCK_SIZE & 1) ? (p.second < q.second) : (p.second > q.second);
}
```

You can read about even faster sorting approach [here](https://codeforces.com/blog/entry/61203).

## Practice Problems

* [Codeforces - Kuriyama Mirai's Stones](https://codeforces.com/problemset/problem/433/B)
* [UVA - 12003 - Array Transformer](https://uva.onlinejudge.org/index.php?option=com_onlinejudge&Itemid=8&page=show_problem&problem=3154)
* [UVA - 11990 Dynamic Inversion](https://uva.onlinejudge.org/index.php?option=com_onlinejudge&Itemid=8&page=show_problem&problem=3141)
* [SPOJ - Give Away](http://www.spoj.com/problems/GIVEAWAY/)
* [Codeforces - Till I Collapse](http://codeforces.com/contest/786/problem/C)
* [Codeforces - Destiny](http://codeforces.com/contest/840/problem/D)
* [Codeforces - Holes](http://codeforces.com/contest/13/problem/E)
* [Codeforces - XOR and Favorite Number](https://codeforces.com/problemset/problem/617/E)
* [Codeforces - Powerful array](http://codeforces.com/problemset/problem/86/D)
* [SPOJ - DQUERY](https://www.spoj.com/problems/DQUERY)
* [Codeforces - Robin Hood Archery](https://codeforces.com/contest/2014/problem/H)
