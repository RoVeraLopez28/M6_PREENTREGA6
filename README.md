## Justificación Técnica de Transformaciones (Proceso ETL)

El objetivo principal de este pipeline de datos no fue solo "limpiar errores", sino **proteger la integridad de la información financiera**. Antes de eliminar cualquier registro defectuoso, se analizó el impacto que esto tendría en los reportes de ingresos. A continuación, detallo las decisiones clave:

### Tabla `Dim_Clientes` (Limpieza conservadora)
* **El Problema:** Existían clientes que no tenían cargado su `email` o su `ciudad` (valores nulos).
* **La Decisión:** En lugar de borrar a esos clientes del sistema, decidí reemplazar los campos vacíos con el texto `"Sin datos"`.
* **El Motivo (Técnico y de Negocio):** Si elimino el registro de un cliente en esta tabla, todas las compras que esa persona hizo quedarían "huérfanas" cuando se crucen con la tabla de ventas (`Fact_Ventas`). Es decir, el sistema no sabría a quién asignarle ese dinero y los totales de ingresos darían error. Es preferible tener un dato demográfico faltante que perder dinero real en el reporte.

### Tabla `Dim_Productos` (Ingeniería Inversa)
* **El Problema:** Un producto en específico tenía valores nulos en su categoría y, lo que es más crítico, en su precio.
* **La Decisión:** 1. **Categorización:** Al detectar por su nombre que el artículo era una Laptop, se le imputó lógicamente la categoría `"Computación"`.
  2. **Recuperación de Precio:** Eliminar el producto estaba descartado porque ya tenía ventas asociadas. Para resolver el precio nulo, apliqué **ingeniería inversa**: crucé los datos con la tabla `Fact_Ventas`, busqué las transacciones de ese producto en particular y dividí el total cobrado entre la cantidad vendida. Así descubrí el precio real y lo completé.

### Alcance del Modelo (Tabla `Territorios`)
* **La Decisión:** Durante la fase de exploración, detecté una quinta tabla disponible llamada `territorios`. Aunque inicialmente consideré integrarla al modelo, decidí descartarla.
* **El Motivo:** Las mejores prácticas de modelado indican que no se debe sobrecargar el reporte con tablas innecesarias. Para mantener un modelo eficiente, ligero y estrictamente alineado al alcance del proyecto (MVP), me enfoqué exclusivamente en dejar perfectas las 4 tablas principales solicitadas.
