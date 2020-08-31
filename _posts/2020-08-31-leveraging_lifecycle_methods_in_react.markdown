---
layout: post
title:      "Leveraging Lifecycle Methods in React"
date:       2020-08-31 18:35:38 +0000
permalink:  leveraging_lifecycle_methods_in_react
---


The final capstone project for Flatiron School that I built was a hybrid idea of a social media application that merged the ideas of twitter and snapchat. The high level overview of the application is that there is a user model and a post model which are referred to as "blerbs". As a user, there is the ability to sign up for an account and log in to an existing account. The user can then view all of the current blerbs on the feed page and write blerbs of their own. Now the usage of the word current in the previous sentence is a bit different than that of say twitter and how twitter shows you the latest tweets upon logging in to your account. In this application, blerbs only appear on the screen for a certain amount of time, similar to snapchat. After the set amount of time has passed the application updates and only shows any blerbs that are still within the allotted time. 

Let's look at a few bits of code to see how this task gets accomplished within the application. 

```
class Blerb < ApplicationRecord
  belongs_to :user

  validates :content, length: { maximum: 120 }
  validates :content, presence: true

  scope :not_outdated, -> { where("created_at >= ?", 60.seconds.ago) }

end
```

Here is the Blerb model code from the Rails API, which has a scope method set to query the database for all Blerbs that were created a minute ago or less. This bit of code is a little tricky because at first glance it seems as though we would get records that are a minute or older however this is not the case. We are getting records whose `created_at` property is more recent than what the time was one minute ago from the current time.

Moving up the chain, let's look at the index action on the Blerbs controller:

```
class BlerbsController < ApplicationController 

    def index
        blerbs = Blerb.not_outdated

        render json: BlerbSerializer.new(blerbs).serialized_json
    end
```

Here we are simply sending the scope method message to the Blerbs class and saving the results to a variable which gets passed onward to an instance of our Blerbs serializer. 

Now we move to our React frontend:

```
// src/actions/index.js 

export const getBlerbs = () => {
    return (dispatch) => {
        return fetch("http://localhost:3000/blerbs")
            .then(res => res.json())
            .then(blerbs => dispatch({ type: "GET_BLERBS", payload: blerbs.data }))
    };
};
```

This is the code for an asyncronous action creator that will make a post fetch request to the Rails API and get the array of Blerbs which the scope method returns. Now as a quick aside, when attempting to make an asyncronous request in this way, it is necessary to employ middleware such as Redux Thunk. Thunk will allow our action creators to return functions. Thunk will step between our dispatch and reducers and look to see if what was returned from the action creator was a function or an action. If a function was returned then Thunk will call the function, in the code above the function with the asyncronous requset is what is called by Thunk. Once the promise from the fetch resolves an action will be dispatched and again Thunk will step in. This time however we will be returning an action so Thunk will simply pass it on to the Blerbs reducer, which looks like this:

```
export default (state = [], action) => {
    switch (action.type) {
        case "GET_BLERBS":
            return action.payload;
        case "ADD_BLERB":
            return [ ...state, action.payload ]
        default:
            return state;
    };
};
```

This will cause our this piece of state in our Redux store to update and any components using it to rerender. 

Now let's take a look at the piece that brings this whole process home and achieves the goal of keeping our feed current.

```
import React from 'react';
import { connect } from 'react-redux';
import { getBlerbs } from '../actions';
import Blerb from './Blerb';
import BlerbWriter from './BlerbWriter';

class Feed extends React.Component {
    componentDidMount() {
        this.props.getBlerbs();

        this.interval = setInterval(this.props.getBlerbs, 60000);
    };

    componentWillUnmount() {
        clearInterval(this.interval);
    };

    render() {
        return (
            <div>
                <div className="ui segment" style={{ marginLeft: "300px", marginTop: "25px", width: "850px" }}>
                    <BlerbWriter />
                </div>
                <div className="ui container" style={{ width: "700px", marginTop: "50px" }}>
                    <div className="ui relaxed divided list">
                        <Blerb />
                    </div>
                </div>
            </div>
        )
    }
};

const mapStateToProps = ({ blerbs }) => {
    return { blerbs };
};

export default connect(mapStateToProps, { getBlerbs })(Feed);
```

Above is the code for the feed component which shows our list of Blerbs. Let's walk through how we go about implementing everything we have setup up to this point. 

First we import our action creator that we wrote to fetch our list of Blerbs, then at the bottom of the file we will pass in the action creator as our second argument to the connect function that wraps this component. Now that those to things are set in place, we need to decide when we want this action creator to be called. Well a general approach to this task is to call such actions of this nature as soon as the component is rendered. So with this in mind it would seem that the `componentDidMount` lifecycle method would be a prime candidate to kick off such behavior. 

```
componentDidMount() {
        this.props.getBlerbs();
    };
```

This works but it only works this initial time. We want a way to trigger this function to be called every minute to try to align with our scope method from the Rails API. So with that thought in place, let's think about what we have currently for a moment. We have a function which will make our asycronous request, dispatch an action and allow our Redux store to be updated and we know that we want to call it every minute. Seems like utilizing the `setInterval()` function could be useful here, so let's add it in.

```
componentDidMount() {
        this.props.getBlerbs();

        this.interval = setInterval(this.props.getBlerbs, 60000);
    };
```

This looks great and works but there is potential for an issue here. If we don't clean up the interval before the component rerenders and mounts again we could end up in a real mess of having multiple intervals being set every time the component mounts, so lets fix that. Since the interval was saved as a property on this component as seen here:
```
 this.interval = setInterval(this.props.getBlerbs, 60000);
```

We now have reference to the interval and can clear it before the component is rerendered.

```
componentWillUnmount() {
        clearInterval(this.interval);
    };
```


