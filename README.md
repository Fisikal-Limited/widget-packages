# General
## Configuration Parameters
- basepath (Optional): Custom base path

Links:
[Web Portal](https://cuttingedge.fisikal.com) | 
[Developer API](https://cuttingedge.fisikal.com/#/home/developers) | 
[FAQ](https://knowledge.fisikal.com/en/knowledge) 

# Installing Purchase Widget in WordPress Project

## Installation Steps
1. Download `purchase-{brand}.tgz`
2. Extract files in `wp-content/themes/{your-theme}/js/purchase-widget/`
3. Make sure there are 3 files (`package.json, widget.js, widget.umd.cjs`)
4. Enqueue the Widget Script in WordPress
Add to `functions.php`
```
function enqueue_purchase_widget() {
     wp_enqueue_script(
         'purchase-widget', 
         get_template_directory_uri() . '/js/purchase-widget/widget.umd.cjs', 
         [], 
         '1.0.0', // Don't forget to change version
         true
     );
 }
 add_action('wp_enqueue_scripts', 'enqueue_purchase_widget');
```

## Pattern File (Optional)
Create `/wp-content/themes/your-theme/patterns/payment-widget.php`

```
<?php
   /**
    * Title: Payment Widget Pattern
    * Slug: your-theme/payment-widget
    * Categories: widget
    */
   ?>

   <div class="payment-widget-container">
       <payment-widget
       basepath='/widget-test'> // Optional param, ignore if needed
       </payment-widget>
   </div>
```

### Register Pattern (Optional):
Add in `functions.php`
```
function register_payment_widget_pattern() {
   register_block_pattern(
       'your-theme/payment-widget',
       [
           'title'    => __( 'Payment Widget', 'your-theme' ),
           'content'  => file_get_contents( get_template_directory() . '/patterns/payment-widget.php' ),
           'category' => 'widget',
       ]
   );
}
add_action( 'init', 'register_payment_widget_pattern' );
```

## Usage
### Direct Placement
```
<payment-widget
	basepath='/widget-test'> // Optional param, ignore if needed
</payment-widget>
```

### WordPress Shortcode (Optional)
Add to `functions.php`
```
function payment_widget_shortcode($atts) {
    $atts = shortcode_atts([
    	'basepath' => '/widget-test', // Optional, ignore if needed
    ], $atts);

    ob_start();
    ?>
    <payment-widget
    	basepath="<?php echo esc_attr($atts['basepath']); ?>"> // Optional param, ignore if needed
    </payment-widget>
    <?php
    return ob_get_clean();
}
add_shortcode('purchase_widget', 'payment_widget_shortcode');
```
Usage in Posts/Pages:
```
[purchase_widget basepath="/widget-test"] // Optional param, ignore if needed

```

## Event Handling
```
document.addEventListener('DOMContentLoaded', () => {
    const widget = document.querySelector('payment-widget');

    if (widget) {
        // Payment Completed Event
        widget.addEventListener('onCompleted', (event) => {
            console.log('Payment completed:', event.detail);
            // Custom success handling
        });

        // Back Navigation Event
        widget.addEventListener('onBack', () => {
            console.log('User navigated back');
            // Custom back navigation handling
        });
    }
});
```


# Installing Purchase Widget in React Project

## Installation

1. Copy the `purchase-{brand}.tgz` file to your project directory
2. Install the package locally:
```
bash
npm install ./purchase-{brand}.tgz
```

## Usage

1. Import the widget styles in your main App file:
```
tsx
import '@fisikal-ltd/purchase-js/widget.css';
```

2. Create a component to wrap the web component:
```
tsx
import React from 'react';

interface PaymentWidgetProps {
  basepath: string;
  onCompleted?: (details: { success: boolean }) => void;
  onBack?: () => void;
}

export const PaymentWidget: React.FC<PaymentWidgetProps> = ({
  basepath,
  onCompleted,
  onBack
}) => {
  React.useEffect(() => {
    // Import widget only on client-side
    import('@fisikal-ltd/purchase-js');
  }, []);

  React.useEffect(() => {
    const widget = document.querySelector('payment-widget');
    if (widget) {
      widget.addEventListener('onCompleted', ((e: CustomEvent) => {
        onCompleted?.(e.detail);
      }) as EventListener);
      widget.addEventListener('onBack', onBack as EventListener);
    }

    return () => {
      if (widget) {
        widget.removeEventListener('onCompleted', onCompleted as EventListener);
        widget.removeEventListener('onBack', onBack as EventListener);
      }
    };
  }, [onCompleted, onBack]);

  return (
    <payment-widget
      basepath={basepath}
    />
  );
};

// Usage example:
function App() {
  return (
    <PaymentWidget
      basepath="/widget"
      onCompleted={({ success }) => console.log('Payment completed:', success)}
      onBack={() => console.log('Back clicked')}
    />
  );
}
````
