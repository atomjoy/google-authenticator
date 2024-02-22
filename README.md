# PHP Google Authenticator

2FA two-step verification with php and Google Authenticator in Laravel.

## Install

```sh
composer require atomjoy/google-authenticator
```

## GoogleAuthenticator in Laravel

How to generate code from user secret like in GoogleAuthenticator on Android but from php script.

```php
<?php

use Atomjoy\GoogleAuthenticator\GoogleAuthenticator;
use Illuminate\Support\Facades\Route;

// Get GoogleAuthenticator code
Route::get('/test/2fa/google/{secret}', function ($secret) {
  // Authenticator
  $ga = new GoogleAuthenticator();

  // Create secret for user (first time)
  if ($secret == 'create') {
    // Create secret enable 2fa (save secret in database)
    $secret = $ga->createSecret();

    // QR-Code image
    $url = $ga->getQRCodeUrl('Appname:user@example.com', $secret);

    // Show user qr-code
    echo "<h1>New secret is: " . $secret . "</h1>";
    echo '<h2>Google Charts URL for the QR-Code:</h2>';
    echo '<p><img src="' . $url . '"> </p>';
  } else {
    // Generate code from user secret like in GoogleAuthenticator on Android but from php script
    // Past this code in github 2fa confirm code form
    $code = $ga->getCode($secret);
    echo "<p>Checking code <b>" . $code . "</b> and Secret <b>" . $secret . "</b> use this code in 2fa on github, facebook, ...</p>";

    // Confirm code (allow 2*30sec clock tolerance)
    if ($ga->verifyCode($secret, $code, 2)) {
      echo '<h3 style="color: #5c5">OK</h3>';
    } else {
      echo '<h3 style="color: #f23">FAILED</h3>';
    }
  }
});
```

## Enable github two factor with php script

```sh
php artisan serve

# 1. Get and save generated secret when enabling two-factor authentication in panel settings
# QR-code example github_2fa_secret: FA7DKAS2MK6SH3BN

# 2. Enable or Login with 2fa secret
# Get 6-digit code and past in github 2fa form 😄 (refresh every 30 seconds)
http://127.0.0.1:8000/test/2fa/google/{github_2fa_secret}
```
