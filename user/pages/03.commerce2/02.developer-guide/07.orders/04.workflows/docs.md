---
title: Order workflow
taxonomy:
    category: docs
---

Quá trình fulfillment của đơn hàng dựa trên kiểu của cửa hàng  đang chạy trang và quy trình hoạt động của nó. Một cửa hàng bán sản phẩm kỹ thuật số có thể nhận được hóa đơn, thu thanh toán, và gửi sản phẩm bằng điển tử (copy, download ...) và đóng toàn bộ đơn hàng một cách tự động. Một cửa hàng drop-shipping có thể nhận một đơn hàng, chuyển đơn đặt hàng đến một dịch vụ bên ngoài, xác minh tính khả dụng, thu thanh toán, và giao sản phẩm, toàn bộ coi như các bước riêng của tiến trình, một số được thực hiện bằng phần mềm và một một số được thực hiện bằng tay.

Commerce 2 cho phép lập trình viên linh hoạt tùy chỉnh các bước mà đơn đặt hàng cần  đi qua, từ lúc khởi tạo tới lúc hoàn thành. Theo mặc định thì có 4 loại workflows: Default, Default with validation, Fulfillment, và Fulfillment with validation. Lập trình viên có thể được định nghĩa Workflows custom. Một kiểu đơn hàng có thể liên kết với workflow riêng, và điều này bắt buộc đơn hàng phải theo luật của workflow.

Mỗi workflow bao gồm state và transitions.


## So sánh với Commerce 1


User của commerce 1 có thể tự hỏi điều khác biệt trong cấu túc của Commerce 2. Commerce 1 định nghĩa status đơn hàng được nhóm với nhau trong state. Ví dụ, một đơn hàng thông qua quá trình thanh toán sẽ được chuyển thong qua các status của Checkout: Checkout, Checkout:Review, Checkout:Payment, Checkout:Complete, đây toàn bộ các phần của nhóm Checkout state. Cài đặt này là nguồn gốc gây nhâm nhầm lẫn và cũng gây ra một số vấn đề kỹ thuật vì nó đã buộc các workflow liên quan không chặt chẽ với nhau  (order workflow, checkout workflow, payment workflow, fulfillment workflow) hợp lại với nhau thành một workflow. Điều này xác định path giữa các state phức tạp một cách không cần thiết.

Commerce 2 tách riêng workflows làm cho việc cuhyển tiếp giữa các states trở nên đơn giản.
