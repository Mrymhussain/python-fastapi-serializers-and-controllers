<h1>
  <span class="headline">FastAPI Serializers and Controllers</span>
  <span class="subhead">Concepts</span>
</h1>

**Learning objective:** By the end of this lesson, students will be able to explain what serialization is and why it is important in a FastAPI application.

## What is Serialization?

Serialization is the process of converting complex data (such as Python objects) into a format that can be easily stored, shared, or sent over a network.

### Why we need it in our application

FastAPI applications often need to send and receive data in **JSON format** when communicating with clients (like web browsers or mobile apps).  
However, databases store data in a different format, such as **SQLAlchemy models**.

To send this data over an API, we need to _convert_ or **serialize** it into JSON.  
Similarly, when receiving data from a client, we must _convert_ or **deserialize** JSON back into Python objects.

FastAPI makes serialization easy by using **[Pydantic](https://docs.pydantic.dev/latest/)**, a library that helps define and validate data.

## Pydantic

Pydantic provides three important features:

1. **Data Validation**

   - Before saving data to the database, Pydantic checks if it matches the expected structure.
   - For example, if a user submits an email address, Pydantic ensures it is in the correct format.

2. **Serialization**

   - Pydantic converts Python objects (like database models) into JSON so they can be sent over an API.

3. **Typing and Data Structure**

   - Pydantic allows us to **define clear data models** and ensures that API responses have the correct data types.

## Up Next: serializing our SQLAlchemy models

In the next section, we’ll:

- Review our database models created with SQLAlchemy.
- Create Pydantic schemas to serialize the data.
- Use FastAPI to return JSON responses from the database.
