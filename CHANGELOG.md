# Changelog

Cada cambio del contrato de la API de SOMA, con su fecha y su tipo:

- **Compatible**: agrega algo sin romper a quien ya integró. Sigue en la misma versión mayor.
- **Mayor**: rompe algo y sale como una versión nueva (`/v2`). La anterior sigue funcionando al
  menos seis meses.

Cada versión tiene su [release](https://github.com/SomaEngine/soma-api-docs/releases) con estas
mismas notas.

## v1.0.0-draft · 2026-11-04

Primer borrador público del contrato v1. Puede recibir ajustes hasta `1.0.0`.

**Disponible en el ambiente de pruebas (ventas)**

- Clave de API por empresa y ambiente (`soma_test_…` / `soma_live_…`), con alcances por entidad.
- `GET /v1/meta`: ubicaciones, conversiones de unidad, unidad de período y base del valor.
- Lote en partes: abrir (`POST /v1/ingest/batches`), enviar partes (`…/parts`), probar sin aplicar
  (`…/dry-run`), consultar (`GET /v1/ingest/batches/{id}`) e historial (`GET /v1/ingest/batches`).
- `dry-run` con la diferencia contra lo que SOMA tiene (nuevas, modificadas, borradas, sin
  cambio), todos los problemas encontrados y los códigos sin ficha.

**Publicado en el contrato, todavía no disponible**

- Confirmar el lote (`…/commit`) y el atajo de una llamada (`POST /v1/ingest/{entidad}`).
- `GET /v1/data-health`.
- Lotes de inventario, órdenes de compra y órdenes de producción.

**Reglas del contrato**

- Dentro del alcance declarado, lo que no viene cuenta como cero.
- Idempotencia por `batch_id` y `part_number`; `extracted_at` con zona horaria; conteos
  declarados (`expected_lines`, `partition_counts`).
- Valores en USD y sin IVA. Bruto o neto lo fija la empresa una vez.
- Unidad base por SKU; `uom` opcional con conversión.
- Errores `application/problem+json` con códigos estables. Retención del primer lote sobre datos
  cargados por archivo (`first_batch_review`) y de lotes con ubicaciones desconocidas
  (`unknown_location`).
