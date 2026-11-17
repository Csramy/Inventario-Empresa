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

---

## 4. Diagrama de flujo con decisiones
Validaciones del ejercicio de reserva y corte/conservación de superficie en RockStock.

```mermaid
flowchart TD
    ini([Inicio])
    disp{Lámina disponible?}
    bloq{Tiene ID de Bloque?}
    apl[Confirmar reserva 48h naturales]
    err1[Error: Reservada o dada de baja por despique]
    err2[Error: Falta ID de Bloque origen]
    fin([Fin])

    ini --> disp
    disp -->|No| err1
    disp -->|Si| bloq
    bloq -->|No| err2
    bloq -->|Si| apl
    err1 --> fin
    err2 --> fin
    apl --> fin

    style apl fill:#ff5d00,color:#fff
```

---

## 5. Diagrama de secuencia
Cómo se pasan los mensajes entre objetos instanciados en orden y en el tiempo al reservar una lámina (CU-05), resolviendo la condición de carrera con una transacción atómica.

```mermaid
sequenceDiagram
    actor Ven as Vendedor
    participant Web as :AppWeb
    participant Inv as :Inventario
    participant Lam as :Lamina
    participant BD as :BaseDeDatos
    participant Cat as :Catalogo

    Ven->>Web: Solicita reservar lámina (idLamina)
    Web->>Inv: bloquearParaReserva(idLamina, idVendedor)
    note right of Inv: Transacción atómica revisa y bloquea al mismo tiempo (Previene carrera)
    
    alt Lámina no disponible (DADA_DE_BAJA o RESERVADA)
        Inv-->>Web: Error (Estado actual)
        Web-->>Ven: Lanza Alerta de error
    else Disponible
        Inv->>Lam: reservar()
        Lam->>Lam: cambiarEstado(RESERVADA)
        Lam-->>Inv: Reserva creada
        Inv->>BD: guardar(Reserva)
        BD-->>Inv: OK
        Inv->>Inv: notificarCambioStock(idLamina)
        note right of Inv: «Observer» avisa a suscriptores
        Inv->>Cat: actualizar(idLamina)
        Cat->>Cat: oscurecerPiezaReservada(idLamina)
        Inv-->>Web: Reserva confirmada
        Web-->>Ven: Muestra pantalla de éxito
    end
```

---

## 6. Diagrama de clases
Atributos, métodos, visibilidad y multiplicidad de las piezas principales de RockStock (aplicando Patrones GoF y Principios SOLID).

```mermaid
classDiagram
    namespace Seguridad {
        class Usuario {
            -idUsuario: string
            -rol: string
            +tienePermiso(accion) boolean
        }
    }

    namespace Comercial_y_Apartados {
        class Catalogo {
            -tiempoMaximoRefrescoSeg: int
            +consultarDisponibilidad() List~Lamina~
            +actualizar(idLamina) void
            -oscurecerPiezaReservada(idLamina) void
        }
        class Reserva {
            -idReserva: string
            -idVendedor: string
            -fechaCreacion: Date
            -horasVigencia: int
            +estaVencida() boolean
            +liberar() void
        }
        class MonitorReservas {
            +revisarVencimientos() void
        }
    }

    namespace Almacen_y_Materiales {
        class EstadoLamina {
            <<enumeration>>
            DISPONIBLE
            RESERVADA
            SALIDA
            DADA_DE_BAJA
            INACTIVA
        }
        class Bloque {
            -idBloque: string ~readOnly~
            -tipoPiedra: string
            -tonoLote: string
            +obtenerOrigen() string
        }
        class Lamina {
            -idLamina: string ~readOnly~
            -superficieM2: double
            -estado: EstadoLamina
            +reservar() Reserva
            +registrarCorte(medidas) List
            +darDeBaja(motivo) void
            +marcarDisponible() void
            -cambiarEstado(nuevoEstado) void
        }
        class Merma {
            -idMerma: string ~readOnly~
            -superficieM2: double
        }
        class Inventario {
            -ultimaActualizacion: Date
            +registrarIngreso(lamina) boolean
            +procesarSalida(movimiento, lamina) boolean
            +bloquearParaReserva(idLamina, idVendedor) boolean
            -notificarCambioStock(idLamina) void
        }
        class Movimiento {
            <<abstract>>
            #idMovimiento: string
            #fecha: Date
            +validarPermisos(usuario) boolean
            +aplicarMovimiento(lamina)* void
        }
        class SalidaPorVenta {
            +aplicarMovimiento(lamina) void
        }
        class TransformacionPorCorte {
            -superficieResultanteM2: double
            +validarConservacionM2(lamina) boolean
            +aplicarMovimiento(lamina) void
        }
        class Baja {
            -motivo: string
            +aplicarMovimiento(lamina) void
        }
    }

    namespace Abastecimiento_y_Alertas {
        class AlertaStock {
            -tipoPiedra: string
            -umbralMinimoM2: double
            +evaluarUmbral(m2Actuales) boolean
            +actualizar(idLamina) void
        }
        class INotificador {
            <<interface>>
            +enviarAlertaStock(mensaje) void
        }
        class NotificadorCorreo {
            +enviarAlertaStock(mensaje) void
        }
    }

    class ObservadorStock {
        <<interface>>
        +actualizar(idLamina) void
    }

    Bloque "1" --> "1..*" Lamina : origina
    Lamina "1" *-- "0..*" Reserva : bloqueada por
    note for Reserva "Máximo 1 reserva activa a la vez"
    Lamina "1" --> "0..*" Merma : genera al cortarse
    Inventario "1" --> "0..*" Lamina : controla
    Inventario "1" --> "0..*" Movimiento : registra
    Catalogo "1" --> "0..*" Lamina : muestra
    Inventario "1" --> "0..*" ObservadorStock : «Observer» avisa a
    ObservadorStock <|.. Catalogo : implementa
    ObservadorStock <|.. AlertaStock : implementa
    AlertaStock "1" --> "1" INotificador : usa
    INotificador <|.. NotificadorCorreo : «Adapter»
    Movimiento <|-- SalidaPorVenta
    Movimiento <|-- TransformacionPorCorte
    Movimiento <|-- Baja
    MonitorReservas "1" --> "0..*" Reserva : libera vencidas
    Usuario "1" --> "0..*" Movimiento : autoriza
    Usuario "1" --> "0..*" Reserva : solicita
```

---

## 7. Diagrama de estados
Ciclo de vida de una Lámina en RockStock enlazado con las operaciones reales que disparan los cambios de estado.

```mermaid
stateDiagram-v2
    [*] --> DISPONIBLE: registrarIngreso(lamina)
    
    DISPONIBLE --> RESERVADA: reservar()
    RESERVADA --> DISPONIBLE: marcarDisponible() [caduca 48h naturales]
    RESERVADA --> SALIDA: aplicarMovimiento(SalidaPorVenta)
    
    DISPONIBLE --> INACTIVA: aplicarMovimiento(TransformacionPorCorte)
    
    DISPONIBLE --> DADA_DE_BAJA: darDeBaja(motivo)
    RESERVADA --> DADA_DE_BAJA: darDeBaja(motivo)
    
    SALIDA --> [*]
    INACTIVA --> [*]
    DADA_DE_BAJA --> [*]
```

---

## 8. Diagrama entidad-relación
Las tablas de la base de datos de RockStock y cómo se relacionan en su esquema físico.

```mermaid
erDiagram
    BLOQUE ||--o{ LAMINA : origina
    LAMINA ||--o| RESERVA : bloquea
    LAMINA ||--o{ MOVIMIENTO : registra
    MATERIAL ||--o| ALERTA_STOCK : vigila

    BLOQUE {
        string id_bloque
        string nombre_piedra
        string tono_lote
    }
    LAMINA {
        string id_lamina
        string id_bloque
        decimal superficie_m2
        string estado
    }
    RESERVA {
        int id_reserva
        string id_lamina
        datetime fecha_inicio
        datetime fecha_expiracion
    }
    MOVIMIENTO {
        int id_movimiento
        string tipo_movimiento
        decimal m2_afectados
        datetime fecha
    }
```
