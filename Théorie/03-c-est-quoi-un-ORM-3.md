*exemple complet et minimal** pour transformer le modèle précédent (`Author`, `Book`, relation 1-N) en **API REST avec FastAPI** et **SQLAlchemy**, incluant :

* Création des modèles
* Base de données SQLite
* Endpoints pour :

  * créer un auteur
  * créer un livre pour un auteur
  * lister tous les livres avec leur auteur



## <h1 id="1-structure-fastapi">1. Structure du projet</h1>

```
fastapi-books/
├── main.py
├── models.py
├── database.py
└── requirements.txt
```

---

## <h2 id="2-dependances">2. Fichier `requirements.txt`</h2>

```txt
fastapi
uvicorn
sqlalchemy
```

---

## <h2 id="3-fichier-database-py">3. Fichier `database.py`</h2>

```python
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker, declarative_base

DATABASE_URL = "sqlite:///./livres.db"

engine = create_engine(DATABASE_URL, connect_args={"check_same_thread": False})
SessionLocal = sessionmaker(bind=engine)
Base = declarative_base()
```

---

## <h2 id="4-fichier-models-py">4. Fichier `models.py`</h2>

```python
from sqlalchemy import Column, Integer, String, ForeignKey
from sqlalchemy.orm import relationship
from database import Base

class Author(Base):
    __tablename__ = "authors"
    id = Column(Integer, primary_key=True, index=True)
    name = Column(String, nullable=False)

    books = relationship("Book", back_populates="author")

class Book(Base):
    __tablename__ = "books"
    id = Column(Integer, primary_key=True, index=True)
    title = Column(String, nullable=False)
    author_id = Column(Integer, ForeignKey("authors.id"))

    author = relationship("Author", back_populates="books")
```

---

## <h2 id="5-fichier-main-py">5. Fichier `main.py`</h2>

```python
from fastapi import FastAPI, Depends, HTTPException
from sqlalchemy.orm import Session
from database import SessionLocal, engine, Base
from models import Author, Book

Base.metadata.create_all(bind=engine)

app = FastAPI()

# Dépendance pour la session
def get_db():
    db = SessionLocal()
    try:
        yield db
    finally:
        db.close()

@app.post("/authors/")
def create_author(name: str, db: Session = Depends(get_db)):
    author = Author(name=name)
    db.add(author)
    db.commit()
    db.refresh(author)
    return author

@app.post("/books/")
def create_book(title: str, author_id: int, db: Session = Depends(get_db)):
    author = db.query(Author).filter(Author.id == author_id).first()
    if not author:
        raise HTTPException(status_code=404, detail="Auteur non trouvé")
    book = Book(title=title, author_id=author_id)
    db.add(book)
    db.commit()
    db.refresh(book)
    return book

@app.get("/books/")
def list_books(db: Session = Depends(get_db)):
    books = db.query(Book).all()
    return [{"id": b.id, "title": b.title, "author": b.author.name} for b in books]
```

---

## <h2 id="6-demarrage">6. Démarrage du serveur</h2>

Dans le terminal :

```bash
uvicorn main:app --reload
```

Tu accèdes à la documentation interactive sur :
[http://localhost:8000/docs](http://localhost:8000/docs)

---

## <h2 id="7-tests">7. Exemple de test</h2>

1. **Créer un auteur** via `/authors/`
   Paramètre `name` = "Victor Hugo"

2. **Créer un livre** via `/books/`
   Paramètres :

   * `title` = "Les Misérables"
   * `author_id` = 1

3. **Lister tous les livres** via `/books/`



* Ajouter un **endpoint de recherche avancée** 
* Ajouter la **relation inverse** (auteur → tous ses livres) 
* Ajouter **Pydantic** pour valider les schémas d'entrée/sortie 
