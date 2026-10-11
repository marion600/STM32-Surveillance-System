# 🔍 Système de Surveillance Embarqué – STM32L496

> Projet de fin de semestre | ENSIM – Le Mans Université | Systèmes Embarqués Temps Réel

[![STM32](https://img.shields.io/badge/MCU-STM32L496-blue)](https://www.st.com/)
[![HAL](https://img.shields.io/badge/Driver-STM32%20HAL-lightgrey)](https://www.st.com/)
[![Language](https://img.shields.io/badge/Language-C-orange)](https://fr.wikipedia.org/wiki/C_(langage))
[![IDE](https://img.shields.io/badge/IDE-STM32CubeIDE-green)](https://www.st.com/en/development-tools/stm32cubeide.html)

---

## 📋 Description

Système de surveillance autonome embarqué sur **STM32L496** (Nucleo-L496ZG). Le système effectue une **ronde radar** en continu et déclenche une alarme différenciée selon le type de menace détectée :

- 🔴 **Bruit fort** → LED rouge (danger sonore)
- 🔵 **Mouvement détecté** → LED bleue (intrusion)

En mode alarme, le servomoteur effectue un mouvement de panique (secousses rapides) pour signaler visuellement l'alerte.

---

## 🏗️ Architecture du système

```
┌─────────────────────────────────────────────────────┐
│                   STM32L496 (Nucleo)                │
│                                                     │
│  ┌──────────┐    ADC1     ┌─────────────────────┐  │
│  │ Micro    │ ──────────► │  Logique de décision │  │
│  │ (son)    │             │                     │  │
│  └──────────┘             │  volume > 125 ?     │  │
│                           │  présence détectée? │  │
│  ┌──────────┐    GPIO     │                     │  │
│  │ Capteur  │ ──────────► │                     │  │
│  │ PIR      │  (PC3)      └──────────┬──────────┘  │
│  └──────────┘                        │              │
│                               ┌──────┴──────┐       │
│                           ┌───┴──┐      ┌───┴───┐   │
│                           │ TIM2 │      │ LEDs  │   │
│                           │ PWM  │      │ R / B │   │
│                           │Servo │      │       │   │
│                           └──────┘      └───────┘   │
└─────────────────────────────────────────────────────┘
```

---

## ⚙️ Fonctionnement

### Mode Radar (veille)
- Le servomoteur balaie de 0° à 180° en continu (mouvement fluide via PWM TIM2)
- Les deux capteurs sont lus en permanence

### Mode Alarme (menace détectée)
| Condition | LED | Comportement moteur |
|-----------|-----|---------------------|
| `volume > 125` (bruit) | 🔴 Rouge (PB14) | Secousses rapides (5×) |
| `présence == SET` (PIR) | 🔵 Bleue (LD2) | Secousses rapides (5×) |

Après 2 secondes, le système reprend automatiquement la ronde.

---

## 🛠️ Technologies & Périphériques

| Élément | Détail |
|--------|--------|
| **MCU** | STM32L496ZGTxP (ARM Cortex-M4, 80 MHz) |
| **IDE** | STM32CubeIDE |
| **Drivers** | STM32 HAL |
| **Timer** | TIM2 – PWM pour servomoteur |
| **ADC** | ADC1 – lecture capteur sonore (micro) |
| **GPIO** | PC3 – capteur PIR, PB14 / LD2 – LEDs |
| **UART** | LPUART1 (configuré) |
| **USB** | USB Host (configuré) |

---

## 📁 Structure du projet

```
FinalProject/
├── Core/
│   ├── Inc/
│   │   ├── main.h              # Définitions et prototypes
│   │   └── stm32l4xx_hal_conf.h
│   └── Src/
│       ├── main.c              # ⭐ Logique principale du système
│       ├── stm32l4xx_hal_msp.c # Configuration bas niveau HAL
│       └── stm32l4xx_it.c      # Gestionnaires d'interruptions
├── Drivers/                    # HAL STM32L4xx (ST)
└── .mxproject                  # Configuration STM32CubeMX
```

---

## 🚀 Compilation & Flash

1. Ouvrir le projet dans **STM32CubeIDE**
2. `Project > Build Project` (Ctrl+B)
3. Connecter la carte **Nucleo-L496ZG** en USB
4. `Run > Debug` ou `Run As > STM32 Cortex-M C/C++ Application`

---

## 👩‍💻 Auteur

**Nawel Yonkoua Ngnoba**  
Étudiante ingénieure 5ème année – ISEE (Systèmes Temps Réels Embarqués)  
ENSIM – Le Mans Université  
[LinkedIn](https://www.linkedin.com/in/nawel-yonkoua-18418b336/) | [Portfolio](https://marion600.github.io/portfolio)

---

*Projet réalisé dans le cadre du cursus ingénieur FISE – ENSIM 2026*
