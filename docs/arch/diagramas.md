# Diagramas de ejemplo mermaid

## 1. Diagrama de flujo: registro de abono

```mermaid
flowchart TD
    ini([Inicio])
    val{Monto mayor a cero?}
    exc{Excede el saldo?}
    apl[Aplicar el abono]
    rec[Imprimir recibo]
    err1[Monto invalido]
    err2[Excede el saldo]
    fin([Fin])

    ini --> val
    val -->|Monto invalido| err1
    val -->|Si| exc
    exc -->|Si| err2
    exc -->|No| apl
    err2 --> fin
    apl --> rec
    rec --> fin

    style apl fill:#ff5d00,color:#fff
```

## 2. Diagrama de secuencia: registro de un abono

```mermaid
sequenceDiagram
    actor Rec as Recepcionista
    participant Web as App web
    participant Cob as Cobros
    participant Age as Agenda
    participant BD as Base de datos
    participant Not as Notificador

    Rec->>Web: Registra un abono de 800
    Web->>Cob: abonar(cuenta, 800)
    Cob->>Age: Que servicio se atendio?
    Age-->>Cob: Consulta general
    Cob->>BD: Guarda el pago
    Cob->>Not: Enviar recibo
    Cob-->>Web: Pago registrado
    Web-->>Rec: Muestra el nuevo saldo
```

## 3. Diagrama de clases

```mermaid
classDiagram
    class CuentaDePaciente {
        -saldo: double
        -pagos: List~Pago~
        +abonar(monto) string
        +consultarSaldo() double
    }

    class Pago {
        -monto: double
        -fecha: Date
    }

    class Paciente {
        -nombre: string
        -especie: string
    }

    class Veterinario {
        -cedula: string
    }

    Veterinario "1" --> "*" Paciente : atiende
    Paciente "1" --> "1" CuentaDePaciente : posee
```

## 4. Diagrama de estados: ciclo de vida de una cita

```mermaid
stateDiagram-v2
    [*] --> Agendada
    Agendada --> Confirmada: confirma por WhatsApp
    Agendada --> Cancelada: se cancela a tiempo
    Confirmada --> EnEspera: llega a la clinica
    EnEspera --> Atendida: pasa a consultorio
    Confirmada --> NoAsistio: no se presenta
    Atendida --> [*]
    Cancelada --> [*]
```

## 5. Diagrama entidad-relación

```mermaid
erDiagram
    PACIENTE ||--o{ CITA : agenda
    PACIENTE ||--|| CUENTA : tiene
    CUENTA ||--o{ PAGO : registra
    PAGO ||--|| RECIBO : emite

    PACIENTE {
        int id
        string nombre
        string especie
    }
    CITA {
        int id
        date fecha
        string motivo
    }
    PAGO {
        int id
        decimal monto
        date fecha
    }
    RECIBO {
        int id
        string folio
    }
```

## 6. Diagrama de contexto

```mermaid
flowchart TD
    rec([Recepcionista])
    vet([Veterinario])
    due([Dueno de mascota])
    sis[Sistema de la clinica]
    lab[Laboratorio externo]
    pago[Pasarela de pagos]

    rec -->|agenda citas y cobra| sis
    vet -->|consulta expedientes| sis
    due -->|consulta citas| sis
    sis -->|envia estudios| lab

    style sis fill:#ff5d00,color:#fff
```

## 7. Diagrama de contenedores

```mermaid
flowchart TD
    rec([Recepcionista])

    subgraph sistema[Sistema de la clinica]
        web[App web<br/>React]
        api[API<br/>Node.js]
        db[(Base de datos<br/>PostgreSQL)]
    end

    lab[Laboratorio externo]
    correo[Servicio de correo]

    rec -->|usa| web
    web -->|HTTPS/JSON| api
    api -->|SQL| db
    api -->|SMTP| correo

    style api fill:#ff5d00,color:#fff
```

## 8. Diagrama de componentes

```mermaid
flowchart TD
    web[App web]

    subgraph api[API del sistema]
        agenda[Agenda<br/>citas y horarios]
        cobros[Cobros<br/>cuentas y pagos]
        exp[Expedientes<br/>historial clinico]
        notif[Notificador<br/>interfaz de envio]
        rep[Reportes<br/>corte de caja]
    end

    db[(Base de datos)]
    correo[Servicio de correo]

    web --> agenda
    web --> cobros
    web --> exp
    web --> rep
    cobros -->|pregunta que se atendio| agenda
    cobros -->|mandarCorreo| notif
    notif --> correo
    agenda --> db
    cobros --> db

    style cobros fill:#ff5d00,color:#fff
```
