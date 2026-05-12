# Arquitectura

## Arquitectura General

```
┌─────────────────────────────────────────────────────────────────┐
│                    Ego Pod (Cápsula de Autoconciencia)           │
│                                                                 │
│  ┌───────────┐    ┌───────────┐    ┌───────────┐    ┌────────┐ │
│  │  Data      │───→│  Corpus   │───→│  Training │───→│Version │ │
│  │ Collector  │    │ Compiler  │    │  Engine   │    │ Vault  │ │
│  └───────────┘    └───────────┘    └───────────┘    └────────┘ │
│       ↑                                                  ↓      │
│  ┌─────────┐                                      ┌──────────┐ │
│  │  Source  │                                      │ Ego      │ │
│  │  Config  │                                      │ Editions │ │
│  └─────────┘                                      └──────────┘ │
│                                                                 │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │                  Config Center                            │   │
│  │  Selección de Base · Def. Ediciones · Params Entrenamiento · Mapeo Fuentes · Programación │
│  └──────────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────────┘
```

## Detalle de Módulos

### 1. Data Collector

Lee datos del anfitrión desde las fuentes de datos configuradas.

**Tipos de Fuentes de Datos Soportados:**
- Directorios locales (rutas del sistema de archivos)
- URLs (endpoints HTTP/HTTPS)
- Almacenamiento en la nube (S3, OSS, Google Drive, etc., extensible mediante plugins)
- Adaptadores personalizados (los usuarios pueden escribir plugins de recolección)

**Categorías de Datos Recolectados (correspondientes a la arquitectura iFay):**

| Categoría de Datos | Módulo iFay | Ejemplo del Mundo Real |
|-------------------|-------------|------------------------|
| Datos de Percepción | Sense | Rutas de ejercicio diario, distribución del tiempo de uso del teléfono, ubicaciones de restaurantes frecuentados, patrones de sueño |
| Datos de Comportamiento | Action | Hábitos de operación del teléfono (siempre revisa mensajes antes del correo), atajos frecuentes, scripts de automatización |
| Datos Cognitivos | Thought | Notas de lectura, diarios de trabajo, reflexiones de aprendizaje, evaluaciones y procesos de pensamiento sobre soluciones técnicas |
| Datos de Habilidades | Skill | Lenguajes de programación y niveles de competencia, recetas favoritas, instrumentos tocados y repertorio |
| Datos de Credenciales | Credentials | Identificadores de identidad, alcance de autorización (ej., puede enviar correos en tu nombre pero no puede hacer transferencias), límites de permisos |

> Por ejemplo: La configuración del Collector de una traductora freelance podría verse así — Sense apunta a su memoria de traducción (directorio local), Action apunta a los registros de operación de su herramienta CAT (URL), Thought apunta a sus notas terminológicas (almacenamiento en la nube), Skill apunta a sus pares de idiomas y lista de especialización (archivo local).

El Collector no se preocupa por el formato interno de los datos (eso lo definen los proyectos hermanos) — solo le importa "de dónde obtener" y "qué categoría de datos se obtuvo".

### 2. Corpus Compiler

Transforma los datos en bruto recolectados en corpus utilizable para el entrenamiento LoRA.

**Responsabilidades Principales:**
- **Limpieza y Anonimización de Datos** — Especialmente para datos relacionados con Credentials. Ejemplo: Tu clave API `sk-abc123...` se reemplaza con `[REDACTED]`, asegurando que no haya información sensible en los datos de entrenamiento
- **Estandarización de Formato** — Unificado en formatos aceptables por los frameworks de entrenamiento. Ejemplo: Una nota de lectura "Este libro trata sobre el teorema CAP en sistemas distribuidos..." se transforma en `{"instruction": "Explica el teorema CAP", "output": "El teorema CAP en sistemas distribuidos..."}`
- **Clasificación del Corpus** — Agrupado por capas LoRA. Ejemplo: "Me gusta tomar americano" va a la capa de preferencias, "Viajé a Japón en 2024" a la capa de memoria, "El mecanismo GIL de Python es..." a la capa de conocimiento
- **Filtrado de Calidad** — Eliminar datos ruidosos y duplicados. Ejemplo: Registros de chat duplicados conservan solo una copia, fragmentos demasiado cortos (como "ok", "claro") se filtran
- **Aumento de Datos** — Expandir el corpus mediante plantillas o reescritura cuando sea necesario. Ejemplo: El dato en bruto "Recomiéndame una canción" se aumenta a "Exprésalo de otra forma: Recomiéndame una canción", "Reformula: Recomiéndame una canción"

**Formatos de Salida:**
- Dataset estándar de ajuste fino por instrucciones (tripletas instrucción/entrada/salida)
- Dataset en formato de conversación (conversación multi-turno)
- Sub-datasets agrupados por capas LoRA


### 3. Training Engine (Trainer)

Entrena adaptadores LoRA basados en el modelo base seleccionado y el corpus compilado.

**Auto-Adaptación Multi-Backend:**

El Trainer detecta automáticamente tu entorno de hardware y selecciona el backend de entrenamiento óptimo:

| Prioridad | Backend | Entorno | Velocidad |
|-----------|---------|---------|-----------|
| 1 | Unsloth | GPU NVIDIA (CUDA) | Más rápido (2x aceleración) |
| 2 | HuggingFace PEFT | GPU NVIDIA (CUDA) | Velocidad estándar |
| 3 | Apple MLX | Mac (Apple Silicon) | Aceleración nativa |
| 4 | HuggingFace PEFT | Solo CPU | Más lento pero universal |
| 5 | Stub | Sin dependencias ML | Modo simulación (dev/test) |

> No necesitas preocuparte por qué backend se usa — Ego Pod selecciona automáticamente. Si tienes una RTX 4090, usa Unsloth para aceleración; si estás en un MacBook Pro M3, usa MLX; si solo quieres probar el flujo primero, usa el modo Stub para entrenamiento simulado.

**Flujo de Entrenamiento:**
1. Cargar modelo base (cuantización de 4 bits)
2. Entrenar adaptadores secuencialmente por capa LoRA
3. Validar resultados del entrenamiento (evaluación de alineación automática)
4. Generar combinaciones base+LoRA para cada edición

**Estrategia de Entrenamiento Multi-Edición:**
- Cada edición usa un modelo base de diferente escala
- Mismo corpus, LoRA entrenado por separado para diferentes bases
- Múltiples ediciones pueden entrenarse en paralelo

> Por ejemplo: En un escritorio con GPU, ejecutar `ego train` hará que Ego Pod entrene simultáneamente tres versiones de LoRA para tu reloj inteligente (lite), teléfono (normal) y computadora (plus). Todo el proceso toma aproximadamente 10-30 minutos, dependiendo del volumen de datos.

### 4. Version Vault

Gestiona las versiones del modelo Ego y sus salidas.

**Responsabilidades:**
- Almacenar adaptadores LoRA producidos en cada ejecución de entrenamiento
- Gestionar archivos de modelo de múltiples ediciones
- Soportar reversión de versiones (preservar versiones históricas)
- Sobrescribir versiones anteriores (comportamiento por defecto)
- Exportar como paquetes Ego distribuibles

> Por ejemplo: Entrenaste un Ego la semana pasada (v0.1.0), luego agregaste nuevos datos y reentrenaste esta semana (v0.2.0). Descubres que el estilo de respuesta de la nueva versión ha cambiado — ya no suena como tú. Puedes revertir a v0.1.0 con un clic, como degradar el sistema operativo de un teléfono.

**Estructura del Paquete Ego:**
```
ego-output/
├── manifest.json              # Metadatos del Ego (versión, info de base, lista de ediciones)
├── lite/
│   ├── config.json            # Config de edición (modelo base, cuantización, requisitos de hardware)
│   └── adapters/
│       └── ego-lora.safetensors  # Pesos del adaptador LoRA
├── normal/
│   ├── config.json
│   └── adapters/
│       └── ego-lora.safetensors
├── plus/
│   ├── config.json
│   └── adapters/
│       └── ego-lora.safetensors
├── super/
│   ├── config.json
│   └── adapters/
│       └── ego-lora.safetensors
└── [custom-name]/             # Edición definida por el usuario
    ├── config.json
    └── adapters/
        └── ego-lora.safetensors
```

### 5. Config Center

Gestiona centralmente todas las configuraciones de Ego Pod.

**Elementos de Configuración:**

```yaml
# ego-config.yaml ejemplo

# Mapeo de fuentes de datos — indica a Ego Pod dónde encontrar tus datos
sources:
  sense:
    type: "local"
    path: "/data/ifay/sense/"          # Tu directorio de datos de percepción
  action:
    type: "url"
    endpoint: "https://api.example.com/action-logs"  # API de registros de comportamiento
  thought:
    type: "local"
    path: "/data/ifay/thought/"        # Tus notas y base de conocimientos
  skill:
    type: "s3"
    bucket: "ifay-skills"              # Datos de habilidades en la nube
    prefix: "user-001/"
  credentials:
    type: "local"
    path: "/data/ifay/credentials/"    # Datos de credenciales (anonimizados automáticamente)

# Parámetros de entrenamiento — normalmente no necesitan modificación, los valores por defecto se adaptan a la mayoría de escenarios
training:
  method: "qlora"
  quantization: "4bit"
  lora_rank: 16
  lora_alpha: 32
  learning_rate: 2e-4
  epochs: 3
  batch_size: 4

# Definiciones de ediciones — qué versiones quieres producir
editions:
  lite:
    base: "qwen3.5-0.8B"
    description: "Relojes inteligentes, grabadoras de voz, drones, IoT"
  normal:
    base: "qwen3.5-2B"
    description: "Teléfonos, tablets"
  plus:
    base: "qwen3.5-4B"
    description: "PCs de alto rendimiento, servidores locales"
  super:
    base: "qwen3.5-27B"
    description: "Servicios en la nube, clústeres GPU"
  # Puedes agregar ediciones personalizadas aquí, ej.:
  # clinic:
  #   base: "qwen3.5-4B"
  #   description: "Edición para consulta externa"

# Estrategia de programación — cuándo reentrenar automáticamente
schedule:
  auto_update: true
  interval: "weekly"          # Forjar automáticamente cada semana
  trigger: "data_change"      # O manual / cron (tarea programada)
```
