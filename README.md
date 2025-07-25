🧱 Objetivo del flujo
Filtrar una lista de SharePoint donde:

* El campo Title (que contiene el número de legajo) sea igual al ingresado en el formulario.
* El campo FECHA DESDE (nombre interno: FECHA\_x0020\_DESDE) sea mayor o igual a una fecha fija (ej. 2024-07-01).

✅ Requisitos previos

* Lista de SharePoint con los campos:

  * Title (número de legajo, tipo texto).
  * FECHA DESDE (tipo fecha).
* Un Microsoft Form conectado que recibe el número de legajo como campo.
* El flujo se dispara cuando se envía una respuesta al Form.

🔁 Pasos en Power Automate

1. **Disparador**

   * Acción: "Cuando se envía una respuesta nueva" (Forms).
   * Configuración: Seleccionar el formulario correspondiente.

2. **Obtener detalles de la respuesta**

   * Acción: “Obtener los detalles de la respuesta”.
   * ID de respuesta: usar el ID del paso anterior.

3. **Inicializar variable – FiltroConsulta**

   * Acción: “Inicializar variable”.
   * Nombre: `FiltroConsulta`
   * Tipo: `Cadena`
   * Valor: `""` (vacío).
   * ⚠️ Este paso debe ir **antes** del paso “Obtener elementos”.

4. **Establecer variable – FiltroConsulta**

   * Acción: “Establecer variable”.
   * Nombre: `FiltroConsulta`
   * Valor (usando expresión):

     ```plaintext
     concat('Title eq ''', outputs('Obtener_los_detalles_de_la_respuesta')?['body/{ID_DEL_CAMPO_LEGAJO}'], ''' and FECHA_x0020_DESDE ge 2024-07-01T00:00:00Z')
     ```

     🔁 Reemplazar `{ID_DEL_CAMPO_LEGAJO}` por el nombre interno del campo que viene del formulario. Por ejemplo:

     ```plaintext
     concat('Title eq ''', outputs('Obtener_los_detalles_de_la_respuesta')?['body/numerodelegajo'], ''' and FECHA_x0020_DESDE ge 2024-07-01T00:00:00Z')
     ```

5. **Obtener elementos – SharePoint**

   * Acción: “Obtener elementos”.
   * Dirección del sitio: colocar el sitio correspondiente.
   * Nombre de la lista: el nombre real de la lista (por ejemplo: `REGISTRO MY DAYS`).
   * Consulta de filtro OData:

     ```plaintext
     @{variables('FiltroConsulta')}
     ```
   * ✅ Asegurate de usar `@{}` para que reconozca la variable como expresión.

🛠️ Resultado
El flujo ejecutará la búsqueda filtrando con algo como:

```plaintext
Title eq '3710151' and FECHA_x0020_DESDE ge 2024-07-01T00:00:00Z
```

🧪 Tip final de debugging

* Verificá que:

  * El campo `Title` esté bien escrito.
  * La fecha esté en el formato: `yyyy-MM-ddTHH:mm:ssZ`.
  * No falten ni sobren comillas simples (`'`) en el `concat`.
