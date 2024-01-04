# General WordPress Setup

- [Hide WordPress Update Nag to All But Admins](#hide-wordpress-update-nag-to-all-but-admins)
- [Utilize Proper WordPress Titles](#utilize-proper-wordpress-titles)
- [Create Custom WordPress Dashboard Widget](#create-custom-wordpress-dashboard-widget)
- [Remove All Dashboard Widgets](#remove-all-dashboard-widgets)
- [Include Navigation Menus](#include-navigation-menus)
- [Insert Custom Login Logo](#insert-custom-login-logo)
- [Modify Admin Footer Text](#modify-admin-footer-text)
- [Enqueue Styles and Scripts](#enqueue-styles-and-scripts)
- [Enqueue Google Fonts](#enqueue-google-fonts)

### Hide WordPress Update Nag to All But Admins

```php
/**
 * Hide WordPress update nag to all but admins
 */
 
function hide_update_notice_to_all_but_admin() {
    if ( !current_user_can( 'update_core' ) ) {
        remove_action( 'admin_notices', 'update_nag', 3 );
    }
}
add_action( 'admin_head', 'hide_update_notice_to_all_but_admin', 1 );
```

### Utilize Proper WordPress Titles

Make sure to remove the `<title>` tag from your header.

```php
/**
 * Utilize proper WordPress titles
 */

add_theme_support( 'title-tag' );
```

### Create Custom WordPress Dashboard Widget

```php
/**
 * Create custom WordPress dashboard widget
 */
 
function dashboard_widget_function() {
    echo '
        <h2>Custom Dashboard Widget</h2>
        <p>Custom content here</p>
    ';
}

function add_dashboard_widgets() {
    wp_add_dashboard_widget( 'custom_dashboard_widget', 'Custom Dashoard Widget', 'dashboard_widget_function' );
}
add_action( 'wp_dashboard_setup', 'add_dashboard_widgets' );
```

### Remove All Dashboard Widgets

```php
/**
 * Remove all dashboard widgets
 */
 
function remove_dashboard_widgets() {
    global $wp_meta_boxes;
    
    unset( $wp_meta_boxes['dashboard']['side']['core']['dashboard_quick_press'] );
    unset( $wp_meta_boxes['dashboard']['normal']['core']['dashboard_incoming_links'] );
    unset( $wp_meta_boxes['dashboard']['normal']['core']['dashboard_right_now'] );
    unset( $wp_meta_boxes['dashboard']['normal']['core']['dashboard_plugins'] );
    unset( $wp_meta_boxes['dashboard']['normal']['core']['dashboard_recent_drafts'] );
    unset( $wp_meta_boxes['dashboard']['normal']['core']['dashboard_recent_comments'] );
    unset( $wp_meta_boxes['dashboard']['side']['core']['dashboard_primary'] );
    unset( $wp_meta_boxes['dashboard']['side']['core']['dashboard_secondary'] );

    remove_meta_box( 'dashboard_activity', 'dashboard', 'normal' );
}
add_action( 'wp_dashboard_setup', 'remove_dashboard_widgets' );
```

### Include Navigation Menus

```php
/** 
 * Include navigation menus
 */

function register_my_menu() {
    register_nav_menu( 'nav-menu', __( 'Navigation Menu' ) );
}
add_action( 'init', 'register_my_menu' );
```

Insert this where you want it to appear, and save the menu in **Appearance -> Menus**.

```php
wp_nav_menu( array( 'theme_location' => 'nav-menu' ) );
```

Here's the code for multiple menus:

```php
function register_my_menus() {
    register_nav_menus(
        array(
            'new-menu' => __( 'New Menu' ),
            'another-menu' => __( 'Another Menu' ),
            'an-extra-menu' => __( 'An Extra Menu' ),
        )
    );
}
add_action( 'init', 'register_my_menus' );
```

### Insert Custom Login Logo

```php
/**
 * Insert custom login logo
 */
 
function custom_login_logo() {
    echo '
        <style>
            .login h1 a { 
                background-image: url(image.jpg) !important; 
                background-size: 234px 67px; 
                width:234px; 
                height:67px; 
                display:block; 
            }
        </style>
    ';
}
add_action( 'login_head', 'custom_login_logo' );
```

### Modify Admin Footer Text

```php
/**
 * Modify admin footer text
 */
 
function modify_admin_footer() {
    echo 'Created by <a href="mailto:you@example.com">you</a>.';
}
add_filter( 'admin_footer_text', 'modify_admin_footer' );
```

### Enqueue Styles and Scripts

```php
/**
 * Enqueue styles and scripts
 */
 
function custom_scripts() {
    wp_enqueue_style( 'bootstrap', get_template_directory_uri() . '/css/bootstrap.min.css', array(), '3.3.6' );
    wp_enqueue_style( 'style', get_template_directory_uri() . '/css/style.css' );
    wp_enqueue_script( 'bootstrap', get_template_directory_uri() . '/js/bootstrap.min.js', array('jquery'), '3.3.6', true );
    wp_enqueue_script( 'script', get_template_directory_uri() . '/js/script.js' );
}
add_action( 'wp_enqueue_scripts', 'custom_scripts' );
```

### Enqueue Google Fonts

```php
/**
 * Enqueue Google Fonts
 */
 
function google_fonts() {
    wp_register_style( 'OpenSans', '//fonts.googleapis.com/css?family=Open+Sans:400,600,700,800' );
    wp_enqueue_style( 'OpenSans' );
}
add_action( 'wp_print_styles', 'google_fonts' );
```
