---
layout: post
title:  Double Render Errors
date:   2017-06-03 19:07:00 +0000
---

So I'm not a long form blogger. Hours of typing up clever posts substituted for hours of typing up actual code is not my idea of a well spent afternoon. With the exception of project posts, I'm starting a series of short segments each focusing on a particular code problem.

![Murder She Wrote - Typing](http://i.imgur.com/7fyj7Y1.gif)

The other reason for this change is that it hopefully motivates me to blog on a semi-regular basis. Fingers crossed. This week:
## Avoiding Double Render* (and redirect_to) Errors
RailsGuides are a lifesaver. Oftentimes, I've discovered the solution to a baffling Rails problems in them. However, there have been situations when I know nothing more about what I'm looking for, than just a vague error message and a few guesses as to what it might mean. This particular example will seem contrived. The only point of the application was to discover how Rails handles cookies through sessions and nothing more.

The sessions controller has three actions: `new`, `create`, and `destroy`. The `new` action leads to a login page. The `create` action instantiates a cookie in the sessions hash under `session[:name]`, and the `destroy` action deletes the cookie when the user logs out.

My double render error occured in the `create` action exclusively. Here's the code that created the error:

```
def create
    session[:name] = params[:name]
		
    if !session[:name] || session[:name].empty?
      redirect_to login_path
    end
		
	redirect_to root_path
end
```

You see, Rails reads this as having two redirect_to routes and spits out an error that says something like this: "Can only render or redirect once per action."

My first solution to the problem was in desperate need of refactoring. I'll reproduce it here:

```
def create
    if params[:name].present?
      session[:name] = params[:name]
      redirect_to root_path
    elsif !session[:name] || session[:name].empty?
      redirect_to login_path
    end
  end
```

Let me preface this by saying it works like this, but the code's a mess. Since Rubyists love simple, elegant code and since we programmers like to keep our controllers DRY, I sought out a better solution to my double render error and found one. This is it:

```
def create
    session[:name] = params[:name]
    if !session[:name] || session[:name].empty?
      redirect_to login_path and return
    end
    redirect_to root_path
  end
```

Notice I was close to the solution with the code that brought about the error. In fact, there are only two words difference between the two snippets. Did you spot it? That's right. The difference is `and return`. Looks simple, but what's happening?

Until the return is added to the action, Rails reads everything in the if statement and then continues beyond it, throwing the error message when it arrives at a second `redirect_to` path. But remember in Ruby, `return` acts like a `break` and exits the method before the entire block is executed, which means that if there is no `session[:name]` or it is empty, Rails will redirect to the login path and break out of the block.

Without breaking, the if statement tries executing both the if block and the line under it. In the Rails server, you will see that controller action works as expected, but it will not pass any tests instead spitting out a cryptic paragraph that had me scratching my head. I'd like to think the only reason it worked in the Rails server was dumb luck, but Rails likes to guess.

Needless to say, use return in your action whenever there are multiple redirects. You might even could write it like this:

```
return redirect_to login_path
```

Even less code! Adding a return statement protects you from Double Render Errors, and it makes your code readable to other developers. Cheers!

*If you want to read up on Double Render Errors in the RailsGuides, check it out here: [http://guides.rubyonrails.org/layouts_and_rendering.html#using-render](http://guides.rubyonrails.org/layouts_and_rendering.html#using-render), section 2.2.14.
