---
title: "Discovering Cloudflare Workers"
date: 2025-04-13
hero: email.webp
description: I developed a small web app with Cloudflare Workers that fetches codes received by email
theme: Toha
---

I developed a small web app that retrieves temporary codes I receive by email. A rather odd idea if the goal is for anyone to be able to log into my accounts... Which was precisely my goal.

I had already [integrated my domains into Cloudflare to catch all received emails](../catch-all). I thought I had explored all the free features of the beast.

Apparently not.

In this blog post I wrote down how I used Cloudflare Workers to create an app that fetches temporary codes sent by mail, and also 2nd-factor Authentication (2FA) ones, and expose them on a simple website.

{{< vs 4 >}}

<p align="center">
  {{< img src="/posts/tutoriels/cloudflare-email/schema.webp" width="500" align="center" alt="Service diagram with Cloudflare Email Routing going to Workers, which registers the 2FA code in KV and then exposes it on Pages." >}}
  <p style="text-align: center;"><i>My app's diagram</i></p>
</p>

{{< vs 4 >}}

## Cloudflare Workers

Using the Email / Email routing tab, I discovered there was this "Email Workers (beta)" option next to the "Routing rules" I had been using for my [catch-all setup](../catch-all). I clicked Create to see what it was about, **and that's where I discovered Workers**.

It's basically Cloudflare's "functions" or "serverless" service. Using JavaScript code, you can program a logic triggered upon receiving an event: an incoming email.

Once the basic Worker is created, I linked it to my incoming emails. Going back to "Routing rules" from the "Emails", then "custom addresses", I set up thoses sent to my-account@whitemail.fr`, with the action set to "Send to a Worker", targeting the Worker I just created. Then I need to develop my app.

{{< vs 4 >}}

<p align="center">
  {{< img src="/posts/tutoriels/cloudflare-email/send-to-worker.webp" width="600" align="center" alt="Screenshot of the informations filled to trigger mails sent to example@timothechau.vet to a Worker" >}}
  <p style="text-align: center;"><i>Emails received at this address will be forwarded to the Worker</i></p>
</p>

{{< vs 4 >}}

## Retrieving codes received by email

For this project, I want to disclose these information:

1. The temporary code I receive by email.
2. The TOTP code that expires every 30 seconds.

Since my Worker is triggered upon receiving an email, this email can be parsed as an argument.

To develop it, I go to my Worker and can program it using the  button.

```
export default {
  // So here I receive the email, and I can parse it
  async email(message, env, ctx) {
    // Here I get the email subject
    const subject = message.headers.get("subject");
    // If it matches, I run my program
    if (
      message.to === "example@timothechau.vet" &&
      subject.startsWith("Your code is ")
    ) {
      // Regex to extract the code from the subject
      const match = subject.match(/\d{6}/);
      if (match) {
        // I'll talk about this right after
      }
    }

    // And here I forward it to my personal email
    await message.forward(env.MAIL_FORWARD);
  }
}
```

You need to click "Deploy" for it to be online.

The thing with my code is that I need it to be saved somewhere to be able to distribute it. I used Cloudflare's KV store for this. You just need to create a "namespace", and you're set.

{{}}


  {{}}
  The codes will be saved here


{{}}

Then I go back into Worker: Workers & Pages / my worker / Settings. Under "Bindings", I create a connection to my KV store, which I name "BINDING_KV". Finally, in my code, I can save the received value using this:

```
if (match) {
  // match corresponds to the regex matching the 6-digit code
  await env.BINDING_KV.put("temp_code", match);
}
```

{{}}

## Generating 6-Digit TOTP Codes

To retrieve the famous 6-digit temporary "One-Time Passwords" that change every 30 seconds, I took the easy route. I found [this JavaScript example by turistu on GitHub](https://github.com/turistu/totp-in-javascript/blob/main/totp.js).

```
// This part I cheerfully borrowed from the internet
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
  let a = new Uint8Array(buf), i = a & 0xf;
	return fmt(10, digits, ((a[i]&0x7f) {
    let i = 'abcdefghijklmnopqrstuvwxyz234567'.indexOf(c);
		if(i  parseInt(d, 2)));
}
function pack64bu(v){
  let b = new ArrayBuffer(8), d = new DataView(b);
	d.setUint32(0, v / 2**32);
	d.setUint32(4, v);
	return b;
}
```

{{}}

To use it, you need to call it with the generated 2FA token. If you only have a QR code available, you need to decode it and retrieve the part in "secret=XXXX".

```
const codeTotp = await generateTOTP("ABCDEFGHIJKL0987654321MNOPQRSTUVWXYZ");
```

{{}}

## Exposing These Codes on a Website

Now I need to expose this on a web page. For that, no need to change the Worker. I use the same one by adding this:

{{}}

```
export default {
  async email(message, env, ctx) {
    // ... (previous email handling code)
  },

  // fetch() is used for each request made via the web
  async fetch(request, env, ctx) {
    // I retrieve the value of "temp_code"
    const codeMail = await env.BINDING_KV.get("temp_code");
    // I retrieve the TOTP value
    const codeTotp = await generateTOTP(env.TOKEN_OTP);

    // I build the result
    const response = `Code received by email: ${codeMail}\n\n 2FA Code (valid 30s):\n\n${codeTotp}`

    return new Response(response || "Error building the response");
  },
};
```

{{}}

## Environment Variables

I've mentioned values like `env.SOMETHING` several times now. A quick recap of what we have so far:

-   `env.MAIL_FORWARD`: my personal email, saved in environment variables
-   `env.BINDING_KV`: the connection to the Cloudflare KV store, already provisioned via bindings
-   `env.TOKEN_OTP`: The token used to generate a 2FA code

Apart from BINDING_KV which is handled via bindings, the other two values need to be filled in as environment variables.

For this, I go back to my Worker, then Settings... In "Variables and Secrets", I can easily add my values. Whether they are set as 'Secret' or 'Text' doesn't change how they're accessed in the code, so I set both as secrets (Secrets are encrypted and not visible in the dashboard after saving). You need to click "Deploy" when done.

{{}}


  {{}}
  Environment variables can be easily modified


{{}}

## Managing Access

I don't want everyone to be able to access these codes (obviously).

I want to keep it simple: send unique links to people. I also want to be able to easily revoke their access.

For this, I'll deploy another environment variable, this time in JSON format.

-   Type: JSON
-   Variable name: AUTHORIZED_URLS
-   Value: `["a1b2c3d4","z9y8x7w6"]`

This way, I just have to remove the code (the string in the array) I no longer want to be active.

To implement this in the code, I get the accessed URL and the path segment after the domain (e.g., www.mysite.com/**thecode**) to check if it's in my list.

{{}}

```
export default {
  async email(message, env, ctx) {
    // ... (email handling code)
  },

  // fetch() is used for each request made via the web
  async fetch(request, env, ctx) {
    // I get the URL and split its pathname by '/' characters
    const url = new URL(request.url);
    const pathSegments = url.pathname.split('/').filter(Boolean); // filter(Boolean) removes empty strings from leading/trailing slashes

    // I check if the first path segment is in the list
    if (pathSegments.length > 0 && env.AUTHORIZED_URLS.includes(pathSegments)) {
      // The previous code to get and display codes
      // I retrieve the value of "temp_code"
      const codeMail = await env.BINDING_KV.get("temp_code");
      // I retrieve the TOTP value
      const codeTotp = await generateTOTP(env.TOKEN_OTP);

      // I build the result
      const response = `Code received by email: ${codeMail}\n\n 2FA Code (valid 30s):\n\n${codeTotp}`
      return new Response(response || "Error building the response");
    }

    // If the path segment is not in the authorized list, or no path segment exists
    return new Response("Unauthorized", { status: 401 });
  },
};
```

{{}}

I can also, by using an `else if` with other lists (perhaps stored in other environment variables or another structure), have a single Worker for multiple accounts and return a different response each time.

{{}}

## Custom Domain

Cloudflare handles this quite easily, especially since your domain is already in Cloudflare.

I go back to my Worker, then Settings. The "Triggers" tab (note: Cloudflare UI might change, it could be under "Triggers" or a dedicated "Custom Domains" section) allows me to configure my custom domain via Routes or direct Custom Domains assignment.

I click "Add Custom Domain" (or similar), add the domain "code.timothechau.vet", and Cloudflare guides through the setup (usually involving adding a CNAME record).

{{}}


  {{}}
  My domain timothechau.vet is already in Cloudflare. Otherwise, it would have needed to be added manually first.


{{}}

## tl;dr

So, the steps were:

1.  Add your domain to Cloudflare.
2.  Create an empty Worker.
3.  Add a rule in your domain's Email / Email Routing / Routing Rules to "Send to Worker".
4.  Create an empty key-value store in Workers & Pages / KV / Create Namespace.
5.  Bind the KV namespace in the Worker / Settings / Variables / KV Namespace Bindings.
6.  Create environment variables `MAIL_FORWARD`, `TOKEN_OTP`, `AUTHORIZED_URLS` (JSON) in Worker / Settings / Variables / Environment Variables.
7.  Deploy the code below.
8.  Attach a custom domain like `code.mydomain.com` in the Worker / Triggers / Custom Domains (or Routes).

Here's a recap of the final code. [I've also put it in this repo](https://github.com/timothechauvet/workers-code-cloudflare/blob/main/worker.js)

```
export default {
  async email(message, env, ctx) {
    // Here I get the email subject
    const subject = message.headers.get("subject");
    // If it matches criteria, I run my program
    if (
      message.to === "example@timothechau.vet" && // Replace with your target email address
      subject.startsWith("Your code is ") // Adjust based on expected subject line
    ) {
      // Regex to extract the 6-digit code from the subject
      const match = subject.match(/\d{6}/);
      if (match) {
        // Store the extracted code in KV
        await env.BINDING_KV.put("temp_code", match);
      }
    }

    // Forward the email to the personal inbox specified in the environment variable
    await message.forward(env.MAIL_FORWARD);
  },

  // fetch() handles incoming web requests to the Worker URL/custom domain
  async fetch(request, env, ctx) {
    // I get the URL and split its pathname by '/' characters
    const url = new URL(request.url);
    const pathSegments = url.pathname.split('/').filter(Boolean);

    // Check if the first path segment exists and is included in the authorized list (from JSON env var)
    if (pathSegments.length > 0 && env.AUTHORIZED_URLS.includes(pathSegments)) {
      // Retrieve the temporary code stored in KV
      const codeMail = await env.BINDING_KV.get("temp_code") || "No code found"; // Provide default if null
      // Generate the current TOTP code using the secret from env var
      const codeTotp = await generateTOTP(env.TOKEN_OTP);

      // Construct the response text
      const responseText = `Code received by email: ${codeMail}\n\n2FA Code (valid 30s):\n\n${codeTotp}`;
      return new Response(responseText || "Error building the response"); // Provide fallback text
    }

    // If the path segment is not authorized or missing
    return new Response("Unauthorized", { status: 401 });
  },
};

// --- TOTP Generation Code (Borrowed) ---
async function generateTOTP(key, secs = 30, digits = 6){
  // Generates TOTP using HOTP algorithm with time-based counter
  return hotp(unbase32(key), pack64bu(Math.floor(Date.now() / 1000 / secs)), digits);
}
async function hotp(key, counter, digits){
  // HMAC-based One-Time Password (HOTP) core logic
  let cryptoSubtle = self.crypto.subtle;
	if(!cryptoSubtle) throw Error('Web Crypto API (crypto.subtle) not available');
	// Import the key for HMAC-SHA-1 signing
	let importedKey = await cryptoSubtle.importKey('raw', key, {name: 'HMAC', hash: 'SHA-1'}, false, ['sign']);
  // Sign the counter with the key
	let signature = await cryptoSubtle.sign('HMAC', importedKey, counter);
  // Truncate the signature to get the HOTP value
	return hotp_truncate(signature, digits);
}
function hotp_truncate(buffer, digits){
  // Truncates the HMAC-SHA-1 signature based on RFC 4226
  let hashBytes = new Uint8Array(buffer);
  // Get the offset from the last nibble of the hash
  let offset = hashBytes[hashBytes.length - 1] & 0xf;
  // Extract 4 bytes from the hash at the calculated offset
  let binaryCode = ((hashBytes[offset] & 0x7f)  {
    let index = base32chars.indexOf(char);
		if(index  parseInt(byteString, 2)));
}
function pack64bu(v){
  // Packs a 64-bit unsigned integer (like a timestamp counter) into an ArrayBuffer (Big-Endian)
  let buffer = new ArrayBuffer(8);
  let dataView = new DataView(buffer);
  // Split the 64-bit number into two 32-bit parts
	dataView.setUint32(0, Math.floor(v / 2**32)); // High 32 bits
	dataView.setUint32(4, v % 2**32);          // Low 32 bits
	return buffer;
}
```

---

If you have any questions or suggestions, feel free to contact me by [email](mailto:timothe@chauvet.cloud), on [LinkedIn](https://www.linkedin.com/in/timothechauvet/), or directly by sending [an *issue* on GitHub](https://github.com/timothechauvet/timothechauvet.github.io/issues).
