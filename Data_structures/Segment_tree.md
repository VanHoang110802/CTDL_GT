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

### Structure of the Segment Tree

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

### Construction

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

### Sum queries

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

### Update queries

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


### Implementation

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

### Memory efficient implementation

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

It can be quite easy to change the Segment Tree in a direction, such that it computes different queries (e.g. computing the minimum / maximum instead of the sum), but it also can be very nontrivial. 

#### Finding the maximum

Let us slightly change the condition of the problem described above: instead of querying the sum, we will now make maximum queries.

The tree will have exactly the same structure as the tree described above. 
We only need to change the way $t[v]$ is computed in the $\text{build}$ and $\text{update}$ functions.
$t[v]$ will now store the maximum of the corresponding segment. 
And we also need to change the calculation of the returned value of the $\text{sum}$ function (replacing the summation by the maximum).

Of course this problem can be easily changed into computing the minimum instead of the maximum.

Instead of showing an implementation to this problem, the implementation will be given to a more complex version of this problem in the next section.

#### Finding the maximum and the number of times it appears 

This task is very similar to the previous one.
In addition of finding the maximum, we also have to find the number of occurrences of the maximum. 

To solve this problem, we store a pair of numbers at each vertex in the tree: 
In addition to the maximum we also store the number of occurrences of it in the corresponding segment. 
Determining the correct pair to store at $t[v]$ can still be done in constant time using the information of the pairs stored at the child vertices. 
Combining two such pairs should be done in a separate function, since this will be an operation that we will do while building the tree, while answering maximum queries and while performing modifications.

```{.cpp file=segment_tree_maximum_and_count}
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
#### Compute the greatest common divisor / least common multiple

In this problem we want to compute the GCD / LCM of all numbers of given ranges of the array. 

This interesting variation of the Segment Tree can be solved in exactly the same way as the Segment Trees we derived for sum / minimum / maximum queries:
it is enough to store the GCD / LCM of the corresponding vertex in each vertex of the tree. 
Combining two vertices can be done by computing the GCD / LCM of both vertices.

#### Counting the number of zeros, searching for the $k$-th zero { #counting-zero-search-kth data-toc-label="Counting the number of zeros, searching for the k-th zero"}

In this problem we want to find the number of zeros in a given range, and additionally find the index of the $k$-th zero using a second function.

Again we have to change the store values of the tree a bit:
This time we will store the number of zeros in each segment in $t[]$. 
It is pretty clear, how to implement the $\text{build}$, $\text{update}$ and $\text{count_zero}$ functions, we can simply use the ideas from the sum query problem.
Thus we solved the first part of the problem.

Now we learn how to solve the problem of finding the $k$-th zero in the array $a[]$. 
To do this task, we will descend the Segment Tree, starting at the root vertex, and moving each time to either the left or the right child, depending on which segment contains the $k$-th zero.
In order to decide to which child we need to go, it is enough to look at the number of zeros appearing in the segment corresponding to the left vertex.
If this precomputed count is greater or equal to $k$, it is necessary to descend to the left child, and otherwise descent to the right child.
Notice, if we chose the right child, we have to subtract the number of zeros of the left child from $k$.

In the implementation we can handle the special case, $a[]$ containing less than $k$ zeros, by returning -1.

```{.cpp file=segment_tree_kth_zero}
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

#### Searching for an array prefix with a given amount

The task is as follows: 
for a given value $x$ we have to quickly find smallest index $i$ such that the sum of the first $i$ elements of the array $a[]$ is greater or equal to $x$ (assuming that the array $a[]$ only contains non-negative values).

This task can be solved using binary search, computing the sum of the prefixes with the Segment Tree.
However this will lead to a $O(\log^2 n)$ solution.

Instead we can use the same idea as in the previous section, and find the position by descending the tree:
by moving each time to the left or the right, depending on the sum of the left child.
Thus finding the answer in $O(\log n)$ time.

#### Searching for the first element greater than a given amount

The task is as follows: 
for a given value $x$ and a range $a[l \dots r]$ find the smallest $i$  in the range $a[l \dots r]$, such that $a[i]$ is greater than $x$.

This task can be solved using binary search over max prefix queries with the Segment Tree.
However, this will lead to a $O(\log^2 n)$ solution.

Instead, we can use the same idea as in the previous sections, and find the position by descending the tree:
by moving each time to the left or the right, depending on the maximum value of the left child.
Thus finding the answer in $O(\log n)$ time. 

```{.cpp file=segment_tree_first_greater}
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

#### Finding subsegments with the maximal sum

Here again we receive a range $a[l \dots r]$ for each query, this time we have to find a subsegment $a[l^\prime \dots r^\prime]$ such that $l \le l^\prime$ and $r^\prime \le r$ and the sum of the elements of this segment is maximal. 
As before we also want to be able to modify individual elements of the array. 
The elements of the array can be negative, and the optimal subsegment can be empty (e.g. if all elements are negative).

This problem is a non-trivial usage of a Segment Tree.
This time we will store four values for each vertex: 
the sum of the segment, the maximum prefix sum, the maximum suffix sum, and the sum of the maximal subsegment in it.
In other words for each segment of the Segment Tree the answer is already precomputed as well as the answers for segments touching the left and the right boundaries of the segment.

How to build a tree with such data?
Again we compute it in a recursive fashion: 
we first compute all four values for the left and the right child, and then combine those to archive the four values for the current vertex.
Note the answer for the current vertex is either:

 * the answer of the left child, which means that the optimal subsegment is entirely placed in the segment of the left child
 * the answer of the right child, which means that the optimal subsegment is entirely placed in the segment of the right child
 * the sum of the maximum suffix sum of the left child and the maximum prefix sum of the right child, which means that the optimal subsegment intersects with both children.

Hence the answer to the current vertex is the maximum of these three values. 
Computing the maximum prefix / suffix sum is even easier. 
Here is the implementation of the $\text{combine}$ function, which receives only data from the left and right child, and returns the data of the current vertex. 

```{.cpp file=segment_tree_maximal_sum_subsegments1}
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

Using the $\text{combine}$ function it is easy to build the Segment Tree. 
We can implement it in exactly the same way as in the previous implementations.
To initialize the leaf vertices, we additionally create the auxiliary function $\text{make_data}$, which will return a $\text{data}$ object holding the information of a single value.

```{.cpp file=segment_tree_maximal_sum_subsegments2}
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

It only remains, how to compute the answer to a query. 
To answer it, we go down the tree as before, breaking the query into several subsegments that coincide with the segments of the Segment Tree, and combine the answers in them into a single answer for the query.
Then it should be clear, that the work is exactly the same as in the simple Segment Tree, but instead of summing / minimizing / maximizing the values, we use the $\text{combine}$ function.

```{.cpp file=segment_tree_maximal_sum_subsegments3}
data query(int v, int tl, int tr, int l, int r) {
    if (l > r) 
        return make_data(0);
    if (l == tl && r == tr) 
        return t[v];
    int tm = (tl + tr) / 2;
    return combine(query(v*2, tl, tm, l, min(r, tm)), query(v*2+1, tm+1, tr, max(l, tm+1), r));
}
```

### <a name="saving-the-entire-subarrays-in-each-vertex"></a>Saving the entire subarrays in each vertex

This is a separate subsection that stands apart from the others, because at each vertex of the Segment Tree we don't store information about the corresponding segment in compressed form (sum, minimum, maximum, ...), but store all elements of the segment.
Thus the root of the Segment Tree will store all elements of the array, the left child vertex will store the first half of the array, the right vertex the second half, and so on.

In its simplest application of this technique we store the elements in sorted order.
In more complex versions the elements are not stored in lists, but more advanced data structures (sets, maps, ...). 
But all these methods have the common factor, that each vertex requires linear memory (i.e. proportional to the length of the corresponding segment).

The first natural question, when considering these Segment Trees, is about memory consumption.
Intuitively this might look like $O(n^2)$ memory, but it turns out that the complete tree will only need $O(n \log n)$ memory.
Why is this so?
Quite simply, because each element of the array falls into $O(\log n)$ segments (remember the height of the tree is $O(\log n)$). 

So in spite of the apparent extravagance of such a Segment Tree, it consumes only slightly more memory than the usual Segment Tree. 

Several typical applications of this data structure are described below.
It is worth noting the similarity of these Segment Trees with 2D data structures (in fact this is a 2D data structure, but with rather limited capabilities).

#### Find the smallest number greater or equal to a specified number. No modification queries.

We want to answer queries of the following form: 
for three given numbers $(l, r, x)$ we have to find the minimal number in the segment $a[l \dots r]$ which is greater than or equal to $x$.

We construct a Segment Tree. 
In each vertex we store a sorted list of all numbers occurring in the corresponding segment, like described above. 
How to build such a Segment Tree as effectively as possible?
As always we approach this problem recursively: let the lists of the left and right children already be constructed, and we want to build the list for the current vertex.
From this view the operation is now trivial and can be accomplished in linear time:
We only need to combine the two sorted lists into one, which can be done by iterating over them using two pointers. 
The C++ STL already has an implementation of this algorithm.

Because this structure of the Segment Tree and the similarities to the merge sort algorithm, the data structure is also often called "Merge Sort Tree".

```{.cpp file=segment_tree_smallest_number_greater1}
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

We already know that the Segment Tree constructed in this way will require $O(n \log n)$ memory.
And thanks to this implementation its construction also takes $O(n \log n)$ time, after all each list is constructed in linear time in respect to its size. 

Now consider the answer to the query. 
We will go down the tree, like in the regular Segment Tree, breaking our segment $a[l \dots r]$ into several subsegments (into at most $O(\log n)$ pieces). 
It is clear that the answer of the whole answer is the minimum of each of the subqueries.
So now we only need to understand, how to respond to a query on one such subsegment that corresponds with some vertex of the tree.

We are at some vertex of the Segment Tree and we want to compute the answer to the query, i.e. find the minimum number greater that or equal to a given number $x$. 
Since the vertex contains the list of elements in sorted order, we can simply perform a binary search on this list and return the first number, greater than or equal to $x$.

Thus the answer to the query in one segment of the tree takes $O(\log n)$ time, and the entire query is processed in $O(\log^2 n)$.

```{.cpp file=segment_tree_smallest_number_greater2}
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

The constant $\text{INF}$ is equal to some large number that is bigger than all numbers in the array. 
Its usage means, that there is no number greater than or equal to $x$ in the segment. 
It has the meaning of "there is no answer in the given interval".

#### Find the smallest number greater or equal to a specified number. With modification queries.

This task is similar to the previous.
The last approach has a disadvantage, it was not possible to modify the array between answering queries.
Now we want to do exactly this: a modification query will do the assignment $a[i] = y$.

The solution is similar to the solution of the previous problem, but instead of lists at each vertex of the Segment Tree, we will store a balanced list that allows you to quickly search for numbers, delete numbers, and insert new numbers. 
Since the array can contain a number repeated, the optimal choice is the data structure $\text{multiset}$. 

The construction of such a Segment Tree is done in pretty much the same way as in the previous problem, only now we need to combine $\text{multiset}$s and not sorted lists.
This leads to a construction time of $O(n \log^2 n)$ (in general merging two red-black trees can be done in linear time, but the C++ STL doesn't guarantee this time complexity).

The $\text{query}$ function is also almost equivalent, only now the $\text{lower_bound}$ function of the $\text{multiset}$ function should be called instead ($\text{std::lower_bound}$ only works in $O(\log n)$ time if used with random-access iterators).

Finally the modification request. 
To process it, we must go down the tree, and modify all $\text{multiset}$ from the corresponding segments that contain the affected element.
We simply delete the old value of this element (but only one occurrence), and insert the new value.

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

Processing of this modification query also takes $O(\log^2 n)$ time.

#### Find the smallest number greater or equal to a specified number. Acceleration with "fractional cascading".

We have the same problem statement, we want to find the minimal number greater than or equal to $x$ in a segment, but this time in $O(\log n)$ time.
We will improve the time complexity using the technique "fractional cascading".

Fractional cascading is a simple technique that allows you to improve the running time of multiple binary searches, which are conducted at the same time. 
Our previous approach to the search query was, that we divide the task into several subtasks, each of which is solved with a binary search. 
Fractional cascading allows you to replace all of these binary searches with a single one.

The simplest and most obvious example of fractional cascading is the following problem:
there are $k$ sorted lists of numbers, and we must find in each list the first number greater than or equal to the given number.

Instead of performing a binary search for each list, we could merge all lists into one big sorted list.
Additionally for each element $y$ we store a list of results of searching for $y$ in each of the $k$ lists.
Therefore if we want to find the smallest number greater than or equal to $x$, we just need to perform one single binary search, and from the list of indices we can determine the smallest number in each list.
This approach however requires $O(n \cdot k)$ ($n$ is the length of the combined lists), which can be quite inefficient. 

Fractional cascading reduces this memory complexity to $O(n)$ memory, by creating from the $k$ input lists $k$ new lists, in which each list contains the corresponding list and additionally also every second element of the following new list.
Using this structure it is only necessary to store two indices, the index of the element in the original list, and the index of the element in the following new list.
So this approach only uses $O(n)$ memory, and still can answer the queries using a single binary search. 

But for our application we do not need the full power of fractional cascading.
In our Segment Tree a vertex will contain the sorted list of all elements that occur in either the left or the right subtrees (like in the Merge Sort Tree). 
Additionally to this sorted list, we store two positions for each element.
For an element $y$ we store the smallest index $i$, such that the $i$th element in the sorted list of the left child is greater or equal to $y$.
And we store the smallest index $j$, such that the $j$th element in the sorted list of the right child is greater or equal to $y$.
These values can be computed in parallel to the merging step when we build the tree.

How does this speed up the queries?

Remember, in the normal solution we did a binary search in every node.
But with this modification, we can avoid all except one.

To answer a query, we simply do a binary search in the root node.
This gives us the smallest element $y \ge x$ in the complete array, but it also gives us two positions.
The index of the smallest element greater or equal $x$ in the left subtree, and the index of the smallest element $y$ in the right subtree. Notice that $\ge y$ is the same as $\ge x$, since our array doesn't contain any elements between $x$ and $y$.
In the normal Merge Sort Tree solution we would compute these indices via binary search, but with the help of the precomputed values we can just look them up in $O(1)$.
And we can repeat that until we visited all nodes that cover our query interval.

To summarize, as usual we touch $O(\log n)$ nodes during a query. In the root node we do a binary search, and in all other nodes we only do constant work.
This means the complexity for answering a query is $O(\log n)$.

But notice, that this uses three times more memory than a normal Merge Sort Tree, which already uses a lot of memory ($O(n \log n)$).

It is straightforward to apply this technique to a problem, that doesn't require any modification queries.
The two positions are just integers and can easily be computed by counting when merging the two sorted sequences.

It it still possible to also allow modification queries, but that complicates the entire code.
Instead of integers, you need to store the sorted array as `multiset`, and instead of indices you need to store iterators.
And you need to work very carefully, so that you increment or decrement the correct iterators during a modification query.

#### Other possible variations

This technique implies a whole new class of possible applications. 
Instead of storing a $\text{vector}$ or a $\text{multiset}$ in each vertex, other data structures can be used:
other Segment Trees (somewhat discussed in [Generalization to higher dimensions](segment_tree.md#generalization-to-higher-dimensions)), Fenwick Trees, Cartesian trees, etc.

### Range updates (Lazy Propagation)

All problems in the above sections discussed modification queries that only affected a single element of the array each.
However the Segment Tree allows applying modification queries to an entire segment of contiguous elements, and perform the query in the same time $O(\log n)$. 

#### Addition on segments

We begin by considering problems of the simplest form: the modification query should add a number $x$ to all numbers in the segment $a[l \dots r]$.
The second query, that we are supposed to answer, asked simply for the value of $a[i]$.

To make the addition query efficient, we store at each vertex in the Segment Tree how many we should add to all numbers in the corresponding segment. 
For example, if the query "add 3 to the whole array $a[0 \dots n-1]$" comes, then we place the number 3 in the root of the tree.
In general we have to place this number to multiple segments, which form a partition of the query segment. 
Thus we don't have to change all $O(n)$ values, but only $O(\log n)$ many.

If now there comes a query that asks the current value of a particular array entry, it is enough to go down the tree and add up all values found along the way.

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

#### Assignment on segments

Suppose now that the modification query asks to assign each element of a certain segment $a[l \dots r]$ to some value $p$.
As a second query we will again consider reading the value of the array $a[i]$.

To perform this modification query on a whole segment, you have to store at each vertex of the Segment Tree whether the corresponding segment is covered entirely with the same value or not.
This allows us to make a "lazy" update: 
instead of changing all segments in the tree that cover the query segment, we only change some, and leave others unchanged.
A marked vertex will mean, that every element of the corresponding segment is assigned to that value, and actually also the complete subtree should only contain this value.
In a sense we are lazy and delay writing the new value to all those vertices.
We can do this tedious task later, if this is necessary.

So after the modification query is executed, some parts of the tree become irrelevant - some modifications remain unfulfilled in it.

For example if a modification query "assign a number to the whole array $a[0 \dots n-1]$" gets executed, in the Segment Tree only a single change is made - the number is placed in the root of the tree and this vertex gets marked.
The remaining segments remain unchanged, although in fact the number should be placed in the whole tree.

Suppose now that the second modification query says, that the first half of the array $a[0 \dots n/2]$ should be assigned with some other number. 
To process this query we must assign each element in the whole left child of the root vertex with that number. 
But before we do this, we must first sort out the root vertex first. 
The subtlety here is that the right half of the array should still be assigned to the value of the first query, and at the moment there is no information for the right half stored.

The way to solve this is to push the information of the root to its children, i.e. if the root of the tree was assigned with any number, then we assign the left and the right child vertices with this number and remove the mark of the root.
After that, we can assign the left child with the new value, without losing any necessary information.

Summarizing we get:
for any queries (a modification or reading query) during the descent along the tree we should always push information from the current vertex into both of its children. 
We can understand this in such a way, that when we descent the tree we apply delayed modifications, but exactly as much as necessary (so not to degrade the complexity of $O(\log n)$). 

For the implementation we need to make a $\text{push}$ function, which will receive the current vertex, and it will push the information for its vertex to both its children. 
We will call this function at the beginning of the query functions (but we will not call it from the leaves, because there is no need to push information from them any further).

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

Notice: the function $\text{get}$ can also be implemented in a different way: 
do not make delayed updates, but immediately return the value $t[v]$ if $marked[v]$ is true.

#### Adding on segments, querying for maximum

Now the modification query is to add a number to all elements in a range, and the reading query is to find the maximum in a range.

So for each vertex of the Segment Tree we have to store the maximum of the corresponding subsegment. 
The interesting part is how to recompute these values during a modification request.

For this purpose we keep store an additional value for each vertex. 
In this value we store the addends we haven't propagated to the child vertices.
Before traversing to a child vertex, we call $\text{push}$ and propagate the value to both children.
We have to do this in both the $\text{update}$ function and the $\text{query}$ function.

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

Let the problem be the following: there are $n$ points on the plane given by their coordinates $(x_i, y_i)$ and queries of the form "count the number of points lying in the rectangle $((x_1, y_1), (x_2, y_2))$".
It is clear that in the case of such a problem it becomes unreasonably wasteful to construct a two-dimensional Segment Tree with $O(n^2)$ elements.
Most on this memory will be wasted, since each single point can only get into $O(\log n)$ segments of the tree along the first coordinate, and therefore the total "useful" size of all tree segments on the second coordinate is $O(n \log n)$.

So we proceed as follows:
at each vertex of the Segment Tree with respect to the first coordinate we store a Segment Tree constructed only by those second coordinates that occur in the current segment of the first coordinates. 
In other words, when constructing a Segment Tree inside some vertex with index $vx$ and the boundaries $tlx$ and $trx$, we only consider those points that fall into this interval $x \in [tlx, trx]$, and build a Segment Tree just using them.

Thus we will achieve that each Segment Tree on the second coordinate will occupy exactly as much memory as it should.
As a result, the total amount of memory will decrease to $O(n \log n)$.
We still can answer the queries in $O(\log^2 n)$ time, we just have to make a binary search on the second coordinate, but this will not worsen the complexity.

But modification queries will be impossible with this structure:
in fact if a new point appears, we have to add a new element in the middle of some Segment Tree along the second coordinate, which cannot be effectively done.

In conclusion we note that the two-dimensional Segment Tree contracted in the described way becomes practically equivalent to the modification of the one-dimensional Segment Tree (see [Saving the entire subarrays in each vertex](segment_tree.md#saving-the-entire-subarrays-in-each-vertex)).
In particular the two-dimensional Segment Tree is just a special case of storing a subarray in each vertex of the tree.
It follows, that if you gave to abandon a two-dimensional Segment Tree due to the impossibility of executing a query, it makes sense to try to replace the nested Segment Tree with some more powerful data structure, for example a Cartesian tree.

### Preserving the history of its values (Persistent Segment Tree)

A persistent data structure is a data structure that remembers it previous state for each modification.
This allows to access any version of this data structure that interest us and execute a query on it.

Segment Tree is a data structure that can be turned into a persistent data structure efficiently (both in time and memory consumption).
We want to avoid copying the complete tree before each modification, and we don't want to loose the $O(\log n)$ time behavior for answering range queries.

In fact, any change request in the Segment Tree leads to a change in the data of only $O(\log n)$ vertices along the path starting from the root. 
So if we store the Segment Tree using pointers (i.e. a vertex stores pointers to the left and the right child vertices), then when performing the modification query, we simply need to create new vertices instead of changing the available vertices.
Vertices that are not affected by the modification query can still be used by pointing the pointers to the old vertices.
Thus for a modification query $O(\log n)$ new vertices will be created, including a new root vertex of the Segment Tree, and the entire previous version of the tree rooted at the old root vertex will remain unchanged.

Let's give an example implementation for the simplest Segment Tree: when there is only a query asking for sums, and modification queries of single elements. 

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

For each modification of the Segment Tree we will receive a new root vertex.
To quickly jump between two different versions of the Segment Tree, we need to store this roots in an array.
To use a specific version of the Segment Tree we simply call the query using the appropriate root vertex.

With the approach described above almost any Segment Tree can be turned into a persistent data structure.

#### Finding the $k$-th smallest number in a range {data-toc-label="Finding the k-th smallest number in a range"}

This time we have to answer queries of the form "What is the $k$-th smallest element in the range $a[l \dots r]$. 
This query can be answered using a binary search and a Merge Sort Tree, but the time complexity for a single query would be $O(\log^3 n)$.
We will accomplish the same task using a persistent Segment Tree in $O(\log n)$.

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

```{.cpp file=kth_smallest_persistent_segment_tree_build}
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
