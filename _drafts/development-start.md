---

layout: single
title: Creating a bank simulation application&#58; Initial Models
category: development
---

Hey guys! Before we code anything, I'll go over the application's
general model structure as it is up to now. Along the way, I may change
my mind and add or remove models or maybe change hierarchies or
relationships, but worry not, since I'll blog throughout the development
process, you'll know about any changes I end up making. Either way, you
can follow my progess on this post in the `post-2` branch.

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

<img src="{{site_url}}/images/bank-sim-models.png">

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
ActiveRecord is designed doesn't really allow me to do that easily.
Figure 1.2 below shows more or less what I was thinking.

#### Fig. 1.2 - Organization Family

<img src="{{site_url}}/images/organization-family.png">

I made the classes on the right tentative because for now, I'll model
the organization family through a single table inheritance (STI)
approach. As you can see, if I were to use MTI, the Bank class would
remain empty, and that'd be pretty useless. If I my opinion changes in
the future, I'll go ahead and walk you through the changes, but for now,
I'll go ahead and create the organization model, keep in mind that I'll
be adding the fields in the Company class diagram. Figure 1.3 shows the
updated class diagram for the Organization model.

#### Fig. 1.23 - Updated Organization Class

<img src="{{site_url}}/images/organization-class.png">


As you can see, I added a "type" field in order to maintain STI
convention. For now, the value in the "type" field will either be "bank"
or "company". Before I go ahead and generate this model, I'll just add
that the last field, event_id will be a foreign key that ties the
organization to a particular event (the class I just modelled). Alright,
moving on:


```bash
$ rails g model Organization name:string market:string 
        numerator:integer type:string event:references
```

The migration should look something like this:

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

After migrating the database one more time with `$ rails db:migrate`
