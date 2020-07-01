---
layout: post
title:      "Hide and Seek and Destroy!!!"
date:       2020-07-01 17:23:45 +0000
permalink:  hide_and_seek_and_destroy
---


The magically feeling of joy one can get when you successfully accomplish the task of being able to have elements appear, disappear and re-appear on a page can be a great experience. But it can come with its share of challenges. In this blog post, we will explore a set of thoughts and approaches to solving such challenges.

To set up some context lets get a zoomed out view of the setup of the application. The basic idea is that we have a main section that displays some bit of data, could be a blog post or a picture, and then users can leave comments on that data. There is a sidebar where users can click to see other occurrences of similar bits of information; we can imagine an instagram-esque application but instead of scrolling through a feed you would click on a label to have the image displayed for you to comment on.

Now that we have a high level overview of the general idea of the application, let's think about what we would like to happen. First let's look at the html file that we have as a foundation to manipulate in the future.

```
<body style="background-color: white;">

    <div class="main" id="main"></div>

    <div class="form-div">
        <form class="form" id="submit">
            <label class="form-heading-label"><u>Leave A Review:</u></label>
            <br>
            <label class="form-name-label">Your Name:</label>
            <br>
            <input type="text" class="form-field-name" id="commentor"></text>
            <br>
            <label class="form-review-label">Review:</label>
            <br>
            <textarea class="form-field-review" id="comment" rows="4" cols="50"></textarea>
            <br>
            <input class="form-submit-button" type="submit">
        </form>
    </div>

    <div class="sidebar" id="sidebar"></div>

</body>
```

We can see that inside of the body tag we have three divs, one with id of "main" which will be the div that we append the main bits of data information to, a second which contains a form for users to use to leave comments and a third for the sidebar. When a user visits the home page, which is just a generic landing page, we don't want a random form hanging out on the page, that would just look odd. So, looking at our html file, we see that the form has a class name of "form", this is great! Lets grab use that to make a css class and add some styling to the form and also set the display property to "none" so that the form will not show up upon landing on the home page.

```
.form {
  text-align: center;
  font-weight: bold;
  font-family: 'Arimo', sans-serif;
  border-style: solid;
  border-color: grey;
  border-width: 1px;
  box-shadow: 10px 10px 5px grey;
  border-radius: 30px;
  margin: 40px 40px 0px 0px;
  padding: 10px 10px 10px 10px;
  display: none;
  background-color: rgb(253, 251, 239);
}
```
We won't be talking about the functionality to bring the form back to being able to be viewed later on, but after reading this post, you should be able to apply the ideas to aid in figuring out a solution to such a task. While it's nice to have a complete walkthrough of everything, personally I believe in the journey of going through a bit of struggle in order to try to solve a problem armed with the knowledge that you do have at your diposal already.

Now that we have the form hidden, the next bit of functionality we would like to have happen is that when a user clicks on a label in the sidebar, we can have a card show up in the main section of the page. With that knowledge in hand, lets first see what we have to work with. Again, looking at our html document we see there is a div with an id of "main", this sounds like a great place to hook into! So we need to setup an event listener for the labels in the sidebar so that when one is clicked, we can trigger further actions that will get us to our end goal of showing the data in the main section. At this point I will describe the application that this post was built around for context so that the following code will hopefully be better understood. The application is an app that allows users to leave reviews about their local police departments. Our event listener looks like the following:

```
function getPdDetailInfo() {
    const pTags = Array.from(document.getElementsByClassName("departments"));   <--- Get all sidebar elements
    
    pTags.map(p => {                                                                                                       <--- Iterate over the collection
        p.addEventListener("click", (e) => {                                                                       <--- Attach event listener to each
            const pdId = e.target.dataset.id;
            const apiShow = new ApiAdapter;
            apiShow.fetchPdDetails(pdId)                                                                            <--- Fetch details about selection
            .then(pd => {
                const cardDiv = document.getElementById("cardDiv");                                <--- Create card

                if (cardDiv) {                                                                           <--- Check if the card div already exists on the page
                    const main = document.getElementById("main");            <--- If so grab the main div

                    main.removeChild(cardDiv);                                              <--- Remove the child of the main div
                }
								
                newPd = new PoliceDepartment(pd)                                    <--- Create a new instance of the police dept class
                newPd.detailView;                                                                 <--- Call the detailView method on the new instace
            })
        })
    })
}
```

So in short, in this function we are checking if a card div is already on the page and if so we remove it before creating another when we call the detailView method on the newly created police department instance. Here is a view of what that method looks like:

```
get detailView() {
        const main = document.getElementById("main");
        const div = createCardDiv();
        const h1 = this.createH1;
        const h2 = this.createH2;
        const h3 = this.createH3;
        const h5 = this.createH5;
        const reviewDiv = this.showReviews; 
        const hArray = [];
        
        hArray.push(h1, h2, h3, h5);
        hArray.forEach(h => div.appendChild(h));
        
        main.appendChild(div);
        main.appendChild(reviewDiv);
        return main;
    }
```

We can see here that we grab the main div, call a function that creates a card div, we then gather all of the information about a certain selection, append that data to the card div and then append the card div as a child to the main div. The result is that everytime a label in the sidebar is clicked the user will only see information about a certain police department in the card div that they clicked on and eveytime they click on a different label, the previous card div will be removed and the new one created and appended to the main div. Thus, our hide and seek and destroy approach is given life!



