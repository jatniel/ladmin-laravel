## Ladmin (Laravel Admin)

This package will handle the admin page for your laravel project without jetstream.

![Example Image](https://github.com/hexters/ladmin/blob/master/ss.png?raw=true)

## Installation

Follow installation step below

Laravel vserion
|Version|Laravel|
|:-:|:-:|
| v1.0.x| 7.x |
| v1.1.x| 8.x |

You can install this package via composer:
```
$ composer require hexters/ladmin
```

Generate login / registration scaffolding
```
$ php artisan ui bootstrap --auth
```

Add this trait to your user model
```
. . .
use Hexters\Ladmin\LadminTrait;

class User extends Authenticatable {

  use Notifiable, LadminTrait;

  . . .
```

Publish asset and system package
```
$ php artisan vendor:publish --tag=assets --force
$ php artisan vendor:publish --tag=core

```

Install database
```
$ php artisan migrate
```

Attach role to user admin with database seed or other
```
. . .
use App\Models\User;
use App\Models\Role;
. . .

\App\Models\User::factory(10)
  ->has(Role::factory()->count(1))
  ->create();

```


Add Ladmin route to your route project `routes/web.php`
```
. . .

use Hexters\Ladmin\Routes\Ladmin;
use App\Http\Controllers\HomeController;

. . .

Route::get('/home', [HomeController::class, 'index'])->name('home');

. . .

Ladmin::route(function() {

    // Your module route here
    Route::resource('withdrawal', WithdrawalController::class);

});

. . .

```

Create Datatables server render with command
```
$ php artisan make:datatables UserDataTables  --model=User
```

## Blade
Open folder `resources/views/vendor/ladmin`

extended for Blade layout
```

@extends('ladmin::layouts.app')
@section('title', 'Module Title')
@section('content')
    
    <!-- Component here -->

@endsection

```

Access admin page
```
http://domain.com/administrator
```


## Blade Component

Card Component
```
<x-ladmin-card class="mb-3">
  <x-slot name="header"> <!-- not required -->
    Card Header
  </x-slot>

    Card Content

  <x-slot name="footer"> <!-- not required -->
    Card Footer
  </x-slot>
</x-ladmin-card>
```
|Attribute|value|require|
|-|-|-|
|`class`|String|NO|

Input Component
```
<x-ladmin-input name="money" type="number" placeholder="Input your income">
  <x-slot name="prepend"> <!-- not required -->
    <i class="fas fa-wallet"></i>
  </x-slot>

  <x-slot name="append"> <!-- not required -->
    USD
  </x-slot>
</x-ladmin-input>

// OR

<x-ladmin-input name="email" type="email" label="E-Mail Address" />

```

|Attribute|value|require|
|-|-|-|
|`name`|String|YES|
|`type`|String|YES|
|`label`|String|NO|
|`placeholder`|String|NO|
|`old`|Boolean default `false`|NO|
|`value`|String|NO|
|`required`|Boolean default `false`|NO|

## Menus

Open file `app/menus/sidenar.php` and `top_right.php` for manage admin menu

## Custom Style
Add this code to your file `webpack.mix.js` and check file `resource/js/ladmin` and `resource/sass/ladmin`

```
/*
| Package required
|
| npm i jquery popper.js bootstrap @fortawesome/fontawesome-free datatables.net datatables.net-bs4 --save
|
*/

mix.js('resources/js/app.js', 'public/js')
    .sass('resources/sass/app.scss', 'public/css');

mix.js('resources/js/ladmin/app.js', 'public/js/ladmin/app.js')
   .sass('resources/sass/ladmin/app.scss', 'public/css/ladmin/app.css');
```

## Notificaiton

Set true for notification status in ladmin config file

```
. . .

'notification' => true

. . .
```

Send notification
```
(new User)->ladminSendNotification([
  'title' => 'New invoice',
  'link' => 'http://localhost:8000/invoice/2',
  'image_link' => null,
  'description' => 'Lorem ipsum dolor sit amet consectetur adipisicing elit.'
]);

```
Notification required
|Option|Type|required|
|-|-|:-:|
|`title`|String|YES|
|`link`|String|YES|
|`image_link`|String|NO|
|`description`|String|YES|

Listening For Notification
```
Echo.channel(`ladmin`)
    .listen('.notification', (e) => {
        console.log(e.update);
    });
```
