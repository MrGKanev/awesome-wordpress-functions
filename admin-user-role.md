# Admin Menu & User Role Management

- [Remove Admin Menu Items Depending on User Role](#remove-admin-menu-items-depending-on-user-role)
- [Remove Admin Menu Items Depending on Email Address (Domain)](#remove-admin-menu-items-depending-on-email-address-domain)
- [Reorder Admin Menu Items](#reorder-admin-menu-items)

## Remove Admin Menu Items Depending on User Role

```php
/**
 * Clone the administrator user role
 */

function clone_admin_role() {
    global $wp_roles;
    if ( ! isset( $wp_roles ) )
        $wp_roles = new WP_Roles();
    
    $adm = $wp_roles->get_role( 'administrator' );
    
    // Add new "Client" role with all admin capabilities
    $wp_roles->add_role( 'client', 'Client', $adm->capabilities );
}
add_action( 'init', 'clone_admin_role' );

/**
 * Specify which admin menu items are visible for users with role "Client"
 */

function remove_dashboard_menus() {
    if ( current_user_can( 'client' ) ) {
        // Hide Updates under Dashboard menu
        remove_submenu_page( 'index.php', 'update-core.php' );

        // Hide Comments
        remove_menu_page( 'edit-comments.php' );

        // Hide Plugins
        remove_menu_page( 'plugins.php' );

        // Hide Themes, Customizer and Widgets under Appearance menu
        remove_submenu_page( 'themes.php', 'themes.php' );
        remove_submenu_page( 'themes.php', 'customize.php?return=' . urlencode( $_SERVER['REQUEST_URI'] ) );
        remove_submenu_page( 'themes.php', 'widgets.php' );

        // Hide Tools
        remove_menu_page( 'tools.php' );

        // Hide General Settings
        remove_menu_page( 'options-general.php' );
    }
}
add_action( 'admin_menu', 'remove_dashboard_menus' );
```

## Remove Admin Menu Items Depending on Email Address (domain)

```php
/**
 * Specify which users can see admin menu items based on their email address
 */

function remove_dashboard_menus() {
    $user_data = get_userdata( get_current_user_id() );
    $user_email = isset( $user_data->user_email ) ? $user_data->user_email : '';

    if ( ! strpos( $user_email, '@yourcompany.com' ) ) {
        // Hide Updates under Dashboard menu
        remove_submenu_page( 'index.php', 'update-core.php' );

        // Hide Comments
        remove_menu_page( 'edit-comments.php' );

        // Hide Plugins
        remove_menu_page( 'plugins.php' );

        // Hide Themes, Customizer and Widgets under Appearance menu
        remove_submenu_page( 'themes.php', 'themes.php' );
        remove_submenu_page( 'themes.php', 'customize.php?return=' . urlencode( $_SERVER['REQUEST_URI'] ) );
        remove_submenu_page( 'themes.php', 'widgets.php' );

        // Hide Tools
        remove_menu_page( 'tools.php' );

        // Hide General Settings
        remove_menu_page( 'options-general.php' );
    }
}
add_action( 'admin_menu', 'remove_dashboard_menus' );
```

## Reorder Admin Menu Items

```php
/**
 * Reorder admin menu
 */

function custom_menu_order( $menu_ord ) {
    if ( ! $menu_ord ) { return true; }
        return array(
            'index.php',
            'separator1',
            'edit.php?post_type=page', 
            'edit.php', 
            'edit.php?post_type=[your_post_type_slug]',
            'upload.php',
            'edit-comments.php',
            'separator2',
            'themes.php',
            'plugins.php',
            'users.php',
            'tools.php',
            'options-general.php'
        );
    }
}
add_filter( 'custom_menu_order', 'custom_menu_order' );
add_filter( 'menu_order', 'custom_menu_order' );
```
