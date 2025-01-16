# Examples

## Create your own checkout UI

You can create your own user interface for customers to create Partially payment plans using the Partially API and Stripe.js. Doing so involves a few steps, detailed below. We also have a [sample app](https://gitlab.com/partially-public/api-checkout-example) implemented in nodejs demonstrating a working example of a custom interface for purchasing tickets with payment plans.

1. Capture customer details. At a minimum, Partially will need a customer's email address and first and last names.

2. Use the [create payment plan endpoint](#create-a-new-payment-plan) to create a new payment plan, making sure to set the `create_stripe_payment_intent` parameter to `true`. Doing so will return a Stripe payment intent in the response in the `"payment_intent"` key of the returned json.

3. Display a form to the user with a [Stripe payment element](https://docs.stripe.com/payments/payment-element) to capture payment method details, the payment schedule contract text (available in the response from step 2 in the `"payment_schedule.contract_body"` key), a checkbox for the customer to agree with the contract terms, and a text input for the customer's signed name.

4. To initialize the Stripe payment element, you will need the public key for your Stripe account, and the payment intent secret from the intent returned in step 2 (`"payment_intent.secret_key"`). You will also need to specify a URL for Stripe to redirect users to after confirming the payment intent, which should be your confirmation page.

5. After the user submits the form, call the [add contract signature endpoint](#add-a-contract-signature) with the payment schedule id from step 2 and the customer's signature.

6. After adding the contract signature, use Stripe.js to confirm the payment intent with a `stripe.confirmPayment` function call.

7. Wait for Partially to send a `plan_opened` webhook before taking any actions on your server, such as sending confirmation notifications, etc.