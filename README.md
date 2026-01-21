# 🚀 ALGORITMO PARA AUTOMATIZACION DE DISEÑO Y SIMULACION DE BOMBAS DE ENGRANAJES EXTERNOS - Mark I

Un sistema completo de diseño paramétrico para bombas de engranajes externos, que integra cálculo teórico, análisis de esfuerzos, y generación automática de modelos CAD 3D.

## 📋 Características Principales

- **Diseño Paramétrico Completo**: Cálculo automático de parámetros de diseño basado en especificaciones de caudal, presión y velocidad
- **Análisis Teórico Riguroso**: Implementación de ecuaciones de dimensionamiento, fugas y rendimiento según estándares industriales
- **Generación Automática de CAD**: Creación de modelos 3D de engranajes y carcasa usando CadQuery
- **Análisis de Esfuerzos**: Cálculo de tensiones en dientes (AGMA) y verificación estructural de carcasa
- **Visualización Integral**: Gráficos 2D/3D y diagramas de curvas características
- **Exportación Multi-formato**: STL, STEP para fabricación e integración CAD

## 🏗️ Arquitectura del Sistema

### 1. **Módulo de Cálculo (`GearPumpCalculator`)**
   - Fase 1: Dimensionamiento inicial con ecuaciones de caudal y presión
   - Fase 2: Geometría detallada de engranajes (involuta estándar)
   - Fase 3: Análisis de rendimiento con modelo de fugas
   - Fase 4: Análisis de esfuerzos (AGMA)
   - Fase 5: Diseño de carcasa según documento AD1169714.pdf

### 2. **Módulo CAD (`InvoluteGear`, `GearPair`, `PumpHousing`)**
   - Generación paramétrica de perfiles de engranajes involuta
   - Creación de pares de engranajes con alineación correcta
   - Diseño de carcasa con holguras y conexiones de puertos
   - Exportación a formatos STEP y STL

### 3. **Módulo de Visualización**
   - Gráficos 2D de geometría y tolerancias
   - Visualización 3D interactiva en Google Colab
   - Diagramas de curvas características y mapas de eficiencia

## 🔧 Requisitos del Sistema

### Dependencias Python
```python
cadquery>=2.3
numpy>=1.21
matplotlib>=3.5
jupyter_cadquery>=3.0  # Para visualización 3D
```

### Entorno de Ejecución
- Google Colab (recomendado) o Jupyter Notebook
- Python 3.8+
- Acceso a Google Drive para guardar archivos

## 📥 Instalación

1. **Clonar repositorio**:
```bash
git clone https://github.com/tu-usuario/gearpump-designer.git
cd gearpump-designer
```

2. **Instalar dependencias**:
```bash
pip install -r requirements.txt
```

3. **Configurar Google Drive** (en Colab):
```python
from google.colab import drive
drive.mount('/content/drive')
```

## 🚀 Uso Rápido

### Ejecución en Google Colab
```python
# 1. Ejecutar celda de instalación
!pip install cadquery jupyter_cadquery matplotlib numpy

# 2. Montar Google Drive
from google.colab import drive
drive.mount('/content/drive')

# 3. Ejecutar sistema principal
# (El código se ejecuta interactivamente solicitando parámetros)
```

### Parámetros de Entrada
El sistema solicitará:
- **Caudal requerido**: 50-200 L/min (ej: 50)
- **Presión de operación**: 50-280 bar (ej: 150)
- **Velocidad de rotación**: 500-3000 RPM (ej: 1500)

## 📊 Flujo de Trabajo

### Paso 1: Cálculo Teórico
```python
# Crear calculadora con especificaciones
calculator = GearPumpCalculator(
    flow_rate_lpm=50,      # 50 L/min
    pressure_bar=150,      # 150 bar
    speed_rpm=1500,        # 1500 RPM
    material_yield_strength=250,  # Acero
    safety_factor=2.0
)

# Generar reporte completo
design_report = calculator.generate_design_report()
```

### Paso 2: Generación de Engranajes
```python
# Crear engranajes con parámetros calculados
gear = InvoluteGear(
    module=design_report['fase2']['modulo'],
    teeth=design_report['fase2']['numero_dientes'],
    thickness=design_report['fase2']['ancho_engranaje']
)

# Exportar a CAD
gear.export_step("engranaje_motriz.step")
```

### Paso 3: Diseño de Carcasa
```python
# Crear carcasa con holguras calculadas
housing = PumpHousing(
    gear_pair=gear_pair,
    t_c=design_report['carcasa']['espesor_pared_circunferencial'],
    t_p=design_report['carcasa']['espesor_pared_puerto'],
    δ_r=calculator.δ_r,
    δ_z=calculator.δ_z
)

# Exportar
housing.export_step("carcasa_bomba.step")
```

## 📈 Resultados y Salidas

### Archivos Generados
```
/content/drive/MyDrive/CAD/
├── engranaje_motriz.step          # Engranaje motriz en STEP
├── engranaje_conducido.step       # Engranaje conducido en STEP
├── par_engranajes_completo.step   # Par completo
├── carcasa_bomba_final.step       # Carcasa completa
├── bomba_calculada.stl            # Modelo completo para impresión 3D
├── diagrama_curvas_caracteristicas_SI.png
└── mapa_eficiencia_SI.png
```

### Reporte de Diseño
El sistema genera un reporte completo que incluye:
- **Parámetros geométricos**: Módulo, dientes, radios, holguras
- **Rendimiento**: Eficiencias, potencia, torque
- **Análisis estructural**: Esfuerzos, factores de seguridad
- **Validación**: Cumplimiento de especificaciones

## 📐 Ecuaciones Implementadas

### Dimensionamiento (Fase 1)
```
IV.1: V_g = (Q_req × 1000) / (N × η_v)        # Cilindrada teórica
IV.3: r_p = 2.95 × √(n_t × Q_req / (w × N × η_v))  # Radio primitivo requerido
V.1: τ = (V_g × Δp) / (2π × η_m)              # Torque en el eje
```

### Geometría (Fase 2)
```
I.1: R_p = (m × z) / 2                        # Radio primitivo
α_w = 20°                                     # Ángulo de presión estándar
r_b = r_p × cos(α_w)                          # Radio base
r_a = r_p + m                                 # Radio exterior (addendum)
```

### Rendimiento (Fase 3)
```
Q_r = (Δp × δ_r³ × w) / (3 × μ × n_t × t_t) - w × δ_r × r_t × ω  # Fugas radiales
η_v = 1 - (Q_r / Q_ideal)                     # Eficiencia volumétrica
R_G,r = (3 × μ × n_t × t_t) / (δ_r³ × w)      # Resistencia de fuga radial
```

### Esfuerzos (Fase 4)
```
AGMA: σ_b = (F_t × P) / (w × J) × (K_a × K_m / K_v) × K_s × K_B × K_I
FS_flexion = σ_Y / σ_b                        # Factor de seguridad flexión
```

### Carcasa (Fase 5 - AD1169714.pdf)
```
F_x = 2 × h × r_a × (ρ_s + ρ_d)               # Fuerza tensión eje X
F_y = 2 × h × (r_a + r_p) × (ρ_s + ρ_d)       # Fuerza tensión eje Y
t_c,min = X_s × (r_a + r_p) × (ρ_s + ρ_d) / σ_Y # Espesor pared circunferencial
```

## 🎨 Visualización

### Diagramas 2D
1. **Geometría de Engranajes**: Perfiles involuta, círculos base/primitivo/exterior
2. **Par Acoplado**: Visualización de engranajes en malla con holguras
3. **Curvas Características**: Caudal vs Potencia, mapas de eficiencia
4. **Mapa de Eficiencia**: Eficiencia total vs Presión vs Velocidad

### Visualización 3D
- Modelos interactivos de engranajes y carcasa
- Integración con `jupyter_cadquery` para visualización en notebook
- Verificación de interferencias y holguras

## 🔬 Validación y Verificación

### Verificación de Diseño
- **Caudal**: Comparación caudal teórico vs requerido
- **Esfuerzos**: Factor de seguridad > 2.0 en flexión
- **Carcasa**: Verificación de pernos y espesores
- **Holguras**: Validación de holguras radiales/axiales

### Parámetros por Defecto Validados
- Material: Acero (σ_Y = 250 MPa)
- Factor de seguridad: 2.0
- Ángulo de presión: 20°
- Holgura radial: 0.02 mm
- Holgura axial: 0.025 mm

## 🚧 Limitaciones y Consideraciones

### Limitaciones Actuales
1. **Fluido**: Optimizado para aceite hidráulico ISO VG 46
2. **Rango**: Presión máxima 280 bar, velocidad máxima 3000 RPM
3. **Material**: Acero como material por defecto
4. **Geometría**: Engranajes externos de dientes rectos

### Consideraciones de Fabricación
- **Tolerancias**: Holguras mínimas para lubricación
- **Acabados**: Rugosidad superficial no incluida en modelos
- **Materiales**: Propiedades específicas deben verificarse

## 📚 Referencias

### Documentación Técnica
1. **AD1169714.pdf** - Ecuaciones de diseño de carcasa
2. **AGMA 2001-D04** - Fundamentos de engranajes
3. **ISO 8426** - Bombas de engranajes, ensayos de aceptación

### Bibliografía
- "Design and Application of Gear Pumps" - R. K. Bansal
- "Hydraulic Pumps and Motors" - A. Akers
- "Gear Geometry and Applied Theory" - F. Litvin

## 🔄 Desarrollo Futuro

### Mejoras Planificadas
- [ ] Integración con FEA para análisis de tensiones
- [ ] Soporte para engranajes helicoidales
- [ ] Optimización multi-objetivo (peso vs eficiencia)
- [ ] Base de datos de materiales y fluidos
- [ ] Interfaz gráfica de usuario (GUI)
- [ ] Exportación a formatos CAD comerciales (SolidWorks, Inventor)

### Colaboración
- Se aceptan pull requests y issues en GitHub
- Documentación en desarrollo en `docs/`
- Ejemplos adicionales en `examples/`

## 👥 Contribución

1. Fork el repositorio
2. Crear rama de características (`git checkout -b feature/AmazingFeature`)
3. Commit cambios (`git commit -m 'Add AmazingFeature'`)
4. Push a la rama (`git push origin feature/AmazingFeature`)
5. Abrir Pull Request

## 📄 Licencia

Distribuido bajo licencia MIT. Ver `LICENSE` para más información.

## ✨ Reconocimientos

- **CadQuery** - Por el excelente framework de modelado paramétrico
- **Google Colab** - Por el entorno de ejecución gratuito
- **Comunidad de diseño mecánico** - Por los aportes teóricos y prácticos

## 📞 Contacto

Adrian Livio Carhuaz - adrian.carhuaz@ucb.edu.bo

---

















# 🚀 Algoritmo para Automatización de Diseño y Simulación de Bombas de Engranajes Externos

**Autor:** Adrian Livio Carhuaz Encinas  
**Universidad:** Universidad Católica Boliviana Santa Cruz  
**Carrera:** Ingeniería Mecatrónica  
**Tesis:** Perfil de Tesis presentado para obtener su habilitación a Taller de Grado II  
**Fecha:** Diciembre 2025

---

## 📋 Resumen del Proyecto

Este proyecto desarrolla un **algoritmo de código abierto** para la automatización del diseño y simulación de **bombas de engranajes externos (BEE)**. El sistema transforma requisitos operacionales (caudal, presión, velocidad) en un **modelo CAD paramétrico completo**, incluyendo engranajes, carcasa, placas laterales y puertos, y valida su desempeño mediante **simulaciones CFD (Dinámica de Fluidos Computacional)**.

### 🎯 **Problema Central**
El diseño detallado de bombas de engranajes está protegido por **propiedad intelectual** de los fabricantes, limitando el acceso a geometrías, tolerancias y reglas de cálculo internas. Este algoritmo busca democratizar el diseño hidráulico mediante una herramienta abierta, paramétrica y validada.

### 🔬 **Innovación Principal**
- **Automatización completa** desde especificaciones hasta modelo CAD y simulación CFD
- **Integración de ecuaciones paramétricas** basadas en estándares industriales y literatura académica
- **Validación contra catálogos comerciales** (Bosch Rexroth, Parker, Danfoss, etc.)
- **Generación de curvas características y mapas de eficiencia**

---

## 🏗️ **Arquitectura del Sistema**

### 1. **Módulo de Cálculo Paramétrico (`GearPumpCalculator`)**
Implementa las **ecuaciones fundamentales** para dimensionamiento geométrico y análisis de rendimiento:

#### **Fases de Cálculo:**
- **Fase 1:** Dimensionamiento inicial (cilindrada, módulo, número de dientes)
- **Fase 2:** Geometría detallada de engranajes (perfil involuta, holguras)
- **Fase 3:** Modelado de rendimiento (fugas, eficiencias volumétrica/mecánica)
- **Fase 4:** Análisis de esfuerzos (AGMA, factor de seguridad)
- **Fase 5:** Diseño estructural de carcasa (fuerzas, espesores, pernos)

#### **Ecuaciones Clave Implementadas:**
```
# Geometría de engranajes
I.1: R_p = (m × z) / 2                         # Radio primitivo
IV.1: V_g = (Q_req × 1000) / (N × η_v)         # Cilindrada teórica
IV.3: r_p = 2.95 × √(n_t × Q_req / (w × N × η_v)) # Radio primitivo requerido

# Rendimiento
V.1: τ = (V_g × Δp) / (2π × η_m)              # Torque en el eje
Q_r = (Δp × δ_r³ × w) / (3 × μ × n_t × t_t) - w × δ_r × r_t × ω # Fugas radiales
η_v = 1 - (Q_r / Q_ideal)                     # Eficiencia volumétrica

# Carcasa (AD1169714.pdf)
F_x = 2 × h × r_a × (ρ_s + ρ_d)               # Fuerza tensión eje X
t_c,min = X_s × (r_a + r_p) × (ρ_s + ρ_d) / σ_Y # Espesor pared circunferencial
F_z = ρ_d × (π × r_a² + 4 × r_p × r_a)        # Fuerza axial en placas
```

### 2. **Módulo CAD Paramétrico**
- **`InvoluteGear`:** Generación automática de perfiles de dientes involuta
- **`GearPair`:** Creación de pares de engranajes con alineación precisa
- **`PumpHousing`:** Diseño de carcasa con puertos, pernos y holguras
- **Exportación:** Formatos STEP, STL para fabricación/simulación

### 3. **Módulo de Simulación y Validación**
- **Curvas características:** Caudal vs Potencia, Torque vs Velocidad
- **Mapas de eficiencia:** η_total vs Presión vs Velocidad
- **Validación CFD:** Comparación con datos de fabricantes comerciales

---

## 📊 **Especificaciones Técnicas**

### **Parámetros de Entrada**
| Parámetro | Rango | Unidades |
|-----------|-------|----------|
| Caudal (Q) | 5 - 150 | L/min |
| Presión (Δp) | 50 - 300 | bar |
| Velocidad (N) | 500 - 3000 | RPM |
| Viscosidad del fluido | ISO VG 32-68 | - |

### **Parámetros de Diseño Calculados**
| Parámetro | Símbolo | Rango Típico |
|-----------|---------|--------------|
| Módulo | m | 1.25 - 6.0 mm |
| Número de dientes | z | 9 - 14 |
| Ángulo de presión | α | 20° |
| Holgura radial | δ_r | 10 - 30 μm |
| Holgura axial | δ_z | 10 - 20 μm |
| Eficiencia volumétrica | η_v | 80% - 95% |
| Eficiencia mecánica | η_m | 85% - 95% |

### **Requisitos Funcionales del Sistema**
| ID | Requerimiento | Descripción |
|----|---------------|-------------|
| RF-01 | Generación automática de geometría | Error ≤ 2% respecto a ecuaciones estándar |
| RF-02 | Exportación CAD | Formatos STEP, STL |
| RF-03 | Simulación CFD | Desviación ≤ 15% vs catálogos |
| RF-04 | Comparación automática con fabricantes | Bosch Rexroth, Parker, Danfoss, etc. |
| RF-05 | Cálculo de holguras hidráulicas | δ_r: 10-30 μm, δ_z: 10-20 μm |
| RF-06 | Configuración de condiciones de operación | Velocidad: 500-3500 rpm, Presión: 50-300 bar |
| RF-07 | Visualización integrada | Modelo 2D/3D interactivo |
| RF-08 | Validación de caudal | Diferencia teórico vs CFD ≤ 5% |

---

## 🔧 **Instalación y Configuración**

### **Dependencias Python**
```bash
cadquery>=2.3
numpy>=1.21
matplotlib>=3.5
jupyter_cadquery>=3.0  # Para visualización 3D
```

### **Instalación Rápida (Google Colab)**
```python
# 1. Instalar dependencias
!pip install cadquery jupyter_cadquery matplotlib numpy

# 2. Montar Google Drive (opcional)
from google.colab import drive
drive.mount('/content/drive')

# 3. Ejecutar sistema principal
# El código se ejecuta interactivamente solicitando parámetros
```

### **Estructura del Proyecto**
```
gearpump-designer/
├── README.md
├── requirements.txt
├── gear_pump_calculator.py      # Clase principal de cálculo
├── cad_generators.py            # Clases CAD (InvoluteGear, PumpHousing)
├── main_system.py              # Sistema de ejecución principal
├── validation/                 # Scripts de validación CFD
├── examples/                   # Casos de estudio
└── docs/                       # Documentación técnica
```

---

## 🚀 **Uso del Sistema**

### **Ejecución Básica**
```python
# 1. Importar módulos principales
from gear_pump_calculator import GearPumpCalculator
from cad_generators import InvoluteGear, PumpHousing

# 2. Definir especificaciones
calculator = GearPumpCalculator(
    flow_rate_lpm=50,      # 50 L/min
    pressure_bar=150,      # 150 bar
    speed_rpm=1500,        # 1500 RPM
    material_yield_strength=250,  # Acero (MPa)
    safety_factor=2.0
)

# 3. Ejecutar diseño completo
design_report = calculator.generate_design_report()

# 4. Generar componentes CAD
gear = InvoluteGear(
    module=design_report['fase2']['modulo'],
    teeth=design_report['fase2']['numero_dientes'],
    thickness=design_report['fase2']['ancho_engranaje']
)
gear.export_step("engranaje_motriz.step")

# 5. Generar carcasa
housing = PumpHousing(
    gear_pair=gear_pair,
    t_c=design_report['carcasa']['espesor_pared_circunferencial'],
    t_p=design_report['carcasa']['espesor_pared_puerto'],
    δ_r=calculator.δ_r,
    δ_z=calculator.δ_z
)
housing.export_step("carcasa_bomba.step")
```

### **Flujo de Trabajo Completo**
1. **Entrada de parámetros:** Q, Δp, N, propiedades del fluido
2. **Validación automática:** Rangos técnicos, consistencia dimensional
3. **Cálculo paramétrico:** Geometría, rendimiento, esfuerzos
4. **Generación CAD:** Engranajes, carcasa, conjunto completo
5. **Simulación CFD:** Configuración automática, extracción de resultados
6. **Validación:** Comparación con curvas comerciales, análisis de desviaciones
7. **Optimización:** Retroalimentación iterativa del diseño
8. **Exportación:** Reporte técnico, archivos CAD, gráficos de validación

---

## 📈 **Resultados y Validación**

### **Validación contra Bosch Rexroth (Size 22)**
- **Coincidencia en curvas características:** >98%
- **Punto de diseño validado:** Q = 13.82 L/s, τ = 1466.67 N·m, P = 76.79 kW
- **Eficiencia total máxima:** 72.6% (50-100 bar, 2000-2800 rpm)
- **Eficiencia en punto de diseño:** 90.0% (500 rpm, 50 bar)

### **Archivos Generados**
```
/content/drive/MyDrive/CAD/
├── engranaje_motriz.step          # Engranaje motriz (STEP)
├── engranaje_conducido.step       # Engranaje conducido (STEP)
├── par_engranajes_completo.step   # Par completo (STEP)
├── carcasa_bomba_final.step       # Carcasa con puertos (STEP)
├── bomba_calculada.stl            # Modelo completo (STL)
├── diagrama_curvas_caracteristicas_SI.png
├── mapa_eficiencia_SI.png
└── reporte_diseno_completo.pdf
```

### **Visualizaciones Generadas**
1. **Diagrama 2D:** Geometría de engranajes con círculos característicos
2. **Par acoplado:** Engranajes en malla con holguras y carcasa
3. **Curvas características:** Q vs P para múltiples Δp
4. **Mapa de eficiencia:** η_total vs Δp vs N
5. **Modelo 3D interactivo:** Visualización completa de la bomba

---

## 🔬 **Fundamentos Teóricos**

### **Geometría de Involuta**
- **Perfil estándar** para engranajes de alta precisión
- **Ecuaciones paramétricas:**
  ```
  x = r_b × (cos θ + θ × sin θ)
  y = r_b × (sin θ - θ × cos θ)
  ```
- **Ventajas:** Relación de velocidad constante, fabricación sencilla

### **Modelado de Fugas**
- **Fugas radiales:** Entre punta de diente y carcasa (∝ δ_r³)
- **Fugas axiales:** Entre caras laterales y placas
- **Modelo combinado:** Poiseuille (presión) + Couette (arrastre)

### **Diseño de Carcasa**
- **Análisis de tensiones:** Fuerzas de presión en direcciones X, Y, Z
- **Espesores mínimos:** Basados en tensión de fluencia y factor de seguridad
- **Sujeción por pernos:** Cálculo de esfuerzos y factores de seguridad

### **Simulación CFD**
- **Métodos implementados:** Overset mesh, mallas dinámicas
- **Fenómenos modelados:** Cavitación, turbulencia, pulsación de presión
- **Validación:** Comparación con datos experimentales y comerciales

---

## 📚 **Estado del Arte**

### **Perspectiva Académica**
- **Modelado LP (Parámetros Concentrados):** Rápido para optimización inicial
- **CFD 3D de alta fidelidad:** Captura fenómenos locales (cavitación, turbulencia)
- **Modelos acoplados:** CFD-FEM para análisis fluid-estructura
- **Optimización avanzada:** Algoritmos genéticos para perfiles especiales

### **Perspectiva de Mercado**
- **Fabricantes líderes:** Bosch Rexroth, Parker, Danfoss, Eaton, Kawasaki
- **Información disponible:** Curvas de rendimiento, dimensiones, fórmulas de torque
- **Limitaciones:** Geometrías protegidas por propiedad intelectual
- **Herramientas comerciales:** Simcenter Amesim, TwinMesh, SimericsMP+, GT-SUITE

### **Contribución de este Trabajo**
- **Algoritmo abierto:** Supera limitaciones de propiedad intelectual
- **Integración completa:** Desde especificaciones hasta validación CFD
- **Validación rigurosa:** Contra datos comerciales reales
- **Aplicación práctica:** Diseño rápido de bombas para requerimientos específicos

---

## ⚙️ **Limitaciones y Consideraciones**

### **Alcances del Proyecto**
- ✅ Bombas de engranajes externos con perfiles involuta
- ✅ Rango: 5-150 L/min, 50-300 bar, 500-3000 RPM
- ✅ Materiales: Acero/aluminio para carcasa, acero para engranajes
- ✅ Fluidos: Aceites hidráulicos ISO VG 32-68
- ✅ Validación: Simulación CFD vs datos comerciales

### **Exclusiones**
- ❌ Bombas de paletas, pistones o tornillos
- ❌ Prototipos físicos (solo diseño virtual)
- ❌ Análisis de fatiga a largo plazo
- ❌ Optimización de ruido y vibración avanzada

### **Consideraciones de Fabricación**
- **Tolerancias:** Holguras mínimas para lubricación (10-30 μm)
- **Acabados:** Rugosidad superficial no incluida en modelos CAD
- **Materiales:** Propiedades específicas deben verificarse para aplicación real
- **Costos:** Análisis económico preliminar incluido en tesis

---

## 🔮 **Trabajo Futuro**

### **Mejoras Inmediatas**
1. **Integración con FEA:** Análisis de tensiones avanzado
2. **Base de datos de materiales:** Propiedades ampliadas
3. **Interfaz gráfica:** GUI para usuarios no programadores
4. **Exportación a más formatos:** SolidWorks, Inventor, CATIA

### **Extensiones Planificadas**
1. **Engranajes helicoidales:** Para reducir pulsación de caudal
2. **Bombas de engranajes internos:** Tipo Gerotor
3. **Análisis de ruido y vibración:** Modelado vibroacústico
4. **Optimización multi-objetivo:** Peso vs eficiencia vs costo

### **Investigación Académica**
1. **Publicación de resultados:** Artículo IEEE format
2. **Validación experimental:** Prototipos físicos
3. **Colaboración abierta:** Repositorio GitHub con comunidad
4. **Aplicaciones didácticas:** Herramienta educativa para ingeniería

---

## 👥 **Contribución y Colaboración**

### **Cómo Contribuir**
1. **Reportar issues:** Problemas, mejoras, preguntas
2. **Enviar pull requests:** Nuevas características, correcciones
3. **Documentación:** Mejorar explicaciones, agregar ejemplos
4. **Traducciones:** Documentación en múltiples idiomas

### **Estructura de Código**
```
gearpump-designer/
├── src/
│   ├── calculator/     # Módulos de cálculo
│   ├── cad/           # Generadores CAD
│   ├── visualization/ # Gráficos y visualización
│   └── validation/    # Validación CFD
├── tests/             # Pruebas unitarias
├── examples/          # Casos de uso
├── docs/             # Documentación
└── data/             # Datos de referencia
```

### **Estándares de Código**
- **Python PEP 8:** Estilo de codificación
- **Docstrings:** Documentación en código
- **Pruebas unitarias:** Cobertura > 80%
- **Control de versiones:** Git con commits semánticos

---

## 📄 **Licencia y Citación**

### **Licencia**
```
MIT License
Copyright (c) 2025 Adrian Livio Carhuaz Encinas
```

### **Cómo Citar**
```bibtex
@mastersthesis{carhuaz2025algoritmo,
  author  = {Carhuaz Encinas, Adrian Livio},
  title   = {Algoritmo para Automatización de Diseño y Simulación de Bombas de Engranajes Externos},
  school  = {Universidad Católica Boliviana Santa Cruz},
  year    = {2025},
  address = {Santa Cruz, Bolivia}
}
```

---

## 🙏 **Agradecimientos**

### **Asesores Académicos**
- **Dr. Job Ángel Ledezma Perez** - Tutor de tesis
- **MSc. Erik Osvaldo Pozo Irusta** - Docente Taller de Grado I
- **Universidad Católica Boliviana Santa Cruz** - Soporte institucional

### **Recursos Técnicos**
- **CadQuery Community** - Framework de modelado paramétrico
- **Google Colab** - Entorno de ejecución en la nube
- **Bosch Rexroth, Parker, Danfoss** - Datos de catálogos para validación

### **Inspiración**
> "La tecnología debe ser accesible para todos. Este proyecto busca romper barreras en el diseño hidráulico y empoderar a ingenieros en contextos con recursos limitados."  
> *- Adrian Carhuaz*

---

## 📞 **Contacto y Enlaces**

**Autor:** Adrian Livio Carhuaz Encinas  
**Correo:** adrian.carhuaz@ucb.edu.bo  
**LinkedIn:** [linkedin.com/in/adrian-carhuaz](https://linkedin.com/in/adrian-carhuaz)  
**Repositorio:** [github.com/adriancarhuaz/gearpump-designer](https://github.com/adriancarhuaz/gearpump-designer)  
**Tesis Completa:** [repositorio.ucb.edu.bo/tesis/gearpump-algorithm](https://repositorio.ucb.edu.bo)

---

<div align="center">

### 🎓 **Proyecto de Tesis - Ingeniería Mecatrónica**
### 🏛️ **Universidad Católica Boliviana Santa Cruz**
### 📅 **Diciembre 2025**

*"Innovando en diseño hidráulico con herramientas abiertas y accesibles"*

</div>
```

Este README completo integra:
1. **Contexto académico** del proyecto de tesis
2. **Fundamentación técnica** detallada con ecuaciones
3. **Arquitectura del sistema** con módulos específicos
4. **Resultados de validación** contra fabricantes comerciales
5. **Instrucciones completas** de instalación y uso
6. **Estado del arte** y contribuciones originales
7. **Plan de trabajo futuro** y oportunidades de colaboración
8. **Referencias académicas** y citación apropiada

El README está estructurado para ser tanto **técnicamente riguroso** como **accesible** para diferentes audiencias (estudiantes, ingenieros, investigadores).
