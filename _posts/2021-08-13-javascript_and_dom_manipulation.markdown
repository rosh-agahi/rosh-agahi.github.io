---
layout: post
title:      "JavaScript & DOM Manipulation"
date:       2021-08-13 23:37:02 +0000
permalink:  javascript_and_dom_manipulation
---


In the previous two projects we built web applications that acted as GUIs - graphical user interfaces - that allowed users to navigate through multiple pages, or views, to interact with the underlying databases. Each view had its own unique url path and a corresponding http request. For the fourth project, we leapt forward and implemented what we learned about DOM manipulation using JavaScript to handle the various views and requests to the database on a single page. 

To satisfy the requirements of this project, I created an app to help students store and study their flashcards. The study feature has two options, one that only uses the currents user's flashcards and the second allows students to flip through flashcards for a particular subject that were created by other students - hence the name, "StudyShare". 

The app is set up using a Rails API on the backend with models for User, Subject, and Flashcard. The frontend uses the following 6 AJAX calls to the backend to **create** or **read** instances of the models. 

Flashcards
1. create new flashcards
2. read flashcards for a particular subject

Subjects 
3. create new subject, 
4. read all existing subjects to populate new flashcard form and study form

User
5. create new user 
6. read through existing users to login user. 

On the frontend, JavaScript OO objects are used for Subjects and Flashcards. When the user starts a study session, the request identifies all of the flashcards in the database that belong to the Subject associated with the request and creates a Javascript Flashcard object and collects all of these into an array that are looped through for studying. Similarly, when the user opens either the form to create a new flashcard or start a study session, the form needs to be populated with all of the available Subjects in the database. 

Since my app doesn't collect any sensitive information, I considered not having a user model and letting a user interact with the data without requiring login. But because I wanted a user to be able to access only their own flashcards instead of the whole universe of flashcards, I decided to implement a simplified user login that doesn't require a password, just an existing username. Having a single page application means that I wasn't able to set a user session upon login, so instead I set about implementing this simplifiend user experience as follows:

### A Simplified User Experience

To register for an account, the app requires that the user only enter a unique username. When the user hits "Register", a form handler sends a post request to the database to add a new user, which is only completed if the username they entered is unique because the model. 

```
function createUserRegisterHandler(e) {
  e.preventDefault()
  const usernameInput = document.querySelector('#username_input').value
  postUserFetch(usernameInput)
}

function postUserFetch(username) {
  const userFormData = {username}

  fetch(endPointUsers, {
    method: "POST",
    headers: {"Content-Type": "application/json"},
    body: JSON.stringify(userFormData)
  })

  .then(response => response.json())
  .then(user => {
    console.log(user);
  })
}
```

The Javascript functions to then log in the user is as follows. It start by setting these variables:

```
let userLoggedIn = [false];
let i = 0;
let matchingUserId = 0;
```

Then the form handler function is invoked which takes the user input for username, passes it to the getUser function which then tries to match it against the usernames in the database. If a match is found, the matchingUserId variable is set to that user's id. Since this variable is in the global scope of the application, setting it and then not having any functions which can modify it, effectively acts similarly to setting the user id in the session id which we used to do with the Sinatra and Rails applications. 

```
function createUserLoginHandler(e) {
  e.preventDefault()
  usernameErrors("")
  const userLoginInput = document.querySelector('#username_input').value
  getUser(userLoginInput)
}

function getUser(findUser) {
  fetch(endPointUsers)
  .then(response => response.json())
  .then(users => {
    users.data.forEach(u => {
      if (u.attributes.username == findUser) {
        i += 1;
        updateHeadingOnLogin(findUser);
        matchingUserID = u.id;
      }
    })
  if (i > 0) {
    userLoggedIn = true;
    populateFlaschardField('<p class="box"> Use the navigation to the left to select a subject and start studying, add a new subject, or add flashcards for existing subjects.</p>');
    }
  if (i == 0) {usernameErrors("User Not Found. Hit Register.")}
  })
}
```

By using this method, in order to log a user out, one would only have to refresh the page, which would reset the matchingUserID variable. 




