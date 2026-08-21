# FareverModKit

**Unofficial Lua mod framework and DirectX 12 overlay for Farever on Windows.**  
**Framework de mods Lua non officiel et overlay DirectX 12 pour Farever sous Windows.**

[English](#english) · [Français](#français)


> [!WARNING]
> FareverModKit is an experimental community project. It is not affiliated with, endorsed by, or supported by Shiro Games or Valve. Only use it with a supported Farever build and keep a backup of any existing `dxgi.dll`.

Current archive SHA-256:

```text
7dc7a3c3edede522e764cadb4fff729b764fe0b3b4e775b3fc33857207c7af8d
```

---

# English

## About FareverModKit

FareverModKit (FMK) is an open-source mod framework for Farever on Windows. It provides a native DirectX 12 overlay, a sandboxed Lua 5.4 runtime, centralized read-only game-memory access and a modular interface for community tools.

FMK is designed for two audiences:

- **players**, who want an installable collection of useful Farever overlays and tools;
- **mod developers**, who want to build isolated Lua modules without handling the DirectX overlay or game-memory offsets themselves.

The project is under active development. The current public build is an experimental pre-release.

## Features

- Native DirectX 12 overlay loaded through a DXGI proxy.
- Movable and resizable module windows with persistent positions.
- Persistent module activation and interface settings.
- Lockable module icons and separate icon/window layout resets.
- `F2` capture mode to hide and restore the entire FMK interface.
- Sandboxed Lua 5.4 runtime with per-module isolation and instruction limits.
- Centralized, rate-limited and read-only game-memory worker.
- Farever build validation before memory data is exposed to modules.
- Local per-account and per-character storage under `%LOCALAPPDATA%`.
- English, French and Spanish translation support for compatible modules.

## Included modules

| Module                                                                                                                                                                             | Author   | Description                                                           | Status     |
| ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | --------------------------------------------------------------------- | ---------- |
| [Collection Atlas](modules/Blaakan/Atlas/README.md)                                                                                                                                | Blaakan  | Collection browser with filters, item information and ownership state | Functional |
| [BossRun](modules/Patobeur/BossRun/README.md)                                                                                                                                      | Patobeur | Language-independent boss fight timer and local statistics            | Functional |
| [Console](modules/Patobeur/Console/README.md)                                                                                                                                      | Patobeur | Runtime and diagnostic information                                    | Functional |
| [Map](modules/Patobeur/Map/README.md)                                                                                                                                              | Patobeur | Interactive map and map controls                                      | Prototype  |
| [Report](modules/Patobeur/Report/README.md)                                                                                                                                        | Patobeur | Local HTML character and account reports                              | Prototype  |
| [Bravo](modules/Patobeur/Bravo/README.md)                                                                                                                                          | Patobeur | Small example module for the Lua runtime                              | Example    |
| [Chat](modules/Blaakan/Chat/README.md), [Loot](modules/Blaakan/Loot/README.md), [Players](modules/Blaakan/Players/README.md), [Progression](modules/Blaakan/Progression/README.md) | Blaakan  | Modules being migrated to the FMK architecture                        | Skeletons  |

Skeleton modules are visible in the module manager but do not yet provide their final features. See each module README for its current state.

## Requirements

- Windows 10 or Windows 11, 64-bit.
- A legitimate Steam installation of Farever.
- A Farever build explicitly supported by the current FMK release.
- [Node.js](https://nodejs.org/) only when generating the missing Atlas resources with `generer-atlas.cmd`.

## Player installation

1. Close Farever completely.
2. Download `FareverModKit-v0.1.1.zip`.
3. Open your Farever installation folder from Steam: **Library → Farever → Properties → Installed Files → Browse**.
4. Back up any existing `dxgi.dll`. Only one DXGI proxy can be active at a time.
5. Extract the complete ZIP archive into the Farever game folder.
6. If you want to use Collection Atlas, install Node.js and run `generer-atlas.cmd` from the game folder to generate the required local resources.
7. Start Farever from the Steam client. Do not launch `Farever.exe` directly.

After extraction, the game folder must contain at least:

```text
Farever/
├── dxgi.dll
├── farevermodkit/
├── generer-atlas.cmd
├── LISEZ-MOI.md
└── SHA256SUMS.txt
```

## Verify the download

Open PowerShell in the download folder and run:

```powershell
(Get-FileHash .\FareverModKit-v0.1.1.zip -Algorithm SHA256).Hash
```

The result must match:

```text
7DC7A3C3EDEDE522E764CADB4FFF729B764FE0B3B4E775B3FC33857207C7AF8D
```

You can also compare it with the published `FareverModKit-v0.1.1.zip.sha256` file.

## Usage

- The FMK icon remains available while the overlay is running.
- Open the FMK window to enable or disable modules.
- Drag module icons and windows to organize the interface.
- Resize compatible module windows from their resize control.
- Lock the icons when the layout is ready.
- Press `F2` to hide or restore all FMK interface elements for screenshots.

Module state, positions and compatible settings are saved automatically.

## Data and configuration

Installed code and static resources remain inside the Farever game folder. User settings and generated data are stored under:

```text
%LOCALAPPDATA%\farevermodkit\
```

This includes UI state, module settings, navigation data, BossRun statistics and Report exports. Account and character data are separated whenever the game exposes the necessary identifiers.

See [Storage and migration](docs/STORAGE.md) for the complete directory layout and migration rules.

## Safety and compatibility

FMK centralizes all Farever memory reads in its native core. Modules receive controlled copies of values, never raw pointers.

The Lua sandbox removes dangerous system libraries and functions, including `os`, `io`, `debug`, `package`, `require`, `dofile`, `loadfile` and `load`. A Lua error disables the affected module without stopping the other modules.

Memory offsets belong to the FMK core and are validated against the Farever build. If the build hash is unknown, memory access is refused instead of being guessed or scanned globally. A Farever update may therefore temporarily disable features until a compatible FMK release is available.

## Uninstallation

1. Close Farever completely.
2. Remove the FMK `dxgi.dll` from the Farever game folder.
3. Remove the `farevermodkit` folder and the FMK helper files if you no longer need them.
4. Restore your previous `dxgi.dll` only if you backed one up during installation.

User data is kept under `%LOCALAPPDATA%\farevermodkit` so an update or reinstall does not erase it. Remove that folder manually only if you also want to delete all FMK settings, reports and module data.

## Creating a Lua module

A module is stored under `modules/<Author>/<Module>/` and contains at least:

```text
MyModule/
├── manifest.json
└── main.lua
```

It may also provide:

```text
icon.png
README.md
assets/
languages/
```

All callbacks are optional:

```lua
function on_init() end
function on_render() end
function on_event(name, data) end
function on_settings() end
function on_shutdown() end
```

Start with the [Lua API documentation](docs/LUA_API.md) and the existing modules in [`modules/`](modules/).

## Building from source

Requirements:

- Windows x64;
- Visual Studio with the MSVC C++ build tools;
- Node.js for Atlas generation;
- a local Farever installation for generating game-derived resources.

```powershell
git clone https://github.com/patobeur/farevermodkit.git
cd farevermodkit
cmd /c third_party\lua\build.cmd
node tools\gen-atlas.mjs --game "D:\SteamLibrary\steamapps\common\Farever"
cmd /c core\build.cmd
cmd /c native\build.cmd
cmd /c native\package-test.cmd
```

The installable test package is generated in `native/test-package/`. Generated `build/` and `native/test-package/` directories are not versioned.

## Project structure

```text
assets/                 shared FMK and Atlas resources
config/                 native and Lua runtime configuration
core/                   Lua host and centralized memory reader
modules/<Author>/<Mod>/ Lua modules and module-specific resources
native/                 DXGI proxy and Direct3D 12 overlay
third_party/lua/        Lua 5.4.8 sources and reproducible build
tools/                  Atlas generation and validation tools
docs/                   API, storage and development documentation
```

## Documentation

- [Lua API](docs/LUA_API.md)
- [Native core](native/README.md)
- [Memory reader](core/src/memory/README.md)
- [Storage and migration](docs/STORAGE.md)
- [Development history](docs/DEVELOPMENT_HISTORY.md)
- [Credits and acknowledgements](CREDITS.md)
- [Third-party notices](THIRD_PARTY_NOTICES.md)
- [Project TODO](TODO.md)

## Support and contributions

Before reporting a problem, check that you are using a supported Farever build and the most recent FMK pre-release.

- [Report a bug or request a feature](https://github.com/patobeur/farevermodkit/issues)
- [Browse the source code](https://github.com/patobeur/farevermodkit)
- [View all releases](https://github.com/patobeur/farevermodkit/releases)

Contributions should preserve read-only game access, module isolation, build validation and clear attribution of third-party work.

---

# Français

## À propos de FareverModKit

FareverModKit (FMK) est un framework open source de mods pour [Farever](https://store.steampowered.com/app/3672400/) sous Windows. Il fournit un overlay natif DirectX 12, un environnement Lua 5.4 isolé, une lecture centralisée et strictement en lecture seule de la mémoire du jeu, ainsi qu’une interface modulaire pour les outils communautaires.

FMK s’adresse à deux publics :

- **les joueurs**, qui souhaitent installer une collection d’overlays et d’outils pratiques pour Farever ;
- **les créateurs de mods**, qui souhaitent développer des modules Lua isolés sans gérer eux-mêmes l’overlay DirectX ni les offsets mémoire du jeu.

Le projet est en développement actif. La version publique actuelle est une préversion expérimentale.

## Fonctionnalités

- Overlay natif DirectX 12 chargé par un proxy DXGI.
- Fenêtres de modules déplaçables et redimensionnables avec positions persistantes.
- Activation des modules et réglages d’interface enregistrés automatiquement.
- Icônes verrouillables et réinitialisation séparée des icônes et des fenêtres.
- Mode capture avec `F2` pour masquer puis restaurer toute l’interface FMK.
- Environnement Lua 5.4 isolé avec budget d’instructions propre à chaque module.
- Lecture mémoire centralisée, limitée en fréquence et strictement en lecture seule.
- Validation du build Farever avant de transmettre les données aux modules.
- Stockage local séparé par compte et par personnage sous `%LOCALAPPDATA%`.
- Prise en charge de l’anglais, du français et de l’espagnol pour les modules compatibles.

## Modules inclus

| Module                                                                                                                                                                             | Auteur   | Description                                                                     | État        |
| ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------------------------------------------------------------------------------- | ----------- |
| [Collection Atlas](modules/Blaakan/Atlas/README.md)                                                                                                                                | Blaakan  | Collection avec filtres, fiches d’objets et état de possession                  | Fonctionnel |
| [BossRun](modules/Patobeur/BossRun/README.md)                                                                                                                                      | Patobeur | Chronomètre de combats de boss indépendant de la langue et statistiques locales | Fonctionnel |
| [Console](modules/Patobeur/Console/README.md)                                                                                                                                      | Patobeur | Informations de fonctionnement et de diagnostic                                 | Fonctionnel |
| [Map](modules/Patobeur/Map/README.md)                                                                                                                                              | Patobeur | Carte interactive et commandes cartographiques                                  | Prototype   |
| [Report](modules/Patobeur/Report/README.md)                                                                                                                                        | Patobeur | Rapports HTML locaux pour les personnages et les comptes                        | Prototype   |
| [Bravo](modules/Patobeur/Bravo/README.md)                                                                                                                                          | Patobeur | Petit module d’exemple pour l’environnement Lua                                 | Exemple     |
| [Chat](modules/Blaakan/Chat/README.md), [Loot](modules/Blaakan/Loot/README.md), [Players](modules/Blaakan/Players/README.md), [Progression](modules/Blaakan/Progression/README.md) | Blaakan  | Modules en cours de migration vers l’architecture FMK                           | Squelettes  |

Les modules squelettes apparaissent dans le gestionnaire, mais ne proposent pas encore leurs fonctions définitives. Consultez le README de chaque module pour connaître son état actuel.

## Prérequis

- Windows 10 ou Windows 11 en 64 bits.
- Une installation Steam légitime de Farever.
- Un build de Farever explicitement pris en charge par la version actuelle de FMK.
- [Node.js](https://nodejs.org/) uniquement pour générer les ressources Atlas manquantes avec `generer-atlas.cmd`.

## Installation pour les joueurs

1. Fermez complètement Farever.
2. Téléchargez FareverModKit-v0.1.1.zip
3. Ouvrez le dossier d’installation de Farever depuis Steam : **Bibliothèque → Farever → Propriétés → Fichiers installés → Parcourir**.
4. Sauvegardez tout ancien fichier `dxgi.dll`. Un seul proxy DXGI peut être actif à la fois.
5. Extrayez la totalité de l’archive ZIP dans le dossier du jeu Farever.
6. Pour utiliser Collection Atlas, installez Node.js puis lancez `generer-atlas.cmd` depuis le dossier du jeu afin de générer les ressources locales nécessaires.
7. Lancez Farever depuis le client Steam. Ne lancez pas directement `Farever.exe`.

Après l’extraction, le dossier du jeu doit contenir au minimum :

```text
Farever/
├── dxgi.dll
├── farevermodkit/
├── generer-atlas.cmd
├── LISEZ-MOI.md
└── SHA256SUMS.txt
```

## Vérifier le téléchargement

Ouvrez PowerShell dans le dossier de téléchargement et exécutez :

```powershell
(Get-FileHash .\FareverModKit-v0.1.1.zip -Algorithm SHA256).Hash
```

Le résultat doit être identique à :

```text
7DC7A3C3EDEDE522E764CADB4FFF729B764FE0B3B4E775B3FC33857207C7AF8D
```

Vous pouvez également le comparer au fichier publié `FareverModKit-v0.1.1.zip.sha256` de la release.

## Utilisation

- L’icône FMK reste disponible tant que l’overlay fonctionne.
- Ouvrez la fenêtre FMK pour activer ou désactiver les modules.
- Déplacez les icônes et les fenêtres pour organiser l’interface.
- Redimensionnez les fenêtres compatibles depuis leur commande de redimensionnement.
- Verrouillez les icônes lorsque la disposition vous convient.
- Appuyez sur `F2` pour masquer ou restaurer tous les éléments FMK pendant une capture d’écran.

L’état des modules, leurs positions et leurs réglages compatibles sont enregistrés automatiquement.

## Données et configuration

Le code installé et les ressources statiques restent dans le dossier du jeu. Les réglages utilisateur et les données générées sont enregistrés sous :

```text
%LOCALAPPDATA%\farevermodkit\
```

Ce dossier contient notamment l’état de l’interface, les réglages des modules, les données de navigation, les statistiques BossRun et les exports Report. Les données sont séparées par compte et par personnage lorsque le jeu fournit les identifiants nécessaires.

Consultez la documentation [Stockage et migration](docs/STORAGE.md) pour obtenir l’arborescence complète et les règles de migration.

## Sécurité et compatibilité

FMK centralise toutes les lectures de la mémoire de Farever dans son cœur natif. Les modules reçoivent uniquement des copies contrôlées des valeurs, jamais des pointeurs bruts.

La sandbox Lua retire les bibliothèques et fonctions système dangereuses, notamment `os`, `io`, `debug`, `package`, `require`, `dofile`, `loadfile` et `load`. Une erreur Lua désactive uniquement le module concerné, sans arrêter les autres modules.

Les offsets mémoire appartiennent au cœur FMK et sont validés pour chaque build de Farever. Si le hash du build est inconnu, les lectures mémoire sont refusées au lieu d’être devinées ou obtenues par un scan global. Une mise à jour de Farever peut donc désactiver temporairement certaines fonctions jusqu’à la publication d’une version compatible de FMK.

## Désinstallation

1. Fermez complètement Farever.
2. Supprimez le fichier `dxgi.dll` appartenant à FMK dans le dossier du jeu.
3. Supprimez le dossier `farevermodkit` et les fichiers d’assistance FMK si vous n’en avez plus besoin.
4. Restaurez votre ancien `dxgi.dll` uniquement si vous en aviez sauvegardé un pendant l’installation.

Les données utilisateur sont conservées sous `%LOCALAPPDATA%\farevermodkit` afin qu’une mise à jour ou une réinstallation ne les efface pas. Supprimez manuellement ce dossier uniquement si vous souhaitez également effacer tous les réglages, rapports et données des modules FMK.

## Créer un module Lua

Un module est placé sous `modules/<Auteur>/<Module>/` et contient au minimum :

```text
MonModule/
├── manifest.json
└── main.lua
```

Il peut également fournir :

```text
icon.png
README.md
assets/
languages/
```

Tous les callbacks sont facultatifs :

```lua
function on_init() end
function on_render() end
function on_event(name, data) end
function on_settings() end
function on_shutdown() end
```

Commencez par consulter la documentation de l’[API Lua](docs/LUA_API.md) et les modules existants dans [`modules/`](modules/).

## Compiler depuis les sources

Prérequis :

- Windows x64 ;
- Visual Studio avec les outils de compilation C++ MSVC ;
- Node.js pour la génération de l’Atlas ;
- une installation locale de Farever pour générer les ressources dérivées du jeu.

```powershell
git clone https://github.com/patobeur/farevermodkit.git
cd farevermodkit
cmd /c third_party\lua\build.cmd
node tools\gen-atlas.mjs --game "D:\SteamLibrary\steamapps\common\Farever"
cmd /c core\build.cmd
cmd /c native\build.cmd
cmd /c native\package-test.cmd
```

Le paquet de test installable est généré dans `native/test-package/`. Les dossiers générés `build/` et `native/test-package/` ne sont pas versionnés.

## Structure du projet

```text
assets/                 ressources partagées de FMK et de l’Atlas
config/                 configuration native et environnement Lua
core/                   hôte Lua et lecteur mémoire centralisé
modules/<Auteur>/<Mod>/ modules Lua et ressources propres aux modules
native/                 proxy DXGI et overlay Direct3D 12
third_party/lua/        sources de Lua 5.4.8 et build reproductible
tools/                  outils de génération et de validation de l’Atlas
docs/                   documentation de l’API, du stockage et du développement
```

## Documentation

- [API Lua](docs/LUA_API.md)
- [Cœur natif](native/README.md)
- [Lecture mémoire](core/src/memory/README.md)
- [Stockage et migration](docs/STORAGE.md)
- [Historique de développement](docs/DEVELOPMENT_HISTORY.md)
- [Crédits et remerciements](CREDITS.md)
- [Mentions relatives aux dépendances](THIRD_PARTY_NOTICES.md)
- [Liste des tâches du projet](TODO.md)

## Assistance et contributions

Avant de signaler un problème, vérifiez que vous utilisez un build de Farever pris en charge et la préversion FMK la plus récente.

- [Signaler un bug ou proposer une fonctionnalité](https://github.com/patobeur/farevermodkit/issues)
- [Consulter le code source](https://github.com/patobeur/farevermodkit)
- [Voir toutes les releases](https://github.com/patobeur/farevermodkit/releases)

Les contributions doivent préserver la lecture seule de la mémoire du jeu, l’isolation des modules, la validation des builds et l’attribution claire des travaux tiers.

---

## Credits, license and disclaimer / Crédits, licence et avertissement

FareverModKit is released under the [MIT License](LICENSE). Lua 5.4.8 retains its own license, reproduced in [`THIRD_PARTY_NOTICES.md`](THIRD_PARTY_NOTICES.md).

FareverModKit est distribué sous [licence MIT](LICENSE). Lua 5.4.8 conserve sa propre licence, reproduite dans [`THIRD_PARTY_NOTICES.md`](THIRD_PARTY_NOTICES.md).

See [CREDITS.md](CREDITS.md) for attribution to Blaakan, ramisotti13-eng, Brudr, Lua and the Farever modding community.

Consultez [CREDITS.md](CREDITS.md) pour les remerciements et attributions à Blaakan, ramisotti13-eng, Brudr, Lua et la communauté de modding Farever.

Farever, its names, data and resources belong to Shiro Games. FareverModKit is an unofficial community project with no affiliation, endorsement or support from Shiro Games or Valve. Game-derived resources required by some modules must be generated locally from a legitimate Farever installation.

Farever, ses noms, ses données et ses ressources appartiennent à Shiro Games. FareverModKit est un projet communautaire non officiel, sans affiliation, approbation ni assistance de Shiro Games ou Valve. Les ressources dérivées du jeu nécessaires à certains modules doivent être générées localement depuis une installation légitime de Farever.
