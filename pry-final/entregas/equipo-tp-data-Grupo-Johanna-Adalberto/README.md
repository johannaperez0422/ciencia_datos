# Pipeline analítico de un minimercado

Base de trabajo para la [tarea final del curso](https://github.com/ubuntumb/ciencia_datos/tree/main/pry-final/entregas). El nombre `equipo-minimercado` es provisional: el equipo debe sustituirlo por `equipo-<nombre_del_grupo>` con el código `tp-data-G...` que le corresponda.

## Reproducir desde cero

Se necesita Python 3.12 y [uv](https://docs.astral.sh/uv/). Abrir la terminal en esta carpeta, donde están `pyproject.toml` y `uv.lock`. Estos comandos son para una terminal Bash o Zsh, como la del Codespace:

```bash
uv sync --locked
mkdir -p .dagster
cp orchestration/dagster.yaml .dagster/dagster.yaml
export DAGSTER_HOME="$PWD/.dagster"
export DAGSTER_DISABLE_TELEMETRY=1
uv run --locked dagster job execute -f orchestration/definitions.py -j minimercado
uv run --locked python -m pytest -q
uv run --locked ruff check .
uv run --locked ruff format --check .
```

La instalación necesita acceso a los paquetes. El flujo y las pruebas se ejecutan con archivos locales, sin API ni credenciales. El código desactiva la telemetría de dlt y de dbt; la configuración anterior hace lo mismo para Dagster.

### Ejecutar las etapas por separado

```bash
uv run --locked python generar_datos.py
uv run --locked python pipeline.py
uv run --locked python analisis.py
```

- `data/`: cinco CSV reproducibles con la semilla 42 y 90 días a partir del 1 de enero de 2026.
- `data.duckdb`: tablas de origen en `raw` y modelos en `analytics`. Cada ingesta reemplaza el contenido de las tablas de origen, de modo que volver a cargar los mismos CSV no duplica filas.
- `resultados.json`: estadística diaria, clasificación ABC, inventario, rentabilidad, vendedores y conciliaciones.
- `target/run_results.json`: resultado de los 13 modelos y 50 controles ejecutados por dbt.
- `informe.md`: arquitectura, decisiones, hallazgos y limitaciones para revisión del equipo.

La prueba de integración ejecuta los cuatro activos de Dagster en una carpeta temporal y compara la facturación del resultado con el cálculo independiente a partir del CSV de ventas.

## Preparar la entrega en equipo

1. Confirmar los tres integrantes y el código del grupo con el docente.
2. Partir de la versión actual de `main` del curso en el fork compartido. Crear la rama `pyt/equipo-<nombre_del_grupo>` y colocar la carpeta en `pry-final/entregas/equipo-<nombre_del_grupo>/`.
3. Revisar y adaptar el proyecto entre todos. Cada integrante registra **al menos tres cambios significativos desde su cuenta**. Por ejemplo: generación, consistencia e ingesta; staging, integridad y métricas; orquestación, integración y documentación. El mínimo es nueve commits reales. Esta base se preparó con asistencia de IA y no constituye el historial de contribuciones del equipo.
4. Ejecutar las pruebas sobre la versión del grupo y actualizar el informe con los resultados que verifiquen. Confirmar que el PR hacia `main` de `ubuntumb/ciencia_datos` modifica solo la carpeta del equipo.
5. Cada integrante adjunta el informe final y el enlace al mismo PR en Moodle y confirma el envío.

Conservar en Git los CSV, el código, la configuración, las pruebas y el informe. La base DuckDB y los archivos de ejecución se regeneran con los comandos anteriores.
