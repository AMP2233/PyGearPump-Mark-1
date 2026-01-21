# 🚀 Sistema de Diseño Paramétrico de Bombas de Engranajes - Mark I

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
