Togglable Background Processing via Flags

<!--more-->


One of the great perks of using Amazon Web Services is the ability to spin up instances when you need them and not wasting any money. Heroku is hosted on Amazon and provides the same benefit of scaling up or down based off business needs to it's customers. Heroku offers this scaling service via to 2 toggles; **web** and **worker** **dynos**. Web Dynos offer load balancing web requests across multiple app instances. Where Worker Dynos offer background processing via **Delayed Jobs**, **Resque**, or **Celery**, etc. Dynos are equally costly, around $35 per instance.

I help run a small web site that uploads pictures taken at events (ex. concerts, parties, ad-hoc); then distributes the pictures in realtime. To do this we upload the pictures using the the AWS S3 backed gem called Paperclip; an all in one server side photo resizer and uploaded. 


```
STYLES={
    thumb: '100x100#', 
    square: '200x200#', 
    medium: '500x500>',
    large: '800x800>',
    xlarge: { 
      :geometry => '1600x1400>', 
      :watermark_path => "#{Rails.root}/public/watermarks/logo.png"}
  }
  
  if ENV['BACKGROUND_PROCESSING']=='true'
    has_attached_file :image, 
    :processors => [:watermark],
    :styles => STYLES, 
    :only_process => []
        
    # Process these styles in the background
    self.process_in_background :image, :only_process => [:thumb, :medium, :large]
  else
    has_attached_file :image, 
    :processors => [:watermark],
    :styles => STYLES
  end
```

As you can see, we wrap the has_attached_file definition with a flag. If we want to process the encoding and uploading of Paperclip photos int the background, all we have to do is set the BACKGROUND_PROCESSING environment variable to be true.