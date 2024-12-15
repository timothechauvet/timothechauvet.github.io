---
title: "Kubernetes mono-node avec reverse proxy ☸️"
date: 2024-12-14
hero: catch-all.webp
description: La manière dont je me suis pris pour avoir mon propre Kubernetes
theme: Toha
---

Pour un ami, j'avais [codé un bot Telegram et conteneurisé l'appli avec Docker](../telegram-bot-python/). En réalité, c'était le début d'un long calvaire pour garder uniquement Docker et configurer un reverse proxy, car je suis trop mauvais pour ça.

À la place, **j'ai installé k3s qui m'a permis d'avoir Kubernetes en single-node**, c'est-à-dire sur une seule VM. Kubernetes permet d'orchestrer les conteneurs, et donc de résoudre pas mal de mes soucis.

{{< vs 4>}}

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
1. Installer MetalLB pour créer des LoadBalancer avec `kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.14.8/config/manifests/metallb-native.yaml`

{{< vs 2>}}

{{< alert type="info" >}}
Pour tous les fichiers `.yaml` qui suivent, vous pourrez les appliquer avec la commande `kubectl apply -f monfichier.yaml`
{{< /alert >}}

{{< vs 4>}}

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

{{< vs 4>}}

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
          class: traefik
```

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
        - name: HASHED_PASSWORD
          valueFrom:
            secretKeyRef:
              name: telebot-secrets
              key: HASHED_PASSWORD
      imagePullSecrets:
      - name: ghcr-secret
```

Je ne vais évidemment pas vous envoyer mes secrets en clair.


{{< img src="https://risibank.fr/cache/medias/0/23/2399/239915/full.gif" height="200" align="left" title="Néanmoins !!" >}}

**Néanmoins !!**

J'ai pris la peine de publier la configuration de mes secrets que vous pouvez [retrouver sur mon GitHub](https://github.com/timothechauvet/mes-yaml-de-kube/tree/main/secrets). Donc n'hésitez pas à les prendre comme exemple si jamais !

### Une appli HTTP





---

Si vous avez des questions ou des suggestions, n'hésitez pas à me contacter par [mail](mailto:timothe@chauvet.cloud), sur [LinkedIn](https://www.linkedin.com/in/timothechauvet/) ou directement en envoyant [une *issue* sur GitHub](https://github.com/timothechauvet/timothechauvet.github.io/issues)