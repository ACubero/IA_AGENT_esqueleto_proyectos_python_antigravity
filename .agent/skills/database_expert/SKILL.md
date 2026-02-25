---
name: database-expert
description: Experto en bases de datos Python. SQLAlchemy ORM, Alembic migraciones y optimización SQL.
---

# 🗄️ Database Expert

Skill para trabajar con bases de datos profesionalmente en Python.

## Cuándo Usar

- Diseñar esquemas de base de datos
- Configurar SQLAlchemy ORM
- Gestionar migraciones con Alembic
- Optimizar consultas SQL

---

## 📦 Dependencias

```bash
pip install sqlalchemy[asyncio] alembic psycopg2-binary asyncpg
```

---

## 🏗️ SQLAlchemy 2.0 Models

```python
# src/models/base.py
from datetime import datetime
from sqlalchemy.orm import DeclarativeBase, Mapped, mapped_column
from sqlalchemy.sql import func

class Base(DeclarativeBase):
    pass

class TimestampMixin:
    created_at: Mapped[datetime] = mapped_column(default=func.now())
    updated_at: Mapped[datetime] = mapped_column(default=func.now(), onupdate=func.now())
```

```python
# src/models/user.py
from sqlalchemy import String, ForeignKey, Index
from sqlalchemy.orm import Mapped, mapped_column, relationship
from .base import Base, TimestampMixin

class User(Base, TimestampMixin):
    __tablename__ = "users"

    id: Mapped[int] = mapped_column(primary_key=True)
    email: Mapped[str] = mapped_column(String(255), unique=True, index=True)
    name: Mapped[str] = mapped_column(String(100))
    is_active: Mapped[bool] = mapped_column(default=True)

    orders: Mapped[list["Order"]] = relationship(back_populates="user")
```

---

## 🔌 Conexión

```python
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker

DATABASE_URL = "postgresql://user:pass@localhost:5432/mydb"

engine = create_engine(DATABASE_URL, pool_size=5, max_overflow=10)
SessionLocal = sessionmaker(bind=engine, autoflush=False)

def get_db():
    db = SessionLocal()
    try:
        yield db
    finally:
        db.close()
```

---

## 🔄 Migraciones Alembic

```bash
alembic init alembic
alembic revision --autogenerate -m "add users table"
alembic upgrade head
alembic downgrade -1
```

---

## ⚡ Optimización

```python
# Evitar N+1 con eager loading
from sqlalchemy.orm import selectinload
stmt = select(User).options(selectinload(User.orders))

# Bulk insert
from sqlalchemy import insert
db.execute(insert(Model), [{"name": "a"}, {"name": "b"}])
```

---

## ✅ Checklist

- [ ] ¿Modelos con Mapped type hints?
- [ ] ¿Connection pooling?
- [ ] ¿Índices en columnas de búsqueda?
- [ ] ¿Migraciones con Alembic?
- [ ] ¿Eager loading para N+1?
