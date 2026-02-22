```markdown
# Playground Media Player

## Overview

Playground is an offline-first mobile media player built with React Native and Expo. It supports video and audio playback from device storage, with features like persistent thumbnails, mini-players, gestures, PiP, background play, and more. No cloud or internet required—everything runs locally.

Key goals: Seamless UX, persistent sessions, auto media updates, and pro-level controls inspired by Spotify and VLC.

## Features

### Core
- Bottom tabs: Video List, Audio List, Settings
- Offline media scanning from device (videos + audio)
- Persistent thumbnails (generated once, saved locally)
- Incremental auto-updates for new files (no manual refresh)

### Lists
- Compact vs Big-card layout toggle (persistent)
- Contextual search (collapses to exact scroll)
- Sort: Name/Size/Date (asc/desc, per-tab persistent)
- Recently Played horizontal row (resume from last spot)

### Playback
- Independent sessions (position, sort, repeat, speed)
- Queue/next/prev syncs with list sort
- Mini-player (separate for audio/video-as-audio, docked bottom above tabs)
- Video-to-audio mode (collapse to mini, seamless expand)

### Video Player
- Auto-rotate (landscape for wide, portrait for shorts)
- Gestures: seek (drag left/right), brightness/volume (swipe left/right), long-press speed (2×–4× overlay)
- Lock mode (hide controls, tap reveal)
- PiP support
- Timeline slider + thumbnails row

### Audio Player
- Spotify-style: big artwork + blurred background
- Gapless playback
- Sleep timer

### System Integration
- Background play + notifications/lock screen controls
- Home screen widgets + deep linking
- Keep screen awake during play

## Installation

1. Clone the repo:
   ```
   git clone <your-repo-url>
   cd playground
   ```

2. Install dependencies:
   ```
   npx expo install expo-av expo-video expo-media-library expo-image expo-file-system expo-keep-awake expo-brightness expo-linear-gradient expo-background-fetch @expo/vector-icons react-native-gesture-handler react-native-reanimated react-native-safe-area-context react-native-screens @react-navigation/native @react-navigation/bottom-tabs @react-navigation/native-stack @gorhom/bottom-sheet zustand @react-native-async-storage/async-storage react-native-track-player @react-native-community/slider expo-video-thumbnails
   ```

3. For custom dev client (required for track-player, etc.):
   ```
   npx expo prebuild --clean
   ```

4. Update `tsconfig.json` with path alias:
   ```json
   {
     "extends": "expo/tsconfig.base",
     "compilerOptions": {
       "strict": true,
       "baseUrl": ".",
       "paths": {
         "@/*": ["src/*"]
       }
     }
   }
   ```

## Running the App

- Start dev server:
  ```
  npx expo start
  ```

- Android:
  ```
  npx expo run:android
  ```

- iOS:
  ```
  npx expo run:ios
  ```

- For release build:
  ```
  npx expo run:android --variant release
  ```

Grant media permissions on first launch for scanning.

## Folder Structure

```
playground/
├── app/                # Expo Router routes
│   ├── _layout.tsx     # Root tabs
│   ├── video/          # Video List
│   ├── audio/          # Audio List
│   ├── settings/       # Settings
│   ├── player/         # Full players (deep-linked)
│   └── +not-found.tsx
├── src/                # All logic
│   ├── components/     # UI pieces (common, player, list)
│   ├── hooks/          # Custom hooks (media, gestures, etc.)
│   ├── lib/            # Utils (storage, formatTime, etc.)
│   ├── services/       # Business logic (scanner, queue)
│   ├── types/          # TS types
│   ├── store/          # Zustand stores
│   └── assets/         # Fonts/images
├── app.json            # Expo config
├── package.json
├── tsconfig.json
└── README.md
```

## Development Notes

- State: Zustand for global (playerStore, uiStore, mediaStore)
- Icons: @expo/vector-icons (Ionicons/MaterialIcons)
- Bottom sheets: @gorhom/bottom-sheet
- Testing: Real devices first (PiP, background, permissions)
- TODO: Add tests (`__tests__/`), EAS build config

## Troubleshooting / Known Fixes

Here are fixes for common setup issues encountered during development:

### react-native-track-player Issues (Kotlin Null Safety in Expo SDK 54+)
- Downgrade to compatible version:
  ```
  npm install react-native-track-player@4.1.1
  ```
- Install patch-package:
  ```
  npm install patch-package --save-dev
  ```
- Add postinstall script to package.json:
  ```json
  "scripts": {
    "postinstall": "patch-package",
    ...
  }
  ```
- Patch MusicModule.kt (add `?: Bundle()` fallback):
  - Line 548: `callback.resolve(Arguments.fromBundle(musicService.tracks[index].originalItem ?: Bundle()))`
  - Lines 587-588: `else Arguments.fromBundle(musicService.tracks[musicService.getCurrentTrackIndex()].originalItem ?: Bundle())`
- Patch MusicService.kt (remove `?` from Intent):
  - Line 764: `override fun onBind(intent: Intent): IBinder {`
- Create and apply patch:
  ```
  npx patch-package react-native-track-player
  ```
- Run `npm install` to reapply.

### react-native-video-thumbnails Deprecated (Maven Plugin Error)
- Uninstall old package:
  ```
  npm uninstall react-native-video-thumbnails
  ```
- Install Expo-compatible alternative:
  ```
  npx expo install expo-video-thumbnails
  ```

### Bundling Errors (Missing Peer Dependencies)
- Install missing linking for expo-router:
  ```
  npx expo install expo-linking
  ```

These patches and swaps ensure compatibility with modern Expo/Gradle/Kotlin versions.

## Contributing

Fork, branch, PR. Issues welcome for bugs or features.

Built by Moyo.
```
