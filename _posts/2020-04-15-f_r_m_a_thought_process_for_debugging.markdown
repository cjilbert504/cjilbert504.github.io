---
layout: post
title:      "F.R.M: A Thought Process For Debugging"
date:       2020-04-15 15:03:59 +0000
permalink:  f_r_m_a_thought_process_for_debugging
---


Learning to code can be very challenging at times and yet early on in an application build or lab things can seem to go incredibly smooth and then walls are hit and bugs are born. In my experience it seems to happen to everyone at least once, myself included. For myself I found a few ways to help find where these creatures reside by pulling on my past experience as an audio engineer.

In programming the concept of object relational mapping (ORM), is the idea of mapping the objects of an application to tables in a database management system, allowing the attibutes and relationships of the objects to be stored and retrieved with ease, relational mapping. The idea of relational mapping got my mind turning out of nowehere while trying to find a bug in a lab at Flatiron School one day and suddenly I had a moment of clarity and realized that an application flows very similarly to that of how audio signal flows through an audio mixing console.

The idea of signal flow in the audio engineering world is all about knowing where your signal from an input source, i.e. a microphone or a direct input from something like a keyboard or other electronic devices, is at in the console at any given moment and being able to utilize that knowledge to then manipulate the signal as needed/ desired. Applications, like audio mixing consoles, take input from an outside source, manipulate that input in someway and store and return the input to the user as desired(hopefully). Audio signal can be re-routed to different paths/ places, much like an object can be routed to different places in an application. Audio signal can be altered, i.e. compressed, equalized, much like data can be altered, i.e. split, sorted, reversed and so much more. The connections were omnipresent.

Armed with this new knowledge of being able to *relate* past skills to my current path of learning to program I was able to incrementaly step through the entire flow of the application, pulling the output from the application at any point that I desired, much the same as I would with an audio signal, to verify that the output at each point matched what I expected until I encountered a place where it did not! Alas, bug located! 

Back to the relational piece from the paragraphs above, being able to relate my past experience to my current situation is what lead me to coming up with the idea of FRM. Derived from ORM, FRM is what I am referring to as Familiarity Relational Mapping. The idea of drawing on past knowledge as a way to relate to your current task at hand. It has been an incredibly powerful tool for me since it came to me and even if you don't know anything about audio engineering, I invite you to try to draw from something in your past to make a relational connection to understand whatever your current task at hand or struggles may be.


