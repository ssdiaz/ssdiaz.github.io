---
layout: post
title:      "Final Project – React/Redux "
date:       2022-03-29 04:17:06 +0000
permalink:  final_project_react_redux
---

This blog is dedicated to my final project, using React as frontend to create a Single Page Application. 

My application is a Bachelorette Planner Dashboard, intended to help you plan and organize an upcoming Bachelorette event. You can add a trip with associated attendees and activities, and give unique details to each attribute. 

In this blog I will summarize the project set up. To build the frontend, I used the `npm create-react-app MyAppName` generator command. 

### Backend
* Ruby on Rails was used as the backend server. 
* A Trip has many Activities and Attendees. Therefore, Attendees and Activities belongs to a Trip.

### Index.js
* This file tells our app what to render and where. 
* Here we import any packages we want to use throughout the app and set up the Redux store.
* We also wrap the root component (App.js) inside the `Provider` tags (to connect the App to the Redux store) and  `Router` tags (to use RESTful routes throughout our app with package React Router). 

### App.js
* As mentioned, this is the root component of our app. 
* Here, I render the NavBar component, which displays across all pages. 
* I also render the TripsContainer component to kick off the project.

### Container Components
* The container components are used to organize like-components and can map the state from the backend to the frontend, which can be passed down to the child components to render via props.
* Each object has their own container component – `TripsContainer.js`, `AttendeesContainer.js`, `ActivitiesContainer.js`
* The TripsContainer is the main parent container, and calls a fetch request to the backend to gather data on every Trip saved. It calls this fetch request using `componentDidMount`, which then calls an action creator to dispatch the newly gathered Trips to update state in the Redux store.

### Child Components
* The child components render the data received as props from their respective container components.
* Working backwards for example, the TripDetails.js (as a presentational component) receives its data to render as a prop from parent component Trip.js, which receives it’s data from parent component, Trips.js. Trips.js makes a connection to the Redux store to render the trips saved via a function named `mapStateToProps`. The Redux store gets its data from the TripContainer, which upon mounting (with `componentDidMount`), calls an action creator to dispatch the action to the reducer, thus changing the state in the Redux store and triggering a re-render on the DOM to reflect the data. 
* Note that with the use of Thunk middleware, we can return a function with the action creator and the dispatch will wait for that function to fully finish before dispatching the action to our reducer, which then makes the change to state in our store. 
* This is helpful because fetch requests are asynchronous and are not executed immediately when called. They will however return a promise used as a placeholder in code to allow the rest of the function to execute without holding up delays. Once the fetch request finishes loading, it will return the data in place of the promise. 
* A great analogy is to think of a promise like a receipt at a fast food restaurant - the receipt guarantees you a response (food) at a later time (when it's finished cooking). 

### Presentational Components
* These components are mainly responsible for rendering content. 
* Since these components are stateless, they are not concerned about changes in data. 
* They can however receive props to render, but should not invoke any changes to state.
* Examples in my project: NavBar.js, Home.js, TripDetails.js, AttendeeDetails.js, ActivityDetails.js

### RESTful Routing with React Router
* I wrapped the App.js component with `Router` tags to declare where to use React Router - the whole application.
* Elsewhere, I used `Route` tags to render a component when the URL matches a declared path.
* Some components have multiple `Route` tags, which are themselves wrapped in a `Switch` tag, meaning that the first child component that matches the path in the URL will be rendered and the rest of the child components will be ignored. 
* [Here](https://www.freecodecamp.org/news/react-router-cheatsheet/#:~:text=The%20switch%20component%20looks%20through,one%20page%20at%20a%20time) is a great cheat sheet on React Router. 

### Difference Between Props and State in React
* To simplify – props are used to pass down data from a parent component to a child component, and do not change when received inside the child component. 
* State on the other hand, is usually local to the component it was initialized in, and updates based on user interaction, say through a controlled form (which is just a form that gets it’s value from state!).
-	To make your state global to your whole project instead of one component, use Redux to store the state, and connect specific components to particular data in that state.  

### A Note about Redux
* While it’s possible that I could have managed local state in each component using only React props for this project, using Redux made things easier and kept my code leaner. 
* Redux emphasizes that we store our data in one place – the state. 
* The Redux flow is that when we are ready to make a change to our state, we dispatch an Action (with a type) to our Reducer, which will update the old state with the new. This change in state, along with React, will trigger a re-render, allowing us to see the state change on the virtual DOM (if we choose to render it as so). 
* While using Redux, it’s important to never mutate state – so use the spread operator or Object.assign() to create a copy of your state with the new/updated values.


### Lastly, a Thank You [Bootstrap](https://react-bootstrap.github.io/getting-started/introduction/)
* If you are in a time crunch to complete a project, Bootstrap is a great tool to help stylize your webpage. 
* It works great with React – I enjoyed creating my Activities components to render as card components on the browser. 
* Note – if you use Bootstrap for React and are styling directly inside the components instead of a CSS file, make sure you do not use any reserved words. 
* For example, do not style your `div` with `class` – use `className` instead. Same applies to `for` - use `htmlFor` instead.  
* ‘Class’ and ‘for’ are reserved words in JavaScript. And after all, React components are just JavaScript classes.







