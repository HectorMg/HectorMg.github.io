---
layout: single
title: Creating a bank simulation application&#58; Introduction
category: development
---


Hey guys! This is the first post in a new series of blog posts dedicated
to sharing my experience with building bank simulation applications in
the Ruby on Rails development framework! I'll do my best to explain my
process and reasoning to the best of my ability, so make sure to email
me in case you have any questions! You can also follow the project on my
GitHub account, [HectorMg](https://github.com/HectorMg)!

The motivation section below provides a background explanation as to why
I decided to start this series, and why I thought it was relevant to
share my knowledge and experience in developing this kind of
application. If you're not interested in learning about why I'm doing
this, and want to go straight to the meaty, technical process, go ahead
and check out the [next post]({% post_url 2016-07-22-creating-a-bank-simulation-application-setup %}) in the series!


## Motivation

### Background

A few years ago, when I attended high school, I got involved in a student
organization focused on promoting leadership and entrepreneurial skills
amongst Jr. High and Highschool level students. The organization's main
responsibility is organizing and hosting two editions of a particular
event every year; once in the fall semester, and once in the spring
semester. Each edition is a three-day event in which those
participating are be grouped into 8 companies, each of which is assigned
one of four arbitrary markets/industries (food/entertainment/public
services).

The way in which these companies operate relies heavily on the bank
instituted by the organizers. Each company has its own bank account and
every account has a staff member assigned to manage it. The bank is
responsible for overseeing every and any oficial transaction between:

+ A company and the bank
  + Deposits
  + Withdrawals
  + Copyrights
  + Property/Real Estate
  + Fine payments
  + Tax payments

+ A company and another company
  + Business contracts
  + Sale contracts
  + Property/Goods Exchange
  + Licensing/Royalty contracts (bypassing proprietary copyrights)
  + Fine payments

My first experience with the bank was actually very interesting. I
initially joined as a staff member, and I was assigned to manage a
particular company's account. It was definitely a fun experience, but
that had very little to do with the actual bank work and more to do with
the people involved. In terms of the bank's system of operations, it was
hell. There was paperwork to be filled left and right, paper copies of
every contract, receipt and copyright, as well as detailed spreadsheets that
were as complicated as they were boring.

The system was so complex and delicate that we were expected to stay at the school past
11pm for the last night of every edition in order to make sure every
cent was accounted for.

### The "solution"

I was eventually chosen to take part in the organization's
student board in my senior year, precisely as the head of finance and thus, president of
the bank. My job description was simple:

+ get money
+ train the bank staff
+ supervise bank operations during the events

I, however, decided to add a fourth point to that description:

+ leave this better than I found it

So I decided to save my staff and all future generations from those late
night accounting marathons, countless hours of getting yelled at and
tons of stress by pouring all of those "downsides" onto myself for the
next five to six months. At the time, I had just started my first
computer science course in the International Baccalaureate's diploma
programme, where we were taught some Java. I had the basic programming
down, but in no way was I ready to migrate a fully functional manual banking
system into a software solution.

My first attempt took around 6 months. I used PHP to code the back end for the
banking system, MySQL as a database manager and Twitter Bootstrap for the front
end. Keep in mind I knew jack about either PHP, SQL (or any kind of database system), or CSS.
The application was a hot mess, it took a great effor and kind of worked for one reason and
one reason only... I was present when it was being used. For the first
edition under my supervision, the app crashed at least ten times per
day, I had to rollback transactions manually when buttons were clicked
twice in a row, and I had to hardcode login credentials for 15 staff
members and about 80 participants.

By the second edition (few months later), the app was a little more
stable, but I still only put about 9% trust in it not bringing the event
down in flames if I looked away. What I had built was not software, it
was a time-bomb that proved to be the least maintainable piece of code I
would ever write. About a month later, I tried to show the working app
to some friends, only to find out the mysqli database connector I was
using was long-since deprecated in PHP and the app turned into an ugly
static-site with weird error messages instead of navigation links.

Despite the horror I went through, I worked hard to hide it. I was
actually proud of my first big "software" project, and I was commended
by organizers that came both before and after me, other members of our board, the
whole staff (the end users of my app), participants and even the school's upper
management. They were so impressed that they asked the worst of me... to
make sure it was ready to be used by the following (and successive)
generations.

### Latest Fix

The idea of maintaining the code I wrote was horrible. Thankfully, I had
just been introduced to Ruby on Rails at [Icalia
Labs](http://icalialabs.com), the company were I had just started as an
apprentice and I'm now an intern at. Even with the help of my mentors at
Icalia, I hit several walls that I'm sure every developer has had to
deal with. The app I was (re)building was certainly unique in terms of
its functional requirements, so I couldn't just follow some tutorial. In
fact, I ran an extensive search to at least find tutorials on similar
projects, and the closest I came to what I was looking for was Michael Hartl's [Ruby on Rails
Tutorial](http://www.railstutorial.org/). I went through most of it, at
least the relevant parts. This helped me setup the application's basics
like the authentication system, static page views and some resources and
their respective controllers.

After going through Hartl's tutorial, I was really stuck. There was no
online resource that would teach me the specific steps to develop the
back-end logic that was unique to my app. I ended up coding the rest of
the app based on what I learned from Hartl's tutorial and pure
reasoning. By the end, the app worked fine, the pre-event setup was
barely a challenge, and there were almost no problems during the events
it was used in. Overall, I was happy with what I'd done, at lot happier
than the first time, but still, there was some serious smell coming from
my code. As I said, I had almost 0 experience devloping this kind of app
with Rails, and I coded stuff up as I came up with it. 

The lack of information I stumbled upon is what has inspired me to write
up this "tutorial" series. Partly, I'm hoping there's people out there
looking to develop bank simulation apps in rails and this tutorial ends
up being exactly what they are looking for, but really, I'm just trying
to share a bit of my knowledge hoping maybe parts of this tutorial will
help or inspire others to help!

That's it for my introductory rant, so make sure to check out the [next
part of this series]({% post_url 2016-07-22-creating-a-bank-simulation-application-setup %}), where I'll talk about the app more in depth and
walk you through some setup.


