# Visión del producto

> **Plantilla del curso · Ingeniería de Software I · SIS3407**
> Este documento es el primer entregable del semestre y la base de todo lo que viene después.
> Se entrega completo en la **semana 4** y se presenta ante el grupo.


---

**Autor: César Méndez**
**Fecha de la última versión: 18/08/2026**
**Repositorio: Inventario-Empresa**

---

## 1. Descripción del sistema


**Nombre del sistema:**

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


**Un conflicto entre usuarios: Vendedor vs. Encargado de almacén — reservar material
El vendedor quiere poder cerrar una venta rápido y "apartar" material para no perder al cliente, incluso si no está 100% seguro de la cantidad exacta que se va a usar. El de almacén, en cambio, necesita que las existencias reflejen la realidad para no quedarse sin material para otro proyecto que ya estaba confirmado. Si el sistema deja reservar con demasiada facilidad, se puede "apartar" material que en la práctica nunca se usa, y eso ensucia el inventario.**

---

## 3. Alcance

*Instrucción: lo que escribes en "fuera del alcance" es lo que después evita que el proyecto crezca sin control. Sé específico: "reportes" no dice nada, "reportes de ventas mensuales exportables a PDF" sí.*

### Dentro del alcance

-
-
-
-

### Explícitamente fuera del alcance

-
-
-

**Por qué queda fuera:**

*Instrucción: para al menos una de las exclusiones, explica la razón. Puede ser tiempo, complejidad, o que no aporta al problema central.*

---

## 4. Tipo de sistema y restricciones

*Instrucción: identifica de qué tipo es tu sistema y qué te obliga a garantizar ese tipo. Un sistema de información y un sistema crítico no se diseñan igual.*

**Tipo de sistema:**

*(De información · Embebido · Crítico · Web y SaaS · De datos y análisis)*

**Por qué es de ese tipo:**

**Atributos de calidad que impone:**

| Atributo | Por qué importa en mi caso | Qué pasa si no se cumple |
|---|---|---|
| | | |
| | | |
| | | |

**Reglas de negocio que ya identifiqué:**

*Instrucción: reglas que no son obvias desde fuera y que alguien que conoce el dominio tendría que explicarte. Si no encuentras ninguna, tu caso puede ser demasiado simple.*

1.
2.
3.

---

## 5. Ciclo de vida elegido

*Instrucción: este apartado se trabaja en la semana 3, después de ver los modelos de desarrollo. La justificación pesa más que la elección: no hay un modelo correcto, hay uno defendible para tu caso.*

**Modelo elegido:**

**Por qué le conviene a este proyecto:**

*Instrucción: argumenta con las características reales de tu caso. Estabilidad de los requisitos, disponibilidad del cliente, nivel de riesgo, tamaño del equipo, frecuencia de entregas esperada.*

### Alternativas descartadas

**Alternativa 1:**

*Por qué la descarté:*

**Alternativa 2:**

*Por qué la descarté:*

---

## Antes de entregar

Reviso que el documento cumpla lo siguiente:

- [ ] La descripción del apartado 1 se entiende sin ser del área
- [ ] Hay al menos dos tipos de usuario con necesidades distintas
- [ ] Identifiqué un conflicto real entre usuarios
- [ ] El alcance dice qué queda fuera, no solo qué queda dentro
- [ ] Las exclusiones son específicas, no genéricas
- [ ] Identifiqué el tipo de sistema y al menos dos atributos de calidad
- [ ] Anoté al menos tres reglas de negocio no obvias
- [ ] Justifiqué el ciclo de vida contra dos alternativas descartadas
- [ ] El documento está en mi repositorio y se puede leer desde el navegador
- [ ] Borré todas las instrucciones en cursiva de la plantilla
