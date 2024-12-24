> Bài viết được dịch lại của trang: [cp-algorithm](https://cp-algorithms.com/data_structures/segment_tree.html)

# Segment Tree

Cây Segment (Segment Tree) là một cấu trúc dữ liệu lưu trữ thông tin về các khoảng (intervals) trong mảng dưới dạng một cây. Điều này cho phép trả lời các truy vấn theo khoảng trên mảng một cách hiệu quả, đồng thời vẫn đủ linh hoạt để cho phép thay đổi nhanh chóng mảng.
Điều này bao gồm việc tìm tổng các phần tử liên tiếp trong mảng $a[l \dots r]$, hoặc tìm phần tử nhỏ nhất trong một khoảng như vậy với thời gian $O(\log n)$.
Trong quá trình trả lời các truy vấn như vậy, Cây Segment cho phép sửa đổi mảng bằng cách thay thế một phần tử, hoặc thậm chí thay đổi các phần tử của một đoạn con (ví dụ: gán tất cả các phần tử $a[l \dots r]$ với một giá trị nào đó, hoặc cộng một giá trị vào tất cả các phần tử trong đoạn con đó).

Nói chung, Cây Segment là một cấu trúc dữ liệu rất linh hoạt, và một lượng lớn các bài toán có thể được giải quyết bằng nó. 
Ngoài ra, cũng có thể áp dụng các phép toán phức tạp hơn và trả lời các truy vấn phức tạp hơn.
Cụ thể, Cây Segment có thể dễ dàng được tổng quát hóa cho các chiều lớn hơn.
Chẳng hạn, với Cây Segment hai chiều, bạn có thể trả lời các truy vấn về tổng hoặc giá trị nhỏ nhất trên một subrectangle (hình chữ nhật con) của một ma trận cho trước trong chỉ $O(\log^2 n)$ thời gian.

Một đặc điểm quan trọng của Cây Segment là chúng chỉ yêu cầu một lượng bộ nhớ tuyến tính. 
Cây Segment chuẩn yêu cầu $4n$ đỉnh để hoạt động trên một mảng có kích thước $n$.

## Dạng đơn giản nhất của Cây Segment (Simplest form of a Segment Tree)

Để bắt đầu một cách dễ dàng, chúng ta xem xét dạng đơn giản nhất của Cây Segment.
Chúng ta muốn trả lời các truy vấn về tổng một cách hiệu quả.
Định nghĩa chính thức của bài toán của chúng ta là:
Cho một mảng $a[0 \dots n-1]$, Cây Segment phải có khả năng tìm tổng các phần tử giữa các chỉ số $l$ and $r$ (tức là tính tổng $\sum_{i=l}^r a[i]$), và cũng phải xử lý việc thay đổi giá trị của các phần tử trong mảng (tức là thực hiện các phép gán dưới dạng $a[i] = x$).
Cây Segment phải có khả năng xử lý cả hai truy vấn trong thời gian $O(\log n)$.

Đây là một cải tiến so với các phương pháp đơn giản hơn.
Một cách triển khai mảng ngây thơ - chỉ sử dụng một mảng đơn giản - có thể cập nhật các phần tử trong thời gian $O(1)$, nhưng yêu cầu $O(n)$ để tính mỗi truy vấn tổng.
Còn việc tính tổng truy vấn bằng cách sử dụng mảng tổng tiền tố đã được tính trước có thể thực hiện trong $O(1)$, nhưng việc cập nhật một phần tử trong mảng lại yêu cầu phải thay đổi $O(n)$ giá trị trong mảng tổng tiền tố.

### Cấu trúc của Cây Đoạn (Structure of the Segment Tree)

Chúng ta có thể áp dụng phương pháp chia để trị (divide-and-conquer) khi làm việc với các đoạn mảng.
Chúng ta tính toán và lưu trữ tổng của tất cả các phần tử trong mảng, tức là tổng của đoạn $a[0 \dots n-1]$. 
Sau đó, chúng ta chia mảng thành hai phần: $a[0 \dots n/2-1]$ và $a[n/2 \dots n-1]$ và tính tổng của mỗi phần rồi lưu trữ chúng. 
Mỗi phần trong số hai phần này lại tiếp tục được chia đôi, và quá trình này lặp lại cho đến khi tất cả các đoạn mảng có kích thước bằng $1$. 

Chúng ta có thể coi các đoạn mảng này tạo thành một cây nhị phân:
Gốc của cây này là đoạn $a[0 \dots n-1]$, và mỗi đỉnh (ngoại trừ các đỉnh lá) có chính xác hai đỉnh con. 
Đây là lý do tại sao cấu trúc dữ liệu này được gọi là "Cây Segment", mặc dù trong hầu hết các triển khai, cây không được xây dựng một cách rõ ràng. (see [Implementation](segment_tree.md#implementation)).

Dưới đây là một biểu diễn trực quan của Cây Segment như vậy trên mảng $a = [1, 3, -2, 8, -7]$:

![sum-segment-tree](https://github.com/user-attachments/assets/aa6b7220-b0d0-4ef0-9b67-63084d43ef0e)

Từ mô tả ngắn gọn về cấu trúc dữ liệu này, chúng ta có thể kết luận rằng một Cây Segment chỉ yêu cầu một số lượng đỉnh tuyến tính.
TCấp đầu tiên của cây chứa một đỉnh duy nhất (gốc), cấp thứ hai sẽ chứa hai đỉnh, cấp thứ ba sẽ chứa bốn đỉnh, và cứ tiếp tục như vậy cho đến khi số lượng đỉnh đạt $n$. 
Do đó, số lượng đỉnh trong trường hợp xấu nhất có thể được ước tính bằng tổng $1 + 2 + 4 + \dots + 2^{\lceil\log_2 n\rceil} \lt 2^{\lceil\log_2 n\rceil + 1} \lt 4n$.

Cần lưu ý rằng khi $n$ không phải là một số mũ của hai, không phải tất cả các cấp của Cây Segment sẽ được điền đầy đủ.
Chúng ta có thể thấy hành vi này trong hình ảnh.
Hiện tại, chúng ta có thể bỏ qua thực tế này, nhưng nó sẽ trở nên quan trọng sau này trong quá trình triển khai.

Chiều cao của Cây Segment là $O(\log n)$, vì khi di chuyển từ gốc đến các lá, kích thước của các đoạn mảng giảm dần một cách gần như gấp đôi.

### Xây dựng (Construction)

Trước khi xây dựng cây đoạn, chúng ta cần quyết định rằng:

1. the *value* được lưu trữ tại mỗi nút của cây phân đoạn.
   Ví dụ, trong cây phân đoạn tổng, một nút sẽ lưu trữ tổng của các phần tử trong phạm vi $[l, r]$ của nó.
2. the *merge* operation dùng để hợp nhất hai nút con trong cây đoạn.
   Ví dụ, trong cây đoạn tổng, hai nút tương ứng với các phạm vi $a[l_1 \dots r_1]$ và $a[l_2 \dots r_2]$ sẽ được hợp nhất thành một nút tương ứng với phạm vi $a[l_1 \dots r_2]$ bằng cách cộng giá trị của hai nút này lại với nhau.

Lưu ý rằng một đỉnh là "đỉnh lá" (leaf vertex) nếu đoạn tương ứng của nó chỉ bao phủ một giá trị trong mảng gốc. Nó xuất hiện ở cấp thấp nhất của cây đoạn. Giá trị của nó sẽ bằng với phần tử (tương ứng) $a[i]$.

Bây giờ, để xây dựng cây đoạn, chúng ta bắt đầu từ cấp thấp nhất (các đỉnh lá) và gán cho chúng các giá trị tương ứng. Dựa trên các giá trị này, chúng ta có thể tính toán giá trị của cấp trước đó, sử dụng hàm `merge` .
Và dựa trên các giá trị đó, chúng ta có thể tính toán giá trị của cấp trước đó, và lặp lại quy trình cho đến khi đạt đến đỉnh gốc.

Thật tiện lợi khi mô tả phép toán này một cách đệ quy theo hướng ngược lại, tức là từ đỉnh gốc đến các đỉnh lá. Quy trình xây dựng, nếu được gọi trên một đỉnh không phải lá, sẽ thực hiện các bước sau:

1. Đệ quy xây dựng các giá trị của hai đỉnh con.
2. Hợp nhất các giá trị đã tính toán của các đỉnh con này.

Chúng ta bắt đầu xây dựng từ đỉnh gốc, và do đó, có thể tính toán toàn bộ cây phân đoạn.

Độ phức tạp thời gian của quá trình xây dựng này là $O(n)$, giả sử rằng phép hợp nhất có độ phức tạp thời gian hằng số (phép hợp nhất được gọi $n$ lần, tương đương với số lượng các nút nội tại trong cây đoạn).

### Truy vấn tổng (Sum queries)

Hiện tại, chúng ta sẽ trả lời các truy vấn tổng. Như một đầu vào, chúng ta nhận hai số nguyên $l$ và $r$, và chúng ta phải tính tổng của đoạn $a[l \dots r]$ trong thời gian $O(\log n)$. 

Để làm điều này, chúng ta sẽ duyệt qua Cây Phân Đoạn và sử dụng các tổng đã được tính toán trước của các đoạn.
Giả sử rằng chúng ta hiện đang ở đỉnh bao phủ đoạn $a[tl \dots tr]$.
Có ba trường hợp khả thi.

Trường hợp dễ nhất là khi đoạn $a[l \dots r]$ bằng với đoạn tương ứng của đỉnh hiện tại (tức là $a[l \dots r] = a[tl \dots tr]$), khi đó chúng ta đã hoàn thành và có thể trả về tổng đã được tính toán trước và lưu trữ trong đỉnh.

Ngoài ra, đoạn của truy vấn có thể rơi hoàn toàn vào miền của một trong hai đỉnh con.
Nhớ rằng đỉnh trái bao phủ đoạn $a[tl \dots tm]$ và đỉnh phải bao phủ đoạn $a[tm + 1 \dots tr]$ với $tm = (tl + tr) / 2$. 
Trong trường hợp này, chúng ta có thể đơn giản di chuyển đến đỉnh con mà đoạn tương ứng của nó bao phủ đoạn truy vấn, và thực hiện thuật toán đã được mô tả ở đây với đỉnh đó. 

Và sau đó là trường hợp cuối cùng, khi đoạn truy vấn giao với cả hai đỉnh con.
Trong trường hợp này, chúng ta không có lựa chọn nào khác ngoài việc thực hiện hai cuộc gọi đệ quy, mỗi cuộc gọi cho một đỉnh con.
Trước tiên, chúng ta đi đến đỉnh trái, tính toán một phần kết quả cho đỉnh này (tức là tổng các giá trị của phần giao nhau giữa đoạn truy vấn và đoạn của đỉnh trái), sau đó đi đến đỉnh phải, tính toán kết quả phần của nó, và cuối cùng kết hợp các kết quả bằng cách cộng chúng lại.
Nói cách khác, vì đỉnh trái đại diện cho đoạn $a[tl \dots tm]$ và đỉnh phải đại diện cho đoạn $a[tm+1 \dots tr]$, chúng ta tính tổng truy vấn $a[l \dots tm]$ sử dụng đỉnh trái, và tổng truy vấn $a[tm+1 \dots r]$ sử dụng đỉnh phải. 

Vì vậy, việc xử lý một truy vấn tổng là một hàm đệ quy gọi chính nó một lần với đỉnh trái hoặc đỉnh phải (mà không thay đổi biên giới của truy vấn), hoặc hai lần, một lần cho đỉnh trái và một lần cho đỉnh phải (bằng cách chia truy vấn thành hai truy vấn con).
Và đệ quy sẽ kết thúc khi biên giới của đoạn truy vấn hiện tại trùng với biên giới của đoạn của đỉnh hiện tại.
Trong trường hợp đó, câu trả lời sẽ là giá trị đã được tính toán trước của tổng của đoạn này, được lưu trữ trong cây.

Nói cách khác, việc tính toán truy vấn là một phép duyệt qua cây, lan rộng qua tất cả các nhánh cần thiết của cây, và sử dụng các giá trị tổng đã được tính toán trước của các đoạn trong cây.

Hiển nhiên, chúng ta sẽ bắt đầu việc duyệt từ đỉnh gốc của Cây Phân Đoạn.

Quy trình này được minh họa trong hình dưới đây.
Một lần nữa, mảng $a = [1, 3, -2, 8, -7]$ được sử dụng, và ở đây chúng ta muốn tính tổng $\sum_{i=2}^4 a[i]$.
Các đỉnh được tô màu sẽ được thăm, và chúng ta sẽ sử dụng các giá trị đã tính toán trước của các đỉnh màu xanh lá cây.
Kết quả thu được là $-2 + 1 = -1$.

![sum-segment-tree-query](https://github.com/user-attachments/assets/80e47893-b3b7-467d-9fc4-bfe31c790ec7)

Tại sao độ phức tạp của thuật toán này là $O(\log n)$?
Để chứng minh độ phức tạp này, chúng ta xem xét từng cấp của cây.
Hóa ra, ở mỗi cấp, chúng ta chỉ thăm không quá bốn đỉnh.
Và vì chiều cao của cây là $O(\log n)$, chúng ta có được thời gian thực thi mong muốn.

Chúng ta có thể chứng minh rằng phát biểu này (tối đa bốn đỉnh mỗi cấp) là đúng bằng phương pháp quy nạp.
Ở cấp đầu tiên, chúng ta chỉ thăm một đỉnh, đỉnh gốc, vì vậy ở đây chúng ta thăm ít hơn bốn đỉnh.
Bây giờ, hãy xem xét một cấp bất kỳ.
Theo giả thuyết quy nạp, chúng ta thăm tối đa bốn đỉnh.
Nếu chúng ta chỉ thăm tối đa hai đỉnh, thì cấp tiếp theo sẽ có tối đa bốn đỉnh. Điều này là hiển nhiên, vì mỗi đỉnh chỉ có thể gây ra tối đa hai cuộc gọi đệ quy.
Vậy hãy giả sử rằng chúng ta thăm ba hoặc bốn đỉnh ở cấp hiện tại.
Từ những đỉnh này, chúng ta sẽ phân tích kỹ hơn các đỉnh ở giữa.
Vì truy vấn tổng yêu cầu tính tổng của một dãy con liên tiếp, chúng ta biết rằng các đoạn tương ứng với các đỉnh đã thăm ở giữa sẽ hoàn toàn được bao phủ bởi đoạn của truy vấn tổng.
Do đó, các đỉnh này sẽ không tạo ra bất kỳ cuộc gọi đệ quy nào.
Vậy chỉ có đỉnh trái nhất và đỉnh phải nhất mới có khả năng tạo ra các cuộc gọi đệ quy.
Và chúng chỉ tạo ra tối đa bốn cuộc gọi đệ quy, vì vậy cấp tiếp theo cũng sẽ thỏa mãn phát biểu này.
Chúng ta có thể nói rằng một nhánh tiếp cận biên trái của truy vấn, và nhánh còn lại tiếp cận biên phải của truy vấn.

Do đó, chúng ta thăm tối đa $4 \log n$ đỉnh tổng cộng, và điều này tương đương với thời gian thực thi $O(\log n)$. 

Tóm lại, truy vấn hoạt động bằng cách chia đoạn đầu vào thành nhiều đoạn con, với tất cả các tổng đã được tính toán trước và lưu trữ trong cây.
Và nếu chúng ta dừng việc phân chia khi đoạn truy vấn trùng với đoạn của đỉnh, thì chúng ta chỉ cần tối đa $O(\log n)$ đoạn như vậy, điều này giải thích hiệu quả của Cây Phân Đoạn.

### Truy vấn cập nhật (Update queries)

Bây giờ, chúng ta muốn sửa đổi một phần tử cụ thể trong mảng, giả sử chúng ta muốn thực hiện phép gán $a[i] = x$. 
Và chúng ta phải xây dựng lại Cây Đoạn, sao cho nó tương ứng với mảng đã được sửa đổi mới.

Truy vấn này dễ dàng hơn truy vấn tổng.
Mỗi cấp của Cây Phân Đoạn tạo thành một phân hoạch của mảng.
Do đó, một phần tử $a[i]$ chỉ đóng góp vào một đoạn tại mỗi cấp.
Vì vậy, chỉ cần cập nhật $O(\log n)$ đỉnh.

Rất dễ thấy rằng yêu cầu cập nhật có thể được triển khai bằng một hàm đệ quy.
Hàm này nhận đỉnh cây hiện tại làm đối số, và nó sẽ gọi đệ quy với một trong hai đỉnh con (đỉnh chứa $a[i]$ trong đoạn của nó), sau đó tính lại giá trị tổng của đỉnh đó, tương tự như cách thực hiện trong phương thức xây dựng (tức là tổng của hai đỉnh con của nó).

Một lần nữa, đây là hình minh họa sử dụng mảng giống như trước.
Ở đây, chúng ta thực hiện cập nhật $a[2] = 3$.
Các đỉnh màu xanh lá cây là các đỉnh mà chúng ta thăm và cập nhật.

![sum-segment-tree-update](https://github.com/user-attachments/assets/9f4b0f09-6c34-4dbc-a72b-eed3b14252d7)


### Triển khai (Implementation)

Điều quan trọng chính là cách lưu trữ Cây Phân Đoạn.
Tất nhiên, chúng ta có thể định nghĩa một cấu trúc $\text{Vertex}$ và tạo ra các đối tượng lưu trữ biên giới của đoạn, tổng của nó và thêm vào đó là các con trỏ đến các đỉnh con của nó.
Tuy nhiên, cách này yêu cầu lưu trữ rất nhiều thông tin thừa dưới dạng con trỏ.
Chúng ta sẽ sử dụng một mẹo đơn giản để làm điều này hiệu quả hơn nhiều bằng cách sử dụng một _implicit data structure_: chỉ lưu trữ các tổng trong một mảng.
(Một phương pháp tương tự được sử dụng cho các đống nhị phân).
Tổng của đỉnh gốc ở chỉ số 1, tổng của hai đỉnh con của nó ở các chỉ số 2 và 3, tổng của các đỉnh con của hai đỉnh này ở các chỉ số từ 4 đến 7, và cứ như vậy.
Với chỉ số bắt đầu từ 1, một cách thuận tiện là đỉnh trái của một đỉnh ở chỉ số $i$ được lưu trữ tại chỉ số $2i$, và đỉnh phải ở chỉ số $2i + 1$. 
Tương tự, đỉnh cha của một đỉnh ở chỉ số $i$ được lưu trữ tại chỉ số $i/2$ (phép chia nguyên).

Điều này giúp đơn giản hóa việc triển khai rất nhiều.
Chúng ta không cần phải lưu trữ cấu trúc của cây trong bộ nhớ.
Cấu trúc này được định nghĩa ngầm.
Chúng ta chỉ cần một mảng duy nhất chứa tổng của tất cả các đoạn.

Như đã đề cập trước đó, chúng ta cần lưu trữ tối đa $4n$ đỉnh.
Có thể ít hơn, nhưng để thuận tiện, chúng ta luôn cấp phát một mảng có kích thước $4n$.
Sẽ có một số phần tử trong mảng tổng mà không tương ứng với bất kỳ đỉnh nào trong cây thực tế, nhưng điều này không làm phức tạp hóa việc triển khai.

Vậy, chúng ta lưu trữ Cây Đoạn đơn giản dưới dạng một mảng $t[]$ có kích thước gấp bốn lần kích thước đầu vào $n$:

```cpp
int n, t[4*MAXN];
```

Quy trình xây dựng Cây Phân Đoạn từ một mảng cho trước $a[]$ có dạng như sau:
Đây là một hàm đệ quy với các tham số $a[]$ (mảng đầu vào), $v$ (chỉ số của đỉnh hiện tại), và các biên giới $tl$ và $tr$ của đoạn hiện tại.
Trong chương trình chính, hàm này sẽ được gọi với các tham số của đỉnh gốc: $v = 1$, $tl = 0$, và $tr = n - 1$. 

```cpp
void build(int a[], int v, int tl, int tr)
{
    if (tl == tr) {
        t[v] = a[tl];
    } else {
        int tm = (tl + tr) / 2;
        build(a, v*2, tl, tm);
        build(a, v*2+1, tm+1, tr);
        t[v] = t[v*2] + t[v*2+1];
    }
}
```

Hơn nữa, hàm để trả lời các truy vấn tổng cũng là một hàm đệ quy, nhận làm tham số thông tin về đỉnh/đoạn hiện tại (tức là chỉ số $v$ và các biên giới $tl$ và $tr$) và cũng nhận thông tin về các biên giới của truy vấn, $l$ và $r$. 
Để đơn giản hóa mã nguồn, hàm này luôn thực hiện hai cuộc gọi đệ quy, ngay cả khi chỉ cần một cuộc gọi - trong trường hợp đó, cuộc gọi đệ quy thừa sẽ có $l > r$, và điều này có thể dễ dàng được phát hiện bằng cách kiểm tra thêm ở đầu hàm.

```cpp
int sum(int v, int tl, int tr, int l, int r) {
    if (l > r) 
        return 0;
    if (l == tl && r == tr) {
        return t[v];
    }
    int tm = (tl + tr) / 2;
    return sum(v*2, tl, tm, l, min(r, tm)) + sum(v*2+1, tm+1, tr, max(l, tm+1), r);
}
```

Cuối cùng là truy vấn cập nhật. Hàm này cũng sẽ nhận thông tin về đỉnh/đoạn hiện tại, và ngoài ra còn nhận tham số của truy vấn cập nhật (tức là vị trí của phần tử và giá trị mới của nó).

```cpp
void update(int v, int tl, int tr, int pos, int new_val) {
    if (tl == tr) {
        t[v] = new_val;
    } else {
        int tm = (tl + tr) / 2;
        if (pos <= tm)
            update(v*2, tl, tm, pos, new_val);
        else
            update(v*2+1, tm+1, tr, pos, new_val);
        t[v] = t[v*2] + t[v*2+1];
    }
}
```

### Triển khai tiết kiệm bộ nhớ (Memory efficient implementation)

Hầu hết mọi người sử dụng triển khai từ phần trước. Nếu bạn nhìn vào mảng `t` bạn sẽ thấy rằng nó theo thứ tự đánh số các đỉnh của cây theo cách duyệt theo chiều rộng (duyệt theo thứ tự cấp).
Sử dụng duyệt này, các đỉnh con của đỉnh $v$ là $2v$ và $2v + 1$ tương ứng.
Tuy nhiên, nếu $n$ không phải là lũy thừa của hai, phương pháp này sẽ bỏ qua một số chỉ số và để trống một số phần của mảng `t`.
Tuy nhiên, mức tiêu thụ bộ nhớ bị giới hạn bởi $4n$, mặc dù Cây Phân Đoạn của một mảng có $n$ phần tử chỉ yêu cầu $2n - 1$ đỉnh.

Tuy nhiên, điều này có thể được giảm thiểu.
Chúng ta sẽ đánh số lại các đỉnh của cây theo thứ tự của một chuyến tham quan Euler (pre-order traversal)(duyệt trước) , và chúng ta ghi tất cả các đỉnh này cạnh nhau.

Hãy xem xét một đỉnh có chỉ số $v$, và giả sử nó chịu trách nhiệm cho đoạn $[l, r]$, và giả sử $mid = \dfrac{l + r}{2}$.
Điều hiển nhiên là đỉnh trái sẽ có chỉ số $v + 1$.
Đỉnh trái chịu trách nhiệm cho đoạn $[l, mid]$, tức là tổng cộng sẽ có $2 * (mid - l + 1) - 1$ đỉnh trong cây con của đỉnh trái.
Vì vậy, chúng ta có thể tính chỉ số của đỉnh phải của $v$. Chỉ số của nó sẽ là $v + 2 * (mid - l + 1)$.
Với cách đánh số này, chúng ta đạt được việc giảm bộ nhớ cần thiết xuống còn $2n$.

## Các phiên bản nâng cao của Cây Đoạn (Advanced versions of Segment Trees)

Cây Đoạn là một cấu trúc dữ liệu rất linh hoạt, và cho phép các biến thể và mở rộng theo nhiều hướng khác nhau.
Hãy cùng thử phân loại chúng dưới đây.

### Các truy vấn phức tạp hơn (More complex queries)

Việc thay đổi Cây Đoạn theo một hướng sao cho nó tính toán các truy vấn khác (ví dụ: tính toán giá trị nhỏ nhất/tối đa thay vì tổng) có thể khá dễ dàng, nhưng cũng có thể rất không đơn giản.

#### Tìm giá trị lớn nhất (Finding the maximum)

Hãy thay đổi một chút điều kiện của bài toán được mô tả ở trên: thay vì truy vấn tổng, chúng ta sẽ thực hiện các truy vấn giá trị lớn nhất.

Cây sẽ có cấu trúc giống hệt như cây đã mô tả ở trên.
Chúng ta chỉ cần thay đổi cách tính $t[v]$ trong các hàm $\text{build}$ và $\text{update}$.
Giờ đây, $t[v]$ sẽ lưu trữ giá trị lớn nhất của đoạn tương ứng.
Và chúng ta cũng cần thay đổi cách tính giá trị trả về của hàm $\text{sum}$ (thay thế phép tính tổng bằng phép tính giá trị lớn nhất).

Tất nhiên, bài toán này có thể dễ dàng được thay đổi thành việc tính giá trị nhỏ nhất thay vì giá trị lớn nhất.

Thay vì trình bày một triển khai cho bài toán này, phần triển khai sẽ được đưa ra cho một phiên bản phức tạp hơn của bài toán trong phần tiếp theo.

#### Tìm giá trị lớn nhất và số lần nó xuất hiện (Finding the maximum and the number of times it appears)

Nhiệm vụ này rất giống với bài toán trước.
Ngoài việc tìm giá trị lớn nhất, chúng ta còn phải tìm số lần giá trị lớn nhất xuất hiện.

Để giải quyết vấn đề này, chúng ta lưu trữ một cặp số tại mỗi đỉnh trong cây:
Ngoài giá trị lớn nhất, chúng ta cũng lưu trữ số lần xuất hiện của nó trong đoạn tương ứng.
Việc xác định cặp số chính xác để lưu trữ tại $t[v]$ vẫn có thể thực hiện trong thời gian hằng số bằng cách sử dụng thông tin của các cặp số được lưu trữ tại các đỉnh con. 
Kết hợp hai cặp số như vậy nên được thực hiện trong một hàm riêng biệt, vì đây sẽ là một phép toán mà chúng ta sẽ thực hiện khi xây dựng cây, khi trả lời các truy vấn giá trị lớn nhất và khi thực hiện các phép sửa đổi.

```cpp
pair<int, int> t[4*MAXN];

pair<int, int> combine(pair<int, int> a, pair<int, int> b) {
    if (a.first > b.first) 
        return a;
    if (b.first > a.first)
        return b;
    return make_pair(a.first, a.second + b.second);
}

void build(int a[], int v, int tl, int tr) {
    if (tl == tr) {
        t[v] = make_pair(a[tl], 1);
    } else {
        int tm = (tl + tr) / 2;
        build(a, v*2, tl, tm);
        build(a, v*2+1, tm+1, tr);
        t[v] = combine(t[v*2], t[v*2+1]);
    }
}

pair<int, int> get_max(int v, int tl, int tr, int l, int r) {
    if (l > r)
        return make_pair(-INF, 0);
    if (l == tl && r == tr)
        return t[v];
    int tm = (tl + tr) / 2;
    return combine(get_max(v*2, tl, tm, l, min(r, tm)), get_max(v*2+1, tm+1, tr, max(l, tm+1), r));
}

void update(int v, int tl, int tr, int pos, int new_val) {
    if (tl == tr) {
        t[v] = make_pair(new_val, 1);
    } else {
        int tm = (tl + tr) / 2;
        if (pos <= tm)
            update(v*2, tl, tm, pos, new_val);
        else
            update(v*2+1, tm+1, tr, pos, new_val);
        t[v] = combine(t[v*2], t[v*2+1]);
    }
}
```
#### Tính toán ước chung lớn nhất / bội chung nhỏ nhất (Compute the greatest common divisor / least common multiple)

Trong bài toán này, chúng ta muốn tính toán ước chung lớn nhất (GCD) / bội chung nhỏ nhất (LCM) của tất cả các số trong các phạm vi cho trước của mảng.

Phiên bản thú vị này của Cây Đoạn có thể được giải quyết theo cách giống hệt như các Cây Đoạn mà chúng ta đã xây dựng cho các truy vấn tổng / giá trị nhỏ nhất / giá trị lớn nhất:
chỉ cần lưu trữ GCD / LCM của đoạn tương ứng trong mỗi đỉnh của cây.
Việc kết hợp hai đỉnh có thể được thực hiện bằng cách tính toán GCD / LCM của cả hai đỉnh.

#### Đếm số lượng số không, tìm kiếm số không thứ k (Counting the number of zeros, searching for the $k$-th zero)

Trong bài toán này, chúng ta muốn tìm số lượng số không trong một phạm vi cho trước, và thêm vào đó là tìm chỉ số của số không thứ $k$ bằng cách sử dụng một hàm thứ hai.

Một lần nữa, chúng ta cần thay đổi một chút cách lưu trữ giá trị trong cây:
Lần này, chúng ta sẽ lưu trữ số lượng số không trong mỗi đoạn trong $t[]$. 
Rất rõ ràng, cách triển khai các hàm ${build}$, ${update}$ và ${count_zero}$, có thể được thực hiện bằng cách sử dụng những ý tưởng từ bài toán truy vấn tổng.
Vậy là chúng ta đã giải quyết xong phần đầu tiên của bài toán.

Bây giờ chúng ta học cách giải quyết bài toán tìm số không thứ $k$ trong mảng $a[]$. 
Để làm được điều này, chúng ta sẽ di chuyển xuống Cây Đoạn, bắt đầu từ đỉnh gốc, và mỗi lần di chuyển đến con trái hoặc con phải, tùy thuộc vào đoạn nào chứa số không thứ $k$.
Để quyết định cần đi đến con nào, chỉ cần nhìn vào số lượng số không xuất hiện trong đoạn tương ứng với đỉnh con trái.
Nếu số lượng số không đã tính trước này lớn hơn hoặc bằng $k$, chúng ta cần đi xuống con trái, nếu không thì đi xuống con phải.
Lưu ý, nếu chọn con phải, chúng ta phải trừ số lượng số không của con trái khỏi $k$.

Trong triển khai, chúng ta có thể xử lý trường hợp đặc biệt, khi mảng, $a[]$ chứa ít hơn $k$ số không, bằng cách trả về -1.

```cpp
int find_kth(int v, int tl, int tr, int k) {
    if (k > t[v])
        return -1;
    if (tl == tr)
        return tl;
    int tm = (tl + tr) / 2;
    if (t[v*2] >= k)
        return find_kth(v*2, tl, tm, k);
    else 
        return find_kth(v*2+1, tm+1, tr, k - t[v*2]);
}
```

#### Tìm kiếm một tiền tố mảng với một số lượng cho trước (Searching for an array prefix with a given amount)

Bài toán như sau:
Với một giá trị $x$ cho trước, chúng ta cần nhanh chóng tìm chỉ số nhỏ nhất $i$ sao cho tổng của $i$ phần tử đầu tiên trong mảng $a[]$ lớn hơn hoặc bằng $x$ (giả sử mảng $a[]$ chỉ chứa các giá trị không âm).

Bài toán này có thể được giải quyết bằng cách sử dụng tìm kiếm nhị phân, tính tổng của các tiền tố mảng bằng Cây Đoạn.
Tuy nhiên, điều này sẽ dẫn đến giải pháp có độ phức tạp thời gian $O(\log^2 n)$.

Thay vào đó, chúng ta có thể sử dụng ý tưởng giống như trong phần trước, và tìm vị trí bằng cách di chuyển xuống cây:
mỗi lần di chuyển đến con trái hoặc con phải, tùy thuộc vào tổng của con trái.
Như vậy, ta có thể tìm được câu trả lời trong thời gian $O(\log n)$.

#### Tìm kiếm phần tử đầu tiên lớn hơn một giá trị cho trước (Searching for the first element greater than a given amount)

Bài toán như sau:
Với một giá trị $x$ và một dải $a[l \dots r]$ tìm chỉ số nhỏ nhất $i$ trong dải $a[l \dots r]$, sao cho $a[i]$ lớn hơn $x$.

Bài toán này có thể được giải quyết bằng cách sử dụng tìm kiếm nhị phân trên các truy vấn tiền tố cực đại với Cây Đoạn.
Tuy nhiên, điều này sẽ dẫn đến một giải pháp có độ phức tạp thời gian $O(\log^2 n)$.

Thay vào đó, chúng ta có thể sử dụng ý tưởng giống như trong các phần trước và tìm vị trí bằng cách di chuyển xuống cây:
mỗi lần di chuyển đến con trái hoặc con phải, tùy thuộc vào giá trị cực đại của con trái.
Như vậy, ta có thể tìm câu trả lời trong thời gian $O(\log n)$. 

```cpp
int get_first(int v, int tl, int tr, int l, int r, int x) {
    if(tl > r || tr < l) return -1;
    if(t[v] <= x) return -1;
    
    if (tl== tr) return tl;
    
    int tm = tl + (tr-tl)/2;
    int left = get_first(2*v, tl, tm, l, r, x);
    if(left != -1) return left;
    return get_first(2*v+1, tm+1, tr, l ,r, x);
}
```

#### Tìm các đoạn con với tổng lớn nhất (Finding subsegments with the maximal sum)

Ở đây, mỗi truy vấn lại nhận một khoảng $a[l \dots r]$ , lần này chúng ta phải tìm một dãy con $a[l^\prime \dots r^\prime]$ sao cho $l \le l^\prime$ and $r^\prime \le r$ và tổng các phần tử trong dãy con này là lớn nhất. 
Như trước, chúng ta cũng muốn có khả năng sửa đổi các phần tử riêng lẻ trong mảng. 
Các phần tử của mảng có thể là số âm, và dãy con tối ưu có thể là rỗng (ví dụ nếu tất cả các phần tử đều là số âm).

Vấn đề này là một ứng dụng không đơn giản của Cây Phân Đoạn.
Lần này, chúng ta sẽ lưu trữ bốn giá trị cho mỗi đỉnh:
tổng của đoạn, tổng tiền tố tối đa, tổng hậu tố tối đa, và tổng của dãy con tối đa trong đoạn đó.
Nói cách khác, đối với mỗi đoạn trong Cây Phân Đoạn, kết quả đã được tính trước, cũng như kết quả cho các đoạn chạm vào biên trái và biên phải của đoạn đó.

Làm thế nào để xây dựng một cây với dữ liệu như vậy?
Lại một lần nữa, chúng ta tính toán nó theo cách đệ quy:
chúng ta đầu tiên tính toán tất cả bốn giá trị cho con trái (the left child) và con phải (the right child), và sau đó kết hợp chúng để có được bốn giá trị cho đỉnh hiện tại.
Lưu ý rằng kết quả cho đỉnh hiện tại là một trong các giá trị sau:

 * Câu trả lời của con trái, có nghĩa là đoạn con tối ưu hoàn toàn nằm trong đoạn của con trái.
 * Câu trả lời của con phải, có nghĩa là đoạn con tối ưu hoàn toàn nằm trong đoạn của con phải.
 * Tổng của tổng hậu tố tối đa của con trái và tổng tiền tố tối đa của con phải, có nghĩa là đoạn con tối ưu giao với cả hai con.

Vì vậy, câu trả lời cho đỉnh hiện tại là giá trị lớn nhất trong ba giá trị này. 
Việc tính toán tổng tiền tố (prefix sum) / tổng hậu tố (suffix sum) tối đa còn dễ dàng hơn. 
Dưới đây là phần triển khai hàm $\text{combine}$, hàm này chỉ nhận dữ liệu từ con trái và con phải, và trả về dữ liệu của đỉnh hiện tại. 

```cpp
struct data {
    int sum, pref, suff, ans;
};

data combine(data l, data r) {
    data res;
    res.sum = l.sum + r.sum;
    res.pref = max(l.pref, l.sum + r.pref);
    res.suff = max(r.suff, r.sum + l.suff);
    res.ans = max(max(l.ans, r.ans), l.suff + r.pref);
    return res;
}
```

Sử dụng hàm ${combine}$ chúng ta có thể dễ dàng xây dựng cây phân đoạn (Segment Tree).
Chúng ta có thể triển khai nó theo cách giống như các triển khai trước đây.
Để khởi tạo các đỉnh lá, chúng ta tạo thêm hàm phụ trợ ${make_data}$, hàm này sẽ trả về một đối tượng ${data}$ chứa thông tin của một giá trị duy nhất.

```cpp
data make_data(int val) {
    data res;
    res.sum = val;
    res.pref = res.suff = res.ans = max(0, val);
    return res;
}

void build(int a[], int v, int tl, int tr) {
    if (tl == tr) {
        t[v] = make_data(a[tl]);
    } else {
        int tm = (tl + tr) / 2;
        build(a, v*2, tl, tm);
        build(a, v*2+1, tm+1, tr);
        t[v] = combine(t[v*2], t[v*2+1]);
    }
}
 
void update(int v, int tl, int tr, int pos, int new_val) {
    if (tl == tr) {
        t[v] = make_data(new_val);
    } else {
        int tm = (tl + tr) / 2;
        if (pos <= tm)
            update(v*2, tl, tm, pos, new_val);
        else
            update(v*2+1, tm+1, tr, pos, new_val);
        t[v] = combine(t[v*2], t[v*2+1]);
    }
}
```

Chỉ còn lại cách tính toán câu trả lời cho một truy vấn. 
Để trả lời truy vấn, chúng ta di chuyển xuống cây phân đoạn như trước, chia truy vấn thành nhiều đoạn con khớp với các đoạn của cây phân đoạn, và kết hợp các câu trả lời trong các đoạn đó thành một câu trả lời duy nhất cho truy vấn.
Sau đó, có thể thấy rằng công việc này hoàn toàn giống như trong cây phân đoạn đơn giản, nhưng thay vì cộng / tìm giá trị tối thiểu / tối đa các giá trị, chúng ta sử dụng hàm ${combine}$.

```cpp
data query(int v, int tl, int tr, int l, int r) {
    if (l > r) 
        return make_data(0);
    if (l == tl && r == tr) 
        return t[v];
    int tm = (tl + tr) / 2;
    return combine(query(v*2, tl, tm, l, min(r, tm)), query(v*2+1, tm+1, tr, max(l, tm+1), r));
}
```

### Đưa toàn bộ dãy con vào mỗi đỉnh (Saving the entire subarrays in each vertex)

Đây là một phần phụ riêng biệt, tách biệt so với các phần khác, vì tại mỗi đỉnh của Cây Phân Đoạn, chúng ta không lưu trữ thông tin về đoạn tương ứng dưới dạng nén (tổng, giá trị nhỏ nhất, giá trị lớn nhất, ...), mà lưu trữ tất cả các phần tử của đoạn đó.
Do đó, gốc của Cây Phân Đoạn sẽ lưu tất cả các phần tử của mảng, đỉnh con trái sẽ lưu nửa đầu của mảng, đỉnh con phải sẽ lưu nửa sau, và cứ như vậy.

Trong ứng dụng đơn giản nhất của kỹ thuật này, chúng ta lưu các phần tử theo thứ tự đã sắp xếp.
Trong các phiên bản phức tạp hơn, các phần tử không được lưu trong danh sách mà trong các cấu trúc dữ liệu tiên tiến hơn (sets, maps, ...). 
Tuy nhiên, tất cả những phương pháp này có một yếu tố chung là mỗi đỉnh yêu cầu bộ nhớ tuyến tính (tức là tỷ lệ thuận với độ dài của đoạn tương ứng).

Câu hỏi tự nhiên đầu tiên khi xem xét các Cây Phân Đoạn này là về việc tiêu thụ bộ nhớ.
Về trực quan, điều này có vẻ như sẽ tiêu tốn $O(n^2)$ bộ nhớ, nhưng thực tế, cây hoàn chỉnh chỉ cần $O(n \log n)$ bộ nhớ.
Tại sao lại như vậy?
Rất đơn giản, vì mỗi phần tử của mảng rơi vào $O(\log n)$ đoạn (nhớ rằng chiều cao của cây là $O(\log n)$). 

Vì vậy, mặc dù có vẻ tốn kém về bộ nhớ, nhưng Cây Phân Đoạn này chỉ tiêu tốn một chút bộ nhớ hơn so với Cây Phân Đoạn thông thường.

Dưới đây là một số ứng dụng điển hình của cấu trúc dữ liệu này.
Cần lưu ý sự tương đồng của những Cây Phân Đoạn này với các cấu trúc dữ liệu 2D (thực tế đây là một cấu trúc dữ liệu 2D, nhưng với các khả năng khá hạn chế).

#### Tìm số nhỏ nhất lớn hơn hoặc bằng một số đã cho. Không có truy vấn thay đổi. (Find the smallest number greater or equal to a specified number. No modification queries.)

Chúng ta muốn trả lời các truy vấn có dạng sau: 
Với ba số cho trước $(l, r, x)$ ta phải tìm số nhỏ nhất trong đoạn $a[l \dots r]$ mà lớn hơn hoặc bằng $x$.

Chúng ta xây dựng một Cây Phân Đoạn. 
Trong mỗi đỉnh, ta lưu một danh sách đã được sắp xếp của tất cả các số xuất hiện trong đoạn tương ứng, như đã mô tả ở trên. 
Làm thế nào để xây dựng một Cây Phân Mảnh như vậy một cách hiệu quả nhất?
Như thường lệ, ta tiếp cận bài toán này theo cách đệ quy: giả sử danh sách của các đỉnh con trái và phải đã được xây dựng, và ta muốn xây dựng danh sách cho đỉnh hiện tại.
Từ góc độ này, thao tác trở nên đơn giản và có thể hoàn thành trong thời gian tuyến tính:
Chúng ta chỉ cần kết hợp hai danh sách đã sắp xếp thành một danh sách duy nhất, điều này có thể thực hiện được bằng cách duyệt qua chúng bằng hai con trỏ. 
C++ STL đã có một triển khai thuật toán này.

Vì cấu trúc của Cây Phân Mảnh này và sự tương đồng với thuật toán sắp xếp hợp nhất (merge sort),cấu trúc dữ liệu này cũng thường được gọi là Cây Sắp Xếp Hợp Nhất (Merge Sort Tree).

```cpp
vector<int> t[4*MAXN];

void build(int a[], int v, int tl, int tr) {
    if (tl == tr) {
        t[v] = vector<int>(1, a[tl]);
    } else { 
        int tm = (tl + tr) / 2;
        build(a, v*2, tl, tm);
        build(a, v*2+1, tm+1, tr);
        merge(t[v*2].begin(), t[v*2].end(), t[v*2+1].begin(), t[v*2+1].end(), back_inserter(t[v]));
    }
}
```

Chúng ta đã biết rằng Cây Phân Đoạn được xây dựng theo cách này sẽ yêu cầu bộ nhớ là $O(n \log n)$.
Và nhờ vào triển khai này, việc xây dựng cây cũng mất $O(n \log n)$ thời gian, vì mỗi danh sách được xây dựng trong thời gian tuyến tính theo kích thước của nó. 

Bây giờ, hãy xét đến việc trả lời truy vấn.
Chúng ta sẽ đi xuống cây, giống như trong Cây Phân Đoạn thông thường, chia đoạn $a[l \dots r]$ thành nhiều đoạn con (tối đa là $O(\log n)$ đoạn). 
Rõ ràng, câu trả lời cho truy vấn tổng thể chính là giá trị nhỏ nhất trong các truy vấn con.
Vậy bây giờ, chúng ta chỉ cần hiểu cách trả lời một truy vấn trên một đoạn con tương ứng với một đỉnh của cây.

Chúng ta đang ở một đỉnh của Cây Phân Đoạn và muốn tính câu trả lời cho truy vấn, tức là tìm giá trị nhỏ nhất lớn hơn hoặc bằng một số $x$. 
Vì đỉnh này chứa danh sách các phần tử theo thứ tự sắp xếp, chúng ta có thể đơn giản thực hiện tìm kiếm nhị phân trên danh sách này và trả về số đầu tiên lớn hơn hoặc bằng $x$.

Do đó, câu trả lời cho truy vấn trên một đoạn con của cây mất $O(\log n)$ thời gian, và toàn bộ truy vấn được xử lý trong $O(\log^2 n)$.

```cpp
int query(int v, int tl, int tr, int l, int r, int x) {
    if (l > r)
        return INF;
    if (l == tl && r == tr) {
        vector<int>::iterator pos = lower_bound(t[v].begin(), t[v].end(), x);
        if (pos != t[v].end())
            return *pos;
        return INF;
    }
    int tm = (tl + tr) / 2;
    return min(query(v*2, tl, tm, l, min(r, tm), x), query(v*2+1, tm+1, tr, max(l, tm+1), r, x));
}
```

Hằng số ${INF}$ bằng một số lớn hơn tất cả các số trong mảng.
Ý nghĩa của việc sử dụng nó là, không có số nào lớn hơn hoặc bằng $x$ trong đoạn. 
Nó mang ý nghĩa là "không có câu trả lời trong khoảng cho trước" (there is no answer in the given interval).

#### Tìm số nhỏ nhất lớn hơn hoặc bằng một số đã chỉ định. Với các truy vấn sửa đổi. (Find the smallest number greater or equal to a specified number. With modification queries.)

Vấn đề này tương tự như vấn đề trước.
Phương pháp cuối cùng có một nhược điểm, đó là không thể sửa đổi mảng giữa các truy vấn.
Giờ đây, chúng ta muốn thực hiện chính điều này: một truy vấn sửa đổi sẽ thực hiện phép gán $a[i] = y$.

Giải pháp tương tự như giải pháp của vấn đề trước, nhưng thay vì lưu trữ các danh sách ở mỗi đỉnh của Cây Phân Đoạn, chúng ta sẽ lưu trữ một danh sách cân bằng cho phép tìm kiếm số nhanh chóng, xóa số, và chèn số mới.
Vì mảng có thể chứa các số lặp lại, lựa chọn tối ưu là cấu trúc dữ liệu ${multiset}$. 

Việc xây dựng Segment Tree như vậy thực hiện khá giống với bài toán trước, chỉ khác là giờ đây chúng ta cần kết hợp các ${multiset}$ thay vì các danh sách đã sắp xếp.
Điều này dẫn đến thời gian xây dựng là $O(n \log^2 n)$ (vì về lý thuyết, việc hợp nhất hai cây red-black có thể được thực hiện trong thời gian tuyến tính, nhưng STL của C++ không đảm bảo độ phức tạp thời gian này).

Hàm ${query}$ cũng gần giống như trước, nhưng bây giờ cần gọi hàm ${lower_bound}$ của ${multiset}$ thay vì sử dụng (Vì ${std::lower_bound}$ chỉ hoạt động trong $O(\log n)$ khi sử dụng với iterators có thể truy cập ngẫu nhiên).

Cuối cùng, với yêu cầu sửa đổi. 
Để xử lý yêu cầu này, chúng ta phải đi xuống cây và sửa đổi tất cả các ${multiset}$ từ các đoạn tương ứng chứa phần tử bị ảnh hưởng. 
Cách đơn giản là xóa giá trị cũ của phần tử này (chỉ xóa một lần xuất hiện) và chèn giá trị mới vào.

```cpp
void update(int v, int tl, int tr, int pos, int new_val) {
    t[v].erase(t[v].find(a[pos]));
    t[v].insert(new_val);
    if (tl != tr) {
        int tm = (tl + tr) / 2;
        if (pos <= tm)
            update(v*2, tl, tm, pos, new_val);
        else
            update(v*2+1, tm+1, tr, pos, new_val);
    } else {
        a[pos] = new_val;
    }
}
```

Việc xử lý yêu cầu sửa đổi này cũng mất $O(\log^2 n)$ thời gian.

#### Tìm số nhỏ nhất lớn hơn hoặc bằng một số chỉ định. Tăng tốc với "fractional cascading" (Find the smallest number greater or equal to a specified number. Acceleration with "fractional cascading".)

Chúng ta có cùng một đề bài, chúng ta muốn tìm số nhỏ nhất lớn hơn hoặc bằng $x$ trong một đoạn, nhưng lần này trong thời gian $O(\log n)$.
Chúng ta sẽ cải thiện độ phức tạp thời gian bằng cách sử dụng kỹ thuật "fractional cascading".

Fractional cascading là một kỹ thuật đơn giản cho phép cải thiện thời gian chạy của nhiều tìm kiếm nhị phân, được thực hiện đồng thời.
Cách tiếp cận trước đây của chúng ta cho truy vấn tìm kiếm là chia bài toán thành nhiều tiểu bài toán, mỗi bài toán được giải quyết bằng một tìm kiếm nhị phân.
Fractional cascading cho phép bạn thay thế tất cả các tìm kiếm nhị phân này bằng một tìm kiếm duy nhất.

Ví dụ đơn giản và rõ ràng nhất về fractional cascading là bài toán sau:
Có $k$ danh sách đã được sắp xếp, và chúng ta phải tìm trong mỗi danh sách số đầu tiên lớn hơn hoặc bằng số cho trước.

Thay vì thực hiện một tìm kiếm nhị phân cho mỗi danh sách, chúng ta có thể gộp tất cả các danh sách lại thành một danh sách lớn duy nhất.
Thêm vào đó, đối với mỗi phần tử $y$ chúng ta lưu một danh sách các kết quả tìm kiếm cho $y$ trong mỗi danh sách $k$.
Do đó, nếu chúng ta muốn tìm số nhỏ nhất lớn hơn hoặc bằng $x$, chúng ta chỉ cần thực hiện một tìm kiếm nhị phân duy nhất, và từ danh sách các chỉ số, chúng ta có thể xác định số nhỏ nhất trong mỗi danh sách.
Tuy nhiên, cách tiếp cận này yêu cầu $O(n \cdot k)$ bộ nhớ ($n$ là độ dài của các danh sách đã gộp), điều này có thể khá không hiệu quả. 

Fractional cascading giảm độ phức tạp bộ nhớ này xuống $O(n)$ bộ nhớ, bằng cách tạo ra từ $k$ danh sách đầu vào $k$ danh sách mới, trong đó mỗi danh sách chứa danh sách tương ứng và thêm vào đó mỗi phần tử thứ hai của danh sách tiếp theo.
Sử dụng cấu trúc này, chỉ cần lưu hai chỉ số, chỉ số của phần tử trong danh sách ban đầu và chỉ số của phần tử trong danh sách mới tiếp theo. 
Vì vậy, cách tiếp cận này chỉ sử dụng $O(n)$ bộ nhớ, và vẫn có thể trả lời các truy vấn bằng một tìm kiếm nhị phân duy nhất. 

Tuy nhiên, đối với ứng dụng của chúng ta, chúng ta không cần toàn bộ khả năng của fractional cascading.
Trong Segment Tree của chúng ta, mỗi đỉnh sẽ chứa danh sách đã được sắp xếp của tất cả các phần tử xuất hiện trong cây con bên trái hoặc bên phải (giống như trong Merge Sort Tree). 
Ngoài danh sách đã sắp xếp này, chúng ta lưu hai chỉ số cho mỗi phần tử.
Đối với một phần tử $y$ chúng ta lưu chỉ số nhỏ nhất $i$, sao cho phần tử thứ $i$ trong danh sách đã sắp xếp của cây con trái lớn hơn hoặc bằng $y$.
Và chúng ta lưu chỉ số nhỏ nhất $j$, sao cho phần tử thứ $j$th trong danh sách đã sắp xếp của cây con phải lớn hơn hoặc bằng $y$.
Các giá trị này có thể được tính song song với bước kết hợp khi xây dựng cây.

Làm thế nào để tăng tốc các truy vấn?

Hãy nhớ lại, trong giải pháp bình thường, chúng ta thực hiện một tìm kiếm nhị phân tại mỗi nút.
Nhưng với sự điều chỉnh này, chúng ta có thể tránh tất cả các tìm kiếm nhị phân ngoại trừ một.

Để trả lời một truy vấn, chúng ta chỉ cần thực hiện một tìm kiếm nhị phân tại nút gốc.
Điều này cho chúng ta phần tử nhỏ nhất $y \ge x$ trong toàn bộ mảng, nhưng nó cũng cho chúng ta hai chỉ số.
Chỉ số của phần tử nhỏ nhất lớn hơn hoặc bằng $x$ trong cây con bên trái, và chỉ số của phần tử nhỏ nhất $y$ trong cây con bên phải. Lưu ý rằng $\ge y$ là giống như $\ge x$, vì mảng của chúng ta không chứa bất kỳ phần tử nào giữa $x$ và $y$.
Trong giải pháp Merge Sort Tree thông thường, chúng ta sẽ tính toán các chỉ số này qua tìm kiếm nhị phân, nhưng với sự trợ giúp của các giá trị đã được tính sẵn, chúng ta chỉ cần tra cứu chúng trong $O(1)$.
Và chúng ta có thể lặp lại điều này cho đến khi thăm tất cả các nút bao phủ phạm vi truy vấn của chúng ta.

Tóm lại, như thường lệ, chúng ta sẽ chạm vào $O(\log n)$ nút trong một truy vấn. Ở nút gốc, chúng ta thực hiện một tìm kiếm nhị phân, và ở tất cả các nút khác, chúng ta chỉ làm công việc hằng số.
Điều này có nghĩa là độ phức tạp để trả lời một truy vấn là $O(\log n)$.

Nhưng lưu ý rằng, điều này sử dụng bộ nhớ gấp ba lần so với Merge Sort Tree bình thường, vốn đã sử dụng rất nhiều bộ nhớ ($O(n \log n)$).

Kỹ thuật này dễ dàng áp dụng cho một bài toán không yêu cầu truy vấn sửa đổi. 
Hai chỉ số này chỉ là các số nguyên và có thể dễ dàng tính toán khi hợp nhất hai dãy đã sắp xếp.

Tuy nhiên, vẫn có thể cho phép truy vấn sửa đổi, nhưng điều này làm cho toàn bộ code phức tạp hơn.
Thay vì các số nguyên, bạn cần lưu trữ mảng đã sắp xếp dưới dạng `multiset`, và thay vì các chỉ số, bạn cần lưu trữ các iterator.
Và bạn cần làm việc rất cẩn thận, để đảm bảo rằng bạn tăng hoặc giảm đúng iterator trong một truy vấn sửa đổi.

#### Các biến thể khả thi khác (Other possible variations)

Kỹ thuật này mở ra một lớp ứng dụng mới hoàn toàn. 
Thay vì lưu trữ một ${vector}$ hoặc một ${multiset}$ trong mỗi đỉnh, có thể sử dụng các cấu trúc dữ liệu khác:
other Segment Trees, Fenwick Trees, Cartesian trees, etc.

### Cập nhật đoạn (Range updates (Lazy Propagation))

Tất cả các bài toán trong các phần trên đều bàn về các truy vấn sửa đổi chỉ ảnh hưởng đến một phần tử duy nhất trong mảng.
Tuy nhiên, Segment Tree cho phép áp dụng các truy vấn sửa đổi đối với một dải các phần tử liên tiếp trong mảng, và thực hiện truy vấn trong cùng một thời gian $O(\log n)$. 

#### Phép cộng trên các đoạn (Addition on segments)

Chúng ta bắt đầu xem xét các vấn đề đơn giản nhất: truy vấn thay đổi giá trị nên cộng một số $x$ vào tất cả các số trong đoạn $a[l \dots r]$.
Truy vấn thứ hai mà chúng ta cần trả lời chỉ đơn giản là yêu cầu giá trị của $a[i]$.

Để làm cho truy vấn cộng trở nên hiệu quả, chúng ta lưu trữ tại mỗi đỉnh trong Segment Tree số lượng mà chúng ta cần cộng vào tất cả các số trong dải tương ứng.
Ví dụ, nếu truy vấn "cộng 3 vào toàn bộ mảng $a[0 \dots n-1]$" đến, thì chúng ta sẽ đặt số 3 vào gốc của cây.
Thông thường, chúng ta phải đặt số này vào nhiều dải, tạo thành một phân hoạch của dải truy vấn. 
Vì vậy, chúng ta không phải thay đổi tất cả $O(n)$ giá trị, mà chỉ thay đổi $O(\log n)$ giá trị.

Nếu bây giờ có một truy vấn yêu cầu giá trị hiện tại của một phần tử cụ thể trong mảng, chỉ cần đi xuống cây và cộng tất cả các giá trị tìm thấy dọc theo đường đi.

```cpp
void build(int a[], int v, int tl, int tr) {
    if (tl == tr) {
        t[v] = a[tl];
    } else {
        int tm = (tl + tr) / 2;
        build(a, v*2, tl, tm);
        build(a, v*2+1, tm+1, tr);
        t[v] = 0;
    }
}

void update(int v, int tl, int tr, int l, int r, int add) {
    if (l > r)
        return;
    if (l == tl && r == tr) {
        t[v] += add;
    } else {
        int tm = (tl + tr) / 2;
        update(v*2, tl, tm, l, min(r, tm), add);
        update(v*2+1, tm+1, tr, max(l, tm+1), r, add);
    }
}

int get(int v, int tl, int tr, int pos) {
    if (tl == tr)
        return t[v];
    int tm = (tl + tr) / 2;
    if (pos <= tm)
        return t[v] + get(v*2, tl, tm, pos);
    else
        return t[v] + get(v*2+1, tm+1, tr, pos);
}
```

#### Gán giá trị trên các đoạn (Assignment on segments)

Giả sử bây giờ truy vấn thay đổi yêu cầu gán mỗi phần tử của một đoạn nhất định $a[l \dots r]$ với một giá trị $p$.
Với truy vấn thứ hai, chúng ta sẽ lại xem xét việc đọc giá trị của một phần tử trong mảng $a[i]$.

Để thực hiện truy vấn thay đổi trên toàn bộ đoạn, bạn phải lưu tại mỗi đỉnh của Segment Tree xem liệu đoạn tương ứng có bị bao phủ hoàn toàn với cùng một giá trị hay không.
Điều này cho phép chúng ta thực hiện một cập nhật "lười biếng" (lazy update): 
thay vì thay đổi tất cả các đoạn trong cây bao phủ đoạn truy vấn, chúng ta chỉ thay đổi một số đoạn và để các đoạn khác không thay đổi.
Một đỉnh đã đánh dấu có nghĩa là mỗi phần tử của đoạn tương ứng được gán với giá trị đó, và thực tế là toàn bộ cây con cũng chỉ chứa giá trị này.
Ở một chừng mực nào đó, chúng ta đang làm việc một cách lười biếng và trì hoãn việc ghi giá trị mới vào tất cả các đỉnh đó.
Chúng ta có thể thực hiện công việc tẻ nhạt này sau, nếu cần thiết.

Vì vậy, sau khi truy vấn thay đổi được thực hiện, một số phần của cây trở nên không liên quan - một số thay đổi vẫn chưa được thực hiện trong đó.

Ví dụ, nếu một truy vấn sửa đổi yêu cầu 'gán một giá trị cho toàn bộ mảng $a[0 \dots n-1]$" trong Cây Segment chỉ cần thực hiện một thay đổi duy nhất - số đó được đặt vào gốc của cây và đỉnh này được đánh dấu. 
Các đoạn còn lại không thay đổi, mặc dù thực tế giá trị đó nên được gán cho toàn bộ cây.

Giả sử bây giờ truy vấn sửa đổi thứ hai yêu cầu rằng nửa đầu của mảng $a[0 \dots n/2]$ nên được gán một giá trị khác. 
Để xử lý truy vấn này, chúng ta phải gán mỗi phần tử trong nửa trái của cây con gốc với giá trị đó. 
Tuy nhiên, trước khi làm điều này, chúng ta phải giải quyết gốc của cây trước. 
Sự tinh tế ở đây là nửa phải của mảng vẫn nên được gán với giá trị của truy vấn đầu tiên, và hiện tại không có thông tin nào về nửa phải được lưu trữ.

Cách giải quyết vấn đề này là đẩy thông tin của gốc xuống các con của nó, tức là nếu gốc của cây đã được gán một giá trị nào đó, thì chúng ta sẽ gán các đỉnh con trái và con phải với giá trị này và xóa dấu hiệu của gốc.
Sau đó, chúng ta có thể gán giá trị mới cho đỉnh con trái mà không mất đi bất kỳ thông tin cần thiết nào.

Tóm lại, chúng ta có:
Đối với bất kỳ truy vấn nào (cả truy vấn sửa đổi hay truy vấn đọc) trong quá trình đi xuống cây, chúng ta luôn phải đẩy thông tin từ đỉnh hiện tại xuống cả hai đỉnh con của nó. 
Chúng ta có thể hiểu điều này theo cách, khi đi xuống cây, chúng ta áp dụng các sửa đổi bị hoãn lại, nhưng chỉ áp dụng đúng mức cần thiết (để không làm giảm độ phức tạp của thuật toán xuống $O(\log n)$). 

Để triển khai, chúng ta cần tạo một hàm ${push}$, hàm này sẽ nhận vào đỉnh hiện tại và đẩy thông tin của đỉnh đó xuống cả hai đỉnh con của nó. 
Chúng ta sẽ gọi hàm này ở đầu các hàm truy vấn (nhưng không gọi nó từ các lá, vì không cần phải đẩy thông tin từ các lá nữa).

```cpp
void push(int v) {
    if (marked[v]) {
        t[v*2] = t[v*2+1] = t[v];
        marked[v*2] = marked[v*2+1] = true;
        marked[v] = false;
    }
}

void update(int v, int tl, int tr, int l, int r, int new_val) {
    if (l > r) 
        return;
    if (l == tl && tr == r) {
        t[v] = new_val;
        marked[v] = true;
    } else {
        push(v);
        int tm = (tl + tr) / 2;
        update(v*2, tl, tm, l, min(r, tm), new_val);
        update(v*2+1, tm+1, tr, max(l, tm+1), r, new_val);
    }
}

int get(int v, int tl, int tr, int pos) {
    if (tl == tr) {
        return t[v];
    }
    push(v);
    int tm = (tl + tr) / 2;
    if (pos <= tm) 
        return get(v*2, tl, tm, pos);
    else
        return get(v*2+1, tm+1, tr, pos);
}
```

Lưu ý: hàm ${get}$ cũng có thể được triển khai theo một cách khác:
không thực hiện cập nhật trễ, mà ngay lập tức trả về giá trị $t[v]$ nếu $marked[v]$ là đúng.

#### Thêm vào các đoạn, truy vấn giá trị lớn nhất (Adding on segments, querying for maximum)

Bây giờ, truy vấn sửa đổi là thêm một số vào tất cả các phần tử trong một đoạn, và truy vấn đọc là tìm giá trị lớn nhất trong một đoạn.

Vì vậy, với mỗi đỉnh của Cây Phân Đoạn, chúng ta phải lưu trữ giá trị lớn nhất của đoạn con tương ứng. 
Phần thú vị là làm thế nào để tính lại các giá trị này trong quá trình yêu cầu sửa đổi.

Để làm điều này, chúng ta lưu trữ thêm một giá trị cho mỗi đỉnh. 
Trong giá trị này, chúng ta lưu trữ các giá trị cộng thêm mà chúng ta chưa truyền tới các đỉnh con.
Trước khi di chuyển tới một đỉnh con, chúng ta gọi hàm ${push}$ và truyền giá trị này xuống cả hai đỉnh con.
Chúng ta phải làm điều này trong cả hàm ${update}$ và hàm ${query}$.

```cpp
void build(int a[], int v, int tl, int tr) {
    if (tl == tr) {
        t[v] = a[tl];
    } else {
        int tm = (tl + tr) / 2;
        build(a, v*2, tl, tm);
        build(a, v*2+1, tm+1, tr);
        t[v] = max(t[v*2], t[v*2 + 1]);
    }
}

void push(int v) {
    t[v*2] += lazy[v];
    lazy[v*2] += lazy[v];
    t[v*2+1] += lazy[v];
    lazy[v*2+1] += lazy[v];
    lazy[v] = 0;
}

void update(int v, int tl, int tr, int l, int r, int addend) {
    if (l > r) 
        return;
    if (l == tl && tr == r) {
        t[v] += addend;
        lazy[v] += addend;
    } else {
        push(v);
        int tm = (tl + tr) / 2;
        update(v*2, tl, tm, l, min(r, tm), addend);
        update(v*2+1, tm+1, tr, max(l, tm+1), r, addend);
        t[v] = max(t[v*2], t[v*2+1]);
    }
}

int query(int v, int tl, int tr, int l, int r) {
    if (l > r)
        return -INF;
    if (l == tl && tr == r)
        return t[v];
    push(v);
    int tm = (tl + tr) / 2;
    return max(query(v*2, tl, tm, l, min(r, tm)), query(v*2+1, tm+1, tr, max(l, tm+1), r));
}
```

### <a name="generalization-to-higher-dimensions"></a>Generalization to higher dimensions

A Segment Tree can be generalized quite natural to higher dimensions.
If in the one-dimensional case we split the indices of the array into segments, then in the two-dimensional we make an ordinary Segment Tree with respect to the first indices, and for each segment we build an ordinary Segment Tree with respect to the second indices.

#### Simple 2D Segment Tree

A matrix $a[0 \dots n-1, 0 \dots m-1]$ is given, and we have to find the sum (or minimum/maximum) on some submatrix $a[x_1 \dots x_2, y_1 \dots y_2]$, as well as perform modifications of individual matrix elements (i.e. queries of the form $a[x][y] = p$).

So we build a 2D Segment Tree: first the Segment Tree using the first coordinate ($x$), then the second ($y$).

To make the construction process more understandable, you can forget for a while that the matrix is two-dimensional, and only leave the first coordinate.
We will construct an ordinary one-dimensional Segment Tree using only the first coordinate.
But instead of storing a number in a segment, we store an entire Segment Tree: 
i.e. at this moment we remember that we also have a second coordinate; but because at this moment the first coordinate is already fixed to some interval $[l \dots r]$, we actually work with such a strip $a[l \dots r, 0 \dots m-1]$ and for it we build a Segment Tree.

Here is the implementation of the construction of a 2D Segment Tree.
It actually represents two separate blocks: 
the construction of a Segment Tree along the $x$ coordinate ($\text{build}_x$), and the $y$ coordinate ($\text{build}_y$).
For the leaf nodes in $\text{build}_y$ we have to separate two cases: 
when the current segment of the first coordinate $[tlx \dots trx]$ has length 1, and when it has a length greater than one. In the first case, we just take the corresponding value from the matrix, and in the second case we can combine the values of two Segment Trees from the left and the right son in the coordinate $x$.

```cpp
void build_y(int vx, int lx, int rx, int vy, int ly, int ry) {
    if (ly == ry) {
        if (lx == rx)
            t[vx][vy] = a[lx][ly];
        else
            t[vx][vy] = t[vx*2][vy] + t[vx*2+1][vy];
    } else {
        int my = (ly + ry) / 2;
        build_y(vx, lx, rx, vy*2, ly, my);
        build_y(vx, lx, rx, vy*2+1, my+1, ry);
        t[vx][vy] = t[vx][vy*2] + t[vx][vy*2+1];
    }
}

void build_x(int vx, int lx, int rx) {
    if (lx != rx) {
        int mx = (lx + rx) / 2;
        build_x(vx*2, lx, mx);
        build_x(vx*2+1, mx+1, rx);
    }
    build_y(vx, lx, rx, 1, 0, m-1);
}
```

Such a Segment Tree still uses a linear amount of memory, but with a larger constant: $16 n m$.
It is clear that the described procedure $\text{build}_x$ also works in linear time. 

Now we turn to processing of queries. We will answer to the two-dimensional query using the same principle: 
first break the query on the first coordinate, and then for every reached vertex, we call the corresponding Segment Tree of the second coordinate.

```cpp
int sum_y(int vx, int vy, int tly, int try_, int ly, int ry) {
    if (ly > ry) 
        return 0;
    if (ly == tly && try_ == ry)
        return t[vx][vy];
    int tmy = (tly + try_) / 2;
    return sum_y(vx, vy*2, tly, tmy, ly, min(ry, tmy)) + sum_y(vx, vy*2+1, tmy+1, try_, max(ly, tmy+1), ry);
}

int sum_x(int vx, int tlx, int trx, int lx, int rx, int ly, int ry) {
    if (lx > rx)
        return 0;
    if (lx == tlx && trx == rx)
        return sum_y(vx, 1, 0, m-1, ly, ry);
    int tmx = (tlx + trx) / 2;
    return sum_x(vx*2, tlx, tmx, lx, min(rx, tmx), ly, ry) + sum_x(vx*2+1, tmx+1, trx, max(lx, tmx+1), rx, ly, ry);
}
```

This function works in $O(\log n \log m)$ time, since it first descends the tree in the first coordinate, and for each traversed vertex in the tree it makes a query in the corresponding Segment Tree along the second coordinate.

Finally we consider the modification query. 
We want to learn how to modify the Segment Tree in accordance with the change in the value of some element $a[x][y] = p$.
It is clear, that the changes will occur only in those vertices of the first Segment Tree that cover the coordinate $x$ (and such will be $O(\log n)$), and for Segment Trees corresponding to them the changes will only occurs at those vertices that covers the coordinate $y$ (and such will be $O(\log m)$).
Therefore the implementation will be not very different form the one-dimensional case, only now we first descend the first coordinate, and then the second.

```cpp
void update_y(int vx, int lx, int rx, int vy, int ly, int ry, int x, int y, int new_val) {
    if (ly == ry) {
        if (lx == rx)
            t[vx][vy] = new_val;
        else
            t[vx][vy] = t[vx*2][vy] + t[vx*2+1][vy];
    } else {
        int my = (ly + ry) / 2;
        if (y <= my)
            update_y(vx, lx, rx, vy*2, ly, my, x, y, new_val);
        else
            update_y(vx, lx, rx, vy*2+1, my+1, ry, x, y, new_val);
        t[vx][vy] = t[vx][vy*2] + t[vx][vy*2+1];
    }
}

void update_x(int vx, int lx, int rx, int x, int y, int new_val) {
    if (lx != rx) {
        int mx = (lx + rx) / 2;
        if (x <= mx)
            update_x(vx*2, lx, mx, x, y, new_val);
        else
            update_x(vx*2+1, mx+1, rx, x, y, new_val);
    }
    update_y(vx, lx, rx, 1, 0, m-1, x, y, new_val);
}
```

#### Compression of 2D Segment Tree

Giả sử bài toán như sau: có $n$ điểm trên mặt phẳng với tọa độ $(x_i, y_i)$ và các truy vấn có dạng "đếm số điểm nằm trong hình chữ nhật $((x_1, y_1), (x_2, y_2))$".
Rõ ràng trong trường hợp như vậy, việc xây dựng một Segment Tree hai chiều với $O(n^2)$ phần tử là không hợp lý, vì sẽ lãng phí bộ nhớ.
Phần lớn bộ nhớ này sẽ bị lãng phí, vì mỗi điểm đơn có thể chỉ vào được $O(\log n)$ đoạn của cây trên tọa độ thứ nhất, và vì vậy kích cỡ "hữu ích" ("useful") tổng cộng của tất cả các đoạn cây trên tọa độ thứ hai là $O(n \log n)$.

Vì vậy, chúng ta thực hiện như sau:
Tại mỗi đỉnh của cây phân đoạn liên quan đến tọa độ thứ nhất, chúng ta sẽ lưu trữ một cây phân đoạn chỉ bao gồm các tọa độ thứ hai xuất hiện trong đoạn của tọa độ thứ nhất. 
Nói cách khác, khi xây dựng một cây phân đoạn trong một đỉnh có chỉ số $vx$ và các biên độ $tlx$ và $trx$, chúng ta chỉ xem xét những điểm nằm trong khoảng $x \in [tlx, trx]$, và xây dựng một cây phân đoạn chỉ với những điểm này.

Với cách tiếp cận này, chúng ta sẽ đảm bảo rằng mỗi cây phân đoạn trên tọa độ thứ hai chỉ chiếm đúng lượng bộ nhớ cần thiết.
Kết quả là tổng lượng bộ nhớ sẽ giảm xuống còn $O(n \log n)$.
Chúng ta vẫn có thể trả lời các truy vấn trong thời gian $O(\log^2 n)$, chỉ cần thực hiện một tìm kiếm nhị phân trên tọa độ thứ hai, nhưng điều này sẽ không làm giảm độ phức tạp.

Tuy nhiên, các truy vấn thay đổi sẽ không thể thực hiện được với cấu trúc này:
thực tế, nếu một điểm mới xuất hiện, chúng ta phải thêm một phần tử mới vào giữa một cây phân đoạn nào đó theo tọa độ thứ hai, điều này không thể thực hiện hiệu quả.

Tóm lại, chúng ta lưu ý rằng Cây Phân Đoạn hai chiều được xây dựng theo cách mô tả ở trên thực chất tương đương với việc thay đổi Cây Phân Đoạn một chiều (see [Saving the entire subarrays in each vertex](segment_tree.md#saving-the-entire-subarrays-in-each-vertex)).
Cụ thể, Cây Phân Đoạn hai chiều chỉ là một trường hợp đặc biệt của việc lưu trữ một mảng con trong mỗi đỉnh của cây.
Do đó, nếu bạn phải bỏ qua việc sử dụng Cây Phân Đoạn hai chiều do không thể thực thi một truy vấn, thì có thể cân nhắc thay thế Cây Phân Đoạn lồng vào nhau bằng một cấu trúc dữ liệu mạnh mẽ hơn, ví dụ như cây Cartesian.

### Preserving the history of its values (Persistent Segment Tree)

Cấu trúc dữ liệu bền vững (persistent data structure) là một cấu trúc dữ liệu nhớ lại trạng thái trước của nó sau mỗi lần sửa đổi.
Điều này cho phép truy cập bất kỳ phiên bản nào của cấu trúc dữ liệu mà chúng ta quan tâm và thực hiện một truy vấn trên nó.

Segment Tree là một cấu trúc dữ liệu có thể được chuyển thành một cấu trúc dữ liệu bền vững một cách hiệu quả (cả về thời gian và mức độ tiêu thụ bộ nhớ).
Chúng ta muốn tránh việc sao chép toàn bộ cây trước mỗi lần sửa đổi, và không muốn mất đi thời gian $O(\log n)$ khi trả lời các truy vấn phạm vi.

Thực tế, bất kỳ yêu cầu thay đổi nào trong Segment Tree sẽ dẫn đến thay đổi dữ liệu chỉ ở $O(\log n)$ đỉnh dọc theo đường đi bắt đầu từ gốc. 
Vì vậy, nếu chúng ta lưu trữ Segment Tree bằng con trỏ (tức là mỗi đỉnh lưu trữ con trỏ tới các đỉnh con trái và phải), thì khi thực hiện truy vấn sửa đổi, chúng ta chỉ cần tạo các đỉnh mới thay vì thay đổi các đỉnh đã có.
Những đỉnh không bị ảnh hưởng bởi truy vấn sửa đổi vẫn có thể được sử dụng bằng cách trỏ các con trỏ đến các đỉnh cũ.
Do đó, đối với một truy vấn sửa đổi, sẽ có $O(\log n)$ đỉnh mới được tạo ra, bao gồm một đỉnh gốc mới của Segment Tree, và toàn bộ phiên bản trước của cây với đỉnh gốc cũ sẽ vẫn không thay đổi.

Hãy lấy một ví dụ về cách triển khai cho Segment Tree đơn giản nhất: khi chỉ có một truy vấn yêu cầu tính tổng, và các truy vấn sửa đổi các phần tử đơn.

```cpp
struct Vertex {
    Vertex *l, *r;
    int sum;

    Vertex(int val) : l(nullptr), r(nullptr), sum(val) {}
    Vertex(Vertex *l, Vertex *r) : l(l), r(r), sum(0) {
        if (l) sum += l->sum;
        if (r) sum += r->sum;
    }
};

Vertex* build(int a[], int tl, int tr) {
    if (tl == tr)
        return new Vertex(a[tl]);
    int tm = (tl + tr) / 2;
    return new Vertex(build(a, tl, tm), build(a, tm+1, tr));
}

int get_sum(Vertex* v, int tl, int tr, int l, int r) {
    if (l > r)
        return 0;
    if (l == tl && tr == r)
        return v->sum;
    int tm = (tl + tr) / 2;
    return get_sum(v->l, tl, tm, l, min(r, tm)) + get_sum(v->r, tm+1, tr, max(l, tm+1), r);
}

Vertex* update(Vertex* v, int tl, int tr, int pos, int new_val) {
    if (tl == tr)
        return new Vertex(new_val);
    int tm = (tl + tr) / 2;
    if (pos <= tm)
        return new Vertex(update(v->l, tl, tm, pos, new_val), v->r);
    else
        return new Vertex(v->l, update(v->r, tm+1, tr, pos, new_val));
}
```

Mỗi khi sửa đổi Segment Tree, chúng ta sẽ nhận được một đỉnh gốc mới.
Để nhanh chóng chuyển giữa các phiên bản khác nhau của Segment Tree, chúng ta cần lưu trữ các đỉnh gốc này trong một mảng.
Để sử dụng một phiên bản cụ thể của Segment Tree, chúng ta chỉ cần gọi truy vấn bằng cách sử dụng đỉnh gốc tương ứng.

Với cách tiếp cận được mô tả ở trên, gần như bất kỳ Segment Tree nào cũng có thể được chuyển thành một cấu trúc dữ liệu bền vững (persistent data structure).

#### Tìm phần tử nhỏ thứ k trong một khoảng (Finding the k-th smallest number in a range)

Lần này, chúng ta phải trả lời các câu hỏi dạng "Phần tử nhỏ thứ $k$ trong khoảng $a[l \dots r]$ là gì?". 
Câu hỏi này có thể được trả lời bằng cách sử dụng tìm kiếm nhị phân và Merge Sort Tree, nhưng độ phức tạp thời gian cho một câu hỏi đơn lẻ sẽ là $O(\log^3 n)$.
Chúng ta sẽ thực hiện cùng một nhiệm vụ này bằng cách sử dụng Persistent Segment Tree trong $O(\log n)$.

Đầu tiên, chúng ta sẽ thảo luận về một giải pháp cho một bài toán đơn giản hơn:
Chúng ta chỉ xét các mảng trong đó các phần tử bị ràng buộc bởi $0 \le a[i] \lt n$.
Và chúng ta chỉ muốn tìm phần tử nhỏ thứ $k$ trong một tiền tố của mảng $a$.
Việc mở rộng các ý tưởng đã phát triển sau này cho các mảng không bị ràng buộc và các truy vấn phạm vi không bị ràng buộc sẽ rất dễ dàng.
Lưu ý rằng chúng ta sẽ sử dụng chỉ số bắt đầu từ 1 cho mảng $a$.

Chúng ta sẽ sử dụng một Cây Segment để đếm tất cả các số xuất hiện, tức là trong Cây Segment, chúng ta sẽ lưu trữ biểu đồ tần suất của mảng.
Vì vậy, các đỉnh lá sẽ lưu trữ tần suất xuất hiện của các giá trị $0$, $1$, $\dots$, $n-1$ trong mảng, và các đỉnh khác sẽ lưu trữ số lượng các số trong một phạm vi nhất định có mặt trong mảng.
Nói cách khác, chúng ta sẽ tạo một Cây Segment thông thường với các truy vấn tổng trên biểu đồ tần suất của mảng.
Tuy nhiên, thay vì tạo ra tất cả $n$ Cây Segment cho mỗi tiền tố có thể, chúng ta sẽ tạo ra một Cây Segment duy nhất có tính chất bền vững (persistent), chứa tất cả thông tin tương tự.
Chúng ta sẽ bắt đầu với một Cây Segment trống (tất cả các tần suất sẽ là $0$) được trỏ bởi $root_0$, và lần lượt thêm các phần tử $a[1]$, $a[2]$, $\dots$, $a[n]$ one after another.
Mỗi khi có một sự thay đổi, chúng ta sẽ nhận được một đỉnh gốc mới. Giả sử $root_i$ là gốc của Cây Segment sau khi chèn phần tử thứ $i$ của mảng $a$.
Cây Segment có gốc tại $root_i$ sẽ chứa biểu đồ tần suất của tiền tố $a[1 \dots i]$.
Dùng Cây Segment này, chúng ta có thể tìm vị trí của phần tử thứ $k$ trong thời gian $O(\log n)$ bằng cách sử dụng kỹ thuật đã thảo luận trong [Counting the number of zeros, searching for the k zero](https://cp-algorithms.com/data_structures/segment_tree.html#counting-zero-search-kth).

Bây giờ, đến phiên bản không bị giới hạn của bài toán.

Đầu tiên là sự hạn chế đối với các truy vấn:
Thay vì chỉ thực hiện các truy vấn này trên một tiền tố của mảng $a$, chúng ta muốn sử dụng bất kỳ đoạn nào tùy ý $a[l \dots r]$.
Ở đây, chúng ta cần một Cây Segment đại diện cho biểu đồ tần suất (histogram) của các phần tử trong đoạn $a[l \dots r]$. 
Dễ dàng nhận thấy rằng một Cây Segment như vậy chỉ là hiệu giữa Cây Segment có gốc tại $root_{r}$ và Cây Segment có gốc tại $root_{l-1}$, tức là mỗi đỉnh trong Cây Segment $[l \dots r]$ có thể được tính bằng cách lấy giá trị của đỉnh trong cây $root_{r}$ trừ đi giá trị của đỉnh trong cây $root_{l-1}$.

Trong triển khai của hàm find_kth điều này có thể được xử lý bằng cách truyền hai con trỏ của các đỉnh và tính toán số lượng/tổng của đoạn hiện tại dưới dạng hiệu của hai số lượng/tổng của các đỉnh.

Dưới đây là các hàm build, update và find_kth đã được chỉnh sửa.

```{.cpp file=kth_smallest_persistent_segment_tree}
Vertex* build(int tl, int tr) {
    if (tl == tr)
        return new Vertex(0);
    int tm = (tl + tr) / 2;
    return new Vertex(build(tl, tm), build(tm+1, tr));
}

Vertex* update(Vertex* v, int tl, int tr, int pos) {
    if (tl == tr)
        return new Vertex(v->sum+1);
    int tm = (tl + tr) / 2;
    if (pos <= tm)
        return new Vertex(update(v->l, tl, tm, pos), v->r);
    else
        return new Vertex(v->l, update(v->r, tm+1, tr, pos));
}

int find_kth(Vertex* vl, Vertex *vr, int tl, int tr, int k) {
    if (tl == tr)
    	return tl;
    int tm = (tl + tr) / 2, left_count = vr->l->sum - vl->l->sum;
    if (left_count >= k)
    	return find_kth(vl->l, vr->l, tl, tm, k);
    return find_kth(vl->r, vr->r, tm+1, tr, k-left_count);
}
```

Như đã đề cập ở trên, chúng ta cần lưu trữ gốc của Cây Segment ban đầu, cũng như tất cả các gốc sau mỗi lần cập nhật.
Dưới đây là đoạn mã để xây dựng một Cây Segment bền vững (persistent Segment Tree) trên một vector `a` với các phần tử có giá trị trong khoảng `[0, MAX_VALUE]`.

```cpp
int tl = 0, tr = MAX_VALUE + 1;
std::vector<Vertex*> roots;
roots.push_back(build(tl, tr));
for (int i = 0; i < a.size(); i++) {
    roots.push_back(update(roots.back(), tl, tr, a[i]));
}

// find the 5th smallest number from the subarray [a[2], a[3], ..., a[19]]
int result = find_kth(roots[2], roots[20], tl, tr, 5);
```

Bây giờ, về các hạn chế đối với các phần tử trong mảng:
Chúng ta thực tế có thể chuyển đổi bất kỳ mảng nào thành một mảng như vậy thông qua việc nén chỉ mục (index compression).
Phần tử nhỏ nhất trong mảng sẽ được gán giá trị 0, phần tử nhỏ thứ hai sẽ được gán giá trị 1, và cứ như vậy.
Việc tạo bảng tra cứu (ví dụ, sử dụng $\text{map}$), để chuyển đổi một giá trị thành chỉ mục của nó và ngược lại là rất dễ dàng, với độ phức tạp $O(\log n)$.



### Dynamic segment tree

(Được gọi như vậy vì hình dạng của nó là động và các đỉnh thường được cấp phát động.
Cũng được biết đến với tên gọi _implicit segment tree_ hoặc _sparse segment tree_.)

Trước đây, chúng ta đã xem xét các trường hợp khi có khả năng xây dựng cây segment ban đầu. Nhưng phải làm gì nếu kích thước ban đầu được điền với một số phần tử mặc định, nhưng kích thước của nó không cho phép bạn xây dựng hoàn toàn cây segment lên trước?

Chúng ta có thể giải quyết vấn đề này bằng cách tạo cây segment một cách lười biếng (theo từng bước). Ban đầu, chúng ta chỉ tạo gốc cây, và chỉ tạo các đỉnh còn lại khi cần thiết.
Trong trường hợp này, chúng ta sẽ sử dụng triển khai trên con trỏ (trước khi đi đến các đỉnh con, kiểm tra xem chúng đã được tạo hay chưa, và nếu chưa, thì tạo chúng).
Mỗi truy vấn vẫn có độ phức tạp chỉ $O(\log n)$, điều này đủ nhỏ cho hầu hết các trường hợp sử dụng (ví dụ $\log_2 10^9 \approx 30$).

Trong triển khai này, chúng ta có hai truy vấn: thêm một giá trị vào một vị trí (ban đầu tất cả các giá trị đều là $0$), và tính tổng của tất cả các giá trị trong một phạm vi.
`Vertex(0, n)` will be the root vertex of the implicit tree.

```cpp
struct Vertex {
    int left, right;
    int sum = 0;
    Vertex *left_child = nullptr, *right_child = nullptr;

    Vertex(int lb, int rb) {
        left = lb;
        right = rb;
    }

    void extend() {
        if (!left_child && left + 1 < right) {
            int t = (left + right) / 2;
            left_child = new Vertex(left, t);
            right_child = new Vertex(t, right);
        }
    }

    void add(int k, int x) {
        extend();
        sum += x;
        if (left_child) {
            if (k < left_child->right)
                left_child->add(k, x);
            else
                right_child->add(k, x);
        }
    }

    int get_sum(int lq, int rq) {
        if (lq <= left && right <= rq)
            return sum;
        if (max(left, lq) >= min(right, rq))
            return 0;
        extend();
        return left_child->get_sum(lq, rq) + right_child->get_sum(lq, rq);
    }
};
```

Rõ ràng, ý tưởng này có thể được mở rộng theo nhiều cách khác nhau. Ví dụ, bằng cách thêm hỗ trợ cho các cập nhật theo phạm vi thông qua lazy propagation.
