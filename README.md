# raie\_pastenague — RPG Pêche asiatique‑fantasy (Minecraft 1.20.1)

> **Objectif** : Transformer la pêche vanilla en un sous‑système RPG profond, data‑driven (datapack + resource pack), dans un monde asiatique‑fantasy. Progression par zones, cannes, appâts, raretés et trésors.

**Namespace datapack :** `rp`
**Nom du pack :** `raie_pastenague`
**Cible :** Java 1.20.1 (pack\_format \~ 15 pour data & RP)
**Philosophie :** 100 % data d’abord (loot tables, predicates, tags biomes, advancements, recipes, model overrides). Module Forge minimal seulement si bloquant en vanilla.

---

## TL;DR

* Le datapack **remplace les loot tables** de pêche vanilla `minecraft:gameplay/fishing/*` et redirige vers des tables modulaires sous `rp:gameplay/fishing/*`.
* Les poissons sont des items vanilla **taggés via NBT** (`CustomModelData`, nom, lore, tag `rp:{...}`) pour être **re‑skinnés** côté resource pack.
* **Progression** par *Tier* d’équipement (cannes), appâts et zones. Premier jet : **conditions neutres** (pas de biome/météo/heure) pour faciliter l’intégration.

---

## Installation

1. Décompressez `raie_pastenague/` dans votre dossier `world/datapacks/`.
2. Placez le resource pack compagnon dans `resourcepacks/` puis activez‑le en jeu.
3. `/reload` puis `/datapack list` pour vérifier l’activation.

---

## Arborescence (premier jet)

```text
raie_pastenague/
├─ pack.mcmeta
├─ data/
│  ├─ minecraft/
│  │  └─ loot_tables/
│  │     └─ gameplay/
│  │        └─ fishing/
│  │           ├─ fish.json          # override vanilla → redirige vers rp:gameplay/fishing/fish_t1
│  │           ├─ junk.json          # override vanilla → redirige vers rp:gameplay/fishing/junk_common
│  │           └─ treasure.json      # override vanilla → redirige vers rp:gameplay/fishing/treasure_common
│  └─ rp/
│     ├─ loot_tables/
│     │  └─ gameplay/
│     │     └─ fishing/
│     │        ├─ fish_t1.json
│     │        ├─ junk_common.json
│     │        └─ treasure_common.json
│     ├─ tags/
│     │  └─ items/
│     │     └─ baits.json            # liste d’appâts (placeholder)
│     ├─ recipes/
│     │  └─ bait_simple.json         # recipe de placeholder (sans NBT)
│     └─ advancements/
│        └─ story/
│           └─ catch_first_fish.json
└─ resourcepack_raie_pastenague/     # recommandé: pack séparé (voir plus bas)
   └─ assets/
      └─ rp/
         ├─ models/
         │  └─ item/
         │     ├─ cod.json           # overrides CustomModelData
         │     └─ fish/jade_carp.json
         ├─ textures/
         │  └─ item/fish/jade_carp.png
         └─ lang/
            └─ fr_fr.json
```

---

## `pack.mcmeta` (datapack)

```jsonc
{
  "pack": {
    "pack_format": 15, // 1.20–1.20.1
    "description": "raie_pastenague — RPG Pêche asiatique‑fantasy (data-driven)"
  }
}
```

## Overrides minimalistes des loot tables vanilla

> On garde le routage vanilla (les poids fish/junk/treasure sont gérés par `minecraft:gameplay/fishing.json`). On **remplace seulement** le contenu des tables `fish`, `junk` et `treasure` et on redirige vers nos tables modulaires.

**`data/minecraft/loot_tables/gameplay/fishing/fish.json`**

```json
{
  "pools": [
    {
      "rolls": 1,
      "entries": [
        { "type": "minecraft:loot_table", "name": "rp:gameplay/fishing/fish_t1" }
      ]
    }
  ]
}
```

**`data/minecraft/loot_tables/gameplay/fishing/junk.json`**

```json
{
  "pools": [
    {
      "rolls": 1,
      "entries": [
        { "type": "minecraft:loot_table", "name": "rp:gameplay/fishing/junk_common" }
      ]
    }
  ]
}
```

**`data/minecraft/loot_tables/gameplay/fishing/treasure.json`**

```json
{
  "pools": [
    {
      "rolls": 1,
      "entries": [
        { "type": "minecraft:loot_table", "name": "rp:gameplay/fishing/treasure_common" }
      ]
    }
  ]
}
```

---

## Tables `rp` (premier jet neutre)

**`data/rp/loot_tables/gameplay/fishing/fish_t1.json`**

```json
{
  "pools": [
    {
      "rolls": 1,
      "entries": [
        {
          "type": "minecraft:item",
          "name": "minecraft:cod",
          "functions": [
            { "function": "minecraft:set_nbt", "tag": "{CustomModelData:10001, rp:{fish_id:\"jade_carp\", tier:1, rarity:\"common\"}}" },
            { "function": "minecraft:set_name", "name": {"text":"Carpe de Jade","color":"green","italic":false} },
            { "function": "minecraft:set_lore", "lore": [
              {"text":"Paisible gardienne des rizières émeraude.","italic":false,"color":"gray"}
            ], "replace": true }
          ],
          "weight": 6
        },
        {
          "type": "minecraft:item",
          "name": "minecraft:salmon",
          "functions": [
            { "function": "minecraft:set_nbt", "tag": "{CustomModelData:10002, rp:{fish_id:\"koi_ecarlate\", tier:1, rarity:\"uncommon\"}}" },
            { "function": "minecraft:set_name", "name": {"text":"Koi Écarlate","color":"red","italic":false} },
            { "function": "minecraft:set_lore", "lore": [
              {"text":"Ses écailles racontent des légendes.","italic":false,"color":"gray"}
            ], "replace": true }
          ],
          "weight": 3
        },
        {
          "type": "minecraft:item",
          "name": "minecraft:tropical_fish",
          "functions": [
            { "function": "minecraft:set_nbt", "tag": "{CustomModelData:10003, rp:{fish_id:\"rasbora_cobalt\", tier:1, rarity:\"rare\"}}" },
            { "function": "minecraft:set_name", "name": {"text":"Rasbora Cobalt","color":"aqua","italic":false} },
            { "function": "minecraft:set_lore", "lore": [
              {"text":"Fugace comme la brume des bambouseraies.","italic":false,"color":"gray"}
            ], "replace": true }
          ],
          "weight": 1
        }
      ]
    }
  ]
}
```

**`data/rp/loot_tables/gameplay/fishing/junk_common.json`**

```json
{
  "pools": [
    {
      "rolls": 1,
      "entries": [
        { "type": "minecraft:item", "name": "minecraft:stick", "weight": 4 },
        { "type": "minecraft:item", "name": "minecraft:string", "weight": 3 },
        { "type": "minecraft:item", "name": "minecraft:bowl", "weight": 2 },
        { "type": "minecraft:item", "name": "minecraft:bone", "weight": 1 }
      ]
    }
  ]
}
```

**`data/rp/loot_tables/gameplay/fishing/treasure_common.json`**

```json
{
  "pools": [
    {
      "rolls": 1,
      "entries": [
        { "type": "minecraft:item", "name": "minecraft:prismarine_crystals", "weight": 4 },
        { "type": "minecraft:item", "name": "minecraft:name_tag", "weight": 2 },
        { "type": "minecraft:item", "name": "minecraft:emerald", "weight": 1 }
      ]
    }
  ]
}
```

---

## Tags d’items (appâts) — placeholder

**`data/rp/tags/items/baits.json`**

```json
{
  "values": [
    "minecraft:wheat_seeds",
    "minecraft:glow_berries"
  ]
}
```

> Note : en vanilla 1.20.1, **les recipes ne posent pas de NBT**. Les appâts "réels" (avec NBT/CMData) seront plutôt générés par loot tables, commandes, marchands, ou via un module Forge optionnel. Ce tag sert à référencer les items considérés comme appâts simples.

---

## Advancement d’exemple

**`data/rp/advancements/story/catch_first_fish.json`**

```json
{
  "criteria": {
    "has_fish": {
      "trigger": "minecraft:inventory_changed",
      "conditions": {
        "items": [
          { "items": ["minecraft:cod", "minecraft:salmon", "minecraft:tropical_fish"] }
        ]
      }
    }
  },
  "rewards": { "experience": 50 },
  "display": {
    "title": {"text": "Première prise !", "color": "gold"},
    "description": {"text": "Le murmure des carpes t’accueille."},
    "icon": {"item": "minecraft:fishing_rod"},
    "frame": "task",
    "show_toast": true,
    "announce_to_chat": false
  }
}
```

---

## Recipe placeholder (optionnelle)

> Simple recette utilitaire pour craft un **appât simple** sans NBT. Elle sert de point d’ancrage à la progression tout en restant neutre.

**`data/rp/recipes/bait_simple.json`**

```json
{
  "type": "minecraft:crafting_shapeless",
  "ingredients": [
    {"item": "minecraft:wheat_seeds"},
    {"item": "minecraft:string"}
  ],
  "result": {"item": "minecraft:paper", "count": 1}
}
```

---

## Resource Pack — modèles & textes

**Override du modèle d’item (`assets/rp/models/item/cod.json`)**

```json
{
  "parent": "minecraft:item/generated",
  "textures": { "layer0": "minecraft:item/cod" },
  "overrides": [
    { "predicate": {"custom_model_data": 10001}, "model": "rp:item/fish/jade_carp" }
  ]
}
```

**Modèle spécifique (`assets/rp/models/item/fish/jade_carp.json`)**

```json
{
  "parent": "minecraft:item/generated",
  "textures": { "layer0": "rp:item/fish/jade_carp" }
}
```

**Lang (`assets/rp/lang/fr_fr.json`)**

```json
{
  "advancements.rp.story.catch_first_fish.title": "Première prise !",
  "advancements.rp.story.catch_first_fish.description": "Le murmure des carpes t’accueille."
}
```

---

## Canne & progression (vision)

* **T1 : Canne en bambou** — accessible dès le départ, `CustomModelData:20001`, poissons communs et premières raretés.
* **T2 : Canne des jardins flottants** — meilleure chance de rare/tesor, `CustomModelData:20002`.
* **T3 : Canne des arches célestes** — accès aux zones lointaines/hostiles, `CustomModelData:20003`.

> Implémentation data‑driven typique : les *tiers* impactent les chances via des tables séparées (`fish_t2.json`, `fish_t3.json`) et/ou des conditions (ex : predicates) quand on passera aux versions avancées.

---

## Tests rapides (en jeu)

```mcfunction
/reload
/loot give @s loot rp:gameplay/fishing/fish_t1
/loot give @s loot rp:gameplay/fishing/junk_common
/loot give @s loot rp:gameplay/fishing/treasure_common
/advancement grant @s only rp:story/catch_first_fish
/weather rain
/time set night
```

---

## Conventions & style

* **Namespace** : `rp` ; **snake\_case** partout.
* **NBT custom** : paqueté sous `rp:{...}` pour éviter les collisions.
* **CustomModelData** :

  * 10 000–19 999 : poissons
  * 20 000–20 099 : cannes
  * 21 000–21 099 : appâts

---

## Roadmap (prochaine itération)

* Ajouter **biomes/météo/heure** via conditions (progression par zones).
* Introduire des **appâts NBT** générés par loot tables/PNJ.
* Déclencher des **rituels météo** (sons/particules, trésors uniques).
* Équilibrer poids & raretés, ajouter **tableaux de zones** et **PNJ pêcheurs**.
* Si nécessaire : **module Forge optionnel** pour gating avancé (lecture NBT de la canne depuis l’hameçon), sinon rester full‑data.

---

## Licence

MIT (par défaut). Adapter selon besoin.

---

### Notes pour GitHub Copilot

* Les fichiers d’exemple servent de **patrons**. Étendre les tables en dupliquant `fish_t1.json` vers `fish_t2.json`, etc.
* Rechercher le commentaire `//` dans les JSON pour comprendre l’intention de structure (les commentaires dans JSON sont pour la lecture humaine ici).
* Respecter la **séparation** : overrides sous `data/minecraft/...` → contenu réel sous `data/rp/...`.
* Proposer des **overrides de modèles** supplémentaires (CMData) et des **textures** correspondantes.
