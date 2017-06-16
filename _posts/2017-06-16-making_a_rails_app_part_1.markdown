---
layout: post
title:  "Making a Rails App, Part 1"
date:   2017-06-15 21:26:15 -0400
---

![Kramer Typing](http://i.imgur.com/bOjrnGa.gif)

A few late nights and very little to eat, and here I am. Rails project complete!

It may not be pretty... not yet anyway. But it works. That came after a week of writing, rewriting, and restarting when I created such a huge mess it was better to just start over. It left me with a few things I wanted share, which is why this post is coming to you in three parts. Each part is so important I think it's better if I spread them out.

I'm this part I'll be talking about setting up your models.

Sounds simple, right? If you've been coding through Flatiron this whole time, you've been making models since the early days in vanilla ruby before you ever knew of foreign keys and join tables and MVC applications.

I thought making models was in the bag... until it wasn't.

Up until just a few moments ago laying dejectedly in my recycling bin were the remains of two tries before that I picked over for useful code before finally deleting permanently. Remember them. Bad planning was their demise.

This post is all about planning and getting started. Here are three essential components of that:

## Writing it out

Check out the repo for my rails app here: [https://github.com/abonner1/code_learning_resources_manager](https://github.com/abonner1/code_learning_resources_manager).

You'll probable notice I have a NOTES.md file in directory, something I got in the habit of from all of Avi's review videos. While I probably should have plotted my models and their associations via Gliffy, writing out the models in my notes file did wonders.

```
User [name, username, email, password, provider, uid]
- has_many resources
- has_many enrolled_courses
- has_many courses, through: enrolled_courses

Resource [title, url, description, user_id, language_id]
- belongs_to user
- belongs_to language
- has_many resource_topics
- has_many topics, through: resource_topics
- has_many course_resources
- has_many courses, through: course_resources

ResourceTopics [resource_id, topic_id]
- belongs_to resource
- belongs_to topic

Topic [name]
- has_many resource_topics
- has_many resources, through resource_topics

Language [name]
- has_many resources

ADDED LATER:
EnrolledCourses [user_id, course_id]
- belongs_to user
- belongs_to course

Course [title, description]
- has_many enrolled_courses
- has_many users, through: enrolled_courses

CourseResources [course_id, resource_id]
- has_many course_resources
- has_many resources, through: course_resources
```

You might notice that I even wrote out the relationships between tables in a Rails-y sort of way in order to get down what exactly I was doing. I highly recommend listing out all columns, too. It helped me sort out errors in my logic that associations alone weren't doing.

All that being said, it didn't save me from severe model troubles. That's where testing comes in.

## Testing it out

I just recently purchased *Everyday Rails Testing with RSpec*. I'm only a few pages in, so I had no intention of rolling out tests for my project. If I had I don't think I would have needed to start over... twice. In fact, the only reason the third try succeeded was because I took the time to write out some simple model tests to ensure that everyone was playing nicely.

Needless to say, testing is important. And it doesn't have to complicated if you haven't written tests before. Look at my user spec:

```
RSpec.describe User, type: :model do
  before(:each) do
    @user = User.first
  end

  describe "username" do
    it "is unique" do
      User.create(
            username: "aviflombaum",
            email: "aviflombaum2@flatiron.com",
            password: "password")
      expect(User.where(username: "aviflombaum").count).to eq(1)
    end

    it "validates user when present" do
      @user.username = nil
      expect(@user).not_to be_valid
    end
  end

  it "has many resources" do
    expect(@user.resources.first).to be_a(Resource)
  end
end
```

I don't even think I wrote everything correctly or at least in the ruby way. I couldn't figure out when to use `describe`, but those tests work, as well as my models. That's all that matters for now!

If your reading this and haven't gotten to Rails yet, please, take the time to learn a little RSpec the moment you start Rails. It will save you countless hours of working through every error in your controllers and views until you finally come out with a app that does the job and does it right.

## Working it out

`rails c` sends you straight to the console. If you didn't before, you do know, and you should use it. The Rails console is different than the rake console and irb that you were using before. Rails console is meant to test out your Rails components in its actual environment, which means any methods you make in the app can be tried out here. It's also the perfect place to play with your models.

As Avi always advises: once you get some models live, play with them, see if they break, and figure out why. If it weren't for the Rails console, I would have missed numerous bugs in my models that came with the initial set up. I wouldn't have discovered that Rails 5 makes all belongs_to relationships required by default. I'm [not kidding](http://blog.bigbinary.com/2016/02/15/rails-5-makes-belong-to-association-required-by-default.html).

However, don't rely on the console alone. Seeding your database can ease some of the burden, and it gives you models to play around with once you've got your first views up and running as well.

You can also edit your seeds in the console when you discover new model issues once you've expanded your domain.

## Conclusion

I cannot express it enough how much writing it out, testing it out, and working it out made creating models a less painful process. It still has its difficulties, but if you implement these three principles, I promise you'll thank me.

Up next: Devise, Omniauth, and Rails, getting all three to work together
