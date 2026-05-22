---
title: "Al-Midan"
date: 2025-09-01
description: "Mobile app for coordinating team sports among Moroccan university students."
tags: ["Flutter", "Dart", "Firebase", "Mobile", "Social"]
icon: "📱"
filetype: "dart"
github: "https://github.com/red1intheocean/al-midan"
hw_specs:
  - { key: "Platform",    value: "Android / iOS" }
  - { key: "Framework",   value: "Flutter 3.x" }
  - { key: "Language",    value: "Dart" }
  - { key: "Backend",     value: "Firebase Firestore" }
  - { key: "Auth",        value: "Firebase Auth" }
  - { key: "State Mgmt",  value: "Riverpod" }
  - { key: "Target",      value: "Moroccan Universities" }
---

## Overview

**Al-Midan** (الميدان — "The Field") is a mobile application designed to solve a real
coordination problem: finding teammates for spontaneous sports sessions on university campuses.

The app connects students across INPT, UM5, and other Moroccan institutions, letting them
create or join pickup games for football, basketball, volleyball, and more.

## Key Features

- **Match Board** — Browse open games by sport, location, and time
- **Team Formation** — Create a game, set player limits, and share a join link
- **Campus Map** — Integrated map view of available sports fields
- **Real-Time Updates** — Live player count and game status via Firestore streams
- **Profile System** — Sport preferences, skill level, and match history

## Technical Highlights

The frontend is built entirely in Flutter, using Riverpod for reactive state management.
Firestore provides real-time sync with offline support. Authentication handles both
email/password and Google Sign-In flows.

{{< codeblock lang="dart" filename="match_provider.dart" >}}
final matchStreamProvider = StreamProvider.family<Match, String>((ref, matchId) {
  return FirebaseFirestore.instance
      .collection('matches')
      .doc(matchId)
      .snapshots()
      .map((snap) => Match.fromJson(snap.data()!));
});
{{< /codeblock >}}

## Challenges

Handling real-time concurrent updates (multiple users joining simultaneously) required
careful Firestore transaction design to prevent race conditions on player count fields.
