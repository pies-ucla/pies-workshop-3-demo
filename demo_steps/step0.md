# Step 0: How was this project set up?

If you are interested in how this project was set up, here are the commands to do it! If you aren't, you can move on to `step1.md`.

## Creating a Virtual Environment
### What is a virtual environment?
- When creating projects, we don't want to reinvent the wheel! We want to use code that other (super talented) people have developed. We can use `pip`, a package manager for Python to download packages (a bundle of code) made by other people.
- A virtual environment is an isolated space to work on Python projects. We can install our packages here, and they won't conflict with any other packages installed on your computer!
### Why do we need a virtual environment?
- Sometimes, code depends on different versions of the same package. Maybe my project requires 'hello v1' while your project requires 'hello v1.1', and using different versions of the 'hello' package will break both projects.
- A virtual environment ensures that you can install specific versions of projects without conflicting with different versions of the same packages for other projects.

## Creating the Demo
### Dependencies
- If you don't already have it on your computer, download `pip` by running this command:

    Linux/Mac
    ```console
    python get-pip.py
    ```

    Windows
    ```console
    py get-pip.py
    ```
- Install the `djangorestframework` package:
    ```console
    pip3 install djangorestframework
    ```
    - This will install `djangorestframework` and its dependencies, like `django`, `asgiref`, etc. so you can use it out-of-the-box!
### Creating a Django Rest Framework (DRF) Project
- We can set up a new DRF project with:

    ```console
    django-admin startproject demo .
    ```