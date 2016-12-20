# Devise

## Learning Objectives

- Plan a user authentication system
- Contrast the use cases for sessions and permanent storage
- Discuss the various security threats related to using sessions
- Implement User Authentication using the Devise gem


### Planning a User Authentication System

Most applications have the idea of multiple users. To make a multi-user application, we should first start with defining the term _authentication_

What is authentication? 
 - making sure the user is who they say they are.
 - common authentication techniques involve asking a user for certain information: a username and password, a phone unlock code, or something unique like a fingerprint. What about hopping on to a computer at the apple store…sometimes already logged into Facebook! How?
 
Remember sessions? They give a state to web applications. Web apps are usually stateless, because they use HTTP. Each request has no knowledge of previous requests. But with sessions, we can persist data across requests and remember things like if we are logged in or not.

How do we plan and create an authentication system?

We need 3 main parts:
1. Users, with some sort of login credentials
2. User state management, which we do through Sessions. Is the current user (request) logged in or out? What do they have access to? Who are they?
3. Views, to allow the user to log in and log out

The Users table

| Users        | 
| ------------- |
| email     |
| password     |


 - how should the password be stored? plaintext? how do we prevent a password from being visible in our application log files?

On the MVC side of things within Rails, we need the ability to create users and persist them to database, i.e. creating user records:

For users, we need:
1. User model
2. Users Controller
  - new action
  - create action
3. User views:
  - new user signup
  - email, password, password confirmation

To handle logging in, knowing when user is logged in and logged out, we can use sessions:

For sessions, we need:
1. Sessions Controller
  - new
  - create
   - find user by email
   - if user found matching email, and password matches? login
     — adds user_id to session
   - else
      redirect to login form, flash message email or login invalid
2. Session View
 - log in form

What will the page at "/sessions/new" display?
What happens when a user clicks "log out"?

What happens when a new user is created?
  — when user is saved, automatically log the user in

What links should be available throughout the app?
 — Add a link asking user to sign up or login

How can we use flash messages to make the authentication system work well for users?
 — flash messages can be used to display when you log in or out successfully

One more thing a good authentication system will give the developer: access to the currently logged in user. 

This current_user logic will be defined in the ApplicationController so it is available to all of our controllers. Remember, each controller inherits from the application controller.

What else would be nice with an authentication system?
 - Remember me: persist sessions for a certain length of time
 - Forgot username / forgot password
 - Email confirmation
 - Integration with 3rd party auth (oath2 providers like github, Facebook, twitter, etc)

### Sessions Review

Before we continue with our authentication system, let's review sessions.

What are sessions?
— user data (request data) that persists across requests
— http is a stateless protocol. Sessions make it stateful.
— where can we store session data?
  — cookies (client-side)
  — database (server-side -- why would we use the db?)
— what is the data structure session data is stored in?


- What’s the difference between sessions and cookies?
  — session is encrypted
  — both can be client side, so don’t put secrets in either

- Is a session the same thing as a user?
  -should logged in / logged out info be stored in the user table?
  -session usually have a user_id to find the session based on current user

Use cases for cookies
- time zone
- password input attempts
- temporary, readable by users, not that important

Use cases for sessions
- logged in / logged out
- shopping cart
- temporary, more difficult to read and change, a little more important

#### Security issues with Sessions

If a cookie is stored on someones' computer, can’t they alter it?

Sessions are a common target for hackers because, if precautions are not taken, can easily be intercepted and manipulated. When hackers gain control of your session, they can gain authenticated access to an appliation. This allows them to do anything you would normally be able to do, including posting embarressing status updates to your Facebook account that you left logged in at the Apple store.

Dive in: in your pods, research security issues that deal with sessions

Take 10 minutes and read chapter 2 on Sessions in the [Rails security guide](http://guides.rubyonrails.org/security.html).
pod 1 - what are sessions, and how does rails store sessions by default? 
pod 2 what is session hijacking, and why is it important to force an SSL connection? why should visitors be on a secure LAN?
pod 3 what are session guidelines presented by the security community? When would you use cookies vs sessions?
pod 4 what are replay attacks? What is session fixation? 

Present to the class what you find!

### Authentication in Rails

Wouldn’t it be nice if there was already a gem built to handle authentication, and many of these features, while handling all the potential security pitfalls? Well lucky for us there is!

Enter Devise. Devise is a ruby gem that handles authentication in Rails apps, provides some really nice features, and takes security seriously.

>Before using devise in your own apps, you should spend the time and read the entire README: https://github.com/plataformatec/devise

>When you’re ready to get started:
https://github.com/plataformatec/devise#user-content-getting-started
 - Make notes of things that you do not understand and we can talk about them as a class.
 - What questions and thoughts do you have after reading through the Devise README?

Today we are going to run through just the essential configurations to get devise up and running in our Scribble app.

For each step in our walkthrough of setting up Devise, try to ask the question: 
 - What just changed in my app? 
 - Why is it done this way?

>A mature open source library like Devise can teach us a ton about ruby, rails and software development, if we take the time to dive into the things we do not yet understand.

## Scribble and Devise Setup 

```
$ git clone https://github.com/ga-wdi-exercises/scribble.git
$ cd scribble
$ git checkout pre-devise
$ rails db:drop db:create db:migrate db:seed
$ rails s
```
> You may need to `bundle update rake` if your system version of `rake` is different from what this application requires

### Devise

Devise is a gem that simplifies implementing user authentication. - https://github.com/plataformatec/devise#starting-with-rails

While it's interesting to know what's happening under the hood when authenticating a user, it's very unlikely
you'll implement your own authentication mechanism in any future project. Devise is a well-tested open source
authentication solution for rails.

#### Devise Install Dependencies

```
# Gemfile

gem "devise"
```

```
$ bundle install
$ rails g devise:install
```

From the output of the previous command, let's make sure we do #3 only:

```
 3. Ensure you have flash messages in app/views/layouts/application.html.erb.

For example:

<p class="notice"><%= notice %></p>
<p class="alert"><%= alert %></p>
```

<details>
<summary>When working with devise, what do you think might show up inside a flash message?</summary>

<ul>
<li>Whether the user has signed in or out successfully</li>
<li>Whether the password is correct/incorrect</li>
<li>Whether the username is taken</li>
</ul>
</details>

### Commit!

Seriously, commit now. It will be easy to fix any devise issues if you have a commit you can go back to.

### Generate User model

```
$ rails g devise User
```

>If you have an existing User model, Devise should build on the functionality. But, if you're getting migration errors, it may be easier to recreate the model using Devise and add your functionality back.

### You do: Check it out!

- What did the previous command generate?
- What files did running the previous command change?
- What is the output of `rails routes`

### Run migrations

```
$ rails db:migrate
$ rails s
```

### Link to Sign Up page

```erb
<!-- app/views/layouts/application.html.erb -->
<%= link_to "Sign Up", new_user_registration_path %>
```

>`new_user_registration_path` provided by Devise - from `$ rails routes`

### Link to Sign Up only if not signed in

```erb
<!-- app/views/layouts/application.html.erb -->
<% if !current_user %>
  <%= link_to "Sign Up", new_user_registration_path %>
<% end %>
```
>current_user is a helper method provided by Devise to get the user from the session. Another helper method you can use to check if a user is signed in is the self-explanatory user_signed_in?.

### You do (5 mins):

If the user is logged in, show link to log out, including the user's email.

Otherwise, show both a link to sign up and sign in.

<details>
<summary>Solution</summary>

```erb
<!-- app/views/layouts/application.html.erb -->
<% if !current_user %>
  <%= link_to "Sign up", new_user_registration_path %>
  <%= link_to "Sign in", new_user_session_path %>
<% else %>
  <%= link_to "Sign out", destroy_user_session_path, :method => :delete %>
    <%= current_user.email %>
<% end %>
```

>The delete method needs to be specified. Get is the default for the link_to helper.
</details>

### You do: Associate Posts with a User (10 mins)

Modify your `User` and `Post` classes so that a user has many
posts and a post belongs to a user.

Create a few seeds to verify you did this part correctly.


<details>
<summary>Solution</summary>

```rb
# app/models/user

has_many :posts
```

```rb
# app/models/post

belongs_to :user
```

```
$ rails g migration add_users_to_posts user:references
$ rake db:migrate
```

</details>

## We do: Update the Controller

```rb
# app/controllers/posts_controller

def create
  @post = Post.create!(post_params.merge(user: current_user))
  redirect_to post_path(@post)
end
```

>notice: no need to update strong_params - this could be bad, actually!

[`merge`](https://ruby-doc.org/core-2.2.0/Hash.html#method-i-merge) is a built-in ruby method for combining two hashes.

## Limiting User Abilities

```rb
# app/controllers/posts_controller

def destroy
  @post = Post.find(params[:id])
  if @post.user == current_user
    @post.destroy
  else
    flash[:alert] = "Only the author of the post can delete"
  end
  redirect_to posts_path
end
```

## You do: Prevent Users from editing someone else's post.

## You do: Associate users with Comments

1. Create a new migration to add a user_id column to comments.
2. Associate the `current_user` with newly created posts.
3. Prevent User's from editing / deleting other's comments.

## Customizing Views

Devise has generated several views for us, but they are not currently visible in our
codebase.

If you want to customize them, generate the views with `rails g devise:views`

The possibilities are endless!

### Styling views

Whether you generated the views or not, you can style the forms the same way.

Identify the selectors you'd use to target the individual form elements, and add
styles in `app/assets/stylesheets/application.css`

## If there's time...

Try implementing an authorization solution on top of devise - https://github.com/ga-wdi-lessons/cancancan
