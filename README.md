# Programme TV — Rugby & Films

Page web pour Yannik : programme TV du rugby et des films, toutes chaînes confondues, sans passer par des sites bourrés de pubs (Télé-Loisirs et compagnie).

**Page en ligne :** https://jeremdelrieux-afk.github.io/Prog-Rugby-Yann/

## Comment ça marche

La page (`index.html`) est un simple fichier statique hébergé sur GitHub Pages. Elle ne contient aucune donnée elle-même : à l'ouverture, elle appelle deux "Workers" Cloudflare qui vont chercher les informations en temps réel sur d'autres sites, puis les met en forme.

Un navigateur ne peut pas lire directement un autre site depuis une page HTML (blocage de sécurité CORS) — les Workers Cloudflare servent d'intermédiaires qui font cette lecture à la place du navigateur.

```
index.html (GitHub Pages)
     │
     ├──► Worker "rugbytv"  (Cloudflare) ──► AgendaTV-Rugby.com
     │
     └──► Worker "filmstv"  (Cloudflare) ──► TV.fr + tv-programme.com
```

## Les deux Workers Cloudflare

### 1. `rugbytv` — programme rugby
- Scrape plusieurs pages d'AgendaTV-Rugby.com (Top 14, Pro D2, Champions Cup, Challenge Cup, Six Nations, Féminines, NRL, Test Match, page d'accueil...)
- Récupère équipes, horaires, chaînes, logos
- Exclut automatiquement les matchs déjà terminés
- URL : `https://rugbytv.jeremdelrieux.workers.dev/`

### 2. `filmstv` — programme films
- Combine deux sources :
  - **TV.fr** (filtre "Cinéma") : grandes chaînes TNT + Canal+ + généralistes, avec synopsis
  - **tv-programme.com** : chaînes cinéma dédiées IPTV — Ciné+ (OCS, Émotion, Frisson, Classic, Family, Festival), Canal+ Cinéma/Box Office/Grand Écran, Action, TCM Cinéma, Warner TV, Paris Première, Comédie+, Planète+
- URL : `https://filmstv.jeremdelrieux.workers.dev/`

## Modifier le code

### La page (`index.html`)
1. Éditer le fichier localement (ou demander à Claude une nouvelle version)
2. Sur GitHub : **Add file → Upload files**, glisser le fichier — il remplace l'ancien automatiquement
3. **Commit changes**
4. GitHub Pages republie automatiquement en 1-2 minutes

### Un des deux Workers
1. Aller sur https://dash.cloudflare.com/ → **Workers & Pages**
2. Cliquer sur `rugbytv` ou `filmstv` → **Edit code**
3. Sélectionner tout (Ctrl+A), coller le nouveau code
4. **Deploy**

⚠️ Piège classique : après avoir collé du code, le bouton "Deploy" reste parfois grisé tant que l'éditeur ne détecte pas de changement. Cliquer dans le code, taper un espace puis le supprimer (Backspace) force la détection.

## Limites connues

- Les deux Workers ne font que **relayer** ce que les sites sources ont déjà publié. Si une info n'est pas encore chez eux (ex : numéro exact d'un canal Canal+ Live confirmé le jour même), elle n'apparaît pas non plus ici — pas un bug, juste une conséquence du principe.
- Si un site source change la structure de ses pages, le scraping correspondant peut casser silencieusement (0 résultat au lieu d'une erreur visible). Pas d'alerte automatique en place — à vérifier de temps en temps.
- Cloudflare peut mettre en cache une requête vers le site source ; les deux Workers désactivent ce cache explicitement (`cacheTtl: 0`) pour toujours avoir des données fraîches.

## Pour Yannik

- Ouvrir la page depuis un favori navigateur (pas besoin de connaître aucune des adresses techniques ci-dessus)
- Deux onglets en haut : **🏉 Rugby** et **🎬 Films**
- Les données se chargent automatiquement à l'ouverture ; bouton "Actualiser" pour forcer une mise à jour
