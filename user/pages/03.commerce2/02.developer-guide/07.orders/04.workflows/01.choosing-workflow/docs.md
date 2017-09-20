---
title: Choosing a workflow - Chọn workflow
taxonomy:
    category: docs
---

! Tài liệu này cũng bao gồm quản lý đơn hàng và xử lý chung.

## Liên kết kiểu order vời một Workflow.

An order type can be associated with a specific workflow, and that forces the order to follow that workflow's rules such as to only move through the defined transitions. To associate an order type with an order workflow, go to ``/admin/commerce/config/order-types`` and select to Edit the desired order type. You can then choose the desired workflow from the Workflow dropdown field. Save the form.

Một kiểu đơn hàng có thể liên kết với workflow riêng, và điều này ép đơn hàng phải theo luật của workflows như là chỉ di chuyển thông qua các transitions đã được định nghĩa. Để liên kệt một kiểu đơn hàng với đon hàng workflow, vào ``/admin/commerce/config/order-types`` để chỉnh sửa loại đơn hàng mong muốn. Bạn có thể chọn workflow mong muốn thông qua trường Workflow dropdown. Lưu form này lại.

![Associating an Order Type with an Order Workflow](../../images/order_workflow_association.jpg)

Thay đổi Order Workflow
===========================


Loại đơn hàng có thể có các kiểu workflows khác nhau dựa vào loại sản phẩm mà cửa hàng bạn đang bán và nếu sản phẩm có thể vận chuyển được v.v. Workflow mặc định của đơn hàng có 2 trạng thái, Draft và Completed. Tuy nhiên, nếu bạn chạy đang điều hành một cửa hàng lớn với sản phẩm có thể giao được, Workflow "Fulfillment, with Validation" có thể là thứ phù hợp nhất mà bạn cần.


Bạn có thể thay đổi kiểu workflow của đơn hàng bằng cách vào `/admin/commerce/config/order-types`. Sau đó, chọn kiểu đơn hàng bạn muốn thay đổi và click "Edit". Bây giò, bạn có thể thay đổi workflow bằng cách chọn "Fuilfillment, with validation" từ select list `Workflow`.

![](../../images/select_order_type_workflow.png)

Now, let's take a look at how this new workflow works.

## Order Fulfillment with Validation Workflow

The fulfillment, with validation, order process moves through the following cycle:

![](../../images/order_workflow.png)

It starts with the order being in the shopping cart, which is the Draft/Cart state, then, once the order is placed, it is put in the Validation state. Once you're ready to ship the goods, the order is moved to the Fulfillment state. And finally, once it leaves our store, the order is officially Completed.

Now, that you know the process, let's take a look at how you can create orders on behalf of customers and move them along the order life cycle.

Creating an Order
-----------------
Site administrators can create orders on behalf of their customers by going to ``/admin/commerce/orders/add``. From here, you can either create a new order for an existing customer (chosen from the autocomplete search box). Or, you can create a new customer on the fly by providing just an email address.

![](../../images/create_a_new_order.png)

**Note:** You also have the option to create the order for a different date.

Once you've made the appropriate selections, you are taken to the order creation page where you enter the billing details and the products in the order.

![](../../images/order_details.png)

As you move further down, you'll see that there is an "Adjustments" section. This where you can add promotions, add a shipping amount, add tax, as well as, any custom amount to the order total. ([See steps on creating a promotion](../../../06.product-merchandising/01.create-promotion))

 And finally, you can apply coupons to the order. ([See steps on creating a coupon](../../../06.product-merchandising/02.create-coupon))

![](../../images/applying_coupons_to_order.png)

If you already have promotions running, this will automatically be reflected in the item pricing and the overall order total.

Saving an Order
---------------

Now that you've added all the order details, let's save the order. You also have the option of saving this new order to our cart. This will automatically add the products in this order to the shopping cart so you can complete the checkout by going to ``/cart``.

![](../../images/save_order_to_cart.png)

Now, click to view the order. Notice that a discount has been automatically applied to the order total as there was a "20% Off" promotion running store-wide. Also, notice that the order is currently in `Draft` state.

![](../../images/promotion_applied_to_order.png)

Adding Payments
----------------

As an admin, once you've got all the order details done, our next job would be to complete payment on the order. That's where the 'Payments' tab comes in. The payments page allows us to process a Credit Card/Email Money Transfer/Bank Transfer/Cheque payment for the order using the store's preferred Payment Gateway.

![](../../images/capture_order_payment.png)

Now, that you've got money for the goods from the customer, let's go ahead and officially place the order by clicking on the "Place order" button. This will put the order in `Validation` state.

![](../../images/order_in_validation_state.png)

Completing the Order
--------------------

The next steps are pretty obvious, once you are ready to ship the order, you must click the "Validate Order" button and it will put the order in `Fulfillment` state.

![](../../images/order_in_fulfillment_state.png)

And finally, once the order has shipped out, you can hit the "Fulfill Order" button and the order enters `Completed` state.

![](../../images/order_completed.png)
