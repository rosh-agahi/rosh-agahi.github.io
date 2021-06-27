---
layout: post
title:      "Sinatra MVC - Expense Tracking"
date:       2021-06-27 18:39:03 +0000
permalink:  sinatra_mvc_-_expense_tracking
---


Project number 2 is about to be in the books. 

In the most recent section I learned about the MVC or Model - View - Controller structure which is employed by Sinatra, a domain-specific language building on Ruby to create web applications. 

In the first section we learned about Object Orientation which is the practice of developing code respective to the objects it represents. In the MVC structure this corresponds to the Model component. The **models** are comprised of object classes that manage the type of data associated with the models and the logic describing the functions of those classes. In a web app, models are not the only component with which the user interacts. Enter Controllers and Views. **Controllers** manage the types of requests a user can make to interact with the data such as creating, reading, editing, and destroying instances of object classes, and navigating through the interface of the web application using routes, aka requests. **Views** are built with HTML and CSS to render the content associated with a particular controller request. 

In this section we also learned about associations between objects in the forms of "has_many" and "belongs_to". When an object is created, it has a unique id automatically associated with it so that when it is stored in a database, it can be identified in a table of like object instaces. This unique id is also referred to as **primary key** . When a has_many-belongs_to relationship is established between objects, the database table for the object that is owned also contains a column for a **foreign key** that is the id number for the object instance that is doing the owning. 

To put all of this into the context of the app that I made for this project:

### Models: 

* User - has many expenses and categories
* Expense Category - belongs to a user and has many expenses
* Expense - belongs to a category and belongs to a user. 

When the user registers an account with a unique username, an instance of the User class is created and added to the database with a unique identification number *primary key*. The user then creates instances of expenses and expense categories. The category will have it's own unique id primary key, but it will also have a *foreign key* which is the id of the user that created it - this is what is used to join the two database tables, users and categories, to determine which users are the owners of which categories. The same goes for expenses, but instead of just one foreign key which is it's user_id, it also has a second foreign key which is the id of the category to which it belongs. 

#### Validation

Validation was implemented in two ways in my app to ensure that bad data was not persisted to the database: 
1. Upon instantiation of the models, such as the User class shown below, using validation tools provided by ActiveRecord

```
class User < ActiveRecord::Base
  has_secure_password

  has_many :categories
  has_many :expenses, through: :categories

  validates_uniqueness_of(:username)
  validates_presence_of :username, :name, :password
   
end
```

2. When editing the name of a category I wanted to make sure that the user didn't already have a category with that name, so I used a conditional statement in the categories controller when making a patch (edit) request for the category name: 


```
patch '/categories/:id' do
    find_category

    if existing_category_name
      flash[:notice_red] = "Category with this name already exists. Can't change this category name."
      redirect "/categories/#{@category.id}/edit"
    else

    @category.update(name: params[:name])
    redirect "/categories/#{@category.id}"
    end

  end

def find_category
  @category = Category.find_by_id(params[:id])
end

def existing_category_name
  !!current_user.categories.find_by_name(params[:name])
end
```


### Controllers 

Each of the models have a corresponding controller which handles the requests made by the user to interact with the models. There is also an additional application controller where information regarding the session, helper methods that are used commonly throughout the app like *current_user* or *logged_in?* are defined, and the initial get request to the main homepage of the app is routed. 

There are 7 types of RESTful routes. (REST stands for REpresentation State Transfer which was developed to standardize the types of requests a user can make when interacting with a database through a web app)

| Route Name | URL           | HTTP Request | Description                                                                                                   |
|------------|---------------|--------------|---------------------------------------------------------------------------------------------------------------|
| Index      | /expenses     | GET          | provides a list of all expenses created by the user                                                           |
| New        | /expenses/new | GET          | renders a form to collect inputs for a new expense                                                            |
| Create     | /expenses     | POST         | takes form data to create a new expense instance, typically redirects to another another request to view data |
| Show       | /expenses/:id | GET          | uses the :id parameter in the url to find an expense instand and render its data                              |
| Edit       | /expenses     | GET          | renders a form to collect inputs to edit an existing expense                                                  |
| Update     | /expenses/:id | PATCH        | updates a particular expense and redirects                                                                    |
| Destroy    | /expenses/:id | DELETE       | deletes a particular expense from the database and redirects                                                  |

### Views

Each of the controller actions either renders a **view** which is an html file with embedded ruby (erb) or redirects to another request which has an associated view file. To embed a Ruby function into an HTML page, we use alligator tags, so called because it looks like an alligator 
```
<%= ruby function %>
```

Here's an example of the view that is rendered by the category show request where you can see a variety of HTML tags, CSS class designations and ERB alligator tags that allowed me to write dynamic code which changes based on what is returned from the database by the controller action: 
```
<h2><%= @category.name %></h2>

<div class="container">
<a class="home_button" href="/categories/<%=@category.id%>/edit">Edit Category Name</a>
<% if @category.expenses.count == 0 %>
    <form action="/categories/<%= @category.id %>" method="post">
      <input type="hidden" name="_method" value="DELETE">
      <input class="delete_category" type="submit" value="Delete Category">
    </form>
<% end %>
</div>

<br>
<br>

<table>
  <tr>
    <th>Date</th>
    <th>Vendor</th>
    <th>Amount</th>
    <th>Description</th>
    <th></th>
    <th></th>
  </tr>

  <% @category.expenses.each do |e| %>
  <tr>
    <td><%= e.date %></td>
    <td><%= e.vendor %></td>
    <td><%= dollar_display(e.amount) %></td>
    <td><%= e.description %></td>
    <td>
      <form action="/expenses/<%= e.id %>/c" method="post">
        <input type="hidden" name="_method" value="DELETE">
        <input type="hidden" name="category_id" value="<%=@category.id%>">
        <input class="deletebutton" type="submit" value="X">
      </form>
    </td>
    <td><a class="editbutton" href='/expenses/<%= e.id %>/edit'>Edit</a></td>
</tr>
  <% end %>
</table>
```

