| Package | View | Javascript |
| ---- | ------- | --- |
| [Website](http://www.dropzonejs.com) | [Source](https://github.com/pionl/laravel-chunk-upload-example/blob/master/resources/views/example/dropzone.blade.php) | [Source](https://github.com/pionl/laravel-chunk-upload-example/blob/master/resources/assets/js/dropzone.js) |

1. Read the official docs
2. Create the view as package requires 
3. Implement the basic dropzone code


```javascript
// A quick way setup
var myDropzone = new Dropzone("#my-awesome-dropzone", {
    // Setup chunking
    chunking: true,
    method: "POST",
    maxFilesize: 400000000,
    chunkSize: 1000000
});

// Append token to the request - required for web routes
myDropzone.on('sending', function (file, xhr, formData) {
    formData.append("_token", token);
})
```