---
layout: post
title:      "Logging in and authenticating different types of user models"
date:       2020-10-11 17:24:35 -0400
permalink:  logging_in_and_authenticating_different_types_of_user_models
---


### takeaways
* commit regularly and often
* the `[user_id]` key used for adding the user's id to a session in order to log them in is a stand in for whatever the name of your user model is (yes, I learned this the *very hard* way)

#### My Sinatra portfolio project inadvertently turned into a low-grade social media app for connecting people who have tattoos (users) with those who create tattoos (artists). 

Once I had the initial MVC model down I decided that, instead of the "Artist" column being a static value, I would create a has-many-through relationship using the `tattoos` table as the join. 

So I created the model, controller, and views folder for **Artists** and set about connecting them to the rest of my app. I created new `helper` methods for the Artist class. Now I had `user_logged_in?` and `artist_logged_in?` as well as `logged_in?` which showed me if either type of account was logged in. I also had both `current_user` and  `current_artist`, as well as `redirect_if_not_logged_in` and `redirect_if_logged_in`, both of which used the `logged_in?` helper method.

I was intent on both types of account holders being able to use the same login page, which meant playing around with the `sessions_controller` to create a conditional that allowed this to work.

Initially I nested a conditional within a case statement:

```
    post '/login' do
        case 
        when @user = User.find_by(username: params[:username])
            if @user && @user.authenticate(params[:password])
                session[:user_id] = @user.id
                redirect "/tattoos"
            else    
                flash[:login_issue] = "You've entered an invalid username or password - please try again."
                redirect "sessions/login"
            end
        when @artist = Artist.find_by(username: params[:username])
            if @artist && @artist.authenticate(params[:password])
                session[:artist_id] = @artist.id
                redirect "/tattoos"
            else    
                flash[:login_issue] = "You've entered an invalid username or password - please try again."
                redirect "sessions/login"
            end
        end
    end
```

It seemed to work until I entered a username that wasn't in the database and ended up on a blank page because my `when` statements only account for circumstances where the username exists but the password is incorrect. So, I played around in `pry` and discovered 


After playing around in `pry` for a while this is what I settled on:

```
    post '/login' do
        @user = User.find_by(username: params[:username])
        @artist = Artist.find_by(username: params[:username])
        if @user && @user.authenticate(params[:password])
            session[:user_id] = @user.id
            redirect "/tattoos"
        elsif @artist && @artist.authenticate(params[:password])
            session[:artist_id] = @artist.id
            redirect "/tattoos"
        else    
            flash[:login_issue] = "You've entered an invalid username or password - please try again."
            redirect "/login"
        end
    end
```


The biggest issue I had with multiple user models logging in was actually in the act of logging in - or adding the `user_id` to the `session` hash. What I mean by this is that for a while, whenever I'd log in an *artist*, the program would get so far as to match the correct account and authenticate the *artist*, but would then log in the *user* with the matching id number. (I followed each step of the login process in `pry` to see exactly where the disconnect was.)

WHY. I spent a few hours trying to figure it out, troubleshooting high and low on Google, and actually reverted my project back to a simple "has-many" and "belongs-to" MVC before realizing that my problem had a 2 character solution. (I hadn't pushed the changes to github, for the record, and then had to recreate the has-many-through relationship from scratch: and so this project has also been an invaluable lesson in commiting regularly and often.)

What I knew about logging in was that it involved setting the `session[user_id] = user.id` 

And that's what I was doing. 

`session[user_id] = user.id` 

`session[user_id] = artist.id` 

For whatever reason I thought the session `key` for the corresponding id `value` had to ***literally*** be `[user_id]`.

It does not. In all the online literature I read, it's written as such. But I now know that `user` in this instance is simply a stand in for whatever the name of your user model is (in my case it was *User* and *Artist*).

So I rebuilt my has-many-through framework and logged in the User and the Artist with the correct respective keys.

`session[:user_id] = @user.id`

`session[:artist_id] = @artist.id`

If someone, somewhere on the internet would have told me that it probaby would have saved me about 12 hours of troubleshooting and recreating things that I ⌘Z-ed into oblivion and hadn't backed up anywhere.

I'm bitter - but just at myself. 


