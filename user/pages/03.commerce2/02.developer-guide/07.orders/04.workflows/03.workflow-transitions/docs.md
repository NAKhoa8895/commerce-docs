---
title: Order workflow transitions
taxonomy:
    category: docs
---

! This should document all possible events which trigger and moved to a developer guide.


Workflow Transitions là một đường dẫn mà đơn hàng có thể di chuyển từ state này sang state khác. Ví dụ, ở trạng thái mặc định của workflow Fulfillment, đơn hàng có thể di chuyển từ state Draft sang state Fulfillment, từ state fulfillment chuyển sang state Completed, và cũng có thể chuyển từ state Canceled từ cả 2 stae Draft và Fulfillment.

Workflow Transitions are defined as part of the workflow definition in a YAML configuration file. Here is an example of how transitions for the Fulfillment order workflow are defined by the Commerce Order module.

Các Workflow Transition được xác định như là một phần của định nghĩa workflow trong file cấu hình YAML. Dưới đây là ví dụ cách mà transition cho Fulfillment order workflow được xác định bằng module Commerce Order

```yaml
// commerce_order.workflows.yml

order_fulfillment:
  transitions:
    place:
      label: 'Place order'
      from: [draft]
      to:   fulfillment
    fulfill:
      label: 'Fulfill order'
      from: [fulfillment]
      to: completed
    cancel:
      label: 'Cancel order'
      from: [draft, fulfillment]
      to:   canceled
```
