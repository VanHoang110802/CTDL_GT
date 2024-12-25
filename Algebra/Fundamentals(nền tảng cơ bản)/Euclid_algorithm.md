> Bài viết được dịch tại: [cp-algorithms](https://cp-algorithms.com/algebra/euclid-algorithm.html)

# Euclidean algorithm for computing the greatest common divisor

Given two non-negative integers $a$ and $b$, we have to find their **GCD** (greatest common divisor), i.e. the largest number which is a divisor of both $a$ and $b$.
It's commonly denoted by $\gcd(a, b)$. Mathematically it is defined as:

$$\gcd(a, b) = \max \{k > 0 : (k \mid a) \text{ and } (k \mid b) \}$$

(here the symbol "$\mid$" denotes divisibility, i.e. "$k \mid a$" means "$k$ divides $a$")

Khi một trong các số là zero, trong khi số còn lại là khác không, ước chung lớn nhất của chúng, theo định nghĩa, là số thứ hai. Khi cả hai số đều là zero, ước chung lớn nhất của chúng không xác định (nó có thể là bất kỳ số nào tùy ý), nhưng để bảo tồn tính kết hợp của $\gcd$ , ta định nghĩa nó là zero. Điều này cho ta một quy tắc đơn giản: nếu một trong các số là zero, ước chung lớn nhất là số còn lại.

Thuật toán Euclid, được thảo luận dưới đây, cho phép tìm ước chung lớn nhất của hai số $a$ và $b$ trong thời gian $O(\log \min(a, b))$. Vì hàm này là **kết hợp (associative)**, để tìm ước chung lớn nhất của **hơn hai số (more than two numbers)**, ta có thể thực hiện $\gcd(a, b, c) = \gcd(a, \gcd(b, c))$ và tiếp tục như vậy.

Thuật toán này lần đầu tiên được mô tả trong "Elements" của Euclid (khoảng 300 TCN), nhưng có thể thuật toán này có nguồn gốc từ trước đó.

## Algorithm

Thuật toán Euclid ban đầu được trình bày như sau: trừ số nhỏ hơn khỏi số lớn hơn cho đến khi một trong hai số trở thành số không. Thực vậy, nếu $g$ chia $a$ và $b$, thì nó cũng chia $a-b$. Mặt khác, nếu $g$ chia $a-b$ và $b$, thì nó cũng chia $a = b + (a-b)$, điều này có nghĩa là tập các ước chung của $\{a, b\}$ và $\{b,a-b\}$ là giống nhau.

Lưu ý rằng $a$ vẫn là số lớn hơn cho đến khi $b$ bị trừ khỏi nó ít nhất $\left\lfloor\frac{a}{b}\right\rfloor$ lần. Do đó, để tăng tốc, $a-b$ được thay thế bằng $a-\left\lfloor\frac{a}{b}\right\rfloor b = a \bmod b$. Sau đó, thuật toán được trình bày một cách vô cùng đơn giản:

$$\gcd(a, b) = \begin{cases}a,&\text{nếu }b = 0 \\ \gcd(b, a \bmod b),&\text{ngược lại.}\end{cases}$$

## Implementation

```cpp
int gcd (int a, int b) {
    if (b == 0)
        return a;
    else
        return gcd (b, a % b);
}
```

Using the ternary operator in C++, we can write it as a one-liner.

```cpp
int gcd (int a, int b) {
    return b ? gcd (b, a % b) : a;
}
```

And finally, here is a non-recursive implementation:

```cpp
int gcd (int a, int b) {
    while (b) {
        a %= b;
        swap(a, b);
    }
    return a;
}
```

Note that since C++17, `gcd` is implemented as a [standard function](https://en.cppreference.com/w/cpp/numeric/gcd) in C++.

## Time Complexity

Thời gian chạy của thuật toán được ước tính bởi định lý Lamé's, định lý này thiết lập một mối liên hệ bất ngờ giữa thuật toán Euclid và dãy số Fibonacci:

Nếu $a > b \geq 1$ và $b < F_n$ với một số $n$, thuật toán Euclid sẽ thực hiện tối đa $n-2$ lần gọi đệ quy.

Hơn nữa, có thể chứng minh rằng giới hạn trên của định lý này là tối ưu. Khi $a = F_n$ và $b = F_{n-1}$, $gcd(a, b)$ sẽ thực hiện chính xác $n-2$ lần gọi đệ quy. Nói cách khác, các số Fibonacci liên tiếp là đầu vào xấu nhất cho thuật toán Euclid.

Vì các số Fibonacci tăng theo cấp số mũ, ta có thể kết luận rằng thuật toán Euclid hoạt động trong thời gian $O(\log \min(a, b))$.

Một cách khác để ước tính độ phức tạp là nhận thấy rằng $a \bmod b$ đối với trường hợp $a \geq b$ sẽ ít nhất nhỏ hơn $a$ gấp 2 lần, do đó số lớn hơn sẽ bị giảm ít nhất một nửa trong mỗi lần lặp của thuật toán. Áp dụng lý luận này vào trường hợp khi ta tính GCD của tập hợp các số $a_1,\dots,a_n \leq C$, điều này cũng cho phép ước tính tổng thời gian chạy là $O(n + \log C)$, thay vì $O(n \log C)$, vì mỗi lần lặp không tầm thường của thuật toán sẽ giảm ứng viên GCD hiện tại ít nhất một yếu tố hệ số là $2$.

## Least common multiple

Việc tính toán bội chung nhỏ nhất (thường được ký hiệu là **LCM**) có thể được rút gọn thành việc tính GCD với công thức đơn giản sau:

$$\text{lcm}(a, b) = \frac{a \cdot b}{\gcd(a, b)}$$

Do đó, LCM (Bội chung nhỏ nhất) có thể được tính bằng thuật toán Euclid với độ phức tạp thời gian giống như vậy:

Một cách triển khai có thể, khéo léo tránh tràn số nguyên bằng cách chia $a$ cho GCD trước, được đưa ra dưới đây:

```cpp
int lcm (int a, int b) {
    return a / gcd(a, b) * b;
}
```

## Binary GCD

Thuật toán GCD nhị phân là một tối ưu hóa của thuật toán Euclid thông thường.

Phần chậm trong thuật toán thông thường là các phép toán modulo. Mặc dù các phép toán modulo được xem là $O(1)$, nhưng chúng chậm hơn rất nhiều so với các phép toán đơn giản như cộng, trừ hoặc các phép toán bitwise. Vì vậy, tốt hơn là nên tránh sử dụng chúng.

Thật ra, bạn có thể thiết kế một thuật toán GCD nhanh mà không sử dụng phép toán modulo. Thuật toán này dựa trên một số đặc tính:

  - Nếu cả hai số đều chẵn, thì ta có thể lấy ra một yếu tố hai từ cả hai và tính GCD của các số còn lại: $\gcd(2a, 2b) = 2 \gcd(a, b)$.
  - Nếu một số là chẵn và số còn lại là lẻ, ta có thể loại bỏ yếu tố 2 từ số chẵn: $\gcd(2a, b) = \gcd(a, b)$ nếu $b$ là lẻ.
  - Nếu cả hai số đều lẻ, thì phép trừ một số với số còn lại sẽ không thay đổi GCD: $\gcd(a, b) = \gcd(b, a-b)$

Chỉ sử dụng những đặc tính này, cùng với một số hàm bitwise nhanh từ GCC, chúng ta có thể triển khai một phiên bản nhanh.

```cpp
int gcd(int a, int b) {
    if (!a || !b)
        return a | b;
    unsigned shift = __builtin_ctz(a | b);
    a >>= __builtin_ctz(a);
    do {
        b >>= __builtin_ctz(b);
        if (a > b)
            swap(a, b);
        b -= a;
    } while (b);
    return a << shift;
}
```

Lưu ý rằng, tối ưu hóa như vậy thường không cần thiết, và hầu hết các ngôn ngữ lập trình đã có sẵn hàm GCD trong thư viện chuẩn của chúng. Ví dụ, C++17 có hàm `std::gcd` trong thư viện `numeric`.
