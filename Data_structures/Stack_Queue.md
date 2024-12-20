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
This way the smallest element will always be in the head of the queue.
Before adding a new element to the queue, it is enough to make a "cut":
we will remove all trailing elements of the queue that are larger than the new element, and afterwards add the new element to the queue. 
This way we don't break the order of the queue, and we will also not loose the current element if it is at any subsequent step the minimum. 
All the elements that we removed can never be a minimum itself, so this operation is allowed.
When we want to extract an element from the head, it actually might not be there (because we removed it previously while adding a smaller element). 
Therefore when deleting an element from a queue we need to know the value of the element.
If the head of the queue has the same value, we can safely remove it, otherwise we do nothing.

Consider the implementations of the above operations:

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

Here we consider another way of modifying a queue to find the minimum in $O(1)$.
This way is somewhat more complicated to implement, but this time we actually store all elements.
And we also can remove an element from the front without knowing its value.

The idea is to reduce the problem to the problem of stacks, which was already solved by us.
So we only need to learn how to simulate a queue using two stacks.

We make two stacks, `s1` and `s2`. 
Of course these stack will be of the modified form, so that we can find the minimum in $O(1)$. 
We will add new elements to the stack `s1`, and remove elements from the stack `s2`.
If at any time the stack `s2` is empty, we move all elements from `s1` to `s2` (which essentially reverses the order of those elements).
Finally finding the minimum in a queue involves just finding the minimum of both stacks.

Thus we perform all operations in $O(1)$ on average (each element will be once added to stack `s1`, once transferred to `s2`, and once popped from `s2`)

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
