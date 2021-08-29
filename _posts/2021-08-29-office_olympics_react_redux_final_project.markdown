---
layout: post
title:      "Office Olympics: React/Redux Final Project"
date:       2021-08-29 18:26:08 +0000
permalink:  office_olympics_react_redux_final_project
---


Final project is in the books and my review is tomorrow! 

Office Olympics is app an designed with working from home in mind. Right when we thought we might be going back to the office, Covid has thrown us one curveball after the next and we have had to become increasingly creative in how we keep ourselves busy and our moral up when all we do is stare at spreadsheets all day without a healthy dose of human interaction to break up the day. Something we have started doing at work is putting on challenges for our employees to get us to interact with one another outside the frequent and phlegmatic zoom meetings. But organizing these challenges has proven... well, challenging.

This react app allows users to create challenges with a name, description and rules. Users can also select challenges to participate in by making submissions and voting on their favorite submissions. No more threads in disparate slack channels and inconsistent voting practices. Office Olympics takes care of all the details so employees can focus on having fun and participating.

Since the final project did not require user login functionality, I decided to keep it simple and not require users to log in. One limitation that this presented was that it made voting on submissions anonymous and doesn't limit users to one vote per challenge, which could undermine validity of the voting process if this was launched. If I were to continue building this project, that would be the first thing I would address. 

Additionally, the SubmissionCard component is only configured to render an iframe element that receives an embed-specific YouTube url. I think various challenges would require different types of subissions including possibly one or multiple photos. This would have to be addressed in both the backend - to recognize and receive links to photos - and on the frontend to conditionally render media components of a submission based on what is available or required by the challenge. 

Alltogether, this was a very exciting project to work on, not only because it was the last one but also because I was able to see this entire course come together: 
* building out a Rails API that employs models and controllers that are powered by Ruby methods, 
* data management and persistance with ActiveRecord
* styling and rendering on the frontend with HTML and CSS
* using React implement structure and incorporate JavaScript functionality to create an interactive user interface
* using Redux to update the UI in response to realtime changes to the backend API

Seeing as Covid will continue to keep us from returning the office anytime soon, I would love to keep working on this project and potentially launch it for use where I currently work!
