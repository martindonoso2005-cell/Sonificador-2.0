# Sonificación de Imágenes — Escáner Espectral Audiovisual
**Asignatura:** Taller de Interfaces (2026) | Universidad Adolfo Ibáñez  
**Estudiante:** Martín Donoso  
**Profesor:** Jorge Forero  
**Aplicación Web:** [Ver Sonificador Desplegado](https://martindonoso2005-cell.github.io/Sonificador-2.0/)  

---

## 1. Descripción del Proyecto
Herramienta web interactiva desarrollada para la traducción algorítmica de matrices de píxeles bidimensionales a síntesis de audio en tiempo real. Inspirado en las técnicas de sonificación espacial de la NASA y el Telescopio Espacial James Webb (JWST), el sistema escanea visualmente la imagen columna por columna mediante síntesis aditiva polifónica.

* **Interfaz:** Diseño retro *Pixel Art* estructurado con tipografía *Press Start 2P* y fondo procedural inspirado en los "Cosmic Cliffs" de la Nebulosa Carina.
* **Motor de Audio:** Síntesis nativa en el navegador mediante **Web Audio API** con compresión dinámica y control de envolventes lineales y exponenciales.

---

## 2. Estrategia y Criterio de Mapeo Espectral

El algoritmo recorre la imagen en un barrido temporal y descompone las columnas en un número seleccionable de voces polifónicas, asignando coordenadas espaciales y canales cromáticos (RGB) a parámetros de sonido:

| Dimensión / Canal | Tipo de Dato | Parámetro Acústico Mapeado | Implementación Técnica en Código |
| :--- | :--- | :--- | :--- |
| **Eje Horizontal ($X$)** | Espacial continuo | **Línea de Tiempo / Secuencia** | Cabezal de lectura que avanza a velocidad regulable por frame. |
| **Eje Vertical ($Y$)** | Espacial discreto | **Frecuencia Fundamental ($f_0$)** | Mapeado a una escala pentatónica de 16 pasos (130.81 Hz – 1046.50 Hz). Las alturas superiores corresponden a tonos más agudos. |
| **Canal Rojo (R)** | Cromático (0–255) | **Micro-afinación / Desviación** | Modula la frecuencia base hasta un +40% según la intensidad de rojo: $f = f_0 \cdot \left(1 + \frac{R}{255} \cdot 0.4\right)$. |
| **Canal Verde (G)** | Cromático (0–255) | **Timbre / Riqueza Armónica** | Controla la ganancia de armónicos superiores (ondas triangulares en armónicos 2 y 3). |
| **Canal Azul (B)** | Cromático (0–255) | **Amplitud / Dinámica** | Define la ganancia de salida de cada oscilador proporcional a la densidad de azul: $\text{Ganancia} \propto \frac{B}{255}$. |

---

## 3. Controles e Interacción en la Plataforma Web

La interfaz ofrece control total sobre los parámetros de renderizado sonoro en tiempo real:

* **Controles de Transporte:**
  * `► INICIAR SONIFICACIÓN` / `❚❚ PAUSAR`: Dispara o detiene el barrido del cabezal.
  * `↺ REINICIAR`: Restablece la posición del escáner al margen izquierdo ($X = 0$).
* **Entrada de Imagen:** Selector de archivo (`input type="file"`) que permite cargar y analizar cualquier imagen local personalizada.
* **Ajuste de Parámetros:**
  * **Volumen Master:** Control continuo de ganancia con etapa final de limitación por compresor dinámico.
  * **Velocidad de Escaneo:** Regula el avance del cabezal de lectura en píxeles por frame.
  * **Voces Verticales ($Y$):** Selector de polifonía simultánea (entre 8 y 32 osciladores activos por columna).

---

## 4. Diagrama de Flujo del Algoritmo

```text
[ Cargar Imagen (Default o Custom) ]
               │
               ▼
[ Click en INICIAR SONIFICACIÓN ] ──► [ Inicializar AudioContext + Compresor ]
               │
               ▼
[ Bucle requestAnimationFrame ]
       │
       ├─► 1. Obtener píxeles de la columna actual X (getImageData)
       ├─► 2. Muestrear N puntos verticales según "Voces Y"
       ├─► 3. Para cada voz:
       │         • Asignar nota de la escala pentatónica según Y
       │         • Modular tono fundamental con el valor R
       │         • Sumar armónicos 2 y 3 según el valor G
       │         • Aplicar envolvente de volumen según el valor B
       ├─► 4. Dibujar línea del cabezal en canvas overlay
       ├─► 5. X = X + Velocidad
       │
       ▼
[ ¿X >= Ancho de Imagen? ]
       ├── SÍ ──► Detener y reiniciar X = 0
       └── NO ──► Siguiente frame
## 5. Estructura del Repositorio
├── index.html        # Aplicación web completa (estructura HTML, estilos retro y motor Web Audio API)
└── README.md         # Documentación técnica, justificación metodológica y guía de uso
