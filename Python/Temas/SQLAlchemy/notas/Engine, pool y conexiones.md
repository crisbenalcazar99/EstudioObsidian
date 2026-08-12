---
tags: [nota, python, sqlalchemy]
tema: SQLAlchemy
creado: 2026-08-10
estado: completado
---

# Engine, pool y conexiones

> Resumen ejecutivo. El detalle completo (tablas, diagramas, glosario de ~50 términos) está en el [[../recursos/sqlalchemy-conexiones-dashboard.html|dashboard]].

## Contexto

La mayoría de los incidentes con SQLAlchemy (timeouts de pool, `DetachedInstanceError`, N+1, conexiones agotadas) vienen de confundir estos 4 objetos entre sí o de darles un ciclo de vida equivocado.

## Conceptos clave

- **Engine** = configuración + fábrica del pool. Uno por proceso, vive todo el proceso.
- **Pool** = inventario de conexiones DBAPI reutilizables. Checkout al pedir, checkin (con `ROLLBACK` implícito) al soltar.
- **Connection** = un checkout + una transacción. Dura una operación.
- **Session** (ORM) = identity map + unit of work. Dura una operación de negocio; nunca thread-safe, nunca compartida.

**Reglas de oro:** 1 engine por proceso · 1 session por operación/request · `flush` (escribe en la transacción) ≠ `commit` (confirma).

## Desarrollo

- `create_engine()` no abre conexiones; la primera se abre en la primera consulta.
- El pool es donde vive el 80% de los problemas: vigilar `pool_size + max_overflow` × workers contra el `max_connections` real del servidor.
- La sesión no es thread-safe y `expire_on_commit=True` (default) expira los atributos tras el commit — causa típica de `DetachedInstanceError` si se accede fuera del `with`.
- En async, el lazy loading implícito rompe (`MissingGreenlet`): usar carga eager (`selectinload`) siempre.

## Errores comunes / Gotchas

| Síntoma | Causa | Arreglo |
|---|---|---|
| `TimeoutError: QueuePool limit...` | Sesiones sin cerrar | Usar siempre `with`; subir `pool_size` solo retrasa el fallo |
| `DetachedInstanceError` | Atributo expirado accedido fuera del `with` | `expire_on_commit=False` o carga eager antes de salir |
| `Connection reset by peer` | Servidor/firewall mató conexión ociosa | `pool_recycle` + `pool_pre_ping=True` |
| N+1 queries | Relación lazy dentro de un bucle | `selectinload()` |
| Errores tras arrancar workers | Sockets heredados por `fork()` | `engine.dispose()` en el hijo |

**Nunca:** engine por request · session global/entre hilos · f-strings en SQL.
**Siempre:** un engine por proceso · `with` para toda sesión · parámetros ligados.

## Recursos relacionados

- [[../SQLAlchemy|Índice del tema]]
- [[../recursos/sqlalchemy-conexiones-dashboard.html|Dashboard: Engine, pool, conexiones y sesiones]] — referencia completa (mapa de capas, diagramas de flujo, DBAPI/Dialect/Pool/Engine/Connection/Session/Async/Tests en detalle, tabla de fallos y glosario filtrable).

## Preguntas abiertas

- ¿Qué `pool_size` / `max_overflow` conviene según workers y `max_connections` real del servidor?

## Referencias

- `recursos/sqlalchemy-conexiones-dashboard.html`