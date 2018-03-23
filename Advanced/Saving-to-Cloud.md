Due the speed and memory requirements we are unable to save chunks on the cloud (it would be unefficient) but the final file can be moved to the cloud. Here is a quick example how to send the file to cloud (Amazons s3).

**I strongly advice** to create a queable job that would send the file to cloud. It's unefficient to send final file to cloud with users request (will take some time). 

```php
/**
 * Saves the file to S3 server
 *
 * @param UploadedFile $file
 *
 * @return \Illuminate\Http\JsonResponse
 */
protected function saveFileToS3($file)
{
    $fileName = $this->createFilename($file);

    $disk = Storage::disk('s3');
    // It's better to use streaming Streaming (laravel 5.4+)
    $disk->putFileAs('photos', $file, $fileName);

    // for older laravel
    // $disk->put($fileName, file_get_contents($file), 'public');
    $mime = str_replace('/', '-', $file->getMimeType());

    // We need to delete the file when uploaded to s3
    unlink($file->getPathname());

    return response()->json([
        'path' => $disk->url($fileName),
        'name' => $fileName,
        'mime_type' =>$mime
    ]);
}
```