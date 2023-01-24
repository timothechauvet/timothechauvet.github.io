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
1. Installer Git <i class="fa-brands fa-git-alt"></i>, Go <i class="fa-brands fa-golang"></i>, Hugo (et éventuellement un éditeur de texte) sur son ordinateur
2. Créer un repository sur GitHub <i class="fa-brands fa-github"></i> pour héberger le code et le cloner en local
3. Trouver et installer un thème pour Hugo
4. Remplir un peu le site et l'envoyer sur GitHub <i class="fa-brands fa-github"></i>
5. Activer GitHub Pages <i class="fa-brands fa-github"></i> et éventuellement ajouter un nom de domaine
6. Mettre en place une *pipeline CI/CD* pour déployer le site automatiquement

C'est parti 🚀

</br>
</br>

# Étape 1 • Installer Git, Go, Hugo et Visual Studio Code

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

```bash
  # Installer Chocolatey, le gestionnaire de paquets supporté par Hugo et que nous allons utiliser pour les autres logiciels. Très utile pour vos autres projets
  Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))

  # Installer Git 
  choco install git

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

```bash
  # Installer Homebrew, le gestionnaire de paquets supporté par de nombreux logiciels. Très utile pour vos autres projets
  /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

  # Installer Git 
  brew install git

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

Déplacez-vous dans le dossier de votre choix (par exemple `cd ./Documents`) et entrez les commandes :
```bash
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

### d. Initialiser Hugo
Dans Visual Studio Code (Vscode pour faire + rapide <sub>notez l'usage du "+" pour aller encore + rapidement</sub>), remarquez que le projet est vide. C'est normal, il faut d'abord que Hugo installe le projet dans le dossier actuel. 

Dans le terminal, lancez :
```bash
hugo new site . -f yaml
```

</br>

Trois choses : `hugo new site` va générer un site vide, `.` indique que le site se trouve dans le dossier actuel, et `-f yaml` indique que les fichiers de configuration utiliseront le format [YAML](https://yaml.org/), un moyen de sauvegarder des données que j'apprécie davantage que [TOML](https://toml.io/en/) utilisé par défaut, et que j'utiliserai pour ce tutoriel. C'est tout à fait optionnel et vous pouvez refaire la commande sans ça si vous préférez.

</br>

### e. Push le code sur GitHub

Maintenant que le site commence à venir, on va d'abord le sauvegarder sur GitHub. 

Dans le terminal, lancez :
```bash
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

### f. Voir ce que ça donne

Revenons sur votre site, on va voir ensemble ce que ça donne. Lancez la commande suivante :

```bash
hugo serve
```

</br>

Sur votre [navigateur internet de haute qualité](https://firefox.com), allez sur [https://localhost:1313](https://localhost:1313) (ou bien ce qui est indiqué après "Web Server is available at https://localhost:XXXX" dans le terminal) 

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
```bash
git submodule add https://github.com/{votre_thème} themes/{votre_thème}
# Par exemple : git submodule add https://github.com/hugo-toha/toha themes/toha
```

</br>
</br>

Ajouter un sous-module, avec la commande `git submodule add`, revient à ajouter un projet Git dans un même projet Git. Ainsi, pour chacune des mises à jour du créateur de votre thème, celles-ci se reflèteront sur votre propre site. Pratique non ?

</br>
</br>

### b bis. Alternativement : ajouter le thème manuellement

Il est aussi possible que certaines mises à jour puissent casser votre site. Personnellement ça ne m'est jamais arrivé, mais si vous voulez télécharger le thème depuis une version très spécifique, il vous suffit de télécharger le repository (depuis le bouton <kbd><i class="fa-solid fa-code"></i> Code</kbd> puis <kbd><i class="fa-regular fa-file-zipper"></i> Download ZIP</kbd>). Ensuite, il faut extraire le contenu de l'archive .zip dans le dossier `themes` puis dans un sous-dossier du même nom.

</br>
</br>

### c. Ajouter le thème à la configuration

---


🤖 *Cet article a été écrit <u>sans</u> l'aide d'une IA*

Bannière "Éléphants" générée par [DALL•E](https://labs.openai.com)