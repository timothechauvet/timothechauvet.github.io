---
title: "Transférer ses films de TV Time vers Letterboxd 🎬"
date: 2025-05-19
hero: banner.webp
description: Exporter ses données TV Time pour les réimporter sur Letterboxd
theme: Toha
---


Je suis le seul à avoir un entourage qui me **force** à m'inscrire sur Letterboxd ? 

Le truc c'est que je viens de TV Time. L'appli est notoire pour [empêcher d'accéder à ses données](https://www.reddit.com/r/TVTime/comments/mxx3km/do_you_know_a_way_to_exportimport_watchlist_and/). Du coup je suis un peu obligé de passer par un autre moyen, car j'ai plusieurs centaines de films à ajouter.

{{< vs 4 >}}

<p align="center">
  {{< img src="/posts/tutoriels/tvtime-to-letterboxd/banner.webp" width="700" align="center" >}}
</p>

{{< vs 4 >}}

# Une appli monopage codée avec Flutter

Heureusement pour nous, il est possible d'accéder à [TV Time via le web](https://app.tvtime.com/).

1. Se connecter
2. Aller sur le profil
3. Voir la liste des films vus.

Je me rends vite compte que le DOM HTML n'est pas accessible. C'est en raison du *shadow DOM* que permet Flutter (si j'ai bien compris). 

On va donc devoir manœuvrer autrement.

{{< vs 4 >}}

## L'onglet Network

Dans la console développeur (`Ctrl/Cmd + Shift + C`), aller dans l'onglet `Network`. 

En faisant un [hard refresh](https://sensorstechforum.com/fr/hard-refresh-browsers-force-clear-cache/) un json assez gros est récupéré. 

L'URL du json correspond à `https://app.tvtime.com/sidecar?o=https%3A%2F%2Fmsapi.tvtime.com%2Fprod%2Fv1%2Ftracking%2Fcgw%2Ffollows%2Fuser%2F<user_id>&entity_type=movie&sort=watched_date%2Cdesc`. Il pèse ~400kb pour ~200 films regardés.

Cliquer dessus, et dans l'onglet `Response` le résultat ressemble en gros à ça :

```json
{
    "status": "success",
    "data": {
        "user_id": 12345678,
        "type": "list",
        "objects": [
            {
                "uuid": "1f663b20-66d9-5f07-b6eb-59225ffea406",
                "type": "follow",
                "...": "..."
            }
        ]
    }
}
```

{{< vs 4 >}}

<p align="center">
  {{< img src="/posts/tutoriels/tvtime-to-letterboxd/firefox-network.webp" align="center" alt="Capture d'écran de l'onglet Network avec la réponse du json sur Firefox" >}}
  <p style="text-align: center;"><i>Récupérer le json sur Firefox</i></p>
</p>

{{< vs 4 >}}

<p align="center">
  {{< img src="/posts/tutoriels/tvtime-to-letterboxd/chrome-network.webp" align="center" alt="Capture d'écran de l'onglet Network avec la réponse du json sur Chrome" >}}
  <p style="text-align: center;"><i>Récupérer le json sur Chrome/Chromium</i></p>
</p>

{{< vs 4 >}}

Si c'est le bon, copier son contenu et le coller dans un fichier `tvtime.json`.

{{< vs 4 >}}

## Manipulation avec jq

Letterboxd a [un outil d'import de fichiers csv](https://letterboxd.com/about/importing-data/). C'est pourquoi il faut convertir le json pour qu'il soit lu par Letterboxd. 

Il faut une liste avec les titres, les années de sortie et les dates de visionnage des films.

[Installez donc jq au préalable](https://jqlang.org/download/).

Dans la colonne *WatchedDate*, je récupère la date de visionnage. Comme certains films n'ont pas la valeur de remplie, j'ai dû mettre une date par défaut. 

Pensez donc à remplacer le `2025-05-19`.

```bash
jq -r '
  (["Title","Year","WatchedDate"]),
  ( .data.objects[] |
    [
      .meta.name,
      (.meta.first_release_date | split("-")[0]),
      (.watched_at // "" | if length > 0 then split("T")[0] else "2025-05-19" end)
    ]
  )
  | @csv
' tvtime.json > letterboxd.csv
```

{{< vs 4 >}}

Un équivalent sur PowerShell pour Windows (n'utilise pas jq)

```pwsh
$json = Get-Content -Raw -Path "tvtime.json" | ConvertFrom-Json
$csvLines = @()
$csvLines += '"Title","Year","WatchedDate"'

foreach ($item in $json.data.objects) {
    $title = $item.meta.name
    $year = ($item.meta.first_release_date -split "-")[0]

    if ($item.watched_at) {
        $watchedDate = ($item.watched_at -split "T")[0]
    } else {
        $watchedDate = "2025-05-19"
    }

    $csvLine = '"' + $title.Replace('"', '""') + '","' + $year + '","' + $watchedDate + '"'
    $csvLines += $csvLine
}

$csvLines | Set-Content -Path "letterboxd.csv" -Encoding UTF8
```

{{< vs 4 >}}

## Import du csv dans Letterboxd

Sur [la page d'importation](https://letterboxd.com/import/), ouvrir le fichier `letterboxd.csv` généré au-dessus. 

Il faut ajouter manuellement les films qui n'ont pas été reconnus. Activer *Hide successful matches* pour les compléter. 

Puis cliquer sur *Import films*.

---

Si vous avez des questions ou des suggestions, n'hésitez pas à me contacter par [mail](mailto:timothe@chauvet.cloud), sur [LinkedIn](https://www.linkedin.com/in/timothechauvet/) ou directement en envoyant [une *issue* sur GitHub](https://github.com/timothechauvet/timothechauvet.github.io/issues)