# ESP32 Pong — SAE 2.03

Jeu de Pong sur écran OLED avec un ESP32, jouable en solo contre une IA ou en multijoueur local via **ESP-NOW** (deux cartes ESP32 communicant sans routeur Wi-Fi).

**Vidéo de démonstration (EN)** : [https://youtu.be/keIEQiKue9o?si=39X66mhDlDRYl4x2](https://youtu.be/keIEQiKue9o?si=39X66mhDlDRYl4x2)

**Vidéo technique** : disponible sur [Moodle](https://moodle.univ-lyon1.fr/mod/assign/view.php?id=475172)

---

## Fonctionnalités

- **Mode Solo** : raquette adverse contrôlée automatiquement (IA), 3 vies, score en temps réel, bip sonore à chaque rebond.
- **Mode Multijoueur** : deux ESP32 reliés en ESP-NOW. Le maître gère la physique et envoie l'état à l'esclave. 3 vies chacun.
- **Menu principal** navigable au joystick (MULTI / SOLO / PARAMÈTRES).
- **Pause** disponible en mode Solo.
- **Retour au menu** depuis n'importe quel écran via le bouton C.

---

## Matériel nécessaire

| Composant | Qté (Solo) | Qté (Multi) |
|---|---|---|
| ESP32-C2 (ou compatible Feather) | 1 | 2 |
| Écran OLED SH1107 128×64 I²C | 1 | 2 |
| **Grove Shield FeatherWing** (Adafruit #4309) | 1 | 2 |
| **Grove Joystick** (Seeed) | 1 | 2 |
| **Grove Speaker** (Seeed) | 1 | 2 |
| Câbles Grove 4 broches | 2 | 4 |

---

## Câblage

L'ESP32 s'enfiche sur le **Grove Shield FeatherWing**. Les modules Grove se branchent ensuite sur le shield via câbles Grove — aucune soudure nécessaire. L'écran OLED se branche directement sur l'ESP32 (I²C).

| Module | Port du Grove Shield |
|---|---|
| Grove Joystick | Port analogique **A2/A3** |
| Grove Speaker | Port digital **A0** |

Le **bouton C** (retour menu) est un bouton intégré à l'écran OLED.

---

## Librairies à installer

Dans l'IDE Arduino (Gestionnaire de bibliothèques) :

| Librairie | Remarque |
|---|---|
| `Adafruit GFX Library` | ≥ 1.11 |
| `Adafruit SH110X` | ≥ 2.1 |
| `WiFi` | Incluse dans le core ESP32 |
| `esp_now` | Incluse dans le core ESP32 |

**Core Arduino ESP32** : installer via le Gestionnaire de cartes avec l'URL :
`https://raw.githubusercontent.com/espressif/arduino-esp32/gh-pages/package_esp32_index.json` (version ≥ 2.0)

---

## Compilation & flash

1. Ouvrir `master.ino` dans l'IDE Arduino.
2. Vérifier que tous les fichiers (`balle.h`, `balle.cpp`, `barre.h`, `barre.cpp`, `menu.h`, `menu.cpp`) sont dans le **même dossier** que `master.ino`.
3. Sélectionner la carte : **ESP32C2 Dev Module**.
4. Flasher via USB.

### Mode Multijoueur — configuration de l'adresse MAC esclave

Dans `master.ino`, remplacer l'adresse MAC par celle de l'ESP32 esclave :

```cpp
uint8_t adresseEsclave[] = {0xC4, 0x4F, 0x33, 0x64, 0x8D, 0x11};
```

Pour connaître l'adresse MAC de l'esclave, ouvrir le Moniteur Série après le démarrage — elle s'affiche automatiquement au boot.

Flasher ensuite le sketch **esclave** sur le second ESP32.

---

## Utilisation

### Démarrage

Brancher l'ESP32 via USB ou alimentation externe. L'écran OLED affiche le **menu principal**.

### Navigation dans les menus

| Action | Geste |
|---|---|
| Déplacer le curseur | Incliner le joystick ↑ / ↓ |
| Valider | Appuyer sur le bouton joystick (clic) |
| Retour au menu | Bouton C (GPIO 14) |

### Mode Solo

La raquette se déplace avec le joystick. 3 vies affichées à gauche, score en haut à droite. Maintenir le bouton joystick enfoncé ≥ 1 seconde pour mettre en **pause**.

### Mode Multijoueur

1. Sélectionner **MULTI** sur les deux ESP32.
2. Une fois la connexion ESP-NOW établie, "CONNEXION REUSSIE !" s'affiche et la partie démarre.
3. Le joueur maître lance la balle en appuyant sur le bouton joystick.
4. Chaque joueur contrôle sa raquette avec son propre joystick.

---

## Structure du code

```
├── master/
│   ├── master.ino
│   ├── balle.h / balle.cpp
│   ├── barre.h / barre.cpp
│   └── menu.h  / menu.cpp
└── slave/
    ├── slave.ino
    ├── balle.h / balle.cpp
    ├── barre.h / barre.cpp
    └── menu.h  / menu.cpp
```