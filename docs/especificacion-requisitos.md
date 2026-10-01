# Especificación de requisitos

**Sistema:** RockStock
**Autor:** César Méndez
**Versión:** 1.1
**Fecha de la última actualización:** 01/10/2026

---

## 1. Propósito y alcance

**Propósito del documento:**
Establecer los requisitos funcionales y no funcionales que debe cumplir RockStock, sirviendo como base para el diseño, desarrollo y validación del sistema. RockStock actuará como la única fuente de verdad en tiempo real para controlar las entradas, salidas y existencias físicas de láminas, bloques e insumos de piedra natural de la empresa.

**Alcance del sistema:**
* Registrar ingreso de materiales, asegurando que cada unidad sea trazable desde su entrada al almacén con su documentación técnica.
* Descontar stock por mermas o transformación (venta, rotura o descarte natural) para mantener paridad absoluta entre el almacén físico y el sistema.
* Alertar stock mínimo a la gerencia automatizando notificaciones en umbrales críticos.
* Bloquear temporalmente material por reserva de vendedor para gestionar compromisos comerciales y evitar sobreventas.
* Consultar catálogo en tiempo real de forma autónoma e inmediata.

**Fuera del alcance:**
* No habrá facturación ni pasarela de pagos, ya que es una herramienta de gestión interna y no un software contable.
* No incluye logística ni rutas de entrega, terminando su proceso funcional en el registro de salida.
* No se desarrollará un optimizador gráfico de cortes (Nesting).

---

## 2. Usuarios y su contexto

| Usuario | Qué hace hoy sin el sistema | Qué espera del sistema |
|---|---|---|
| Dueña de la empresa | Decide cuándo comprar material basándose en un archivo Excel desactualizado que circula por correo. | Ver existencias actualizadas y confiables para decidir cuándo y qué comprar. |
| Encargado de almacén | Envía el Excel por correo perdiendo el control de la información centralizada. | Registrar entradas y salidas de material de forma rápida y centralizada sin que le quite tiempo de su trabajo diario. |
| Vendedor / atención | Revisa Exceles desactualizados, corriendo el riesgo de ofrecer lo que no hay. | Consultar disponibilidad real antes de vender o comprometer una fecha de entrega con un cliente. |
| Encargado de proyecto | Confía ciegamente en que el material apartado estará en almacén sin un aviso certero. | Confirmar que el material reservado para su proyecto esté efectivamente disponible cuando lo necesite. |

**Conflictos identificados entre usuarios:**
Vendedor vs. Encargado de almacén por reservar material. El vendedor quiere apartar material rápido para asegurar ventas sin estar 100% seguro de la cantidad, mientras que el encargado de almacén requiere un reflejo exacto de la realidad para no dejar a otros proyectos sin material.

---

## 3. Requisitos funcionales

### 3.1 Resumen

| ID | Nombre | Prioridad | Origen |
|---|---|---|---|
| RF-001 | Registro de entrada de material | Imprescindible | Confirmado en entrevista con Almacén, 30 sep |
| RF-002 | Trazabilidad por cortes e indivisibilidad | Imprescindible | Entrevista con Almacén, 30 sep (Mermas) |
| RF-003 | Bloqueo temporal por reserva comercial | Alta | Confirmado en entrevista con Almacén, 30 sep |
| RF-004 | Liberación automática de reservas caducas | Alta | Derivado de entrevista con Almacén, 30 sep |
| RF-005 | Restricción cromática por origen de bloque | Media | Entrevista con Almacén, 30 sep (ID de bloque) |

### 3.2 Fichas

**RF-001 · Registro de entrada de material**

| Campo | Contenido |
|---|---|
| Descripción | El sistema registra el ingreso de láminas, bloques e insumos generando la documentación técnica pertinente. |
| Origen | Confirmado en entrevista con Almacén, 30 sep. |
| Prioridad | Imprescindible |
| Criterio de aceptación | Al ingresar un material, el sistema asigna un identificador único. Si faltan datos técnicos obligatorios, el sistema rechaza el guardado y muestra un error. |
| Relacionado con | RF-005, RNF-USA-001 |

**RF-002 · Trazabilidad por cortes e indivisibilidad**

| Campo | Contenido |
|---|---|
| Descripción | El sistema retira del stock activo el ID original de una lámina al registrarse un corte, y genera identificadores nuevos para las sub-unidades y mermas. |
| Origen | Entrevista con Almacén, 30 sep (Mermas). |
| Prioridad | Imprescindible |
| Criterio de aceptación | Al ejecutar el registro de un corte, el sistema inactiva el ID padre y suma las superficies de los IDs hijos verificando que igualen exactamente a la superficie original. |
| Relacionado con | RF-001 |

**RF-003 · Bloqueo temporal por reserva comercial**

| Campo | Contenido |
|---|---|
| Descripción | El sistema bloquea existencias de material al ser reservadas por un vendedor, restándolas del inventario disponible a la vista general. |
| Origen | Confirmado en entrevista con Almacén, 30 sep. |
| Prioridad | Alta |
| Criterio de aceptación | Al realizar un vendedor la reserva, el material cambia su estado a "reservado" y ya no puede ser seleccionado por otro vendedor. |
| Relacionado con | RF-004, RNF-SEG-001 |

**RF-004 · Liberación automática de reservas caducas**

| Campo | Contenido |
|---|---|
| Descripción | El sistema reintegra automáticamente el material al stock disponible si una reserva temporal no cuenta con orden de venta confirmada tras 48 horas hábiles. |
| Origen | Derivado de entrevista con Almacén, 30 sep (Solución a olvidos de liberación manual). |
| Prioridad | Alta |
| Criterio de aceptación | Al transcurrir exactamente 48 horas hábiles sin una actualización de orden confirmada, el estado del material vuelve a "disponible" sin intervención manual. |
| Relacionado con | RF-003 |

**RF-005 · Restricción cromática por origen de bloque**

| Campo | Contenido |
|---|---|
| Descripción | El sistema obliga a vincular el registro de cada lámina con un ID de Bloque de origen determinado. |
| Origen | Entrevista con Almacén, 30 sep (ID de bloque). |
| Prioridad | Media |
| Criterio de aceptación | Al registrar una lámina nueva, el sistema no permite finalizar la captura si no se ha seleccionado o creado previamente su ID de bloque padre. |
| Relacionado con | RF-001 |

---

## 4. Requisitos no funcionales

### 4.1 Resumen

| ID | Atributo | Nombre | Prioridad | Origen |
|---|---|---|---|---|
| RNF-USA-001 | Usabilidad | Registro ágil de movimientos | Imprescindible | Atributos de calidad (Visión) |
| RNF-SEG-001 | Seguridad | Control estricto de bajas y reservas | Imprescindible | Atributos de calidad (Visión) |
| RNF-REN-001 | Rendimiento | Actualización de catálogo en tiempo real | Alta | Análisis del agujero de comunicación |

### 4.2 Fichas

**RNF-USA-001 · Registro ágil de movimientos**

| Campo | Contenido |
|---|---|
| Atributo de calidad | Usabilidad |
| Descripción | Un usuario de almacén puede registrar la entrada o salida de un material en un máximo de tres pasos desde la pantalla principal. |
| Métrica | Número máximo de clics o transiciones de pantalla requeridos igual a tres. (Justificación: En el entorno de un almacén, si el usuario tiene que hacer más de tres interacciones para un registro rutinario, lo percibirá como una traba burocrática y preferirá anotar en papel para seguir trabajando, causando que el inventario se desincronice). |
| Origen | Derivado de la preocupación del encargado de almacén para no perder tiempo en su día a día. |
| Prioridad | Imprescindible |
| Por qué importa | Si el sistema toma demasiado tiempo para capturar, el encargado abandonará la captura en tiempo real y anotará en papel, desincronizando el inventario. |
| Afecta a | RF-001, RF-002 |

**RNF-SEG-001 · Control estricto de bajas y reservas**

| Campo | Contenido |
|---|---|
| Atributo de calidad | Seguridad |
| Descripción | El sistema solicita credenciales con nivel de gerencia o jefe de almacén para autorizar bajas definitivas de inventario por mermas. |
| Métrica | 100% de los intentos de baja realizados por un usuario nivel vendedor deben ser bloqueados. (Justificación: Permitir que un vendedor modifique el inventario a nivel de bajas definitivas vulnera la confiabilidad de los datos, dando paso a manipular mermas o esconder errores que terminarán generando problemas financieros por datos erróneos). |
| Origen | Atributos de calidad (Visión del producto). |
| Prioridad | Imprescindible |
| Por qué importa | Previene la manipulación no autorizada, reservas fraudulentas y problemas financieros por datos erróneos. |
| Afecta a | RF-002, RF-003 |

**RNF-REN-001 · Actualización de catálogo en tiempo real**

| Campo | Contenido |
|---|---|
| Atributo de calidad | Rendimiento |
| Descripción | Los cambios en la disponibilidad del catálogo se reflejan a todos los usuarios en pantalla en menos de cinco segundos tras completarse una transacción. |
| Métrica | Tiempo de actualización de información y despliegue inferior a 5 segundos. (Justificación: Un tiempo máximo de cinco segundos garantiza la sincronía de la información de almacén, manteniendo un margen aceptable de espera para que el vendedor consulte el sistema sin interrupciones severas en su flujo comercial). |
| Origen | Requerimiento de que sea la única fuente de verdad y solucione el problema de sincronización de Excel. |
| Prioridad | Alta |
| Por qué importa | Si el vendedor consulta un catálogo lento o no sincronizado, venderá productos que ya no existen, reviviendo el problema original. |
| Afecta a | RF-003, RF-004 |

---

## 5. Casos de uso

**Lista de casos de uso del sistema (Ver diagrama en docs/diagramas/casos-de-uso.png):**
1. Registrar entrada de material nuevo (Actor: Encargado de almacén)
2. Registrar baja por rotura o despique (Actor: Encargado de almacén)
3. Registrar corte de lámina y merma (Actor: Encargado de almacén)
4. Consultar catálogo de disponibilidad (Actores: Vendedor, Dueña)
5. Reservar material para venta (Actor: Vendedor)
6. Configurar alertas de stock mínimo (Actor: Dueña)

### Detalle del caso de uso principal

**CU-05 · Reservar material para venta**

**Actor principal:** 
Vendedor

**Objetivo:** 
Apartar temporalmente una o varias láminas del inventario para asegurar el material mientras se confirma la compra con el cliente, evitando que otro vendedor ofrezca la misma pieza.

**Precondición:** 
El vendedor ha iniciado sesión en el sistema y el material requerido aparece con estado "Disponible" en el catálogo.

**Escenario principal:**
1. El vendedor busca el material en el catálogo por tipo de piedra o ID de bloque.
2. El sistema muestra los detalles del material, confirmando que el estado es "Disponible".
3. El vendedor selecciona la lámina y hace clic en "Reservar material".
4. El sistema verifica que el material siga disponible.
5. El sistema cambia el estado de la lámina a "Reservado" y oculta la pieza de la vista general de disponibilidad.
6. El sistema inicia un contador interno de 48 horas hábiles asociado a esa reserva.

**Flujos alternos:**
*   **4a. Condición de carrera (El material ya fue reservado):** Si en el lapso entre que el vendedor vio el catálogo y dio clic en "Reservar", otro vendedor apartó la misma lámina, el sistema bloquea la acción y muestra una alerta: "Este material acaba de ser reservado por otro usuario". El sistema actualiza la pantalla mostrando alternativas del mismo ID de bloque.
*   **4b. Material dado de baja por despique:** El sistema detecta que el encargado de almacén acaba de registrar una rotura en esa pieza hace unos minutos. El sistema bloquea la reserva y notifica: "El material ya no se encuentra en condiciones de venta".

**Postcondición:** 
El material queda bloqueado para otros usuarios, su estado cambia a "Reservado" y se agenda su liberación automática si no se confirma la venta.

**Requisitos que realiza:**
RF-003, RF-004, RNF-REN-001, RNF-SEG-001

---

## 6. Trazabilidad

| Requisito | Origen | Caso de uso | Pantalla del prototipo | Estado |
|---|---|---|---|---|
| RF-001 | Confirmado en entrevista (30 sep) | CU-01 Registrar entrada | Pendiente (Semana 8) | Vigente |
| RF-002 | Entrevista (30 sep - Mermas) | CU-03 Registrar corte | Pendiente (Semana 8) | Vigente |
| RF-003 | Confirmado en entrevista (30 sep) | CU-05 Reservar material | Catálogo / Detalle de pieza | Vigente |
| RF-004 | Entrevista (30 sep - Olvidos) | CU-05 Reservar material | Backend / Alerta de caducidad | Vigente |
| RF-005 | Entrevista (30 sep - ID bloque) | CU-01 Registrar entrada | Pendiente (Semana 8) | Vigente |
| RNF-USA-001 | Atributos de calidad (Visión) | CU-01 Registrar entrada | Pendiente (Semana 8) | Vigente |
| RNF-SEG-001 | Atributos de calidad (Visión) | CU-02 Registrar baja | Pendiente (Semana 8) | Vigente |
| RNF-REN-001 | Agujero de comunicación | CU-04 / CU-05 | Catálogo en vivo | Vigente |

---

## 7. Registro de cambios

| Fecha | Requisito | Qué cambió | Por qué |
|---|---|---|---|
| 30/09/2026 | Todos | Creación inicial de los requisitos y actualización de orígenes | Avance de la entrega de la Especificación de Requisitos y validación con entrevista |
| 01/10/2026 | RNF-REN-001 | Modificación de métrica de 2 a 5 segundos y adición de justificaciones. | Cumplimiento de estándar de atributos y límites verificables |
