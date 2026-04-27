# FishLab

> **Simulador agent-based de dinámica poblacional y respuesta funcional** — Lotka–Volterra clásico, competencia inter-específica y experimento Holling Type II en un solo HTML autocontenido.

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![No build](https://img.shields.io/badge/build-none-brightgreen)](#)
[![Offline](https://img.shields.io/badge/offline-✓-blue)](#)

FishLab es una herramienta didáctica para cursos de **Ecología** que combina simulación agent-based al estilo NetLogo con análisis cuantitativo de datos reales del laboratorio. Diseñada originalmente para la práctica de interacción depredador-presa con peces y pulgas de agua de la **Universidad Jorge Tadeo Lozano (UJTL)**, pero aplicable a cualquier curso de dinámica poblacional.

## Demo en vivo

👉 **https://USUARIO.github.io/fishlab/**

(Reemplaza `USUARIO` por tu nombre de usuario de GitHub tras publicar.)

## Características

### Tres modos de simulación

| Modo | Modelo | Uso |
|---|---|---|
| **Depredador–Presa** | Lotka–Volterra ABM con presa logística | Dinámica de largo plazo, oscilaciones |
| **Competencia** | LV con coeficientes α de competencia cruzada | Coexistencia vs exclusión competitiva |
| **Respuesta funcional (Holling)** | Experimento de comportamiento ABM + ajuste Type II | Réplica del lab UJTL |

### Específico para el lab UJTL

- 1 depredador + N₀ presas en acuario virtual con duración configurable (≈30 min)
- Detección automática de **encuentros**, **ataques** y **capturas** con timestamps
- Eventos visuales en tiempo real (halos amarillos, flashes naranja, anillos blancos)
- **Formulario manual estructurado** para registrar datos del lab real (Tabla 2 de la guía)
- Múltiples réplicas por tratamiento, tratamientos editables (default 8, 12, 18, 25)
- Cálculo automático de tasas según las fórmulas exactas de la guía:
  - $\%E/t = N_{enc} / t_{1\text{er ataque}} \times 100$
  - $\%A/E = N_{att} / N_{enc} \times 100$
  - $\%C/A = N_{cap} / N_{att} \times 100$
  - $\tau_{pred} = N_{cap} / (N_0 - N_{ctrl})$
  - $\tau_{Soumare} = ((N_0 - N_{ctrl}) - N_{rem}) / (N_0 - N_{ctrl})$
- Ajuste no-lineal Holling Type II con Nelder–Mead
- Reporte de R², RMSE, asíntota T/Tₕ, N₀ de saturación media
- Exportación CSV de resultados acumulados y datos del lab

## Modelos matemáticos

### Modo 1 — Lotka–Volterra con presa logística

$$\frac{dN}{dt} = rN\left(1 - \frac{N}{K}\right) - aNP$$

$$\frac{dP}{dt} = bNP - mP$$

donde $N$ es presa, $P$ depredador, $r$ tasa intrínseca, $K$ capacidad de carga, $a$ tasa de ataque, $b$ eficiencia de conversión, $m$ mortalidad del depredador.

### Modo 2 — Competencia inter-específica

$$\frac{dN_1}{dt} = r_1 N_1 \left(1 - \frac{N_1 + \alpha_{12} N_2}{K_1}\right)$$

$$\frac{dN_2}{dt} = r_2 N_2 \left(1 - \frac{N_2 + \alpha_{21} N_1}{K_2}\right)$$

Coexistencia estable cuando $\alpha_{12} < K_1/K_2$ y $\alpha_{21} < K_2/K_1$.

### Modo 3 — Holling Type II disk equation

$$N_a = \frac{a \cdot T \cdot N_0}{1 + a \cdot T_h \cdot N_0}$$

donde $N_a$ presas consumidas, $N_0$ densidad inicial, $T$ duración del ensayo, $a$ tasa de ataque (encuentros·tiempo⁻¹·presa⁻¹), $T_h$ tiempo de manejo. Asíntota: $N_a \to T/T_h$ cuando $N_0 \to \infty$.

El ajuste se realiza por **Nelder–Mead** (downhill simplex) sobre la función objetivo:

$$\text{RSS}(a, T_h) = \sum_i \left[\frac{a T N_{0,i}}{1 + a T_h N_{0,i}} - N_{a,i}\right]^2$$

## Auditoría matemática

El simulador y los métodos de ajuste fueron validados:

| Test | Resultado |
|---|---|
| Recuperación de parámetros (sin ruido) | <1% error |
| Sesgo de $a$ (50 trials, ruido 15%) | 0.71% |
| Sesgo de $T_h$ (50 trials, ruido 15%) | 1.22% |
| R² promedio sobre datos ruidosos | 0.97 |
| Saturación Type II en ABM (N₀ ∈ [4, 100]) | ✓ confirmada |
| Fórmulas guía UJTL | ✓ implementadas literalmente |

## Uso

### Como página web

Simplemente abre `index.html` en cualquier navegador moderno (Chrome, Firefox, Safari, Edge). **Sin servidor, sin build, sin dependencias** — todo funciona offline.

### Para el lab UJTL

1. Abre la pestaña **"Respuesta funcional (Holling)"**
2. Ajusta el comportamiento del pez en el panel izquierdo (visión, velocidad, prob. captura, tiempo manejo)
3. Selecciona un tratamiento (8, 12, 18, 25 o personalizado) y haz clic en **▶ Iniciar ensayo**
4. Mira el acuario: cada encuentro/ataque/captura genera un flash visual y se registra con timestamp
5. Tras 30 min (1800 ticks), el ensayo termina automáticamente y se guarda en la tabla de resultados
6. Repite con réplicas y tratamientos diferentes
7. **Registra tus datos reales** en el formulario "Datos del lab" del panel derecho
8. La gráfica de respuesta funcional se actualiza en tiempo real con simulación + datos reales
9. Exporta todo a CSV con los botones de descarga

## Estructura

```
fishlab/
├── index.html      ← aplicación completa (HTML + CSS + JS inline)
├── README.md
└── LICENSE
```

Sin `package.json`, sin `node_modules`, sin pipeline de build. Todo el código vive en un solo archivo HTML de ~96 KB.

## Tecnología

- **Vanilla JavaScript** (sin frameworks)
- **Canvas 2D** para animación y gráficas
- **CSS Grid** para layout responsive
- **Nelder–Mead** simplex method para optimización no-lineal
- **DPI-aware rendering** para pantallas retina

## Citación académica

Si usas FishLab en un trabajo académico:

```bibtex
@software{fishlab2026,
  title  = {FishLab: Simulador agent-based de dinámica poblacional y respuesta funcional},
  year   = {2026},
  url    = {https://github.com/USUARIO/fishlab},
  note   = {Herramienta didáctica para Ecología General, UJTL}
}
```

## Referencias bibliográficas

- Holling, C. S. (1959). *The components of predation as revealed by a study of small mammal predation of the European pine sawfly*. The Canadian Entomologist, 91(5), 293–320.
- Lotka, A. J. (1925). *Elements of Physical Biology*. Williams & Wilkins.
- Volterra, V. (1926). *Variazioni e fluttuazioni del numero d'individui in specie animali conviventi*. Memoria della R. Accademia dei Lincei.
- Soumare et al. (2004). Predation rate calculation methods (citado en guía UJTL).
- Pinilla, G. (2015). *Prácticas de Limnología*. Universidad Nacional de Colombia.

## Licencia

MIT — ver [LICENSE](./LICENSE).

---
