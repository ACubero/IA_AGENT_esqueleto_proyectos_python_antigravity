---
name: performance-profiling
description: Experto en profiling y optimización de rendimiento Python.
---

# ⚡ Performance Profiling Expert

Skill para analizar y optimizar rendimiento en Python.

## Cuándo Usar

- Identificar cuellos de botella
- Profiling de CPU y memoria
- Optimizar código lento
- Analizar memory leaks

---

## 🔍 cProfile - CPU Profiling

```python
import cProfile
import pstats

# Profiling básico
cProfile.run('my_function()', 'output.prof')

# Analizar resultados
stats = pstats.Stats('output.prof')
stats.sort_stats('cumulative')
stats.print_stats(20)  # Top 20 funciones
```

```bash
# Desde línea de comandos
python -m cProfile -s cumulative script.py
```

---

## 📊 line_profiler

```bash
pip install line_profiler
```

```python
@profile  # Decorador especial
def slow_function():
    result = []
    for i in range(1000):
        result.append(i ** 2)
    return result
```

```bash
kernprof -l -v script.py
```

---

## 🧠 Memory Profiling

```bash
pip install memory_profiler
```

```python
from memory_profiler import profile

@profile
def memory_heavy():
    data = [x for x in range(1000000)]
    return sum(data)
```

```bash
python -m memory_profiler script.py
```

---

## ⏱️ timeit para Benchmarks

```python
import timeit

# Comparar implementaciones
time_list = timeit.timeit('[x**2 for x in range(1000)]', number=1000)
time_gen = timeit.timeit('list(x**2 for x in range(1000))', number=1000)

print(f"List comp: {time_list:.4f}s")
print(f"Generator: {time_gen:.4f}s")
```

---

## 🚀 Optimizaciones Comunes

```python
# ❌ Lento
result = ""
for s in strings:
    result += s

# ✅ Rápido
result = "".join(strings)

# ❌ Lento
if item in my_list:  # O(n)

# ✅ Rápido
if item in my_set:   # O(1)

# ❌ Lento
[x for x in range(1000000)]  # Lista en memoria

# ✅ Eficiente
(x for x in range(1000000))  # Generador lazy
```

---

## ✅ Checklist

- [ ] ¿Profiling antes de optimizar?
- [ ] ¿Cuellos de botella identificados?
- [ ] ¿Estructuras de datos apropiadas?
- [ ] ¿Generadores para datos grandes?
- [ ] ¿Memory leaks verificados?
