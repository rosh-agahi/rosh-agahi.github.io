---
layout: post
title:      "Project 3: Ruby on Rails - Walk-N-Roll"
date:       2021-07-05 18:51:41 +0000
permalink:  project_3_ruby_on_rails_-_walk-n-roll
---

### A Dog Walking Business Organizer

Ruby on Rails... a buzz phrase I have heard for years and finally understand!! Or at least, am beginning to.

Similar to Sinatra, Ruby on Rails is another framework used to build web applications that are connected to a database. Ruby on Rails also adheres to the MVC structure. What made RoR easier for me to use than Sinatra was the **implicit routing.** As long as a method was defined in a controller, the system had the ability to recognize the route. For instance, instead of writing out all of the routes in the controller like the:

```
get '/clients' do 
  @clients = Client.all 
  erb :'clients/index.html.erb'
end 
```

in RoR, as long as the RESTful route is defined in the appropriate controller as follows, the framework knows how to implement it and this makes for more simplified code:

*businessowners_controller.rb*
```
def index 
  @clients = Client.all 
end 
```

#### ActiveRecord Scope Methods 

Another tool that we learned to use in this section was ActiveRecord Scope Methods. The **scope** is a way to filter the instances of a model within the database table. 

In my project, I have a model for services that the business offers. A service has a description, a price and a boolean status (true = active or false = archived). I used the following scope method to identify all active services: 

```
scope :active, -> {where("status = ?", true)}
```

One way to deal with services was to allow the user (business_owner) to delete services that are no longer available. The reason I chose not to set it up this way and use a scope method instead was because one of the relationships in my app was that appointments belong to a service. If a services is deleted, the appointment would not be able to look up that service_id and it would cause an error in the app. Instead, using scope methods, I'm allowing the user to archive services that are not currently available. In the new appointment form, the dropdown to select a service only shows active services by calling the "active" scope method on the current_business_owner's services.all : 

*appointments_controller.rb*
```
 def new
    @cbo = helpers.current_business_owner
    @services = @cbo.services.all.active
    @dogwalkers = @cbo.dogwalkers.all

    if params[:dog_id]
      @dog = Dog.find_by_id(params[:dog_id])
      @appointment = @dog.appointments.build
    else
      # @clients = @cbo.clients.all
      @dogs = Dog.all.select { |d| d.client.business_owner_id == @cbo.id}
      @appointment = Appointment.new
    end
  end
	```

When planning an app, if you intend to allow your user to remove instances from being accessed, consider using scope methods to archive the instance instead of deleting it entirely if that instance has anything that belongs to it. That way you can keep your app from erroring because it can't join to the "has_many" side of the table.

