# Laravel Sanctum 
What is Laravel Sanctum ?
Laravel Sanctum provides a featherweight authentication system for SPAs (single page applications), mobile applications, and simple, token based APIs. Sanctum allows each user of your application to generate multiple API tokens for their account. These tokens may be granted abilities / scopes which specify which actions the tokens are allowed to perform..

### You have to just follow a few steps to get following web services
##### Login API
##### Details API




## Getting Started
###  setup database in .env file

```
DB_DATABASE=youtube
DB_USERNAME=root
DB_PASSWORD= redhat@123
```

## Install Laravel Sanctum.

```
composer require laravel/sanctum
```

## Publish the Sanctum configuration and migration files .

```
php artisan vendor:publish --provider="Laravel\Sanctum\SanctumServiceProvider"
```

## Run your database migrations.

```
php artisan migrate
```

## Add the Sanctum's middleware.

```javascript
../app/Http/Kernel.php

use Laravel\Sanctum\Http\Middleware\EnsureFrontendRequestsAreStateful;

...

    protected $middlewareGroups = [
        ...

        'api' => [
            EnsureFrontendRequestsAreStateful::class, // add this line
            'throttle:60,1',
            \Illuminate\Routing\Middleware\SubstituteBindings::class,
        ],
    ];

    ...
],

```

## To use tokens for users.

````
use Laravel\Sanctum\HasApiTokens;

class User extends Authenticatable
{
    use HasApiTokens, Notifiable;
}

````

## Let's create the seeder for the User model

```javascript 
php artisan make:seeder UsersTableSeeder
```

## Now let's insert as record

```javascript 
use Illuminate\Support\Facades\DB;
use Illuminate\Support\Facades\Hash;
...
...
DB::table('users')->insert([
    'name' => 'John Doe',
    'email' => 'john@doe.com',
    'password' => Hash::make('password')
]);
```

## To seed users table with user

```javascript 
php artisan db:seed --class=UsersTableSeeder
```

## login & register function in the AuthController


```javascript 
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use App\User;
use Illuminate\Support\Facades\Hash;
class UserController extends Controller
{
    function login(Request $request)
    {

        $user= User::where('email', $request->email)->first();

            if (!$user || !Hash::check($request->password, $user->password)) {
                return response([
                    'message' => ['These credentials do not match our records.']
                ], 404);
            }
        
            $token = $user->createToken('my-app-token')->plainTextToken;
        
            $response = [
                'user' => $user,
                'token' => $token
            ];
        
             return response($response, 201);

    }

    function register(Request $request)
    {

        $fields = $request->validate([
            'fname' => 'required|string',
            'lname' => 'required|string',
            'email' => 'required|string|unique:users,email',
            'phone' => 'required|string',
            'password' => 'required|string|confirmed'
        ]);

        $user = User::create([
            'fname' => $fields['fname'],
            'lname' => $fields['lname'],
            'email' => $fields['email'],
            'phone' => $fields['phone'],
            'password' => bcrypt($fields['password'])
        ]);

        $token = $user->createToken('my-app-token')->plainTextToken;

        $response = [
            'user' => $user,
            'token' => $token
        ];

        return response($response , 201);
    }

}

```


## Test with postman, Result will be below

```javascript 

{
    "user": {
        "id": 1,
        "name": "John Doe",
        "email": "john@doe.com",
        "email_verified_at": null,
    },
    "token": "AbQzDgXa..."
}

```

## Make Details API or any other with secure route  

```javascript 

Route::group(['middleware' => 'auth:sanctum'], function(){

    // Secured Routes

});


Route::post("login",[UserController::class,'login']);
Route::post("login",[UserController::class,'register']);

```
