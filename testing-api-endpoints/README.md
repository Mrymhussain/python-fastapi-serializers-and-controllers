<h1>
  <span class="headline">FastAPI Serializers and Controllers</span>
  <span class="subhead">Testing API Endpoints</span>
</h1>

**Learning objective:** By the end of this lesson, students will be able to test their completed API and performing real CRUD operations from the browser interface.

## Testing the Tea API Endpoints

Test each API route to ensure that your application is working as expected. Below is guide with some sample data you can use to test:

- **List all teas** (`GET /teas`):  
   This endpoint will return a list of all teas in the database.

  **No parameters required**. Just click "Execute" to retrieve the list.

  > Save a couple of ID's from the list to test the next few routes

- **Get a single tea** (`GET /teas/{tea_id}`):  
   This endpoint returns details for a single tea. You need to provide a valid `tea_id` as a parameter.

- **Create a new tea** (`POST /teas`):  
   This endpoint allows you to create a new tea by sending data in the request body.

  **Sample request body:**

  ```json
  {
    "name": "Lapsang souchong",
    "in_stock": true,
    "rating": 4,
    "comments": []
  }
  ```

  This will create a new tea with the specified information.

- **Update a tea** (`PUT /teas/{tea_id}`):  
   This endpoint lets you update an existing tea. Provide the `tea_id` and the new data to update the tea.

  **Example:**  
  We can change the `in_stock` value of an existing tea to `false`:

  ```json
  {
    "name": "Green Tea",
    "in_stock": false,
    "rating": 4,
    "comments": []
  }
  ```

- **Delete a tea** (`DELETE /teas/{tea_id}`):  
   This endpoint will delete a tea from the database. You need to provide a valid `tea_id` as a parameter.
