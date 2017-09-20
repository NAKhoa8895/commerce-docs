---
title: Orders
taxonomy:
    category: docs
---

! We need help filling out this section! Feel free to follow the *edit this page* link and contribute.

Đơn hàng chứa dach sách của sản phẩm trong đơn hàng và thông tin khách hàng. Đơn hàng có trạng thái được điều khiển thông qua State Machine

[Order items](02.order-items) - Orders contain order items, which represent purchased items.

[Order types](01.order-types) - Bạn có thể có nhiều kiểu đơn hàng. Mỗi Kiểu đơn hàng có tùy chỉnh riêng khi nó đến cart, checkout, và khi processing.

[Workflows](04.workflows) - Mỗi loại đơn hàng sử dụng luồng xử lý đơn hàng khác nhau dựa trên kiểu của mặt hàng đang bán, cho dù mặt hàng có thể ship được v.v

Advanced topics
---------------

[Order processing](03.order-processing) - Cho phép bạn xử lý một đơn hàng, khi hệ thống đang tính lại giá  của mặt hàng và mặt hàng đó có khả dụng hay không.


```php

    /**
     * id [String]
     *   Khóa chính cho kiểu hóa đơn này.
     *
     * label [String]
     *   Nhãn hiển thị cho kiểu hóa đơn này.
     *
     * status [Bool] - [OPTIONAL, DEFAULTS TO TRUE]
     *   [AVAILABLE = FALSE, TRUE]
     *   Module này có được bật không. 1 để enabled.
     *
     * workflow [String] - [DEFAULT = order_default]
     *   [AVAILABLE = order_default, order_default_validation, order_fulfillment, order_fulfillment_validation]
     *   Workflow ID để sử dụng như một workflow.
     *
     * refresh_mode [String] - [DEFAULT = always]
     *   [AVAILABLE = always, customer]
     *   Chế độ refresh sử dụng.
     *
     * refresh_frequency [Integer] - [DEFAULT = 30]
     *   Thời gian refresh một lần.
     */
    $order_type = \Drupal\commerce_order\Entity\OrderType::create([
      'status' => TRUE,
      'id' => 'custom_order_type',
      'label' => 'My custom order type',
      'workflow' => 'order_default',
      'refresh_mode' => 'always',
      'refresh_frequency' => 30,
    ]);
    $order_type->save();

    // Phải gọi hàm này sau khi lưu order type.
    commerce_order_add_order_items_field($order_type);

```

Loading an order type
---------------------

```php

    // Load kiểu order này dựa vào khóa chính [String] đã được định nghĩa khi tạo
    $order_type = \Drupal\commerce_order\Entity\OrderType::load('custom_order_type');

```

Creating order item types
-------------------------

```php

    /**
     * id [String]
     *   Khóa chính cho kiểu mặt hàng trong hóa đơn này
     *
     * label [String]
     *   Nhạn của kiểu mặt hàng trong hóa đơn này.
     *
     * status [Bool] - [OPTIONAL, DEFAULTS TO TRUE]
     *   [AVAILABLE = FALSE, TRUE]
     *    Module này có được bật không. 1 để enabled.
     *
     * purchasableEntityType [String] - [DEFAULT = commerce_product_variation]
     *  Khoá ngoại tham chiếu tới những kiểu entity có thể mua được
     *
     * orderType [String] - [DEFAULT = default]
     *   Khoái ngoại tham chiếu tới kiểu đơn hàng
     */
    $order_item_type = \Drupal\commerce_order\Entity\OrderItemType::create([
      'id' => 'custom_order_item_type',
      'label' => 'My custom order item type',
      'status' => TRUE,
      'purchasableEntityType' => 'commerce_product_variation',
      'orderType' => 'custom_order_type',
    ]);
    $order_item_type->save();
```

Loading an order item type
--------------------------

```php

    // Load kiểu mặt hàng trong hóa đơn này dựa vào khóa chính [String] được định nghĩa khi tạo.
    $order_item_type = \Drupal\commerce_order\Entity\OrderItemType::load('custom_order_item_type');

```

Creating order items
--------------------

```php

    /**
     * type [String] - [DEFAULT = product_variation]
     *   Khóa ngoại dùng để tham chiếu tới kiểu mặt hàng trong đơn hàng
     *
     * purchased_entity [Integer | \Drupal\commerce\PurchasableEntityInterface]
     *    Khóa ngoại dùng để tham chiếu tới các entity đã được thêm vào đơn hàng. ID, hoặc đối tượng thực hiện giao dịch 
     *
     * quantity [Integer]
     *  Có bao nhiêu mặt hàng này trong hóa đơn.
     *
     * unit_price [\Drupal\commerce_price\Price]
     *   Giá của mỗi mặt hàng, không phải là tổng giá của mặt hàng này
     *
     * adjustments [OPTIONAL] - [Array(Drupal\commerce_order\Adjustment)]
     *   Mảng chứa bất cứ giá điều chỉnh nào (thuế)
     */
    $order_item = \Drupal\commerce_order\Entity\OrderItem::create([
      'type' => 'custom_order_item_type',
      'purchased_entity' => $variation_red_medium,
      'quantity' => 2,
      'unit_price' => $variation_red_medium->getPrice(),
    ]);
    $order_item->save();

    // Bạn có thể set số lượng bằng hàm setQuantity().
    $order_item->setQuantity('1');
    $order_item->save();

    // Bạn cũng có thể đặt giá với hàm setUnitPrice().
    $unit_price = new \Drupal\commerce_price\Price('9.99', 'USD');
    $order_item->setUnitPrice($unit_price);
    $order_item->save();
    
```

Loading an order item
---------------------

```php

    // Load mặt hàng trong đơn hàng dựa vào khóa chính [Integer]
    //   1 sẽ là item được lưu đầu tiên, 2 là item tiếp theo, v.v.
    $order_item = \Drupal\commerce_order\Entity\OrderItem::load(1);

```

Creating orders
---------------

```php

    /**
     * type [String] - [DEFAULT = default]
     *   Khóa ngoại để tham chiếu đến kiểu hóa đơn
     *
     * state [String] - [DEFAULT = draft]
     *   [AVAILABLE = draft, completed, canceled]
     *   Trạng thái hiện tại của đơn hàng.
     *
     * mail [String]
     *   Địa chỉ email sở hữu đơn hàng.
     *
     * uid [Integer]
     *   User id sở hữu đơn hàng.
     *
     * ip_address [String]
     *   Địa chỉ IP tạo đơn hàng.
     *
     * order_number [Integer | String] - [OPTIONAL, DEFAULTS TO id]
     *   Số thứ tự của đơn đặt hàng . Nếu để trống, mặt định là id của đơn hàng.
     *
     * billing_profile [\Drupal\profile\Entity\ProfileInterface]
     *   Profile thanh toán cho đơn hàng
     *
     * store_id [Integer]
     *   Khóa ngoại tham chiếu đến cửa hàng mà đơn hàng thuộc về
     *
     * order_items [Array(\Drupal\commerce_order\Entity\OrderItemInterface]
     *   Chuối chứa toàn bộ sản phẩm thuộc về đơn hàng này.
     *
     * adjustments [OPTIONAL] - [Array(Drupal\commerce_order\Adjustment)]
     *   Giá tiền điều chỉnh thêm (shipping)
     *
     * placed [Timestamp]
     *   Thời gian đơn hàng được đặt
     *
     * completed [OPTIONAL] - [Timestamp]
     *   Thời gian đơn hàng hoàn thành.
     */

    // Create the billing profile.
    $profile = \Drupal\profile\Entity\Profile::create([
      'type' => 'customer',
      'uid' => 1,
    ]);
    $profile->save();

    // Next, we create the order.
    $order = \Drupal\commerce_order\Entity\Order::create([
      'type' => 'custom_order_type',
      'state' => 'draft',
      'mail' => 'user@example.com',
      'uid' => 1,
      'ip_address' => '127.0.0.1',
      'order_number' => '6',
      'billing_profile' => $profile,
      'store_id' => $store->id(),
      'order_items' => [$order_item],
      'placed' => time(),
    ]);
    $order->save();
    
```

Loading an order
----------------

```php

    // Load đơn hàng dựa vào khóa chính [Integer]
    //   1 sẽ load đơn hàng đầu tiên, 2 load đơn hàng tiếp theo, v.v.
    $order = \Drupal\commerce_order\Entity\Order::load(1);

```
