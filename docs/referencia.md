---
hide:
  - toc
---

# Referencia OpenAPI · v1

El contrato formal de la API (OpenAPI 3.1). Es el mismo que valida el servidor de SOMA.
Descarga: [`openapi/v1.json`](openapi/v1.json).

<div id="redoc-container"></div>
<script src="https://cdn.jsdelivr.net/npm/redoc@2.1.5/bundles/redoc.standalone.js"></script>
<script>
  Redoc.init("../openapi/v1.json", { hideDownloadButton: false, expandResponses: "200,201,202",
    theme: { colors: { primary: { main: "#00A38C" } } } }, document.getElementById("redoc-container"));
</script>
