---

layout: single
title: Creating a bank simulation application \#3&#58; Initial Models
category: development
---

Hey guys! Before we code anything, I'll go over the application's
general model structure as it is up to now. Along the way, I may change
my mind and add or remove models or maybe change hierarchies or
relationships, but worry not, since I'll blog throughout the development
process, you'll know about any changes I end up making. Either way, you
can follow my progess on this post in the `initial-models` branch of my
[Github repo](http://github.com/hectormg/bank-simulation-app).

## Hierarchical Overview
The first model I want to include, which I think is up to now the most
important addition to my previous attempts at this kind of application,
is the Event model. Since the app will be running bank simulations for
multiple events and editions, it's important for the administrators to be
able to create or destroy the data for entire simulations through
one simple model. By creating a Event model, we're allowing whoever
runs the show to quickly get rid of all the data produced in the last
event, and subsequently set up the backbone for the next edition just as
quickly. This will prevent me from having to set up the application for
the administrators for every future edition.

The diagram in fig. 1.1 shows the application's general hierarchy. Some
of the models in the diagram will end up being polymorphic to allow for
certain features, but 'll explain that when we get there.

#### Fig. 1.1 - General Hierarchy
<br>
<center><img src="{{site_url}}/images/bank-sim-models.png"></center>
<br>
As we'll see later in this post, Events will have many organizations of
different types, which will then have Members, Assets and Accounts of
different types as well. The final level of the hierarchy, Transactions,
will probably end up being the most important objects in the
application's execution.

So, as I said before, I'll get started by modelling my events. The
event model is a rather simple one. Other than a unique ID, all the
Event model is going to have for now is an edition and a year. I'll go
ahead and generate the Event model and migrate the database.

```bash
$ rails generate model Event edition:string year:integer
$ rails db:migrate
```

Having migrated my database, all that is left is to add some basic
validations to my Event model. Event is a rather simple model, so I'll
go ahead and validate that the edition is present and that the year is
actually a number. I don't have to validate the year's presence because
validating for numericality does that implicitly.

#### app/models/event.rb
```ruby
class Event < ApplicationRecord
  validates :edition, presence: true
  validates :year, numericality: true
end
```

That's pretty much it for the Event model for the time being! Now I'll
move on and start working on the Organization model!


I'm a little undecided when it comes to this model. There's basically
two types of organizations.The first type, the Bank, should actually be
unique in terms of the event. There's really only one bank per event.
The other kind of organization, companies, has a somewhat similar
behavior and attributes, but the implementation does differ slightly. 
I was thinking about implementing some sort of multiple table inheritance (MTI)
in order to properly encapsulate the different behavior that comes with
the implementation of each type of organization, but the way
ActiveRecord (AR) is designed doesn't really allow me to do that easily.
Figure 1.2 below shows more or less what I was thinking.

#### Fig. 1.2 - Organization Family
<br>
<center><img src="{{site_url}}/images/organization-family.png"></center>
<br>
I made the classes on the right tentative because for now, I'll model
the organization family through a single table inheritance (STI)
approach. As you can see, if I were to use MTI, the Bank class would
remain empty, and that'd be pretty useless. If I my opinion changes in
the future, I'll walk you through the changes, but for now,
I'll go ahead and create the organization model. Keep in mind that I'll
be adding the fields in the Company class diagram to the Organization
class. Figure 1.3 shows the updated class diagram for the Organization model.

#### Fig. 1.23 - Updated Organization Class
<br>
<center><img src="{{site_url}}/images/organization-class.png"></center>
<br>

As you can see, I added a "type" field in order to maintain STI
convention. For now, the value in the "type" field will either be "Bank"
or "Company", but I doubt we'll ever instantiate a particular kind of
organization by setting it's type. We'll probably use one of the advantages of STI and do something like
`Company.create(...)`, which creates an organization with type "Company", rather than `Organization.create(...:type => "Company")` which does the exact same thing. 
Before I go ahead and generate this model, I'll just add
that the last field, event_id will be a foreign key that ties the
organization to a particular event (the class I just modelled). Alright,
moving on:


```bash
$ rails g model Organization name:string market:string 
        numerator:integer type:string event:references
```

The migration should look something like this:


#### db/migrate/20160803032955_create_organizations.rb
```ruby
class CreateOrganizations < ActiveRecord::Migration[5.0]
  def change
    create_table :organizations do |t|
      t.string :name
      t.string :market
      t.integer :numerator
      t.string :type
      t.references :event, foreign_key: true

      t.timestamps
    end
  end
end
```

After migrating the database one more time with `$ rails db:migrate`,
I'll create two classes in the models directory without creating tables
for them in the database. These classes will be Bank and Company. I'll
go through each of the new models next, and then we'll tie up a couple
loose ends.

#### app/models/organization.rb

```ruby
class Organization < ApplicationRecord
  validates :name, presence: true, uniqueness: true
  validates :type, presence: true
  belongs_to :event
end
```
Starting with the organization model, I added some universal behavior in the
form of AR validations. Before an organization is saved into
the database, our model will validate that the organization's name is
both present and unique. This means that an empty string (""), nil or
repeated value in the name field will result in the instance object not
being saved in the database. Finally, no matter what type of
organization we instantiate, it will belong to a particular event,
hence `belongs_to :event`.


#### app/models/bank.rb

```ruby
class Bank < Organization
  after_initialize :default_attributes

  private

    def default_attributes
      self.name ||= "The Bank"
    end
end
```

The Bank model is a little more simple. The class inherits from
Organization, and besides that, the only method I included in this
model is a private method called `default_attributes`. This method simply memoizes the object's
name attribute, so if it's not set as a parameter when built, it will be set to "The Bank" by
default after it is initialized. This method comes from the fact that there
will only be one bank per event, and it will be initialized
automatically when the event is created, but you can feel free to change the
default name to anything you like, or simply omit this method and its
call as an after_initialize callback.



#### app/models/company.rb

```ruby
class Company < Organization
  validates :market, presence: true
  validates :numerator, numericality: true
end
```

Unlike Bank, the Company model has validations of its own. Since companies are
divided into four markets, they absolutely must include both market and
numerator. Since the numerator is supposed to be an integer, I'll also
validate for numericality (which, as stated earlier, validates for
presence implicitly). Since there are eight companies and four
markets in the particular case of the event I'm developing this app for, I'll assume that there
should be two companies with each market. Between those, they'll have
1 and 2 as a value for their numerator attribute. 

Having said this, I will not add
validations to enforce a numerical limit or check for the markets of
each company. Since I'll be the one to code the method
that sets up the whole structure before the event, and I already know
how it should be, that would not be necessary.


Finally, I made two slight additions to the Event model. We've already
setup half of the relation in the Organization model by adding the
`belongs_to :event` line, so we'll wrap it up on the Event side.

#### app/models/event.rb

```ruby
class Event < ApplicationRecord
  .
  .
  has_many :companies
  has_one :bank
end
```

As you can see, I handle both models (Company and Bank) here. The
Organization model already specified that an organization, whatever type
it has, will belong to an event, but it doesn't specify the rest of the
relation. An event will have many (in this case probably 8) companies,
but it only really needs one bank. Thankfully, having used an STI
approach will allow for this simple distinction. 

The `has_many :companies` line allows me to create companies through the
actual Event instance like so: `instance_of_event.companies.create(name:
"foo", market:"bar", numerator: 1)`. As you can see, I don't need to add
`type: "Company"` as a parameter value because AR understands through
STI that `instance_of_event.companies` stands for an Organization with `type: "Company"` and
the bevior of the Company child class. That line will add the company to
the Organizations database with all of its attributes, type "Company",
and with an event_id that matches `instance_of_event`.

The `has_one:bank` line, on the other hand, allows for a different yet much
simpler instantiation. Whenever I need to instantiate an event's bank,
thanks to the way the class is designed, I need only write
`instance_of_event.create_bank`. 

Of course, I could add a name, market,
or even numerator, as they are part of the class that the Bank inherits
from. However, since the validations for those attributes happen in a
class at the same level as the Bank model, Bank doesn't inherit them, so
they're completely optional. Combining that with my `default_attributes`
callback, that single line of code will instantiate a Bank object, give
it "The Bank" as a name, and save it in the database under the
Organizations table, type "Bank", and the event_id belonging to
`instance_of_event`.


And, guess what! We're done with all these models for now! If you'd like
to see what the tests I write for them look like, you can always see the
project in Github. The specs for this post should be under the
"initial-models" branch in the specs/models folders. As always, I
encourage you to email me if you have any questions, or open up an Issue
on the [Github repo](http://github.com/hectormg/bank-simulation-app) if
there's anything you think can improve! 

Stay tuned for post #3, as we'll start modelling the users for our
bank simulation application!
