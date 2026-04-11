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

Matcha est un outil OSINT de veille antifasciste qui agrège, géolocalise et cartographie en temps réel les événements liés à l'extrême droite en France. Le projet combine un pipeline d'extraction automatique (web scraping + LLM) avec une interface cartographique interactive.


---

## API Endpoints


### `GET /api/events`

Retourne tous les événements vérifiés, ordonnés par date croissante.

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

**Cache :** 60 secondes. **Méthodes autorisées :** GET uniquement.

### `GET /api/flux`

Retourne les 100 derniers articles RSS agrégés, ordonnés par date de publication décroissante.

**Réponse :** `200 OK` — JSON array

```json
[
  {
    "id": 701,
    "title": "Des pesticides PFAS pourraient recevoir une approbation illimitée",
    "url": "https://reporterre.net/...",
    "excerpt": "Les pesticides contenant des PFAS...",
    "source_name": "Reporterre",
    "category": "investigation",
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


## Roadmap

- [ ] Explorer Bluesky comme source (API AT Protocol)
- [x] Déduplication floue par LLM
- [ ] Vue calendrier
- [ ] Export CSV/JSON
- [ ] Page admin web (Supabase Auth)
- [ ] Interface de contribution communautaire

---

## Licence

Ce projet est un outil de veille antifasciste développé dans un cadre militant et de recherche. L'utilisation des données est soumise au respect du droit applicable.

---
