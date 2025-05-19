---
title: "Export your data from TV Time to Letterboxd 🎬"
date: 2025-05-19
hero: banner.webp
description: Export the data from TV Time to import it to Letterboxd
theme: Toha
---

Am I the only one with friends **forcing me** to join Letterboxd?

I come from TV Time. The app is notorious for [blocking users from accessing their data](https://www.reddit.com/r/TVTime/comments/mxx3km/do_you_know_a_way_to_exportimport_watchlist_and/). So I had to find another way, since I have hundreds of movies to transfer.

{{< vs 4 >}}

<p align="center">
  {{< img src="/posts/tutoriels/tvtime-to-letterboxd/banner.webp" width="700" align="center" >}}
</p>

{{< vs 4 >}}

# A monopage app coded with Flutter

Luckily for us, it's possible to access [TV Time on the web](https://app.tvtime.com/).

1. Login
2. Go to your profile
3. Go to your movie list.

I quickly realized that the HTML DOM isn't available. It's because of Flutter's *shadow DOM* (if I got it right).

We need to think outside the box.

{{< vs 4 >}}

## The Network tab

In the developer console (`Ctrl/Cmd + Shift + C`), go to the `Network` tab. 

By doing a [hard refresh](https://www.howtogeek.com/672607/how-to-hard-refresh-your-web-browser-to-bypass-your-cache/) a pretty big json is fetched. 

The json is fetched from `https://app.tvtime.com/sidecar?o=https%3A%2F%2Fmsapi.tvtime.com%2Fprod%2Fv1%2Ftracking%2Fcgw%2Ffollows%2Fuser%2F<user_id>&entity_type=movie&sort=watched_date%2Cdesc`. It weights ~400kb for ~200 movies.

Click on it, and on `Response` the json looks like this :

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
  {{< img src="/posts/tutoriels/tvtime-to-letterboxd/firefox-network.webp" align="center" alt="Screenshot of the Network tab from Firefox" >}}
  <p style="text-align: center;"><i>Get the json on Firefox</i></p>
</p>

{{< vs 4 >}}

<p align="center">
  {{< img src="/posts/tutoriels/tvtime-to-letterboxd/chrome-network.webp" align="center" alt="Screenshot of the Network tab from Chrome" >}}
  <p style="text-align: center;"><i>Get the json on Chrome/Chromium</i></p>
</p>

{{< vs 4 >}}

If it's correct, copy/paste its contents to a file named `tvtime.json`.

{{< vs 4 >}}

## Transform the json with jq

Letterboxd has a [csv importer tool](https://letterboxd.com/about/importing-data/). That's why we need to convert the json so it can be read by Letterboxd.

We need a list with titles, release years and watched dates.

[Install jq first](https://jqlang.org/download/).

On the *WatchedDate* column, I grab the watched date. As some of them don't have this field, I had to fill them out with a default date. 

So you need to change the part with `2025-05-19` to your preference, inside the script.

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

The following is for PowerShell users (doesn't use jq)

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

## Import the csv on Letterboxd

On the [import page](https://letterboxd.com/import/), open the `letterboxd.csv` file.

We need to manually import the movies that weren't recognized. Check *Hide successful matches* to fill them up. 

Then click on *Import films*.

---

If you have any questions or suggestions, feel free to contact me by [email](mailto:timothe@chauvet.cloud), on [LinkedIn](https://www.linkedin.com/in/timothechauvet/), or directly by sending [an *issue* on GitHub](https://github.com/timothechauvet/timothechauvet.github.io/issues).
