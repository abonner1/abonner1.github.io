---
layout: post
title:  Self Join
date:   2017-06-10 17:00:00 -0700
---

There's a way to create ActiveRecord associations within a single table. It's called a self join.

The concept is a little harder than you think to first wrap your head around. I'll give you two short examples of the self join and where it is useful.

I discovered the self join while working on a cart and user model for an online store app. While the application is far more complex than two models, this is all you need know to observe the effectiveness of the first example. It is possible to write getter and setter methods for the user model (I did so and passed the rspec test); however, it doesn't actually provide the functionality we want.

Here's a simple example of the self join:

```
class User < ActiveRecord::Base
  has_many :carts
  belongs_to :current_cart, :class_name => "Cart"
end
```

You've definitely seen these macros (`has_many` `belongs_to`) if you've worked with ActiveRecord Associations before, but `:class_name` might look unfamiliar. What's occuring here is simply assigning the `current_cart` association to the Cart model thus giving it all the methods and perks associated with that model. In this case, it means that `current_cart` will have a method `.items` (in the cart model) which will show all items currently in that cart.

According to the Rails guide for associations, the only way for this macro to work is add a reference to the migration like so:

```
class AddCurrentCartIdToUsers < ActiveRecord::Migration
  def change
    add_reference :users, :current_cart, index: true
    add_foreign_key :users, :current_carts
  end
end
```

In actuality the rails guide only specifies the `add_reference` line of the code, but if you were to use rails to generate the migration (`rails g migration AddCurrentCartIdToUsers current_cart:references`), the second line is added.

Now `@user` can respond to `@user.current_cart` and return a cart object that has been assigned as the current cart.

The second example is straight from the Rails guide:

```
class Employee < ApplicationRecord
  has_many :subordinates, class_name: "Employee",
                          foreign_key: "manager_id"
 
  belongs_to :manager, class_name: "Employee"
end
```

```
class CreateEmployees < ActiveRecord::Migration[5.0]
  def change
    create_table :employees do |t|
      t.references :manager, index: true
      t.timestamps
    end
  end
end
```

The `belongs_to` association is exactly like the above example for carts, while the `has_many` example, is just a little different.

In the case of the `has_many`, a foreign key is specified. Just as in a normal association, only the table with a `belongs_to` relationship needs a foregin key physically added to the database. This `has_many` association extablishes that an employee has many subordinates whose association with particuarl managers can be tracked through a `manager_id` attached to the manager association.

Just as above, `@employee` will now respond to the method `@employee.subordinates` and `@employee.manager`.
