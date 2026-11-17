# AI-001: Análisis de impacto · SC-001 (Unificar la caducidad de reservas en 48 horas hábiles con orden de venta confirmada)

## Solicitud analizada

* **ID:** SC-001
* **Título:** Unificar la caducidad de reservas en 48 horas hábiles con orden de venta confirmada
* **Solicitante:** Jimena Morales (dupla de revisión del proyecto RockStock)
* **Analista responsable:** César Méndez
* **Fecha del análisis:** 12 de noviembre de 2026

## Resumen del cambio

Alinear la `Especificación de Requisitos` y la `Visión del Producto` para que el plazo de caducidad automática de una reserva (`RF-004`) sea de **48 horas hábiles** (excluyendo sábados, domingos y días festivos registrados) en lugar de 48 horas naturales, y que la condición para mantener la lámina apartada sea contar con una **orden de venta confirmada** en lugar de exigir que ya haya ocurrido la `"Salida de almacén"` física.

---

## 1. Qué artefactos toca (Análisis de trazabilidad)

### 1.1 Documentos de visión y requisitos
* **`docs/vision-producto.md` (Alcance y Regla de negocio 2):** Unificar la redacción sobre el bloqueo temporal y la caducidad mandatoria de 48 horas hábiles con orden de venta confirmada.
* **`docs/especificacion-requisitos.md`:**
  * **`RF-004 · Caducidad mandatoria de reservas`:** Modificar la descripción y el criterio de aceptación para reemplazar *"48 horas naturales"* por *"48 horas hábiles (sin contar fines de semana ni días festivos registrados)"* y cambiar la condición de *"Salida de almacén"* por *"orden de venta confirmada"*.
  * **`RF-003 · Bloquear temporalmente material por reserva`:** Vincular la transición del estado `"Reservado"` con la confirmación de la orden de venta para detener el reloj de caducidad.
  * **`Sección 1 · Fuera del alcance`:** Precisar que registrar una *"orden de venta confirmada"* consiste únicamente en una confirmación de estado o folio comercial interno dentro de RockStock, manteniendo explícitamente fuera del alcance la facturación y las pasarelas de pago.
  * **`RNF-REN-001 · Rendimiento`:** Se mantiene la métrica (< 5 segundos), ahora aplicada también a reflejar el estado `"Disponible"` en todas las sesiones activas en cuanto venza una reserva.
  * **`Sección 6 · Trazabilidad y Control de Cambios`:** Actualizar el estado de `RF-004` y registrar el cambio derivado de `SC-001`.

### 1.2 Casos de uso
* **`CU-05 · Reservar material para venta`:**
  * **Paso 4 del escenario principal:** Cambiar *"activa el temporizador de 48 horas"* por *"inicia el temporizador de 48 horas hábiles (excluyendo sábados, domingos y días festivos registrados)"*.
  * **Postcondición:** Especificar que la reserva sólo se libera automáticamente si transcurren 48 horas hábiles sin que se confirme una orden de venta.
* **Ajuste en casos de uso existentes (`CU-05` / `CU-06`):** Contemplar la acción del vendedor/almacén para marcar una reserva como *"Orden de venta confirmada"* y la configuración de días festivos por parte de la Dueña.

### 1.3 Pantallas del prototipo (Figma)
* **Marco `Detalle de lámina`:** Actualizar la leyenda informativa de vigencia a *"Reserva válida por 48 horas hábiles"*.
* **Marco `Pantalla de Éxito`:** Mostrar la fecha y hora exacta de vencimiento calculada con calendario hábil e incluir la acción para confirmar la orden de venta.
* **Marco `Catálogo`:** Asegurar que cuando la reserva caduque sin orden de venta, la tarjeta de la lámina deje de verse oscurecida y vuelva a mostrar el estado `"Disponible"` con su botón habilitado.

### 1.4 Arquitectura y diseño (`doc/arch/diagramas.md` y `ADR-001`)
* **`docs/arch/adr-001-monolito-modular.md`:** En la *Sección 1 (Contexto y Problema)*, cambiar el texto *"bloqueo temporal de reservas por 48 horas naturales (RF-003, RF-004)"* a *"48 horas hábiles"*. La decisión arquitectónica (Monolito Modular) **no se ve afectada**, ya que el cambio queda aislado dentro del **Módulo 2 · Comercial y Apartados**.
* **Diagrama de clases:**
  * En la clase `Reserva`, agregar los atributos `-fechaExpiracion: Date` y `-ordenVentaConfirmada: boolean`, el método `+confirmarOrdenVenta() void`, y ajustar `+estaVencida(diasFestivos) boolean` para que ignore fines de semana/festivos y devuelva `false` si existe orden de venta confirmada.
* **Diagrama de estados:**
  * Separar el estado `VentaConfirmada` para las láminas cuya venta ya se cerró pero siguen físicamente en el almacén:
    * `Reservada --> Disponible: caduca tras 48h hábiles sin orden de venta`
    * `Reservada --> VentaConfirmada: se confirma orden de venta`
    * `VentaConfirmada --> SalidaAlmacen: retiro físico de almacén`
* **Diagrama entidad-relación (Base de datos):**
  * En la tabla `RESERVA`, cambiar el tipo de dato de `fecha_inicio` y `fecha_expiracion` a `datetime` y añadir el campo `boolean orden_venta_confirmada`.
  * Añadir la tabla `DIA_FESTIVO` (`date fecha`, `string descripcion`) para descontar días inhábiles del cálculo.

---

## 2. Esfuerzo y costo estimado

* **Nivel de esfuerzo:** **Bajo - Medio** (porque el proyecto se encuentra en fase de documentación, diseño y prototipo; aún no hay código en producción que refactorizar).
* **Desglose de tareas:**
  1. Actualizar redacción de `RF-004`, `CU-05` y tabla de trazabilidad en `docs/especificacion-requisitos.md` y `docs/vision-producto.md`.
  2. Actualizar `ADR-001` y los diagramas en `doc/arch/diagramas.md`.
  3. Ajustar textos de vigencia en el prototipo de Figma (`Detalle de lámina` y `Pantalla de Éxito`).
  4. Definir los casos de prueba de aceptación con fechas de fin de semana y días festivos.

---

## 3. Riesgos y efectos secundarios

* **Riesgo de alcance (Falsa expectativa de facturación):** Que el equipo o el cliente interpreten *"orden de venta confirmada"* como la obligación de programar un módulo de facturación o cobros.
  * *Mitigación:* Dejar por escrito en `RF-004` que la confirmación de orden de venta es un marcador de estado operativo dentro de la reserva y no procesa pagos ni facturas.
* **Riesgo de inventario inmovilizado ("stock fantasma"):** Si un vendedor marca una reserva como *"orden de venta confirmada"* sin que la venta sea real para evitar que le caduque a las 48 horas hábiles, la lámina quedaría bloqueada indefinidamente.
  * *Mitigación:* Mantener la restricción de seguridad (`RNF-SEG-001`) o exigir folio/validación para confirmar la orden de venta.
* **Complejidad en el cálculo de fechas:** Calcular 48 horas hábiles requiere una lista mantenible de días festivos; si no se cargan los festivos del año, el sistema contará ese día como laborable.

---

## 4. Decisión

* [x] **Aprobada**
* [ ] **Rechazada**
* [ ] **Aprobada con ajustes / Posponer**

### Justificación de la decisión (¿Vale lo que cuesta?)
**Sí vale lo que cuesta.** El costo de aplicar el cambio hoy es mínimo porque solo modifica archivos Markdown de requisitos, diagramas Mermaid y textos en Figma. En cambio, no hacerlo dejaría una contradicción directa entre la `Visión del Producto` y el `RF-004`, provocaría que las reservas del viernes caduquen el domingo cuando la marmolera está cerrada y liberaría por error láminas ya vendidas que están esperando turno de corte o transporte para salir físicamente del almacén.
