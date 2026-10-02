# Especificación de Requisitos · RockStock

**Sistema: RockStock**
**Autor: César Méndez**
**Fecha de la última actualización: 01/10/2026** 
**Repositorio: Inventario-Empresa**

---

## 1. Propósito y Alcance
*   **Propósito:** RockStock es un sistema de información a la medida que permite a una empresa de superficies de piedra natural y tecnológica controlar su inventario, registrando qué entra al almacén, qué sale (por transformación o venta), y manteniendo las existencias en tiempo real.
*   **Dentro del alcance:** Registrar ingresos de materiales con trazabilidad de su entrada al almacén, descontar stock por bajas y mermas para mantener paridad física, alertar stock mínimo a gerencia, bloquear material por reserva de vendedores, y proveer consulta de catálogo de disponibilidad en tiempo real.
*   **Fuera del alcance explícito:** El sistema no incluye módulos de facturación ni pasarelas de pago, no gestiona logística ni rutas de entrega, y excluye optimizadores gráficos de cortes (Nesting) dentro de las láminas.

## 2. Usuarios y Contexto
*   **Dueña de la empresa:** Necesita ver existencias actualizadas y confiables para tomar decisiones de compra inteligente.
*   **Encargado de almacén:** Necesita registrar entradas y salidas de forma rápida y centralizada sin que le quite tiempo operativo.
*   **Vendedor:** Requiere consultar disponibilidad real antes de ofrecer o comprometer inventario con un cliente.
*   **Conflicto principal identificado:** El vendedor busca reservar material rápido para no perder clientes, mientras el encargado de almacén requiere paridad exacta para evitar inventario inmovilizado o "stock fantasma" que ensucia el almacén.

## 3. Requisitos Funcionales

*   **RF-003 · Bloquear temporalmente material por reserva**
    *   **Descripción:** El sistema debe permitir a un vendedor reservar una lámina. El sistema mantendrá la lámina visible en el catálogo, pero oscurecida y con el estado "Reservado", deshabilitando el botón de selección para el resto de los usuarios.
    *   **Origen:** Confirmado en entrevista con Almacén.
    *   **Criterio de aceptación:** Al confirmar la reserva, la lámina cambia instantáneamente de estado visual y de sistema, impidiendo su selección en otras sesiones activas.

*   **RF-004 · Caducidad mandatoria de reservas**
    *   **Descripción:** El sistema liberará la reserva automáticamente a las 48 horas naturales si el usuario no cambia el estado de la lámina a "Salida de almacén".
    *   **Origen:** Confirmado en entrevista con Almacén.
    *   **Criterio de aceptación:** Pasadas exactamente 48 horas desde el registro temporal, el estado de la lámina vuelve a "Disponible" sin intervención humana.

*   **RF-005 · Vinculación obligatoria de lámina con ID de bloque original**
    *   **Descripción:** El sistema debe obligar a vincular el registro de cada lámina con su ID de bloque de origen para garantizar la consistencia cromática de la piedra.
    *   **Origen:** Regla de negocio derivada de la Visión del producto.
    *   **Criterio de aceptación:** El formulario no permite guardar un nuevo registro de lámina si el campo "ID de Bloque" se encuentra vacío.

*   **RF-006 · Consultar catálogo en tiempo real**
    *   **Descripción:** El sistema debe proporcionar una interfaz principal para verificar la disponibilidad actualizada de todos los materiales, sus IDs y su superficie.
    *   **Origen:** Modificado tras inspección por pares.
    *   **Criterio de aceptación:** Los cambios realizados sobre el inventario se visualizan en el catálogo de los vendedores de forma inmediata.

*   **RF-007 · Descontar bajas y mermas**
    *   **Descripción:** El sistema debe registrar las salidas por rotura o despique de material, inactivando el ID original y manteniendo la paridad absoluta con el almacén físico.
    *   **Origen:** Modificado tras inspección por pares.
    *   **Criterio de aceptación:** El sistema retira exitosamente del stock el ID seleccionado al aplicarse una baja por despique.

*   **RF-008 · Alertar stock crítico**
    *   **Descripción:** El sistema debe enviar notificaciones automáticas a los administradores cuando las existencias de un material en específico crucen su nivel mínimo parametrizado.
    *   **Origen:** Modificado tras inspección por pares.
    *   **Criterio de aceptación:** Al registrar una salida que baje el stock del límite permitido, aparece una alerta en el panel de la Dueña.

## 4. Requisitos No Funcionales
*   **RNF-USA-001 · Usabilidad (Eficiencia operativa)**
    *   **Descripción:** Un usuario debe poder completar el flujo de reserva en un máximo de 3 clics desde la pantalla principal del catálogo.
    *   **Justificación:** Los vendedores están atendiendo clientes en piso o por teléfono; agregar fricción o pantallas innecesarias provocará que registren las reservas en papel y dejen de usar el sistema.
*   **RNF-REN-001 · Rendimiento (Prevención de carrera)**
    *   **Descripción:** El sistema debe procesar la transacción en menos de 5 segundos, medidos desde que el usuario hace clic en "Confirmar Reserva" hasta que despliega la pantalla de éxito.
    *   **Justificación:** Si el sistema tarda más de 5 segundos, los vendedores volverán a dar clic o recargarán la página, incrementando la posibilidad de choques en base de datos al intentar reservar el mismo material.
*   **RNF-SEG-001 · Seguridad (Control de privilegios)**
    *   **Descripción:** El sistema debe impedir que un usuario con rol "Vendedor" realice salidas definitivas de almacén o ingresos de stock.
    *   **Justificación:** Proteger la integridad de los datos logísticos y evitar fraudes o manipulaciones no autorizadas del inventario físico.

## 5. Casos de Uso
*(Detalle principal para validación)*

**CU-05 · Reservar material para venta**
*   **Actor principal:** Vendedor
*   **Objetivo:** Apartar una lámina del inventario para garantizar que no se asigne a otro proyecto mientras se cierra la venta.
*   **Precondición:** El vendedor tiene el catálogo de disponibilidad abierto.
*   **Escenario principal:**
    1. El vendedor busca una lámina en estado "Disponible" y hace clic en "Reservar".
    2. El sistema muestra la pantalla de Detalle con la superficie y el ID del bloque.
    3. El vendedor hace clic en "Confirmar Reserva".
    4. El sistema asocia el ID de la lámina al usuario y activa el temporizador de 48 horas.
    5. El sistema muestra la pantalla de Éxito confirmando la reserva.
    6. El sistema oscurece la lámina en el catálogo, actualiza su estado a "Reservado" y deshabilita su botón de selección.
*   **Flujos alternos:**
    *   **3a. Condición de carrera (Material ganado por otro usuario):** Al dar clic en confirmar, el sistema detecta que la lámina acaba de ser reservada por otro vendedor. El sistema bloquea el avance y despliega la *Alerta de error*.
    *   **3b. Material dado de baja por despique:** El almacén inhabilitó la lámina minutos antes. El sistema lanza la *Alerta de error* informando la baja por despique.
*   **Postcondición:** La lámina queda reservada, asegurando la imposibilidad de que sufra una doble asignación.
*   **Requisitos asociados:** RF-003, RF-004, RF-005, RNF-USA-001, RNF-REN-001.

## 6. Trazabilidad y Control de Cambios

| Requisito | Origen | Caso de uso | Pantalla del prototipo | Estado |
| :--- | :--- | :--- | :--- | :--- |
| **RF-003** | Entrevista | CU-05 Reservar material | Detalle de lámina / Éxito | Modificado tras inspección |
| **RF-004** | Entrevista | CU-05 Reservar material | Pantalla de Éxito | Modificado tras inspección |
| **RF-005** | Regla de negocio | CU-05 Reservar material | Detalle de lámina | Modificado tras inspección |
| **RF-006** | Inspección de Dupla | CU-04 Consultar catálogo | Catálogo | Nuevo |
| **RF-007** | Inspección de Dupla | CU-02 Registrar baja por despique | N/A | Nuevo |
| **RF-008** | Inspección de Dupla | CU-06 Configurar alertas | N/A | Nuevo |
| **RNF-USA-001** | Derivado de Usabilidad | CU-05 Reservar material | Flujo completo (3 clics) | Modificado tras inspección |
| **RNF-REN-001** | Derivado de Rendimiento | CU-05 Reservar material | Detalle -> Éxito | Modificado tras inspección |
| **RNF-SEG-001** | Derivado de Seguridad | CU-05 Reservar material | Alerta de error | Vigente |

### Registro de Inspección de Dupla
*   **Revisado por: Jimena Morales**
*   **Fecha de revisión: 01/10/2026**
*   **Hallazgos principales integrados:** Se identificó ambigüedad en el límite de las métricas (RNF), se renombró el RF-005 para mayor claridad de la acción, y se agregaron los requisitos faltantes (RF-006, RF-007, RF-008) para respaldar los casos de uso "Consultar catálogo", "Registrar baja" y "Configurar alertas" que no tenían un requisito base documentado.
