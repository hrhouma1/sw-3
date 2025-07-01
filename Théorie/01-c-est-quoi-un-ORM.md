## ORM

Un **ORM** (Object-Relational Mapping), ou **mappage objet-relationnel** en français, est une technique qui permet de **manipuler une base de données relationnelle (comme PostgreSQL, MySQL, SQLite)** en utilisant un **langage orienté objet** (comme Python, Java, etc.) **sans écrire directement des requêtes SQL**.



### <u>Pourquoi utiliser un ORM ?</u>

Parce qu'il :

* évite d’écrire manuellement du SQL
* facilite la lecture et la maintenance du code
* protège contre certaines erreurs (ex. injections SQL)
* permet de travailler **avec des objets Python** au lieu de lignes/colonnes



### <u>Exemple simple avec Python et SQLAlchemy (un ORM)</u>

#### ⚠ Sans ORM (SQL brut) :

```python
import sqlite3

conn = sqlite3.connect("example.db")
cursor = conn.cursor()

cursor.execute("INSERT INTO users (name, age) VALUES (?, ?)", ("Alice", 30))
conn.commit()
```

## Avec ORM (SQLAlchemy) :

```python
from sqlalchemy import create_engine, Column, Integer, String
from sqlalchemy.orm import declarative_base, Session

Base = declarative_base()

class User(Base):
    __tablename__ = "users"
    id = Column(Integer, primary_key=True)
    name = Column(String)
    age = Column(Integer)

engine = create_engine("sqlite:///example.db")
Base.metadata.create_all(engine)

with Session(engine) as session:
    user = User(name="Alice", age=30)
    session.add(user)
    session.commit()
```



## <u>ORMS populaires :</u>

* **Python** : SQLAlchemy, Django ORM, Tortoise ORM
* **Java** : Hibernate
* **PHP** : Eloquent (Laravel)
* **JavaScript (Node.js)** : Sequelize, TypeORM, Prisma



## <u>Résumé</u>

| Terme                    | Signification                            |
| ------------------------ | ---------------------------------------- |
| ORM                      | Mappage Objet-Relationnel                |
| Utilité                  | Manipuler une base avec des objets       |
| Avantages                | Moins de SQL, code plus clair, plus sûr  |
| Inconvénients potentiels | Moins de contrôle fin, parfois plus lent |


