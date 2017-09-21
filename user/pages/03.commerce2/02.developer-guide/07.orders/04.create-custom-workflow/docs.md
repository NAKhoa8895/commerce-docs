---
title: Cách để tạo một workflow cho đơn hàng
taxonomy:
    category: docs
---

Trong nhiều trường hợp, workflow mặc định của đơn hàng có thể không cung cấp state và transitions phù hợp với các quy trình hoạt động của cửa hàng. Commerce 2 cho phép lập trình viên tạo một workflows để phù hợp với yêu cầu riêng.

Trong bài hướng dẫn này chúng tôi sẽ cho bạn thấy cách để tạo một custom workflow, xây dựng trên workflow fulfilment được cung cấp bởi module commerce order. Giả sử rằng xử lý và hoàn thành đơn hàng là 2 bước khác nhau cho cửa hàng của chúng ta. Điều này là có thể vì có nhân viên chịu trách nhiệm xử lý đơn hàng (ví dụ: xác minh thanh toán và kiểm tra tính khả dụng), và nhân viên khác làm phần đóng gói và chuyển phát sản phẩm.

Định nghĩa một Workflow
-------------------

Một order workflow được định nghĩa bằng file cấu hình Yaml trong một module tự tạo hoặc module cộng  đồng, hãy gọi module này là ``my_module``. Vậy file nên có tên là ``my_module.workflows.yml`` và nó nên được tạo ở file gốc của module. Drupal Commerce sẽ tự động phát hiện file và load workflows được định nghĩa trong đó, sau khi bạn clear caches.

Hãy nhìn qua định nghĩa cơ bản của fulfillment workflow trong file ``commerce_order.workflows.yml``. Chúng ta sẽ thêm state Processing và chỉ định đơn hàng nên chuyển từ state Draft, đến Processing ,đến Fulfillment, và cuối cùng là state Completed.

Group key trong định nghĩa nên luôn có giá trị "commerce_order".

```yaml
    // my_module.workflows.yml

    my_module_fulfillment_processing:
      id: my_module_fulfillment_processing
      group: commerce_order
      label: 'Fulfillment, with processing'
      states:
        draft:
          label: Draft
        processing:
          label: Processing
        fulfillment:
          label: Fulfillment
        completed:
          label: Completed
        canceled:
          label: Canceled
      transitions:
        place:
          label: 'Place order'
          from: [draft]
          to: processing
        process:
          label: 'Process order'
          from: [processing]
          to: fulfillment
        fulfill:
          label: 'Fulfill order'
          from: [fulfillment]
          to: completed
        cancel:
          label: 'Cancel order'
          from: [draft, processing, fulfillment]
          to:   canceled
```

Liên kết kiểu đơn hàng với Workflow
--------------------------------------------

Khi workflow đã được đăng ký, chúng ta cần liên kết kiểu order với nó. Giả sử rằng chúng ta sử dụng kiểu đơn hàng mặc định cho ví dụ này. Vào trang ``/admin/commerce/config/order-types`` và chọn Edit the default workflow. Sử dụng workflow dropdown để chọn option "Fulfill, with processing".

![Associating the Order Type with the New Workflow](order_workflow_association.jpg)

Trên trang web trang sản phẩm, có thể bạn muốn xuất kiểu Dơn hàng như là một tùy chỉnh và nó cũng chứa liên kết workflow -xem [Managing your site's configuration](https://www.drupal.org/docs/8/configuration-management/managing-your-sites-configuration).

Kiểm thử kết quả
------------------


Khi workflow đã được đăng ký và đã được liên kết đến kiểu order, người quản lý cửa hàng có thể chuyển đơn hàng sang các định nghĩa state thông qua các định nghĩa transitions. Thử một đơn hàng và vào trong trang admin. Đơn hàng nên được tự động đặt state là Processing và bạn có thể chuyển state sang Fulfillment  bằng cách vào nút "Process order" (cho biết rằng đơn hàng đã được xử lý), và chuyển sang state Completed khi click vào nút "Fulfill order".

Bạn cũng có thể hủy đơn hàng ở bất cứ bước nào, như định nghĩa trong transition của workflow.

![Moving an Order from Processing to Fulfillment](process_order.jpg)

![Moving an Order from Fulfillment to Completed](fulfill_order.jpg)
