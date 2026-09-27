<h1>
  <span class="headline">FastAPI Serializers and Controllers</span>
  <span class="subhead">Connecting Controllers to Database</span>
</h1>

**Learning objective:** By the end of this lesson, students will be able to implement real CRUD operations by adding serializers and connecting the FastAPI controllers to a database.

## Connecting our API

In this section, we will walk through the steps required to add serializers and connect our API to a database.

1. Import all necessary models and schemas into each controller module.
2. Use the correct **ModelSchema** for each CRUD route.
3. Add error handling to each controller function.
4. Test each endpoint (index, detail, create, delete, and update) using FastAPI’s built-in documentation.

## Getting started

We will focus on the `teas` controller, which is located in the `controllers/teas.py` file.

### 1. Import necessary modules

First, let's ensure we have all the required imports at the top of the file. Edit `controllers/teas.py` to look like this:

```py

# controllers/teas.py

from fastapi import APIRouter, Depends, HTTPException
from sqlalchemy.orm import Session
from models.tea import TeaModel
from serializers.tea import TeaSchema
from typing import List
from database import get_db

router = APIRouter()
```

## Updating the Tea controller

In this section, we will implement CRUD operations by refactoring the routes for handling `GET`, `POST`, `PUT`, `PATCH`, and `DELETE` HTTP methods.

| Method   | CRUD Functionality | Database Action      |
| -------- | ------------------ | -------------------- |
| `GET`    | Read               | Retrieve data        |
| `POST`   | Create             | Add new data         |
| `PUT`    | Update             | Modify existing data |
| `PATCH`  | Update             | Modify existing data |
| `DELETE` | Delete             | Remove data          |

### 1. `Read/GET` All Teas Route

Let’s refactor the route to get all the teas. This is the `GET /teas` endpoint:

```py
@router.get("/teas", response_model=List[TeaSchema])
def get_teas(db: Session = Depends(get_db)):
    teas = db.query(TeaModel).all()
    return teas
```

- `GET /teas` retrieves a list of all teas from the database.
- We use the `TeaSchema` Pydantic model to serialize the database results into a format that can be sent as JSON.

### 2. `Read/GET` Single Tea Route

Next, let’s refactor the route to get a single tea. This is the `GET /teas/{tea_id}` endpoint:

```py
@router.get("/teas/{tea_id}", response_model=TeaSchema)
def get_single_tea(tea_id: int, db: Session = Depends(get_db)):
    tea = db.query(TeaModel).filter(TeaModel.id == tea_id).first()
    if not tea:
        raise HTTPException(status_code=404, detail="Tea not found")
    return tea
```

- `GET /teas/{tea_id}` retrieves a single tea by its `id`.
- If no tea with the given `tea_id` is found, we raise a 404 error with a message saying `"Tea not found"`.

### 3. Create/POST Route

Now, let’s refactor the `POST /teas` route to create a new tea in the database:

```py
@router.post("/teas", response_model=TeaSchema)
def create_tea(tea: CreateTeaSchema, db: Session = Depends(get_db)):
    new_tea = TeaModel(**tea.dict()) # Convert Pydantic model to SQLAlchemy model
    db.add(new_tea)
    db.commit() # Save to database
    db.refresh(new_tea) # Refresh to get the updated data (including auto-generated fields)
    return new_tea
```

- `POST /teas` creates a new tea entry.
- The `tea.dict()` converts the Pydantic model into a format that can be used to create a SQLAlchemy model.
- The `**` syntax unpacks the dictionary returned by `tea.dict()` into keyword arguments when passing it to the TeaModel constructor

### 4. Update/PUT Route

Let’s now refactor the `PUT /teas/{tea_id}` route to update an existing tea:

```py
@router.put("/teas/{tea_id}", response_model=TeaSchema)
def update_tea(tea_id: int, tea: UpdateTeaSchema, db: Session = Depends(get_db)):
    db_tea = db.query(TeaModel).filter(TeaModel.id == tea_id).first()
    if not db_tea:
        raise HTTPException(status_code=404, detail="Tea not found")

    tea_data = tea.dict(exclude_unset=True)  # Only update the fields provided
    for key, value in tea_data.items():
        setattr(db_tea, key, value)

    db.commit()  # Save changes
    db.refresh(db_tea)  # Refresh to get updated data
    return db_tea
```

- `PUT /teas/{tea_id}` updates an existing tea.
- We use the `tea.dict(exclude_unset=True)` method to only update the fields that have been modified, leaving other fields unchanged.

---

### 5. DELETE Route

Finally, let’s refactor the `DELETE /teas/{tea_id}` route to delete a tea:

```py
@router.delete("/teas/{tea_id}")
def delete_tea(tea_id: int, db: Session = Depends(get_db)):
    db_tea = db.query(TeaModel).filter(TeaModel.id == tea_id).first()
    if not db_tea:
        raise HTTPException(status_code=404, detail="Tea not found")

    db.delete(db_tea)  # Remove from database
    db.commit()  # Save changes
    return {"message": f"Tea with ID {tea_id} has been deleted"}
```
There is one small problem. We need to tell the database what to do with the comments that belong to this tea. Pydantic to the rescue!


Right now we have this situation:

A Tea can have many Comments

Each Comment belongs to one Tea

The database does NOT allow you to delete a Tea if comments still point to it, because that would leave “broken” records.

So we tell the system:

“When a Tea is deleted, automatically delete all its Comments too.”

This is called cascade delete.

We just need to add 3 lines of code:

replace the relationship line with the one below:
```py
# TeaModel
comments = relationship('CommentModel', back_populates='tea',  cascade="all, delete-orphan")

```

update the relationship and the tea_id field on the CommentModel
```py
# CommentModel
tea_id = Column(Integer, ForeignKey("teas.id", ondelete="CASCADE"), nullable=False)
tea = relationship("TeaModel", back_populates="comments", passive_deletes=True)

```

- `DELETE /teas/{tea_id}` removes a tea from the database.
- If the tea is not found, we raise a 404 error with the message `"Tea not found"`.

## Verify All Endpoints

To test the API routes, run your FastAPI application with:

```bash
pipenv run uvicorn main:app --reload
```

Now you can test each endpoint using FastAPI's built-in documentation.

> Navigate to FastAPI Documentation: Open [`http://localhost:8000/docs`](http://localhost:8000/docs) in your browser.
