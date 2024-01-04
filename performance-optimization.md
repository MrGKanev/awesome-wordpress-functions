# Performance Optimization

- [Load heavy 3rd-party scripts later for better performance](#load-heavy-3rd-party-scripts-later-for-better-performance)
- [Set custom favicon on admin pages](#set-custom-favicon-on-admin-pages)
- [Disable the message - JQMIGRATE: Migrate is installed, version 1.4.1]    (#disable-the-message---jqmigrate-migrate-is-installed-version-141)

###  Load heavy 3rd-party scripts later for better performance

Lighthouse and similar performance analysis tools always complain about render-blocking scripts (and styles),
short cache TTL etc. Most of these scripts and styles come from 3rd-party sources which we can't control –
Google's own Tag Manager and Analytics, Facebook Pixel, other trackers and chat scripts etc. However, we
can load them only when a real user interacts with a page, significantly reducing the Time To Interactive
metric and scoring much higher performance results.

Depending on where you like these 3rd-party scripts to be, you can either use `wp_footer` action to print the
code in footer, or put it in your main `app.js` script which, in turn, is enqueued on `wp_enqueue_scripts` action.

```javascript
<script>
var fired = false;

window.addEventListener('scroll', () => {
    if (fired === false) {
        fired = true;
        
        setTimeout(() => {

            // Marketing scripts go here.

        }, 1000) // 1000ms or 1s works fine, but you can adjust this timeout.
    }
});
</script>
```

### Set custom favicon on admin pages

Useful for distinguishing between front-end tabs and admin tabs at a glance.

```php
add_action('admin_head', function () { ?>
  <link rel="icon" type="image/png" href="/path/to/other/favicon.png">
<?php });
```

### Disable the message - JQMIGRATE: Migrate is installed, version 1.4.1

```php
add_action('wp_default_scripts', function ($scripts) {
    if (!empty($scripts->registered['jquery'])) {
        $scripts->registered['jquery']->deps = array_diff($scripts->registered['jquery']->deps, ['jquery-migrate']);
    }
});
```
