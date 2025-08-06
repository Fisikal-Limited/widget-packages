# Installing Purchase Widget in React Project

## Installation

1. Copy the `purchase-{brand}.tgz` file to your project directory
2. Install the package locally:
```bash
npm install ./purchase-{brand}.tgz
```

## Usage

1. Import the widget styles in your main App file:
```tsx
import '@fisikal-ltd/purchase-js/widget.css';
```

2. Create a component to wrap the web component:
```tsx
import React from 'react';

interface PaymentWidgetProps {
  basepath: string;
  publicFisikalKey: string;  // Required - Fisikal public key
  publicStripeKey: string;   // Required - Stripe public key
  locationId: number;        // Required - Location identifier
  onCompleted?: (details: { success: boolean }) => void;
  onBack?: () => void;
}

export const PaymentWidget: React.FC<PaymentWidgetProps> = ({
  basepath,
  publicFisikalKey,
  publicStripeKey,
  locationId,
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
      publicFisikalKey={publicFisikalKey}
      publicStripeKey={publicStripeKey}
      locationId={locationId}
    />
  );
};

// Usage example:
function App() {
  return (
    <PaymentWidget
      basepath="/widget-packages"
      publicFisikalKey="fisikal_public_key_xxx"
      publicStripeKey="pk_test_xxx"
      locationId={1110}
      onCompleted={({ success }) => console.log('Payment completed:', success)}
      onBack={() => console.log('Back clicked')}
    />
  );
}
````
