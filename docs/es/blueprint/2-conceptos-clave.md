# Conceptos Clave

## Ego Pod (Cápsula de Autoconciencia)

Ego Pod es el motor central de Ego — un pipeline de entrenamiento completamente automatizado. Como una "cámara de cultivo", introduces datos en bruto y automáticamente forja un modelo de IA que te comprende.

Hace tres cosas:
1. **Recolectar** — Leer datos del Human Prime desde fuentes especificadas (directorios locales/URLs/almacenamiento en la nube/...)
2. **Forjar** — Procesar datos en bruto en corpus de entrenamiento, entrenar adaptadores LoRA sobre un modelo base seleccionado
3. **Producir** — Generar múltiples versiones (ediciones) del modelo Ego, sobrescribiendo versiones anteriores

> Analogía: Ego Pod es como una "fábrica de personalización". Introduces tu diario, historial de chat y notas de trabajo (materias primas), y automáticamente las limpia, clasifica y procesa, produciendo finalmente una "mini versión de ti" (producto terminado). Este producto puede instalarse en un reloj inteligente (edición lite) o en una computadora (edición plus), como la misma persona usando ropa de diferentes tallas.

```
Fuentes de Datos           Ego Pod                        Salida
┌──────────────┐        ┌─────────────────┐         ┌──────────────┐
│ Sense Data    │──┐     │                 │         │ Ego-lite     │
│ Action Data   │──┤     │  ① Recolección   │        │ Ego-normal   │
│ Thought Data  │──┼───→ │  ② Compilación   │───────→│ Ego-plus     │
│ Skill Data    │──┤     │  ③ Entrenamiento │        │ Ego-super    │
│ Credentials  │──┘     │  ④ Salida Multi-v │        │ [custom...]  │
└──────────────┘        └─────────────────┘         └──────────────┘
 Directorio/URL/Nube      Totalmente Auto · Repetible   Sobrescribe Anterior
```

## Estructura del Modelo Ego

Cada Ego = Modelo Base + Adaptador LoRA

- **Modelo Base**: Modelos de código abierto con pocos parámetros (ej., Qwen 3.5, Gemma 4), que proporcionan capacidades generales de lenguaje y razonamiento. Analogía: La base es como un "apartamento sin amueblar" — tiene estructura básica e instalaciones, pero sin estilo personal.
- **Adaptador LoRA**: Entrenado a partir de datos del Human Prime, portando la personalidad, preferencias, recuerdos, conocimientos y habilidades del Human Prime. Analogía: LoRA es como tu "plan de diseño de interiores" — tus muebles favoritos, colores de pared y disposición de iluminación son lo que hacen de la casa "tu hogar".

La base no contiene información personalizada; LoRA es el "alma".

> Cambiar de base es como mudarse: Te mudas de un estudio (0.8B) a un apartamento de tres habitaciones (4B) — la casa se hace más grande, pero tu estilo interior (LoRA) necesita ser readaptado al nuevo diseño. La buena noticia es que Ego Pod maneja automáticamente este proceso de "renovación".

## Sistema de Ediciones

Ego proporciona cuatro versiones por defecto, adaptadas a dispositivos con diferentes capacidades de cómputo:

| Edición | Dispositivo Objetivo | Escala Base | Escenario Típico |
|---------|---------------------|-------------|------------------|
| **lite** | Relojes inteligentes, grabadoras de voz, drones, dispositivos IoT | ~0.8B | Ego-lite se ejecuta en tu reloj inteligente, sabe que te despiertas a las 7 AM y prefieres café negro, envía automáticamente recordatorios de clima y agenda cada mañana en el mismo tono que tus propias notas |
| **normal** | Teléfonos, tablets | ~2B | Ego-normal en tu teléfono entiende tus hábitos de conversación, usa automáticamente tus frases comunes y formato de firma al redactar correos |
| **plus** | PCs de alto rendimiento, servidores locales | ~4-9B | Ego-plus en tu laptop te asiste con la programación, habiendo aprendido tu estilo de código (convenciones de nombres de variables, estilo de comentarios, patrones de diseño comunes), dando sugerencias como si tú mismo las hubieras escrito |
| **super** | Servicios en la nube, clústeres GPU | ~27B+ | Ego-super en la nube tiene tu base de conocimientos completa, puede asistir a revisiones técnicas en tu nombre, dando opiniones altamente consistentes con tu propio criterio |

Se admiten ediciones personalizadas — los usuarios pueden nombrarlas y configurarlas libremente.

> Por ejemplo: Un médico puede definir una edición personalizada "clínica" (base 4B), especializada para escenarios de consulta externa, habiendo aprendido sus hábitos de consulta, plantillas de recetas comunes y estilo de comunicación con pacientes.

Cada edición usa un modelo base de diferente escala y tiene su propio conjunto de adaptadores LoRA entrenados independientemente.
