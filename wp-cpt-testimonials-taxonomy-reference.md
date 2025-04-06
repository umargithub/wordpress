# 📘 WordPress Custom Post Type & Taxonomy Reference: Testimonials (with Slug Removal)

## 📦 Registering Custom Post Type: **Testimonials**

```php
function register_cpt_testimonial() {
    $labels = [
        'name' => 'Testimonials',
        'singular_name' => 'Testimonial',
        'add_new' => 'Add New',
        'add_new_item' => 'Add New Testimonial',
        'edit_item' => 'Edit Testimonial',
        'new_item' => 'New Testimonial',
        'view_item' => 'View Testimonial',
        'search_items' => 'Search Testimonials',
        'not_found' => 'No Testimonials Found',
        'not_found_in_trash' => 'No Testimonials Found in Trash',
        'all_items' => 'All Testimonials',
        'menu_name' => 'Testimonials',
    ];

    register_post_type('testimonial', [
        'labels' => $labels,
        'public' => true,
        'has_archive' => true,
        'rewrite' => ['slug' => 'testimonial'], // use '' if removing slug
        'supports' => ['title', 'editor', 'thumbnail'],
        'menu_icon' => 'dashicons-testimonial',
        'show_in_rest' => true,
    ]);
}
add_action('init', 'register_cpt_testimonial');
```

---

### 🏷 Registering Custom Taxonomy: **Testimonial Categories**

```php
function register_testimonial_taxonomy() {
    $labels = [
        'name' => 'Testimonial Categories',
        'singular_name' => 'Testimonial Category',
        'search_items' => 'Search Categories',
        'all_items' => 'All Categories',
        'edit_item' => 'Edit Category',
        'update_item' => 'Update Category',
        'add_new_item' => 'Add New Category',
        'new_item_name' => 'New Category Name',
        'menu_name' => 'Categories',
    ];

    register_taxonomy('testimonial_category', 'testimonial', [
        'labels' => $labels,
        'hierarchical' => true,
        'show_in_rest' => true,
        'rewrite' => ['slug' => 'testimonial-category'], // change or remove if needed
    ]);
}
add_action('init', 'register_testimonial_taxonomy');
```

---

### 🔍 WP_Query for Testimonials

```php
$args = [
    'post_type' => 'testimonial',
    'posts_per_page' => 5,
    'tax_query' => [[
        'taxonomy' => 'testimonial_category',
        'field' => 'slug',
        'terms' => 'client-feedback',
    ]],
];
$query = new WP_Query($args);

if ($query->have_posts()) :
    while ($query->have_posts()) : $query->the_post();
        the_title('<h2>', '</h2>');
        the_content();
    endwhile;
    wp_reset_postdata();
endif;
```

---

### 🧼 Remove CPT Slug from URL

**From:** `yoursite.com/testimonial/john-smith`  
**To:** `yoursite.com/john-smith`

#### 1. CPT slug as `/` in rewrite (optional)

```php
// in register_post_type
'rewrite' => ['slug' => '/']
```

#### 2. Filter and hook

```php
function remove_testimonial_slug($post_link, $post) {
    if ('testimonial' === $post->post_type && 'publish' === $post->post_status) {
        return home_url('/' . $post->post_name . '/');
    }
    return $post_link;
}
add_filter('post_type_link', 'remove_testimonial_slug', 10, 2);

function support_testimonial_slugless($query) {
    if (!isset($query->query_vars['name']) || isset($query->query_vars['post_type'])) return;

    $post = get_page_by_path($query->query_vars['name'], OBJECT, 'testimonial');
    if ($post) {
        $query->set('post_type', 'testimonial');
    }
}
add_action('pre_get_posts', 'support_testimonial_slugless');
```

---

### 🚫 (Optional) Remove Taxonomy Slug from URL

**From:** `yoursite.com/testimonial-category/happy-clients`  
**To:** `yoursite.com/happy-clients`

```php
add_filter('term_link', function($url, $term, $taxonomy) {
    if ('testimonial_category' === $taxonomy) {
        return str_replace('/testimonial-category/', '/', $url);
    }
    return $url;
}, 10, 3);

add_action('request', function($vars) {
    if (isset($vars['pagename'])) {
        $term = get_term_by('slug', $vars['pagename'], 'testimonial_category');
        if ($term) {
            $vars['testimonial_category'] = $vars['pagename'];
            unset($vars['pagename']);
        }
    }
    return $vars;
});
```

---

### 🔄 Flush Permalinks (Required after slug removal)

```php
function flush_on_activation() {
    register_cpt_testimonial();
    register_testimonial_taxonomy();
    flush_rewrite_rules();
}
register_activation_hook(__FILE__, 'flush_on_activation');
```

Or manually: **Settings > Permalinks > Save Changes**
