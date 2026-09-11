Primero revisaría el estado general de la base para identificar qué está causando la lentitud. Validaría el número de conexiones, sesiones bloqueadas, consultas de mayor consumo y espacio disponible.

Después identificaría las sesiones que generan bloqueos y revisaría si tienen transacciones activas antes de cancelarlas, ya que esto podría generar un rollback y aumentar la carga.

También revisaría las consultas con mayor consumo de CPU, tiempo e I/O, así como el crecimiento del almacenamiento.

Una vez estabilizado el servicio, buscaría la causa raíz y configuraría alertas para conexiones, bloqueos, rendimiento y espacio. En Oracle utilizaría principalmente V$SESSION, V$RESOURCE_LIMIT, V$SQL y V$TRANSACTION para realizar el diagnóstico.
