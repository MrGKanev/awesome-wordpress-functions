# Customizations & Tweaks

- [Add Custom Post Type](#add-custom-post-type)
- [Exclude a Category From WordPress Loops](#exclude-a-category-from-wordpress-loops)
- [Implement Preconnect to Google Fonts in Themes](#implement-preconnect-to-google-fonts-in-themes)
- [Add Thumbnail Column to Post Listing](#add-thumbnail-column-to-post-listing)
- [Add Lead Class to First Paragraph](#add-lead-class-to-first-paragraph)
- [Exclude Custom Post Type from Search](#exclude-custom-post-type-from-search)
- [Remove Query String from Static Resources](#remove-query-string-from-static-resources)
- [Disable Website Field From Comment Form](#disable-website-field-from-comment-form)
- [Modify jQuery](#modify-jquery)
- [Disable JSON Rest API](#disable-json-rest-api)

###  Add Custom Post Type

```php
/**
 * Add custom post type
 */

function create_custom_post() {
    register_post_type( 'custom-post', // slug for custom post type
        array(
        'labels' => array(
            'name' => __( 'Custom Post' ),
        ),
        'public' => true,
        'hierarchical' => true, 
        'has_archive' => true,
        'supports' => array(
            'title',
            'editor',
            'excerpt',
            'thumbnail'
        ), 
        'can_export' => true,
        'taxonomies' => array(
                'post_tag',
                'category'
        )
    ));
}
add_action('init', 'create_custom_post');
```

### Exclude a Category From WordPress Loops

```php
/**
 * Exclude a category from all WordPress loops
 */

add_action( 'pre_get_posts', function( $query ) { // anonymous callback
    
    global $wp_query; 

    // Hard coded category ID, but can be dynamic: esc_attr(get_option('your-cat-id')); 
    $excluded_cat_id = 25;

    // add category ID to existing, avoid overwriting it 
    $cat[] = $query->get( 'cat' );
    $cat[] = "-" . $excluded_cat_id;

    $query->set( 'cat', $cat );
    }
});
```


###  Implement Preconnect to Google Fonts in Themes

```php
/**
 * Implement preconnect to Google Fonts in themes
 */

function twentyfifteen_resource_hints( $urls, $relation_type ) {
    // Checks whether the subject is carrying the source of fonts google and the `$relation_type` equals preconnect.
    // Replace `enqueue_font_id` the `ID` used in loading the source.
    if ( wp_style_is( 'enqueue_font_id', 'queue' ) && 'preconnect' === $relation_type ) {
        // Checks whether the version of WordPress is greater than or equal to 4.7
        // to ensure compatibility with older versions
        // because the 4.7 has become necessary to return an array instead of string
        if ( version_compare( $GLOBALS['wp_version'], '4.7-alpha', '>=' ) ) {
            // Array with url google fonts and crossorigin
            $urls[] = array(
                'href' => 'https://fonts.gstatic.com',
                'crossorigin',
            );
        } else {
            // String with url google fonts
            $urls[] = 'https://fonts.gstatic.com';
        }
    }
    return $urls;
}
add_filter( 'wp_resource_hints', 'twentyfifteen_resource_hints', 10, 2 ); 
```

###  Add Thumbnail Column to Post Listing

```php
/**
 * Add thumbnail column to post listing
 */

add_image_size( 'admin-list-thumb', 80, 80, false );

function wpcs_add_thumbnail_columns( $columns ) {
     
    if ( !is_array( $columns ) )
        $columns = array();
    $new = array();

    foreach( $columns as $key => $title ) {
        if ( $key == 'title' ) // Put the Thumbnail column before the Title column
            $new['featured_thumb'] = __( 'Image');
        $new[$key] = $title;
    }
    return $new;
}

function wpcs_add_thumbnail_columns_data( $column, $post_id ) {
    switch ( $column ) {
    case 'featured_thumb':
        echo '<a href="' . $post_id . '">';
        echo the_post_thumbnail( 'admin-list-thumb' );
        echo '</a>';
        break;
    }
}

if ( function_exists( 'add_theme_support' ) ) {
    add_filter( 'manage_posts_columns' , 'wpcs_add_thumbnail_columns' );
    add_action( 'manage_posts_custom_column' , 'wpcs_add_thumbnail_columns_data', 10, 2 );
}
```

###  Add Lead Class to First Paragraph

```php
/**
 * Add lead class to first paragraph
 */

function first_paragraph( $content ) {
    return preg_replace( '/<p([^>]+)?>/', '<p$1 class="lead">', $content, 1 );
}
add_filter( 'the_content', 'first_paragraph' );
```

Adds a `lead` class to the first paragraph in [the_content](https://developer.wordpress.org/reference/functions/the_content/).

###  Exclude Custom Post Type from Search

```php
/**
 * Exclude custom post type from search
 */

function excludePages( $query ) {
if ( $query->is_search ) {
    $query->set( 'post_type', 'post' );
}
    return $query;
}
add_filter( 'pre_get_posts','excludePages' );
```

###  Remove Query String from Static Resources

```php
/**
 * Remove query string from static resources 
 */
 
function remove_cssjs_ver( $src ) {
    if ( strpos( $src, '?ver=' ) )
        $src = remove_query_arg( 'ver', $src );
    return $src;
}
add_filter( 'style_loader_src', 'remove_cssjs_ver', 10, 2 );
add_filter( 'script_loader_src', 'remove_cssjs_ver', 10, 2 );
```

###  Modify jQuery

```php
/**
 * Modify jQuery
 */

function modify_jquery() {
    wp_deregister_script( 'jquery' );
    wp_register_script( 'jquery', 'https://ajax.googleapis.com/ajax/libs/jquery/3.2.1/jquery.min.js', false, '3.2.1' );
    wp_enqueue_script( 'jquery' );
}
if (!is_admin()) add_action('wp_enqueue_scripts', 'modify_jquery');
```

###  Disable Website Field From Comment Form

```php
/** 
 * Disable website field from comment form
 */

function disable_website_field( $field ) { 
    if( isset($field['url']) ) {
        unset( $field['url'] );
    }
    return $field;
}
add_filter('comment_form_default_fields', 'disable_website_field');
```

###  Disable JSON REST API

```php
/** 
 * Disable JSON REST API  
 */

add_filter('json_enabled', '__return_false');
add_filter('json_jsonp_enabled', '__return_false');
```
