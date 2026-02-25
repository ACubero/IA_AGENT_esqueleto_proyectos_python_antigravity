---
name: api-design
description: Experto en diseño de APIs REST y GraphQL. OpenAPI spec, versionado, autenticación y mejores prácticas.
---

# 🔌 API Design Expert

Skill para diseñar APIs REST profesionales y escalables.

## Cuándo Usar Esta Skill

- Diseñar endpoints RESTful
- Crear especificaciones OpenAPI
- Implementar versionado de APIs
- Configurar autenticación/autorización
- Diseñar respuestas y errores consistentes

---

## 📐 Principios REST

### Convenciones de Endpoints

```
# Colecciones (plural)
GET    /api/v1/users          # Listar usuarios
POST   /api/v1/users          # Crear usuario
GET    /api/v1/users/{id}     # Obtener usuario
PUT    /api/v1/users/{id}     # Actualizar completo
PATCH  /api/v1/users/{id}     # Actualizar parcial
DELETE /api/v1/users/{id}     # Eliminar usuario

# Recursos anidados
GET    /api/v1/users/{id}/orders        # Pedidos del usuario
POST   /api/v1/users/{id}/orders        # Crear pedido
GET    /api/v1/users/{id}/orders/{oid}  # Pedido específico

# Acciones (verbos como excepción)
POST   /api/v1/users/{id}/activate      # Acción específica
POST   /api/v1/orders/{id}/cancel       # Cancelar pedido
```

### Métodos HTTP

| Método | Idempotente | Seguro | Uso                |
| ------ | ----------- | ------ | ------------------ |
| GET    | ✅          | ✅     | Leer recurso       |
| POST   | ❌          | ❌     | Crear recurso      |
| PUT    | ✅          | ❌     | Reemplazar recurso |
| PATCH  | ❌          | ❌     | Actualizar parcial |
| DELETE | ✅          | ❌     | Eliminar recurso   |

---

## 📄 OpenAPI Specification

```yaml
# openapi.yaml
openapi: 3.1.0
info:
  title: Mi API
  description: API para gestión de usuarios
  version: 1.0.0
  contact:
    email: api@example.com

servers:
  - url: https://api.example.com/v1
    description: Producción
  - url: https://staging-api.example.com/v1
    description: Staging

paths:
  /users:
    get:
      summary: Listar usuarios
      operationId: listUsers
      tags:
        - Users
      parameters:
        - $ref: "#/components/parameters/PageParam"
        - $ref: "#/components/parameters/LimitParam"
        - name: status
          in: query
          schema:
            type: string
            enum: [active, inactive, pending]
      responses:
        "200":
          description: Lista de usuarios
          content:
            application/json:
              schema:
                $ref: "#/components/schemas/UserList"
        "401":
          $ref: "#/components/responses/Unauthorized"

    post:
      summary: Crear usuario
      operationId: createUser
      tags:
        - Users
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: "#/components/schemas/UserCreate"
      responses:
        "201":
          description: Usuario creado
          content:
            application/json:
              schema:
                $ref: "#/components/schemas/User"
        "400":
          $ref: "#/components/responses/BadRequest"
        "422":
          $ref: "#/components/responses/ValidationError"

  /users/{userId}:
    get:
      summary: Obtener usuario
      operationId: getUser
      tags:
        - Users
      parameters:
        - $ref: "#/components/parameters/UserId"
      responses:
        "200":
          description: Usuario encontrado
          content:
            application/json:
              schema:
                $ref: "#/components/schemas/User"
        "404":
          $ref: "#/components/responses/NotFound"

components:
  schemas:
    User:
      type: object
      required:
        - id
        - email
        - name
      properties:
        id:
          type: integer
          example: 1
        email:
          type: string
          format: email
          example: user@example.com
        name:
          type: string
          example: John Doe
        created_at:
          type: string
          format: date-time

    UserCreate:
      type: object
      required:
        - email
        - name
        - password
      properties:
        email:
          type: string
          format: email
        name:
          type: string
          minLength: 2
          maxLength: 100
        password:
          type: string
          minLength: 8

    UserList:
      type: object
      properties:
        data:
          type: array
          items:
            $ref: "#/components/schemas/User"
        meta:
          $ref: "#/components/schemas/PaginationMeta"

    PaginationMeta:
      type: object
      properties:
        total:
          type: integer
        page:
          type: integer
        limit:
          type: integer
        pages:
          type: integer

    Error:
      type: object
      required:
        - code
        - message
      properties:
        code:
          type: string
        message:
          type: string
        details:
          type: array
          items:
            type: object

  parameters:
    UserId:
      name: userId
      in: path
      required: true
      schema:
        type: integer
    PageParam:
      name: page
      in: query
      schema:
        type: integer
        default: 1
        minimum: 1
    LimitParam:
      name: limit
      in: query
      schema:
        type: integer
        default: 20
        minimum: 1
        maximum: 100

  responses:
    BadRequest:
      description: Solicitud inválida
      content:
        application/json:
          schema:
            $ref: "#/components/schemas/Error"
    Unauthorized:
      description: No autenticado
      content:
        application/json:
          schema:
            $ref: "#/components/schemas/Error"
    NotFound:
      description: Recurso no encontrado
      content:
        application/json:
          schema:
            $ref: "#/components/schemas/Error"
    ValidationError:
      description: Error de validación
      content:
        application/json:
          schema:
            $ref: "#/components/schemas/Error"

  securitySchemes:
    BearerAuth:
      type: http
      scheme: bearer
      bearerFormat: JWT

security:
  - BearerAuth: []
```

---

## 📝 Estructura de Respuestas

### Respuesta Exitosa

```python
# Respuesta de item
{
    "data": {
        "id": 1,
        "name": "John Doe",
        "email": "john@example.com"
    }
}

# Respuesta de lista con paginación
{
    "data": [
        {"id": 1, "name": "John"},
        {"id": 2, "name": "Jane"}
    ],
    "meta": {
        "total": 100,
        "page": 1,
        "limit": 20,
        "pages": 5
    },
    "links": {
        "self": "/api/v1/users?page=1",
        "next": "/api/v1/users?page=2",
        "last": "/api/v1/users?page=5"
    }
}
```

### Respuesta de Error

```python
{
    "error": {
        "code": "VALIDATION_ERROR",
        "message": "Los datos enviados no son válidos",
        "details": [
            {
                "field": "email",
                "message": "El email ya está registrado"
            },
            {
                "field": "password",
                "message": "Mínimo 8 caracteres"
            }
        ]
    }
}
```

---

## 🔐 Autenticación

### JWT Bearer Token

```python
# Headers
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...

# Ejemplo FastAPI
from fastapi import Depends, HTTPException
from fastapi.security import HTTPBearer, HTTPAuthorizationCredentials

security = HTTPBearer()

async def get_current_user(
    credentials: HTTPAuthorizationCredentials = Depends(security)
) -> User:
    token = credentials.credentials
    try:
        payload = jwt.decode(token, SECRET_KEY, algorithms=["HS256"])
        user_id = payload.get("sub")
        if user_id is None:
            raise HTTPException(status_code=401, detail="Token inválido")
        return await get_user(user_id)
    except JWTError:
        raise HTTPException(status_code=401, detail="Token expirado")
```

### API Keys

```python
# Headers
X-API-Key: sk_live_abc123...

# Ejemplo
from fastapi import Security
from fastapi.security import APIKeyHeader

api_key_header = APIKeyHeader(name="X-API-Key")

async def validate_api_key(api_key: str = Security(api_key_header)):
    if not is_valid_api_key(api_key):
        raise HTTPException(status_code=403, detail="API Key inválida")
    return api_key
```

---

## 🔢 Versionado

### URL Path (recomendado)

```
/api/v1/users
/api/v2/users
```

### Header

```
Accept: application/vnd.myapi.v1+json
```

### Query Parameter

```
/api/users?version=1
```

---

## 📊 Códigos de Estado HTTP

| Código | Significado           | Uso                            |
| ------ | --------------------- | ------------------------------ |
| 200    | OK                    | GET exitoso, PUT/PATCH exitoso |
| 201    | Created               | POST exitoso                   |
| 204    | No Content            | DELETE exitoso                 |
| 400    | Bad Request           | Sintaxis inválida              |
| 401    | Unauthorized          | No autenticado                 |
| 403    | Forbidden             | Sin permisos                   |
| 404    | Not Found             | Recurso no existe              |
| 409    | Conflict              | Conflicto (duplicado)          |
| 422    | Unprocessable Entity  | Validación fallida             |
| 429    | Too Many Requests     | Rate limit excedido            |
| 500    | Internal Server Error | Error del servidor             |

---

## ✅ Checklist API Design

- [ ] ¿Endpoints usan sustantivos plurales?
- [ ] ¿Métodos HTTP correctos?
- [ ] ¿Códigos de estado apropiados?
- [ ] ¿Respuestas consistentes (data/error)?
- [ ] ¿Paginación implementada?
- [ ] ¿Versionado definido?
- [ ] ¿OpenAPI spec actualizada?
- [ ] ¿Autenticación documentada?
- [ ] ¿Rate limiting configurado?
