<p align="center">
  <img src="https://nostalgiafun.io/logo_red.png" alt="Matcha" width="120" />
</p>
<h1 align="center">
  <a href="https://nostalgiafun.io/">Matcha</a>
</h1>
<p align="center">
  <strong>Veille Extrême Droite France</strong><br/>
</p>
<p align="center">
  <img src="https://img.shields.io/badge/svelte-5-ff3e00?logo=svelte" alt="Svelte 5" />
  <img src="https://img.shields.io/badge/sveltekit-static-ff3e00" alt="SvelteKit" />
  <img src="https://img.shields.io/badge/supabase-realtime-3ecf8e?logo=supabase" alt="Supabase" />
  <img src="https://img.shields.io/badge/maplibre-gl-396CB2?logo=maplibre" alt="MapLibre" />
  <img src="https://img.shields.io/badge/python-3.12-3776ab?logo=python" alt="Python" />
</p>

---

## Présentation

Matcha est un outil OSINT de veille antifasciste qui agrège, géolocalise et cartographie en temps réel les événements liés à l'extrême droite en France. Le projet combine un pipeline d'extraction automatique (web scraping, Telegram, LLM) avec une interface cartographique interactive.

Une seconde couche de données recense les violences policières sur le territoire français depuis 2008, permettant une lecture croisée des dynamiques de violence politique.

---

## API Endpoints

### `GET /api/events`

Retourne tous les événements extrême droite vérifiés, ordonnés par date croissante.

**Réponse :** `200 OK` — JSON array

```json
[
  {
    "id": 19,
    "title": "Colloque de l'Institut Iliade 2026",
    "organisation": "Identitaire",
    "date": "2026-04-11",
    "location_text": "Paris",
    "lat": 48.8566,
    "lng": 2.3522,
    "source_url": "https://t.me/institutiliade",
    "verified": true,
    "notes": null,
    "created_at": "2026-04-07T18:05:39Z"
  }
]
```

**Organisations :** `RN`, `Reconquête`, `Identitaire`, `Néo-fasciste`, `NR`, `Catho tradi`, `Royaliste`, `Autre`

**Cache :** 60 secondes. **Méthodes autorisées :** GET uniquement.

### `GET /api/police-events`

Retourne les signalements de violences policières géolocalisés, ordonnés par date décroissante.

**Réponse :** `200 OK` — JSON array

```json
[
  {
    "id": 4201,
    "vp_id": 9521,
    "title": "Mort d'El Hacen Diarra en garde à vue dans le commissariat du 11e",
    "date": "2026-01-15",
    "lat": 48.8596,
    "lng": 2.3784,
    "severity": 0.91,
    "source_url": "https://violencespolicieres.fr/signalement/9521"
  }
]
```

**Champs :**
- `severity` — score de gravité continu (0.15–1.22), calculé par violencespolicieres.fr
- `vp_id` — identifiant original sur violencespolicieres.fr
- `source_url` — lien vers la fiche détaillée du signalement

**Source :** [violencespolicieres.fr](https://violencespolicieres.fr) (StreetPress) — licence CC-SA-BY. Données mises à jour hebdomadairement.

**Cache :** 300 secondes. **Méthodes autorisées :** GET uniquement.

### `GET /api/flux`

Retourne les 100 derniers articles RSS agrégés, ordonnés par date de publication décroissante.

**Réponse :** `200 OK` — JSON array

```json
[
  {
    "id": 701,
    "title": "01/05 : mobilisation contre le congrès du RN",
    "url": "https://lahorde.info/...",
    "excerpt": "Appel à manifester contre la tenue du congrès...",
    "source_name": "La Horde",
    "category": "veille",
    "published_at": "2026-04-09T06:00:00+00:00"
  }
]
```

**Catégories :** `veille` (6 sources antifascistes), `investigation` (9 sources), `theorie` (8 revues).

**Cache :** 300 secondes. **Méthodes autorisées :** GET uniquement.

### `POST /api/signal`

Soumet un signalement d'événement. L'événement est créé avec `verified=false` et n'apparaît pas sur la carte tant qu'il n'est pas validé par un administrateur.

**Corps (JSON) :**

```json
{
  "title": "Rassemblement Reconquête — Lyon",
  "organisation": "Reconquête",
  "date": "2026-05-15",
  "location_text": "Lyon",
  "source_url": "https://...",
  "notes": "Place Bellecour, 14h",
  "verified": false
}
```

**Organisations valides :** `RN`, `Reconquête`, `Identitaire`, `Néo-fasciste`, `NR`, `Catho tradi`, `Royaliste`, `Autre`

**Réponse :** `201 Created`

**Rate limit :** 2 requêtes par minute par IP. **Méthodes autorisées :** POST uniquement.

### Flux RSS

**`GET /feed.xml`** — Flux RSS 2.0 standard avec les derniers articles agrégés. Mis à jour quotidiennement par le pipeline. Compatible avec tous les lecteurs RSS (Feedly, Inoreader, NetNewsWire, etc.).

---

## Données

### Extrême droite

~2 700 événements géolocalisés (2022–2026), extraits automatiquement depuis 53 canaux Telegram et plusieurs sites de veille antifasciste. Les événements couvrent l'ensemble du spectre : RN, Reconquête, mouvances identitaires, néo-fascistes, nationalistes-révolutionnaires, catholiques traditionalistes et royalistes.

Pipeline d'ingestion quotidien. Signalements participatifs via `/api/signal`.

### Violences policières

~8 100 signalements géolocalisés (2008–2026), issus de [violencespolicieres.fr](https://violencespolicieres.fr) (StreetPress). Chaque signalement inclut un score de gravité et un lien vers la fiche source avec les preuves documentées.

Mise à jour hebdomadaire.

---

## Roadmap

- [x] Carte interactive multi-couches (ED + violences policières)
- [x] Pipeline d'extraction Telegram (53 canaux)
- [x] Sliders temporels par couche
- [x] Popups enrichis avec liens sources
- [x] Déduplication floue par LLM
- [x] Flux RSS agrégé (25 sources)
- [ ] Explorer Bluesky comme source (API AT Protocol)
- [ ] Scraping historique des archives web
- [ ] Vue calendrier
- [ ] Export CSV/JSON
- [ ] Page admin web (Supabase Auth)
- [ ] Interface de contribution communautaire

---

## Licence

Ce projet est un outil de veille antifasciste développé dans un cadre militant et de recherche. L'utilisation des données est soumise au respect du droit applicable.

Les données de violences policières sont issues de [violencespolicieres.fr](https://violencespolicieres.fr) sous licence [CC BY-SA](https://creativecommons.org/licenses/by-sa/4.0/).

---
