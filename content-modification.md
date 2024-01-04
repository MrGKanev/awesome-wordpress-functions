# Content Modification

- [Modify Excerpt Length](#modify-excerpt-length)
- [Change Read More Link](#change-read-more-link)
- [Change More Excerpt](#change-more-excerpt)
- [Disable Emojis Mess](#disable-emojis-mess)
- [Remove Comments](#remove-comments)
- [Limit Post Revisions](#limit-post-revisions)
- [Change Media Gallery URL](#change-media-gallery-url)
- [Create Custom Thumbnail Size](#create-custom-thumbnail-size)
- [Add Categories for Attachments](#add-categories-for-attachments)
- [Add Tags for Attachments](#add-tags-for-attachments)
- [Add Custom Excerpt to Pages](#add-custom-excerpt-to-pages)

### Modify Excerpt Length

```php
/**
 * Modify excerpt length
 */
 
function custom_excerpt_length( $length ) {
    return 20;
}
add_filter( 'excerpt_length', 'custom_excerpt_length', 999 );
```

### Change Read More Link

```php
/**
 * Change Read More link
 */
 
function custom_read_more_link() {
    return '<a href="' . get_permalink() . '">Read More</a>';
}
add_filter( 'the_content_more_link', 'custom_read_more_link' );
```

### Change More Excerpt

```php
/**
 * Change More excerpt
 */
 
function custom_more_excerpt( $more ) {
    return '...';
}
add_filter( 'excerpt_more', 'custom_more_excerpt' );
```

### Disable Emojis Mess

```php
/**
 * Disable Emojis mess
 */
 
function disable_wp_emojicons() {
    remove_action( 'admin_print_styles', 'print_emoji_styles' );
    remove_action( 'wp_head', 'print_emoji_detection_script', 7 );
    remove_action( 'admin_print_scripts', 'print_emoji_detection_script' );
    remove_action( 'wp_print_styles', 'print_emoji_styles' );
    remove_filter( 'wp_mail', 'wp_staticize_emoji_for_email' );
    remove_filter( 'the_content_feed', 'wp_staticize_emoji' );
    remove_filter( 'comment_text_rss', 'wp_staticize_emoji' );
    add_filter( 'tiny_mce_plugins', 'disable_emojicons_tinymce' );
    add_filter( 'emoji_svg_url', '__return_false' );
}
add_action( 'init', 'disable_wp_emojicons' );

function disable_emojicons_tinymce( $plugins ) {
    return is_array( $plugins ) ? array_diff( $plugins, array( 'wpemoji' ) ) : array();
}
```

### Remove Comments

```php
/**
 * Remove comments
 */
 
// Removes from admin menu

function my_remove_admin_menus() {
    remove_menu_page( 'edit-comments.php' );
}
add_action( 'admin_menu', 'my_remove_admin_menus' );

// Removes from post and pages
function remove_comment_support() {
    remove_post_type_support( 'post', 'comments' );
    remove_post_type_support( 'page', 'comments' );
}
add_action( 'init', 'remove_comment_support', 100 );

// Removes from admin bar
function mytheme_admin_bar_render() {
    global $wp_admin_bar;
    
    $wp_admin_bar->remove_menu( 'comments' );
}
add_action( 'wp_before_admin_bar_render', 'mytheme_admin_bar_render' );
```

### Limit Post Revisions

```php
define('WP_POST_REVISIONS', 5); // Limit to 5 revisions per post/page

```

### Change Media Gallery URL

```php
/**
 * Change Media Gallery URL
 */
 
if ( empty( get_option( 'upload_url_path' ) ) ) {
    update_option( 'upload_url_path', 'http://assets.website.com/wp-content/uploads' );
}
```

Also, you can filter the option value before it's retrieved from the database, which is slightly better:

```php
/**
 * Change Media Gallery URL
 */
 
add_filter( 'pre_option_upload_url_path', function() {
    return 'http://assets.website.com/wp-content/uploads';
});
```

### Create Custom Thumbnail Size

```php
/**
 * Create custom thumbnail size
 */
 
add_image_size( 'custom-thumbnail', 250, 250, true );
```

**Retrieve Thumbnail**

 ```php
 $thumb = wp_get_attachment_image_src( get_post_thumbnail_id($post->ID), 'custom-thumbnail' );

 echo $thumb[0]; 
 ```

Since WordPress 4.4.0, you can use:

```php
the_post_thumbnail_url( $size );
```

### Add Categories for Attachments

```php
/**
 * Add categories for attachments
 */
 
function add_categories_for_attachments() {
    register_taxonomy_for_object_type( 'category', 'attachment' );
}
add_action( 'init' , 'add_categories_for_attachments' );
```

### Add Tags for Attachments

```php
/**
 * Add tags for attachments
 */
 
function add_tags_for_attachments() {
    register_taxonomy_for_object_type( 'post_tag', 'attachment' );
}
add_action( 'init' , 'add_tags_for_attachments' );
```

### Add Custom Excerpt to Pages

```php
/**
 * Add custom excerpt to pages
 */
 
function add_page_excerpt() {
    add_post_type_support( 'page', array( 'excerpt' ) );
}
add_action( 'init', 'add_page_excerpt' );
```
