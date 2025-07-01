## <h1 id="definition">1. Définition rapide</h1>

* **Sans ORM (Object Relational Mapping)** :
  Tu écris **toi-même les requêtes SQL** avec `sqlite3`, `psycopg2`, etc.

* **Avec ORM** (comme SQLAlchemy, Django ORM, etc.) :
  Tu manipules des **objets Python** représentant tes tables. Le framework **génère les requêtes SQL** pour toi.

---

## <h2 id="objectif">2. Objectif</h2>

On va créer une table `User` et insérer un utilisateur `Alice` dans une base SQLite.

---

## <h2 id="sans-orm">3. Version SANS ORM (avec `sqlite3`)</h2>

```python
import sqlite3

# Connexion à la base
conn = sqlite3.connect("exemple.db")
cursor = conn.cursor()

# Création de la table
cursor.execute("""
CREATE TABLE IF NOT EXISTS user (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    name TEXT NOT NULL,
    email TEXT NOT NULL UNIQUE
)
""")

# Insertion manuelle
cursor.execute("INSERT INTO user (name, email) VALUES (?, ?)", ("Alice", "alice@example.com"))

# Lecture des données
cursor.execute("SELECT * FROM user")
for row in cursor.fetchall():
    print(row)

# Fermeture
conn.commit()
conn.close()
```

> Tu écris **toi-même** tout le SQL : création, insertion, lecture, etc.

---

## <h2 id="avec-orm">4. Version AVEC ORM (avec SQLAlchemy)</h2>

```python
from sqlalchemy import create_engine, Column, Integer, String
from sqlalchemy.orm import declarative_base, sessionmaker

Base = declarative_base()

# Définition du modèle
class User(Base):
    __tablename__ = "user"
    id = Column(Integer, primary_key=True)
    name = Column(String, nullable=False)
    email = Column(String, unique=True, nullable=False)

# Connexion et création de la table
engine = create_engine("sqlite:///exemple.db")
Base.metadata.create_all(engine)

# Insertion avec ORM
Session = sessionmaker(bind=engine)
session = Session()

user = User(name="Alice", email="alice@example.com")
session.add(user)
session.commit()

# Lecture
for user in session.query(User).all():
    print(user.id, user.name, user.email)
```

> Tu **décris la table comme une classe Python** et tu manipules des **objets Python** : plus simple, plus sûr, plus maintenable.

---

## <h2 id="comparaison">5. Comparaison rapide</h2>

| Critère                 | Sans ORM (sqlite3)           | Avec ORM (SQLAlchemy)             |
| ----------------------- | ---------------------------- | --------------------------------- |
| Niveau d’abstraction    | Faible (requêtes SQL brutes) | Élevé (manipulation d’objets)     |
| Lisibilité              | Moins lisible                | Plus lisible pour les devs Python |
| Sécurité SQL Injection  | Nécessite précautions        | Gérée par l’ORM                   |
| Complexité des requêtes | Tout à la main               | Simplifié avec méthodes ORM       |
| Maintenance             | Difficile sur gros projets   | Facile à long terme               |

<br/>

# Partie 2 - relations (1-N, N-N) 



*Exercice guidé pas à pas** avec **relation 1-N** (un-à-plusieurs), en deux versions :

1. **Sans ORM** (requêtes SQL manuelles avec `sqlite3`)
2. **Avec ORM** (SQLAlchemy, relation entre deux classes)



## <h1 id="objectif">1. Objectif pédagogique</h1>

Créer une base de données qui contient :

* Une table `Author` (auteur)
* Une table `Book` (livre), liée à un auteur (relation un auteur → plusieurs livres)

On va :

* Créer les tables
* Insérer des données
* Lister tous les livres avec le nom de leur auteur

---

## <h2 id="version-sans-orm">2. Version SANS ORM (avec `sqlite3`)</h2>

```python
import sqlite3

# Connexion
conn = sqlite3.connect("livres.db")
cursor = conn.cursor()

# Tables
cursor.execute("""
CREATE TABLE IF NOT EXISTS author (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    name TEXT NOT NULL
)
""")

cursor.execute("""
CREATE TABLE IF NOT EXISTS book (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    title TEXT NOT NULL,
    author_id INTEGER,
    FOREIGN KEY(author_id) REFERENCES author(id)
)
""")

# Insertion
cursor.execute("INSERT INTO author (name) VALUES (?)", ("Victor Hugo",))
cursor.execute("INSERT INTO author (name) VALUES (?)", ("Jules Verne",))
conn.commit()

cursor.execute("INSERT INTO book (title, author_id) VALUES (?, ?)", ("Les Misérables", 1))
cursor.execute("INSERT INTO book (title, author_id) VALUES (?, ?)", ("Notre-Dame de Paris", 1))
cursor.execute("INSERT INTO book (title, author_id) VALUES (?, ?)", ("Vingt mille lieues sous les mers", 2))
conn.commit()

# Jointure
cursor.execute("""
SELECT book.title, author.name
FROM book
JOIN author ON book.author_id = author.id
""")

for row in cursor.fetchall():
    print(f"Livre : {row[0]} | Auteur : {row[1]}")

conn.close()
```

---

## <h2 id="version-avec-orm">3. Version AVEC ORM (SQLAlchemy – relation 1-N)</h2>

```python
from sqlalchemy import create_engine, Column, Integer, String, ForeignKey
from sqlalchemy.orm import declarative_base, relationship, sessionmaker

Base = declarative_base()

# Modèle Author
class Author(Base):
    __tablename__ = "author"
    id = Column(Integer, primary_key=True)
    name = Column(String, nullable=False)

    books = relationship("Book", back_populates="author")

# Modèle Book
class Book(Base):
    __tablename__ = "book"
    id = Column(Integer, primary_key=True)
    title = Column(String, nullable=False)
    author_id = Column(Integer, ForeignKey("author.id"))

    author = relationship("Author", back_populates="books")

# Connexion
engine = create_engine("sqlite:///livres.db")
Base.metadata.create_all(engine)
Session = sessionmaker(bind=engine)
session = Session()

# Insertion
a1 = Author(name="Victor Hugo")
a2 = Author(name="Jules Verne")

session.add_all([
    Book(title="Les Misérables", author=a1),
    Book(title="Notre-Dame de Paris", author=a1),
    Book(title="Vingt mille lieues sous les mers", author=a2)
])

session.commit()

# Affichage
books = session.query(Book).all()
for book in books:
    print(f"Livre : {book.title} | Auteur : {book.author.name}")
```

---

## <h2 id="conclusion">4. Résultat attendu</h2>

```
Livre : Les Misérables | Auteur : Victor Hugo
Livre : Notre-Dame de Paris | Auteur : Victor Hugo
Livre : Vingt mille lieues sous les mers | Auteur : Jules Verne
```


