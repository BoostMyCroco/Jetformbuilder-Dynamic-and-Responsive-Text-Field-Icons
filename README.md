# Dynamic and Responsive Text Field Icons

This solution dynamically adds icons to text fields and ensures that the text does not overlap with the icons by calculating the `padding-left` dynamically. It is fully responsive and works for any text field with an icon.

---

## How It Works

1. **PHP Filter**:
   - A PHP filter scans the content for text fields with the class `text-field-icon` and a specific FontAwesome icon class (e.g., `fa-envelope` or `fa-phone`).
   - It wraps the input field and the icon in a `div` container.

2. **CSS**:
   - Basic styles are applied to position the icon and ensure the input field has enough space for the icon.

3. **JavaScript**:
   - A JavaScript script calculates the `padding-left` dynamically based on the width of the icon.
   - It ensures the `padding-left` is responsive by recalculating it when the window is resized.

---

## How to Use
1. Add the PHP code to your theme's functions.php file or a custom plugin.
2. Ensure the text fields have the class text-field-icon and a FontAwesome icon class (e.g., fa-envelope).
3. The icons will be added automatically, and the padding-left will be calculated dynamically.

## Code Implementation

### PHP (Filter to Add Icons and styles)

```php
add_action('wp_enqueue_scripts', function() {
    wp_enqueue_style('font-awesome', 'https://cdnjs.cloudflare.com/ajax/libs/font-awesome/5.15.4/css/all.min.css', [], null);
});

add_filter('the_content', 'add_custom_icon_to_text_fields', 20);
function add_custom_icon_to_text_fields($content) {
    if (preg_match_all('/<input[^>]*class="[^"]*text-field-icon[^"]*fa-([a-z0-9-]+)[^"]*"[^>]*>/i', $content, $matches)) {
        foreach ($matches[0] as $index => $input_field) {
            $icon_name = $matches[1][$index];
            $icon_class = 'fa-' . $icon_name;

            // Generate HTML with the icon and input field
            $icon_html = '<div class="text-field-icon-wrapper">';
            $icon_html .= '<i class="fa ' . $icon_class . '"></i>';
            $icon_html .= str_replace('class="', 'class="text-field-with-icon ', $input_field);
            $icon_html .= '</div>';

            // Replace the original input field in the content
            $content = str_replace($input_field, $icon_html, $content);
        }
    }
    return $content;
}

add_action('wp_enqueue_scripts', function () {
    // Load Font Awesome
    wp_enqueue_style('font-awesome', 'https://cdnjs.cloudflare.com/ajax/libs/font-awesome/5.15.4/css/all.min.css', [], null);

    // Custom CSS
    $custom_css = "
        .text-field-icon-wrapper {
            position: relative;
            width: 100%;
            display: block;
        }
        .text-field-icon-wrapper i {
            position: absolute;
            left: 10px;
            top: 50%;
            transform: translateY(-50%);
            color: #fff;
            font-size: 1.2em;
            pointer-events: none;
            background: hsl(11deg 100% 62.33%);
            padding: 8px;
            border-radius: 50%;
        }
        .text-field-with-icon {
            width: 100%;
            box-sizing: border-box;
        }
        @media (max-width: 768px) {
            .text-field-icon-wrapper i {
                font-size: 1em; /* Smaller icon on mobile */
            }
        }
    ";
    wp_add_inline_style('font-awesome', $custom_css);
});

add_action('wp_footer', 'dynamic_padding_for_text_fields');
function dynamic_padding_for_text_fields() {
    ?>
    <script type="text/javascript">
        document.addEventListener('DOMContentLoaded', function() {
            // Select all text field containers with icons
            const wrappers = document.querySelectorAll('.text-field-icon-wrapper');

            wrappers.forEach(wrapper => {
                // Get the icon and input field inside the container
                const icon = wrapper.querySelector('i');
                const input = wrapper.querySelector('input');

                if (icon && input) {
                    // Calculate padding-left based on the icon's width
                    const iconWidth = icon.offsetWidth;
                    const paddingLeft = iconWidth + 15; // Add extra space (15px)
                    input.style.paddingLeft = `${paddingLeft}px`;

                    // Adjust padding-left on window resize
                    window.addEventListener('resize', () => {
                        const newIconWidth = icon.offsetWidth;
                        const newPaddingLeft = newIconWidth + 15;
                        input.style.paddingLeft = `${newPaddingLeft}px`;
                    });
                }
            });
        });
    </script>
    <?php
}



