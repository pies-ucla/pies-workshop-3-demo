# Step 2: Coding a simple Django Rest Framework server
Enough with the definitions and examples!! Let's actually code our backend server!
## Coding myfirstapp
### Coding a Model
- Within your IDE (VSCode), click on the file `myfirstapp/models.py`.
- We are going to create a model called `User` that will store a first name, last name, and email. Each field will have a max character limit of 50.
- The completed `models.py` file should look like:
```py
from django.db import models

# Create your models here.
class User(models.Model):
    first_name = models.CharField(max_length=50)
    last_name = models.CharField(max_length=50)
    email = models.EmailField(max_length=50)
```
### Coding a Serializer
- Within your IDE (VSCode), click on the file `myfirstapp/serializers.py`.
- We are going to create a Serializer for the User called `UserSerializer`. The Serializer will take in the User model and the fields you want to make "public". We want to publicize the User's first name, last name, and email.
- The completed `serializer.py` file should look like:
```py
from rest_framework import serializers
from .models import User

class UserSerializer(serializers.ModelSerializer):
    class Meta:
        model = User
        fields = ['first_name', 'last_name', 'email']
```
### Coding a View
- Within your IDE (VSCode), click on the file `myfirstapp/views.py`.
    - Delete all code within the `views.py` before continuing; we won't need this.
- We want to code 3 views here to create a user, get a user by their ID, and get all users:
    - The `create_user` view will allow us to create a User from a first name, last name, and email. This view can be targeted/invoked with the POST method.
        ```py
        @api_view(['POST'])
        def create_user(request):
            serializer = UserSerializer(data=request.data)
            if serializer.is_valid():
                serializer.save()
                return Response(serializer.data, status=status.HTTP_201_CREATED)
            else:
                return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)
        ```
        - The UserSerializer is used to convert the JSON data that the view is given into an actual Python User object to save to our database.
        - If the serializer was given valid info, then we can save it in our database and return an HTTP response code of 201. If the serializer was given bad info, we return an HTTP response code of 400 and do nothing else.
    - The `get_user` view will allow us to get a User from their ID. This view can be targeted/invoked with the GET method.
        ```py
        @api_view(['GET'])
        def get_user(request, id):
            try:
                user = User.objects.get(pk=id)
                serializer = UserSerializer(user)
                return Response(serializer.data)
            except User.DoesNotExist:
                return Response(status=status.HTTP_404_NOT_FOUND)
        ```
        - What the heck is try and catch??
        - If there is no user corresponding to the ID passed in, the User.objects.get() method will throw an error called "exception". 
        - You can handle an exception in Python by wrapping any code that will cause an exception in a "try" block, and then handle that exception if it occurs in an "except block".
    - The `get_users` view will allow us to get all Users within the database. This view can be targeted/invoked with the GET method.
        ```py
        @api_view(['GET'])
        def get_users(request):
            users = User.objects.all()
            serializer = UserSerializer(users, many=True)
            return Response(serializer.data)
        ```
- The final `myfirstapp/views.py` file should look like:
    ```py
    from rest_framework.decorators import api_view
    from rest_framework.response import Response
    from rest_framework import status
    from .models import User
    from .serializer import UserSerializer

    @api_view(['POST'])
    def create_user(request):
        serializer = UserSerializer(data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data, status=status.HTTP_201_CREATED)
        else:
            return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)
    
    @api_view(['GET'])
    def get_user(request, id):
        try:
            user = User.objects.get(pk=id)
            serializer = UserSerializer(user)
            return Response(serializer.data)
        except User.DoesNotExist:
            return Response(status=status.HTTP_404_NOT_FOUND)
        
    @api_view(['GET'])
    def get_users(request):
        users = User.objects.all()
        serializer = UserSerializer(users, many=True)
        return Response(serializer.data)
    ```
- We're almost done with `myfirstapp`! Now we have to hook these views up to URLs so anyone on the Internet can access them.
### Assigning a View to a URL
- Within your IDE (VSCode), click on the file `myfirstapp/urls.py`.
    - NOTE: the `demo` folder also contains a `urls.py` file. This file is for hooking up our app's URLs to Django, so we shouldn't touch it yet! Make sure to click on the right `urls.py`.
- We want to hook each view up to a specific URL. 
    - The `create_user` view should be invoked with the `users/create/` URL. 
    - The `get_users` view should be invoked with the `users/` URL.
    - The `get_user` view should be invoked with a special URL; we want the URL to contain the ID of the user we want to get (e.g. `users/1` gets user `1`). We can add a URL parameter, which is basically a variable within the URL. We can do this with the URL: `users/<int:id>`
- The final `myfirstapp/urls.py` file should look like:
    ```py
    from django.urls import path
    from .views import create_user, get_user, get_users

    urlpatterns = [
        path('users/', get_users, name='get_users'),
        path('users/<int:id>/', get_user, name='get_user'),
        path('users/create/', create_user, name='create_user')
    ]
    ```
- After finishing up with the URLs, myfirstapp has been completed!
## Hooking up myfirstapp with Django
### Settings and the Project URL
- myfirstapp is an independent module of a Django project; Django isn't automatically aware of this app yet. To hook up this app with Django, we can add it in the list of `INSTALLED_APPS`. Go to `demo/settings.py` and append `"myfirstapp"` to the list of `INSTALLED_APPS`. Now, your `INSTALLED_APPS` should look like:
    ```py
    INSTALLED_APPS = [
        "django.contrib.admin",
        "django.contrib.auth",
        "django.contrib.contenttypes",
        "django.contrib.sessions",
        "django.contrib.messages",
        "django.contrib.staticfiles",
        "rest_framework",
        "myfirstapp",
    ]
    ```
- To hook up our app URLs to the Django server's URLs, we can visit the `demo/urls.py` file. Add myfirstapp's URLs to this file like so:
    ```py
    from django.contrib import admin
    from django.urls import path, include

    urlpatterns = [
        path("admin/", admin.site.urls),
        path("app/", include('myfirstapp.urls'))
    ]
    ```
- myfirstapp's views are now visible on the Django server. They can be invoked in the following way:
    - `create_user`: `POST {backend server}/app/users/create/`
        - This endpoint requires that you include JSON in the HTTP body containing the `first_name`, `last_name`, and `email` of the User you want to create.
    - `get_user`: `GET {backend server}/app/users/<id>`
        - For example, `GET {backend server}/app/users/1/` to get user `1`.
    - `get_users`: `GET {backend server}/app/users/`
### Migrations
- Before we can run the server, we must perform our migrations; we must update Django's server with our models.
- To create a migration, you can run the following command in the root folder:
    ```console
    python3 manage.py makemigrations
    ```
- After running this command, you should see this in your terminal:
    ```console
    Migrations for 'myfirstapp':
    myfirstapp/migrations/0001_initial.py
        + Create model User
    ```
    - This indicates that Django has created a migration for the User model; this is a list of SQL statements to automatically set up the database schema in Django's database. However, the database hasn't actually been updated yet; this just shows us what is going to happen when we migrate.
- We can perform the migration by running the following command in the root folder:
    ```console
    python3 manage.py migrate
    ```
- After running this command, you should see this in your terminal:
    ```console
    Operations to perform:
    Apply all migrations: admin, auth, contenttypes, myfirstapp, sessions
    Running migrations:
    Applying contenttypes.0001_initial... OK
    Applying auth.0001_initial... OK
    Applying admin.0001_initial... OK
    Applying admin.0002_logentry_remove_auto_add... OK
    Applying admin.0003_logentry_add_action_flag_choices... OK
    Applying contenttypes.0002_remove_content_type_name... OK
    Applying auth.0002_alter_permission_name_max_length... OK
    Applying auth.0003_alter_user_email_max_length... OK
    Applying auth.0004_alter_user_username_opts... OK
    Applying auth.0005_alter_user_last_login_null... OK
    Applying auth.0006_require_contenttypes_0002... OK
    Applying auth.0007_alter_validators_add_error_messages... OK
    Applying auth.0008_alter_user_username_max_length... OK
    Applying auth.0009_alter_user_last_name_max_length... OK
    Applying auth.0010_alter_group_name_max_length... OK
    Applying auth.0011_update_proxy_permissions... OK
    Applying auth.0012_alter_user_first_name_max_length... OK
    Applying myfirstapp.0001_initial... OK
    Applying sessions.0001_initial... OK
    ```
## Moving On
- Congratulations! We have completed our very first Django server! If you want to test it out, move on to Step 3.