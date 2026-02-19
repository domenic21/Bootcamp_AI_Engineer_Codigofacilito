
# Resumen de Conceptos: Asistente de Cocina con IA

Este documento explica los conceptos clave del script `actividad_extractor_recetas.py`.

## 1. ¿Qué Hace Este Script?

Este programa es un **asistente de cocina inteligente** que utiliza un Modelo de Lenguaje Grande (LLM) para:

1.  **Extraer información de recetas**: Identifica ingredientes, cantidades y pasos de un texto.
2.  **Consultar recetas guardadas**: Permite buscar en las recetas que ha procesado.

Utiliza el LLM para entender el lenguaje natural en lugar de reglas de programación tradicionales.

---

## 2. Conceptos Clave de Inteligencia Artificial

### a. El Modelo (`model`) y el Cliente (`client`)

-   **`client = OpenAI()`**: Crea un objeto "cliente" para comunicarse con la API de un proveedor de modelos de IA (como OpenAI o Groq).
-   **`model=MODEL`**: Especifica qué modelo de IA se usará (ej. "gpt-4o"). Cada modelo tiene distintas capacidades y costos.
-   **`client.chat.completions.create(...)`**: La función principal que envía la petición (prompt) al modelo y recibe su respuesta.

### b. La Temperatura (`temperature`)

La `temperature` es un parámetro que controla la creatividad del modelo.

-   **Baja (ej. 0.2)**: Respuestas predecibles y precisas. Ideal para extraer datos, como en este script.
-   **Alta (ej. 1.0)**: Respuestas más creativas y variadas. Útil para generar ideas o textos originales.

### c. System Prompt (Instrucciones para el Modelo)

El "System Prompt" define la personalidad y el objetivo del modelo. En este caso:
```python
"Eres un chef experto. Extrae la receta del texto del usuario en el formato estructurado solicitado..."
```
Le dice al modelo cómo debe comportarse antes de procesar la petición del usuario.

### d. Herramientas o "Tools" (Function Calling)

Esta es una técnica avanzada que permite al modelo de IA utilizar funciones de Python que tú defines.

1.  **Defines tus funciones** en Python (ej. `extraer_receta_de_texto`).
2.  **Informas al modelo** sobre estas herramientas, describiendo qué hacen y qué argumentos necesitan.
3.  **El modelo decide** si necesita usar una herramienta para responder a la petición del usuario.
4.  Si es así, el modelo **pide permiso para ejecutarla**, devolviendo el nombre de la función y los argumentos necesarios.
5.  **Tu código ejecuta la función** y envía el resultado de vuelta al modelo.
6.  El modelo usa ese resultado para formular la **respuesta final** al usuario.

---

## 3. Pydantic: Definiendo la Estructura de los Datos

Pydantic es una librería de Python para **validación de datos**. Permite definir un "contrato" o "esquema" para tus datos.

### a. ¿Qué es Pydantic?

Funciona creando clases que actúan como plantillas. Si un dato no cumple con la estructura de la clase, Pydantic genera un error.

### b. ¿Cómo se usa aquí?

Se definen clases como `Ingrediente` y `Receta` para asegurar que el modelo de IA devuelva la información en un formato estructurado y predecible.

```python
class Ingrediente(BaseModel):
    nombre: str
    cantidad: float
    unidad: str

class Receta(BaseModel):
    titulo: str
    ingredientes: List[Ingrediente]
    pasos: List[str]
```

-   `nombre: str`: Asegura que el nombre del ingrediente siempre sea texto.
-   `ingredientes: List[Ingrediente]`: Asegura que una receta contenga una lista, y que cada elemento de esa lista sea un objeto `Ingrediente` válido.

Esto obliga al modelo a ser preciso y facilita el manejo de los datos en tu programa.

---

## 4. Sintaxis de Python

-   **`import ...`**: Importa librerías para usarlas en el código.
-   **`def mi_funcion(argumento: str) -> int:`**: Define una función con "pistas de tipo" (Type Hints), indicando que `argumento` es un `str` y que la función devuelve un `int`. Es una buena práctica que mejora la legibilidad.
-   **`f"Hola, {nombre}"` (f-string)**: Una forma moderna de formatear strings, insertando el valor de las variables directamente en el texto.
-   **`[]` (Listas)**: Colecciones ordenadas de elementos. Ej: `mi_lista = [1, 2, 3]`.
-   **`{}` (Diccionarios)**: Colecciones de pares clave-valor. Ej: `mi_dict = {"nombre": "Ana", "edad": 30}`.

---

## 5. Resumen del Flujo Completo

1.  **Inicio**: El programa se ejecuta con una lista vacía de recetas.
2.  **Usuario Escribe**: Un usuario introduce un texto con una receta.
3.  **Llamada al Modelo**: Se envía el texto al LLM junto con la lista de herramientas disponibles.
4.  **Decisión del Modelo**: El LLM determina que debe usar la herramienta `extraer_receta_de_texto`.
5.  **Ejecución en Python**: El script ejecuta la función, la cual vuelve a llamar al modelo pidiéndole que rellene la estructura Pydantic `Receta`.
6.  **Guardado**: La receta estructurada se guarda en la lista de recetas.
7.  **Respuesta Final**: El modelo genera una respuesta amigable para el usuario (ej. "¡He guardado la receta!").
