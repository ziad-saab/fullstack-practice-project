# DecodeMTL full-stack practice project: Dashboardly!

## Introduction
Here's the situation:

Dashboardly hired a senior full-stack developer to build their grand-parents dashboard application. The dude started building the API, database and front-end in parallel, but got hit by a bus midway through. You are his succession. You have been tasked with completing the missing code to create the MVP for the app, then implementing one by one a set of features to make the app better.

To help you complete your work successfully, Dashboardly has done three things for you:

1. Provided a set of mockups of what the application should look like.
2. Written document describing all the missing code to make the MVP work, as well as an explanation of each feature they want to build (attached)
3. Hired three experienced consultants (mentors) who are really good at giving hints, but cannot type due to having lost their hands in a crocodile fight

Your task is to bring the project forward as much as possible for Friday, which was announced as the official launch date for Dashboardly a year ago.

Apart from this time constraint, your other constraint is money. These consultants are :moneybag:uper expensive, and you are only allowed **25 credits** per team. Each credit gives you a **15-minute slot** with a mentor and you cannot take more than one slot per hour. A credit is all or nothing: if you use mentor time for only three minutes, you have consumed a credit.

---

## Mockups

### Home Page (displays all boards)

#### Mobile
![Mobile Home Page](mockups/Home-mobile.png)

#### Desktop
![Desktop Home Page](mockups/Home.png)

### Board Page (displays a single board)

#### Mobile
![Mobile Board Page](mockups/Board-mobile.png)

#### Desktop
![Desktop Board Page](mockups/Board.png)

### Menu (view of the opened menu)

#### Mobile
![Mobile Menu](mockups/Menu-mobile.png)

#### Desktop
![Desktop Menu](mockups/Menu.png)

### Create a board modal

#### Mobile
![Mobile Create Board Modal](mockups/Create-Board-mobile.png)

#### Desktop
![Desktop Create Board Modal](mockups/Create-Board.png)

### Create a bookmark modal

#### Mobile
![Mobile Create Bookmark Modal](mockups/Create-Bookmark-mobile.png)

#### Desktop
![Desktop Create Bookmark Modal](mockups/Create-Bookmark.png)

---

## Document from Dashboardly
This document outlines what is currently missing to make the MVP 100% functional.

### What is currently there?

#### Database
The table structure that supports the MVP is 100% created and available in the API repository.

#### Back-end API
There is an **[ExpressJS API for Dashboardly](https://github.com/ziad-saab/dashboardly-api)**. It is not 100% in compliance with the API contract. More is explained below.

#### Front-end
There is a **[web app for Dashboardly](https://github.com/ziad-saab/dashboardly-frontend)**. It is not 100% complete nor in line with the mockups. More is explained below.

### What is left to do?

#### Database
Have a look at the queries being made on the database and verify if any indexes still need to be added to make some queries faster.

#### Back-end API
First, you should read all the existing code and understand what it's doing. You should also execute the code in your development environment and make sure it is working.

Then, you need to implement the following:

1. You need to fill in the code for the `controllers/bookmarks.js` module. You can take advantage of the already completed code in `controllers/boards.js` to get a hint on how to do it. You'll also have to implement some accompanying methods in the `DashboardlyDataLoader` module. **Make sure to validate the user before modifying/deleting bookmarks!!** 
2. You'll also need to fill in the code for the `POST /boards/:id/bookmarks` handler in `controllers/boards.js` in a similar way. This may also require adding methods in `DashboardlyDataLoader`.
3. Fill in the code for the `GET /boards/:id/bookmarks` handler in `controllers/boards.js`.
4. Fill in the code for the `GET /auth/me` handler in `controllers/auth.js`.
5. The API contract says that user objects should have an `avatarUrl` field, but as you can see that field is not part of the `users` table in the database. That's OK. The database and REST representations don't have to match at 100%, and resources don't even have to be tied to any storage. In this case though, the `avatarUrl` can be generated from the `email` field using the [Gravatar service](https://en.gravatar.com/site/implement/images/). To do this you'll need to generate an `md5` hash of the email (read the docs!). The best place to make this addition would be in your data loader: before sending out a user object, add an `avatarUrl` property using the Gravatar instructions to generate it. 

    **NOTE**: Even though this logic could be implemented on the client, we choose to implement it at the level of the API. This way we can change our `avatarUrl` logic only once, and all client applications will see the change.
  

When you are done, make sure to test your API thoroughly using [Postman](https://chrome.google.com/webstore/detail/postman/fhbjgbiflinjbdggehcddcbncdddomop?hl=en). You should test the following:

1. List all boards
2. List a single board
3. List all the links for a single board
4. Signup, as well as receiving proper errors where appropriate
5. Login and get a token, as well as receiving proper errors where appropriate, then try it with `/auth/me`
6. Logout, and checking that your previous token was indeed removed from the system
7. Using a valid token, create/modify/delete a new board
8. Try creating a board without a token and make sure it fails
9. Using a valid token, create/modify/delete a new bookmark under a board you own
10. Using a valid token, create a new bookmark under a board you don't own and make sure it fails
11. Try creating/modifying/deleting a bookmark without a token and make sure it fails

Once all these elements are working through Postman, the next step will be to test how well they integrate with the client application. Since the app was built using the Apiary mock server, there should be a 100% match :)

#### Front-end
First, you should read all the existing code and understand what it's doing. You should also execute the code in your development environment and make sure it is working.

Then, you need to implement the following:

1. Styling: try to get as close as possible to the mockups, without using any CSS framework. You may use the grid system that you built in week 4.
2. Implement the signup component. You can look at the login component for some inspiration, even though it's not 100% the same.
    i. Create the signup form in `src/components/pages/SignUp.js`
    ii. Make an API call to the "Create new user (signup)" endpoint
    iii. On success, use React Router's `browserHistory` to redirect the user to `/login`
    iv. On error, display the errors in the form
3. For logged in users, make sure the menu displays their avatar URL from the API. To do this you'll have to call the `/auth/me` endpoint and retrieve the `avatarUrl` of the logged in user.
4. For logged in users on the home page, make the `+` button work. It should open a modal prompting the user to create a new board like in the mockups. On submit, make the appropriate API call. Once the board is created, redirect the user to that board's page with React Router's `browserHistory`.
5. Fix `src/components/pages/Board.js` to add a `+` button if the logged in user owns the current board. Then, implement that `+` to let the logged in user add a bookmark on their board. It should open a modal like in the mockup, then make the appropriate API call to create the bookmark. On success, you should refresh the list of bookmarks from the server, making sure to keep the component DRY.
6. Fix the logout functionality in `src/components/modals/Menu.js`: instead of being a `<Link>` to `/logout`, this should simply be a clickable element that fires the appropriate API call to delete the session, then removes the session token from local storage.
7. On the home page listing all boards, add an `Edit` button to the boards that are owned by the logged in user. Clicking `Edit` will open a modal letting the user modify the board's title and description. Submitting should call the appropriate `PATCH` endpoint on the API, then reload the list of boards.
8. On a board page that is owned by the logged in user, add an `Edit` button to all the bookmarks. Clicking on `Edit` will open a modal just like the one for creating a bookmark, letting the user change the url and title of the bookmark. Submitting should call the appropriate `PATCH` endpoint of the API, then reload the list of links.
9. In a similar way to (7) and (8), also provide `Delete` buttons to allow the user to delete their own boards as well as bookmarks individually.

You will have to test all this UI by connecting it to the real API you are building rather than the mock data from Apiary. To do this, simply modify the `API_URL` config in `src/config.js` and test away.
