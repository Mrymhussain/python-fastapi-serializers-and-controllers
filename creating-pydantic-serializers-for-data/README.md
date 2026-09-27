<h1>
  <span class="headline">FastAPI Serializers and Controllers</span>
  <span class="subhead">Creating Pydantic Serializers for Data</span>
</h1>

**Learning objective:** By the end of this lesson, students will be able to create Pydantic models for data serialization in FastAPI applications and use these models to normalize and structure API data for easier transmission.

## Pydantic Models

Like SQLAlchemy, Pydantic provides a way to define models which can be used to simplify the serialization process.

First we'll install `pydantic` in our project:

```sh
pipenv install pydantic
```

Next, create a `serializers` directory to organize our Pydantic models:

```sh
mkdir serializers
```

Finally, create serializer files for each of your models:

```bash
touch serializers/tea.py serializers/comment.py
```

In `serializers/tea.py`, define the Pydantic model for the `Tea` model. The `TeaSchema` will be used to serialize the `Tea` database model for easy transmission over the API:

```py
# serializers/tea.py

from pydantic import BaseModel
from typing import Optional, List
from .comment import CommentSchema

class TeaSchema(BaseModel):
  id: Optional[int] = True # This makes sure you don't have to explicitly add an id when sending json data
  name: str
  in_stock: bool
  rating: int
  comments: List[CommentSchema] = []

  class Config:
    orm_mode = True

class CreateTeaSchema(BaseModel):
  name: str
  in_stock: bool
  rating: int

  class Config:
    orm_mode = True

class UpdateTeaSchema(BaseModel):
  name: str
  in_stock: bool
  rating: int

  class Config:
    orm_mode = True
```

> Notice that you can **nest** Pydantic models (like `CommentSchema`), enabling the inclusion of related data (like comments for a tea).

Next, define the `CommentSchema` in `serializers/comment.py`:

```py
# serializers/comment.py

from pydantic import BaseModel

class CommentSchema(BaseModel):
  id: int
  content: str

  class Config:
    orm_mode = True
```

You can now use these Pydantic models to serialize the SQLAlchemy models for transmission over HTTP.
