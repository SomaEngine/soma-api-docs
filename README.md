# API de SOMA

Documentación pública de la API de [SOMA Engine](https://somaengine.co), la plataforma de
planificación de la cadena de suministro (pronóstico de demanda, reposición y S&OP).

La API permite que el sistema de una empresa (su ERP, su punto de venta o un conector) envíe a
SOMA sus datos operacionales: **ventas, inventario, órdenes de compra en tránsito y órdenes de
producción en curso**. Es la misma para todos los clientes.

| | |
|---|---|
| **Sitio de documentación** | https://somaengine.github.io/soma-api-docs/ |
| **Cómo funciona una integración** (visión general) | [`docs/index.md`](docs/index.md) |
| **Guía técnica v1** | [`docs/guia/v1.md`](docs/guia/v1.md) |
| **Catálogo de errores** | [`docs/errores.md`](docs/errores.md) |
| **Contrato formal (OpenAPI 3.1)** | [`openapi/v1.json`](openapi/v1.json) |
| **Cambios por versión** | [`CHANGELOG.md`](CHANGELOG.md) y [Releases](https://github.com/SomaEngine/soma-api-docs/releases) |

## Estado

Versión **`1.0.0-draft`**: el contrato está publicado y el envío de ventas funciona en el
ambiente de pruebas. El contrato puede recibir ajustes hasta la versión `1.0.0`; cada cambio
queda en el changelog y en su release.

Para recibir un aviso con cada versión nueva: **Watch → Custom → Releases** en este repositorio.

## Versiones

- La versión mayor va en la URL de la API (`/v1/…`). Dentro de una versión mayor solo se
  agregan cosas; un cambio que rompe es una versión mayor nueva, y la anterior sigue
  funcionando al menos seis meses.
- Cada versión del contrato es un tag de este repositorio (`v1.0.0`, `v1.1.0`, …) con su
  release y sus notas.

## Contacto

Para obtener una clave de API de pruebas o integrar tu empresa, escríbenos desde
[somaengine.co](https://somaengine.co).

---

Este repositorio contiene solo documentación. El código de SOMA es privado. Todos los datos de
los ejemplos son ficticios.
