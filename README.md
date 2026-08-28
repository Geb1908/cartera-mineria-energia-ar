# Cartera minera y energética argentina

Dashboard interactivo con **59 proyectos** de minería y Gas \& Oil en Argentina: qué es cada uno, quién lo opera, cuánto invierte, cuándo arranca y qué produce.

[**→ Ver el dashboard**](https://USUARIO.github.io/REPOSITORIO/)

!\[Vista del dashboard](docs/preview.png)

\---

## Por qué existe

La información del sector está desparramada entre reportes técnicos, resoluciones del Boletín Oficial, presentaciones a inversores y prensa especializada. Cada fuente cubre una parte. Este tablero la junta en un solo lugar y la deja comparable.

El dato que más ordena la lectura del sector: **el 66% del capital comprometido corresponde a proyectos que recién arrancan en 2029 o después. Solo el 8% está produciendo hoy.**

## Qué incluye

|||
|-|-|
|**Proyectos**|59 · USD 128.896 millones de inversión declarada|
|**Cobre**|7 proyectos|
|**Litio**|15 proyectos|
|**Oro y plata**|14 proyectos|
|**Hidrocarburos**|8 bloques y áreas|
|**Midstream y GNL**|8 proyectos|
|**Otros RIGI**|7 proyectos|

Secciones:

* **Mapa** de los 59 proyectos, dimensionados por inversión y coloreados por sector
* **Ficha individual** de cada proyecto con operadores, participaciones, cronograma, empleo, estado del RIGI y fuente
* **Gráficos** de inversión, origen del capital por país, cartera RIGI, producción de petróleo por operador, curva proyectada de cobre, capacidad de litio y proyecciones de exportación
* **Cadena de valor**: contratistas, proveedores y financiadores con adjudicaciones publicadas
* **Comparador** de hasta tres proyectos lado a lado
* **Simulador** de sensibilidad de precios de cobre, litio, Brent y GNL
* **Marco regulatorio**: regalías, retenciones y restricciones provinciales
* **Tabla maestra** filtrable, ordenable y exportable a CSV

## Cómo leer los datos

Cada dato lleva una marca de confiabilidad:

|Marca|Significado|Cantidad|
|-|-|-|
|`dato`|Verificado en fuente oficial o reporte técnico|25 proyectos|
|`empresa`|Declarado por la compañía o cámara sectorial|25 proyectos|
|`estimación`|Proyección de terceros o escenario|9 proyectos|

**Los campos sin información pública confiable quedan como `s/d`, no estimados.** Son 146 campos vacíos entre CAPEX, empleo en construcción, empleo en operación, año de arranque y producción anual. Rellenarlos habría sido fácil y habría hecho que la tabla se viera completa, pero un tablero que aparenta una precisión que no tiene es peor que uno con huecos: el hueco te dice dónde no apoyarte.

## Advertencias

Leé esto antes de usar los números para algo que importe.

* **Buena parte de las cifras de producción y exportación futuras son proyecciones**, no hechos. Provienen de las propias empresas, de cámaras sectoriales o del Gobierno.
* **Las proyecciones de exportación no coinciden entre fuentes.** BCRA, CEPH y Rystad están graficadas como series separadas a propósito: el escenario oficial asume que todos los cronogramas se cumplen, las consultoras privadas descuentan demoras y precios más conservadores. No están promediadas.
* **Varios proyectos no tienen decisión final de inversión.** Taca Taca, El Pachón y MARA están en esa situación. La evaluación económica de Vicuña es preliminar, no un estudio de factibilidad.
* **Algunos CAPEX se publican en rangos amplios.** El Pachón entre USD 9.000 y 11.600 millones; Los Azules entre 2.460 y 4.000. Los gráficos usan el punto medio; la ficha muestra el rango completo.
* **El mapa es esquemático.** La proyección es equirectangular con paralelo estándar en 38°S y el contorno está simplificado. Sirve para ubicar espacialmente, no para medir ni para catastro.
* **El simulador es una aproximación gruesa.** Multiplica volumen por precio. No descuenta costos, importaciones, retenciones ni consumo interno. Sirve para dimensionar órdenes de magnitud, no para proyectar la balanza comercial.
* **No hay dato público confiable** sobre la alícuota de retención al gas natural, distinta del esquema del crudo.

**Corte de datos: 27 de agosto de 2026.** Verificá siempre contra el reporte técnico o la resolución original antes de tomar una decisión.

## Cómo está construido

Un único archivo HTML de \~103 KB. **Cero dependencias externas.**

No usa librerías de gráficos ni de mapas: el motor de visualización está escrito en SVG puro dentro del mismo archivo. Eso significa que funciona sin conexión, no se rompe cuando un CDN cambia una versión, y no tiene superficie de ataque por terceros. Las únicas peticiones externas son las tipografías de Google Fonts, que degradan a fuentes del sistema si están bloqueadas.

```
index.html          # el dashboard completo
README.md
docs/preview.png    # captura para el README
```

## Actualizar los datos

Todo vive en un solo bloque al inicio del `<script>`, separado de la lógica de render:

```js
const P = \[
  {id:'vicuna', n:'Vicuña (Josemaría + Filo del Sol)', s:'cobre', prov:'San Juan',
   lat:-29.30, lng:-69.90, op:'BHP + Lundin Mining (50/50)', pais:'Australia / Canadá',
   capex:9700, capexMin:7000, capexMax:18000, etapa:3, ini:2030,
   prod:'395.000 t/a Cu · 711.000 oz Au · 22,2 Moz Ag',
   empC:12000, empO:5000, rigi:'Aprobado', rigiFecha:'16/06/2026', conf:'empresa',
   nota:'...', fuente:'PEA integrada 16/02/2026 · resolución RIGI'},
  // ...
];
```

Editás ese array y el tablero entero se recalcula: KPIs, mapa, gráficos, tabla, comparador y CSV.

Campos:

|Campo|Descripción|
|-|-|
|`s`|Sector: `cobre`, `litio`, `oro`, `hidro`, `mid`, `otros`|
|`etapa`|1 exploración · 2 prefactibilidad · 3 factibilidad · 4 FID · 5 construcción · 6 producción|
|`capex`|USD millones. Usar `capexMin`/`capexMax` cuando la fuente da un rango|
|`conf`|`dato`, `empresa` o `estimacion`|
|`rigi`|`Aprobado`, `En evaluación`, `En preparación`, `Rechazado`, `Bloqueado`, `No presentado`|
|`null`|Deja el campo como `s/d`. **Usalo en vez de estimar**|

Los contratistas se cargan aparte, en `CADENA`, indexados por `id` de proyecto.

## Publicar en GitHub Pages

1. Subí el repo con el archivo nombrado `index.html`
2. Settings → Pages → Source: `Deploy from a branch` → rama `main`, carpeta `/ (root)`
3. Queda publicado en `https://geb1908.github.io/cartera-mineria-energia-ar/`

No necesita build ni configuración adicional.

## Fuentes

Reportes técnicos NI 43-101 y estudios de factibilidad de las compañías, presentaciones a inversores y filings ante SEC y SEDAR, resoluciones del RIGI publicadas en el Boletín Oficial, Secretaría de Minería y Secretaría de Energía de la Nación, cámaras sectoriales, y prensa especializada. Cada ficha indica su fuente.

## Contribuir

Si encontrás un dato mal o desactualizado, abrí un issue con el proyecto, el campo y la fuente que lo corrige. Los aportes con fuente verificable se incorporan.

## Licencia

MIT para el código. Los datos provienen de fuentes públicas y se recomienda verificarlos contra la fuente original antes de usarlos.

