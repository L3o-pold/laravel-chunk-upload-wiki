| Package | handler class|
| ---- | --- |
| [Github](https://github.com/danialfarid/ng-file-upload) | NgFileUploadHandler |

1. Read the official docs
2. Create the view as package requires 
3. Implement the basic ng-file-upload code (you need to set the resumeChunkSize)


```javascript
$scope.upload = function (file) {
    file = Upload.upload({
      url: 'http://yourapi.com/upload',
      data: { file: file, your_additionnal_data: 'foo' },
      resumeChunkSize: '5MB'
    });
    
    file.upload.then(function (response) {
      /**
      * User response after download as you need 
      */
    }, function (response) {
      if (response.status > 0) {
        /**
        * Handle error as you need
        */
      }
    }, function (evt) {
      // Math.min is to fix IE which reports 200% sometimes
      file.progress = Math.min(100, parseInt(100.0 * evt.loaded / evt.total));
    });
};
```