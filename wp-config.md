# WP Config

- [Change Table Prefixes](#change-table-prefixes)
- [Enable the Wordpress Debugger](#enable-the-wordpress-debugger)
- [Disable the theme and plugin editor](#disable-the-theme-and-plugin-editor)
- [Moving wp-content Directory](#moving-wp-content-directory)
- [Limiting HTTPS requests](#limiting-https-requests)


### Change Table Prefixes

```php
$table_prefix = 'wp_';
```

### [Enable the Wordpress Debugger](#enable-the-wordpress-debugger)

```php
define( 'WP_DEBUG', true );
define( 'WP_DEBUG_LOG', true );
define( 'WP_DEBUG_DISPLAY', false );

```

### [Disable the theme and plugin editor](#disable-the-theme-and-plugin-editor)

```php
define( 'DISALLOW_FILE_EDIT', true );

```

### [Moving wp-content Directory](#moving-wp-content-directory)

You can move the content directory and the plugin directory in the same way.

```php
define( 'WP_CONTENT_DIR', $_SERVER['DOCUMENT_ROOT'] . '/blog/wp-content' );
define( 'WP_CONTENT_URL', 'http://example/blog/wp-content');
define( 'WP_PLUGIN_DIR', $_SERVER['DOCUMENT_ROOT'] . '/blog/wp-content/plugins' );
define( 'WP_PLUGIN_URL', 'http://example/blog/wp-content/plugins');
```

### [Limiting HTTPS requests](#limiting-https-requests)

```php
define( 'WP_HTTP_BLOCK_EXTERNAL', true );
```

Or only allow certain hosts:

```php
define( 'WP_ACCESSIBLE_HOSTS', 'api.wordpress.org,*.github.com' );

```
