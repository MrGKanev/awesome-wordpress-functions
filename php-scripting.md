# PHP & Scripting

- [PHP Logger](#php-logger)
- [Always Show Second Bar in TinyMCE](#always-show-second-bar-in-tinymce)
- [Switch Post Type](#switch-post-type)

###  PHP Logger

```php
/**
 * PHP Logger
 */

function php_logger( $data ) {
    $output = $data;
    if ( is_array( $output ) )
        $output = implode( ',', $output );
        
    // print the result into the JavaScript console
    echo "<script>console.log( 'PHP LOG: " . $output . "' );</script>";
}
```

###  Always Show Second Bar in TinyMCE

```php
/**
 * Always show second bar in TinyMCE
 */

function show_tinymce_toolbar( $in ) {
    $in['wordpress_adv_hidden'] = false;
    return $in;
}
add_filter( 'tiny_mce_before_init', 'show_tinymce_toolbar' );
```

###  Switch Post Type

```php
/**
 * Switch post type
 */

function switch_post_type ( $old_post_type, $new_post_type ) {
    global $wpdb;

    // Run the update query
    $wpdb->update(
        $wpdb->posts,
        // Set
        array( 'post_type' => $new_post_type),
        // Where
        array( 'post_type' => $old_post_type )
    );
}
```
