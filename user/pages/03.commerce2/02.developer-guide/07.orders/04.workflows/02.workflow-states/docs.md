---
title: Order workflow states
taxonomy:
    category: docs
---

Workflow states là những state khác nhau mà đơn hàng có thể tồn tại. Ví dụ, trong workflow Fulfilment mặc định, đơn hàng trong state Draft khi khách hàng thêm sản phẩm vào giỏ hàng, sau khi giỏ hàng được gửi thì đơn hàng có state là Fulfillment (đợi cửa hàng hoàn thành đơn hàng), và sau khi sản phẩm được giao đi thì state là Completed

Những state mặc định cho workflow mặc định là

| Default   | Default with validation | Fulfillment  | Fulfillment with validation |
|----------------------------------------------------------------------------------|
| Draft     | Draft                   | Draft        | Draft                       |
| Completed | Validation              | Fulfillment  | Validation                  |
| Canceled  | Completed               | Completed    | Fulfillment                 |
|           | Canceled                | Canceled     | Completed                   |
|           |                         |              | Canceled                    |

Những Worflow state được định nghĩa như là một phần của định nghĩa workflow trong file cấu hình YAML. Đây là một ví dụ cách mà state cho Fulfillment order workflow được định nghĩa bằng module Commerce Order.
```yaml
// commerce_order.workflows.yml

order_fulfillment:
  states:
    draft:
      label: Draft
    fulfillment:
      label: Fulfillment
    completed:
      label: Completed
    canceled:
      label: Canceled
```
