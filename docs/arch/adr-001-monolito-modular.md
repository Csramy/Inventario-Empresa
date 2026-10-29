# ADR-001: Adopción de Arquitectura de Monolito Modular para RockStock

* **Autor:** César Méndez
* **Fecha:** 2026-10-29

---

## 1. Contexto y Problema
En el desarrollo del sistema **RockStock** (sistema de información a la medida para la gestión de inventario de superficies de piedra natural y tecnológica), necesitamos definir la estructura general de despliegue y organización de los componentes del servidor. 

La arquitectura debe soportar reglas de negocio estrictas como la consistencia cromática por ID de bloque (**RF-005**), la conservación de superficie al realizar cortes y mermas (**RF-002**), el bloqueo temporal de reservas por 48 horas naturales (**RF-003**, **RF-004**) y la actualización del catálogo en tiempo real en menos de 5 segundos (**RNF-REN-001**). 

Debemos decidir cómo estructurar el software considerando los recursos humanos del proyecto y el costo de mantenimiento a futuro.

## 2. Decisión Arquitectónica
Adoptaremos una arquitectura de **Monolito Modular**. 

Esto significa que **RockStock** se desplegará y compilará como una única aplicación ejecutable (respaldada por una API en Node.js y una base de datos relacional PostgreSQL), pero internamente sus componentes lógicos estarán estrictamente separados en tres módulos de negocio independientes según el Principio de Responsabilidad Única (SRP):
1. **Almacén y Materiales** (Bloques, láminas, inventario, movimientos y despique).
2. **Comercial y Apartados** (Catálogo y reservas).
3. **Abastecimiento y Alertas** (Control de umbrales y stock crítico).

La comunicación entre estos módulos se gestionará mediante contratos limpios y el patrón *Observer*, evitando que un módulo acceda a las entrañas o tablas directas de otro.

## 3. Alternativas Consideradas

### Alternativa A: Microservicios
* **Descripción:** Partir el sistema en servicios independientes desplegados por separado, cada uno con su propia base de datos y comunicación por red.
* **Por qué se descartó:** Por la Ley de Conway y las limitaciones de equipo.

### Alternativa B: Monolito Tradicional
* **Descripción:** Un único programa donde todas las clases y funciones se llaman entre sí sin restricciones ni paquetes delimitados.
* **Por qué se descartó:** Aunque es simple al inicio, carece de disciplina arquitectónica. Con el tiempo, las fronteras se difuminan (por ejemplo, que el módulo de ventas modifique directamente las tablas de almacén), lo que degrada el código y encarece cualquier cambio futuro.

## 4. Consecuencias

### Positivas
* **Simplicidad operativa:** Se compila, despliega y opera como una sola unidad, facilitando las transacciones ACID en PostgreSQL (por ejemplo, al registrar cortes o reservas concurrentes).
* **Fronteras claras desde el día uno:** Obliga a mantener el desacoplamiento mediante patrones (*Observer* y *Adapter*), facilitando que el código sea mantenible.
* **Cero fricción por red:** Las llamadas internas entre módulos son llamadas a funciones rápidas y síncronas, garantizando cumplir con la métrica de rendimiento de menos de 5 segundos (**RNF-REN-001**).

### Negativas
* **Exige disciplina de desarrollo:** Como el lenguaje de programación (JavaScript/Node.js) no impide físicamente que un módulo importe código de otro de manera indebida, la separación depende enteramente de la disciplina del desarrollador y de las revisiones de código.
* **Escalabilidad acoplada:** Si en el futuro la carga operativa creciera exponencialmente en un solo módulo, se tendría que escalar la aplicación completa (aunque esto no representa un riesgo para el volumen de operación actual).
