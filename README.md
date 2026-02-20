# FastAPI: guía de escalabilidad modular

Este repositorio contiene una guía práctica (en español) para diseñar y evolucionar aplicaciones FastAPI con múltiples módulos sin perder mantenibilidad.

## 📘 Documento principal

- **Guía completa:** [`FASTAPI_ESCALABILIDAD.md`](./FASTAPI_ESCALABILIDAD.md)

## ✅ Qué encontrarás

- Arquitectura por dominio (`users`, `billing`, `inventory`, etc.).
- Separación de capas (`router` / `service` / `repository`).
- Configuración tipada con `pydantic-settings`.
- Inyección de dependencias reutilizable.
- Versionado de API y contratos estables.
- Observabilidad, seguridad y rendimiento.
- Estrategia de pruebas y despliegue.

## 🚀 Uso recomendado

1. Abre la guía principal: [`FASTAPI_ESCALABILIDAD.md`](./FASTAPI_ESCALABILIDAD.md).
2. Toma la sección **Estructura sugerida** como base de carpetas.
3. Adapta cada módulo a tu contexto (dominio, volumen, equipo).

---

Si quieres, en un siguiente paso puedo convertir esta guía en un **esqueleto ejecutable** de FastAPI (con `app/main.py`, routers por módulo, configuración y tests base).
