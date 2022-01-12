---
layout: post
title:      "Rails Project – Salon Application"
date:       2022-01-12 05:08:01 +0000
permalink:  rails_project_salon_application
---


For my Rails Project, I created application that imitates how a hair salon would manage client bookings. My project, **Salon Application**, provides a skeletal framework for a salon to manage client appointments for stylists. 

This blog will briefly explain the application schema,[ found here on GitHub](http://github.com/ssdiaz/salon_application)! 

### Overview

Anyone can view the list of Stylists and Services provided, as well as the static pages - 'Home', 'About', and 'Contact Us'.  I imagine a receptionist or stylist would have an account to do more dynamic actions. 

If a user is logged in, they can book appointments for clients/stylists, create a new stylist, create a new service, and manage the client list. A user can also search by name to find a specific client, where they can book an appointment for the client if desired. Each appointment must have a client, a stylist, and at least one service. An appointment can only be booked with a stylist if they have the selected services associated to them. 


### User Log In - Now with Google API

A user can choose to log in by creating an account, or they can log in with their Google account thanks to the [OAuth](http://github.com/oauth-xx/oauth-ruby) gem. A salon could create a Google account as an organization using the Google Developer tools and give their employees/stylists an authorized email to sign in and create an account.

Things to Note when setting up OAuth - make sure the callback URL you use in the Third Party's Developer tools uses regular ‘http’ and not ‘https’ – something easily overlooked if you copy and paste from a secure browser. In summary, I added a column in my Users table, 'uid', to store the username, created a new controller action and private method, and created the route to login via Google.


### Model Relationships

Models:
* **User**
* **Stylist**
* **Client**
* **Appointment**
* **Service**

Relationships, written in Rails Language:
* **Stylist** has_many **appointments** 
* **Stylist** has_many **clients**, through **appointments**
* **Stylist** has_many **services**, through a join table
* **Client** has_many **appointments**
* **Client** has_many **stylists**, through **appointments**
* **Service** has_many **appointments**, through a join table
* **Service** has_many **stylists**, through a join table
* **Appointment** has_many **Services**, through a join table
* **Appointment** belongs_to both **Stylist** and **Client**

Join Tables for the many_to_many relationships, written in Rails Language:
* **Appointments** - belongs_to **client** & **stylist**, with foreign keys: client_id & stylid_id 
* **AppointmentServices** - belongs_to **appointment** & **service**, with foreign keys: appointment_id & service_id
* **StylistServices** - belongs_to **stylist** & **service**, with foreign keys: stylist_id & service_id


### Validations

Each model has many validations - some unique, and some not. As an example, models for Client, User, Stylist, and Service contain `before_save :titleize_name` to capitalize the `:name` attribute before saving to the database. Since the below method applies to multiple models, and it was placed inside the **ApplicationRecord** model for all models to inherit from. 


```    
  def titleize_name
    self.name = name.titleize  
  end
```


### Controller

The controllers contain the bulk of the code (as it should!).  They contain familiar RESTful actions like new, create, index, show, edit, update, and destroy. 

The main difference with rails with that you need strong params, as shown as an example here from the Service Controller:
```
# app > controllers > services_controller.rb

    def service_params
        params.require(:service).permit(:name, :minutes, :price, stylist_ids:[])
    end
```


### Views

Each model has their own views folder with templates for new, index, show, and edit. I used the [simple_calendar](http://github.com/excid3/simple_calendar) gem to display all appointments on - you guessed it - a simple calendar.


### Stylesheet

 The stylesheet for CSS is found in `app > assets > stylesheets` folder. Any CSS placed in the `application.css` file will be applied to the whole application. Rails allows you to create stylesheets based on your model's controller actions which will be included  in the `application.css` file. For example, I used CSS in a file for my Appointment Controller actions, in file: `appointment.css`.
 
 
###  Conclusion

There's always more that could be said - and done! In the future, I'd like to expand this project to determine a client's services, and rank the stylists based on most bookings or highest appointment value per month. Alternatively, there is so much that can be explored with Rails, and refactoring my code to keep it DRY and concise will always be something to practice. 

 
Thanks for reading & happy coding!


