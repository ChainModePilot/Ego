# Flujo de Usuario

## Para Usuarios Comunes

```
Paso 1: Instalar Ego Pod
  $ pip install ego-pod

Paso 2: Inicializar Configuración
  $ ego init
  → Guía interactiva: seleccionar modelo base, configurar rutas de fuentes de datos, elegir ediciones
  → Como configurar un teléfono nuevo, solo sigue las indicaciones paso a paso

Paso 3: Iniciar Entrenamiento
  $ ego train
  → Recolección automática de datos → compilación de corpus → entrenamiento LoRA → salida multi-versión de Ego
  → En el tiempo que toma beber un café, tu IA personalizada está lista

Paso 4: Usar Ego
  $ ego run --edition normal
  → Cargar base + LoRA, iniciar un modelo de IA alineado contigo
  → Intenta chatear con él, mira si "suena como tú"

Paso 5: Actualizar Ego (después de cambios en los datos)
  $ ego train --update
  → Recolección incremental de datos → reentrenar → sobrescribir versión anterior
  → ¿Aprendiste algo nuevo recientemente? Forja de nuevo, y Ego se pone al día
```

## Integración con iFay

El cuerpo de iFay le indica a Ego las direcciones de fuentes de datos a través de archivos de configuración. Ego Pod completa automáticamente el entrenamiento, y los adaptadores LoRA producidos son cargados por Fayger.

> Por ejemplo: Tu iFay se ejecuta en tu teléfono, sincronizando periódicamente nuevos datos a un directorio designado, luego activando a Ego Pod para reentrenar. Después de que el entrenamiento se completa, el nuevo adaptador LoRA se envía automáticamente a tu teléfono, y Ego "evoluciona" — todo el proceso es invisible para ti.
