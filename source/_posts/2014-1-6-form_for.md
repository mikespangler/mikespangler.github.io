# rails magic : form_for

When refactoring forms, I often find that I have eschewed rails magic in favor of needlessly verbose statements.  In this post, I will closely examine the `form_for` helper in order to determine exactly what shortcuts it offers.

`form_for` 'binds' a form to a model object that you declare.

	<%= form_for @cheese %>

In this case we have binded our form to our Cheese model.  To create a new instance of Cheese , we have a method in our controller that probably looks something like this.

	class CheeseController < ApplicationController
	  	def new
	      @cheese = Cheese.new
	  	end
	  	def create
      	  @bowl = Bowl.create(bowl_params)
      	  @bowl.save
          Player.add_new_bowl
          redirect_to bowls_path
  		end
	 end
	  	
The form_for method creates a form builder object that is expressed with the `f` variable.  You can call a myriad number of methods on `f` to generate your HTML form.

	<%= form_for @cheese do |f| %>
	  <%= f.label_tag :stinkiness, "Stinkiness" %>
  	  <%= f.text_field :stinkiness %>
  	  <%= f.submit "Create" %>
	<% end %>
	  	
The above ERB will generate the below HTML.

	<form accept-charset="UTF-8" action="/cheeses" class="cheese" 	id="new_cheese" method="post">
  	  <label for="stinkiness">Stinkiness</label>
  	  <input id="cheese_stinkiness" name="cheese[stinkiness]" type="text" /><br>
	</form>

So what did we get from our `form_for`? 

Well, we never specified whether we wanted to create a new instance of cheese, or update an old one.  Because of `form_for`, Rails knew to call `record.new_record?`, and determined that the method called should be 'create', and not 'patch'.  We saved ourselves the trouble of writing the following line of code.

	form_for(@cheese, url: {action: "create"})
	
Had our record already existed and our goal was to update, we would have saved writing the following line of code.

	form_for(@cheese, url: cheese_path(@cheese), html: {method: "patch"})

Also, `form_for` created a new key in the params hash called `params[:cheese]`, that has all the input fields as key-value pairs.  This is particularly useful when editing multiple objects in the same form with the `fields_for` tag.

The last benefit of `form_for` is in the realm of routing namespaces. If in our routes we had code like this:

	namespace :charcuterie do
  	  resources :cheeses
	end
	
We could specify to our form the correct path and action with `form_for` simply by using the below code.

	form_for [:charcuterie, @cheese]
	
This has been a brief summary of the magic of `form_for`.  Stay tuned for future snippets on choice bits of rails magic.

	