# Sistema de Inventario de una Tienda de Zapatos

## 1. Información del proyecto

**Estudiante:** Moises David Arrieta Hernandez  
**Asignatura:** Desarrollo de Aplicaciones con Acceso a Datos  
**Lenguaje de programación:** Python 3.13.14  
**ORM:** SQLAlchemy  
**Base de datos:** SQLite  

## 2. Descripción del proyecto

Este proyecto consiste en implementar un sistema de inventario para una pequeña tienda de zapatos, utilizando Python y SQLAlchemy como herramienta ORM para administrar la información mediante una base de datos SQLite.

La aplicación permite registrar zapatos, consultar los registros existentes, buscar productos mediante su identificador, actualizar precios y eliminar registros.

La finalidad principal es implementar la persistencia de datos para que la información permanezca almacenada incluso después de cerrar y volver a ejecutar el programa.

## 3. Tecnologías utilizadas

- **Python:** lenguaje utilizado para desarrollar la aplicación.
- **SQLAlchemy:** ORM que permite trabajar con la base de datos mediante clases y objetos de Python.
- **SQLite:** sistema de base de datos utilizado para almacenar permanentemente los zapatos.
- **Visual Studio Code:** entorno utilizado para desarrollar y ejecutar el proyecto.

## 4. Estructura del proyecto

```text
tienda_zapatos/
│
├── main.py
├── datos.py
├── requirements.txt
├── README.md
├── zapateria.db
│
└── evidencias/
    ├── 01_registro.png
    ├── 02_listado.png
    ├── 03_busqueda.png
    ├── 04_actualizacion.png
    ├── 05_eliminacion.png
    └── 06_persistencia.png
```

El archivo `main.py` contiene el menú y la interacción con el usuario. El archivo `datos.py` contiene la conexión a SQLite, el modelo ORM y las operaciones CRUD. El archivo `zapateria.db` se genera automáticamente al ejecutar el programa.

## 5. Instalación y ejecución

### Requisitos

- Tener Python instalado.
- Tener Visual Studio Code o un editor compatible.
- Tener instalado Git para trabajar con el repositorio.

### Instalación

1. Clonar o descargar el repositorio.
2. Abrir la carpeta del proyecto en Visual Studio Code.
3. Crear un entorno virtual:

```bash
python -m venv venv
```

4. Activar el entorno virtual en Windows PowerShell:

```powershell
.\venv\Scripts\Activate.ps1
```

Si PowerShell bloquea la ejecución de scripts, se puede permitir temporalmente con:

```powershell
Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass
```

Después, volver a ejecutar el comando de activación.

5. Instalar las dependencias:

```bash
python -m pip install -r requirements.txt
```

6. Ejecutar la aplicación:

```bash
python main.py
```

## 6. Implementación de SQLAlchemy

### Modelo Zapato

Para representar la tabla de la base de datos se creó la clase `Zapato`, que hereda de `Base`.

La clase contiene los siguientes atributos:

- `id`: identificador único y clave primaria autoincremental.
- `modelo`: nombre o referencia del zapato.
- `talla`: talla del producto.
- `precio`: precio del zapato.

Se utilizó `Mapped` y `mapped_column` para definir los atributos y sus respectivos tipos de datos.

### Operaciones CRUD

Se implementaron las siguientes operaciones:

| Operación | Función |
|---|---|
| Crear | `crear_zapato()` |
| Leer | `listar_zapatos()` |
| Buscar | `buscar_zapato()` |
| Actualizar | `actualizar_precio()` |
| Eliminar | `eliminar_zapato()` |

Todas las operaciones se realizan mediante sesiones y objetos de SQLAlchemy ORM, sin utilizar directamente la biblioteca `sqlite3` ni escribir consultas SQL manuales.

## 7. Respuestas a las preguntas del parcial

### Pregunta 1. ¿Qué clase de Python representa la tabla de la base de datos? Explica cómo SQLAlchemy identifica el nombre de la tabla y sus columnas.

La clase de Python que representa la tabla de la base de datos es `Zapato`, la cual hereda de la clase `Base`, creada mediante `DeclarativeBase`.

En nuestro código se utiliza:

```python
class Zapato(Base):
    __tablename__ = "zapatos"
```

Con `__tablename__` se indica que el nombre de la tabla en SQLite será `zapatos`.

Las columnas se identifican mediante los atributos declarados con `Mapped` y `mapped_column`. Por ejemplo, `id` representa la clave primaria, `modelo` almacena el nombre del zapato, `talla` guarda su talla y `precio` almacena su valor.

De esta manera, SQLAlchemy relaciona los objetos de Python con los registros de la tabla.

### Pregunta 2. ¿Qué ocurre cuando ejecutas el programa por primera vez? Explica cómo se crea la base de datos zapateria.db y la tabla zapatos.

Cuando se ejecuta por primera vez el programa, la función `main()` de `main.py` llama a:

```python
datos.inicializar_bd()
```

Esta función ejecuta:

```python
Base.metadata.create_all(engine)
```

El `engine` establece la conexión con SQLite mediante:

```python
engine = create_engine("sqlite:///zapateria.db")
```

Si el archivo `zapateria.db` todavía no existe, SQLite lo crea automáticamente. Después, SQLAlchemy utiliza la información del modelo `Zapato` para crear la tabla `zapatos`.

Así, la aplicación queda preparada para almacenar los registros del inventario.

### Pregunta 3. Cuando seleccionas la opción de registrar un zapato, ¿qué recorrido siguen los datos desde el menú hasta quedar almacenados en SQLite? Identifica las funciones utilizadas.

Cuando el usuario selecciona la opción 1 del menú, el programa solicita el modelo, la talla y el precio del zapato.

Luego, `main.py` llama a:

```python
datos.crear_zapato(modelo, talla, precio)
```

Dentro de esta función se crea un objeto de la clase `Zapato` con los datos ingresados.

Posteriormente, se abre una sesión con `Session(engine)`, se agrega el objeto utilizando `session.add()` y se confirma la transacción mediante `session.commit()`.

Finalmente, SQLAlchemy guarda el nuevo registro en la tabla `zapatos` de SQLite y la función devuelve `True` cuando termina correctamente.

### Pregunta 4. ¿Para qué se utiliza una sesión de SQLAlchemy? ¿Qué ocurriría si agregas o modificas un objeto, pero no confirmas la transacción con commit()?

Una sesión de SQLAlchemy permite establecer una unidad de trabajo entre la aplicación y la base de datos. A través de ella podemos agregar, consultar, modificar y eliminar objetos.

En nuestro proyecto se utiliza:

```python
with Session(engine) as session:
```

Esto permite trabajar con una sesión que se cierra automáticamente al terminar el bloque.

El método `commit()` es importante porque confirma y guarda los cambios realizados en la base de datos.

Si agregamos o modificamos un objeto, pero no ejecutamos `commit()`, los cambios no quedan confirmados. Al cerrar la sesión