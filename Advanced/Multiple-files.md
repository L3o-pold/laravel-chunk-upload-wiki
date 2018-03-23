We can handle multiple files by looping existing files in the request.

```php
/**
 * Handles the file upload
 *
 * @param Request $request
 *
 * @param Int $fileIndex
 *
 * @return \Illuminate\Http\JsonResponse
 * 
 * @throws UploadMissingFileException
 */
public function upload(Request $request) {
    // Response for the files - completed and uncompleted
    $files = [];

    // Get array of files from request
    $files = $request->file('files');
    
    if (!is_array($files)) {
        throw new UploadMissingFileException();
    }

    
    // Loop sent files
    foreach ($files as $file) {
        // Instead of passing the index name, pass the UploadFile object from the $files array we are looping
        // Exception is thrown if file upload is invalid (size limit, etc)
        
        // Create the file receiver via dynamic handler
        $receiver = new FileReceiver($file, $request, HandlerFactory::classFromRequest($request));
        // or via static handler usage
        $receiver = new FileReceiver($file, $request, ContentRangeUploadHandler::class);
        
        if ($receiver->isUploaded()) {
	        continue;
        }
        // receive the file
        $save = $receiver->receive();
    
        // check if the upload has finished (in chunk mode it will send smaller files)
        if ($save->isFinished()) {
            // save the file and return any response you need
            $files[] = $this->saveFile($save->getFile());
        } else {
            // we are in chunk mode, lets send the current progress
    
            /** @var ContentRangeUploadHandler $handler */
            $handler = $save->handler();
            
            // Add the completed file
            $files[] = [
                "progress" => $handler->getPercentageDone(),
                "finished" => false
            ];
        }
    }
    
    return response()->json($files);
}
```