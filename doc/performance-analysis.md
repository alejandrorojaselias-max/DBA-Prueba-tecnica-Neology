
EXPLAIN PLAN FOR
SELECT
r.ID_RESIDENTE,
r.NUMERO_RESIDENTE,
r.NOMBRE,
NVL(SUM(e.MINUTOS_ESTANCIA), 0) AS TOTAL_MINUTOS,
NVL(SUM(e.MONTO_CARGO), 0) AS TOTAL_CARGOS
FROM RESIDENTE r
LEFT JOIN ESTANCIA e
ON e.ID_RESIDENTE = r.ID_RESIDENTE
AND e.ESTADO = 'CERRADA'
AND e.FECHA_SALIDA >= TIMESTAMP '2026-09-01 00:00:00'
AND e.FECHA_SALIDA <  TIMESTAMP '2026-10-01 00:00:00'
GROUP BY
r.ID_RESIDENTE,
r.NUMERO_RESIDENTE,
r.NOMBRE
ORDER BY r.ID_RESIDENTE;

SELECT *
FROM TABLE(DBMS_XPLAN.DISPLAY);


Plan hash value: 861718669
 
------------------------------------------------------------------------------------------------------------------
| Id  | Operation                              | Name                    | Rows  | Bytes | Cost (%CPU)| Time     |
------------------------------------------------------------------------------------------------------------------
|   0 | SELECT STATEMENT                       |                         |     3 |   327 |     5  (40)| 00:00:01 |
|   1 |  SORT GROUP BY                         |                         |     3 |   327 |     5  (40)| 00:00:01 |
|   2 |   MERGE JOIN OUTER                     |                         |     3 |   327 |     4  (25)| 00:00:01 |
|   3 |    TABLE ACCESS BY INDEX ROWID         | RESIDENTE               |     3 |   246 |     1   (0)| 00:00:01 |
|   4 |     INDEX FULL SCAN                    | IDX_RESIDENTE_PK        |     3 |       |     1   (0)| 00:00:01 |
|*  5 |    SORT JOIN                           |                         |     1 |    27 |     3  (34)| 00:00:01 |
|*  6 |     TABLE ACCESS BY INDEX ROWID BATCHED| ESTANCIA                |     1 |    27 |     2   (0)| 00:00:01 |
|*  7 |      INDEX RANGE SCAN                  | IDX_ESTANCIA_Q1_ABIERTA |     3 |       |     1   (0)| 00:00:01 |
------------------------------------------------------------------------------------------------------------------
 
Predicate Information (identified by operation id):
---------------------------------------------------
 
   5 - access("E"."ID_RESIDENTE"(+)="R"."ID_RESIDENTE")
       filter("E"."ID_RESIDENTE"(+)="R"."ID_RESIDENTE")
   6 - filter("E"."ID_RESIDENTE"(+) IS NOT NULL)
   7 - access("E"."ESTADO"(+)='CERRADA' AND "E"."FECHA_SALIDA"(+)>=TIMESTAMP' 2026-09-01 
              00:00:00.000000000' AND "E"."FECHA_SALIDA"(+)<TIMESTAMP' 2026-10-01 00:00:00.000000000')
 
Note
-----
   - dynamic statistics used: dynamic sampling (level=2)
   
   
---No existe ningun full access

EXPLAIN PLAN FOR
SELECT
ANIO,
MES,
TOTAL_MINUTOS,
TOTAL_CARGOS,
TOTAL_PAGOS,
ESTADO
FROM CIERRE_MENSUAL
ORDER BY ANIO, MES;

SELECT *
FROM TABLE(DBMS_XPLAN.DISPLAY);

Plan hash value: 2305657404
 
------------------------------------------------------------------------------------------------------
| Id  | Operation                   | Name                   | Rows  | Bytes | Cost (%CPU)| Time     |
------------------------------------------------------------------------------------------------------
|   0 | SELECT STATEMENT            |                        |     2 |   154 |     1   (0)| 00:00:01 |
|   1 |  TABLE ACCESS BY INDEX ROWID| CIERRE_MENSUAL         |     2 |   154 |     1   (0)| 00:00:01 |
|   2 |   INDEX FULL SCAN           | IDX_CIERRE_MENSUAL_UK1 |     2 |       |     1   (0)| 00:00:01 |
------------------------------------------------------------------------------------------------------
 
Note
-----
   - dynamic statistics used: dynamic sampling (level=2)
   
---No existe ningun full access


--Se requiere revisar la volumetría de las tablas para determinar si con el incremento de volumetria el acceso a las tablas por ejemplo instancia no requiere un indice particular.

--se propone un indice sobre la tabla instancia sobre los campos de id_residente y estado, posiblemente se requiera que se agrege por el campo fecha_salida.

-- orden de las columnas id_residente - estado, o id residente, estado , FECHA_SALIDA

-- Para las tabla de alta volumetria se debe de considerar particionamiento sobre los campos de fecha, considerando q hay cierres mensuales la particion sería mensual sin embargo para que 
--este particionamiento funcione a nivel querys se requiere una tabla adicional de control con el nombre de las particiones en el cual se debe referenciar las consultas ya sea en 
--querys normales o en pl/sql para no acceder a la tabla completa.


