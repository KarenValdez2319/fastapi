# Buenas prácticas en FastAPI para escalar aplicaciones con múltiples módulos

Esta guía resume prácticas de arquitectura, operación y calidad para que una aplicación FastAPI crezca de forma ordenada.

## 1) Diseña por dominio, no por tipo de archivo
- Organiza por módulos de negocio (`users/`, `billing/`, `inventory/`, etc.).
- Dentro de cada módulo separa responsabilidades (`router.py`, `service.py`, `repository.py`, `schemas.py`).
- Evita archivos “gigantes” centralizados cuando el sistema crece.

## 2) Mantén capas con responsabilidades claras
- **Router**: validación HTTP, status codes, serialización.
- **Service**: reglas de negocio y orquestación.
- **Repository**: persistencia e integración con fuentes de datos.
- Esta separación mejora testabilidad, legibilidad y escalabilidad del equipo.

## 3) Configuración tipada y centralizada
- Usa `pydantic-settings` para leer variables de entorno.
- Define configuraciones por entorno (dev/staging/prod).
- Evita `os.getenv()` disperso en todo el código.

## 4) Usa DI de FastAPI como patrón estándar
- Declara dependencias para DB session, auth, permisos y clientes externos.
- Reutiliza dependencias entre módulos para reducir duplicación.
- Facilita testing con overrides de dependencias.

## 5) Contratos API estables desde temprano
- Versiona endpoints (`/api/v1/...`) cuando sea necesario.
- Define `response_model` de manera explícita.
- No expongas modelos internos directamente en respuestas públicas.

## 6) Persistencia y transacciones consistentes
- Centraliza transacciones en service/unit-of-work.
- Mantén migraciones con Alembic y política de compatibilidad.
- Separa claramente lectura/escritura si el dominio empieza a exigirlo.

## 7) Async con criterio
- Usa `async` solo cuando la cadena completa de I/O sea asíncrona.
- Evita trabajo pesado dentro de la request.
- Mueve procesos largos a colas (Celery/RQ/Arq) o workers.

## 8) Observabilidad desde el inicio
- Logging estructurado (JSON) con `request_id`/`trace_id`.
- Métricas mínimas: latencia, throughput, tasa de error por endpoint.
- Trazas distribuidas (OpenTelemetry) en flujos críticos.

## 9) Seguridad por defecto
- Valida entrada/salida con Pydantic.
- Limita CORS, aplica rate limiting y timeouts.
- Gestiona secretos fuera del repositorio.
- Estandariza autenticación/autorización (OAuth2/JWT/scopes).

## 10) Estrategia de pruebas para crecer con confianza
- Unit tests en capa service.
- Integration tests en repositorios y base de datos.
- API tests con `TestClient` o `httpx`.
- CI obligatorio en cada PR (lint + typing + tests).

## 11) Rendimiento y operación
- Ajusta workers según CPU/memoria y perfil real de carga.
- Usa caché (por ejemplo Redis) solo en hotspots identificados.
- Define healthchecks (`/health`, `/ready`) y tiempos de timeout.

## 12) Gobierno de módulos
- Define límites y contratos entre módulos para evitar acoplamiento circular.
- Cuando hay mucha interacción, usa eventos internos o mensajería.
- Documenta decisiones relevantes con ADRs.

---

## Estructura sugerida

```text
app/
  main.py
  core/
    config.py
    logging.py
    security.py
  modules/
    users/
      router.py
      service.py
      repository.py
      schemas.py
      models.py
    billing/
      router.py
      service.py
      repository.py
      schemas.py
  infrastructure/
    db.py
    cache.py
    messaging.py
  tests/
    unit/
    integration/
    api/
```

## Regla práctica de refactor
Si un endpoint mezcla validación HTTP + reglas de negocio + SQL + llamadas externas en un único bloque, es señal de refactor inmediato en capas.
