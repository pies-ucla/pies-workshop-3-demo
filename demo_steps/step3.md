# Step 3: Testing our Django Server
## Starting the Server
- To start our newly created Django server, we can run the following command in our root folder:
    ```py
    python3 manage.py runserver
    ```
- The server will be running on the domain and port `http://localhost:8000`.
### Side Note about Web/Application Servers
- This looks kind of familiar with other websites like `https://google.com` and `https://ucla.edu`! What does it mean?
- Underneath the hood, these domain names are just aliases for "IP addresses", which are (put simply) addresses that identify a unique computer on the Internet. For example, (one of) `google.com`'s web servers has the IP address 142.250.68.46. 
    - The `localhost` domain is an alias for the IP address 127.0.0.1, which points specifically to your computer.
- Okay, so we identified the computer we should be connecting with to send and receive content. But how do we actually contact the server program within the computer?
    - You can have a server listening to incoming traffic by setting a server program to listen to a specific port.
    - By default, a Django application server listens on port 8000.
- By specifying the protocol (http), domain name (localhost), and port (8000), we are able to communicate with a web/application server via a REST API.
## Using Postman
- Postman is an app that allows you to easily utilize APIs. We can use Postman to communicate with our REST API on our Django server.
    - You can download Postman [here](https://www.postman.com/downloads/).
- For API endpoint / view we want to test, we can create a request and type in the URL corresponding to that view. Make sure to specify the correct REST method on the left box of the URL field.
- Examples:
    - To create a user (create_user view):
        - `POST http://127.0.0.1:8000/app/users/create/`
        - Body
            ```JSON
            {
                "first_name": "Jared",
                "last_name": "Velasquez",
                "email": "jaredvel25@ucla.edu"
            }
            ```
        - This will return an HTTP response with a status code of 201; the user data that was inputted as part of the body will be shown in the response as JSON.
    - To get a user by ID (get_user view):
        - `GET http://127.0.0.1:8000/app/users/1/`
        - If there are users in the database, this will get the first user registered. This will be returned in an HTTP response as JSON.
    - To get all users (get_users view):
        - `GET http://127.0.0.1:8000/app/users/`
        - This will return an HTTP response as a list, where each element of a list contains a User represented as JSON.
- Congratulations! You have successfully built and tested your Django server.
## The End
- Thank you for taking the time and following along with PIES Projects' first backend workshop demo! We really appreciate each and every one of our members for joining us in creating hands-on projects for our club.