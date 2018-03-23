# File receiver
If upload has failed (like file size limit, exception is raised)

## Methods

### `isUploaded()`
Determines if the file object is in the request

### `receive()`
Tries to handle the upload request. If the file is not uploaded, returns false. If the file
is present in the request, it will create the save object.

If the file in the request is chunk, it will create the `ChunkSave` object, otherwise creates the `SingleSave`
which does nothing at this moment.

# Handlers

Objects that will handle parsing request data for chunk detection for every provider.

## AbstractHandler

Use `AbstractHandler` for type hint or use a specific handler to se additional methods.

## ContentRangeUploadHandler

* supported by blueimp-file-upload
* uses the Content-range header with the bytes range

### Additional methods

* `getBytesStart()` - returns the starting bytes for current request
* `getBytesEnd()` - returns the ending bytes for current request
* `getBytesTotal()` - returns the total bytes for the file

## ChunksInRequestUploadHandler
* Supported by plupload
* uses the chunks numbers from the request

## ResumableJSUploadHandler
* Supported by resumable.js
* uses the chunks numbers from the request

## DropZoneUploadHandler
* Supported by DropZone
* uses the chunks numbers from the request

## Using own implementation

See the `Contribution` section in Readme

# Automatic handler (HandlerFactory)

You can use the automatic detection of the correct handler (provider) by using the `HandlerFactory::classFromRequest` as
a third parameter when constructing the `FileReceiver`.
 
```php
// Exception is thrown if file upload is invalid (size limit, etc)
$receiver = new FileReceiver("file", $request, HandlerFactory::classFromRequest($request));
```
## Fallback class
The default fallback class is stored in the HandlerFactory (default `SingleUploadHandler::class`). 

You can change it globally by calling 

```php
HandlerFactory::setFallbackHandler(CustomHandler::class)
```     
or pass as second parameter when using 
 
```php
HandlerFactory::classFromRequest($request, CustomHandler::class)
```