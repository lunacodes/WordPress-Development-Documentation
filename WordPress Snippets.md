# WordPress Snippets

<!-- MarkdownTOC -->

* .htaccess
    * 1. Automatic rewrites for SSL
* wp-config.php
  * 1. Disable WordFence:
  * 2. Permanently Delete WordPress Default Themes
* Admin Customization
  * 1. Disable all\(?\) plugin notifications:
  * 2. Remove WordPress Version from Dashboard Footer
  * 3. Remove WordPress Version from Dashboard Footer for non-admins
* Shortcodes
  * 1. Add a shortcode
* jQuery
  * 1. Load jQuery
* Icon Fonts
  * 1. Load Font Awesome
* Troubleshooting and Hacking
  * 1. Rewrite A User's Password
* Database
  * 1. Database Collation and Character Set Issues

<!-- /MarkdownTOC -->

<br />
## .htaccess  

#### 1. Automatic rewrites for SSL 

This makes sure you don't lose your existing links from other sites. Do this 
in addition to changing your Site/Home URL in Settings:

```apacheconf
<IfModule mod_rewrite.c>
  RewriteEngine On
  RewriteCond %{SERVER_PORT} 80 
  RewriteRule ^(.*)$ https://www.dart-creations.com/$1 [R=301,L]
</IfModule>
```

<br />
## wp-config.php

### 1. Disable WordFence:
```php
<?php define('WFWAF_ENABLED', false); ?>
```

### 2. Permanently Delete WordPress Default Themes

Stop WordPress from including the Twenty Fourteen - Twenty Seventeen themes when 
it auto-upgrades. 

Note: I would make sure to still have Twenty Seventeen installed and updated for 
troubleshooting and site-restoration purposes

```php
<?php define( 'CORE_UPGRADE_SKIP_NEW_BUNDLED', true ); ?>
```

<br />
## Admin Customization

### 1. Disable all(?) plugin notifications:
```php 
<?php add_filter('site_transient_update_plugins', '__return_false'); ?> 
```

### 2. Remove WordPress Version from Dashboard Footer
```php
<?php 

add_action( 'admin_menu', 'my_footer_shh' );
function remove_wp_version_footer() {
    remove_filter( 'update_footer', 'core_update_footer' ); 
}

?>
```

### 3. Remove WordPress Version from Dashboard Footer for non-admins

```php
<?php

add_action ( 'admin_menu', 'remove_wp_version_footer_non_admins' );
function remove_wp_version_footer_non_admins() {
    if ( ! current_user_can('manage_options') ) {
        remove_filter( 'update_footer', 'core_update_footer' );
    }
}

?>
```


<br />
## Shortcodes

### 1. Add a shortcode

```php
<?php

/**
 * To Add a Shortcode
 * @link http://www.billerickson.net/using-shortcodes/
 * @author Bill Erickson
 *
 * @return string ad
 */

function be_ad_shortcode() {
    $ad = '<a href="http://www.example.com"><img src="'.get_stylesheet_directory_uri().'/images/ad.jpg" /></a>';
    return $ad;
}
add_shortcode( 'ad', 'be_ad_shortcode' ); 

?>
```

<br />
## jQuery

### 1. Load jQuery

```php

<?php

/**
 * 
 * Wrapper function to properly load (enqueue) jQuery, in case 
 * your theme or plugin hasn't done so 
 *
 */

add_action( 'wp_enqueue_scripts', 'load_jquery' );
function load_jquery() {
    wp_enqueue_script('jquery');
} 

?>
```

<br />
## Icon Fonts

### 1. Load Font Awesome

```php
<?php 

/**
 * Enqueues Font Awesome stylesheet from the Bootstrap CDN
 */

add_action('wp_enqueue_scripts','enqueue_our_required_stylesheets');
function enqueue_our_required_stylesheets(){
  wp_enqueue_style('font-awesome', '//maxcdn.bootstrapcdn.com/font-awesome/4.3.0/css/font-awesome.min.css'); 
}

?>
```


<br />
## Troubleshooting and Hacking

### 1. Rewrite A User's Password

```php
<?php

/**
 * This script will rewrite a user's password
 * You can either place it in your functions.php file or
 * Upload it as a separate script (ex: a file named rewrite-password.php)
 * Take great care with this, and make sure to immediately delete it once the issue is taken care of
 * 
 * This script can save you if you're locked out of your Site/Dashboard
 * It is also convenient in that you can easily reset any user's password for security purposes 
 * 
 * The example targets the user with ID 1, as that is usually the Admin account
 */

// Sets password for the user with ID 1 to 'new-password'
wp_set_password( 'new-password', 1 );


?>
```

Note: An alternate method of doing this is to use phpMyAdmin or similar and navigate to the wp_users table, and manually change the password there.

I have seen both of these methods fail (usually due to a Database Collation or Character Set issue - either within the Database itself, or between wp-config.php). See the [Database Collation and Character Set Issues](#1-database-collation-and-character-set-issues) section for more info and troubleshooting suggestions.

<br />
## Database 

### 1. Database Collation and Character Set Issues

If you are having issues with your Database misbehaving, you may have Collation or Character Set Issues. If so, try the following:

* [WordPress Codex - Editing wp-config.php - Databse Collation](https://codex.wordpress.org/Editing_wp-config.php#Database_collation)
* [WordPress Codex - Converting Database Character Sets](https://codex.wordpress.org/Converting_Database_Character_Sets)
* [WordPress Plugins Directory - Duplicator Plugin](https://wordpress.org/plugins/duplicator/)

> If you're having trouble understanding the first two links, I'd recommend 
trying to solve the problem with Duplicator. Duplicator lets you specify the 
Collation and Charset when you run the install script, and it will essentially 
handle this process for you.

The WordPress Codex [says](https://codex.wordpress.org/Editing_wp-config.php#Database_character_set) that it's best to set the Database character set
in `wp-config.php` to `'utf-8'`, and the collation to `'utf8_general_ci'`. 
The `wp-config.php` file itself says that it's best to leave the `DB_COLLATE` 
field blank, unless you have a reason not to. I agree with the `wp-config.php` 
file

> I disagree about the charset and collation, and recommend using 
`utf_8_unicode_ci` instead of `utf8_general_ci`, as the latter is an older and 
outdated standard, and the performance difference between the two is no longer 
relevant. Some people also suggest using `utf8mb4` and `utf8mb4_unicode_ci` 
instead. You can read more about this all [here](http://stackoverflow.com/questions/766809/whats-the-difference-between-utf8-general-ci-and-utf8-unicode-ci).

Personally, I'd just stick with the default in [Duplicator](https://wordpress.org/plugins/duplicator/), but whatever you do: **Always Test Locally First.** 

<!-- <br /> -->
<!-- ## WordPress Installations and Migrations -->
