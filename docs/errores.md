# Catálogo de errores · API v1

Los códigos son parte del contrato: no se renombran ni se quitan dentro de v1. Agregar uno nuevo
es un cambio compatible. Un conector que recibe un código que no conoce lo trata como error
genérico de su nivel.

## Formato

Toda respuesta de error usa `application/problem+json` (RFC 9457) con un `code` estable:

```json
{
  "type": "urn:soma:api:error:batch_id_conflict",
  "title": "Ese batch_id ya existe con otro encabezado",
  "status": 409,
  "code": "batch_id_conflict",
  "batch_id": "ventas-2026W45-0001"
}
```

Un `422 validation_error` trae además `errors`, con la ubicación y el motivo de cada problema del
encabezado.

## Errores de request

| `code` | HTTP | Cuándo | Qué hace el conector |
|---|---|---|---|
| `unauthorized` | 401 | Sin clave, clave inválida o revocada | Para y avisa. No reintenta |
| `forbidden_scope` | 403 | La clave no tiene el alcance de esa entidad | Para y avisa |
| `rate_limited` | 429 | Más de 60 requests por minuto por clave | Espera `Retry-After` y reintenta |
| `validation_error` | 422 | El encabezado del lote o de la parte no cumple el contrato (nunca por una línea) | Corrige el conector |
| `payload_too_large` | 413 | Parte con más de 10.000 líneas o más de 5 MB | Partes más chicas |
| `batch_not_found` | 404 | `batch_id` desconocido para esta empresa | — |
| `batch_id_conflict` | 409 | Mismo `batch_id`, o mismo número de parte, con contenido distinto | Usa un `batch_id` nuevo |
| `batch_not_open` | 409 | Parte, dry-run o commit sobre un lote ya cerrado | Consulta el estado |
| `batch_expired` | 410 | Lote abierto más de 24 h sin confirmar | Reenvía con un `batch_id` nuevo |
| `service_unavailable` | 503 | La base de SOMA no acepta escrituras por un momento (mantenimiento, ampliación de disco, reinicio) | Espera `Retry-After` y reintenta la misma llamada: lo ya enviado no se pierde y reenviar no duplica |

## Errores de lote

Al validar, el lote puede quedar **rechazado** (se conservan los datos anteriores) o **retenido**
(espera una decisión de la empresa en la revisión de datos). En ambos casos el código va en
`problem_code` del lote y del `dry-run`.

| `code` | Resultado | Cuándo |
|---|---|---|
| `batch_count_mismatch` | rechazado | Líneas recibidas ≠ `expected_lines` o ≠ `partition_counts` |
| `batch_scope_violation` | rechazado | Una línea cae fuera del alcance declarado (fecha o ubicación) |
| `batch_stale` | rechazado | `extracted_at` anterior al del último lote aplicado sobre ese alcance |
| `batch_quarantine_exceeded` | rechazado | Cuarentena por encima del tope de la empresa (1% inicial) |
| `value_basis_mismatch` | rechazado | `value_basis` del lote distinto al de la empresa |
| `value_basis_undeclared` | rechazado | Ni la empresa ni el lote dicen si el valor es bruto o neto. El primer lote que lo declara lo fija |
| `location_declared_without_lines` | rechazado | Inventario, OC u OP: ubicación declarada sin líneas y fuera de `empty_locations` |
| `as_of_in_future` | rechazado | Fecha de corte posterior a hoy |
| `batch_discarded` | rechazado | Un lote retenido que la empresa descartó en la revisión de datos, o que reemplazó un lote más nuevo que cubre su alcance |
| `deletion_guard_tripped` | retenido | La diferencia borraría más que el umbral de la empresa |
| `volume_anomaly` | retenido | Una semana completa del lote trae menos del 15% de lo esperado (plan aprobado, misma semana del año anterior o promedio de 8 semanas) |
| `unknown_location` | retenido | Trae ubicaciones que SOMA no conoce. Las líneas se guardan y no cuentan para el tope de cuarentena. Mientras la empresa no la defina, no corre ningún motor de la empresa |
| `first_batch_review` | retenido | El alcance tiene datos cargados por archivo: el primer lote de la API sobre ellos se revisa antes de reemplazarlos |

## Errores de línea (cuarentena)

No cambian el status HTTP. Van en `quarantine_sample` con `line`, `part`, `field`, `code` y
`message`.

| `code` | Ejemplo |
|---|---|
| `missing_field` | Falta `value_usd` |
| `invalid_type` | `"quantity": "doce"` |
| `invalid_date` | `"date": "14/09/2026"` (el formato es `YYYY-MM-DD`) |
| `invalid_value` | `"movement": "devolucion"`, `"uom": "doc"` (minúsculas) |
| `non_positive_quantity` | `"quantity": 0` o negativa (la devolución va con `"movement": "return"`) |
| `negative_quantity` | `"quantity_on_hand": -1` |
| `negative_value` | `"value_usd": -5` |
| `field_not_allowed` | Un campo que no existe (`qty`), o `original_sale_date` en una venta |
| `date_order` | `original_sale_date` posterior a `date` |
| `unknown_uom` | `uom` sin conversión definida para ese SKU |
| `duplicate_key` | OC u OP con la misma clave dos veces en el lote (la segunda va a cuarentena) |
