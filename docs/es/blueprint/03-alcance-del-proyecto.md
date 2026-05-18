# Alcance del Proyecto

## Dentro del Alcance

- Especificación del modelo Ego (selección de modelo base, estructura LoRA, sistema de ediciones)
- Ego Pod — pipeline de entrenamiento completamente automatizado
- Data Collector — lee datos de fuentes especificadas (define el formato de entrada, no el formato de datos en sí)
- Corpus Compiler — transforma datos en bruto en corpus de entrenamiento
- Training Engine — entrena adaptadores LoRA basados en modelos base
- Gestión de Versiones — salida multi-edición, sobrescritura de versiones anteriores
- Modo Herramienta Independiente — puede usarse independientemente sin iFay

## Fuera del Alcance

- Fayger (entorno de ejecución virtual)
- Faying (protocolo de carga)
- FayID (sistema de identidad)
- Definiciones de formato de datos para Sense/Action/Thought/Skill/Credentials (definidos por proyectos hermanos)
- Capa de protocolos (Faying Protocol, Telepathy Protocol, etc.)

Ego solo define "qué formato de entrada necesito" y "qué produzco como salida". De dónde vienen los datos y cómo llegan a los dispositivos finales es responsabilidad de los proyectos hermanos.

> Analogía: Ego Pod es como un "chef" — le dices en qué estante del refrigerador están los ingredientes (dirección de la fuente de datos), y él se encarga de cocinar (entrenar). Pero cómo está diseñado el refrigerador y cómo se obtienen los ingredientes es trabajo de otros.
