# Trabajo semestral BI-ML 2026-1

Proyecto semestral del curso BI-ML (Ingeniería Industrial, Universidad de Concepción).

## Estado

Proyecto: pronóstico semanal de la demanda en los servicios de urgencia del Gran Concepción, con un dashboard por establecimiento para decidir cuándo reforzar el equipo médico. Datos: *Atenciones de Urgencia* (DEIS-MINSAL, datos.gob.cl).

Avance actual (presentación del 15-10-2026): ETL 2023–2026, **análisis descriptivo sobre toda la data (2023–2026, hasta el 27-09-2026)** y baselines en `Trabajo_V2.ipynb`. Para el modelado se aparta como test un tramo final de 12 semanas (06-07 a 27-09-2026); el EDA sí lo incluye.

## Estructura

```text
Trabajo.ipynb              versión anterior (v0.3.0): EDA solo 2023–2025 y 2026 como test; ya no coincide con resultados/
Trabajo_V2.ipynb           versión vigente: código legible y EDA sobre toda la data (genera data/processed y resultados/)
requirements.txt           dependencias con versión
data/
  raw/                     zips anuales del DEIS (no versionados) + README.md (manifiesto con SHA-256) + diccionario de datos
  processed/               extracto filtrado Gran Concepción (diario y semanal), total país por región, perfil de archivos
resultados/
  figuras/                 fig1…fig4 (PNG, 200 dpi, con nota de fuente)
  tablas/                  tabla1…tabla5 y anexo_* (CSV, UTF-8)
```

## Fuentes de datos

| Archivo | Origen |
|---|---|
| `AtencionesUrgencia{2023,2024,2025,2026}.zip` | DEIS-MINSAL, `https://repositoriodeis.minsal.cl/SistemaAtencionesUrgencia/AtencionesUrgencia{AÑO}.zip` (catálogo: datos.gob.cl, conjunto "Atenciones de urgencia de la red pública de Salud") |
| `diccionario-de-datos-atencionesurgencia.xlsx` | datos.gob.cl, mismo conjunto |

El detalle (tamaño, fecha de descarga y SHA-256) está en `data/raw/README.md`. El archivo 2026 lo actualiza el DEIS a diario.

## Cómo ejecutar

1. `pip install -r requirements.txt`
2. Abrir `Trabajo_V2.ipynb` desde esta carpeta y ejecutar todas las celdas. La primera vez descarga los zips (~150 MB) y hace el ETL (~2 min); después lee `data/processed/` (`FORZAR_ETL = False`) y tarda segundos.

## Historial de versiones y decisiones

| Versión | Fecha | Cambio | Decisión técnica / metodológica |
|---------|-------|--------|---------------------------------|
| 0.1.0 | 2026-10-01 | Commit inicial del repositorio | Se versiona código y datos en el mismo repositorio, según lo pide el enunciado. |
| 0.2.0 | 2026-10-05 | Notebook de ETL y análisis descriptivo para la presentación de avance | Lectura con `encoding="latin-1"` y `sep=";"`, por bloques, con `IdEstablecimiento` como texto. Filtro de 7 comunas núcleo por `CodigoComuna` (8101, 8102, 8103, 8107, 8108, 8110, 8112 → 26 establecimientos). Objetivo provisional: `IdCausa 1` (total de atenciones de urgencia), sin sumar causas porque hay subtotales. Semana lunes–domingo; se descarta la semana parcial del 30–31 dic (quedan 52). EDA sobre el año completo. Baselines con corte cronológico (test desde 30-09-2024, sin usar) y `TimeSeriesSplit(5, test_size=4)`. El CSV crudo no se sube; se versiona el extracto filtrado en `data/processed/`. **Todo por validar con los profesores.** |
| 0.3.0 | 2026-10-05 | Más años, reestructura del repo y mejoras de calidad | Se suman 2023, 2025 y 2026 (mismo esquema que 2024). **Análisis y validación sobre 2023–2025** (156 semanas ISO) y **2026 como test reservado** (no se mira hasta el final), en lugar del EDA sobre 2024 completo. El ETL lee los zips sin descomprimir y guarda un manifiesto con SHA-256. SAR Los Cerros cambió de código (19-912 → 201018) y se unifica; cada establecimiento usa su nombre más reciente. Las semanas anómalas (fuera del 50–150 % de la mediana móvil de 9 semanas o con < 7 días reportados) y los días con objetivo = 0 se **marcan, no se borran**. Baselines con `TimeSeriesSplit(6, test_size=8)`; se agrega el naive estacional (t−52) y el error relativo (MAE / media). Estructura: `data/raw`, `data/processed`, `resultados/figuras`, `resultados/tablas`. **Por validar con los profesores.** |
| 0.3.1 | 2026-10-06 | `Trabajo_V2.ipynb`: versión legible del notebook | Mismo análisis que `Trabajo.ipynb`, con nombres de variables más descriptivos, el ETL separado en funciones, comentarios por paso y un glosario de variables. Sin cambios de método: tablas CSV, figuras y parquet salen idénticos byte a byte (también con `FORZAR_ETL = True`). `Trabajo.ipynb` se conserva como referencia. |
| 0.4.0 | 2026-10-06 | `Trabajo_V2.ipynb`: el EDA usa toda la data (2023–2026) y el test pasa a ser un tramo final | **EDA sobre 2023–2026 completo** (195 semanas ISO, 26 establecimientos; 2026 parcial hasta la semana 39), por pedido del grupo. El **test reservado** son las 12 semanas del 06-07 al 27-09-2026, fijadas por fecha (`INICIO_TEST_SEMANA`) porque el DEIS actualiza 2026 a diario; baselines y modelos usan solo `Y_modelo` (183 semanas hasta el 05-07-2026), con `TimeSeriesSplit(6, test_size=8)`. La semana del 28-09 al 04-10-2026 se descarta porque estaba a medio cargar (81 % de los establecimiento-días; el 04-10 informaron 13 de 26): regla `COBERTURA_MIN_SEMANA = 0,95` para las semanas finales. Limitación: en clase se describe solo el *train* antes de modelar, y aquí el EDA incluye el test; además el test (jul–sep) no cubre el pico de invierno completo. Variación 2026 vs 2025 se calcula sobre las mismas semanas (1–39). `Trabajo.ipynb` queda como versión anterior y sus resultados ya no coinciden. **Por validar con los profesores.** |
| 0.5.0 | 2026-10-07 | Rumbo de modelado y primer ensayo lineal (`Trabajo_V2.ipynb`, sección 16b) | **Modelos del trabajo: regresión lineal y XGBoost**, comparados en un solo scoreboard contra los baselines; se dejan fuera random forest, K-means y PCA. **Un modelo global** para los 26 establecimientos (una fila por establecimiento y semana) en vez de uno por serie. Primer ensayo, exploratorio: una **regresión lineal con el tipo de establecimiento, la estación del año (por mes del lunes de la semana) y la semana anterior**, ajustada con todo `Y_modelo` dentro de un `Pipeline`; se revisa la **autocorrelación de sus errores** (por rezago, por establecimiento y del total) y la prueba de Ljung-Box a 8 rezagos (`tabla6_autocorrelacion_residuos.csv`, `tabla6b_residuos_por_establecimiento.csv`, `fig5_autocorrelacion_residuos.png`). No se usa Durbin-Watson porque con la semana anterior como variable queda sesgado hacia 2. No se compara con los baselines, no se afina y el test no se toca. Pendiente: decidir cómo tratar la autocorrelación que quede, XGBoost con Optuna, test una sola vez e importancia de variables. **Por validar con los profesores** (alcance de los modelos; `TimeSeriesSplit` en lugar de K-fold aleatorio). |
| 0.6.0 | 2026-10-07 | Horizonte del pronóstico fijado en 4 semanas (`HORIZONTE = 4`) | **Se pronostica la semana t + 4 con lo conocido en t** (un valor, estrategia directa); la trayectoria de las 4 semanas queda para después. El desafío del trabajo: **el modelo más preciso para predecir atenciones con la data existente**; sin bases externas ni conversión a médicos por ahora. Cambian las Tablas 5, 5b, 6 y 6b y la Figura 5; el naive de «última semana» pasa a llamarse «Último valor conocido (naive)». A 4 semanas el mejor baseline es el naive estacional (t−52), MAE 84,1 ± 14,5 (9,4 %), frente a 99,6 ± 21,8 (11,3 %) del último valor conocido, que solo gana en los hospitales (Tabla 5b). La regresión del primer ensayo usa ahora el dato de hace 4 semanas (`lag_h`): R² 0,903, coeficiente 0,874. Con este horizonte, la autocorrelación de los errores en los rezagos 1 a 3 es esperable por construcción (errores de semanas vecinas comparten información); se mira desde el rezago 4 (−0,16 la mediana) y a 52 semanas (0,33). Ljung-Box rechaza en los 26 establecimientos, en parte por esa misma razón. Se agrega la columna `ACFh_errores` a la Tabla 6b. Sin cambios en el test (no se usa). **Por validar con los profesores** (horizonte). |
