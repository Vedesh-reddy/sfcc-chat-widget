# Shopper guide

[← README](../README.md) · [Merchant guide](MERCHANT-GUIDE.md) · [Architecture](ARCHITECTURE.md)

Every screen and outcome of the assistant, captured on sandbox `zyeu-002`, site `RefArch_Practice`, on October 7, 2026, with headless Chrome.

### 1. Launcher and start menu

The widget opens automatically once per browser session and can be reopened from the launcher.

| Closed | Guest menu | Signed-in menu |
| --- | --- | --- |
| ![Launcher button](images/storefront-launcher.png) | ![Guest start menu](images/guest-menu.png) | ![Signed-in start menu](images/signed-in-menu.png) |

<details>
<summary><strong>Mobile</strong></summary>

![Widget on a 390 px wide phone screen](images/mobile-widget.png)

</details>

### 2. Browse and search

| Categories | Subcategories | Product carousel |
| --- | --- | --- |
| ![Top-level categories](images/browse-categories.png) | ![Mens subcategories](images/browse-subcategories.png) | ![Mens product carousel](images/product-carousel.png) |

| Search | Results | No results |
| --- | --- | --- |
| ![Search form](images/search-form.png) | ![Results for shirt](images/search-results.png) | ![No products match](images/search-no-results.png) |

### 3. Choose options and add to cart

| Options | Ready to add | Added |
| --- | --- | --- |
| ![Variation chooser](images/variation-chooser.png) | ![Color and size selected](images/variation-ready.png) | ![Added to cart](images/added-to-cart.png) |

### 4. Cart and promotions

| Cart | Quantity updated | Promo code rejected |
| --- | --- | --- |
| ![Cart with shipping methods](images/cart.png) | ![Quantity changed to 2](images/cart-quantity-updated.png) | ![Coupon cannot be added](images/cart-coupon-error.png) |

| Promotions (none active) | Guest checkout |
| --- | --- |
| ![No promotions available](images/promotions.png) | ![Sign in to checkout](images/checkout-sign-in-required.png) |

### 5. Sign in, register, reset password

| Sign in | Wrong credentials | Registration error |
| --- | --- | --- |
| ![Sign-in form](images/sign-in-form.png) | ![Invalid login or password](images/sign-in-error.png) | ![Confirm email mismatch](images/register-error.png) |

| Reset password | Reset requested |
| --- | --- |
| ![Reset password form](images/reset-password-form.png) | ![Check your email](images/reset-password-sent.png) |

The reset response is the same whether or not the account exists, so the widget cannot be used to discover accounts.

### 6. Account

| Summary | Edit profile |
| --- | --- |
| ![Account summary](images/account-summary.png) | ![Profile form](images/profile-form.png) |

| Addresses | Add address | Added |
| --- | --- | --- |
| ![Address book](images/addresses.png) | ![New address form](images/address-form.png) | ![WidgetDemo added](images/address-added.png) |

| Delete prompt | Deleted |
| --- | --- |
| ![Delete confirmation](images/address-delete-confirm.png) | ![Address deleted](images/address-deleted.png) |

| Saved cards | Order history | Order details |
| --- | --- | --- |
| ![Masked saved Visa](images/saved-cards.png) | ![Order list](images/order-history.png) | ![Order details](images/order-details.png) |

### 7. Checkout in the widget

| Delivery address | Shipping method | Payment |
| --- | --- | --- |
| ![Saved delivery addresses](images/checkout-delivery.png) | ![Shipping methods](images/checkout-shipping-method.png) | ![Saved card and CVV](images/checkout-payment.png) |

| Review | Placed, with review form |
| --- | --- |
| ![Review and place order](images/checkout-review.png) | ![Order 00000102 placed](images/order-confirmation.png) |

The security code goes only to `CheckoutServices-SubmitPayment` and is never stored.
The store's standard confirmation email follows:

![Order confirmation email for 00000102](images/order-confirmation-email.png)

### 8. Review the order and sign out

| Comment without consent | Review saved | Signed out |
| --- | --- | --- |
| ![Consent required](images/review-consent-error.png) | ![Thank you](images/review-thank-you.png) | ![Signed out](images/signed-out.png) |

