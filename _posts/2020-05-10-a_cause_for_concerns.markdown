---
layout: post
title:      "A Cause For Concerns"
date:       2020-05-10 16:23:35 +0000
permalink:  a_cause_for_concerns
---


As I was going through the rails module of the Flatiron School curriculum, building sideprojects and doing tutorials, I would always try to look through all of the files just to see what was going on inside of them. There was one file I would always come across and would wonder, "What is this for?". That file was the concerns file in the controllers directory. Despite my curiousity being piqued, I didn't get around to looking further into the matter until about the half way point of building out my rails project for Flatiron School.

The point of research and discovery into this mysterious conerns file occured when I needed to start building out some ways to control authorization within my application. The task at hand was to limit actions to users who were not logged in to the application. Since the id of the user gets set to a key on the session hash called user_id when they log in i.e. `session[:user_id] = user.id`, we can write a method to check if there is a value stored in that key and if so return a boolean instead of the actual value to aid us in controlling the flow in our application. I am also going to add in a conditional that will always redirect someone trying to do actions that require being logged in to the log in screen unless the key on the session hash returns true.
```
def logged_in?
        redirect_to new_session_path, alert: "You must be logged in to do that" unless !!session[:user_id]
end
```

This method can be written inside a controller where you want to limit what actions a user can take if they are not logged in to the application. A simple example of this being applied is this example of a lists controller where we wouldn't want users to create, edit or delete lists without being logged in to the application.
```
class ListsController < ApplicationController

    before_action :logged_in?

    def index 
        @lists = List.order("name")
    end

   # ...
		
		    private 
				
		def logged_in?
        redirect_to new_session_path, alert: "You must be logged in to do that" unless !!session[:user_id]
    end
		
	end
```

So we have added the method as a private method within the lists controller and are calling it as a before action on every action in this controller. Nothing can be done pertaining to a list without being logged in as a user. 

Now lets think about where else we may need to use this method. In my application a list has tasks on it and as a result we have a tasks controller. Here, I also wanted to limit the actions users could take without being logged in. So now the question becomes how to handle this issue. We need a method available in the tasks controller exactly as we have in the lists controller, but we don't want to write repetitious code, we want to be DRY. Since all of our controllers inherit from application controller we can move the method from out of our lists controller and into our application controller.
```
class ApplicationController < ActionController::Base

    private

    def logged_in?
        redirect_to new_session_path, alert: "You must be logged in to do that" unless !!session[:user_id]
    end
		
end
```

This now allows the method to be available across all of our controllers as long as they inherit from application controller. This works just fine but this can get out of hand and cause our application controller to bulk up quickly. Furthermore, what if we want a method or a few methods that could only be used in a few controllers instead of all of them. Well, we can use that mysterious concerns file previously mentioned. Lets make a file inside of the `app/controllers/concerns` directory called `logged_in_check.rb`. In this file let's add the method from our application controller inside of a module similarly named `LoggedInCheck`. The entire file will look as follows:
```
module LoggedInCheck

    private

    def logged_in?
        redirect_to new_session_path, alert: "You must be logged in to do that" unless !!session[:user_id]
    end

end
```
Now let's go back to our application controller and add this module like so:
```
class ApplicationController < ActionController::Base
    include LoggedInCheck
end
```

This is much cleaner and still provides us with information as to what this module is and can be easily found if the method ever needs to be modified. In addition, if we do ever have to make the edit, we only have to do so in one place in our code.

Because we have now included this module in application controller and all other controllers inherit from application controller, we have the method available to the other controllers. If you needed other functionality for only say two of your controllers, you could make a new file in the `app/controllers/concerns` directory and simply just include the module in only the two controllers that require the functionality. 

I hope this helped you to gain some insight into just the beginning of what can be done when you harness the usage of the concerns file within controllers. There is also a concerns file under the models directory but I will leave that to you to explore at your leisure.
