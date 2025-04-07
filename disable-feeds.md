# ✅ **Disabling All Feeds in WordPress (Full Reference)**

Use the following snippets (as needed) in your `functions.php` or a custom plugin to **disable RSS, Atom, RDF, and individual post feeds**, while preventing unexpected feed behavior across WordPress setups (including WPEngine hosting):

---

## 🚫 **1. Disable All Feed Endpoints + Remove Feed Links From `<head>`**

```php
add_action('init', function () {
    // Remove core feed endpoints
    remove_action('do_feed', 'do_feed_rss2', 10);
    remove_action('do_feed_rdf', 'do_feed_rdf', 10);
    remove_action('do_feed_rss', 'do_feed_rss', 10);
    remove_action('do_feed_atom', 'do_feed_atom', 10);

    // Remove feed links from head
    remove_action('wp_head', 'feed_links', 2);
    remove_action('wp_head', 'feed_links_extra', 3);
});
```

---

## 🔄 **2. Redirect Any Feed Attempt to Homepage (Standard Redirect)**

```php
function redirect_all_feeds_to_home() {
    if (is_feed()) {
        wp_redirect(home_url(), 301);
        exit;
    }
}
add_action('template_redirect', 'redirect_all_feeds_to_home');
```

> ✅ _For Multisite: Use the following variation to only apply on specific site IDs (e.g., 1, 2, 3):_

```php
function redirectFeeds() {
    if (is_feed()) {
        $currentSiteId = get_current_blog_id();
        if (in_array($currentSiteId, [1, 2, 3])) {
            wp_redirect(home_url(), 301);
            exit;
        }
    }
}
add_action('template_redirect', 'redirectFeeds');
```

> 🔀 _Multisite tip:_ Use `get_current_blog_id()` to conditionally redirect feeds only for specific subsites.

---

## 🧠 **3. Redirect Individual Post Feeds to Their Post (Optional Behavior)**

```php
function redirect_post_feed_to_post() {
    if (is_singular() && is_feed()) {
        wp_redirect(get_permalink(), 301);
        exit;
    } elseif (is_feed()) {
        wp_redirect(home_url(), 301);
        exit;
    }
}
add_action('template_redirect', 'redirect_post_feed_to_post');
```

---

## 🔍 **4. Prevent Feed Queries Using `pre_get_posts`**

This ensures any feed request at query level (especially archive or category feeds) is blocked:

```php
add_action('pre_get_posts', function ($query) {
    if ($query->is_feed() && !is_home()) {
        wp_redirect(home_url('/'), 301);
        exit;
    }
});
```

---

## 🛡️ **5. WPEngine-Specific Safe Feed Block (Handles Headers Sent Issue)**

Especially useful if you're on WPEngine or other managed hosts that buffer aggressively:

```php
function wpengine_disable_feeds() {
    if (is_feed()) {
        if (!headers_sent()) {
            wp_redirect(home_url('/'), 301);
            exit;
        } else {
            // For debug only; remove in production
            echo 'Redirect failed: headers already sent.';
            exit;
        }
    }
}
add_action('template_redirect', 'wpengine_disable_feeds');
```

---

### 💡 Notes

- Use **only one `template_redirect`-based redirect** to avoid duplicate logic.
- `pre_get_posts` works at query level, while `template_redirect` works at page level.
- On WPEngine, use the `headers_sent()` check to prevent blank or broken responses.
- You can combine safe redirect logic with global feed disabling and head cleanup for a full-proof block.
