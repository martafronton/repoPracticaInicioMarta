# Proyecto: Docker + Flask + Pre-commit

## Descripción
Práctica para contenerizar una aplicación Flask (producción y desarrollo), ejecutar tests con `pytest`, formatear con `black` y asegurar calidad con un hook `pre-commit` que ejecuta Black y los tests antes de cada commit.

---

## Estructura
```
app.py
requirements.txt
Dockerfile
Dockerfile.dev
docker-compose.yml
.dockerignore
pytest.ini
tests/
.git/hooks/pre-commit
README.md
```

---





### 1) Construir y ejecutar (producción)
```bash
docker build -t mi-app:1.0 .
docker run --rm -p 8000:8000 mi-app:1.0
```
- Ver en navegador: `http://localhost:8000`



### 2) Entorno de desarrollo (docker-compose)
```bash
docker compose build dev
docker compose run --rm -T dev python --version
docker compose run --rm -T dev bash   # para entrar a la shell del contenedor (opcional)
```
- `build dev` construye la imagen `Dockerfile.dev`.
- `run --rm -T` ejecuta comandos en un contenedor efímero del servicio `dev`.

---

### 3) Tests (dentro del contenedor dev)
```bash
docker compose run --rm -T dev pytest
```
- Output esperado: `1 passed` (o el número de tests que tengas).
- Si aparece una advertencia `No files were found in testpaths`, revisa `pytest.ini` y la ubicación de `tests/`.

---

### 4) Formateo con Black
```bash
docker compose run --rm -T dev black --check .
docker compose run --rm -T dev black .
```
- `--check`: solo verifica, no modifica. Si devuelve `would reformat ...` significa que hay archivos que necesitan ser reformateados.
- Ejecuta `black .` para arreglar automáticamente.

---

### 5) Probar hook pre-commit (flujo)
1. Introduce un fallo (formato o test) intencionado en un `.py`.
2. Intenta hacer commit:
   ```bash
   git add .
   git commit -m "test: probar pre-commit"
   ```
3. Resultado esperado: el script `pre-commit` se ejecuta y **bloquea** el commit si hay errores (muestra `Código mal formateado` o `Algunos tests fallaron.`).
4. Arregla los errores (ej. `docker compose run --rm -T dev black .` o corrige el test) y repite el commit.

---



---
