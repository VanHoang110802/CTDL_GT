> Bài viết được dịch lại của trang: [cp-algorithm](https://cp-algorithms.com/data_structures/stack_queue_modification.html)

# Minimum stack / Minimum queue (Ngăn xếp tối thiểu / Hàng đợi tối thiểu)

In this article we will consider three problems: 
first we will modify a stack in a way that allows us to find the smallest element of the stack in $O(1)$, then we will do the same thing with a queue, and finally we will use these data structures to find the minimum in all subarrays of a fixed length in an array in $O(n)$

Trong bài viết này, chúng ta sẽ xem xét ba vấn đề:
Đầu tiên, chúng ta sẽ chỉnh sửa một ngăn xếp theo cách cho phép tìm phần tử nhỏ nhất trong ngăn xếp với thời gian $O(1)$, sau đó, chúng ta sẽ làm điều tương tự với một hàng đợi, và cuối cùng, chúng ta sẽ sử dụng các cấu trúc dữ liệu này để tìm phần tử nhỏ nhất trong tất cả các dãy con có độ dài cố định trong một mảng với thời gian $O(n)$


## Stack modification (chỉnh sửa ngăn xếp)

We want to modify the stack data structure in such a way, that it possible to find the smallest element in the stack in $O(1)$ time, while maintaining the same asymptotic behavior for adding and removing elements from the stack.
Quick reminder, on a stack we only add and remove elements on one end.

To do this, we will not only store the elements in the stack, but we will store them in pairs: the element itself and the minimum in the stack starting from this element and below.

Chúng ta muốn chỉnh sửa cấu trúc dữ liệu ngăn xếp sao cho có thể tìm phần tử nhỏ nhất trong ngăn xếp trong thời gian $O(1)$, đồng thời vẫn duy trì hành vi đồng dạng đối với việc thêm và loại bỏ phần tử khỏi ngăn xếp.
Nhắc lại nhanh, trong một ngăn xếp, chúng ta chỉ thêm và loại bỏ phần tử ở một đầu.

Để làm điều này, chúng ta không chỉ lưu trữ các phần tử trong ngăn xếp, mà còn lưu trữ chúng dưới dạng cặp: phần tử đó và phần tử nhỏ nhất trong ngăn xếp bắt đầu từ phần tử này và phía dưới.

```cpp
stack<pair<int, int>> st;
```

It is clear that finding the minimum in the whole stack consists only of looking at the value `stack.top().second`.

Rõ ràng là việc tìm phần tử nhỏ nhất trong toàn bộ ngăn xếp chỉ đơn giản là nhìn vào giá trị `stack.top().second`.

It is also obvious that adding or removing a new element to the stack can be done in constant time.

Điều này cũng rõ ràng rằng việc thêm hoặc loại bỏ một phần tử mới vào ngăn xếp có thể thực hiện trong thời gian hằng số.

Implementation:

* Adding an element:
```cpp
int new_min = st.empty() ? new_elem : min(new_elem, st.top().second);
st.push({new_elem, new_min});
```

* Removing an element:
```cpp
int removed_element = st.top().first;
st.pop();
```

* Finding the minimum:
```cpp
int minimum = st.top().second;
```

## Queue modification (method 1) (Chỉnh sửa hàng đợi)

Now we want to achieve the same operations with a queue, i.e. we want to add elements at the end and remove them from the front.

Here we consider a simple method for modifying a queue.
It has a big disadvantage though, because the modified queue will actually not store all elements.

The key idea is to only store the items in the queue that are needed to determine the minimum.
Namely we will keep the queue in nondecreasing order (i.e. the smallest value will be stored in the head), and of course not in any arbitrary way, the actual minimum has to be always contained in the queue.
This way the smallest element will always be in the head of the queue.
Before adding a new element to the queue, it is enough to make a "cut":
we will remove all trailing elements of the queue that are larger than the new element, and afterwards add the new element to the queue. 
This way we don't break the order of the queue, and we will also not loose the current element if it is at any subsequent step the minimum. 
All the elements that we removed can never be a minimum itself, so this operation is allowed.
When we want to extract an element from the head, it actually might not be there (because we removed it previously while adding a smaller element). 
Therefore when deleting an element from a queue we need to know the value of the element.
If the head of the queue has the same value, we can safely remove it, otherwise we do nothing.

Consider the implementations of the above operations:

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

* Finding the minimum:
```cpp
int minimum = q.front();
```

* Adding an element:
```cpp
while (!q.empty() && q.back() > new_element)
    q.pop_back();
q.push_back(new_element);
```

* Removing an element:
```cpp
if (!q.empty() && q.front() == remove_element)
    q.pop_front();
```

It is clear that on average all these operation only take $O(1)$ time (because every element can only be pushed and popped once).

Rõ ràng là trung bình, tất cả các thao tác này chỉ tốn thời gian $O(1)$ (bởi vì mỗi phần tử chỉ có thể được đẩy vào và loại bỏ một lần duy nhất)

## Queue modification (method 2) (Sửa đổi hàng đợi) 

This is a modification of method 1.
We want to be able to remove elements without knowing which element we have to remove.
We can accomplish that by storing the index for each element in the queue.
And we also remember how many elements we already have added and removed.

Đây là một sự chỉnh sửa của phương pháp 1. 
Chúng ta muốn có thể loại bỏ các phần tử mà không cần biết phần tử nào cần được loại bỏ.
Chúng ta có thể thực hiện điều đó bằng cách lưu trữ chỉ số của mỗi phần tử trong hàng đợi. 
Và chúng ta cũng nhớ số lượng phần tử mà chúng ta đã thêm vào và loại bỏ.


```cpp
deque<pair<int, int>> q;
int cnt_added = 0;
int cnt_removed = 0;
```

* Finding the minimum:
```cpp
int minimum = q.front().first;
```

* Adding an element:
```cpp
while (!q.empty() && q.back().first > new_element)
    q.pop_back();
q.push_back({new_element, cnt_added});
cnt_added++;
```

* Removing an element:
```cpp
if (!q.empty() && q.front().second == cnt_removed) 
    q.pop_front();
cnt_removed++;
```

## Queue modification (method 3)  (Chỉnh sửa hàng đợi)

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

## Finding the minimum for all subarrays of fixed length (Tìm giá trị nhỏ nhất cho tất cả các dãy con có độ dài cố định)

Suppose we are given an array $A$ of length $N$ and a given $M \le N$.
We have to find the minimum of each subarray of length $M$ in this array, i.e. we have to find:

Giả sử chúng ta được cho một mảng $A$ có độ dài $N$ và một giá trị $M \le N$.
Chúng ta phải tìm giá trị nhỏ nhất của mỗi dãy con có độ dài $M$ trong mảng này, tức là chúng ta phải tìm:

$$\min_{0 \le i \le M-1} A[i], \min_{1 \le i \le M} A[i], \min_{2 \le i \le M+1} A[i],~\dots~, \min_{N-M \le i \le N-1} A[i]$$

We have to solve this problem in linear time, i.e. $O(n)$.

We can use any of the three modified queues to solve the problem.
The solutions should be clear:
we add the first $M$ element of the array, find and output its minimum, then add the next element to the queue and remove the first element of the array, find and output its minimum, etc. 
Since all operations with the queue are performed in constant time on average, the complexity of the whole algorithm will be $O(n)$.

Chúng ta phải giải quyết bài toán này trong thời gian tuyến tính, tức là $O(n)$.

Chúng ta có thể sử dụng bất kỳ một trong ba hàng đợi đã chỉnh sửa để giải quyết bài toán.
Các giải pháp nên rõ ràng: chúng ta thêm $M$ phần tử đầu tiên của mảng, tìm và in ra giá trị nhỏ nhất của nó, sau đó thêm phần tử tiếp theo vào hàng đợi và loại bỏ phần tử đầu tiên của mảng, tìm và in ra giá trị nhỏ nhất của nó, v.v. 
Vì tất cả các thao tác với hàng đợi đều được thực hiện trong thời gian hằng số trung bình, độ phức tạp của toàn bộ thuật toán sẽ là $O(n)$.

## Practice Problems
* [Queries with Fixed Length](https://www.hackerrank.com/challenges/queries-with-fixed-length/problem)
* [Binary Land](https://www.codechef.com/MAY20A/problems/BINLAND)

