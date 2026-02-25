---
name: refactoring-patterns
description: Experto en refactoring y patrones de diseño Python.
---

# 🔧 Refactoring Patterns Expert

Skill para refactorizar código y aplicar patrones de diseño.

## Cuándo Usar

- Mejorar código legacy
- Aplicar patrones de diseño
- Eliminar code smells
- Simplificar funciones complejas

---

## 🚨 Code Smells Comunes

### 1. Función Muy Larga

```python
# ❌ Antes
def process_order(order):
    # 100 líneas de código...

# ✅ Después
def process_order(order):
    validate_order(order)
    calculate_total(order)
    apply_discounts(order)
    save_order(order)
```

### 2. Condicionales Anidados

```python
# ❌ Antes
def get_discount(user):
    if user.is_premium:
        if user.years > 5:
            return 0.3
        else:
            return 0.2
    else:
        return 0.1

# ✅ Después (Early Return)
def get_discount(user):
    if not user.is_premium:
        return 0.1
    if user.years > 5:
        return 0.3
    return 0.2
```

### 3. Magic Numbers

```python
# ❌ Antes
if status == 3:

# ✅ Después
from enum import Enum

class OrderStatus(Enum):
    SHIPPED = 3

if status == OrderStatus.SHIPPED:
```

---

## 🏗️ Patrones de Diseño

### Strategy Pattern

```python
from abc import ABC, abstractmethod

class PaymentStrategy(ABC):
    @abstractmethod
    def pay(self, amount: float) -> bool:
        pass

class CreditCardPayment(PaymentStrategy):
    def pay(self, amount: float) -> bool:
        return process_credit_card(amount)

class PayPalPayment(PaymentStrategy):
    def pay(self, amount: float) -> bool:
        return process_paypal(amount)

# Uso
def checkout(strategy: PaymentStrategy, amount: float):
    return strategy.pay(amount)
```

### Factory Pattern

```python
class NotificationFactory:
    @staticmethod
    def create(notification_type: str) -> Notification:
        if notification_type == "email":
            return EmailNotification()
        elif notification_type == "sms":
            return SMSNotification()
        raise ValueError(f"Unknown type: {notification_type}")
```

---

## 📏 Principios SOLID

| Principio                 | Descripción                                    |
| ------------------------- | ---------------------------------------------- |
| **S**ingle Responsibility | Una clase, una razón para cambiar              |
| **O**pen/Closed           | Abierto a extensión, cerrado a modificación    |
| **L**iskov Substitution   | Subtipos sustituibles por su tipo base         |
| **I**nterface Segregation | Interfaces específicas > interfaces generales  |
| **D**ependency Inversion  | Depender de abstracciones, no implementaciones |

---

## ✅ Checklist

- [ ] ¿Funciones < 20 líneas?
- [ ] ¿Sin condicionales anidados > 2 niveles?
- [ ] ¿Sin magic numbers?
- [ ] ¿SOLID aplicado?
- [ ] ¿Tests antes de refactorizar?
