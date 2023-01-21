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

LinkedIn, Meta et Twitter veulent leur peau 👀. Pour créer son contenu à l'ère des 20's (à partir de 2020), il existe pléthore de plateformes. Parfois naissantes (Mastodon, Notion), parfois éphémères (Google +, Skyblog), parfois boudées (Facebook, Twitter), celles-ci ont une vie parfois plus courte que celle de votre machine à laver. <u>Mais il existe bien une plateforme qui ne subit pas l'obsolescence programmée : les blogs.</u>

Dans le premier article de mon super blog, je voulais vous raconter comment j'ai réalisé... mon blog. Hébergé par GitHub, produit par Hugo et alimenté par moi-même, les coûts me reviennent à 0€, enfin presque. J'espère qu'en lisant ce tutoriel, vous pourrez vous rendre compte à quel point il est facile de, vous aussi, faire votre https://timothechau.vet à moindre frais.

## Le problème avec WordPress pour faire son blog 😓

Je ne dois pas être le seul à trouver que faire un site WordPress, c'est la loose. Même si 43.1% de tous les sites internet au monde utilisent WordPress ([W3techs, janvier 2023](https://w3techs.com/technologies/details/cm-wordpress)), gérer un site dynamique et performant demande beaucoup d'investissement. Certaines de ces complications incluent :
- l'hébergement OVH à 50€ par an minimum (pour un service décent) 💸
- les crashs sans raison
- la panique quotidienne des 200 tentatives de connexion
- la veille permanente des extensions à jour pour minimiser les failles
- la configuration d'un CDN pour avoir un site *responsive* partout dans le monde (parfois + cher que l'hébergement)
- la gestion du SEO pour monter dans les résultats sur Google
- ...

En réalité, WordPress est un très bon [CMS](https://fr.wikipedia.org/wiki/Syst%C3%A8me_de_gestion_de_contenu) pour avoir un beau site dynamique. Puissant, complet, open-source, né de la génération Z (en 2003 quand même), WordPress n'a plus à faire ses preuves. Mais pour faire un blog perso, WordPress est vraiment, vraiment trop poussé. Un site internet statique, n'ayant pas toutes les fonctionnalités dynamiques de WordPress, est souvent largement suffisant pour faire son blog. Et ça tombe bien, c'est justement un site internet statique que vous avez devant vos yeux (ébahis).

## Le meilleur des blogs, c'est avec Hugo

En 2013, quand Hugo Chávez nous quittait, Hugo (le logiciel, pas le président du Venezuela) venait de naître. Ce générateur de site statique écrit en Go, le langage ultra-rapide de Google, est toujours connu comme le ["framework pour construire des sites le plus rapide du monde"](https://gohugo.io/). Sans rentrer dans les détails, il permet au plus grand nombre de configurer et lancer son site. Open-source, gratuit et facile d'utilisation, il est conçu pour qu'on le configure une fois et qu'on l'oublie ensuite.

<p align="center">
  <img src="hugo-mascotte.webp" alt="Mascotte de Hugo" width="150"/>
  <p style="text-align: center;"><i>Go et Hugo ensemble, ça donne <a href="https://gohugo.io">Super-Hugo</a></i></p>
</p>

---

🤖 *Cet article a été écrit <u>sans</u> l'aide d'une IA*

Bannière "Éléphants" générée par [DALL•E](https://labs.openai.com)