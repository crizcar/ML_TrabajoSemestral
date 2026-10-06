# Trabajo semestral BI-ML 2026-1

Proyecto semestral del curso BI-ML (Ingeniería Industrial, Universidad de Concepción).

## Estado

Proyecto: pronóstico semanal de la demanda en los servicios de urgencia del Gran Concepción, con un dashboard por establecimiento para decidir cuándo reforzar el equipo médico. Datos: *Atenciones de Urgencia* (DEIS-MINSAL, datos.gob.cl).

Avance actual (presentación del 15-10-2026): ETL 2023–2026, análisis descriptivo 2023–2025 y baselines en `Trabajo.ipynb`. 2026 queda reservado como test.

## Estructura

```text
Trabajo.ipynb              ETL + análisis descriptivo + baselines (único notebook)
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
2. Abrir `Trabajo.ipynb` desde esta carpeta y ejecutar todas las celdas. La primera vez descarga los zips (~150 MB) y hace el ETL (~2 min); después lee `data/processed/` (`FORZAR_ETL = False`) y tarda segundos.

## Historial de versiones y decisiones

| Versión | Fecha | Cambio | Decisión técnica / metodológica |
|---------|-------|--------|---------------------------------|
| 0.1.0 | 2026-10-01 | Commit inicial del repositorio | Se versiona código y datos en el mismo repositorio, según lo pide el enunciado. |
| 0.2.0 | 2026-10-05 | Notebook de ETL y análisis descriptivo para la presentación de avance | Lectura con `encoding="latin-1"` y `sep=";"`, por bloques, con `IdEstablecimiento` como texto. Filtro de 7 comunas núcleo por `CodigoComuna` (8101, 8102, 8103, 8107, 8108, 8110, 8112 → 26 establecimientos). Objetivo provisional: `IdCausa 1` (total de atenciones de urgencia), sin sumar causas porque hay subtotales. Semana lunes–domingo; se descarta la semana parcial del 30–31 dic (quedan 52). EDA sobre el año completo. Baselines con corte cronológico (test desde 30-09-2024, sin usar) y `TimeSeriesSplit(5, test_size=4)`. El CSV crudo no se sube; se versiona el extracto filtrado en `data/processed/`. **Todo por validar con los profesores.** |
| 0.3.0 | 2026-10-05 | Más años, reestructura del repo y mejoras de calidad | Se suman 2023, 2025 y 2026 (mismo esquema que 2024). **Análisis y validación sobre 2023–2025** (156 semanas ISO) y **2026 como test reservado** (no se mira hasta el final), en lugar del EDA sobre 2024 completo. El ETL lee los zips sin descomprimir y guarda un manifiesto con SHA-256. SAR Los Cerros cambió de código (19-912 → 201018) y se unifica; cada establecimiento usa su nombre más reciente. Las semanas anómalas (fuera del 50–150 % de la mediana móvil de 9 semanas o con < 7 días reportados) y los días con objetivo = 0 se **marcan, no se borran**. Baselines con `TimeSeriesSplit(6, test_size=8)`; se agrega el naive estacional (t−52) y el error relativo (MAE / media). Estructura: `data/raw`, `data/processed`, `resultados/figuras`, `resultados/tablas`. **Por validar con los profesores.** |
