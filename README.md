


# Documentation API - NBA Team Optimizer

## Vue d'ensemble

**Version:** 1.2.1  
**Documentation interactive:** `/docs` (Swagger UI) ou `/redoc` (ReDoc)

---

## Endpoints disponibles

### 1. Health Check
**GET** `/health`

Vérifie le statut de santé du service.

**Réponse:**
```json
{
  "status": "healthy",
  "service": "nba-optimizer"
}
```

---

### 2. Optimisation standard ou MVP
**POST** `/best-comp`

Trouve la meilleure composition d'équipe NBA en maximisant le score total.

#### Modes de fonctionnement

##### Mode Standard (`is_mvp: false`)
- Contraintes classiques : budget, taille d'équipe, stars minimum
- Tous les joueurs consomment leur coût complet

##### Mode MVP (`is_mvp: true`)
- Le joueur avec le coût le plus élevé est **gratuit**
- Budget ajusté automatiquement

#### Requête

```json
{
  "players": [
    {
      "name": "LeBron_James_98329",
      "cost": 45,
      "score": 92.5,
      "is_star": true
    },
    {
      "name": "Stephen_Curry_982093",
      "cost": 43,
      "score": 89.3,
      "is_star": true
    }
  ],
  "cost": 200,
  "team_size": 5,
  "minimum_stars": 2,
  "forced_players": ["LeBron_James_0980293"],
  "is_mvp": false
}
```

**Paramètres:**

| Paramètre | Type | Requis | Description |
|-----------|------|--------|-------------|
| `players` | Array | ✅ | Liste des joueurs disponibles |
| `cost` | Integer | ✅ | Budget total disponible |
| `team_size` | Integer | ✅ | Nombre de joueurs dans l'équipe |
| `minimum_stars` | Integer | ✅ | Nombre minimum de stars requises |
| `forced_players` | Array[String] | ❌ | Noms des joueurs à inclure obligatoirement |
| `is_mvp` | Boolean | ❌ | Active le mode MVP (défaut: `false`) |

#### Réponse - Mode Standard

```json
{
    "players": [
        "darius_garland_20000126",
        "miles_mcbride_20000908",
        "chet_holmgren_20020501",
        "jaylin_williams_20020629",
        "zach_lavine_19950310"
    ],
    "total_cost": 119,
    "total_score": 168.78,
    "star_count": 4,
    "details": [
        {
            "name": "darius_garland_20000126",
            "cost": 23,
            "score": 33.8638,
            "is_star": true
        },
        {
            "name": "miles_mcbride_20000908",
            "cost": 22,
            "score": 31.2915,
            "is_star": true
        },
        {
            "name": "chet_holmgren_20020501",
            "cost": 33,
            "score": 42.5127,
            "is_star": false
        },
        {
            "name": "jaylin_williams_20020629",
            "cost": 17,
            "score": 27.2472,
            "is_star": true
        },
        {
            "name": "zach_lavine_19950310",
            "cost": 24,
            "score": 33.8658,
            "is_star": true
        }
    ],
    "status": "Optimal"
}
```

#### Réponse - Mode MVP

```json
{
    "players": [
        "josh_giddey_20021010",
        "alperen_sengun_20020725",
        "nikola_jokic_19950219",
        "pascal_siakam_19940402",
        "jaylin_williams_20020629"
    ],
    "free_player": "nikola_jokic_19950219",
    "total_cost": 209,
    "paid_cost": 146,
    "total_score": 227.33,
    "star_count": 3,
    "details": [
        {
            "name": "josh_giddey_20021010",
            "cost": 46,
            "score": 39.9116,
            "is_star": false
        },
        {
            "name": "alperen_sengun_20020725",
            "cost": 43,
            "score": 50.4978,
            "is_star": true
        },
        {
            "name": "nikola_jokic_19950219",
            "cost": 63,
            "score": 63.1874,
            "is_star": false
        },
        {
            "name": "pascal_siakam_19940402",
            "cost": 40,
            "score": 46.4837,
            "is_star": true
        },
        {
            "name": "jaylin_williams_20020629",
            "cost": 17,
            "score": 27.2472,
            "is_star": true
        }
    ],
    "status": "Optimal"
}
```

**Champs de réponse:**

| Champ | Type | Description |
|-------|------|-------------|
| `players` | Array[String] | Noms des joueurs sélectionnés |
| `free_player` | String | Joueur gratuit (mode MVP uniquement) |
| `total_cost` | Integer | Coût total de l'équipe |
| `paid_cost` | Integer | Coût réellement payé (mode MVP uniquement) |
| `total_score` | Float | Score total de l'équipe |
| `star_count` | Integer | Nombre de stars dans l'équipe |
| `details` | Array | Détails complets de chaque joueur |
| `status` | String | Statut de l'optimisation (`Optimal`, etc.) |

---

### 3. Top N compositions
**POST** `/best-comp/top-n?n=8`

Trouve les N meilleures compositions d'équipe par ordre décroissant de score.

#### Requête

Même structure que `/best-comp` avec un paramètre query supplémentaire :

**Exemple:**
```
POST /best-comp/top-n
```

#### Réponse

```json
{
    "results": [
        {
            "players": [
                "giannis_antetokounmpo_19941206",
                "gary_trent_jr_19990118",
                "miles_mcbride_20000908",
                "deanthony_melton_19980528",
                "shai_gilgeous_alexander_19980712"
            ],
            "total_cost": 139,
            "total_score": 174.09,
            "star_count": 4,
            "details": [
                {
                    "name": "giannis_antetokounmpo_19941206",
                    "cost": 50,
                    "score": 58.7477,
                    "is_star": false
                },
                {
                    "name": "gary_trent_jr_19990118",
                    "cost": 14,
                    "score": 19.9056,
                    "is_star": true
                },
                {
                    "name": "miles_mcbride_20000908",
                    "cost": 22,
                    "score": 31.2915,
                    "is_star": true
                },
                {
                    "name": "deanthony_melton_19980528",
                    "cost": 0,
                    "score": 7.9408,
                    "is_star": true
                },
                {
                    "name": "shai_gilgeous_alexander_19980712",
                    "cost": 53,
                    "score": 56.2092,
                    "is_star": true
                }
            ],
            "status": "Optimal"
        },
        {
            "players": [
                "giannis_antetokounmpo_19941206",
                "josh_minott_20021125",
                "miles_mcbride_20000908",
                "deanthony_melton_19980528",
                "shai_gilgeous_alexander_19980712"
            ],
            "total_cost": 138,
            "total_score": 173.3,
            "star_count": 4,
            "details": [
                {
                    "name": "giannis_antetokounmpo_19941206",
                    "cost": 50,
                    "score": 58.7477,
                    "is_star": false
                },
                {
                    "name": "josh_minott_20021125",
                    "cost": 13,
                    "score": 19.1062,
                    "is_star": true
                },
                {
                    "name": "miles_mcbride_20000908",
                    "cost": 22,
                    "score": 31.2915,
                    "is_star": true
                },
                {
                    "name": "deanthony_melton_19980528",
                    "cost": 0,
                    "score": 7.9408,
                    "is_star": true
                },
                {
                    "name": "shai_gilgeous_alexander_19980712",
                    "cost": 53,
                    "score": 56.2092,
                    "is_star": true
                }
            ],
            "status": "Optimal"
        }
  ],
  "total_found": 5,
  "requested": 5
}
```

**Champs de réponse:**

| Champ | Type | Description |
|-------|------|-------------|
| `results` | Array | Liste des compositions trouvées, triées par score décroissant |
| `total_found` | Integer | Nombre total de compositions trouvées |
| `requested` | Integer | Nombre de compositions demandées |

---

## Gestion des erreurs

Toutes les erreurs retournent un code HTTP **400** avec un corps JSON structuré.

### Structure d'erreur

```json
{
  "detail": {
    "error": "error_code",
    "message": "Description lisible de l'erreur",
    "context": {
      "additional_info": "valeur"
    }
  }
}
```

### Codes d'erreur

#### `missing_parameters`

**Cause:** Un ou plusieurs paramètres requis sont manquants.

**Exemple:**
```json
{
  "detail": {
    "error": "missing_parameters",
    "message": "Les paramètres requis sont manquants",
    "context": {
      "Champs manquants": ["players", "cost"]
    }
  }
}
```

**Solution:** Vérifiez que tous les champs requis sont présents dans votre requête.

---

#### `forced_players_not_found`

**Cause:** Un ou plusieurs joueurs spécifiés dans `forced_players` n'existent pas dans la liste `players`.

**Exemple:**
```json
{
  "detail": {
    "error": "forced_players_not_found",
    "message": "Un ou plusieurs joueurs forcés n'ont pas été trouvé dans la base de joueur",
    "context": {
      "forced_players_not_found": ["Michael Jordan"],
      "missing": ["Michael Jordan"],
      "available_player_names_sample": ["LeBron James", "Stephen Curry", "..."]
    }
  }
}
```

**Solution:** Vérifiez l'orthographe exacte des noms de joueurs forcés.

---

#### `too_many_forced_players`

**Cause:** Le nombre de joueurs forcés dépasse la taille d'équipe demandée.

**Exemple:**
```json
{
  "detail": {
    "error": "too_many_forced_players",
    "message": "Trop de joueur sont forcés comparés à la taille de la team demandé",
    "context": {
      "forced_count": 7,
      "team_size": 5,
      "forced_players": ["Player1", "Player2", "..."]
    }
  }
}
```

**Solution:** Réduisez le nombre de joueurs forcés ou augmentez `team_size`.

---

#### `insufficient_stars_possible`

**Cause:** Impossible d'atteindre le nombre minimum de stars avec les places restantes.

**Exemple:**
```json
{
  "detail": {
    "error": "insufficient_stars_possible",
    "message": "Impossible de déterminer le nombre d'étoiles requis avec les emplacements restants",
    "context": {
      "minimum_stars": 4,
      "forced_stars": 1,
      "remaining_stars_needed": 3,
      "remaining_slots": 2
    }
  }
}
```

**Solution:** Réduisez `minimum_stars`, augmentez `team_size`, ou modifiez les joueurs forcés.

---

#### `no_optimal_solution`

**Cause:** Aucune solution optimale n'a été trouvée avec les contraintes données.

**Exemple:**
```json
{
  "detail": {
    "error": "no_optimal_solution",
    "message": "Aucune solution optimale trouvée pour les contraintes données",
    "context": {
      "lp_status": "Infeasible",
      "remaining_budget": 50,
      "remaining_slots": 3,
      "forced_players": ["Player1", "Player2"],
      "available_count": 100
    }
  }
}
```

**Causes possibles:**
- Budget insuffisant
- Contraintes incompatibles (ex: trop de stars demandées)
- Joueurs forcés consommant trop de budget

**Solution:** Assouplissez les contraintes ou augmentez le budget.

---

#### `invalid_n`

**Cause:** Le paramètre `n` pour `/best-comp/top-n` est hors limites.

**Exemple:**
```json
{
  "detail": {
    "error": "invalid_n",
    "message": "Le paramètre `n` doit être un entier compris entre 1 et 100",
    "context": {
      "provided_n": 150
    }
  }
}
```

**Solution:** Utilisez une valeur entre 1 et 100.

---

#### `no_compositions_found`

**Cause:** Aucune composition valide trouvée pour le top-n.

**Exemple:**
```json
{
  "detail": {
    "error": "no_compositions_found",
    "message": "Aucune composition valide trouvée avec les contraintes fournies",
    "context": {
      "requested_n": 10,
      "remaining_budget": 100,
      "remaining_slots": 3,
      "remaining_stars_needed": 2
    }
  }
}
```

**Solution:** Assouplissez les contraintes ou réduisez le nombre de compositions demandées.

---

## Exemples d'utilisation

### Exemple 1 : Composition standard simple

```bash
curl -X POST "https://your-api.com/best-comp" \
  -H "Content-Type: application/json" \
  -d '{
    "players": [
      {"name": "Player A", "cost": 40, "score": 85.5, "is_star": true},
      {"name": "Player B", "cost": 35, "score": 78.2, "is_star": true},
      {"name": "Player C", "cost": 30, "score": 70.0, "is_star": false},
      {"name": "Player D", "cost": 25, "score": 65.3, "is_star": false},
      {"name": "Player E", "cost": 20, "score": 60.0, "is_star": false}
    ],
    "cost": 150,
    "team_size": 5,
    "minimum_stars": 2
  }'
```

### Exemple 2 : Mode MVP avec joueurs forcés

```bash
curl -X POST "https://your-api.com/best-comp" \
  -H "Content-Type: application/json" \
  -d '{
    "players": [
      {"name": "Superstar", "cost": 50, "score": 95.0, "is_star": true},
      {"name": "Player B", "cost": 35, "score": 78.2, "is_star": true},
      {"name": "Player C", "cost": 30, "score": 70.0, "is_star": false},
      {"name": "Player D", "cost": 25, "score": 65.3, "is_star": false},
      {"name": "Player E", "cost": 20, "score": 60.0, "is_star": false}
    ],
    "cost": 150,
    "team_size": 5,
    "minimum_stars": 2,
    "forced_players": ["Superstar"],
    "is_mvp": true
  }'
```

### Exemple 3 : Top 10 compositions

```bash
curl -X POST "https://your-api.com/best-comp/top-n?n=10" \
  -H "Content-Type: application/json" \
  -d '{
    "players": [...],
    "cost": 200,
    "team_size": 5,
    "minimum_stars": 3
  }'
```

---

## Optimisations & Performance

### Réduction du problème
Lorsque des joueurs sont forcés, l'algorithme :
1. Pré-calcule leur coût et score total
2. Ne résout l'optimisation que pour les places restantes
3. Réduit drastiquement le temps de calcul

### Limites de temps
- Endpoint `/best-comp` : timeout de 30 secondes
- Endpoint `/best-comp/top-n` : timeout de 15 secondes par itération

### Recommandations
- Pour de grandes listes de joueurs (>100), limitez le `n` dans `/best-comp/top-n`
- Utilisez des joueurs forcés pour accélérer les recherches ciblées
- Le mode MVP ajoute de la complexité : privilégiez-le uniquement si nécessaire

---

## Notes importantes

1. **Joueurs forcés** : Leur nom doit correspondre **exactement** (sensible à la casse)
2. **Mode MVP** : Le joueur gratuit est automatiquement sélectionné parmi tous les joueurs (forcés inclus)
3. **Top-N** : Les compositions sont garanties différentes (au moins un joueur change)
4. **Score arrondi** : Le score total est arrondi à 2 décimales dans les réponses

---

## Support & Documentation

- **Swagger UI** : `/docs`
- **ReDoc** : `/redoc`
- **OpenAPI Schema** : `/openapi.json`
- **Health Check** : `/health`

Pour toute question ou problème, consultez le contexte détaillé dans les réponses d'erreur.
