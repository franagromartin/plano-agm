# Plano de fincas · Agromartín

Página web (un solo `index.html` con Leaflet) con las fincas activas de Agromartín y sus parcelas georreferenciadas sobre:

- la ortofoto PNOA y el mapa base del IGN;
- el Catastro, como capa opcional.

Está adaptada de la demo [franagromartin/plano-demo](https://franagromartin.github.io/plano-demo/), que no se ha modificado.

## Modo actual: plantación de fresa

Ahora mismo la página muestra la **plantación de fresa de la campaña en curso**: solo las fincas de fresa, sin arándano, mora ni frambuesa.

- Cada parcela se colorea con la variedad plantada.
- Las parcelas que aún no se han plantado salen en gris rayado.
- El panel enseña las plantas plantadas frente a las previstas y el % de avance.
- La ficha de cada parcela incluye fechas, lomo, plantas/ha, la campaña anterior y el replante.

Para generar los datos de este modo:

```
py -3 scripts\exportar_plano_fincas_web.py C:\plano-fincas-agromartin --berries
```

Se actualiza solo: la tarea programada «AGROMARTIN - Plano web plantacion» lanza `scriptsctualizar_plano_web.bat` cada 2 horas (de 9:00 a 22:00). Solo sube un commit si cambian los datos.

Sin `--berries` se exportan todas las fincas activas, solo con superficie y cultivo.

## Qué enseña

**Panel de fincas**
- Filtro por familia de cultivo: Fresa, Arándano, Frambuesa, Mora, Hueso, Cítricos y Kaki.
- Totales de ha y nº de parcelas.
- Lista de fincas activas que aún no están dibujadas.

**Plano**
- **Vista general:** una chincheta por finca, con el color de su familia de cultivo.
- **Desde zoom 14:** el contorno de la finca y cada parcela coloreada por su cultivo, con su número.

**Fichas**
- **Ficha de finca:** término municipal, proyecto ERP, cultivos, superficie y tabla de parcelas. Pulsa una fila para ir a esa parcela.
- **Ficha de parcela:** cultivo, variedad, superficie y coordenadas.
- **«Cómo llegar»:** abre Google Maps con la ruta hasta la finca o la parcela.

**Buscador:** finca, parcela, cultivo, variedad o proyecto.

## Datos

`datos/fincas.js` lo genera el ERP. No se edita a mano. Para regenerarlo, desde `aplicacion_costes`:

```
py -3 scripts\exportar_plano_fincas_web.py C:\plano-fincas-agromartin
```

| Dato | Origen |
|---|---|
| Fincas | `Costes.dbo.fincas` con `visible = 1` |
| Geometría vigente | `GEO_HISTORIA`, capa PARCELA, polígonos simplificados a unos 0,3 m |
| Superficie canónica | `SUP_FINCA` / `SUP_PARCELA`; las subparcelas (108A…) sin superficie propia usan la del dibujo |
| Cultivo y variedad | `DatosCompletos`, y si falta, `cultivos_por_finca` |
| Término municipal | `GEO_SIGPAC_RECINTO` |

Las fincas I+D no se publican, porque son confidenciales.

Funciona abriendo `index.html` directamente, sin servidor. Necesita internet para cargar las teselas del IGN y Leaflet.
