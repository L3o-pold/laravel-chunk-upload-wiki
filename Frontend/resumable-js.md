| Package | View | Javascript | handler class|
| ---- | ------- | --- | --- |
| [Github](https://github.com/23/resumable.js) | [Source](https://github.com/pionl/laravel-chunk-upload-example/blob/master/resources/views/example/resumable.js.blade.php) | [Source](https://github.com/pionl/laravel-chunk-upload-example/blob/master/resources/assets/js/resumable.js) | ResumableJSUploadHandler |

1. Read the official docs
2. Create the view as package requires 
3. Implement the basic resumable.js code 


```javascript
var resumable = new Resumable({
    // Use chunk size that is smaller than your maximum limit due a resumable issue
    // https://github.com/23/resumable.js/issues/51
    chunkSize: 1 * 1024 * 1024, // 1MB
    simultaneousUploads: 3,
    testChunks: false,
    throttleProgressCallbacks: 1,
    // Get the url from data-url tag
    target: $fileUpload.data('url'),
    // Append token to the request - required for web routes
    query:{_token : $('input[name=_token]').val()}
});
```