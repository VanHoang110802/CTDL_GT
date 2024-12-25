> Bài viết được dịch tại: [cp-algorithms](https://cp-algorithms.com/algebra/binary-exp.html)

# Binary Exponentiation

Phép lũy thừa nhị phân (còn được biết đến là phép lũy thừa bằng phương pháp bình phương) là một mẹo cho phép tính toán $a^n$ chỉ với $O(\log n)$ phép nhân (thay vì $O(n)$ phép nhân như phương pháp thông thường).

Nó cũng có những ứng dụng quan trọng trong nhiều tác vụ không liên quan đến số học, vì nó có thể được sử dụng với bất kỳ phép toán nào có tính chất kết hợp:

$$(X \cdot Y) \cdot Z = X \cdot (Y \cdot Z)$$

Rõ ràng nhất, điều này áp dụng cho phép nhân modulo, phép nhân ma trận và các bài toán khác mà chúng ta sẽ thảo luận bên dưới.

## Algorithm

Raising $a$ to the power of $n$ is expressed naively as multiplication by $a$ done $n - 1$ times:
$a^{n} = a \cdot a \cdot \ldots \cdot a$. However, this approach is not practical for large $a$ or $n$.

$a^{b+c} = a^b \cdot a^c$ and $a^{2b} = a^b \cdot a^b = (a^b)^2$.

The idea of binary exponentiation is, that we split the work using the binary representation of the exponent.

Let's write $n$ in base 2, for example:

$$3^{13} = 3^{1101_2} = 3^8 \cdot 3^4 \cdot 3^1$$

Since the number $n$ has exactly $\lfloor \log_2 n \rfloor + 1$ digits in base 2, we only need to perform $O(\log n)$ multiplications, if we know the powers $a^1, a^2, a^4, a^8, \dots, a^{2^{\lfloor \log n \rfloor}}$.

So we only need to know a fast way to compute those.
Luckily this is very easy, since an element in the sequence is just the square of the previous element.

$$\begin{align}
3^1 &= 3 \\
3^2 &= \left(3^1\right)^2 = 3^2 = 9 \\
3^4 &= \left(3^2\right)^2 = 9^2 = 81 \\
3^8 &= \left(3^4\right)^2 = 81^2 = 6561
\end{align}$$

So to get the final answer for $3^{13}$, we only need to multiply three of them (skipping $3^2$ because the corresponding bit in $n$ is not set):
$3^{13} = 6561 \cdot 81 \cdot 3 = 1594323$

The final complexity of this algorithm is $O(\log n)$: we have to compute $\log n$ powers of $a$, and then have to do at most $\log n$ multiplications to get the final answer from them.

The following recursive approach expresses the same idea:

$$a^n = \begin{cases}
1 &\text{if } n == 0 \\
\left(a^{\frac{n}{2}}\right)^2 &\text{if } n > 0 \text{ and } n \text{ even}\\
\left(a^{\frac{n - 1}{2}}\right)^2 \cdot a &\text{if } n > 0 \text{ and } n \text{ odd}\\
\end{cases}$$

## Implementation

First the recursive approach, which is a direct translation of the recursive formula:

```cpp
long long binpow(long long a, long long b) {
    if (b == 0)
        return 1;
    long long res = binpow(a, b / 2);
    if (b % 2)
        return res * res * a;
    else
        return res * res;
}
```

The second approach accomplishes the same task without recursion.
It computes all the powers in a loop, and multiplies the ones with the corresponding set bit in $n$.
Although the complexity of both approaches is identical, this approach will be faster in practice since we don't have the overhead of the recursive calls.

```cpp
long long binpow(long long a, long long b) {
    long long res = 1;
    while (b > 0) {
        if (b & 1)
            res = res * a;
        a = a * a;
        b >>= 1;
    }
    return res;
}
```

## Applications

### Effective computation of large exponents modulo a number

**Problem:**
Compute $x^n \bmod m$.
This is a very common operation. For instance it is used in computing the [modular multiplicative inverse](module-inverse.md).

**Solution:**
Since we know that the modulo operator doesn't interfere with multiplications ($a \cdot b \equiv (a \bmod m) \cdot (b \bmod m) \pmod m$), we can directly use the same code, and just replace every multiplication with a modular multiplication:

```cpp
long long binpow(long long a, long long b, long long m) {
    a %= m;
    long long res = 1;
    while (b > 0) {
        if (b & 1)
            res = res * a % m;
        a = a * a % m;
        b >>= 1;
    }
    return res;
}
```

**Note:**
It's possible to speed this algorithm for large $b >> m$.
If $m$ is a prime number $x^n \equiv x^{n \bmod (m-1)} \pmod{m}$ for prime $m$, and $x^n \equiv x^{n \bmod{\phi(m)}} \pmod{m}$ for composite $m$.
This follows directly from Fermat's little theorem and Euler's theorem, see the article about [Modular Inverses](module-inverse.md#fermat-euler) for more details.

### Effective computation of Fibonacci numbers

**Problem:** Compute $n$-th Fibonacci number $F_n$.

**Solution:** For more details, see the [Fibonacci Number article](fibonacci-numbers.md).
We will only go through an overview of the algorithm.
To compute the next Fibonacci number, only the two previous ones are needed, as $F_n = F_{n-1} + F_{n-2}$.
We can build a $2 \times 2$ matrix that describes this transformation:
the transition from $F_i$ and $F_{i+1}$ to $F_{i+1}$ and $F_{i+2}$.
For example, applying this transformation to the pair $F_0$ and $F_1$ would change it into $F_1$ and $F_2$.
Therefore, we can raise this transformation matrix to the $n$-th power to find $F_n$ in time complexity $O(\log n)$.

### Applying a permutation $k$ times { data-toc-label='Applying a permutation <script type="math/tex">k</script> times' }

**Problem:** You are given a sequence of length $n$. Apply to it a given permutation $k$ times.

**Solution:** Simply raise the permutation to $k$-th power using binary exponentiation, and then apply it to the sequence. This will give you a time complexity of $O(n \log k)$.

```cpp
vector<int> applyPermutation(vector<int> sequence, vector<int> permutation) {
    vector<int> newSequence(sequence.size());
    for(int i = 0; i < sequence.size(); i++) {
        newSequence[i] = sequence[permutation[i]];
    }
    return newSequence;
}

vector<int> permute(vector<int> sequence, vector<int> permutation, long long k) {
    while (k > 0) {
        if (k & 1) {
            sequence = applyPermutation(sequence, permutation);
        }
        permutation = applyPermutation(permutation, permutation);
        k >>= 1;
    }
    return sequence;
}
```

**Note:** This task can be solved more efficiently in linear time by building the permutation graph and considering each cycle independently. You could then compute $k$ modulo the size of the cycle and find the final position for each number which is part of this cycle.

### Fast application of a set of geometric operations to a set of points

**Bài toán:** Cho $n$ điểm $p_i$, áp dụng $m$ phép biến đổi lên mỗi điểm trong số này. Mỗi phép biến đổi có thể là một phép dịch chuyển, một phép thay đổi tỉ lệ hoặc một phép quay quanh một trục nhất định với một góc nhất định. Cũng có một phép biến đổi "vòng lặp" áp dụng một danh sách các phép biến đổi nhất định $k$ lần (các phép biến đổi "vòng lặp" có thể lồng nhau). Bạn cần áp dụng tất cả các phép biến đổi nhanh hơn $O(n \cdot length)$, trong đó $length$ là tổng số phép biến đổi cần áp dụng (sau khi giải nén các phép biến đổi "vòng lặp").

**Giải pháp:** Hãy xem xét cách các loại phép biến đổi khác nhau thay đổi tọa độ:

* Phép dịch chuyển: cộng một hằng số khác nhau vào mỗi tọa độ.
* Phép thay đổi tỉ lệ: nhân mỗi tọa độ với một hằng số khác nhau.
* Phép quay: phép biến đổi này phức tạp hơn (chúng ta sẽ không đi vào chi tiết ở đây), nhưng mỗi tọa độ mới vẫn có thể được biểu diễn dưới dạng một kết hợp tuyến tính của các tọa độ cũ.

Như bạn thấy, mỗi phép biến đổi có thể được biểu diễn dưới dạng một phép biến đổi tuyến tính trên các tọa độ. Do đó, một phép biến đổi có thể được viết dưới dạng ma trận $4 \times 4$ theo dạng sau:

$$\begin{pmatrix}
a_{11} & a_ {12} & a_ {13} & a_ {14} \\
a_{21} & a_ {22} & a_ {23} & a_ {24} \\
a_{31} & a_ {32} & a_ {33} & a_ {34} \\
a_{41} & a_ {42} & a_ {43} & a_ {44}
\end{pmatrix}$$

that, when multiplied by a vector with the old coordinates and an unit gives a new vector with the new coordinates and an unit:

$$\begin{pmatrix} x & y & z & 1 \end{pmatrix} \cdot
\begin{pmatrix}
a_{11} & a_ {12} & a_ {13} & a_ {14} \\
a_{21} & a_ {22} & a_ {23} & a_ {24} \\
a_{31} & a_ {32} & a_ {33} & a_ {34} \\
a_{41} & a_ {42} & a_ {43} & a_ {44}
\end{pmatrix}
 = \begin{pmatrix} x' & y' & z' & 1 \end{pmatrix}$$

(Bạn sẽ tự hỏi rằng: Tại sao lại giới thiệu một tọa độ giả tưởng thứ tư? Đó là vẻ đẹp của [tọa độ đồng nhất](https://en.wikipedia.org/wiki/Homogeneous_coordinates), được ứng dụng rộng rãi trong đồ họa máy tính. Nếu không có điều này, chúng ta sẽ không thể thực hiện các phép biến đổi affine như phép dịch chuyển dưới dạng một phép nhân ma trận duy nhất, vì nó yêu cầu chúng ta cộng một hằng số vào các tọa độ. Phép biến đổi affine trở thành một phép biến đổi tuyến tính trong không gian chiều cao hơn!)

Dưới đây là một số ví dụ về cách các phép biến đổi được biểu diễn dưới dạng ma trận:

* Phép dịch chuyển: dịch chuyển tọa độ $x$ bởi $5$, tọa độ $y$ bởi $7$ và tọa độ $z$ bởi $9$.

$$\begin{pmatrix}
1 & 0 & 0 & 0 \\
0 & 1 & 0 & 0 \\
0 & 0 & 1 & 0 \\
5 & 7 & 9 & 1
\end{pmatrix}$$

* Phép biến đổi tỉ lệ: thay đổi tỉ lệ của tọa độ $x$ bởi $10$ và thay đổi tỉ lệ của hai tọa độ còn lại bởi $5$.

$$\begin{pmatrix}
10 & 0 & 0 & 0 \\
0 & 5 & 0 & 0 \\
0 & 0 & 5 & 0 \\
0 & 0 & 0 & 1
\end{pmatrix}$$

* Phép quay: quay góc $\theta$ quanh trục $x$ theo quy tắc tay phải (hướng ngược chiều kim đồng hồ).

$$\begin{pmatrix}
1 & 0 & 0 & 0 \\
0 & \cos \theta & -\sin \theta & 0 \\
0 & \sin \theta & \cos \theta & 0 \\
0 & 0 & 0 & 1
\end{pmatrix}$$

Bây giờ, khi mỗi phép biến đổi được mô tả dưới dạng một ma trận, chuỗi các phép biến đổi có thể được mô tả dưới dạng tích của các ma trận này, và một "loop" với $k$ lần lặp có thể được mô tả dưới dạng ma trận nâng lên lũy thừa $k$ (có thể tính toán bằng phép lũy thừa nhị phân với độ phức tạp $O(\log{k})$). Bằng cách này, ma trận đại diện cho tất cả các phép biến đổi có thể được tính toán trước trong $O(m \log{k})$, và sau đó có thể được áp dụng cho mỗi điểm trong số $n$ điểm với độ phức tạp $O(n)$ tổng cộng có độ phức tạp là $O(n + m \log{k})$.


### Number of paths of length $k$ in a graph

**Bài toán:** Cho đồ thị có hướng không trọng số với $n$ đỉnh, tìm số lượng các đoạn đường có độ dài $k$ từ bất kỳ đỉnh $u$ đến bất kỳ đỉnh $v$.

**Giải pháp:** Bài toán này được xem xét chi tiết hơn trong [một bài viết riêng](https://cp-algorithms.com/graph/fixed_length_paths.html). Thuật toán bao gồm việc nâng ma trận kề $M$ của đồ thị (một ma trận mà trong đó $m_{ij} = 1$ nếu có một cạnh từ $i$ đến $j$, hoặc $0$ nếu không có cạnh) lên lũy thừa $k$. Bây giờ, $m_{ij}$ sẽ là số lượng các đoạn đường có độ dài $k$ từ $i$ đến $j$. Độ phức tạp thời gian của giải pháp này là $O(n^3 \log k)$.

**Lưu ý:** Trong bài viết đó, một biến thể khác của bài toán này cũng được xem xét: khi các cạnh có trọng số và yêu cầu tìm đoạn đường có trọng số tối thiểu chứa chính xác $k$ cạnh. Như đã chỉ ra trong bài viết đó, bài toán này cũng được giải bằng cách nâng ma trận kề lên lũy thừa. Ma trận sẽ có trọng số của cạnh từ $i$ đến $j$, hoặc $\infty$ nếu không có cạnh nào như vậy.
Thay vì sử dụng phép nhân hai ma trận thông thường, một phép toán đã được sửa đổi nên được sử dụng: thay vì phép nhân, hai giá trị được cộng lại, và thay vì phép tổng, lấy giá trị nhỏ nhất. Cụ thể là: $result_{ij} = \min\limits_{1\ \leq\ k\ \leq\ n}(a_{ik} + b_{kj})$.

### Variation of binary exponentiation: multiplying two numbers modulo $m$

**Bài toán:** Nhân hai số $a$ và $b$ modulo $m$. $a$ và $b$ vừa vặn trong các kiểu dữ liệu có sẵn, nhưng tích của chúng quá lớn để chứa trong một số nguyên 64-bit. Ý tưởng là tính $a \cdot b \pmod m$ mà không sử dụng phép toán số học bignum.

**Giải pháp:** Chúng ta chỉ cần áp dụng thuật toán xây dựng nhị phân đã được mô tả ở trên, chỉ thực hiện phép cộng thay vì phép nhân. Nói cách khác, chúng ta đã "mở rộng"("expanded") phép nhân của hai số thành $O (\log m)$ phép cộng và phép nhân với hai (mà về bản chất là một phép cộng).

$$a \cdot b = \begin{cases}
0 &\text{if }a = 0 \\
2 \cdot \frac{a}{2} \cdot b &\text{nếu }a > 0 \text{ và }a \text{ chẵn} \\
2 \cdot \frac{a-1}{2} \cdot b + b &\text{nếu }a > 0 \text{ và }a \text{ lẻ}
\end{cases}$$

Lưu ý: Bạn có thể giải quyết bài toán này theo cách khác bằng cách sử dụng phép toán số thực. Đầu tiên, tính biểu thức $\frac{a \cdot b}{m}$ sử dụng số thực và chuyển nó thành một số nguyên không dấu $q$. Sau đó, trừ $q \cdot m$ ra khỏi $a \cdot b$ bằng phép toán số nguyên không dấu và lấy kết quả modulo $m$ để tìm đáp án. Giải pháp này có vẻ không đáng tin cậy, nhưng rất nhanh và rất dễ triển khai. Xem thêm thông tin tại [here](https://cs.stackexchange.com/questions/77016/modular-multiplication).
