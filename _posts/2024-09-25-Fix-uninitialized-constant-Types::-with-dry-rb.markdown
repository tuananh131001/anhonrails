---
layout: single
title:  "Fix uninitialized constant Types:: with dry-rb"
date:   2024-09-25 23:45:00 +0700
categories: rails dry perdas ticktick
---
My project Perdas need to get task from TickTick Rest API. After getting the response , I use a gem calls dry-struct to initializee an object with validation for the JSON response 

When I try to create a base class then extends its for my project_data class.
```ruby
Class Base
  include Dry.Types()
end
```

It raise an error `uninitialized constant Types`. This is wierd since I check the documents, it mentions the usage of Types::String, Types::Integer. After doing 
https://github.com/dry-rb/dry-types/issues/422

then I modified to
```ruby
Types = Dry.Types()

Class Base
end
```

The result is I have fixed the problem and it seems like this is the new behavior of ruby >3 and some contributor of dry-struct is working to update doc

Thanks for reading my blog. Happy Wednesday
