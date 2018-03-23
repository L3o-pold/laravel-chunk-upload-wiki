## Unique naming
By default we use session to generate unique name for the chunk file (support same file upload at same time). For this to work, the front end library must send a cookie.

You can update the `chunk.name.use` settings for custom usage.


    "use" => [
        "session" => false, // should the chunk name use the session id? The uploader muset send cookie!,
        "browser" => true // instead of session we can use the ip and browser?
    ]