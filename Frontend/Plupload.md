| Package | View | Javascript | handler class|
| ---- | ------- | --- | --- |
| [Github](https://github.com/moxiecode/plupload) | no example | no example | ChunksInRequestUploadHandler  |

1. Read the official docs
2. Create the view as package requires 
3. Implement the basic plupload code (similar to dropuone or jquery upload)

**Do you have an example code? Share with us**

## React implementation

Using [react-plupload](https://github.com/lemonCMS/react-plupload)

```javascript/jsx
import Plupload from 'react-plupload'

...

<Plupload
    id="file-upload"
    runtimes="html5,flash"
    multipart
    chunk_size="2mb"
    url={process.env.APP_API + '/upload'}
    flash_swf_url="/vendor/plupload-2.1.8//Moxie.swf"
    multi_selection={false}
    buttonSelect="Choose file"
    buttonUpload="Upload"
    autoUpload
    onFileUploaded={this.uploadFinished}
    onBeforeUpload={this.uploadStart}
/>
```