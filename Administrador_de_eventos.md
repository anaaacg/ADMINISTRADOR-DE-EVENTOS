# Administrador de eventos
## Descripcion
Un programa en cpp que maneja una base de datos simple. Esta BD trabaja con fechas en formato AAAA-MM-DD y eventos. 
## Funciones 
El programa se maneja a travez de comandos que realizan las siguientes funciones:
- Add Fecha Evento: Agregar evento
- Del Fecha Evento: Eliminar evento
- Del Fecha: Eliminar todos los eventos de la fecha
- Find Fecha: Buscar todos los eventos de una fecha
- Print: Imprimir todos los eventos de todas las fechas
- Find Palabra: Encontrar los eventos que coincidan con la palabra
- Edit Fecha Evento NuevaFecha NuevoEvento: Reemplaza los antiguas datos por nuevos
## Casos de errores
- Comando desconocido: "Unknown command: COMMAND"
- Fecha sin formato: "Wrong date format: DATE"
- Mes menor a 1 o mayor a 12:  "Month value is invalid: MONTH"
- Dia manor a 1 o mayor a 31: "Day value is invalid: DAY"
## Logica
### General
1. Leer una línea completa.
2. Si la línea está vacía → ignorar.
3. Separar la línea en palabras: `comando`, `arg1`, `arg2`, …
4. Según el comando:

   - **Add** → usar la lógica de agregar.
   - **Del** → usar la lógica de eliminar (uno o varios).
   - **Find** (segunda fase):
     - `Find FECHA` → buscar eventos por fecha.
     - `Find PALABRA` → buscar eventos que contengan la palabra en el nombre.
   - **Print** → mostrar toda la base ordenada.
   - **Edit** → localizar un evento existente y modificarlo.
   - **Cualquier otro texto** → mostrar  
     `Unknown command: XXX`  
     y terminar la ejecución.

### Validacion de fecha
1. Verificar el **formato general** `Año-Mes-Día`:
   - Debe haber exactamente dos guiones `'-'`.
   - Antes del primer guion debe haber un entero (año).
   - Entre el primer y segundo guion debe haber un entero (mes).
   - Después del segundo guion debe haber un entero (día).
   - Los enteros pueden tener signo (`+` o `-`) y al menos un dígito.
   - No debe haber caracteres extra antes del año ni después del día.
2. Si el formato NO cumple estas condiciones:
   - Mostrar el mensaje:  
     `Wrong date format: TEXTO_FECHA`
   - Terminar la ejecución del programa.
3. Si el formato es correcto, convertir las tres partes de texto a números enteros:
   - `anio`
   - `mes`
   - `dia`
4. Validar el **mes**:
   - Si `mes < 1` o `mes > 12`:
     - Mostrar el mensaje:  
       `Month value is invalid: MES`
     - Terminar la ejecución del programa.
5. Validar el **día** (solo si el mes fue válido):
   - Si `dia < 1` o `dia > 31`:
     - Mostrar el mensaje:  
       `Day value is invalid: DIA`
     - Terminar la ejecución del programa.
6. Si todas las validaciones pasan:
   - Construir una fecha interna con `anio`, `mes`, `dia`.
   - Devolver esa fecha para que el comando la use.

### Validar evento
Entrada: `textoEvento` (por ejemplo `"Holiday"`)

1. Verificar que `textoEvento` **no sea vacío**.
2. Verificar que `textoEvento` **no contenga espacios internos**:
   - Si separamos la línea por espacios, el nombre del evento es una sola palabra.
3. Si se cumple lo anterior:
   - Aceptar el texto como nombre de evento.
     
### Lógica específica: comando Add

Formato del comando:
- `Add FECHA EVENTO`
Pasos:
1. Comprobar que la línea tenga al menos 3 palabras:
   - `comando = "Add"`
   - `arg1 = textoFecha`
   - `arg2 = textoEvento`
2. Aplicar el **bloque de validación de fecha** sobre `arg1`:
   - Si hay error de formato, mes o día → el programa ya habrá terminado.
3. Aplicar el **bloque de validación de evento** sobre `arg2`.
4. Recorrer todos los eventos actualmente almacenados en la base de datos:
   - Para cada evento:
     - Comparar la fecha guardada con la fecha nueva.
     - Comparar el nombre guardado con el nombre nuevo.
   - Si se encuentra un evento con **misma fecha y mismo nombre**:
     - Mostrar el mensaje:  
       `Duplicate event`
     - No agregar nada.
     - Terminar el procesamiento de este comando y pasar al siguiente.
5. Si después de revisar todos los eventos **no hay duplicado**:
   - Crear un nuevo registro con la fecha y el nombre.
   - Insertarlo en la estructura de datos (por ejemplo, al final del arreglo dinámico).
   - En caso correcto, el comando `Add` **no imprime ningún mensaje**.  

### Lógica específica: comando Del (eliminar uno)
Formato del comando (eliminar un solo evento):
- `Del FECHA EVENTO`
Pasos:
1. Comprobar que la línea tenga al menos 3 palabras:
   - `comando = "Del"`
   - `arg1 = textoFecha`
   - `arg2 = textoEvento`
   - Cuando hay **3 palabras**, se interpreta como “eliminar un evento específico”.
2. Aplicar el **bloque de validación de fecha** sobre `arg1`.
3. Aplicar el **bloque de validación de evento** sobre `arg2`.
4. Recorrer la lista de eventos almacenados:
   - Para cada evento, verificar:
     - si la fecha guardada es igual a la fecha buscada;
     - y si el nombre guardado es igual al nombre buscado.
5. Si se encuentra un evento que coincide en fecha y nombre:
   - Eliminar ese evento de la estructura de datos:
     - Por ejemplo, reemplazarlo con el último evento de la lista y reducir el tamaño en 1.
   - Mostrar el mensaje:  
     `Deleted successfully`
   - Terminar el procesamiento de este comando.
6. Si se termina de recorrer toda la lista y **no se encontró** el evento:
   - Mostrar el mensaje:  
     `Event not found`
     
### Lógica específica: comando Del (eliminar varios)
Formato del comando (eliminar todos los eventos de una fecha):
- `Del FECHA`
Pasos:
1. Comprobar que la línea tenga exactamente 2 palabras relevantes:
   - `comando = "Del"`
   - `arg1 = textoFecha`
   - Sin `arg2` → se interpreta como “eliminar todos los eventos de esa fecha”.
2. Aplicar el **bloque de validación de fecha** sobre `arg1`.
3. Inicializar un contador:  
   `contadorEliminados = 0`
4. Recorrer la lista de eventos con un índice `i`:
   - Mientras `i` sea menor que la cantidad de eventos:
     - Si la fecha del evento en posición `i` es igual a la fecha buscada:
       - Eliminar ese evento (por ejemplo, copiando el último en la posición `i`).
       - Reducir la cantidad de eventos en 1.
       - Incrementar `contadorEliminados` en 1.
       - **No** aumentar `i` (porque en la posición `i` hay un nuevo evento que aún no se ha revisado).
     - Si la fecha es diferente:
       - Aumentar `i` en 1.
5. Al terminar el recorrido, mostrar:
   - `Deleted N events`
   donde `N` es `contadorEliminados` (puede ser 0).

### Lógica específica: comando Find 
El comando `Find` tiene dos formas:
1. `Find FECHA` → buscar por fecha  
2. `Find PALABRA` → buscar por palabra en el nombre del evento

#### 1) Find por fecha
Pasos:
1. `comando = "Find"`, `arg1 = textoFecha`.
2. Aplicar el **bloque de validación de fecha** sobre `arg1`.
3. Recorrer todos los eventos:
   - Seleccionar aquellos cuya fecha sea igual a la fecha buscada.
4. Ordenar esos eventos por nombre en orden ascendente (lexicográfico).
5. Imprimir solo los nombres de esos eventos, un nombre por línea.

#### 2) Find por palabra
Pasos:
1. `comando = "Find"`, `arg1 = palabraBuscada`.
2. Recorrer todos los eventos:
   - Para cada evento, revisar si el nombre contiene `palabraBuscada` como subcadena.
3. Reunir todos los eventos que cumplen la condición.
4. Ordenar esos eventos por nombre en orden ascendente.
5. Imprimir solo los nombres, un nombre por línea.

### Lógica específica: comando Print 
Formato del comando:
- `Print`
Pasos:
1. `comando = "Print"` sin argumentos adicionales.
2. Tomar todos los eventos almacenados en la base de datos.
3. Ordenarlos aplicando estas reglas:
   - Primero por fecha ascendente:
     - año menor primero;
     - si el año es igual, mes menor primero;
     - si el mes es igual, día menor primero.
   - Dentro de la misma fecha, ordenar por nombre de evento en orden ascendente.
4. Para cada evento en el orden resultante:
   - Convertir la fecha al formato de texto `"AAAA-MM-DD"` con ceros a la izquierda.
   - Imprimir una línea con:
     - `FECHA EVENTO`
   Por ejemplo:  
   `2017-01-01 Holiday`

### Lógica específica: comando Edit 
El comando `Edit` permite modificar un evento existente.  
(Aunque el enunciado no fije una sintaxis exacta, la idea general es:)
1. El usuario indica **qué evento quiere modificar**:
   - Por ejemplo, mediante una combinación de fecha y nombre actual.
2. El usuario también indica los **nuevos datos**:
   - puede ser una nueva fecha, un nuevo nombre, o ambos.
Lógica general:
1. Leer el comando `Edit` y sus argumentos (criterios de búsqueda + nuevos valores).
2. Si se usa una fecha en los criterios:
   - Aplicar el **bloque de validación de fecha**.
3. Localizar el evento que coincide con los criterios:
   - Si no se encuentra:
     - Mostrar `Edit Failed`
     - Terminar el comando.
4. Si se encuentra el evento:
   - Borrar el evento antiguo de la base de datos.
   - Crear un nuevo evento con los datos modificados.
   - Insertarlo en la estructura de datos.
   - Mostrar `Edited successfully`.
  
### Lógica específica: comando desconocido

1. Después de leer la línea y separar la primera palabra como `comando`:
   - Si `comando` no es `Add`, `Del`, `Find`, `Print` ni `Edit`:
     - Mostrar:
       `Unknown command: COMANDO`
     - Terminar la ejecución del programa.
       
## Organizacion del codigo
### Clases
Representan los datos. Se almacenan y hacen las funciones correspondientes a cada uno.
- **Fecha**
  - Atributos: `anio`, `mes`, `dia`.
  - Funciones principales:
    - Comparar dos fechas (`fechasIguales`, `compararFechas`).
    - Convertir la fecha a texto en formato `AAAA-MM-DD` (`aCadena`).
  - Se usa en todos los lugares donde se necesite trabajar con fechas ya validadas.
- **Evento**
  - Atributos: una `Fecha` y el nombre del evento (`char*`).
  - Funciones principales:
    - Comparar si un evento coincide con una fecha + nombre (para buscar o eliminar).
    - Comparar eventos entre sí (por fecha y por nombre) para poder ordenarlos.
- **BaseDatos**
  - Atributos: arreglo dinámico de `Evento`, tamaño actual y capacidad.
  - Funciones principales (con el tiempo cubrirán todo el enunciado):
    - Agregar evento (usado por `Add`).
    - Eliminar un solo evento por fecha + nombre (`Del FECHA EVENTO`).
    - Eliminar todos los eventos de una fecha (`Del FECHA`).
    - Buscar eventos por fecha (`Find FECHA`).
    - Buscar eventos por palabra en el nombre (`Find PALABRA`).
    - Obtener todos los eventos ordenados para `Print`.
    - Editar un evento: localizarlo, eliminarlo y agregar la versión modificada (`Edit`).
- **Clases de comando**
  - `Comando` (base abstracta) y derivados como:
    - `ComandoAgregar`, `ComandoEliminar`, `ComandoFind`, `ComandoPrint`, `ComandoEdit`.
  - Cada comando:
    - Guarda los datos ya parseados (fecha, palabra, nombre, etc.).
    - Tiene una función `ejecutar(BaseDatos&)` que llama a los métodos de `BaseDatos`.
  - Sirve para organizar mejor la lógica cuando el proyecto esté más avanzado (herencia y polimorfismo).
- **Manejador de Archivo**
  - Clase encargada de guardar y cargar todos los eventos de `BaseDatos` en un archivo de texto plano.
  - Permite que al iniciar el programa se recupere la información, y al terminar se persista.

### Funciones externas
Implementan la lógica de interacción: lectura de comandos, separación en palabras y validación del formato antes de usar las clases.
- **Entrada y separación de palabras**
  - `leerLinea(buffer, tam)`  
    Lee una línea completa desde la entrada estándar.
  - `dividirEnPalabras(linea, palabras, maxPalabras, cantPalabras)`  
    Separa la línea por espacios en: `comando`, `arg1`, `arg2`, etc.
- **Validación y conversión de fecha (texto → Fecha)**
  - `esDigito(c)`  
    Verifica si un carácter es un dígito.
  - `leerEntero(texto, pos, valor)`  
    Lee un entero con signo desde un `char*`, avanzando la posición.
  - `convertirTextoEnFecha(textoFecha, fechaResultado)`  
    - Verifica el formato `Año-Mes-Día`.
    - Muestra los mensajes de error necesarios:
      - `Wrong date format: DATE`
      - `Month value is invalid: M`
      - `Day value is invalid: D`
    - Si todo es correcto, llena un objeto `Fecha` que luego usarán las clases.
- **Apoyo para búsquedas y ordenamiento**
  - Funciones para comparar nombres de eventos (basadas en `strcmp`).
  - Función para verificar si un nombre contiene una palabra (`contieneSubcadena`), usada en `Find PALABRA`.
  - Funciones auxiliares para intercambiar eventos y ordenar (`swapEventos`, algoritmos de ordenamiento simples).
- **Funciones de integración con comandos**
  - Una función que:
    - lee la línea,
    - separa en palabras,
    - decide qué tipo de comando es,
    - y crea el objeto `Comando` correspondiente (si se usa el modelo con herencia).

## Diagramas
- Diagrama UML: https://lucid.app/lucidchart/fd4aef67-e40e-4eb3-8beb-ad288b54c827/edit?viewport_loc=48%2C111%2C1752%2C751%2CHWEp-vi-RSFO&invitationId=inv_10a7cb7d-19be-433e-b737-942af21bb318
- Diagramas de flujo: 
- Diagrams de entrada, proceso y salida: 




