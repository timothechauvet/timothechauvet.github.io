---
title: "Découverte des Cloudflare Workers"
date: 2025-04-21
hero: email.webp
description: J'ai développé une petite appli web avec les Cloudflare Workers qui récupère les codes reçus par mail
theme: Toha
---

J'ai développé une petite appli web qui récupère les codes temporaires que je reçois par mail. Une idée bien loufoque si l'objectif est que n'importe qui puisse se connecter à ses comptes... Et c'était bien là mon objectif.

J'avais déjà [intégré mes domaines à Cloudflare pour qu'il récupère tous les mails reçus](../catch-all). Je pensais avoir fait le tour des fonctionnalités gratuites de la bête. 

Il semblerait que non.

Dans cet article je vous présente comment j'ai utilisé les Cloudflare Workers pour créer une appli qui récupère les codes temporaires envoyés par mail et 2FA, pour les exposer sur un site tout simple.

Cet article assume que vous avez déjà intégré votre domaine sur Cloudflare.

{{< vs 4 >}}

<p align="center">
  {{< img src="/posts/tutoriels/cloudflare-email/schema.webp" width="700" align="center" alt="Schéma du service avec Cloudflare Email Routing allant vers les Workers qui enregistre le code 2FA dans KV puis l'expose sur Pages" >}}
  <p style="text-align: center;"><i>Schéma de mon application</i></p>
</p>

{{< vs 4 >}}

## Cloudflare Workers

Avec l'onglet Email / Email routing, j'ai découvert qu'il y avait cette option "Email Workers (beta)" à côté des "Routing rules" que j'utilisais jusqu'ici [pour mon catch-all](../catch-all). J'ai cliqué sur Create pour voir ce que ça donne, **et c'est là que j'ai découvert les Workers**. 

C'est en gros l'offre type "functions" ou "serverless" de Cloudflare. Avec du code en JavaScript, on a la possibilité de programmer toute une logique dès qu'on reçoit un événement déclencheur : la réception d'un mail.

Une fois le Worker de base créé, j'ai pu l'associer à mes mails reçus. En revenant dans "Routing rules" puis "custom addresses", je programme les mails reçus à mon-compte@whitemail.fr. Je mets l'action sur "Send to a Worker" en ciblant le Worker que je viens de créer. Plus qu'à développer mon appli

{{< vs 4 >}}

<p align="center">
  {{< img src="/posts/tutoriels/cloudflare-email/send-to-worker.webp" width="700" align="center" alt="Capture d'écran avec les infos remplies pour renvoyer les mails reçus depuis example@timothechau.vet vers un Worker" >}}
  <p style="text-align: center;"><i>Les mails reçus à cette adresse seront renvoyés au Worker</i></p>
</p>

{{< vs 4 >}}

## Récupérer les codes reçus par mail

Pour ce projet, j'avais besoin de mettre à disposition plusieurs informations dynamiques :

1. Le code temporaire que je reçois par mail
2. Le code TOTP qui expire toutes les 30 secondes

Vu que mon Worker est déclenché à la réception d'un mail, ce mail peut être analysé en argument.

Pour le développer, je vais sur mon Worker et je peux le programmer avec le bouton </>

```js
export default {
  // Donc là je reçois le mail, et je peux l'analyser
  async email(message, env, ctx) {
    // Je récupère ici l'objet du mail
    const subject = message.headers.get("subject");
    // Si ça matche, je lance mon programme
    if (
      message.to === "example@timothechau.vet" &&
      message.from === "noreply@linkedin.com" &&
      subject.startsWith("Your code is ")
    ) {
      // Regex pour récupérer le code dans le titre
      const match = subject.match(/\d{6}/);
      if (match) {
        // J'en parle juste après
      } 
    }
    
    // Et là je le renvoie à mon mail perso
    await message.forward(env.MAIL_FORWARD);
  }
}
```

{{< vs 4 >}}

Il faut cliquer sur "Deploy" pour que ce soit en ligne.

Le truc avec mon code c'est que j'ai besoin qu'il soit sauvegardé quelque-part pour pouvoir le distribuer. J'ai utilisé la base KV de Cloudflare pour ça. Il suffit juste de créer un "namespace" et c'est bon.

{{< vs 4 >}}

<p align="center">
  {{< img src="/posts/tutoriels/cloudflare-email/kv.webp" width="800" align="center" alt="Capture d'écran de la base kv sur Cloudflare" >}}
  <p style="text-align: center;"><i>Les codes seront sauvegardés ici</i></p>
</p>

{{< vs 4 >}}

Ensuite je reviens dans Worker : Workers & Pages / mon worker / Setting. Sous "Bindings", je crée une connexion à ma base KV, que je nomme "BINDING_KV". Enfin, dans mon code, je peux sauvegarder la valeur reçue grâce à ça : 

```js
if (match) {
  // match[0] correspond au regex qui matche avec le code à 6 chiffres
  await env.BINDING_KV.put("temp_code", match[0]);
} 
```

{{< vs 4 >}}

## Générer des codes à 6 chiffres TOTP

Pour récupérer les fameux codes temporaires "One-Time Passwords" à 6 chiffres qui changent toutes les 30 secondes, je me suis pas trop embêté. J'ai trouvé [cet exemple en JavaScript de turistu sur GitHub](https://github.com/turistu/totp-in-javascript/blob/main/totp.js)

```js
// Cette partie que j'ai allègrement pompé d'internet
async function generateTOTP(key, secs = 30, digits = 6){
  return hotp(unbase32(key), pack64bu(Date.now() / 1000 / secs), digits);
}
async function hotp(key, counter, digits){
  let y = self.crypto.subtle;
  if(!y) throw Error('no self.crypto.subtle object available');
  let k = await y.importKey('raw', key, {name: 'HMAC', hash: 'SHA-1'}, false, ['sign']);
  return hotp_truncate(await y.sign('HMAC', k, counter), digits);
}
function hotp_truncate(buf, digits){
  let a = new Uint8Array(buf), i = a[19] & 0xf;
  return fmt(10, digits, ((a[i]&0x7f)<<24 | a[i+1]<<16 | a[i+2]<<8 | a[i+3]) % 10**digits);
}
function fmt(base, width, num){
  return num.toString(base).padStart(width, '0')
}
function unbase32(s){
  let t = (s.toLowerCase().match(/\S/g)||[]).map(c => {
    let i = 'abcdefghijklmnopqrstuvwxyz234567'.indexOf(c);
    if(i < 0) throw Error(`bad char '${c}' in key`);
    return fmt(2, 5, i);
  }).join('');
  if(t.length < 8) throw Error('key too short');
  return new Uint8Array(t.match(/.{8}/g).map(d => parseInt(d, 2)));
}
function pack64bu(v){
  let b = new ArrayBuffer(8), d = new DataView(b);
  d.setUint32(0, v / 2**32);
  d.setUint32(4, v);
  return b;
}
```

{{< vs 4 >}}

Pour l'utiliser, il faut l'appeler avec le token 2FA généré. Si n'avez à disposition qu'un QR code, vous devez le décoder et récupérer la partie dans "secret=XXXX".

```js
const codeTotp = await generateTOTP("ABCDEFGHIJKL0987654321MNOPQRSTUVWXYZ");
```

{{< vs 4 >}}

## Exposer ces codes sur un site

Maintenant il me faut exposer ça sur une page web. Pour ça, pas besoin de changer de Worker. J'utilise la même en rajoutant ça 

{{< vs 4 >}}

```js
export default {
  async email(message, env, ctx) {
    // ...
  },

  // fetch() est utilisé pour chaque requête faite via le web
  async fetch(request, env, ctx) {
    // Je récupère la valeur de "temp_code" 
    const codeMail = await env.BINDING_KV.get("temp_code");
    // Je récupère la valeur du TOTP
    const codeTotp = await generateTOTP(env.TOKEN_OTP);

    // Je construis le résultat
    const response = `Code reçu par mail: ${codeMail}\n\n Code 2FA (valable 30s):\n\n${codeTotp}`

    return new Response(response || "Erreur dans la construction de la réponse");
  },
};
```

{{< vs 4 >}}

## Variables d'environnement

Ça fait plusieurs fois que je mentionne des valeurs `env.QUELQUECHOSE`. Petit récap de ce qu'on a pour l'instant : 

- `env.MAIL_FORWARD` : mon mail personnel, sauvegardé dans les variables d'environnement
- `env.BINDING_KV` : la connexion à la base Cloudflare KV, déjà provisionnée
- `env.TOKEN_OTP` : Le token servant à générer un code 2FA

Hormis BINDING_KV qui est déjà implémenté, il reste les deux autres valeurs à compléter en tant que variables d'environnement.

Pour ça je reviens dans mon Worker puis dans Settings. 

Dans "Variables and Secrets" je peux facilement rajouter mes valeurs. Qu'elles soient en "Secret" ou en "Texte" ça revient à la même chose, du coup j'ai mis les deux en mode secret (C'est chiffré et invisible sur le dashboard après sauvegarde). Il faut cliquer sur "Deploy" quand c'est bon.

{{< vs 4 >}}

<p align="center">
  {{< img src="/posts/tutoriels/cloudflare-email/environment-variables.webp" width="700" align="center" alt="Capture d'écran pour rentrer une variable d'environnement" >}}
  <p style="text-align: center;"><i>Les variables d'environnement peuvent facilement être modifiées</i></p>
</p>

{{< vs 4 >}}

## Gérer qui peut y accéder 

Je voudrais que tout le monde ne puisse pas accéder à ces codes (évidemment). 

Je veux faire simple : envoyer des liens uniques aux personnes. Je veux aussi pouvoir leur retirer facilement l'accès

Pour ça, je vais déployer une autre variable d'environnement, cette fois en JSON.

- Type : JSON
- Variable name : AUTHORIZED_URLS
- Value : `["a1b2c3d4","z9y8x7w6"]`

Comme ça, j'ai juste à retirer le code que je ne veux plus fonctionnel.

Pour l'implémenter dans le code, je récupère l'URL accédé et ce qu'il y après le domaine (www.monsite.fr/**lecode**) pour vérifier que ce soit bien dans ma liste.

{{< vs 4 >}}

```js
export default {
  async email(message, env, ctx) {
    // ...
  },

  // fetch() est utilisé pour chaque requête faite via le web
  async fetch(request, env, ctx) {
    // Je récupère l'URL et le divise entre les caractères '/'
    const url = new URL(request.url);
    const pathSegments = url.pathname.split('/').filter(Boolean);

    // Je check si la valeur après le '/' est dans la liste
    if (env.AUTHORIZED_URLS.includes(pathSegments[0])) {
      // Le code d'avant
      // ...
      return new Response(response || "Erreur dans la construction de la réponse");
    }

    return new Response("Unauthorized", { status: 401 });
  },
};
```

{{< vs 4 >}}

Je peux aussi, en faisant un `else if` sur d'autres listes, avoir 1 seul Worker pour plusieurs comptes et renvoyer une réponse différente à chaque fois

{{< vs 4 >}}

## Domaine custom

Cloudflare gère ça assez facilement surtout que votre domaine est déjà dans Cloudflare.

Je reviens dans mon Worker puis dans Settings. L'option "Domains & Routes" me permet de configurer mon domaine personnalisé.

Je fais "Custom domain", je rajoute le domaine "code.timothechau.vet" et c'est bon

{{< vs 4 >}}

<p align="center">
  {{< img src="/posts/tutoriels/cloudflare-email/custom-domain.webp" width="700" align="center" alt="Capture d'écran pour rajouter un domaine personnalisé" >}}
  <p style="text-align: center;"><i>Mon domaine timothechau.vet est déjà dans Cloudflare. Sinon il aurait fallu le rajouter manuellement</i></p>
</p>

{{< vs 4 >}}


## tl;dr

Les étapes étaient donc

1. Ajouter son domaine sur Cloudflare
2. Créer un Worker vide
3. Ajouter une règle dans mon domaine / Email / Email routing / Routing rules / Send to Worker
4. Créer une base key-value vide dans Storage & Databases / KV / Create
5. Rattacher la base KV dans le Worker / Settings / Bindings
6. Créer les variables d'environnement `MAIL_FORWARD`, `TOKEN_OTP`, `AUTHORIZED_URLS` (json)
7. Déployer le code en dessous
8. Rattacher un domaine `code.mondomaine.com` dans le Worker / Settings / Domains & Routes

Voici un récap du code. [Je l'ai aussi mis dans ce repo](https://github.com/timothechauvet/workers-code-cloudflare/blob/main/worker.js)

```js
export default {
  async email(message, env, ctx) {
    // Je récupère ici l'objet du mail
    const subject = message.headers.get("subject");
    // Si ça matche, je lance mon programme
    if (
      message.to === "example@timothechau.vet" &&
      subject.startsWith("Your code is ")
    ) {
      // Regex pour récupérer le code dans le titre
      const match = subject.match(/\d{6}/);
      if (match) {
        await env.BINDING_KV.put("temp_code", match[0]);
      } 
    }
    
    // Et là je le renvoie à mon mail perso
    await message.forward(env.MAIL_FORWARD);
  },

  // fetch() est utilisé pour chaque requête faite via le web
  async fetch(request, env, ctx) {
    // Je récupère l'URL et le divise entre les caractères '/'
    const url = new URL(request.url);
    const pathSegments = url.pathname.split('/').filter(Boolean);

    // Je check si la valeur après le '/' est dans la liste
    if (env.AUTHORIZED_URLS.includes(pathSegments[0])) {
      // Je récupère la valeur de "temp_code" 
      const codeMail = await env.BINDING_KV.get("temp_code");
      // Je récupère la valeur du TOTP
      const codeTotp = await generateTOTP(env.TOKEN_OTP);

      // Je construis le résultat
      const response = `Code reçu par mail: ${codeMail}\n\n Code 2FA (valable 30s):\n\n${codeTotp}`
      return new Response(response || "Erreur dans la construction de la réponse");
    }

    return new Response("Unauthorized", { status: 401 });
  },
};

// Code pompé d'internet
async function generateTOTP(key, secs = 30, digits = 6){
  return hotp(unbase32(key), pack64bu(Date.now() / 1000 / secs), digits);
}
async function hotp(key, counter, digits){
  let y = self.crypto.subtle;
  if(!y) throw Error('no self.crypto.subtle object available');
  let k = await y.importKey('raw', key, {name: 'HMAC', hash: 'SHA-1'}, false, ['sign']);
  return hotp_truncate(await y.sign('HMAC', k, counter), digits);
}
function hotp_truncate(buf, digits){
  let a = new Uint8Array(buf), i = a[19] & 0xf;
  return fmt(10, digits, ((a[i]&0x7f)<<24 | a[i+1]<<16 | a[i+2]<<8 | a[i+3]) % 10**digits);
}
function fmt(base, width, num){
  return num.toString(base).padStart(width, '0')
}
function unbase32(s){
  let t = (s.toLowerCase().match(/\S/g)||[]).map(c => {
    let i = 'abcdefghijklmnopqrstuvwxyz234567'.indexOf(c);
    if(i < 0) throw Error(`bad char '${c}' in key`);
    return fmt(2, 5, i);
  }).join('');
  if(t.length < 8) throw Error('key too short');
  return new Uint8Array(t.match(/.{8}/g).map(d => parseInt(d, 2)));
}
function pack64bu(v){
  let b = new ArrayBuffer(8), d = new DataView(b);
  d.setUint32(0, v / 2**32);
  d.setUint32(4, v);
  return b;
}
```

---

Si vous avez des questions ou des suggestions, n'hésitez pas à me contacter par [mail](mailto:timothe@chauvet.cloud), sur [LinkedIn](https://www.linkedin.com/in/timothechauvet/) ou directement en envoyant [une *issue* sur GitHub](https://github.com/timothechauvet/timothechauvet.github.io/issues)