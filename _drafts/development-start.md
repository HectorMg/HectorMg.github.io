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
Event model is going to have for now is an edition and a year.

```bash
$ rails generate model Event edition:string year:integer
```
