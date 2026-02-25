---
name: logging-observability
description: Experto en logging estructurado, tracing y métricas para aplicaciones Python.
---

# 📊 Logging & Observability Expert

Skill para implementar logging profesional y observabilidad.

## Cuándo Usar

- Configurar logging estructurado
- Implementar tracing distribuido
- Configurar métricas de aplicación
- Debugging en producción

---

## 📝 Logging Estructurado

```python
# src/logging_config.py
import logging
import json
from datetime import datetime
from typing import Any

class JSONFormatter(logging.Formatter):
    def format(self, record: logging.LogRecord) -> str:
        log_data = {
            "timestamp": datetime.utcnow().isoformat(),
            "level": record.levelname,
            "message": record.getMessage(),
            "module": record.module,
            "function": record.funcName,
            "line": record.lineno,
        }
        if record.exc_info:
            log_data["exception"] = self.formatException(record.exc_info)
        if hasattr(record, "extra_data"):
            log_data.update(record.extra_data)
        return json.dumps(log_data)

def setup_logging(level: str = "INFO") -> None:
    handler = logging.StreamHandler()
    handler.setFormatter(JSONFormatter())
    logging.root.handlers = [handler]
    logging.root.setLevel(level)
```

---

## 🔍 Uso del Logger

```python
import logging

logger = logging.getLogger(__name__)

# Logging básico
logger.info("User logged in", extra={"extra_data": {"user_id": 123}})

# Con contexto
logger.warning("Rate limit exceeded", extra={
    "extra_data": {"ip": "192.168.1.1", "requests": 100}
})

# Excepciones
try:
    risky_operation()
except Exception:
    logger.exception("Operation failed")
```

---

## 📈 Métricas con Prometheus

```python
from prometheus_client import Counter, Histogram, start_http_server

# Definir métricas
REQUEST_COUNT = Counter('http_requests_total', 'Total requests', ['method', 'endpoint'])
REQUEST_LATENCY = Histogram('http_request_duration_seconds', 'Request latency')

# Usar métricas
@REQUEST_LATENCY.time()
def handle_request():
    REQUEST_COUNT.labels(method='GET', endpoint='/users').inc()
```

---

## 🔗 Tracing con OpenTelemetry

```python
from opentelemetry import trace
from opentelemetry.sdk.trace import TracerProvider

trace.set_tracer_provider(TracerProvider())
tracer = trace.get_tracer(__name__)

with tracer.start_as_current_span("my-operation") as span:
    span.set_attribute("user.id", 123)
    do_work()
```

---

## ✅ Checklist

- [ ] ¿Logging estructurado (JSON)?
- [ ] ¿Niveles de log apropiados?
- [ ] ¿Contexto en logs (user_id, request_id)?
- [ ] ¿Métricas clave definidas?
- [ ] ¿Tracing configurado?
