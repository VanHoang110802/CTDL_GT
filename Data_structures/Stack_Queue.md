> Bài viết được dịch lại của trang: [cp-algorithm](https://cp-algorithms.com/data_structures/stack_queue_modification.html)

# Minimum stack / Minimum queue (Ngăn xếp tối thiểu / Hàng đợi tối thiểu)

Trong bài viết này, chúng ta sẽ xem xét ba vấn đề:
Đầu tiên, chúng ta sẽ chỉnh sửa một ngăn xếp theo cách cho phép tìm phần tử nhỏ nhất trong ngăn xếp với thời gian $O(1)$, sau đó, chúng ta sẽ làm điều tương tự với một hàng đợi, và cuối cùng, chúng ta sẽ sử dụng các cấu trúc dữ liệu này để tìm phần tử nhỏ nhất trong tất cả các dãy con có độ dài cố định trong một mảng với thời gian $O(n)$

## Stack modification (chỉnh sửa ngăn xếp)

Chúng ta muốn chỉnh sửa cấu trúc dữ liệu ngăn xếp sao cho có thể tìm phần tử nhỏ nhất trong ngăn xếp trong thời gian $O(1)$, đồng thời vẫn duy trì hành vi đồng dạng đối với việc thêm và loại bỏ phần tử khỏi ngăn xếp.
Nhắc lại nhanh, trong một ngăn xếp, chúng ta chỉ thêm và loại bỏ phần tử ở một đầu.

Để làm điều này, chúng ta không chỉ lưu trữ các phần tử trong ngăn xếp, mà còn lưu trữ chúng dưới dạng cặp: phần tử đó và phần tử nhỏ nhất trong ngăn xếp bắt đầu từ phần tử này và phía dưới.

```cpp
stack<pair<int, int>> st;
```

Rõ ràng là việc tìm phần tử nhỏ nhất trong toàn bộ ngăn xếp chỉ đơn giản là nhìn vào giá trị `stack.top().second`.

Điều này cũng rõ ràng rằng việc thêm hoặc loại bỏ một phần tử mới vào ngăn xếp có thể thực hiện trong thời gian hằng số.

Cài đặt:

* Thêm một phần tử:
```cpp
int new_min = st.empty() ? new_elem : min(new_elem, st.top().second);
st.push({new_elem, new_min});
```

* Xoá một phần tử:
```cpp
int removed_element = st.top().first;
st.pop();
```

* Tìm giá trị nhỏ nhất:
```cpp
int minimum = st.top().second;
```

## Queue modification (Chỉnh sửa hàng đợi) (method 1)

Giờ đây, chúng ta muốn thực hiện các thao tác tương tự với một hàng đợi, tức là chúng ta muốn thêm phần tử ở cuối và loại bỏ chúng từ đầu.

Ở đây, chúng ta xem xét một phương pháp đơn giản để chỉnh sửa một hàng đợi. 
Tuy nhiên, phương pháp này có một nhược điểm lớn, vì hàng đợi đã được chỉnh sửa thực tế sẽ không lưu trữ tất cả các phần tử.

Ý tưởng chính là chỉ lưu trữ các phần tử trong hàng đợi cần thiết để xác định giá trị nhỏ nhất. 
Cụ thể, chúng ta sẽ giữ hàng đợi theo thứ tự không giảm (tức là giá trị nhỏ nhất sẽ được lưu ở đầu), và tất nhiên không theo cách tùy ý, giá trị nhỏ nhất thực sự phải luôn có mặt trong hàng đợi.
Theo cách này, phần tử nhỏ nhất sẽ luôn ở đầu hàng đợi. 
Trước khi thêm một phần tử mới vào hàng đợi, chỉ cần thực hiện một 'cắt': chúng ta sẽ loại bỏ tất cả các phần tử phía sau trong hàng đợi mà lớn hơn phần tử mới, và sau đó thêm phần tử mới vào hàng đợi.
Cách này giúp chúng ta không làm thay đổi thứ tự của hàng đợi, và chúng ta cũng sẽ không mất phần tử hiện tại nếu nó là phần tử nhỏ nhất ở bất kỳ bước tiếp theo nào.
Tất cả các phần tử mà chúng ta đã loại bỏ sẽ không bao giờ là phần tử nhỏ nhất, vì vậy thao tác này là hợp lệ.
Khi chúng ta muốn lấy một phần tử từ đầu, thực tế nó có thể không có ở đó (bởi vì chúng ta đã loại bỏ nó trước đó khi thêm một phần tử nhỏ hơn).
Vì vậy, khi xóa một phần tử khỏi hàng đợi, chúng ta cần phải biết giá trị của phần tử đó.
Nếu phần tử ở đầu hàng đợi có cùng giá trị, chúng ta có thể an toàn loại bỏ nó, nếu không chúng ta sẽ không làm gì cả.

Hãy xem xét các triển khai của các thao tác trên:

```cpp
deque<int> q;
```

* Tìm giá trị nhỏ nhất:
```cpp
int minimum = q.front();
```

* Thêm một phần tử:
```cpp
while (!q.empty() && q.back() > new_element)
    q.pop_back();
q.push_back(new_element);
```

* Xoá một phần tử:
```cpp
if (!q.empty() && q.front() == remove_element)
    q.pop_front();
```

Rõ ràng là trung bình, tất cả các thao tác này chỉ tốn thời gian $O(1)$ (bởi vì mỗi phần tử chỉ có thể được đẩy vào và loại bỏ một lần duy nhất)

## Queue modification (Sửa đổi hàng đợi) (method 2)

Đây là một sự chỉnh sửa của phương pháp 1. 
Chúng ta muốn có thể loại bỏ các phần tử mà không cần biết phần tử nào cần được loại bỏ.
Chúng ta có thể thực hiện điều đó bằng cách lưu trữ chỉ số của mỗi phần tử trong hàng đợi. 
Và chúng ta cũng nhớ số lượng phần tử mà chúng ta đã thêm vào và loại bỏ.

```cpp
deque<pair<int, int>> q;
int cnt_added = 0;
int cnt_removed = 0;
```

* Tìm giá trị nhỏ nhất:
```cpp
int minimum = q.front().first;
```

* Thêm một phần tử:
```cpp
while (!q.empty() && q.back().first > new_element)
    q.pop_back();
q.push_back({new_element, cnt_added});
cnt_added++;
```

* Xoá một phần tử:
```cpp
if (!q.empty() && q.front().second == cnt_removed) 
    q.pop_front();
cnt_removed++;
```

## Queue modification (Chỉnh sửa hàng đợi) (method 3)

Ở đây, chúng ta xem xét một cách khác để chỉnh sửa hàng đợi nhằm tìm giá trị nhỏ nhất trong thời gian $O(1)$.
Cách này hơi phức tạp hơn để triển khai, nhưng lần này chúng ta thực sự lưu trữ tất cả các phần tử.
Và chúng ta cũng có thể loại bỏ một phần tử từ đầu mà không cần biết giá trị của nó.

Ý tưởng là giảm bài toán này thành bài toán về ngăn xếp, mà chúng ta đã giải quyết trước đó.
Vậy nên, chúng ta chỉ cần học cách mô phỏng một hàng đợi bằng hai ngăn xếp.

Chúng ta tạo hai ngăn xếp, `s1` and `s2`. 
Chắc chắn rằng những ngăn xếp này sẽ ở dạng đã được chỉnh sửa, để chúng ta có thể tìm giá trị nhỏ nhất trong thời gian $O(1)$. 
Chúng ta sẽ thêm các phần tử mới vào ngăn xếp `s1`, và loại bỏ các phần tử từ ngăn xếp `s2`.
Nếu vào bất kỳ thời điểm nào ngăn xếp `s2` rỗng, chúng ta sẽ chuyển tất cả các phần tử từ `s1` sang `s2` (điều này thực chất đảo ngược thứ tự của các phần tử).
Cuối cùng việc tìm giá trị nhỏ nhất trong hàng đợi chỉ đơn giản là tìm giá trị nhỏ nhất của cả hai ngăn xếp.

Do đó, chúng ta thực hiện tất cả các thao tác trong thời gian $O(1)$ trung bình (mỗi phần tử sẽ được thêm vào ngăn xếp `s1` một lần, chuyển sang `s2`một lần, và bị loại bỏ khỏi `s2` một lần).

Implementation:

```cpp
stack<pair<int, int>> s1, s2;
```

* Finding the minimum:
```cpp
if (s1.empty() || s2.empty()) 
    minimum = s1.empty() ? s2.top().second : s1.top().second;
else
    minimum = min(s1.top().second, s2.top().second);
```

* Add element:
```cpp
int minimum = s1.empty() ? new_element : min(new_element, s1.top().second);
s1.push({new_element, minimum});
```

* Removing an element:
```cpp
if (s2.empty()) {
    while (!s1.empty()) {
        int element = s1.top().first;
        s1.pop();
        int minimum = s2.empty() ? element : min(element, s2.top().second);
        s2.push({element, minimum});
    }
}
int remove_element = s2.top().first;
s2.pop();
```

## Finding the minimum for all subarrays of fixed length (Tìm giá trị nhỏ nhất cho tất cả các dãy con có độ dài cố định.)

Suppose we are given an array $A$ of length $N$ and a given $M \le N$.
We have to find the minimum of each subarray of length $M$ in this array, i.e. we have to find:

$$\min_{0 \le i \le M-1} A[i], \min_{1 \le i \le M} A[i], \min_{2 \le i \le M+1} A[i],~\dots~, \min_{N-M \le i \le N-1} A[i]$$

We have to solve this problem in linear time, i.e. $O(n)$.

We can use any of the three modified queues to solve the problem.
The solutions should be clear:
we add the first $M$ element of the array, find and output its minimum, then add the next element to the queue and remove the first element of the array, find and output its minimum, etc. 
Since all operations with the queue are performed in constant time on average, the complexity of the whole algorithm will be $O(n)$.
