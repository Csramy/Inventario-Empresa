# Visión del producto

> **Plantilla del curso · Ingeniería de Software I · SIS3407**
> Este documento es el primer entregable del semestre y la base de todo lo que viene después.
> Se entrega completo en la **semana 4** y se presenta ante el grupo.


---

**Autor: César Méndez**
**Fecha de la última versión: 01/09/2026**
**Repositorio: Inventario-Empresa**

---

## 1. Descripción del sistema


**Nombre del sistema: RockStock**

**Descripción:** Sistema que permite a una empresa de superficies de piedra natural y tecnológica controlar su inventario de materiales: registrar qué entra al almacén (compras de láminas, bloques u otros insumos), qué sale (por transformación, instalación o venta), y llevar el control de existencias en tiempo real. El sistema avisa cuando un material está por agotarse para que se pueda reabastecer a tiempo.

---

## 2. Problema y usuarios

**El problema: La empresa no tiene visibilidad clara de cuánto material tiene disponible en cada momento, ni de cuánto se está usando en cada proyecto o venta. Esto provoca compras de último momento, retrasos en instalaciones por falta de material, o exceso de stock inmovilizado por no saber con certeza qué hay realmente en almacén.**

**Cómo se resuelve hoy sin el sistema: El inventario se lleva en un archivo de Excel. La dueña de la empresa es quien decide cuándo comprar más material, basándose en ese archivo. El archivo se envía por correo para que otros lo consulten, y en ese proceso se pierde información: no hay una única fuente de verdad actualizada en tiempo real, sino copias del mismo archivo circulando por correo que pueden quedar desincronizadas entre sí.**

**Usuarios del sistema:**

| Tipo de usuario | Qué necesita del sistema | Qué le preocupa |
|---|---|---|
| Dueña de la empresa | Ver existencias actualizadas y confiables para decidir cuándo y qué comprar | Tomar decisiones de compra con información desactualizada o incompleta |
| Encargado de almacén / inventario | Registrar entradas y salidas de material de forma rápida y centralizada | Que el registro sea ágil y no le quite tiempo a su trabajo diario |
| Vendedor / atención a cliente | Consultar disponibilidad real antes de vender o comprometer una fecha de entrega | Ofrecer algo que en realidad no hay en existencia, por estar viendo un Excel desactualizado |
| Encargado de instalación/proyecto | Confirmar que el material reservado para su proyecto esté disponible cuando lo necesite | Que el material se agote o se lo asignen a otro proyecto sin avisarle |


**Un conflicto entre usuarios: Vendedor vs. Encargado de almacén por reservar material. 
El vendedor quiere poder cerrar una venta rápido y "apartar" material para no perder al cliente, incluso si no está 100% seguro de la cantidad exacta que se va a usar. El de almacén, en cambio, necesita que las existencias reflejen la realidad para no quedarse sin material para otro proyecto que ya estaba confirmado. Si el sistema deja reservar con demasiada facilidad, se puede "apartar" material que en la práctica nunca se usa, y eso ensucia el inventario.**

**Huecos encontrados por mi dupla: No entendió cómo se entera alguien hoy si hay material disponible o no.**

---

## 3. Alcance

### Dentro del alcance

- Registrar ingreso de materiales: Documentación técnica de láminas, bloques e insumos. Este módulo asegura que cada unidad sea trazable desde su entrada al almacén, eliminando el ingreso "invisible" de material.
- Descontar stock por mermas o transformación: Gestión de salidas reales por venta, rotura o descarte natural. Su impacto operativo es directo: mantiene la paridad absoluta entre el almacén físico y el sistema.
- Alertar stock mínimo a la gerencia: Automatización de notificaciones cuando los niveles de existencia alcanzan el umbral crítico. Esto permite un reabastecimiento inteligente y planificado.
- Bloquear temporalmente por reserva de vendedor: Un motor de reglas de negocio que gestiona los compromisos comerciales, evitando la sobreventa de una misma pieza de piedra.
- Consultar catálogo en tiempo real: Esta interfaz es la solución directa al "agujero" de comunicación identificado en la fase de diagnóstico. Sustituye por completo la necesidad de realizar llamadas a la dueña o esperar respuestas de correos electrónicos, permitiendo que cualquier usuario verifique la disponibilidad de forma autónoma e inmediata.

### Explícitamente fuera del alcance

- No facturación ni pasarela de pagos: El sistema es una herramienta de control de activos y gestión logística interna, no un software contable ni transaccional.
- No logística ni rutas de entrega: El alcance funcional concluye con el registro de salida del almacén; la gestión del transporte es un proceso externo.
- No optimizador gráfico de cortes (Nesting): No se realizará el acomodo algorítmico de piezas dentro de las láminas.

**Por qué queda fuera: No facturación ni pasarela de pagos**

---El procesamiento de transacciones financieras y la emisión de facturas electrónicas (timbrado fiscal) introducen un nivel de acoplamiento externo y complejidad de infraestructura que excede los objetivos académicos del proyecto.
Desde una perspectiva técnica, el timbrado fiscal exige la integración con APIs SOAP/REST de Proveedores Autorizados de Certificación, lo que obliga a implementar la generación y validación criptográfica de estructuras XML complejas y el manejo seguro de Certificados de Sello Digital. Por otro lado, la integración de pasarelas de pago (como Stripe o PayPal) demanda el cumplimiento riguroso de normativas de seguridad PCI-DSS, la gestión de tokens de pago para evitar el almacenamiento de datos sensibles en nuestra base de datos, y la configuración de webhooks para el manejo asíncrono de eventos (notificaciones de pagos aprobados, rechazados o contracargos).

## 4. Tipo de sistema y restricciones

**Tipo de sistema: De información**

**Por qué es de ese tipo: Porque su propósito fundamental es registrar, consultar y gestionar la información de una organización, lo cual representa el tipo de software más común en la industria y abarca de manera clásica la gestión de inventarios. En este caso, el sistema actúa como la única fuente de verdad en tiempo real para controlar las entradas, salidas y existencias físicas de láminas, bloques e insumos de piedra natural.**

**Atributos de calidad que impone:**

| Atributo | Por qué importa en mi caso | Qué pasa si no se cumple |
|---|---|---|
|Usabilidad |El sistema debe ser fácil de usar para el personal |Nadie usará el sistema porque no se entenderá como usarlo |
|Integridad de Datos |El inventario debe estar completamente sincronizado entre el almacén y el sistema |Compras de emergencia, desajustes financieros por datos erróneos, materiales apartados que se vendan, etc |
|Control de Acceso |Jerarquía de permisos para operaciones de reserva y baja |Manipulación no autorizada del inventario y reservas fraudulentas |

**Reglas de negocio que ya identifiqué:**

1. Indivisibilidad y Conservación de Superficie: Una lámina es una entidad atómica. El sistema deberá asegurar que, tras cualquier proceso de corte, el ID original sea retirado del stock activo y se generen nuevos identificadores para las sub-unidades y mermas. La suma de las superficies de los nuevos registros deberá ser igual a la superficie del registro original.
2. Caducidad Mandatoria de Reservas: Toda reserva temporal sin confirmación de orden de venta deberá ser liberada automáticamente por el sistema tras 48 horas hábiles, reintegrando el material al stock disponible.
3. Consistencia Cromática por ID de Bloque: El sistema deberá obligar a la vinculación de cada lámina con un ID de Bloque de origen. Esta restricción es técnica y geológica, garantizando que el veteado y la tonalidad sean consistentes en pedidos complementarios.

---

## 5. Ciclo de vida elegido

**Modelo elegido: Prototipado Rápido**

**Por qué le conviene a este proyecto: Permite la validación temprana del flujo de reservas entre el vendedor y el almacén antes de consolidar la arquitectura de base de datos. Dado que la usabilidad es un atributo dominante, la creación de prototipos funcionales mitigará el riesgo de rechazo por parte de los usuarios operativos y permitirá ajustar las reglas de "stock fantasma" en etapas iniciales.**

### Alternativas descartadas

**Alternativa 1: Modelo en Cascada**

*Por qué la descarté: Se descarta por su rigidez. La imposibilidad de retroalimentación dinámica en un entorno donde los requisitos de interfaz son críticos derivaría en un alto costo de corrección al finalizar el ciclo.*

**Alternativa 2: Modelo en V**

*Por qué la descarté: Se rechaza debido a la sobrecarga documental que impone. El rigor de verificación cruzada para cada fase excede los límites temporales del proyecto académico, restando agilidad al desarrollo de las funciones nucleares.*

---
