---
title: "La stack moderne de Facebook.com 📚"
date: 2024-02-04
hero: facebook.webp
description: Facebook a renoncé aux technos traditionnelles pour faire sa stack maison
theme: Toha
tags: ["Traduit", "Stack", "Facebook"]
---

J'adorais Facebook pendant mon enfance. Aujourd'hui élu pire réseau social par ma génération, il n'en reste pas moins le réseau social le plus populaire au monde. Avec ses 2 milliards d'utilisateurs actifs **quotidiennement**, Facebook n'est "pas mort, ni mourrant" selon son directeur en mars 2023.

Créé le 4 février 2004 par Mark Zuckerberg, "Zucc" pour les intimes, Facebook a fait ses débuts sur un monolithe PHP codé en dehors des heures de cours de son créateur. N'ayant pu anticiper son succès, le code a été réadapté et l'infrastructure modernisée. 

Aujourd'hui, Facebook (le réseau social) fête ses 20 bougies, et j'ai voulu faire un hommage à ce grand site de mon époque que j'ai quand même décidé de quitter en 2022 à la fin de mes études. Un condensé de sa stack technique sur laquelle Facebook communique dans son blog *[Engineering at Meta](https://engineering.fb.com/)*, et que j'ai décidé de tout vous condenser dans ce petit article qui m'a passionné.

</br>
</br>

# Le frontend développé maison

Si **ReactJS** est utilisé par tant de monde, c'est que tout le monde veut devenir Facebook. Les équipes Facebook ont lancé en 
2013 le framework JS le plus *trendy* [malgré son déclin](https://insights.stackoverflow.com/trends?tags=reactjs%2Cvue.js%2Cangular%2Csvelte%2Cangularjs%2Cvuejs3). C'est aujourd'hui le framework utilisé par [le nouveau site et les applis de Facebook](https://engineering.fb.com/2023/02/06/ios/facebook-ios-app-architecture/).

**GraphQL** est ce qu'ils utilisent pour leurs requêtes API. Pareil que pour React, GraphQL a été développé en 2015 par Facebook et est aujourd'hui open source. Le problème des APIs traditionnelles et qu'elles renvoient trop de données par rapport à ce qui est demandé, ce que résouds notamment GraphQL.

Enfin, [**Relay**, dont on entend moins parler](https://developers.facebook.com/videos/2019/building-the-new-facebookcom-with-react-graphql-and-relay/), gère les données dans React. L'idée derrière ce framework est que les données ont besoin d'être hiérarchisées et que les composants dans React ont besoin de données spécifiques. Relay interroge alors GraphQL uniquement sur ce qu'il l'intéresse, en prenant soin de prioriser le contenu important.

</br>
</br>

# Le backend qui traîne des casseroles

Je me souviens d'une époque où Facebook était régulièrement indisponible, et que je ne pouvais pas poster mes blagues médiocres à mes potes d'internet. Je crois qu'aujourd'hui ce problème est définitivement résolu, et je comprends pourquoi.

Bien que n'ayant pas décidé d'implémenter Kubernetes, Facebook a pris le choix d'utiliser [un orchestrateur maison, **Twine**](https://engineering.fb.com/2019/06/06/data-center-engineering/twine/), dont on ne sait pas grand chose tant il est closed source. C'est le nouveau nom de Tupperware, qui gère les workloads et containers de Facebook [depuis la décennie passée](https://engineering.fb.com/2020/11/11/data-center-engineering/twine-2/).

Côté langage de programmation, *Zucc* était parti sur du PHP. Puis, en 2014, Facebook a développé [**Hack**](https://engineering.fb.com/2014/03/20/core/hack-a-new-programming-language-for-hhvm/), un langage de programmation orienté objet qui compile en PHP. Accompagné d'un serveur HHVM (HipHop Virtual Machine), Hack supporte l'entièreté de sa base de code PHP et résouds pas mal de soucis de sécurité.

[J'ai ouï dire](https://engineering.fb.com/2019/03/14/data-center-engineering/f16-minipack/) que **CentOS** était utilisé pour les serveurs bien que la distro soit morte. Ils ont développé en parallèle [**FBOSS**](https://engineering.fb.com/2015/03/10/data-center-engineering/facebook-open-switching-system-fboss-and-wedge-in-the-open/), un OS pour leurs switchs réseau.

</br>
</br>

# Du hardware maison, aussi

Je n'y comprends pas grand chose au hardware donc je ne vais pas m'étaler dessus, mais Facebook développe [son propre matos depuis longtemps](https://engineering.fb.com/category/data-center-engineering/). Je me rappelle en 2013 qu'ils avaient essayé de lancer un téléphone, le HTC First, mais c'était en [collaboration avec HTC](https://www.businessinsider.com/htc-first-and-facebook-home-review-2013-4?r=US&IR=T).

</br>
</br>

# Côté base de données, on reste sur des classiques

Comme prévu, la plupart des bases de données tournent sous **MySQL**. Ils ont récemment procédé à une [migration vers la release 8.0](https://engineering.fb.com/2021/07/22/data-infrastructure/mysql/), ce qui n'était pas une mince chose à faire.

Mais Facebook a aussi été à l'origine de **Cassandra**, en 2008 utilisé de base pour [la messagerie instantannée](https://engineering.fb.com/2010/11/15/core-infra/the-underlying-technology-of-messages/), qu'ils ont revisité 10 ans plus tard avec [**Rocksandra**](https://instagram-engineering.com/open-sourcing-a-10x-reduction-in-apache-cassandra-tail-latency-d64f86b43589), lui aussi open-source.

</br>
</br>

# Une grosse infra pour des grosses données

Pour la big data, ils ont réécrit une partie du code de MySQL [pour créer **MyRocks**](https://engineering.fb.com/2016/08/31/core-infra/myrocks-a-space-and-write-optimized-mysql-database/), servant à stocker des données optimisant l'espace et l'écriture dans la base maison (et toujours open source) [**RocksDB**](https://engineering.fb.com/2013/11/21/core-infra/under-the-hood-building-and-open-sourcing-rocksdb/) écrit en C++ et basé sur LevelDB de Google.

Facebook est aussi très fier de son [moteur **Scribe**](https://engineering.fb.com/2019/10/07/data-infrastructure/scribe/), le gestionnaire de files d'attentes de messages maison. 

**Apache Spark** est par ailleurs utilisé par les équipes de Machine Learning [pour l'entraînement](https://engineering.fb.com/2017/02/07/core-infra/using-apache-spark-for-large-scale-language-model-training/). Ils revenaient de Hive, [apparemment trop lent](https://engineering.fb.com/2016/08/31/core-infra/apache-spark-scale-a-60-tb-production-use-case/) (j'y connais rien à ça pour avoir un avis).

Enfin, **Presto**, [sorti en 2012](https://engineering.fb.com/2013/11/06/core-infra/presto-interacting-with-petabytes-of-data-at-facebook/), a pour objectif d'optimiser les requêtes SQL pour les gros volumes de données (Hive, HBase, Scribe...).  

</br>
</br>

# Côté automatisation et DevOps, des technos inconnues au bataillon

Si certains des services mentionnés précédemment ne vous disent rien, ceux qui vont suivre sont encore plus obscurs. Chez Facebook, on utilise [**Sapling** pour Git](https://engineering.fb.com/2022/11/15/open-source/sapling-source-control-scalable/), qui leur permet d'avoir un serveur Git scalable pour leurs grosses bases de code. Ils auront pris une dizaine d'année pour développer l'outil `sl` en interne, et l'ont open source en 2022.

Je ne sais pas ce qu'utilise Facebook pour ses pipelines d'intégration et de déploiement CI/CD, mais [ils ont développé **Buck2**](https://engineering.fb.com/2023/04/06/open-source/buck2-open-source-large-scale-build-system/), un système pour build leur énorme base de code codé en Rust. 

Pour leurs tests, ils ont développé [**Infer**](https://fbinfer.com/) pour leurs applis mobiles (C, C++, Objective-C, Java) et [**Sapienz**](https://engineering.fb.com/2018/05/02/developer-tools/sapienz-intelligent-automated-software-testing-at-scale/) pour leurs user stories.

Niveau alerting, **SLICK** leur permet de monitorer [les SLI de leurs services](https://engineering.fb.com/2021/12/13/production-engineering/slick/) (SLI-CK, get it ?). Pratique pour atteindre un objectif de 100% d'uptime. Malheureusement on n'a pas tant d'informations sur ce système qui reste closed source.

Enfin, **Docusaurus** est leur outil de documentation, avec une mascote plutôt adorable de crocodile. Le résultat est très joli, et [ils ont open source le projet](https://docusaurus.io/).

# Conclusion et surprises

J'ai été étonné de voir que Facebook a développé autant de technos en interne, et que la plupart sont open source. C'est une bonne chose pour la communauté dev & ops, et montre que le réseau social le moins apprécié a quand même de longs jours devant lui. 

En 20 ans, Facebook a mis la scalabilité au cœur de ses préoccupations, et a plutôt réussi la transition de son monolithe PHP à une stack moderne. Ils ont accompli l'exploit de satisfaire les besoins de 2 milliards d'utilisateurs quotidiens en 2024 en partant de rien, tout à leur honneur. Quels seront les prochains défis du réseau social pour ne pas perdre, voire gagner, en popularité ?

---

Bannière "Facebook" générée par [DALL•E](https://labs.openai.com)