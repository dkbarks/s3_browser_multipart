S3BrowserMultipart
=========
S3BrowserMultipart allow upload big files to amazon s3, in failing or slow internet connections. 
For this the web browser upload chunks (at least 5 mb) directly to amazon s3, and assemble them in amazon s3 using  [amazon S3 multipart upload](http://docs.aws.amazon.com/AmazonS3/latest/dev/mpuoverview.html)
If any upload fail it retry to upload automatically.

Requirements
------------

### Ruby and Rails
S3BrowserMultipart now requires Ruby version **>= 1.9.3** and Rails version **~ 3.2**, use ActiveRecord.

### HTML5 Web Browser
It requires [HTML5 file API](http://en.wikipedia.org/wiki/HTML5_File_API) for managing files and slicing them.

### Jquery
It requires jQuery version **>= 1.9.1** . 

Installing
------------

#### Add dependency.
Add to Gemfile: 

`gem 's3_browser_multipart', git: 'git@github.com:saberes-development/s3_browser_multipart.git'`

And run at console: 
`bundle install ` 

#### Migrations
The engine includes some migrations (Model S3BrowserMultipart::Upload and S3BrowserMultipart::UploadPart). Those migrations run automatically running: 
`rake db:migrate`

#### Include assets.

* In application.js add:  
 `//= require s3_browser_multipart/application`

* In application.css add (in comments):  
  `*= require s3_browser_multipart/application` 

#### Configuration
 At least configure [amazon S3 keys](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSGettingStartedGuide/AWSCredentials.html) with enough permitions for posting and reading the s3 bucket.
 Create the file /config/initalizer/s3_multipart_upload.rb` with the following content replacing the keys.

 ```ruby
  module S3BrowserMultipart
    class Engine < Rails::Engine
        config.s3_config = {
          access_key_id: "AWS_S3_ACCESS_KEY", 
          secret_access_key: "AWS_S3_SECRET_KEY", 
          bucket_name: "AWS_S3_BUCKET_NAME", 
          region: nil, 
          :server_side_encryption => nil
        } 
    end
  end
 ```
#### Routes
Add in /config/routes.rb: 
`mount S3BrowserMultipart::Engine => "/s3_browser_multipart"`

#### View
 In the view where the file upload is needed use the helper:
 `<%= file_uploader %>` for the file selector.
 `<%= upload_progress %>` for see the progress.

#### Customize fileUploadedEvent
For save the values after the process customize the event (this is an example')

 ```js
S3BrowserMultipart.prototype.fileUploaded = function(arg, file){
  var s3_key = arg.object_key;
  var upload_id = arg.upload_id;
  var filename = file.name
}
 ```
Customization
-------------

#### Upload data
Helper file_uploader get next params
  * max_file_size: 1.gigabyte, 
  * chunk_size: 5.megabytes
  * key_prefix: 'upload/'

#### Progress indicator

In the web project create the file `app/views/s3_browser_multipart/uploads/_partial.html.erb`, following the original id and classes in the html elements (it's posible to ommit some of them).

The original content in that file is: 
```html
  <div class='file_progress'>
    <progress class='progress_bar'>
    </progress>
    <span class='progress_percentaje'></span>
    <p>Bytes:
      <span class='byte_progress'></span>
    </p>
    <p>Parts: 
      <span class='part_progress'></span>/
      <span class='total_parts'></span>
    </p>
    Alerts:
    <span class='message'></span>
  </div>
```

#### CSS
You can puts your own css for styling the elements. Besides the components in partial it's posible use selector as: `.file_input_disabled` and `input.file_uploader`.

#### Alerts
Validation alerts use standard js alert. 
It's posible change it using: 
```js
  S3BrowserMultipart.prototype.alertFunction = function(){
    //message code
  }
```
The mesages can be customized: 
```js
  S3BrowserMultipart.prototype.messages={
    filesize_exceeded: "Other message"
  }
```

Contributing
------------

There are a lot of work for doing.
* Js code is not tested
* Documentate code
* Restart upload after reload page
* Improve progress information (speed, ETA, etc.)
* Improve configuration
* Validate content types
* Allow multiple upload at same time
* Other proposal features

Credits
-------

![Saberes Group](http://www.saberes.com/assets/logo-bf337d09d20cb03b027db6847d812534.png)

S3BrowserMultipart was created by [Saberes.com](http://www.saberes.com/) and other contributors

License
-------

It is free software, and may be redistributed under the terms specified in the MIT-LICENSE file, remember to acknowledge the creators.
