**Sparse Table** là một cấu trúc dữ liệu được sử dụng trong các bài toán yêu cầu trả lời nhanh các truy vấn về một mảng trong đó các truy vấn có thể là:

- **Tính toán giá trị tối thiểu (hoặc tối đa) trên một đoạn con của mảng.**
- **Tính toán các phép toán khử kết hợp (associative) trên một đoạn con mảng.**

### Đặc điểm chính của Sparse Table:
- **Cấu trúc dữ liệu tĩnh**: Sparse Table được thiết kế cho các bài toán mà mảng không thay đổi trong quá trình xử lý (mảng cố định sau khi khởi tạo).
- **Tính năng trả lời truy vấn nhanh**: Sau khi xây dựng, Sparse Table có thể trả lời các truy vấn tìm giá trị tối thiểu (hoặc tối đa) của một đoạn con trong mảng với độ phức tạp **O(1)**.
- **Thời gian xây dựng**: Thời gian xây dựng Sparse Table là **O(n log n)**, nơi ${\(n\)}$ là số phần tử của mảng.

### Ứng dụng của Sparse Table:

1. **Tìm giá trị nhỏ nhất hoặc lớn nhất trên một đoạn con**:
   - **Tính toán giá trị nhỏ nhất trên một đoạn con** (`RMQ - Range Minimum Query`): Sparse Table đặc biệt hữu ích trong các bài toán yêu cầu trả lời nhanh câu hỏi "Giá trị nhỏ nhất trên đoạn từ ${\(l\)}$ đến ${\(r\)}$".
   - **Tính toán giá trị lớn nhất trên một đoạn con** (`Range Maximum Query`): Cũng giống như tìm giá trị nhỏ nhất, bạn có thể sử dụng Sparse Table để tìm giá trị lớn nhất trong một đoạn con của mảng.

2. **Các bài toán liên quan đến chuỗi con**:
   - **Tìm dãy con có giá trị tối ưu** (chẳng hạn tìm đoạn con có tổng lớn nhất, giá trị tối thiểu hoặc tối đa,...).
   - Các bài toán yêu cầu thực hiện nhiều phép toán liên quan đến các đoạn con, đặc biệt là khi cần truy vấn và không thay đổi mảng, Sparse Table là lựa chọn tối ưu.

3. **Các bài toán đếm hoặc phân tích tính chất đoạn con**:
   - Ví dụ, bài toán **Tìm số lần xuất hiện của một giá trị trong một đoạn** hoặc **Tính tổng một hàm số trên đoạn**. Mặc dù Sparse Table chủ yếu dùng cho truy vấn tối thiểu/tối đa, nhưng bạn có thể điều chỉnh cho các phép toán khác như cộng hoặc nhân.

4. **Ứng dụng trong xử lý chuỗi**:
   - **Xử lý chuỗi con với tính toán tối thiểu/tối đa**: Các bài toán tìm đoạn con thỏa mãn một điều kiện nào đó (ví dụ tìm đoạn con có tổng lớn nhất, nhỏ nhất,...).

### Cấu trúc và cách hoạt động của Sparse Table:
- **Bước 1: Khởi tạo Sparse Table**:
  - Dùng một bảng `st[i][j]` để lưu trữ giá trị tối thiểu của mảng con có độ dài ${\(2^j\)}$ bắt đầu từ chỉ số ${\(i\)}$.
  - Tính toán bảng `st` theo công thức đệ quy dựa trên bảng đã tính cho các đoạn có độ dài nhỏ hơn.

- **Bước 2: Trả lời truy vấn**:
  - Để trả lời truy vấn, ta sẽ phân đoạn đoạn con cần truy vấn thành các đoạn nhỏ hơn mà mỗi đoạn có độ dài là một lũy thừa của 2, và lấy giá trị tối thiểu (hoặc tối đa) từ các bảng đã tính sẵn.

### Ưu điểm:
- **Trả lời truy vấn rất nhanh**: Độ phức tạp là **O(1)** cho mỗi truy vấn sau khi đã xây dựng Sparse Table.
- **Tiết kiệm bộ nhớ**: Cấu trúc Sparse Table không yêu cầu bộ nhớ quá lớn. Với mảng dài ${\(n\)}$, ta chỉ cần lưu trữ bảng có kích thước ${\(O(n \log n)\)}$.

### Nhược điểm:
- **Chỉ thích hợp cho các mảng tĩnh**: Nếu mảng thay đổi (cập nhật giá trị), Sparse Table không phải là cấu trúc dữ liệu phù hợp vì phải xây dựng lại từ đầu, hoặc cần có các phương pháp phức tạp để cập nhật.
  
### Ví dụ về bài toán sử dụng Sparse Table:

- **Bài toán Range Minimum Query (RMQ)**: Tìm giá trị nhỏ nhất trong một đoạn từ chỉ số \(l\) đến \(r\).
  
    Giả sử mảng ${\(arr = [5, 3, 7, 9, 2, 6]\)}$, nếu bạn muốn tìm giá trị nhỏ nhất trong đoạn con từ ${\(l = 1\)}$ đến ${\(r = 4\)}$, Sparse Table sẽ giúp bạn trả lời truy vấn này nhanh chóng sau khi đã xây dựng bảng.

