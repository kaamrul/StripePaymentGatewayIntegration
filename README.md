## Card Stripe Payment Gateway Integration In Laravel 8 With example

### Steps 

- Install Laravel 8 or 9 App
- Install Stripe Package
- Make Route
- Create Controller
- Create a Blade View file
- Run Development Server

### Step 1: Install Laravel 8 App

```bash
- laravel new stripe_card
- use this command to create a new Project in laravel for stripe payment gateway add using card.
```

### Step 2: Install Stripe Package

```bash
- composer require stripe/stripe-php
- This command is required because, i want to use stripe in our project.
```

### Step 3: Make Route

```php
<?php

use Illuminate\Support\Facades\Route;
use App\Http\Controllers\UserController;

/*
|--------------------------------------------------------------------------
| Web Routes
|--------------------------------------------------------------------------
|
| Here is where you can register web routes for your application. These
| routes are loaded by the RouteServiceProvider within a group which
| contains the "web" middleware group. Now create something great!
|
*/

use Illuminate\Http\Request;

Route::get ( '/', function () {
    return view ( 'cardForm' );
} );
Route::post ( '/', [UserController::class,'call'] );

Please used above code in your web.php file.
```

### Step 4: Create Controller

```bash
- Now, create a UserController in our project, please follow below command.
- php artisan make:controller UserController
- Then go app/Http/Controllers/ directory and open UserController. Then add the following code into  UserController .php.
```

```php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;

use Stripe;
use Session;

class UserController extends Controller
{
    //
    public function call(Request $request) {
        \Stripe\Stripe::setApiKey('SET_YOUR_SECRET_KEY_HERE');
        $customer = \Stripe\Customer::create(array(
          'name' => 'test',
          'description' => 'test description',
          'email' => 'email@gmail.com',
          'source' => $request->input('stripeToken'),
           "address" => ["city" => "San Francisco", "country" => "US", "line1" => "510 Townsend St", "postal_code" => "98140", "state" => "CA"]

      ));
        try {
            \Stripe\Charge::create ( array (
                    "amount" => 300 * 100,
                    "currency" => "usd",
                    "customer" =>  $customer["id"],
                    "description" => "Test payment."
            ) );
            Session::flash ( 'success-message', 'Payment done successfully !' );
            return view ( 'cardForm' );
        } catch ( \Stripe\Error\Card $e ) {
            Session::flash ( 'fail-message', $e->get_message() );
            return view ( 'cardForm' );
        }
    }
}
```

### Step 5: Create Blade View File

- In this step, Visit app/resources/views/ and create one blade view file name cardForm.blade.php. Then add the following code into the  cardForm.blade.php file:

```php
<html lang="en">
<head>
<meta charset="UTF-8">
<link rel="stylesheet"
  href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.6/css/bootstrap.min.css"
  integrity="sha384-1q8mTJOASx8j1Au+a5WDVnPi2lkFfwwEAa8hDDdjZlpLegxhjVME1fgjWPGmkzs7"
  crossorigin="anonymous">
<link rel="stylesheet"
  href="https://maxcdn.bootstrapcdn.com/font-awesome/4.6.1/css/font-awesome.min.css">
</head>

<body>
  <div class="container">
    <div class='row'>
      <div class='col-md-4'></div>
      <div class='col-md-4'>
        <script src='https://js.stripe.com/v2/' type='text/javascript'></script>
        <form accept-charset="UTF-8" action="/" class="require-validation"
          data-cc-on-file="false"
          data-stripe-publishable-key="SET_YOUR_PUBLISHED_KEY_HERE"
          id="payment-form" method="post">
          @csrf
          <div class='form-row'>
            <div class='col-xs-12 form-group required'>
              <label class='control-label'>Card Holder Name</label> <input
                class='form-control' size='4' type='text' placeholder="Enter Card Holder Name">
            </div>
          </div>
          <div class='form-row'>
            <div class='col-xs-12 form-group card required'>
              <label class='control-label'>Card Number</label> <input
                autocomplete='off' class='form-control card-number' size='20'
                type='text' placeholder="Enter Card number">
            </div>
          </div>
          <div class='form-row'>
            <div class='col-xs-4 form-group cvc required'>
              <label class='control-label'>CVC</label> <input
                autocomplete='off' class='form-control card-cvc'
                placeholder='CVV' size='4' type='text'>
            </div>
            <div class='col-xs-4 form-group expiration required'>
              <label class='control-label'>Expiration</label> <input
                class='form-control card-expiry-month' placeholder='MM' size='2'
                type='text'>
            </div>
            <div class='col-xs-4 form-group expiration required'>
              <label class='control-label'>YEAR</label> <input
                class='form-control card-expiry-year' placeholder='YYYY'
                size='4' type='text'>
            </div>
          </div>
          <!-- <div class='form-row'>
            <div class='col-md-12'>
              <div class='form-control total btn btn-info'>
                Total: <span class='amount'>$300</span>
              </div>
            </div>
          </div> -->
          <div class='form-row'>
            <div class='col-md-12 form-group'>
              <button class='form-control btn btn-primary submit-button'
                type='submit' style="margin-top: 10px;">Confirm</button>
            </div>
          </div>
          <div class='form-row'>
            <div class='col-md-12 error form-group hide'>
              <div class='alert-danger alert'>Please correct the errors and try
                again.</div>
            </div>
          </div>
        </form>
        @if ((Session::has('success-message')))
        <div class="alert alert-success col-md-12">{{
          Session::get('success-message') }}</div>
        @endif @if ((Session::has('fail-message')))
        <div class="alert alert-danger col-md-12">{{
          Session::get('fail-message') }}</div>
        @endif
      </div>
      <div class='col-md-4'></div>
    </div>
  </div>
  <script src="https://code.jquery.com/jquery-1.12.3.min.js"
    integrity="sha256-aaODHAgvwQW1bFOGXMeX+pC4PZIPsvn2h1sArYOhgXQ="
    crossorigin="anonymous"></script>
  <script
    src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.6/js/bootstrap.min.js"
    integrity="sha384-0mSbJDEHialfmuBBQP6A4Qrprq5OVfW37PRR3j5ELqxss1yVqOtnepnHVP9aJ7xS"
    crossorigin="anonymous"></script>
  <script>
    $(function() {
        $('form.require-validation').bind('submit', function(e) {
          var $form         = $(e.target).closest('form'),
              inputSelector = ['input[type=email]', 'input[type=password]',
                               'input[type=text]', 'input[type=file]',
                               'textarea'].join(', '),
              $inputs       = $form.find('.required').find(inputSelector),
              $errorMessage = $form.find('div.error'),
              valid         = true;

          $errorMessage.addClass('hide');
          $('.has-error').removeClass('has-error');
          $inputs.each(function(i, el) {
            var $input = $(el);
            if ($input.val() === '') {
              $input.parent().addClass('has-error');
              $errorMessage.removeClass('hide');
              e.preventDefault(); // cancel on first error
            }
          });
        });
      });

      $(function() {
        var $form = $("#payment-form");

        $form.on('submit', function(e) {
          if (!$form.data('cc-on-file')) {
            e.preventDefault();
            Stripe.setPublishableKey($form.data('stripe-publishable-key'));
            Stripe.createToken({
              number: $('.card-number').val(),
              cvc: $('.card-cvc').val(),
              exp_month: $('.card-expiry-month').val(),
              exp_year: $('.card-expiry-year').val()
            }, stripeResponseHandler);
          }
        });

        function stripeResponseHandler(status, response) {
          if (response.error) {
            $('.error')
              .removeClass('hide')
              .find('.alert')
              .text(response.error.message);
          } else {
            // token contains id, last4, and card type
            var token = response['id'];
            // insert the token into the form so it gets submitted to the server
            $form.find('input[type=text]').empty();
            $form.append("<input type='hidden' name='stripeToken' value='" + token + "'/>");
            console.log(token);
            $form.get(0).submit();
          }
        }
      })
    </script>
</body>
</html>
```

### Step 6: Run Development Server

- Now Run Development Server using this command
```bash
php artisan serve
```
- Now, You can use this below test card to check this Application.

```bash
Card: 4242424242424242
CVV: – 123
MM: – you can use greater than current month.
YYYY:- you can use greater than current Year.
```




![inbox-zero](https://user-images.githubusercontent.com/37633219/182585656-c1fe4a8d-e950-4c4a-9c31-5011b5a875be.svg)

```bash
### Take a break, write some code, do what you do best.
```
