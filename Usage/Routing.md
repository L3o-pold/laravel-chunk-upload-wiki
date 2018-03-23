## Defining route
There are multiple options where to place your upload controller, it depends only on you. 

### Web route

All `post` requests in web routes requires a token to be sent there for dont forget to send token with upload requests (more can be found in front end implementation you have choosen).

### Api route

All requests in api route will not initialize session. Library will detect it so you dont have to worry about it. If are performance geek you can force to use browser for file naming. More in [Config](https://github.com/pionl/laravel-chunk-upload/wiki/config)

__Example__

`web.php`

```php
Route::post('upload-advanced', 'UploadController@upload');

```

Now you need to [choose your frontend](https://github.com/pionl/laravel-chunk-upload/wiki/frontend).
