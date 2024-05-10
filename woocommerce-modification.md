# [WooCommerce modifications](woocommerce-modifications.md)

- [Deactivate some WooCommerce Checkout Fields](#deactivate-some-woocommerce-checkout-fields)
- [Restricting Access to Pages Based on WooCommerce Purchases](#restricting-access-to-pages-based-on-woocommerce-purchases)

### Deactivate some WooCommerce Checkout Fields

Comment out the fields you want to stay active.

```php
add_filter( 'woocommerce_checkout_fields' , 'custom_override_checkout_fields' );
function custom_override_checkout_fields( $fields ) {
unset($fields['billing']['billing_first_name']);
unset($fields['billing']['billing_last_name']);
unset($fields['billing']['billing_company']);
unset($fields['billing']['billing_address_1']);
unset($fields['billing']['billing_address_2']);
unset($fields['billing']['billing_city']);
unset($fields['billing']['billing_postcode']);
unset($fields['billing']['billing_country']);
unset($fields['billing']['billing_state']);
unset($fields['billing']['billing_phone']);
unset($fields['order']['order_comments']);
unset($fields['billing']['billing_email']);
unset($fields['account']['account_username']);
unset($fields['account']['account_password']);
unset($fields['account']['account_password-2']);
return $fields;
}
```

### Restricting Access to Pages Based on WooCommerce Purchases

```php
unction restrict_page_access() {
    // Configuration
    $access_rules = [
        'products-1' => [
            'product_ids' => [1],
            'redirect'    => 'https://websquadron.s3-tastewp.com/shop',
        ],
        'products-2' => [
            'product_ids' => [1, 2],
            'redirect'    => 'https://websquadron.s3-tastewp.com/contact',
        ],
        'products-3' => [
            'product_ids' => [1, 2],
            'redirect'    => 'https://websquadron.s3-tastewp.com/home',
        ],
    ];

    // Get current user ID and check if the user is an administrator
    $user_id = get_current_user_id();
    $is_admin = current_user_can('administrator');
 
    // If user is an administrator, skip restriction checks
    if ($is_admin) {
        return;
    }
 
    // Get the current page
    $current_page = get_queried_object();
 
    // Ensure $current_page is a WP_Post object
    if (!($current_page instanceof WP_Post)) {
        return;
    }
 
    // Iterate through access rules and apply restrictions
    foreach ($access_rules as $slug => $rule) {
        if ($current_page->post_name === $slug) {
            // Check if user has purchased any of the required products
            $has_purchased = false;
            foreach ($rule['product_ids'] as $product_id) {
                if (wc_customer_bought_product($user_id, $user_id, $product_id)) {
                    $has_purchased = true;
                    break;
                }
            }
 
            // Redirect if the user hasn't purchased the required products
            if (!$has_purchased) {
                wp_redirect($rule['redirect']);
                exit;
            }
        }
    }
}
add_action('template_redirect', 'restrict_page_access');
```
