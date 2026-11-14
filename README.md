# Twins Video Player (version native Windows 32 / 64 bits)

Lecteur vidéo et audio natif, **sans HTML et sans ffmpeg**.

- **Interface** : Win32 pur (C++17), aucune bibliothèque tierce.
- **Codecs** : Media Foundation, c'est-à-dire les codecs déjà présents dans Windows (H.264, AAC, MP3, MKV, MP4…).
- **Sortie audio** : WASAPI.
- **Dolby Digital (AC-3)** : décodeur écrit à la main (`src/ac3.cpp`), utilisé pour les fichiers `.ac3`/`.a52` et pour les pistes AC-3 des MP4/MKV, même si Windows n'a pas de codec Dolby.

## Fonctions

- Ouverture par menu, glisser-déposer ou ligne de commande ; liste de lecture (touche `L`) ; lecture continue.
- **Choix de la piste audio** : menu *Audio ▸ Piste audio* (ou touche `A` pour passer à la suivante). Chaque piste affiche son numéro, sa langue, son nom, son codec et ses canaux. Le changement garde la position.
- **Mode d'écoute surround** (menu *Audio*) : Standard (stéréo sans traitement), Dolby Surround, Dolby Pro Logic, Pro Logic II (Films, Musique, Jeux, Matrix), Pro Logic IIx (Films, Musique, Jeux, 7.1). Ils décodent une bande stéréo Lt/Rt en L, C, R, LFE, Ls, Rs (et Lb, Rb en IIx). **Sortie surround** : automatique (6 ou 8 canaux si l'appareil les accepte, sinon repli) ou surround virtuel pour casque / 2 enceintes.
- **Mode downmix** (AC-3 et pistes multicanales) : stéréo Lo/Ro, Dolby Surround Lt/Rt (idéal avec Pro Logic), 5.1 discret.
- **Mode downmix AC-3** : stéréo Lo/Ro, Dolby Surround Lt/Rt, 5.1 discret (si la sortie audio a au moins 6 canaux).
- **Compression de dynamique** : désactivée, standard (métadonnées Dolby), films, mode nuit. S'applique à toutes les pistes.
- Barre de recherche, volume, plein écran (`F`, `Entrée`, double-clic), muet (`M`), ±5 s (`←` `→`), ±30 s (`Maj` + flèches).

## Compiler (GitHub Actions)

1. Poussez ce dossier dans un dépôt GitHub.
2. Le workflow `.github/workflows/build.yml` compile **x64** et **x86 (32 bits)** sur `windows-latest` ; les archives `TwinsPlayer-x64.zip` et `TwinsPlayer-x86.zip` sont téléchargeables dans l'onglet *Actions* (artefacts).
3. Pour publier une version : `git tag v1.0 && git push --tags` (les zips sont attachés à la *Release*).

Le CRT est lié statiquement : l'exécutable fonctionne sans installer le « Visual C++ Redistributable ».

### Compiler en local (Visual Studio 2019/2022 + CMake)

```
cmake -S . -B build64 -A x64   && cmake --build build64 --config Release
cmake -S . -B build32 -A Win32 && cmake --build build32 --config Release
```

## Test du décodeur AC-3 (tout système)

`ac3test` décode un `.ac3` en PCM float : `ac3test entree.ac3 sortie.f32 [stereo|lt|51]`.
Le décodeur a été comparé à ffmpeg sur des flux encodés par ffmpeg : environ 50 dB de précision (limite du float 32 bits) en mono, stéréo, 5.1, couplage et rematriçage, à 44,1 et 48 kHz. Les blocs courts (transitoires) n'ont pas pu être testés.

## Limites actuelles

- Les codecs viennent de Windows : HEVC/H.265 et AV1 demandent les extensions vidéo du Microsoft Store ; E-AC-3 et DTS ne sont lus que si Windows les gère.
- MKV : pris en charge par Windows 10 (1607) et plus récent. Sans cela, le fichier est refusé.
- Les pistes AC-3 doivent être exposées par Media Foundation (MP4/MKV usuels) ; pas d'extraction maison hors de ce cas.
- Les décodeurs Pro Logic sont des **approximations** à logique de pilotage (le brevet et l'algorithme exacts de Dolby ne sont pas reproduits) ; réglages Films / Musique / Jeux = mes propres paramètres. `surroundtest` vérifie le pilotage sur des signaux synthétiques.
- Pas encore portés depuis la version HTML : effets de brouillage TV, export vidéo.
