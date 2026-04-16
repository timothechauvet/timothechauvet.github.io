---
title: "Kubernetes mono-node avec ingress ☸️"
date: 2024-12-16
hero: k3s.webp
description: Comment j'ai fait pour avoir mon propre mono-Kubernetes
theme: Toha
---

Pour un ami, j'avais [codé un bot Telegram et conteneurisé l'appli avec Docker](../telegram-bot-python/). En réalité, c'était le début d'un long calvaire pour garder uniquement Docker et configurer un reverse proxy, car je suis trop mauvais pour ça.

À la place, **j'ai installé [k3s](https://k3s.io/) qui m'a permis d'avoir Kubernetes en single-node**, c'est-à-dire sur une seule VM. Kubernetes permet d'orchestrer les conteneurs, et donc de résoudre pas mal de mes soucis.

{{< vs 4 >}}

<p align="center">
  {{< img src="/posts/tutoriels/k3s/k3s-illu.webp" width="400" align="center" alt="Illustration rouge-orange d'un écran d'ordinateur avec le logo k3s devant" >}}
  <p style="text-align: center;"><i>Bannière "k3s" générée par Flux avec Perplexity</i></p>
</p>

{{< vs 4 >}}

## Comment s'y prendre

Ce tutoriel n'a rien de mirobolant. Mais j'ai fait ce tutoriel car k3s n'arrive pas avec toutes les fonctionnalités dont j'ai eu besoin. Voici comment faire.

1. Avoir une VM sous Linux. 2 CPU et 2Go de RAM minimum feront l'affaire
1. Avoir les ports *80* et *443* ouverts en TCP en inbound (NSG, firewall, iptables, ufw, en fonction de ce que vous avez) 
1. Installer [Docker](https://docs.docker.com/desktop/setup/install/linux/) (sauf si containerD vous suffit)
1. Installer k3s : `curl -sfL https://get.k3s.io | sh -`
1. Activer k3s au prochain redémarrage avec `sudo systemctl enable k3s`
1. Ajouter la ligne `export KUBECONFIG=/etc/rancher/k3s/k3s.yaml` à votre fichier "rc" (.bashrc, .zshrc, que sais-je)
    - Note : en cas de problème, vous pouvez agrandir les permissions du fichier k3s avec `sudo chmod 644 /etc/rancher/k3s/k3s.yaml` (pas recommandé)
1. Installer ingress-nginx pour vos ressources ingress (ici si vous passez par un cloud provider) `kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.11.3/deploy/static/provider/cloud/deploy.yaml`
1. Installer cert-manager pour activer HTTPS par la suite avec `kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.16.1/cert-manager.yaml`
1. Installer MetalLB pour créer des LoadBalancer/Ingresses avec `kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.14.8/config/manifests/metallb-native.yaml`

{{< vs 2 >}}

{{< alert type="info" >}}
Pour tous les fichiers `.yaml` qui suivent, vous pourrez les appliquer avec la commande `kubectl apply -f monfichier.yaml`
{{< /alert >}}

{{< vs 4 >}}

### Configurer MetalLB 

MetalLB doit pouvoir créer des LoadBalancer grâce à une liste d'IPs disponibles. Pour déterminer quelle rangée utiliser :
- lancer `hostname -I`
- prendre une liste d'adresses qui ne touche pas les IPs listées.

Créer et appliquer le fichier `metallb.yaml` :

```yaml
apiVersion: metallb.io/v1beta1
kind: IPAddressPool
metadata:
  name: first-pool
  namespace: metallb-system
spec:
  addresses:
  - 10.0.0.240-10.0.0.250 # à adapter selon votre réseau

---
apiVersion: metallb.io/v1beta1
kind: L2Advertisement
metadata:
  name: l2-advert
  namespace: metallb-system
spec:
  ipAddressPools:
  - first-pool
```

{{< vs 4 >}}

### Configurer Let's Encrypt

Let's Encrypt doit pouvoir certifier que vos noms de domaines vous appartiennent. Créer un fichier `clusterissuer-letsencrypt.yaml`

```yaml
apiVersion: cert-manager.io/v1
kind: ClusterIssuer
metadata:
  name: letsencrypt-prod
spec:
  acme:
    server: https://acme-v02.api.letsencrypt.org/directory
    email: timothe@chauvet.cloud # à remplacer
    privateKeySecretRef:
      name: letsencrypt-prod
    solvers:
    - http01:
        ingress:
          class: traefik # remplacez si vous voulez nginx
```

{{< vs 2 >}}

Comme ça, si vous avez besoin d'un certificat, vous pourrez en créer un avec un fichier `certificate-yourls.yaml` par exemple : 

```yaml
apiVersion: cert-manager.io/v1
kind: Certificate
metadata:
  name: yourls-tls
  namespace: default
spec:
  secretName: yourls-tls
  issuerRef:
    name: letsencrypt-prod
    kind: ClusterIssuer
  dnsNames:
  - tmth.fr
```

{{< vs 4 >}}

## Héberger mes applis

### Mon bot Telegram

Pour mon bot Telegram, c'était plutôt simple car il n'a pas besoin de recevoir des requêtes HTTP. Un simple déploiement a suffi

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: telebot
spec:
  replicas: 1
  selector:
    matchLabels:
      app: telebot
  template:
    metadata:
      labels:
        app: telebot
    spec:
      restartPolicy: Always
      containers:
      - name: telebot
        image: ghcr.io/timothechauvet/telebot:latest
        env:
        - name: TELEGRAM_BOT_TOKEN
          valueFrom:
            secretKeyRef:
              name: telebot-secrets
              key: TELEGRAM_BOT_TOKEN
      imagePullSecrets:
      - name: ghcr-secret
```
{{< vs 4 >}}

### Une appli HTTP

J'avais envie d'un racourcisseur d'URLs. Vous l'avez d'ailleurs sûrement inauguré en cliquant sur [tmth.fr/k3s](https://tmth.fr/k3s) pour venir ici ! [Yourls](https://yourls.org/), que j'ai utilisé pour ça, a besoin d'une base MySQL. C'est ce qui a été le plus compliqué à mettre en place pour moi. 

Pour Yourls, j'ai eu besoin de 
- Un ConfigMap avec config.php
- Un Certificate pour mon domaine tmth.fr
- Un Ingress Traefik (je préfère ça car Nginx est fait pour les gens intelligents) 😭
- Le Service pour mapper avec l'Ingress
- Un PersistentVolume et son claim pour la base MySQL
- Le déploiement, bien évidemment.

Je pense que le plus important ici est l'Ingress qui crée les règles lorsqu'une requête vers tmth.fr est reçue

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: yourls-ingress
  annotations:
    cert-manager.io/cluster-issuer: "letsencrypt-prod"
    traefik.ingress.kubernetes.io/router.entrypoints: "web,websecure"
spec:
  ingressClassName: traefik
  tls:
  - hosts:
    - tmth.fr
    secretName: yourls-tls
  rules:
  - host: tmth.fr
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: yourls
            port: 
              number: 80
```

{{< vs 2 >}}

Je ne vais évidemment pas décrire ici TOUTES mes configurations.

{{< img src="https://risibank.fr/cache/medias/0/23/2399/239915/full.gif" height="200" align="left" title="Cependant !!" >}}

{{< vs 2 >}}

**Cependant !!**

J'ai pris la peine de publier mes configurations que vous pouvez [retrouver sur mon GitHub](https://github.com/timothechauvet/mes-yaml-de-kube/). Donc n'hésitez pas à les prendre comme exemple !

---

Si vous avez des questions ou des suggestions, n'hésitez pas à me contacter par [mail](mailto:timothe@chauvet.cloud), sur [LinkedIn](https://www.linkedin.com/in/timothechauvet/) ou directement en envoyant [une *issue* sur GitHub](https://github.com/timothechauvet/timothechauvet.github.io/issues)