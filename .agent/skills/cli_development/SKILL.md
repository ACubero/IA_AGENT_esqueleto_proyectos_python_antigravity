---
name: cli-development
description: Experto en desarrollo de herramientas CLI con Python. Typer, Click y argparse.
---

# 💻 CLI Development Expert

Skill para crear herramientas de línea de comandos profesionales.

## Cuándo Usar

- Crear CLIs con Typer o Click
- Parsear argumentos complejos
- Implementar subcomandos
- Crear interfaces interactivas

---

## 📦 Dependencias

```bash
pip install typer[all] rich
```

---

## 🚀 Typer - CLI Moderno

```python
# cli.py
import typer
from typing import Optional
from enum import Enum

app = typer.Typer(help="Mi herramienta CLI")

class Format(str, Enum):
    json = "json"
    csv = "csv"
    table = "table"


@app.command()
def greet(
    name: str = typer.Argument(..., help="Nombre a saludar"),
    times: int = typer.Option(1, "--times", "-t", help="Repeticiones"),
    loud: bool = typer.Option(False, "--loud", "-l", help="En mayúsculas"),
):
    """Saluda al usuario."""
    message = f"Hola, {name}!"
    if loud:
        message = message.upper()
    for _ in range(times):
        typer.echo(message)


@app.command()
def export(
    path: str = typer.Argument(..., help="Ruta del archivo"),
    format: Format = typer.Option(Format.json, help="Formato de salida"),
):
    """Exporta datos al formato especificado."""
    typer.echo(f"Exportando a {path} en formato {format.value}")


if __name__ == "__main__":
    app()
```

```bash
python cli.py greet "Alex" --times 3 --loud
python cli.py export data.json --format json
```

---

## 🎨 Rich para Output

```python
from rich.console import Console
from rich.table import Table
from rich.progress import track

console = Console()

# Tabla formateada
table = Table(title="Usuarios")
table.add_column("ID", style="cyan")
table.add_column("Nombre", style="green")
table.add_row("1", "Alex")
console.print(table)

# Barra de progreso
for item in track(items, description="Procesando..."):
    process(item)

# Mensajes con estilo
console.print("[bold green]✓[/] Completado!")
console.print("[bold red]✗[/] Error!", style="red")
```

---

## 📥 Prompts Interactivos

```python
import typer
from rich.prompt import Prompt, Confirm

name = Prompt.ask("Tu nombre")
age = Prompt.ask("Tu edad", default="18")
proceed = Confirm.ask("¿Continuar?")

# Con validación
password = typer.prompt("Password", hide_input=True)
```

---

## ✅ Checklist

- [ ] ¿Comandos con help descriptivo?
- [ ] ¿Argumentos y opciones documentados?
- [ ] ¿Validación de inputs?
- [ ] ¿Mensajes de error claros?
- [ ] ¿Exit codes apropiados?
