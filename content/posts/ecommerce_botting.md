---
title: How ecommerce (sneaker buying) bots actually work [Part 1/2]
description: In this post I try to explain why building ecommerce shopping bots is actually hard
date: 2022-08-22T20:00:00+02:00
draft: false
tags: [bots, reverse engineering, web dev]
---

Automating the process of buying product x on online shop y sounds super easy. You just buy something and take a look at the requests your browser has made. Then you open you favorite editor and start writing a little python script that just does the same requests. For some old simple shops this may actually work but most of the time you will already find yourself failing at the payment process.

## Know your enemies

JavaScript. Most of the problems when automating web stuff these days come with some sort of JS. If the page you are trying to build a bot for is using a JS Framework you are lucky in some way, because you can avoid the process of scraping HTML most of the time (if the page is loading all its data dynamically from some HTTP endpoint). But this is where the good stuff ends. Let me introduce you with the following two gentlemen: **Anti Fraud** and **Anti Bot**.

Let's start with the first one. Nearly every online shop uses some 3rd party payment gateway to process payments. Just providing inputs for the user's credit card details and handling the whole process manually is something no one wants to do. What if the user does not even have a credit card? What if he has one, but it's not his one? Today we have PSD2 laws and 2FA for credit card payments, but imagine like 5 years ago where these requirements did not exist. Shop owners and payment gateway providers came up with different measures: "Authenticity Checks", this is how I would call them at least.

## How do I know my customer is real?

What kind of data does my customer send me while using my shop? Looking at a normal HTTP request there are only two groups of information we can collect. The customer's IP-Address and the request's headers including User-Agent, supported response encoding and the browser's language. This is obviously not the amount of information we want to rely on when verifying credit card payments. Especially, because all of this information can be spoofed. HTTP headers are set by the client and proxies matching the credit card owner's ISP and or location can be bought easily. So somehow we need to get more information from the browser. How are we going to accomplish this? **JavaScript**.
I am probably going the make another blog post about browser validation/identification using JavaScript soon, but for now I will just show you an example output of one of the world's most famous tracking libraries **[FingerPrintJS](https://github.com/fingerprintjs/fingerprintjs)**:

```json
{
  "fonts": {
    "value": [],
    "duration": 112
  },
  "domBlockers": {
    "duration": 103
  },
  "fontPreferences": {
    "value": {
      "default": 149.3125,
      "apple": 149.3125,
      "serif": 149.3125,
      "sans": 144.015625,
      "mono": 132.609375,
      "min": 9.34375,
      "system": 150.3125
    },
    "duration": 106
  },
  "audio": {
    "value": 124.04347527516074,
    "duration": 2
  },
  "screenFrame": {
    "value": [0, -1540, 0, 1540],
    "duration": 0
  },
  "osCpu": {
    "duration": 1
  },
  "languages": {
    "value": [["en-GB"]],
    "duration": 0
  },
  "colorDepth": {
    "value": 24,
    "duration": 0
  },
  "deviceMemory": {
    "value": 8,
    "duration": 0
  },
  "screenResolution": {
    "value": [864, 1536],
    "duration": 0
  },
  "hardwareConcurrency": {
    "value": 16,
    "duration": 0
  },
  "timezone": {
    "value": "Europe/Berlin",
    "duration": 5
  },
  "sessionStorage": {
    "value": true,
    "duration": 0
  },
  "localStorage": {
    "value": true,
    "duration": 0
  },
  "indexedDB": {
    "value": true,
    "duration": 0
  },
  "openDatabase": {
    "value": true,
    "duration": 0
  },
  "cpuClass": {
    "duration": 0
  },
  "platform": {
    "value": "Linux x86_64",
    "duration": 0
  },
  "plugins": {
    "value": [
      {
        "name": "Chrome PDF Plugin",
        "description": "Portable Document Format",
        "mimeTypes": [
          {
            "type": "application/x-google-chrome-pdf",
            "suffixes": "pdf"
          }
        ]
      },
      ...
    ],
    "duration": 0
  },
  "canvas": {
    "value": {
      "winding": true,
      "geometry": "base64 data...",
      "text": "base64 data..."
    },
    "duration": 80
  },
  "touchSupport": {
    "value": {
      "maxTouchPoints": 0,
      "touchEvent": false,
      "touchStart": false
    },
    "duration": 0
  },
  "vendor": {
    "value": "Google Inc.",
    "duration": 0
  },
  "vendorFlavors": {
    "value": ["chrome"],
    "duration": 0
  },
  "cookiesEnabled": {
    "value": true,
    "duration": 1
  },
  "colorGamut": {
    "value": "srgb",
    "duration": 0
  },
  "invertedColors": {
    "duration": 0
  },
  "forcedColors": {
    "value": false,
    "duration": 0
  },
  "monochrome": {
    "value": 0,
    "duration": 0
  },
  "contrast": {
    "value": 0,
    "duration": 0
  },
  "reducedMotion": {
    "value": false,
    "duration": 0
  },
  "hdr": {
    "value": false,
    "duration": 0
  },
  "math": {
    "value": {
      "acos": 1.4473588658278522,
      "acosh": 709.889355822726,
      "acoshPf": 355.291251501643,
      "asin": 0.12343746096704435,
      "asinh": 0.881373587019543,
      "asinhPf": 0.8813735870195429,
      "atanh": 0.5493061443340548,
      ...
    },
    "duration": 0
  }
}
```

This library is made to track users, it collects everything it can, using client side JavaScript and hashes the information to create a user identifier that can easily identify you even if you delete cache and cookies or use a private browser window. You can forget the identification part but only the fact that you can get so much more information from the browser using JavaScript in comparison to what you get from HTTP headers is scary. I assume that these "Authenticity Checks" made by Anti Fraud Systems are using a lot of the information the browser exposes to its JavaScript runtime.

Let's summarize: if the Anti Fraud System thinks you are a sussy baka it will drop your payments.
And there are many reasons why it could be doing so. These systems are not made to keep bots away, but to drop orders that look like they could be fraud.

## Anti Bot

Behaving legit enough to get payments through a shop's payment gateway(s) can already be hard, some shops have their own on site Anti Fraud System in addition to the one provided by their payment processor. So in worst case you have to proof to two independent systems that you are a legit person and actually want to pay for the goods you are trying to buy. Now things get even harder if are trying to automatically buy stuff people actually care about and are willing to pay money for ex: Sneakers, Clothes and GPUs (not anymore, but they were heavily botted half a year ago). One the most notorious Anti Bot Systems is the one used by [Supreme](https://www.supremenewyork.com/) called "Ticket". Its only active for a few minutes on a "drop" (moment new hype products are added to the store) so having the chance so play around with it is rare. As "Ticket" is shipped with some sort of JavaScript you can of course save all static assets of the website while its active and then start reverse engineering it later. People automated this process with scripts that scan the shop 24/7 for hash changes of static assets and then send notifications on discord.

So how does Supreme's Anti Bot work?
As their system is constantly updating it's hard to say, I will explain how it worked the last time I looked at it, but first one should understand what these systems generally do and why they prevent us from just sending simple HTTP requests to the shop to accomplish checkout.

```js
//get current unix timestamp
const time = +new Date();

//get timestamp as string and encode as base64
const code = atob(time.toString());

//send code to backend to proof we are in a real browser
fetch(`/api/antibot-check?code=${code}`)...
```

The code above should be self-explanatory. We generate something in the browser, send it to the backend and let it verify if the stuff we sent it makes any sense. By just placing this code (in obfuscated form) on our website we can already evade botting through simple copy and paste request replay python scripts.

Pro Tip: if you are ever trying to build simple replay scripts checkout the 'copy request as curl' option in chrome's DevTools network tab (right click context menu). This becomes even more useful if you use a [curl to x language converter](https://sqqihao.github.io/trillworks.html).

Reverse engineering the budget anti bot script from above is pretty easy for anyone who knows a little JS, even if its strongly obfuscated. So how does an anti bot developer make this stuff harder? Some ways I came across in the past:

- Obviously: Build a more advanced (more code to reverse) anti bot logic. We are going to talk about this later.
- Code placement. Today most websites use JavaScript bundlers like webpack to bundle many JS files into fewer, but longer and more minified files. Finding anti bot logic inside those makes stuff already a little harder.
- Build a 8bit VM in JavaScript and implement our secret login inside it (Actually google did this for reCAPTCHA at some point in time).
- Write logic in a compiled language and call it from JS using WASM.

The last point is what supreme did with Ticket for at least one month. If you want to know more about the supreme example: [Check this out](https://github.com/gondone666/go-ticket).

## Puppeteer, Playwright & Selenium

Now that you now that there is some script running in my browser that tries to make building simple HTTP bots harder one may ask: Why can't I just build my bot using a real browser? Automating stuff in a REAL browser is easily possible using user scripts or extension. There are still downsides to this approach. Let's start with the overall disadvantages of using browser based bots:

- Way slower. Pages need to render, JS gets executed, Assets (likes images) are loaded, etc...
- This makes it more performance demanding (CPU/RAM/Traffic)
- Fingerprinting. If you have read the beginning of this post you know there are many values once can use to track/identify browsers.

Now one may argue that there is a headless mode in most browsers today. Meaning the browser does not actually to "render" stuff visually and becomes a CLI application that can run on any server without a graphical environment (BIG scalability advantage). Additionally, there is another handy feature that comes in handy when trying to automate browsers at scale: [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/). Its more powerful than the name might suggest. It allows full browser automation, JS injection and more debugging capabilities. In headless mode chromium based browsers don't allow browser extensions anyway, so it's probably your best bet. Libraries that use/implement the protocol on a high level:

- [Puppeteer](https://github.com/puppeteer/puppeteer): Build by Google. NodeJS library to automate chromium based browsers.
- [Playwright](https://github.com/microsoft/playwright): Build by Microsoft. Supports more browser protocols besides CDP, but tries to imitate puppeteers syntax. For example Firefox using the [WebDriver spec](https://developer.mozilla.org/en-US/docs/Web/WebDriver).

Before CDP was used for browser automation, everyone was using WebDriver. The most famous library for this is [Selenium](https://github.com/SeleniumHQ/selenium).

# To be continued...
