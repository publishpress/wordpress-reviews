# PublishPress WordPress Reviews Library
Library for displaying a banner asking for a 5-star review on WordPress plugins.

## Installation

We recommend using composer for adding this library as requirement:

```shell
$ composer require publishpress/wordpress-reviews
```

## How to use it

If your plugin do not load composer's autoloader yet, you need to add the following code:

```php
<?php

require_once 'vendor/autoload.php';
```

The library should be initialized in the method of your plugin that load the main WordPress hooks.
You can add it to the main class fo the plugin. When instantiating it you have to pass 2 params: the plugin slug (the same one used in the URL of the WordPress repository) and the plugin's name.

Pro plugins doesn't require this library, if they use they embed the free plugin. If you instantiate this library on both free and pro plugins, users will probably see duplicated banners.

It by default displays the banner when the following conditional is true:

```php
is_admin() && current_user_can('edit_posts')
```

But you should 

```php
<?php

use PublishPress\WordPressReviews\ReviewsController;

class MyPlugin
{
    /**
    * @var  ReviewsController
    */
    private $reviewController;
    
    public function __construct()
    {
        $this->reviewController = new ReviewsController('your-plugin-slug', 'Your Plugin Name');
    }
    
    public function init()
    {
        // .......
        $this->reviewController->init();
    }
    
    // .......
}
```

By default, the library will use the plugin's slug as prefix for the actions, meta data and options:

```php
[
    'action_ajax_handler' => $this->pluginSlug . '_action',
    'option_installed_on' => $this->pluginSlug . '_wp_reviews_installed_on',
    'nonce_action' => $this->pluginSlug . '_wp_reviews_action',
    'user_meta_dismissed_triggers' => '_' . $this->pluginSlug . '_wp_reviews_dismissed_triggers',
    'user_meta_last_dismissed' => '_' . $this->pluginSlug . '_wp_reviews_last_dismissed',
    'user_meta_already_did' => '_' . $this->pluginSlug . '_wp_reviews_already_did',
    'filter_triggers' => $this->pluginSlug . '_wp_reviews_triggers',
]
```

If you already use 
the original library in your plugin and want to keep compatibility with the current sites data, you can customize the
hooks and keys for the data stored in the DB using the filter `publishpress_wp_reviews_meta_map_<plugin_slug>`:

```php
<?php

add_filter('publishpress_wp_reviews_meta_map_my_plugin', 'my_plugin_wp_reviews_meta_map');

function my_plugin_wp_reviews_meta_map($metaMap)
{
    // You can override all the array, or specific keys.
    $metaMap = [
        'action_ajax_handler' => 'old_slug_ajax_action',
        'option_installed_on' => 'old_slug_wp_reviews_installed_on',
        'nonce_action' => 'old_slug_wp_reviews_action',
        'user_meta_dismissed_triggers' => '_old_slug_wp_reviews_dismissed_triggers',
        'user_meta_last_dismissed' => '_old_slug_wp_reviews_last_dismissed',
        'user_meta_already_did' => '_old_slug_wp_reviews_already_did',
        'filter_triggers' => 'old_slug_wp_reviews_triggers',
    ];

    return $metaMap;
}
```

## Testing

You can easily test the banner in the WordPress admin. 
After initializing the library, change the option `publishpress_wp_reviews_installed_on` in the options table. Set it for an older data to make sure the time difference is bigger than the trigger we are using.

## Copyright

Based on the [library](https://github.com/danieliser/WP-Product-In-Dash-Review-Requests) created by [Daniel Iser](https://danieliser.com).