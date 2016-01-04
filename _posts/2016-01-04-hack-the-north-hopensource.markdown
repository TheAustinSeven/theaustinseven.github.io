---
layout: post
title:  "Hack The North: Hopen Source"
date:   2016-1-4 12:00:07 -0500
categories: HopenSource Hack-The-North Hackathon Rails Ruby
image: "/images/2016/htn-moxtra.jpg"
---
Way back in September, I was a part of a team that rocked Hack the North, or rather, won the Moxtra API prize. We built an app that we named "Hopen Source". With Hopen Source, we aimed to provide a way for non-profits and charities to work together to build open source applications that help those organizations better do their jobs. 

The team consisted of:
- Ben Zheng, a student from University of Waterloo who was tasked with integrating Moxtra's API into our app.
- Shruti Appiah, also a student from University of Waterloo who took the lead on our front end development
- Giorgio Delgado, a student from Wilfrid Laurier University, who ended up working on all parts of the app
- and myself who rocked the backend

We were give a brutal course in Murphy's Law, as everything that could go wrong, went wrong. This was in no way a usable website. Every other page would crash the app, the UI was buggy as we hadn't had time to change the home page so that it would look like you were logged in, and our stylesheets were haphazardly thrown together. Miraculously we were able to demo our app without it breaking in front of the judges or and of the sponsoring organizations.

If that wasn't enough, about 2-3 hours before the deadline, AWS East unexpectedly went down. Not part of AWS East, but the entire thing. This wouldn't have been an issue except since we were using Heroku, we couldn't simply switch which region we wanted it hosted in. Thankfully we were able to quickly put our app up on Linode, but since Linode had the worst possible setup ever, our app needed to be restarted on an hourly basis or the Linode VM would go into some sort of standby mode.

Despite all of this, we managed to win the Moxtra API prize for integrating the Moxtra API into our app, which was no easy task (Ben's had to almost exclusively work on that for the entire hackathon).

Even though everything went wrong, I had a blast. Hack the North puts on a great hackathon, and I had an opportunity to work with an amazing team on an awesome project. That said we are trying to continue this and may eventually launch it to the public. Right now you can see the [devpost](http://devpost.com/software/hopensource), the [github repo](https://github.com/HackTheNorthDreamTeam/HopenSource), or the [site itself](http://hopensource.co).
