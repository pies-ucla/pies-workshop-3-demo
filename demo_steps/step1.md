# Step 1: The Basics
## The Virtual Environment
- The `root directory` is the top-level folder in this project. This is the folder containing the `demo` folder, `manage.py`, `README.md`, etc.
    - The following commands must be run in the root directory to take effect.
- Activate the virtual environment with the command:
    ```console
    source env/bin/activate
    ```
- You can deactivate the virtual environment with the command:
    ```console
    deactivate
    ```
- Once inside the virtual environment, you should see the word `(env)` appear on the left-hand side of each terminal line.
- For now, we will assume that you are inside the virtual environment for the rest of this demo.
- Install the demo's dependencies in the `requirements.txt` file with the command
    ```console
    pip3 install -r requirements.txt
    ```

## The Demo Project
- Opening up the `demo` folder reveals Python files, such as `settings.py`, `urls.py`, etc.
    - The `settings.py` file contains all of the Django configurations for this project. The most important configuration is the `INSTALLED_APPS` variable; this contains all of the apps that you wish to load. We can add our own apps or third-party apps. This will make Django aware of any apps we want to use within the project.
- Add the app `"rest_framework"` to the list of `INSTALLED_APPS` in `demo/settings.py` to make Django aware of the `rest_framework` library.

## Adding your first app
- A Django app is a self-contained module that represents a functionality or a feature in your project.
    - For example, if we were building an e-commerce website, we could have a `user` app, `search` app, `product` app, `shopping_cart` app, and more to represent information about the user, search functionality, product details, and the user's shopping cart, respectively.
- In the root folder, run the following command to create the app `myfirstapp` in the project.
    ```console
    python3 manage.py startapp myfirstapp
    ```

## What's in an app?
- Before we continue, first add two new files called `serializers.py` and `urls.py` in the `myfirstapp` folder.
- There's a lot of new files here! But what do they mean?
### Models
- Django [defines](https://docs.djangoproject.com/en/5.1/topics/db/models/) a `Model` as a source of information about a specific piece of data. It contains the essential fields and behaviors about the data you are storing.
    - In a relational database, each instance of a Model represents a row, while each field represents a column.
    - Each field in a Model will be assigned a field type, like `CharField`, `DateField`, and `IntegerField` to represent text, date-time, and integers, respectively.
    - Models for `myfirstapp` are contained within the `myfirstapp/models.py` folder.
- For example, defining the `Person` model with the `first_name`, `last_name`, and `occupation` fields will create the `Person` table in Django's database. Each `Person` you add to this table will create a new row, and each row has a `first_name`, `last_name`, and `occupation` column.
- Example:
    ```py
    from django.db import models

    class Musician(models.Model):
        first_name = models.CharField(max_length=50)
        last_name = models.CharField(max_length=50)
        instrument = models.CharField(max_length=100)


    class Album(models.Model):
        artist = models.ForeignKey(Musician, on_delete=models.CASCADE)
        name = models.CharField(max_length=100)
        release_date = models.DateField()
        num_stars = models.IntegerField()
    ```
### Serializers
- Django Rest Framework [defines](https://www.django-rest-framework.org/api-guide/serializers/) a serializer as a class that is able convert Model objects into JSON (a format very easily usable by the frontend) and vice versa.
    - Serializers are contained within the `myfirstapp/serializers.py` file. If you do not see this file, create an empty file within the `myfirstapp` folder and name it `serializers.py`.
- Example:
    ```py
    from rest_framework import serializers
    from .models import Musician

    class MusicianSerializer(serializers.ModelSerializer):
        class Meta:
            model = Musician
            # Define what fields to show
            fields = ["first_name", "last_name", "musician"]

    # Musician in JSON:
    # {
    #   "first_name": "Jared",
    #   "last_name": "Velasquez",
    #   "instrument": "guitar"
    # }
    ```

### Views
- Django [defines](https://docs.djangoproject.com/en/5.1/topics/http/views/) a `View` as a function that takes a request and returns a response.
    - Views are contained within the `myfirstapp/views.py` file.
- Views are able to respond to HTTP requests based on the REST method. You can code logic to handle and format responses
    - For example, you can check if user is authenticated and has permissions, or gather up all the data you want to send back to the user.
- Example:
    ```py
    from rest_framework.decorators import api_view
    from rest_framework.response import Response

    @api_view(['GET', 'POST'])
    def hello_world(request):
        if request.method == 'POST':
            return Response({"message": "Got some data!", "data": request.data})
        else:
            return Response({"message": "Hello, world!"})

    @api_view(['POST'])
    def create_musician(request):
        # Serialize the Musician data from the request
        serializer = MusicianSerializer(data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data, status=status.HTTP_201_CREATED)
        else:
            return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)
    ```
### URLs
- A Uniform Resource Locator (URL) is a web address that identifies a resource on the Internet.
    - You most commonly use a URL when typing in web pages (e.g. [google.com](google.com), (my.ucla.edu)[my.ucla.edu], (bruinlearn.ucla.edu)[bruinlearn.ucla.edu]).
- You can perform actions, request content, update content, etc. on a server by specifying the URL and the action (REST method) you want to take.
- We must assign URLs to our functions in `views.py` so they can be accessible by the outside world.
    - URLs are contained within the `myfirstapp/urls.py` file. If you do not see this file, create an empty file within the `myfirstapp` folder and name it `urls.py`.
- Example:
    ```py
    from django.urls import path
    from .views import hello_world, create_musician
    
    urlpatterns = [
        path('hello-world', get_user, name='get_user'),
        path('musicians/create/', create_musician, name='create_musician')
    ]
    ```
### Migrations
- Django [defines](https://docs.djangoproject.com/en/5.1/topics/migrations/) a `Migration` as a set of SQL statements that will automatically update a database schema.
    - After defining a Model, Django will use migrations to set up the tables, rows, columns, etc; you won't have to worry about setting it up yourself at all.
    - Migrations are contained within the `myfirstapp/migrations` folder.
    - Example:
        - Performing a migration for the `Musician` model will create a `Musician` table within Django's database. This table will have four columns: the primary key (id of a musician), first_name, last_name, and instrument.
- To make a migration, first connect your `app` to the overall Django project by adding it as a field to `INSTALLED_APPS` within `demo/settings.py`.
## Moving On
- Now that we've defined all the concepts that make up an app, let's move on to Step 2 to begin coding.