There are 2 options how to create a controller:

1. Using [depedency injection](#depedency-injection) (the easiest way)
2. [Custom initialization](#custom-initialization) - passing `$request` with file index.

In the example we are using method `saveFile` which moves chunk file to final destination. More about saving [here](#saving).

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

You are using `FileReceiver` class that accepts:

1. the `UploadedFile` object or file index (used for retreiving file from `$request`)
2. current `$request` 
3. class with front end implementation 
	* Automatic detection: `HandlerFactory::classFromRequest($request)`.
	* Forced implementation (exaple): `ContentRangeUploadHandler::class`

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

In the example we are moving final file (all chunks merged into single file) to desired destination (directory built from the files mime type). Thanks to move function the chunk is removed. 

You are free to change this implementation. If you are not planing to use a move function, then you are responsible for deleting the chunk file.

```php
/**
 * Saves the file
 *
 * @param UploadedFile $file
 *
 * @return \Illuminate\Http\JsonResponse
 */
protected function saveFile(UploadedFile $file)
{
    $fileName = $this->createFilename($file);
    // Group files by mime type
    $mime = str_replace('/', '-', $file->getMimeType());
    // Group files by the date (week
    $dateFolder = date("Y-m-W");

    // Build the file path
    $filePath = "upload/{$mime}/{$dateFolder}/";
    $finalPath = storage_path("app/".$filePath);

    // move the file name
    $file->move($finalPath, $fileName);

    return response()->json([
        'path' => $filePath,
        'name' => $fileName,
        'mime_type' => $mime
    ]);
}

/**
 * Create unique filename for uploaded file
 * @param UploadedFile $file
 * @return string
 */
protected function createFilename(UploadedFile $file)
{
    $extension = $file->getClientOriginalExtension();
    $filename = str_replace(".".$extension, "", $file->getClientOriginalName()); // Filename without extension

    // Add timestamp hash to name of the file
    $filename .= "_" . md5(time()) . "." . $extension;

    return $filename;
}
```

Full example found in `UploadController.php`.

### Deleting chunk
Required only if not using `move` function on the chunk file.

### Saving to cloud
You need to use cloud? Read more [here](https://github.com/pionl/laravel-chunk-upload/wiki/Saving-to-Cloud).

Now you need to [choose a route](https://github.com/pionl/laravel-chunk-upload/wiki/routing).


