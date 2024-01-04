# Global Settings & Customizations

- [Create a Global String](#create-a-global-string)
- [Support Featured Images](#support-featured-images)
- [Support Search Form](#support-search-form)
- [Excluding pages from search](#excluding-pages-from-search)
- [Disable XMLRPC](#disable-xmlrpcphp)
- [Escape HTML in Posts](#escape-html-in-posts)
- [Create Custom Global Settings](#create-custom-global-settings)
- [Remove WordPress Admin Bar](#remove-wordpress-admin-bar)
- [Add Open Graph Meta Tags](#add-open-graph-meta-tags)
- [Disable the WordPress Version information in the head section](#disable-the-wordpress-version-information-in-the-head-section)


### Create a Global String

```php
/**
 * Create a global string
 */
 
function global_string() {
    return 'String';
}
```

**Retrieve Field**

```php
echo global_string();
```

### Support Featured Images

```php
/**
 * Support featured images
 */
 
add_theme_support( 'post-thumbnails' );
```

### Support Search Form

```php
/**
 * Support search form
 */
 
add_theme_support( 'html5', array( 'search-form' ) );
```

### Excluding Pages From Search

```php
/**
 * Excluding pages from search
 */
 
function exclude_pages_from_search() {
    global $wp_post_types;

    $wp_post_types['page']->exclude_from_search = true;
}
add_action( 'init', 'exclude_pages_from_search' );
```

### Disable xmlrpc.php

```php
/**
 * Disable xmlrpc.php
 */
 
add_filter( 'xmlrpc_enabled', '__return_false' );
remove_action( 'wp_head', 'rsd_link' );
remove_action( 'wp_head', 'wlwmanifest_link' );
```

### Escape HTML in Posts

```php
/**
 * Escape HTML in <code> or <pre><code> tags.
 */
 
function escapeHTML($arr) {
    if (version_compare(PHP_VERSION, '5.2.3') >= 0) {
        $output = htmlspecialchars($arr[2], ENT_NOQUOTES, get_bloginfo('charset'), false);
    }
    else {
        $specialChars = array(
            '&' => '&amp;',
            '<' => '&lt;',
            '>' => '&gt;'
        );

        // decode already converted data
        $data = htmlspecialchars_decode( $arr[2] );
        // escape all data inside <pre>
        $output = strtr( $data, $specialChars );
    }
    if (! empty($output)) {
        return  $arr[1] . $output . $arr[3];
    }    else     {
        return  $arr[1] . $arr[2] . $arr[3];
    }
}
function filterCode($data) { // Uncomment if you want to escape anything within a <pre> tag
    //$modifiedData = preg_replace_callback( '@(<pre.*>)(.*)(<\/pre>)@isU', 'escapeHTML', $data );
    $modifiedData = preg_replace_callback( '@(<code.*>)(.*)(<\/code>)@isU', 'escapeHTML', $data );
    $modifiedData = preg_replace_callback( '@(<tt.*>)(.*)(<\/tt>)@isU', 'escapeHTML', $modifiedData );

    return $modifiedData;
}
add_filter( 'content_save_pre', 'filterCode', 9 );
add_filter( 'excerpt_save_pre', 'filterCode', 9 );
```

Modified from [Escape HTML](https://wordpress.org/plugins/escape-html/).

### Create Custom Global Settings

```php
/**
 * Create custom global settings
 */
 
function custom_settings_page() { ?>
    <div class="wrap">
    <h1>Custom Settings</h1>
    <form method="post" action="options.php">
        <?php
            settings_fields( 'section' );
            do_settings_sections( 'theme-options' );
            submit_button();
        ?>
    </form>
    </div>
<?php }

function custom_settings_add_menu() {
    add_theme_page( 'Custom Settings', 'Custom Settings', 'manage_options', 'custom-settings', 'custom_settings_page', null, 99 );
}
add_action( 'admin_menu', 'custom_settings_add_menu' );

// Example setting
function setting_twitter() { ?>
    <input type="text" name="twitter" id="twitter" value="<?php echo get_option('twitter'); ?>" />
<?php }

function custom_settings_page_setup() {
    add_settings_section( 'section', 'All Settings', null, 'theme-options' );
    add_settings_field( 'twitter', 'Twitter Username', 'setting_twitter', 'theme-options', 'section' );
    register_setting( 'section', 'twitter' );
}
add_action( 'admin_init', 'custom_settings_page_setup' );
```

Retrieve Field

```php
echo get_option( 'twitter' );
```

Modified from [Create a WordPress Theme Settings Page with the Settings API](http://www.sitepoint.com/create-a-wordpress-theme-settings-page-with-the-settings-api/).

### Remove WordPress Admin Bar

```php
/**
 * Remove WordPress admin bar
 */

function remove_admin_bar() {
    remove_action( 'wp_head', '_admin_bar_bump_cb' );
}
add_action( 'get_header', 'remove_admin_bar' );
```

### Add Open Graph Meta Tags

```php
/**
 * Add Open Graph Meta Tags
 */

function meta_og() {
    global $post;

    if ( is_single() ) {
        if( has_post_thumbnail( $post->ID ) ) {
            $img_src = wp_get_attachment_image_src( get_post_thumbnail_id( $post->ID ), 'thumbnail' );
        } 
        $excerpt = strip_tags( $post->post_content );
        $excerpt_more = '';
        if ( strlen($excerpt ) > 155) {
            $excerpt = substr( $excerpt,0,155 );
            $excerpt_more = ' ...';
        }
        $excerpt = str_replace( '"', '', $excerpt );
        $excerpt = str_replace( "'", '', $excerpt );
        $excerptwords = preg_split( '/[\n\r\t ]+/', $excerpt, -1, PREG_SPLIT_NO_EMPTY );
        array_pop( $excerptwords );
        $excerpt = implode( ' ', $excerptwords ) . $excerpt_more;
        ?>
<meta name="author" content="Your Name">
<meta name="description" content="<?php echo $excerpt; ?>">
<meta property="og:title" content="<?php echo the_title(); ?>">
<meta property="og:description" content="<?php echo $excerpt; ?>">
<meta property="og:type" content="article">
<meta property="og:url" content="<?php echo the_permalink(); ?>">
<meta property="og:site_name" content="Your Site Name">
<meta property="og:image" content="<?php echo $img_src[0]; ?>">
<?php
    } else {
        return;
    }
}
add_action('wp_head', 'meta_og', 5);
```

### Disable the WordPress Version information in the head section

```php
    remove_action('wp_head', 'wp_generator');
```
