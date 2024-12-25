> Bài viết được dịch tại: [cp-algorithms](https://cp-algorithms.com/algebra/binary-exp.html)

# Binary Exponentiation

Phép lũy thừa nhị phân (còn được biết đến là phép lũy thừa bằng phương pháp bình phương) là một mẹo cho phép tính toán $a^n$ chỉ với $O(\log n)$ phép nhân (thay vì $O(n)$ phép nhân như phương pháp thông thường).

Nó cũng có những ứng dụng quan trọng trong nhiều tác vụ không liên quan đến số học, vì nó có thể được sử dụng với bất kỳ phép toán nào có tính chất kết hợp:

$$(X \cdot Y) \cdot Z = X \cdot (Y \cdot Z)$$

Rõ ràng nhất, điều này áp dụng cho phép nhân modulo, phép nhân ma trận và các bài toán khác mà chúng ta sẽ thảo luận bên dưới.

## Algorithm

Nâng $a$ lên lũy thừa $n$ được biểu diễn đơn giản như phép nhân $a$ thực hiện $n - 1$ lần:
$a^{n} = a \cdot a \cdot \ldots \cdot a$. Tuy nhiên, phương pháp này không thực tế cho các giá trị lớn của $a$ hoặc $n$.

$a^{b+c} = a^b \cdot a^c$ và $a^{2b} = a^b \cdot a^b = (a^b)^2$.

Ý tưởng của phép nâng lũy thừa nhị phân là chúng ta chia công việc sử dụng biểu diễn nhị phân của số mũ.

Hãy viết $n$ dưới dạng cơ số 2, ví dụ:

$$3^{13} = 3^{1101_2} = 3^8 \cdot 3^4 \cdot 3^1$$

Vì số $n$ có chính xác $\lfloor \log_2 n \rfloor + 1$ chữ số trong hệ cơ số 2, chúng ta chỉ cần thực hiện $O(\log n)$ phép nhân, nếu chúng ta biết các lũy thừa của $a$ với các số mũ dạng $a^1, a^2, a^4, a^8, \dots, a^{2^{\lfloor \log n \rfloor}}$.

Vì vậy, chúng ta chỉ cần biết một cách nhanh chóng để tính toán các giá trị đó. 
May mắn thay, điều này rất dễ dàng, vì một phần tử trong dãy này chỉ là bình phương của phần tử trước đó.

$$\begin{align}
3^1 &= 3 \\
3^2 &= \left(3^1\right)^2 = 3^2 = 9 \\
3^4 &= \left(3^2\right)^2 = 9^2 = 81 \\
3^8 &= \left(3^4\right)^2 = 81^2 = 6561
\end{align}$$

Vì vậy, để có được kết quả cuối cùng cho $3^{13}$, chúng ta chỉ cần nhân ba số trong đó (bỏ qua $3^2$ vì bit tương ứng trong $n$ không được bật):
$3^{13} = 6561 \cdot 81 \cdot 3 = 1594323$

Độ phức tạp cuối cùng của thuật toán này là $O(\log n)$: chúng ta phải tính $\log n$ lũy thừa của $a$, và sau đó thực hiện tối đa $\log n$ phép nhân để có được kết quả cuối cùng.

Cách tiếp cận đệ quy sau đây diễn đạt cùng một ý tưởng:

$$a^n = \begin{cases}
1 &\text{nếu } n == 0 \\
\left(a^{\frac{n}{2}}\right)^2 &\text{nếu } n > 0 \text{ và } n \text{ chẵn}\\
\left(a^{\frac{n - 1}{2}}\right)^2 \cdot a &\text{nếu } n > 0 \text{ và } n \text{ lẻ}\\
\end{cases}$$

## Implementation

Đầu tiên là cách tiếp cận đệ quy, đây là bản dịch trực tiếp của công thức đệ quy:

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

Cách tiếp cận thứ hai thực hiện cùng một nhiệm vụ mà không cần đệ quy.
Nó tính tất cả các lũy thừa trong một vòng lặp, và nhân các lũy thừa có bit tương ứng được đặt trong $n$.
Mặc dù độ phức tạp của cả hai cách tiếp cận là giống nhau, nhưng cách tiếp cận này sẽ nhanh hơn trong thực tế vì chúng ta không phải chịu chi phí của các lời gọi đệ quy.

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

**Bài toán:**
Tính $x^n \bmod m$.
Đây là một phép toán rất phổ biến. Ví dụ, nó được sử dụng trong việc tính [modular multiplicative inverse](module-inverse.md).

**Giải quyết:**
Vì chúng ta biết rằng toán tử modulo không làm ảnh hưởng đến phép nhân ($a \cdot b \equiv (a \bmod m) \cdot (b \bmod m) \pmod m$), chúng ta có thể trực tiếp sử dụng mã nguồn tương tự, và chỉ cần thay thế mỗi phép nhân bằng phép nhân modulo:

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

**Lưu ý:**
Chúng ta có thể tăng tốc thuật toán này cho các giá trị lớn của $b >> m$.
Nếu $m$ là một số nguyên tố, ta có $x^n \equiv x^{n \bmod (m-1)} \pmod{m}$ đối với số nguyên tố $m$, và $x^n \equiv x^{n \bmod{\phi(m)}} \pmod{m}$ đối với số $m$ là hợp số.
Điều này được suy ra trực tiếp từ định lý nhỏ của Fermat và định lý Euler, xem bài viết về [Modular Inverses](https://cp-algorithms.com/algebra/module-inverse.html#fermat-euler) để biết thêm chi tiết.

### Effective computation of Fibonacci numbers

**Bài toán:** Tính số Fibonacci thứ $n$ $F_n$.

**Giải quyết:** Để biết thêm chi tiết, xem bài viết [Fibonacci Number article](https://cp-algorithms.com/algebra/fibonacci-numbers.html).
Chúng ta chỉ đi qua một cái nhìn tổng quan về thuật toán.
Để tính số Fibonacci tiếp theo, chỉ cần hai số trước đó, vì $F_n = F_{n-1} + F_{n-2}$.
Chúng ta có thể xây dựng một ma trận $2 \times 2$ mô tả phép biến đổi này:
chuyển từ $F_i$ và $F_{i+1}$ sang tới $F_{i+1}$ và $F_{i+2}$.
Ví dụ, áp dụng phép biến đổi này lên cặp số $F_0$ và $F_1$ sẽ chuyển nó thành $F_1$ và $F_2$.
Do đó, chúng ta có thể nâng ma trận biến đổi này lên lũy thừa $n$ để tìm $F_n$ với độ phức tạp thời gian là $O(\log n)$.

### Applying a permutation $k$ times

**Bài toán:** Bạn được cho một dãy có độ dài là $n$. Áp dụng một hoán vị đã cho lên dãy này $k$ lần.

**Giải quyết:** Chỉ cần nâng hoán vị lên lũy thừa $k$ bằng cách sử dụng phép nâng lũy thừa nhị phân, sau đó áp dụng nó lên dãy. Điều này sẽ cho bạn độ phức tạp thời gian là $O(n \log k)$.

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

**Lưu ý:** Bài toán này có thể được giải quyết hiệu quả hơn trong thời gian tuyến tính bằng cách xây dựng đồ thị hoán vị và xem xét mỗi chu trình một cách độc lập. Sau đó, bạn có thể tính $k$ modulo kích thước của chu trình và tìm vị trí cuối cùng cho mỗi số thuộc chu trình này.

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

Điều này, khi nhân với một vectơ có tọa độ cũ và một đơn vị, sẽ cho ra một vectơ mới với tọa độ mới và một đơn vị:

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

Bây giờ, khi mỗi phép biến đổi được mô tả dưới dạng một ma trận, chuỗi các phép biến đổi có thể được mô tả dưới dạng tích của các ma trận này, và một "loop" với $k$ lần lặp có thể được mô tả dưới dạng ma trận nâng lên lũy thừa $k$ (có thể tính toán bằng phép lũy thừa nhị phân với độ phức tạp ${O(\log{k})}$. Bằng cách này, ma trận đại diện cho tất cả các phép biến đổi có thể được tính toán trước trong $O(m \log{k})$, và sau đó có thể được áp dụng cho mỗi điểm trong số $n$ điểm với độ phức tạp $O(n)$ tổng cộng có độ phức tạp là ${O(n + m \log{k})}$.


### Number of paths of length $k$ in a graph

**Bài toán:** Cho đồ thị có hướng không trọng số với $n$ đỉnh, tìm số lượng các đoạn đường có độ dài $k$ từ bất kỳ đỉnh $u$ đến bất kỳ đỉnh $v$.

**Giải pháp:** Bài toán này được xem xét chi tiết hơn trong [một bài viết riêng](https://cp-algorithms.com/graph/fixed_length_paths.html). Thuật toán bao gồm việc nâng ma trận kề $M$ của đồ thị (một ma trận mà trong đó $m_{ij} = 1$ nếu có một cạnh từ $i$ đến $j$, hoặc $0$ nếu không có cạnh) lên lũy thừa $k$. Bây giờ, $m_{ij}$ sẽ là số lượng các đoạn đường có độ dài $k$ từ $i$ đến $j$. Độ phức tạp thời gian của giải pháp này là $O(n^3 \log k)$.

**Lưu ý:** Trong bài viết đó, một biến thể khác của bài toán này cũng được xem xét: khi các cạnh có trọng số và yêu cầu tìm đoạn đường có trọng số tối thiểu chứa chính xác $k$ cạnh. Như đã chỉ ra trong bài viết đó, bài toán này cũng được giải bằng cách nâng ma trận kề lên lũy thừa. Ma trận sẽ có trọng số của cạnh từ $i$ đến $j$, hoặc $\infty$ nếu không có cạnh nào như vậy.
Thay vì sử dụng phép nhân hai ma trận thông thường, một phép toán đã được sửa đổi nên được sử dụng: thay vì phép nhân, hai giá trị được cộng lại, và thay vì phép tổng, lấy giá trị nhỏ nhất. Cụ thể là: $result_{ij} = \min\limits_{1\ \leq\ k\ \leq\ n}(a_{ik} + b_{kj})$.

### Variation of binary exponentiation: multiplying two numbers modulo $m$

**Bài toán:** Nhân hai số $a$ và $b$ modulo $m$. $a$ và $b$ vừa vặn trong các kiểu dữ liệu có sẵn, nhưng tích của chúng quá lớn để chứa trong một số nguyên 64-bit. Ý tưởng là tính $a \cdot b \pmod m$ mà không sử dụng phép toán số học bignum.

**Giải pháp:** Chúng ta chỉ cần áp dụng thuật toán xây dựng nhị phân đã được mô tả ở trên, chỉ thực hiện phép cộng thay vì phép nhân. Nói cách khác, chúng ta đã "mở rộng"("expanded") phép nhân của hai số thành $O (\log m)$ phép cộng và phép nhân với hai (mà về bản chất là một phép cộng).

$$a \cdot b = \begin{cases}
0 &\text{nếu }a = 0 \\
2 \cdot \frac{a}{2} \cdot b &\text{nếu }a > 0 \text{ và }a \text{ chẵn} \\
2 \cdot \frac{a-1}{2} \cdot b + b &\text{nếu }a > 0 \text{ và }a \text{ lẻ}
\end{cases}$$

Lưu ý: Bạn có thể giải quyết bài toán này theo cách khác bằng cách sử dụng phép toán số thực. Đầu tiên, tính biểu thức $\frac{a \cdot b}{m}$ sử dụng số thực và chuyển nó thành một số nguyên không dấu $q$. Sau đó, trừ $q \cdot m$ ra khỏi $a \cdot b$ bằng phép toán số nguyên không dấu và lấy kết quả modulo $m$ để tìm đáp án. Giải pháp này có vẻ không đáng tin cậy, nhưng rất nhanh và rất dễ triển khai. Xem thêm thông tin tại [here](https://cs.stackexchange.com/questions/77016/modular-multiplication).
