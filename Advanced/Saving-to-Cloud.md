There are 2 options how to create a controller:

1. Using [depedency injection](#depedency-injection) (the easiest way)
2. [Custom initialization](#custom-initialization) - passing `$request` with file index.

In the example we are using method `saveFile` which saves the file in final destination. More about saving [here](#saving).

## Depedency injection

Depedency injection will automatically use first file in the request and will detect which front end implementation is used to handle the file upload.

```php
/**
 * Handles the file upload
 *
 * @param FileReceiver $receiver
 *
 * @return \Illuminate\Http\JsonResponse
 *
 * @throws UploadMissingFileException
 *
 */
public function uploadFile(FileReceiver $receiver)
{
    // check if the upload is success, throw exception or return response you need
    if ($receiver->isUploaded() === false) {
        throw new UploadMissingFileException();
    }
    // receive the file
    $save = $receiver->receive();

    // check if the upload has finished (in chunk mode it will send smaller files)
    if ($save->isFinished()) {
        // save the file and return any response you need
        return $this->saveFile($save->getFile());
    }

    // we are in chunk mode, lets send the current progress
    /** @var AbstractHandler $handler */
    $handler = $save->handler();
    return response()->json([
        "done" => $handler->getPercentageDone()
    ]);
}
```

[Example controller found here](https://github.com/pionl/laravel-chunk-upload-example/blob/master/app/Http/Controllers/DependencyUploadController.php)

## Custom initialization

```php
/**
     * Handles the file upload
     *
     * @param Request $request
     *
     * @return \Illuminate\Http\JsonResponse
     *
     * @throws UploadMissingFileException
     * @throws \Pion\Laravel\ChunkUpload\Exceptions\UploadFailedException
 */
public function upload(Request $request) {
    // create the file receiver
    $receiver = new FileReceiver("file", $request, HandlerFactory::classFromRequest($request));

    // check if the upload is success, throw exception or return response you need
    if ($receiver->isUploaded() === false) {
        throw new UploadMissingFileException();
    }

    // receive the file
    $save = $receiver->receive();

    // check if the upload has finished (in chunk mode it will send smaller files)
    if ($save->isFinished()) {
        // save the file and return any response you need, current example uses `move` function. If you are
        // not using move, you need to manually delete the file by unlink($save->getFile()->getPathname())
        return $this->saveFile($save->getFile());
    }

    // we are in chunk mode, lets send the current progress
    /** @var AbstractHandler $handler */
    $handler = $save->handler();

    return response()->json([
        "done" => $handler->getPercentageDone(),
    ]);
}
```

[Example controller found here](https://github.com/pionl/laravel-chunk-upload-example/blob/master/app/Http/Controllers/UploadController.php)

## Saving

You need saving the


