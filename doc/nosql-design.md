Para la parte no relacional utilizaría MongoDB para almacenar los eventos de auditoría y actividad del sistema. Oracle seguiría siendo la base principal para estancias, pagos, tarifas y cierres.

MongoDB permitiría guardar cada evento como un documento con información como fecha, usuario, operación realizada, módulo, registro afectado y resultado. Esto facilita agregar nuevos tipos de eventos sin modificar constantemente la estructura.

Crearía índices principalmente por fecha, usuario y tipo de evento, ya que serían los datos más utilizados para consultar la auditoría. Mantendría aproximadamente de 6 a 12 meses de información y posteriormente archivaría o eliminaría los eventos antiguos según la política de retención.
