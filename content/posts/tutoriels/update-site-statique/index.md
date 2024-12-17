---
title: "Mise à jour de mon site statique ⬆️"
date: 2024-12-17
hero: update.webp
description: Mon site Hugo avec le thème Toha a été mis à jour
theme: Toha
---

J'ai fait SUBIR une refonte plutôt conséquente à mon blog. Je voulais me poser quelques minutes pour écrire ma démarche.

Cet article fait suite à [celui sur la création du blog](../site-statique-hugo).

{{< vs 4 >}}

<p align="center">
  {{< img src="/posts/tutoriels/update-site-statique/update.webp" width="400" align="center" alt="Illustration rouge-orange d'une mascotte avec une illustration abstraite de mise à jour" >}}
  <p style="text-align: center;"><i>Bannière "update" générée par Dall-e 3</i></p>
</p>

{{< vs 4 >}}


## La surprise

J'ai appris que Toha, le thème que j'utilise pour Hugo, a été mis à jour. 

C'est d'ailleurs Hugo qui me l'a fait savoir, car *impossible* de compiler le site en local avec une version trop récente de Hugo. J'avais donc deux options :

1. Mettre une ancienne version de Hugo en local
2. **Mettre à jour mon thème pour qu'il soit compatible avec mon Hugo local**

{{< vs 4 >}}

## Passer de module git à module hugo

C'est très bien expliqué sur [la documentation du thème](https://toha-guides.netlify.app/posts/update-v3-to-v4/). Il faut donc

1. From gitmodule (...)
```bash
# Terminal
git rm themes/toha/
git commit -m "Remove v3 theme"
```

2. Retirer la ligne `theme: toha` de la config .yaml

{{< alert type="info" >}}
Il vous faudra Node.js, Hugo et Go à jour pour la suite !
{{< /alert >}}

3. Renommer config.yaml en hugo.yaml
4. (...) to hugomodule
```bash
# Terminal
hugo mod init github.com/timothechauvet/timothechauvet.github.io
```

5. Rajouter le thème en tant que module dans hugo.yaml
```yaml
module:
  imports:
  - path: github.com/hugo-toha/toha/v4
  mounts:
  - source: static/files
    target: static/files
  - source: ./node_modules/flag-icon-css/flags
    target: static/flags
  - source: ./node_modules/@fontsource/mulish/files
    target: static/files
  - source: ./node_modules/katex/dist/fonts
    target: static/fonts
```

6. Faire les [différentes modifications au fichier hugo.yaml](https://toha-guides.netlify.app/posts/update-v3-to-v4/#6-update-the-hugoyaml-file). Pour moi il y avait
    - darkMode
    - analytics (j'utilise goat counter, les chèvres c'est marrant)
7. Initialiser les modules
```bash
# Terminal
hugo mod tidy
hugo mod npm pack
npm install
```

8. Un peu relou car Node déteste les librairies pas à jour. J'ai aussi lancé `npm audit fix --force` dans le terminal pour résoudre ça temporairement.

{{< vs 4 >}}

## Mettre à jour l'action GitHub

Bon déjà un gros problème chez moi c'est que je n'utilisais pas les actions dédiées à la compilation Hugo. J'ai donc troqué le téléchargement du binaire pour l'action [peaceiris/actions-hugo](https://github.com/peaceiris/actions-hugo) qui facilite le tout.

Sinon le reste est resté similaire. [PeaceIris avait aussi une action pour publier le site sur GitHub Pages](https://github.com/peaceiris/actions-gh-pages), mais je la trouve vraiment overkill pour 2 petites étapes en moins.

Voici ce qui a marché chez moi :

```yaml
name: 🤖 Deploy Hugo site to Pages

on:
  push:
    branches: 
      - master
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: "pages"
  cancel-in-progress: true

defaults:
  run:
    shell: bash

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4
        with:
          submodules: true
          fetch-depth: 0

      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v3.0.0
        with:
          hugo-version: '0.139.4' # Dernière version en date
          extended: true
            
      - name: Update hugo modules
        run: |
          # Récupérer les derniers modules
          hugo mod get -u
          hugo mod npm pack

          # Nettoyer le fichier go.sum
          hugo mod tidy
      
      - name: Install node modules
        run: |
          # Installer les modules Node
          npm install

          # Retirer les failles de sécurité
          npm audit fix --force

      - name: Build with Hugo
        env:
          HUGO_ENVIRONMENT: production
          HUGO_ENV: production
        run: |
          hugo \
            --minify \
            --baseURL "${{ steps.pages.outputs.base_url }}/"
      
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: ./public

  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Deploy to GitHub Pages
        uses: actions/deploy-pages@v4 # J'ai pu mettre ça à jour aussi depuis que j'ai Node >16 !
```

{{< vs 4 >}}

## Résoudre des problèmes qui n'étaient pas là avant

Et là, le site fonctionne !! Avec en prime
- Le changement de langue fonctionne bien à l'intérieur un article
- Un meilleur espacement des éléments
- Une nouvelle palette
- Une table des matières
- Possibilité de publier un article dans le futur

Mais j'ai très vite remarqué que pas mal d'images sont maintenant cassées en production 

{{< img src="https://risibank.fr/cache/medias/0/3/326/32694/full.png" alt="J'ai mal" width="100" >}}

{{< vs 2 >}}

J'ai donc dû remplacer tous les `<img src=.*/>` par `{` `{< img src= >}}`

Le chemin des images avait aussi changé, de `monimage.webp` vers `/posts/tutoriels/mon-article/monimage.webp` (uniquement pour la partie anglaise traduite) 

Pour la page d'accueil, les badges de certifications étaient cassés eux aussi. C'est un peu spécial car ils sont rendus de facto par le thème et pas rédigés en Markdown comme cet article par exemple. J'ai donc remplacé les images statiques hébergées dans `static/images/about` par un Storage Account sur Azure.

Et pour finir, j'ai retiré les tags, car devinez-quoi : **maintenant ils fonctionnent**. Et j'ai une flemme absolue d'avoir à gérer ça.

---

Si vous avez des questions ou des suggestions, n'hésitez pas à me contacter par [mail](mailto:timothe@chauvet.cloud), sur [LinkedIn](https://www.linkedin.com/in/timothechauvet/) ou directement en envoyant [une *issue* sur GitHub](https://github.com/timothechauvet/timothechauvet.github.io/issues)