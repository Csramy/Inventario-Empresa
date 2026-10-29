# Diagramas de Arquitectura (Modelo C4) · RockStock

**Sistema:** RockStock
**Autor:** César Méndez
**Curso:** Ingeniería de Software I · SIS3407

---

## 1. Nivel 1 · Diagrama de contexto
El sistema completo, quién lo usa y con quién habla (sin tecnología).

```mermaid
flowchart TD
    alm([Encargado de almacén])
    ven([Vendedor])
    due([Dueña])

    sis[Sistema RockStock]
    correo[Servicio de correo externo]

    alm -->|registra entradas, cortes y despiques| sis
    ven -->|consulta catálogo y reserva láminas| sis
    due -->|consulta disponibilidad y configura umbrales| sis
    sis -->|envía alertas de stock crítico| correo

    style sis fill:#ff5d00,color:#fff
```

---

## 2. Nivel 2 · Diagrama de contenedores
Las piezas que se despliegan y se ejecutan por separado, usando `subgraph` para dibujar la frontera del sistema.

```mermaid
flowchart TD
    alm([Encargado de almacén])
    ven([Vendedor])
    due([Dueña])

    subgraph sistema[Sistema RockStock]
        web[App web<br/>React]
        api[API<br/>Node.js]
        db[(Base de datos<br/>PostgreSQL)]
    end

    correo[Servicio de correo externo]

    alm -->|usa| web
    ven -->|usa| web
    due -->|usa| web
    web -->|HTTPS/JSON| api
    api -->|lee y escribe| db
    api -->|SMTP/HTTPS| correo

    style api fill:#ff5d00,color:#fff
```

---

## 3. Nivel 3 · Diagrama de componentes
Lo que hay dentro del contenedor principal (`API del sistema`): los tres módulos de negocio y la interfaz `Notificador`.

```mermaid
flowchart TD
    web[App web]

    subgraph api[API del sistema]
        almacen[Almacén y Materiales<br/>bloques, láminas, cortes y despiques]
        comercial[Comercial y Apartados<br/>catálogo y reservas 48h]
        abast[Abastecimiento y Alertas<br/>umbrales y stock mínimo]
        notif[Notificador<br/>interfaz de envío]
    end

    db[(Base de datos)]
    correo[Servicio de correo]

    web --> almacen
    web --> comercial
    web --> abast
    comercial -->|consulta y aparta láminas| almacen
    almacen -->|anuncia cambio de stock| comercial
    almacen -->|anuncia cambio de stock| abast
    abast -->|enviarAlertaStock| notif
    notif --> correo
    almacen --> db
    comercial --> db
    abast --> db

    style almacen fill:#ff5d00,color:#fff
```
