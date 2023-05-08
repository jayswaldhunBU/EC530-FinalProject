## Build a Recipe App - FoodMood 


## Project Summary
FoodMood is a recipe website that utilizes Flask as its backend and incorporates MongoDB for the database, UIKit for the CSS framework, Vanilla JavaScript, and PyGal for charting using Python. The website's main purpose is to provide users with access to various world cuisines, which they can view, search, filter, edit, delete, create, leave comments on, and more.

The website's design was aimed at replicating a real-world website of the same kind, and as a result, it has as many features as possible to enhance user experience and encourage users to explore all the features available (which are documented in the Features section of the README).

During launch, the website boasts of 398 recipes sourced from BBC Good Food, which I included by building a web scraper using Beautiful Soup and another Python script to insert_many into the MongoDB database.

I hope you find this website as enjoyable to use as I found building it!

---

## UX

The entire User Interface revolves around two crucial components of the database, namely the users' profile and the recipe filters. To enable swift navigation of the site's recipes based on significant filters like cuisine or diet, I developed a menu system with a primary dropdown menu. 

The secondary menu caters to user actions, providing quick access to the user's profile page, containing saved recipes, recent comments, and site statistics. Users can also log in and log out of the site effortlessly. 

Regardless of the user's location on the site, they can conveniently move around using only the navbar.

#### Colours, Design & UX  

While browsing through various recipe and food websites, I observed the general layout patterns and color tones used. Based on my findings, I decided to use a CSS framework that could quickly help me create a reusable grid system and slider for showcasing multiple recipes. To achieve this, I created small HTML partials and utilized Jinja to insert the HTML into any page where it was needed. This approach made the site very modular, reducing the code size significantly. 

After researching various CSS frameworks, I chose UIKit due to its modern appearance, ease of implementation, excellent responsiveness, and numerous features such as notifications, off-canvas menu, dropdown menus, sliders, and grid systems. Although the HTML code can appear slightly messy with UIKit's attributes and classes, less code is required compared to Bootstrap, making it the perfect choice for the project.

I designed the logo myself using PIXLR PRO, and upon scrolling down the page, the menu resizes, and a small logo appears. However, when attempting to set the new image's URL using JavaScript and the original logo's URL, I encountered an issue. To resolve the problem, I created an absolute URL using `window.location.protocol + '//' + window.location.host + '/';`. This solution prevented the `src` problem, which would have arisen if I had used `window.location.href`, resulting in the image not being found.

I prioritized responsiveness throughout the development process and continuously tested how the elements responded to different screen widths. My aim was to ensure that the site provided the same user experience on both mobile and desktop devices.

#### User Stories

- As a user, I can easily understand the purpose and nature of the site.
- As a user, I have the ability to browse recipes using various main filters.
- As a user, I can apply multiple queries to filter recipes.
- As a user, I can search for a recipe and apply additional filters to my search results.
- As a user, I can create a user profile and log in or out of the site.
- As a user, I can access the site on a mobile device and have a similar experience to a desktop user.
- As a user, I can use the "remember me" feature to stay logged in even after closing my browser.
- As a user, I have the ability to like, dislike, comment on, create, edit, or delete recipes. If I have created or interacted with a recipe, it will be visible on my profile page.

--- 

## Database

I decided to utilize [BBC Good Food](https://bbcgoodfood.co.uk) as my data source for both recipes and images. The json files necessary for creating a new recipe, user, and menu can be located in `/app/data/schemas`. The web scraper and Python script for adding the recipes to the database can be found in `/app/data/scraper`.

### Designing the Database

Initially, I needed a filter system for the website that could serve as both a menu and recipe filters. So, I selected a few categories from bbcgoodfood and analyzed the recipe information available. After reviewing the source code of each recipe, I noticed some patterns common to all recipes that would help me obtain the necessary data.

To collect a large number of recipes for the site, I used Beautiful Soup to scrape for them. I began with a list of URLs to scrape and then extracted the recipe URLs for each category, usually about 20 for each. With a vast list of categories and recipe URLs, I could start scraping each recipe for its data and saving each recipe as an object in a JSON file, which would serve as my database.

Most of the data was easy to parse as it was embedded in tags with IDs or classes, but there was additional metadata in each page's script tag containing data in key-value pairs. This data contained filter information that could be used for a menu system and applied to each recipe. I used a regular expression to extract the data and converted it back to an object, which would serve as my recipe filters.

To add the database to MongoDB, I wrote a small Python script to insert a first user, menu filters, and recipes. The insert_many method was used for recipes, and insert_one was used for the others. To simulate the number of already liked/disliked recipes, I generated random numbers using random.randint(0, 100), for example.

**Default recipe.json**
```
{
    "filters": {
        "planning": "",
        "cuisine": "",
        "mood": "",
        "diet": "",
        "skill": "",
        "main_ingredient": "",
        "course": [],
        "kw": [],
        "ingred": []
    },
    "details": {
        "title": "",
        "author": "",
        "description": "",
        "prep_time": "",
        "cook_time": "",
        "difficulty": "",
        "serves": ""
    },
    "image": [],
    "ingredients": [],
    "methods": [],
    "nutrition": [
        ["kcal", ""],
        ["fat", ""],
        ["saturates", ""],
        ["carbs", ""],
        ["sugars", ""],
        ["fibre", ""]
    ],
    "users": {
        "likes": 0,
        "dislikes": 0,
        "comments": []
    }
}
```

**Default user.json**

The User object was going to small, and only needed to store a few essential keys. 

```
{
    "username": "",
    "password": "",
    "recipes": [],
    "likes": [],
    "dislikes": [],
    "comments": []
}
```

**Default filters.json**

To create the menu system, I iterated over the recipes db.json file as for each filter check for unique values and added to a manually created dictionary. Some filters contained odd results which I removed after it was created and sorted alphabetically.

```
{
    "cuisine": ["american", "british", "caribbean", "chinese", "french", "greek", "indian", "italian", "japanese", "mediterranean", "mexican", "moroccan", "spanish", "thai", "turkish", "vietnamese"],
    "course": ["afternoon-tea", "breakfast", "brunch", "buffet", "canapes", "cocktails", "condiment", "dessert", "dinner", "drink", "fish-course", "lunch", "main-course", "pasta-course", "side-dish", "snack", "soup-course", "starter", "supper", "treat", "vegetable-course"],
    "planning": ["10-minute-supper", "beginners", "easily-doubled", "easily-halved", "everyday-food", "freezable", "in-season", "make-ahead", "make-it-tonight", "ultimate"],
    "mood": ["budget", "casual", "comfort", "family-friendly", "formal", "hearty", "indulgent", "light", "quick", "romantic"],
    "diet": ["dairy-free", "gluten-free", "healthy", "low-calorie", "low-fat", "low-salt", "vegetarian"],
    "skill": ["easy", "medium", "hard"]
}
```

---

## Features  


**Navigation**

The Navigation is the core of the site, it not only provides the navigation, but also the basis for filtering recipes. When creating the initial database, a seperate collection was created that could be used solely for the menu and filter systems. To make this available to all templates, I used `@app.context_processor ` which opens the filters schema, converts to dictionary and is now available to jinja without needing to be passed directly on each route.  


**Search**

Users can search for a recipe through the search icon in the navigation bar. Upon recieving the search term, a redirect to the filters page takes place. The keywords array is search for a matching keyword and the title is also searched for any matches contained the title.

After a search has taken place, results can be further refined with the filters bar / form.

**URL Slugs**

When passing long recipe titles, the url did not look very friendly. No jinja filters appeared to exist to combat this as I was passing the title in a link, I needed to create a custom filter that replaces spaces and non word chars with something such as _ . I created the function `slug_friendly` which can be used as a jinja filter with `|resub`. To make it available as a filter I could assign the new function with `app.jinja_env.filters['resub'] = slug_friendly`

**Recipe CRUD**

Recipes can only be edited, created and deleted by a logged in user. For the simpllicity of the site, any user can edit or delete any recipe, not just those that they have created themselves. Most recipe crud functions can be found in the `/models/recipes` class. 

1. For the Index page I gather random recipes from the database with `aggregate` and `"$sample"`. 

2. For the Category Pages I find all recipes by cuisine type and return a list limited by 12 and sorted by default by likes. Although the can be sorted differently by using the sort menu on the page. Pagination is implemted by counting the number of documents and dividing by 12 to get the nuumber of pages avaible. Pagination logic is written int he category route. The find function skips a number of document depending on the page * 12 which is passed to the `get_categories` function.

3. Recipes can be created by clicking on the + icon on the screen, this will load a new form that when submitted, is processed and assigned to the recipe schema's dictionary before being inserted to the database. Additional Fields may be added to both ingredients and instructions.

4. Recipes can be edited by clicking the first icon in the icon nav, a request to get the recipe details from the database is sent, before being returned to the jinja template which at that point the values are used as the default values in the input fields. Fields can be removed or added via buttons for the ingredients and instructions fields.

- One issue I had was creating multiple fields with the same name such as ingredients, the solution was to append a number to the end of the name in line with the index number of the loop such as `ingredient-1`. Which when turning back to a list to be stored, I used a regex to find all keys with ingredient, and then put them into a list together.  

5. Recipes can be deleted, a `delete_one` request is sent with the recipe id, and any comments that exist should also be removed from all users profiles.


**Comments**

A user can comment on a recipe, to save page refresh, a event listener captures the submitted form and updates the html before sending a xhr request to the backend to update the users profile and the recipe with the new comment.

Captured is the time, the user, recipe_id, title and the reply. This is converted to a dictionary before being inserted to the database. The new comment contains a link to the recipe, so when on the profile page with comments you can go directly to that recipe.  

**Likes / Dislikes**

Recipes can be liked or disliked by a logged in user, by never liked and disliked at the same time. Nor can you like or dislike an item more than once. For the sake a limiting page refreshes, when a like/dislike is clicked a JavaScript event listener is fired, a xhr request is sent to the `/update_favourites` route with the recipe id and opinion, and the page is updated to reflect the updated like/dislike.

An issue I encountered during this was that the like/dislike icons were made with svg and changing the color of the event.target did not work properly, nor did changing the inner Text as it would remove the icon as well.

The solution was to rely on Event Bubling to captue the click and identify the childNodes, which then I could cahnge the colorr of the entire svg and change the textContent of the count.

**Profile**

The profile page serves as both a hub for a user to see the recipes that they have either created or edited, their recent comments that they have made and some site statistics.

Recent comments are limited to 8 results, for recipes I have limited the initial results to 8, pagination can be achieved with dynamic buttons which will increase or decrease the results by 4 recipes. This particular pagination style was achieved with JavaScript.

On deleting a recipe, making a comment or editing/creating a recipe. The profile page should be immediatly updated.


**Login**    

Login authentication logic is wrote in the `/sign/<url>` route, but the user session is handled by Flask-Login. After confirming a successful username/password login or sign up, the user profile is given to Flask Login which uses mongo to find the user and loads the users object through the `User` Class.

A number of methods are then available to you in python and jinja such as `is_authenticated`, and more importantly the `current_user` object, which I can update by calling a written method `User.getData`. If a profile should be updated, then the `current_user` object should also.

---

## Technologies Used

> *Python, Flask, Jinja, Flask-WTF, SSLify, PyGal, PyMongo, BeautifulSoup, Flask-Login, JavaScipt, JSON, CSS, UIKIT, Git, Heroku*  
 
- Python  
https://docs.python.org/3/  
*time, math, json, random, shuffle, datetime, os*  
Python is used as the backend language for created helper functions, logic and routes  
Various modules were used to assist with parsing, math, json operations and file manipulation


- Flask  
http://flask.pocoo.org/  
*render_template, redirect, url_for, session, request, flash*  
Flask was used as a micro framework for constructing a backend.   
It also provided useful functions that I could use to help with routing, errors and messages.

- Jinja  
http://jinja.pocoo.org/  
Jinja is the templating language used by flask. I made a lot of use of jinjas if statements, loops and ability to construct partial templates.

- Flask-WTForms  
https://flask-wtf.readthedocs.io/en/stable/  
*werkzeug.security, generate_password_hash, check_password_hash , wtforms, wtforms.validators*  
I have used flask-wtf to perform only the sign in / up form creation, partial clien-side validation, and similarly realted - `werkzeug.security` to created and validate hashed passwords.

- SSLify  
[https://github.com/kennethreitz/flask-sslify](https://github.com/kennethreitz/flask-sslify "SSLify")  
SSLify was used to correct and ensure that all requests were carried out over secure `https`, as on signing in, heroku switches to http some some unknown reason.


- JavaScript  
https://www.ecma-international.org/  
I have used vanilla JavaScript to perform some aesthetic changes to the DOM, as per guidelines, no logic has been performed withh JavaScript. Most post request are posted directed to the backend with the exception of commentsand likes / dislikes, Instead I have used an XMLHttpRequest to prevent unnecessary page refresh.
JavaScript was also used to listen for clicks on certian elements and show notifications when the user is not logged in, JavaScript is used by UIKit for some elements, and I have used JavaScript for dynamic creation and deletion of inputs on the edit / create recipe form. 

- JSON  
https://www.ecma-international.org/  
JSON is used as the primary data structure that is used to create a new recipe or user. It initially contained the first database before transfering to mongodb.


- CSS, UIKit  
http://www.w3.org/Style/CSS/members  
https://getuikit.com  
I have mainly used UIKit to style most elements with my own code usually reinforcing and applying sublte overwrites to the default styling being applied. Some styles and media queries are used without uikit and are usually a positional or width percentage based style being applied.

- PyGal    
http://pygal.org/en/stable/
I used PyGal for charting two graphs visible on the users profile page. Although not incredible interactive, the speed and simplicity of creating charts with PyGal mad it an easy choice.

- PyMongo    
https://api.mongodb.com/python/current/    
I used the official PyMongo to work with MongoDB with Python, frequently referencing the API documentation for operations I needed to perform with mongodb.

- Flask-Login    
https://flask-login.readthedocs.io/en/latest/    
Flask-Login was used to handle user session management. It handles logging in, logging out, and remembering your useers sessions even after the browser has been closed.

- BeautifulSoup4    
https://www.crummy.com/software/BeautifulSoup/bs4/doc/         
Beautiful Soup was used to scrape content and parse the return html data from bbcgoodfood. It main use was aiding in creating a large database programatically.



---

## Testing

####  User testing

Written test.py for unit testing.

#### Browser Testing

While my main choice of browser for development is google chrome, I regularly checked the performance on firefox and opera browsers. 
Making use of browser resizing and dev tools device toolbars on each browser to test responsiveness and how how the grid, fonts and media queries were performing and the consistency between each. Adjusting to find a happy medium for all three. 

After I had test deployed the site to heroku I was able to see the real life versions which I was able to test on android phone, amazon fire tablet and different orientations. Unfortunitely, I have no safari devices which I am able test on.

The css framework in use, uikit, and the few media queries I wrote were quite sufficent and only twice did I need to  make a few changes a once to correct any elements out of place. Most of the time it was a case of changing an element to a `display: block` to force it to the next line.

---

## GIT

Git was used on project foundation and throughout at regular intervals during development. Not as many commits were performed as previous projects, but they were performed and pushed when needed or a particular feature has been completed.

I created two seperated branches, one branch was for testing the creation of graphs with pygal, and the other was for testing the app on heroku. This allowed me to keep some different files to my locally ran master branch which I was using with mongodb locally, as well as not needing to change debug variables each time.

I have made use of the .gitignore file to exclude my env vars, pycache, venv and vscode files and folders.

---


#### Setting the project up in a local development environment

Should you wish the run a local copy of this application of your local machine, you will need to follow the instructions listed below:

**Tools you may need:**   

Python 3 installed on your machine https://www.python.org/downloads/  
PIP installed on your machine https://pip.pypa.io/en/stable/installing/  
Git installed on your machine: https://gist.github.com/derhuerst/1b15ff4652a867391f03  
A text editor such as https://code.visualstudio.com/ Visual Studio Code  
An account at  https://www.mongodb.com/cloud/atlas MongoDB Atlas or MongoDB running locally on your machine

**Instructions**

- Obtain a copy of the github repository.
- If possible open a termial session in the unzip folder or `cd` to the correct location
- Next your need to install a virtual environment for the python interpreter, I recommend using pythons built in virtual environment. Enter the command `python -m venv venv` . NOTE: Your python command may differ, such as `python3` or `py`.
- Activate the venv with the command `source venv/bin/activate`, again this may differ depending on your operating system, please check https://docs.python.org/3/library/venv.html for further instructions.
- If needed, Upgrade pip locally by `pip install --upgrade pip`.
- Install all required modules withh the command `pip -r requirements.txt`.
- Its now time to open your text editor and create a file called `.flaskenv`.
- Inside this file you will need to create a SECRET_KEY variable and a MONGO_URI  to link to your own database. Please make sure to call your database 'foodie', with 3 collections called recipes, users, filters. You will find the source for these collections in `/app/data`.
- Lastly, open run.py and on replace line 10 to ` app.run(host=os.getenv('IP'), port=os.getenv('PORT'), debug=True)` and save the file
- You can now run the application with the command `python run.py`
- You can visit the website at `http://localhost:8080`

---