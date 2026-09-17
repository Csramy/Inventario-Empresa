# Especificación de requisitos

> **Plantilla del curso · Ingeniería de Software I · SIS3407**
> Copia este archivo a tu repositorio como docs/especificacion-requisitos.md, borra las instrucciones en cursiva y los ejemplos, y escribe tu contenido. Las reglas de nomenclatura y redacción están en la Guía de redacción de requisitos. Se entrega en la semana 8 junto con el prototipo.

---

**Sistema:**
**Autor:**
**Versión:**
**Fecha de la última actualización:**

---

## 1. Propósito y alcance

*Para qué existe este documento y a quién va dirigido. El alcance se retoma de la Visión del producto, no se reinventa. Si cambió, corrígelo también allá.*

**Propósito del documento:**

**Alcance del sistema:**

**Fuera del alcance:**

---

## 2. Usuarios y su contexto

*Se enriquece con lo que salga de la entrevista de elicitación. Si algo cambió respecto a la Visión del producto, anótalo.*

| Usuario | Qué hace hoy sin el sistema | Qué espera del sistema |
|---|---|---|
| | | |

**Conflictos identificados entre usuarios:**

---

## 3. Requisitos funcionales

### 3.1 Resumen

| ID | Nombre | Prioridad | Origen |
|---|---|---|---|
| RF-001 | | | |
| RF-002 | | | |
| RF-003 | | | |

### 3.2 Fichas

*Una ficha por requisito, con los mismos campos siempre. Abajo va un ejemplo completo; bórralo cuando escribas los tuyos.*

**RF-001 · Registro de consulta**

| Campo | Contenido |
|---|---|
| Descripción | El sistema registra la consulta de un paciente con fecha, motivo, diagnóstico y veterinario que atendió. |
| Origen | Entrevista con el veterinario, 15 de septiembre. |
| Prioridad | Imprescindible |
| Criterio de aceptación | Al guardar una consulta con los cuatro datos, esta aparece en el historial del paciente con la fecha correcta. Si falta alguno, el sistema no guarda y señala cuál falta. |
| Relacionado con | RF-004, RNF-SEG-001 |

**RF-002 ·**

| Campo | Contenido |
|---|---|
| Descripción | |
| Origen | |
| Prioridad | |
| Criterio de aceptación | |
| Relacionado con | |

---

## 4. Requisitos no funcionales

### 4.1 Resumen

| ID | Atributo | Nombre | Prioridad | Origen |
|---|---|---|---|---|
| RNF-REN-001 | Rendimiento | | | |
| RNF-SEG-001 | Seguridad | | | |
| RNF-USA-001 | Usabilidad | | | |

### 4.2 Fichas

*Agrupadas por atributo de calidad. Abajo va un ejemplo completo; bórralo cuando escribas los tuyos.*

**RNF-REN-001 · Tiempo de consulta del historial**

| Campo | Contenido |
|---|---|
| Atributo de calidad | Rendimiento |
| Descripción | El historial completo de un paciente se despliega en menos de tres segundos. |
| Métrica | Tiempo entre la solicitud y el despliegue completo, medido con hasta 500 consultas registradas para ese paciente. |
| Origen | Derivado del tipo de sistema: de información, con consulta frecuente durante la atención. |
| Prioridad | Imprescindible |
| Por qué importa | La consulta ocurre con el paciente enfrente. Si tarda, el veterinario abandona el sistema y vuelve al expediente en papel. |
| Afecta a | RF-001, RF-004 |

**RNF-SEG-001 ·**

| Campo | Contenido |
|---|---|
| Atributo de calidad | |
| Descripción | |
| Métrica | |
| Origen | |
| Prioridad | |
| Por qué importa | |
| Afecta a | |

---

## 5. Casos de uso

*Se trabajan en la semana 7, después de la entrevista. Cada caso de uso se relaciona con los requisitos funcionales que realiza.*

---

## 6. Trazabilidad

*Esta tabla es la que hace posible el análisis de impacto de la semana 15. Mantenla actualizada conforme cambien los requisitos.*

| Requisito | Origen | Caso de uso | Elemento del prototipo |
|---|---|---|---|
| RF-001 | Entrevista 15 sep | CU-01 Registrar consulta | Pantalla de consulta |

---

## 7. Registro de cambios

*Cada modificación posterior a la primera versión se anota aquí. Un requisito eliminado se marca como tal, pero su identificador no se reutiliza.*

| Fecha | Requisito | Qué cambió | Por qué |
|---|---|---|---|
| | | | |
