# 14-Reverse-Engineering-Code
## Code Walkthrough

### Summary
```
The following application allows a user to signup and login to a member portal where data can be displayed for a logged in user.
```

#### Server.js
```
Server.js initializes the express server on port 8080 and utilizes the following:

* Express-session middleware which enables user session tracking by setting session ID cookies which tie to user session data stored on the backend. Each session ID cookie is signed with the secret set here.
* Urlencoded middleware to process urlencoded requests
* JSON middleware to process JSON datatypes
* Static middleware to serve static content
* Passport initialize middleware to initialize passport for incoming requests, allowing authentication to be applied
* Passport session middleware to alter the req object and change the 'user' value that is currently the session id from the client cookie into a user object
* Express route for API and HTML endpoints
* Sequelize middleware to initialize the data models in the database
```

#### Routes
```
API-ROUTES
----------
API Routes utilizes Sequelize models and Passport middleware to offer API endpoints with authentication enabled

* POST request for Login at /api/login, if the user had valid credentials they are sent to the members page.
* POST request for Signup at /api/signup which creates a User database entry with the email and password entered, then redirects the user to /api/login, or if an error occurs return a HTTP 401 status code and the error on JSON format.
* GET request for Logout at /logout which utilizes the passport logout() method to invalidate the users session.
* GET request for retieving User Data which validates if the user has an existing session and returns the users email address and id. If the user does not have a session a blank JSON entry is returned.

HTML-ROUTES
----------
HTML Routes utilizes Path middleware for retieving realitve paths of our static files and isAuthenticated middleware for validating if a user is logged in

* GET request for / which checks if a user session exists and redirects the client to /members or serves the signup page if there is no user session.
* GET request for /login which checks if a user session exists and redirects the client to /members or serves the login page if there is no user session.
* GET request for /members which checks if a user session exists by using the isAuthenticated session middleware and if a session exists serves the members page or if there is no session the user is redirected to the signup page.
```

#### Public (Static Files)
```
The public folder containts the client-facing static files and scripts.

HTML
* login.html - login page
* members.html - members page
* signup.html - signup page

STYLESHEETS
* style.css - css for all HTML

JS
* login.js - reads the emailInput and passwordInput values in the loginForm and runs the loginUser function which makes a POST API call to /api/login
* members.js - makes a GET API call to /api/user_data then displays the users email under the .member-name class element
* signup.js - reads the emailInput and passwordInput values in the signUpForm and runs the signUpUser function which makes a POST API call to /api/signup
```

#### Models
```
* index.js - The sequelize index.js reads all model files in the directory  
* user.js - The user.js model defines a Users table which containts the email and password columns. This model also uses the bcrypt middleware to hash a users password, it's also used to hash a password to be matched to an existing password hash in the database for comparison.
```

#### Config
```
* config.json - Configures sequelize database connections per environments (dev, test, prod)
* passport.js - Configures passport middleware to use Local Strategy allowing username/email and password for authentication. Sets the username field to an email address, sets up the sequelize query to run on login, determines what happens if there's no matching email addrress or password is incorrect. Also serializes and deserializes to keep authentication state.
* isAuthenticated.js - restricts routes a user can visit if not logged in and if user is not logged in redirect them to /
```


# Future Development
```
AS A Business Owner 

I WANT a user support portal

SO THAT my customers can login after they've made a purchase on my website to recieve instructional documentation on any products they've purchased
```

## Business Context
```
I have a web store that does not offer a customer backend to provide my customers with necessary post-purchase support documentation such as instructions and diagrams
```

## Acceptance Criteria
```
GIVEN a 3rd party API access to a web store user database
WHEN a customer logs in
THEN an API call is made to the web store database to lookup all purchases
THEN the product purchase data is associated with the user in a new database for this application
THEN lookup if a purchased product has associated documentation by making a call to a local product_doc_db
THEN for all matching products display data to the customer
```