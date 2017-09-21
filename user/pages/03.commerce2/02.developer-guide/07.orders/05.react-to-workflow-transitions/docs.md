---
title: Subscribing to Transition Events - Đăng ký Transition Events
taxonomy:
    category: docs
---

! We had duplicate examples of this, need to consolidate. Both posted here.

Cho ví dụ thật sự được sử dụng trong Drupal Commerce, hãy xem qua state machine transition event subscriber được sử dụng trong commerce_order để thiết lập cho thời gian mà đơn hàng được đặt mua (timestamp):
* [commerce_order.services.yml]
* [TimestampEventSubscriber.php]

Finding Transitions
-------------------

Thông tin Transition có thể tìm thấy trong `{module}.workflows.yml`.

Ví dụ từ commerce_order:

```yaml
    # commerce_order.services.yml
    order_default:
      id: order_default
      group: commerce_order
      label: 'Default'
      states:
        draft:
          label: Draft
        completed:
          label: Completed
        canceled:
          label: Canceled
      transitions:
        place:
          label: 'Place order'
          from: [draft]
          to: completed
        cancel:
          label: 'Cancel order'
          from: [draft]
          to:   canceled
```

Phản ứng với Transitions
-----------------------

Ví dụ - Phản ứng với  order 'place' transition.

```php
    // mymodule/src/EventSubscriber/MyModuleEventSubscriber.php
    namespace Drupal\my_module\EventSubscriber;

    use Symfony\Component\EventDispatcher\EventSubscriberInterface;
    use Drupal\state_machine\Event\WorkflowTransitionEvent;

    class MyModuleEventSubscriber implements EventSubscriberInterface {

      public static function getSubscribedEvents() {
        // The format for adding a state machine event to subscribe to is:
        // {group}.{transition key}.pre_transition or {group}.{transition key}.post_transition
        // depending on when you want to react.
        $events = ['commerce_order.place.post_transition' => 'onOrderPlace'];
        return $events;
      }

      public function onOrderPlace(WorkflowTransitionEvent $event) {
        // @todo Write code that will run when the subscribed event fires.
      }
    }
```

Khai báo cho Drupal về đăng ký Event của bạn

Đăng ký Event của bạn nên được thêm vào `{module}.services.yml` ở file gốc của module của bạn.

Đoạn code sau sẽ đăng ký event của bạn đã viết trên.

```yaml
    # mymodule.services.yml
    services:
      my_module_event_subscriber:
        class: '\Drupal\my_module\EventSubscriber\MyModuleEventSubscriber'
        tags:
          - { name: 'event_subscriber' }
```


[commerce_order.services.yml]: https://github.com/drupalcommerce/commerce/blob/080ca52fbb9ec73b9eeece5487a62d221e75ed04/modules/order/commerce_order.services.yml#L29
[TimestampEventSubscriber.php]: https://github.com/drupalcommerce/commerce/blob/080ca52fbb9ec73b9eeece5487a62d221e75ed04/modules/order/src/EventSubscriber/TimestampEventSubscriber.php

Đăng ký các Transition Event
--------------------------------

Trong nhiều trường hợp chúng ta có thể muốn làm nhiều hơn khi mà việc chuyển tiếp xảy ra hơn là chỉ chuyển sang state kế tiếp. Giả sử chúng ta muốn gởi một email đến khách hàng khi đơn hàng đang được xử lý và đang chờ hoàn tất. Điều này nên xảy ra khi quản lý cửa hàng click vào nút "Process Order" trong ví dụ dưới

Module state_machine cung cấp nền tảng cho workflows phát ra 2 sự kiện khi chuyển tiếp xảy ra. Những event được gọi là ``commerce_order.TRANSITION_ID.TRANSITION_PHASE``, trong đó ``TRANSITION_ID`` là key của định nghĩa transition trong file YAML, và ``TRANSITION_PHASE``  là "pre_transition" cho event đầu tiên được phát ra trước khi transition xảy ra, và "post_transition" cho event thứ 2 được phát ra sau đó.

Trong trường hợp chúng ta muốn gửi mail sau khi transition đến state Fulfillment xảy ra. Chúng ta cần tạo ra một event subscriber để lắng nghe event ``commerce_order.fulfill.post_transition`` 

Dưới đây là một ví dụ mà bạn có thể sửa đổi dựa vào yêu cầu của bạn.

```php
    // my_module/src/EventSubscriber/OrderFulfillmentSubscriber.php

    namespace Drupal\my_module\EventSubscriber;

    use Drupal\state_machine\Event\WorkflowTransitionEvent;
    use Drupal\Core\Language\LanguageManagerInterface;
    use Drupal\Core\Mail\MailManagerInterface;
    use Drupal\Core\StringTranslation\StringTranslationTrait;
    use Symfony\Component\EventDispatcher\EventSubscriberInterface;

    /**
     * Sends an email when the order transitions to Fulfillment.
     */
    class OrderFulfillmentSubscriber implements EventSubscriberInterface {

      use StringTranslationTrait;

      /**
       * The language manager.
       *
       * @var \Drupal\Core\Language\LanguageManagerInterface
       */
      protected $languageManager;

      /**
       * The mail manager.
       *
       * @var \Drupal\Core\Mail\MailManagerInterface
       */
      protected $mailManager;

      /**
       * Constructs a new OrderFulfillmentSubscriber object.
       *
       * @param \Drupal\Core\Language\LanguageManagerInterface $language_manager
       *   The language manager.
       * @param \Drupal\Core\Mail\MailManagerInterface $mail_manager
       *   The mail manager.
       */
      public function __construct(
        LanguageManagerInterface $language_manager,
        MailManagerInterface $mail_manager
      ) {
        $this->languageManager = $language_manager;
        $this->mailManager = $mail_manager;
      }

      /**
       * {@inheritdoc}
       */
      public static function getSubscribedEvents() {
        $events = [
          'commerce_order.fulfill.post_transition' => ['sendEmail', -100],
        ];
        return $events;
      }

      /**
       * Sends the email.
       *
       * @param \Drupal\state_machine\Event\WorkflowTransitionEvent $event
       *   The transition event.
       */
      public function sendEmail(WorkflowTransitionEvent $event) {
        // Create the email.
        $order = $event->getEntity();
        $to = $order->getEmail();
        $params = [
          'from' => $order->getStore()->getEmail(),
          'subject' => $this->t(
            'Regarding your order [#@number]',
            ['@number' => $order->getOrderNumber()]
          ),
          'body' => $this->t(
            'Your order with #@number that you have placed with us has been processed and is awaiting fulfillment.',
            ['@number' => $order->getOrderNumber()]
          ),
        ];

        // Set the language that will be used in translations.
        if ($customer = $order->getCustomer()) {
          $langcode = $customer->getPreferredLangcode();
        }
        else {
          $langcode = $this->languageManager->getDefaultLanguage()->getId();
        }

        // Send the email.
        $this->mailManager->mail('commerce_order', 'receipt', $to, $langcode, $params);
      }

    }
```

Lưu ý rằng những hàm sau được cung cấp bởi event, nếu bạn cần thực thi nhiều hơn logic nâng cao dựa vào state mà bạn đến từ hoặc workflow mà transtion thuộc về.

```php
    $fromState = $event->getFromState();
    $toState = $event->getToState();
    $workflow = $event->getWorkflow();
```

Cuối cùng đừng quên đăng ký event của bạn.

```yaml
    // my_module/my_module.services.yml

    services:
      my_module.order_fulfillment_subscriber:
        class: Drupal\my_module\EventSubscriber\OrderFulfillmentSubscriber
        arguments: ['@language_manager', '@plugin.manager.mail']
        tags:
          - { name: event_subscriber }
```
