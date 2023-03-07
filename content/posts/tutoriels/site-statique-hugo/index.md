---
title: "Héberger son site perso pour 0€ 💸"
date: 2023-01-05
hero: mascot.webp
description: Héberger son site statique Hugo avec GitHub Pages et Actions pour pas un rond
theme: Toha
tags: ["Traduit", "Site internet"]
---

- Accroche (gratuit, facile, config puis oublie)
- Besoins (gh)
- 1) Prendre ce qu'il faut
- 2) Choisir le thème
- 3) Config github
- 4) Push le site
- 5) Custom domain
- 6) Publier sur le site
- Conclusion

LinkedIn, Meta et Twitter veulent leur peau ! Pour créer son contenu à l'ère des 20's (à partir de 2020), il existe pléthore de plateformes. Parfois naissantes (Mastodon, Notion), parfois éphémères (Google +, Skyblog), parfois boudées (Facebook, Twitter), celles-ci ont une vie parfois plus courte que celle de votre machine à laver. <u>Mais il existe bien une plateforme qui ne subit pas l'obsolescence programmée : les blogs.</u>

Dans le premier article de mon super blog, je voulais vous raconter comment j'ai réalisé... mon blog. Hébergé par GitHub <i class="fa-brands fa-github"></i>, produit par Hugo et alimenté par moi-même, les coûts me reviennent à 0€, enfin presque. J'espère qu'en lisant ce tutoriel, vous pourrez vous rendre compte à quel point il est facile de, vous aussi, faire votre https://timothechau.vet à moindre frais.

</br>
</br>

## Le problème avec WordPress pour faire son blog 😓

Je ne dois pas être le seul à trouver que faire un site perso avec WordPress, c'est la loose. Même si 43.1% de tous les sites internet au monde utilisent WordPress ([W3techs, janvier 2023](https://w3techs.com/technologies/details/cm-wordpress)), gérer un site dynamique et performant demande beaucoup d'investissement. Certaines de ces complications incluent :
- l'hébergement OVH à 50€ par an minimum (pour un service décent) 💸
- les crashs sans raison
- la panique quotidienne des 200 tentatives de connexion
- la veille permanente des extensions à jour pour minimiser les failles
- la configuration d'un CDN pour avoir un site *responsive* partout dans le monde (parfois + cher que l'hébergement)
- la gestion du SEO pour monter dans les résultats sur Google
- ...

En réalité, WordPress est un très bon [CMS](https://fr.wikipedia.org/wiki/Syst%C3%A8me_de_gestion_de_contenu) pour avoir un beau site dynamique. Puissant, complet, open-source, né de la génération Z (en 2003 quand même), WordPress n'a plus à faire ses preuves. Mais pour faire un blog perso, WordPress est vraiment, vraiment trop poussé. Un site internet statique, n'ayant pas toutes les fonctionnalités dynamiques de WordPress, est souvent largement suffisant pour faire son blog. Et ça tombe bien, c'est justement un site internet statique que vous avez devant vos yeux (ébahis).

Pour expliquer ce qu'est **un site statique**, il s'agit d'un site n'utilisant que du code source en HTML, CSS et JavaScript et n'ayant pas besoin d'une base de données ou d'un serveur logique poussé.

</br>
</br>

## Le meilleur des blogs, c'est Hugo

En 2013, quand Hugo Chávez nous quittait, Hugo (le logiciel, pas le président du Venezuela) venait de naître. Ce générateur de site statique écrit en Go, le langage ultra-rapide de Google, est toujours connu comme le ["framework pour construire des sites le plus rapide du monde"](https://gohugo.io/). Sans rentrer dans les détails, il permet au plus grand nombre de configurer et lancer son site. Open-source, gratuit et facile d'utilisation, il est conçu pour qu'on le configure une fois et qu'on l'oublie ensuite.

Hugo est **facile à configurer**, et une fois cette configuration faite **on peut l'oublier** et alimenter son blog facilement. C'est ce que je fais avec mon blog ainsi que de nombreuses personnes. 

<p align="center">
  <img src="hugo-mascotte.webp" alt="Mascotte de Hugo" width="150"/>
  <p style="text-align: center;"><i>Go et Hugo ensemble, ça donne <a href="https://gohugo.io">Super-Hugo</a></i></p>
</p>

</br>

#### Comment ça, un générateur de site statique écrit en Go ?

Hugo est en effet écrit en Go, car c'est un logiciel qui prend du code et **génère un site statique en HTML, CSS et JavaScript**. Il n'a donc pas besoin d'être exécuté constamment, contrairement à un site en PHP qui nécessite qu'un serveur PHP soit tout le temps actif pour qu'il fonctionne. Juste un coup de `hugo build` qui génère le code HTML, CSS et JavaScript, et le code source du site statique est prêt à être hébergé sur un serveur ! 

#### Et ce code qu'Hugo prend pour en faire un site, c'est du Go ?

Non. Le logiciel Hugo est codé en Go, mais ce n'est pas le langage qu'il utilise pour alimenter le site. Le [Markdown](https://www.markdownguide.org/), un langage simple pour décorer son code, permet de structurer le contenu. On y reviendra après sur son utilisation

#### Y a-t-il d'autres générateurs de sites statiques que Hugo ?

Un peu choqué et déçu que Hugo ne soit pas à la hauteur, mais [Gatsby](https://www.gatsbyjs.com/docs/how-to/previews-deploys-hosting/how-gatsby-works-with-github-pages/) et [Jekyll](https://docs.github.com/fr/pages/setting-up-a-github-pages-site-with-jekyll) font la même chose

</br>
</br>

## Les grandes étapes de ce tutoriel

Hugo étant donc fait de code (comme n'importe-quel site), il est possible d'héberger ce code avec un repository Git sur GitHub <i class="fa-brands fa-github"></i>. Git <i class="fa-brands fa-git-alt"></i> permet notamment **de gérer les versions de ce code source**, pour ainsi sauvegarder un site Hugo et récupérer une ancienne version, au cas-où. 

GitHub <i class="fa-brands fa-github"></i> permet aussi d'héberger des sites internet *statiques*. On peut aussi mettre à jour automatiquement ce site en mettant le code de Hugo directement dans le *repository* et en laissant GitHub Actions <i class="fa-brands fa-github"></i> se charger du reste.

Ainsi, les grandes étapes sont :
1. Installer Git <i class="fa-brands fa-git-alt"></i>, Go <i class="fa-brands fa-golang"></i>, Hugo, GitHub (et éventuellement un éditeur de texte) sur son ordinateur
2. Créer un repository sur GitHub <i class="fa-brands fa-github"></i> pour héberger le code et le cloner en local
3. Trouver et installer un thème pour Hugo
4. Remplir un peu le site et l'envoyer sur GitHub <i class="fa-brands fa-github"></i>
5. Activer GitHub Pages <i class="fa-brands fa-github"></i> et éventuellement ajouter un nom de domaine
6. Mettre en place une *pipeline CI/CD* pour déployer le site automatiquement

C'est parti 🚀

</br>
</br>

# Étape 1 • Installer Git, GitHub, Go, Hugo et Visual Studio Code

Pour utiliser la glorieuse puissance de GitHub, il faut d'abord installer **Git** <i class="fa-brands fa-git-alt"></i> sur votre ordinateur. Il existe aussi des options plus simples comme [GitHub Desktop <i class="fa-brands fa-github"></i>](https://desktop.github.com/). Mais pour les jeunes padawans* parmi nous qui veulent découvrir le monde du développement, on va passer par la méthode classique, et en ligne de commande !

<sub>* Je ne regarde pas Star Wars merci de ne pas le mentionner dans nos conversations</sub>

On va aussi en profiter pour installer Go <i class="fa-brands fa-golang"></i>, le langage, et Hugo en ligne de commande. Pour Visual Studio Code, vous avez des alternatives comme un bloc notes par défaut, [Notepad++](https://notepad-plus-plus.org/downloads/) ou encore [Microsoft Word](https://www.youtube.com/watch?v=X34ZmkeZDos) <sub>(c'est une blague ne faites pas ça).</sub>

</br>

**Installation Windows <i class="fa-brands fa-microsoft"></i>**

Pour Windows, nous allons aussi installer [Chocolatey](https://chocolatey.org/) qui gère les installations de logiciels en ligne de commande. Tout est dans les lignes de code en dessous

Vous devez ouvrir PowerShell en mode administrateur :
1. Appuyez sur la touche <kbd><i class="fa-brands fa-microsoft"></i></kbd> ou <kbd><i class="fa-brands fa-windows"></i></kbd>
2. Tapez "PowerShell"
3. Cliquez sur "Exécuter en tant qu'administrateur" ou "Run as administrator"
4. Appuyez sur <kbd>Oui</kbd>
5. Lancez ces commandes :

```powershell
  # Installer Chocolatey, le gestionnaire de paquets supporté par Hugo et que nous allons utiliser pour les autres logiciels. Très utile pour vos autres projets
  Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))

  # Installer Git 
  choco install git

  # Installer GitHub
  choco install gh

  # Installer Go
  choco install golang

  # Installer Hugo
  choco install hugo-extended

  # Installer Visual Studio Code (recommandé pour éditer le code)
  choco install vscode
```
</br>

**Installation MacOS <i class="fa-brands fa-apple"></i>**

Pour MacOS, nous allons aussi installer [Homebrew](https://brew.sh/) qui gère les installations de logiciels en ligne de commande. Tout est dans les lignes de code en dessous.

Vous devez ouvrir votre terminal :
1. Appuyez sur les touches <kbd>⌘</kbd> + <kbd>espace</kbd>
2. Tapez "Terminal.app" et cliquez dessus
3. Lancez ces commandes :

```powershell
  # Installer Homebrew, le gestionnaire de paquets supporté par de nombreux logiciels. Très utile pour vos autres projets
  /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

  # Installer Git 
  brew install git

  # Installer GitHub
  brew install gh

  # Installer Go
  brew install go

  # Installer Hugo
  brew install hugo

  # Installer Visual Studio Code (recommandé pour éditer le code)
  brew install --cask visual-studio-code
```
</br>

---

Si jamais vous avez un problème dans l'une de ces étapes (peu importe Windows/Mac) ou que vous êtes sur Linux, voici une liste des logiciels à installer que vous pouvez faire directement depuis leur site :

1. [Git](https://git-scm.com/book/fr/v2/D%C3%A9marrage-rapide-Installation-de-Git) <i class="fa-brands fa-git-alt"></i>
1. [GitHub](https://git-scm.com/book/fr/v2/D%C3%A9marrage-rapide-Installation-de-Git) <i class="fa-brands fa-github-alt"></i>
2. [Go](https://go.dev/doc/install) <i class="fa-brands fa-golang"></i>
3. [Hugo](https://gohugo.io/installation/)
4. (Optionnel) [Visual Studio Code](https://code.visualstudio.com/download)

</br>
</br>

# Étape 2 • Créer un repository sur GitHub et le cloner en local

### a. Créer un compte GitHub
Premièrement, créez un compte sur [github.com](https://github.com) et consciencieusement [ajouté l'authentification en plusieurs étapes (MFA)](https://docs.github.com/fr/authentication/securing-your-account-with-two-factor-authentication-2fa/configuring-two-factor-authentication), et **notez bien votre pseudo**.

</br>

### b. Créer un nouveau repository
Sur la page d'accueil, appuyez sur le bouton vert [<kbd><i class="fa-solid fa-book"></i> New</kbd>](https://github.com/new).

Sur la page, "Create a new repository" apparaît. Remplissez des informations
- *Owner* : votre compte, 
- ***Repository name* `(important)` : {votre_pseudo}.github.com** *(par exemple : timothechauvet.github.io)*
- *Description* : Mon blog trop tendance sur Hugo
- *Public/Private* : Public

Et appuyez sur <kbd>Create repository</kbd>.

Une fois votre repository créé, allez dessus et notez l'URL, par exemple [https://github.com/timothechauvet/timothechauvet.github.io](https://github.com/timothechauvet/timothechauvet.github.io)

</br>

### c. Cloner le repository en local
Maintenant vous allez devoir revenir sur votre terminal/PowerShell (sauf si vous avez GitHub Desktop ou un équivalent). Pas besoin de lancer PowerShell en mode administrateur cette fois si vous êtes sur Windows.

Il vous faudra d'abord vous identifier à GitHub. Pour cela, entrez la commande `gh auth login` et utilisez ces paramètres :
1. GitHub.com
2. HTTPS
3. Y
4. Login with a web browser
   - Copier le code, appuyez sur entrée
   - Coller le code sur https://github.com/login/device
   - Autoriser GitHub CLI
   - Réentrer le mot de passe

Revenez sur la ligne de commandes et vous voilà connecté !

Déplacez-vous dans le dossier de votre choix (par exemple `cd ./Documents`) et entrez les commandes :
```powershell
# Cloner le repository sur votre ordinateur
git clone https://github.com/{votre_pseudo}/{votre_pseudo}.github.io

# Vous déplacer dans le dossier du repository
# Astuce 💡 : écrivez les 3 premières lettres de votre pseudo et appuyez sur Tab 
# pour remplir le reste tout seul
cd {votre_pseudo}.github.io

# Ouvrir le dossier dans Visual Studio Code si vous l'avez installé
code .
```

</br>
</br>

### d. Initialiser Hugo {#d-initialiser-hugo}

Dans Visual Studio Code (Vscode pour faire + rapide <sub>notez l'usage du "+" pour aller encore + rapidement</sub>), remarquez que le projet est vide. C'est normal, il faut d'abord que Hugo installe le projet dans le dossier actuel. 

Dans le terminal, lancez :
```powershell
hugo new site . -f yaml
```

</br>

Trois choses : `hugo new site` va générer un site vide, `.` indique que le site se trouve dans le dossier actuel, et `-f yaml` indique que les fichiers de configuration utiliseront le format [YAML](https://yaml.org/), un moyen de sauvegarder des données que j'apprécie davantage que [TOML](https://toml.io/en/) utilisé par défaut, et que j'utiliserai pour ce tutoriel. C'est tout à fait optionnel et vous pouvez refaire la commande sans ça si vous préférez.

</br>

### e. Configurer .gitignore

Git est plutôt logique, il envoie tous les fichiers du dossier où vous êtes vers GitHub quand vous lui demandez. L'idéal serait de n'envoyer que les fichiers nécessaires pour ne pas encombrer GitHub de fichiers temporaires ou qui contiennent des informations sensibles. C'est là où le fichier `.gitignore` entre en jeu, celui-ci va empêcher des fichiers, dossiers et extensions d'être sauvegardés par Git, et donc d'être publiés sur votre repository GitHub.

Dans Visual Studio Code, dans l'onglet Explorateur, Créez un nouveau fichier à la racine du dossier, appelez-le ".gitignore" et modifiez-le pour ajouter ces lignes :

```php
# Fichiers générés par le thème et votre système
node_modules
.DS_Store
dist
tmp

# Fichiers générés par Hugo
/public/
/resources/_gen/
/assets/jsconfig.json
hugo_stats.json

# Exécutables
hugo.exe
hugo.darwin
hugo.linux

# Verrou de modification
/.hugo_build.lock
```

Vous verrez peut-être déjà des dossiers dans l'onglet explorateur se griser. Ça veut dire qu'ils ont été ignorés

</br>

### f. Push le code sur GitHub

Maintenant que le site commence à venir, on va d'abord le sauvegarder sur GitHub. 

Dans le terminal, lancez :
```powershell
# Indexer tous les fichiers 
git add .

# Faire un commit des fichiers indexés
git commit -m "Premier commit"

# Pousser le code
git push
```

Si tout se passe bien, vous devriez être invité à vous connecter à GitHub. [Sinon, suivez ce tutoriel de GitHub](https://docs.github.com/fr/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent).

Une fois le code poussé avec *push*, vous pouvez retourner sur votre repository pour constater les changements

</br>

### g. Voir ce que ça donne {#g-voir-ce-que-ca-donne}

Revenons sur votre site, on va voir ensemble ce que ça donne. Lancez la commande `hugo serve` dans votre terminal.

Sur votre [navigateur internet de haute qualité](https://firefox.com), allez sur l'URL <u>localhost</u> indiqué après "Web Server is available at https://localhost:XXXX" 

`Page Not Found`

Pas très palpitant pour un super blog. Pas de panique c'est normal, il faut d'abord installer un thème.

</br>
</br>

# Étape 3 • Trouver et installer un thème

### a. Trouver un thème

Maintenant que votre super blog est sur GitHub, il va falloir l'habiller. Allez sur [le site des thèmes de Hugo](https://themes.gohugo.io/) et trouvez ce qu'il vous plait. Alternativement, vous pouvez trouver ces mêmes projets et peut-être davantage directement [en les cherchant sur GitHub](https://github.com/topics/hugo-theme).

Personnellement j'utilise [Toha d'Emruz Hossain](https://github.com/hugo-toha/toha). Une fois votre thème trouvé, appuyez sur <kbd>Download</kbd> pour aller sur le repository Git du thème

</br>
</br>

### b. Ajouter un sous-module Git

Vous voilà avec un magnifique thème pour votre blog ✨

Maintenant que vous êtes sur le repository du thème, il va falloir l'ajouter à votre site. Pour ce faire, lancer dans le terminal (qui doit toujours être dans le dossier du site) :
```powershell
git submodule add https://github.com/{votre_theme} themes/{votre_theme}
# Par exemple : git submodule add https://github.com/hugo-toha/toha themes/toha
```

</br>

Ajouter un sous-module, avec la commande `git submodule add`, revient à ajouter un projet Git dans un même projet Git. Ainsi, pour chacune des mises à jour du créateur de votre thème, celles-ci se reflèteront sur votre propre site. Pratique non ?

</br>
</br>

### b bis. Alternativement : ajouter le thème manuellement

Il est aussi possible que certaines mises à jour puissent casser votre site. Personnellement ça ne m'est jamais arrivé, mais si vous voulez télécharger le thème depuis une version très spécifique, il vous suffit de télécharger le repository (depuis le bouton <kbd><i class="fa-solid fa-code"></i> Code</kbd> puis <kbd><i class="fa-regular fa-file-zipper"></i> Download ZIP</kbd>). Ensuite, il faut extraire le contenu de l'archive .zip dans le dossier `themes\nom_de_votre_thème` (exemple : themes\toha).

</br>
</br>

### c. Ajouter le thème à la configuration

Ouvrez le fichier `config.yaml` et ajoutez ces lignes :

```yaml
baseURL: https://votrepseudo.github.io
languageCode: fr-fr
title: "46000% sport l'actu sportive"
theme: lenomdevotrethème
# Astuce 💡 : enregistrez simplement le fichier, la commande "hugo serve"  
# devrait automatiquement recharger le site sur https://localhost:XXXX
```

</br>

<p align="center">
  <img src="hugo-terminal-vide.png" alt="Capture d'écran du thème Terminal vide" width="500"/>
  <p style="text-align: center;"><i>Exemple avec le thème <a href="https://github.com/panr/hugo-theme-terminal">Terminal</a></i></p>
</p>

Votre site devrait afficher une interface plus accueillante : c'est que votre thème s'est bien installé. Revenez dans le terminal et exécutez les commandes pour envoyer vos modifications à GitHub

Dans le terminal, lancez :
```powershell
# Indexer tous les fichiers modifiés
# Astuce 💡 : remplacez le point par les noms de fichiers/dossiers que vous voulez indexer
# Ces fichiers/dossiers sont indiqués d'un M dans l'explorateur à gauche sur Visual Studio Code
git add .

# Faire un commit des fichiers indexés
git commit -m "Second commit"

# Pousser le code
git push
```

À chaque fois que votre site va beaucoup changer, je vous invite à répéter ces trois commandes pour envoyer vos modifications sur GitHub. 

</br>
</br>

# Étape 4 • Customiser le blog et écrire des articles

### a. Customiser la page d'accueil

Maintenant que vous avez trouvé un tout joli thème pour votre blog, vient le temps de customiser le site. Le fichier `config.yaml` contient tous les détails de configuration.

*Note* : comme chaque thème est unique, votre blog le sera lui aussi. Ici je présente la démarche pour configurer le thème Terminal, **donc pensez bien à adapter votre configuration à celle de votre thème.**

Dans mon cas, le fichier de configuration d'exemple est sur [la page GitHub du thème](https://github.com/panr/hugo-theme-terminal). Ce sera très certainement votre cas

</br>
</br>

#### Convertir le fichier de configuration d'exemple en YAML

Petit problème : le format proposé de la configuration est en TOML. Il est aussi possible que le vôtre soit affiché en JSON ou en XML. Sachant qu'on a choisi notre configuration en YAML [lors de l'initialisation](#d-initialiser-hugo), il est donc indispensable de convertir le fichier dans ce format. 

À la main, ce n'est pas trop compliqué, les trois formats de données étant facilement convertibles. Mais pour nous faciliter la vie, nous allons utiliser un outil en ligne. Choisissez celui qui vous convient.

**Table de convertisseurs de formats (Transform.tools et Convertsimple.com)**
|  |  |  |  YAML  |  |  TOML  |  |  JSON  |  |  XML  |
|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|
|  |  |  |  |  |  |  |  |  |  |
|  | **YAML** |  |  |  | [YAML -> TOML](https://transform.tools/yaml-to-toml) |  | [YAML -> JSON](https://transform.tools/yaml-to-json) |  | [YAML -> XML](https://www.convertsimple.com/convert-toml-to-xml/) |
|  | **TOML** |  | [TOML -> YAML](https://transform.tools/toml-to-yaml) |  |  |  | [TOML -> JSON](https://transform.tools/toml-to-json) |  | [TOML -> XML](https://www.convertsimple.com/convert-toml-to-xml/) |
|  | **JSON** |  | [JSON -> YAML](https://transform.tools/json-to-yaml) |  | [JSON -> TOML](https://transform.tools/json-to-toml) |  |  |  | [JSON -> XML](https://www.convertsimple.com/convert-json-to-xml/) |
|  | **XML**  |  | [XML -> YAML](https://www.convertsimple.com/convert-xml-to-yaml/)  |  | [XML -> TOML](https://www.convertsimple.com/convert-xml-to-toml/)  |  | [XML -> JSON](https://transform.tools/xml-to-json)  |  |  |

Dès que vous avez votre résultat de prêt, copiez-le et rajoutez tout dans votre fichier `config.yaml`. Changez les paramètres selon vos envies.

Astuce 💡 : vous pouvez traduire votre site en plusieures langues. Dans la majorité des thèmes, vous avez un paramètre `languages` pour ça.


</br>

<p align="center">
  <img src="hugo-terminal-vert.png" alt="Capture d'écran du thème Terminal configuré" width="500"/>
  <p style="text-align: center;"><i>Exemple où je configure le thème en vert</i></p>
</p>

</br>

#### (Optionnel) Intégrer des .webp, .svg, tweets...

Dans vos articles si vous voulez intégrer divers types de documents, ajoutez ces lignes dans votre fichier de configuration. <u>Faites bien attention à ce que vous collez depuis internet sur votre site</u>, par exemple intégrer un Tweet implique l'installation d'un cookie Twitter chez chaque visiteur.

```yaml
# Raw HTML in Markdown
markup:
  goldmark:
    renderer:
      unsafe: true
```

</br>

### b. Ajouter des articles

Vient le moment de commencer à écrire un chef d'œuvre de prose virtuelle. Allez dans le dossier `content` et créez un dossier de catégorie, par exemple `articles`. Créez ensuite un fichier `monarticle.md` avec l'extension .md s'il-vous-plaît.

Dans ce fichier, ajoutez ces quelques lignes :

```yaml
---
title: "Super titre"
date: 2023-01-31
hero: illustration.png
description: "Superbe description"
theme: terminal
tags: ["premier article", "article"]
---
```

</br>

Notez l'attribut `hero` qui correspond à l'image d'illustration de l'article pour les thèmes qui sont compatibles.

Complétez le reste du document avec du contenu. Celui-ci utilise le format Markdown, un format très populaire le rédactionnel. Je vous laisse vous inspirer de [cette fiche d'Adam Pritchard](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet) pour remplir votre article.

Enregistrez (régulièrement) votre article puis retournez sur votre site. Normalement il devrait y être !

</br>

<p align="center">
  <img src="hugo-terminal-article.png" alt="Capture d'écran du thème Terminal avec un article" width="500"/>
  <p style="text-align: center;"><i>Exemple où je poste un article</i></p>
</p>

</br>
</br>
</br>

# Étape 5 • Paramétrer et automatiser GitHub Pages

### a. Mettre en place la pipeline GitHub Actions

Nous voilà maintenant prêts à mettre à disposition ce somptueux blog sur internet. GitHub stocke désormais le code source utilisé par Hugo, mais ce code n'est pas celui d'un site internet, vous savez, en HTML, CSS et JavaScript. Quand vous lanciez la commande `hugo serve` [un peu plus haut](#g-voir-ce-que-ca-donne), Hugo générait de manière transparente un site internet. Un site internet statique, pour être plus précis, d'où le fait que Hugo est *un générateur de sites statiques*.

Pour que GitHub puisse mettre à disposition le code source qu'il héberge sur internet, il faut donc qu'il trouve un moyen de lancer Hugo pour générer un site statique. C'est ce à quoi une pipeline de déploiement continu (CD pour Continuous Deployment) sert, et GitHub Actions remplit ce rôle.

Allez dans l'onglet <kbd><i class="fa-solid fa-gear"></i> Settings</kbd> sur votre repository et appuyez sur <kbd><i class="fa-solid fa-pager"></i> Pages</kbd>. Changez la source `Deploy from a branch` en `GitHub Actions`. Dans "browse all workflows", cherchez "Hugo" et un fichier YAML apparaît : c'est votre pipeline CD. 

Appuyez sur <kbd>Commit changes...</kbd> et ce fichier devrait apparaître dans le dossier `.github` de votre repository. Allez sur <kbd><i class="fa-regular fa-circle-play"></i> Actions</kbd>, cliquez sur le workflow à gauche, <kbd>Run workflow</kbd> et votre site devrait être accessible depuis https://votrepseudogithub.github.io ! 

</br>

<p align="center">
  <img src="hugo-terminal-githubio.png" alt="Capture d'écran du thème Terminal publié sur github.io" width="500"/>
  <p style="text-align: center;"><i>Constatez que l'URL (modifié) pointe sur github.io !</i></p>
</p>

</br>
</br>

Pensez à faire `git pull` sur votre ordinateur pour charger votre pipeline CD qui se trouve maintenant dans votre repository.

### b. Configurer Dependabot

### c. Analyser le trafic du site

</br>
</br>

# Étape 6 • Acheter et configurer un nom de domaine

### a. Acheter un nom de domaine

Une fois que vous êtes satisfaits de votre site (après l'avoir push sur GitHub), il est maintenant temps de le publier sur internet. Mais avant, il faut se procurer un nom de domaine.

Un nom de domaine permettra à votre blog de se démarcher : chaque nom de domaine est unique. Il est possible d'en acheter un chez un fournisseur de domaines. Un fournisseur très populaire est OVH, qui fournit des noms de domaines en .fr pour 6€ par an (à l'écriture de l'article).

Achetez donc votre nom de domaine [à ce lien](https://www.ovhcloud.com/fr/domains/) ou chez un autre revendeur. Il y a plusieurs choses à considérer :
- J'ai dit dans le titre que l'hébergement coûte 0€. On parle bien ici du nom de domaine, pas de l'hébergement. Puis de toutes façons vous pouvez aussi faire sans et [aller à l'étape C](#c-configurer-github-pages)
- Les infos administratives que vous fournirez au fournisseur de domaine seront accessibles publiquement par des [outils type Whois](https://whois.domaintools.com/) ; vous devrez voir pour les cacher manuellement après. Apparemment pour OVH c'est caché par défaut chez les particuliers [(source)](https://community.ovh.com/t/masquer-informations-whois-domaine-fr/27694/7).
- Si vous ne renouvelez pas régulièrement le nom de domaine (c'est-à-dire payer 7€ par an), celui-ci libèrera automatiquement votre nom de domaine et de gentils robots s'emploieront (peut-être) à racheter le domaine pour le revendre ensuite plusieurs milliers d'euros [(source)](https://fr.wikipedia.org/wiki/Cybersquattage).

</br>
</br>

### a bis. (Optionnel) Installer un CDN (CloudFlare)

Une option envisageable serait d'installer un CDN (Content Delivery Network) comme CloudFlare qui est gratuit. Cela permet notamment :
- Livraison du site en cache dans tout le monde (notamment pour nos chers lecteurs Malgaches et Canadiens qu'on salue)
- Protection contre les attaques types DDoS
- Source du serveur anonymisée

Dans notre cas nous hébergons notre site sur GitHub donc ce n'est même pas la peine d'essayer de rajouter une protection à celle déjà présente. Mais si vous voulez quand même le faire, vous pouvez suivre [ce tutoriel de Numelion](https://www.numelion.com/installer-cloudflare.html).

</br>
</br>

### b. Configurer les entrées DNS

Une fois que votre domaine est bien installé, vous n'avez plus qu'à configurer les entrées DNS. Vous n'aurez plus rien d'autre à faire du côté du fournisseur de domaines après ça.

1. Allez sur le gestionnaire de DNS
2. Ajoutez des enregistrements A avec les adresses IP suivantes (laissez les autres options comme TTL par défaut):
```txt
185.199.108.153
185.199.109.153
185.199.110.153
185.199.111.153
```

3. Ajoutez des enregistrements AAAA avec les adresses IP suivantes :

```txt
2606:50c0:8000::153
2606:50c0:8001::153
2606:50c0:8002::153
2606:50c0:8003::153
```


### c. Configurer le nom de domaine personnalisé sur GitHub

</br>
</br>


</br>
</br>

# Conclusion

---


🤖 *Cet article a été écrit <u>sans</u> l'aide d'une IA*

Bannière "Éléphants" générée par [DALL•E](https://labs.openai.com)