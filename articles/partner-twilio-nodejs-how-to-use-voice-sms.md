---
title: Používání Twilio pro hlasové, VoIP a SMS zprávy v Azure
description: Přečtěte si, jak telefonovat a odesílat SMS zprávy pomocí služby Twilio API v Azure. Ukázky kódu napsané v souboru Node.js.
services: ''
documentationcenter: nodejs
author: georgewallace
ms.assetid: f558cbbd-13d2-416f-b9b1-33a99c426af9
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 11/25/2014
ms.author: gwallace
ms.openlocfilehash: 164bedffcf9a1aca9f1fa46dea254fb928abcf04
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "69637271"
---
# <a name="using-twilio-for-voice-voip-and-sms-messaging-in-azure"></a>Používání Twilio pro hlasové, VoIP a SMS zprávy v Azure
Tato příručka ukazuje, jak vytvářet aplikace, které komunikují s Twilio a node.js v Azure.

<a id="whatis"/>

## <a name="what-is-twilio"></a>Co je Twilio?
Twilio je platforma API, která vývojářům usnadňuje telefonování, odesílání a přijímání textových zpráv a vkládá volání VoIP do mobilních aplikací založených na prohlížeči a nativních mobilních aplikacích. Pojďme krátce procházet, jak to funguje, než se ponoříme.

### <a name="receiving-calls-and-text-messages"></a>Přijímání hovorů a textových zpráv
Twilio umožňuje [vývojářům zakoupit programovatelná telefonní čísla,][purchase_phone] která lze použít jak k odesílání, tak k přijímání hovorů a textových zpráv. Když číslo Twilio obdrží příchozí hovor nebo textovou zprávu, Twilio odešle webové aplikaci požadavek HTTP POST nebo GET a požádá vás o pokyny, jak zpracovat volání nebo textovou zprávu. Server bude reagovat na požadavek HTTP twilio s [TwiML][twiml], jednoduchou sadu XML značek, které obsahují pokyny, jak zpracovat volání nebo text. Uvidíme příklady TwiML za okamžik.

### <a name="making-calls-and-sending-text-messages"></a>Volání a odesílání textových zpráv
Vytvářením požadavků HTTP do rozhraní API webové služby Twilio mohou vývojáři odesílat textové zprávy nebo iniciovat odchozí telefonní hovory. Pro odchozí volání musí vývojář také zadat adresu URL, která vrací pokyny TwiML pro zpracování odchozího volání po připojení.

### <a name="embedding-voip-capabilities-in-ui-code-javascript-ios-or-android"></a>Vkládání funkcí VoIP do kódu ui (JavaScript, iOS nebo Android)
Twilio poskytuje sdk na straně klienta, který dokáže přeměnit jakýkoli webový prohlížeč pro stolní počítače, aplikaci pro iOS nebo aplikaci pro Android na telefon VoIP. V tomto článku se zaměříme na to, jak používat VoIP volání v prohlížeči. Kromě sady *Twilio JavaScript SDK* spuštěné v prohlížeči musí být k vydání "tokenu schopností" klientovi JavaScriptu použita aplikace na straně serveru (naše aplikace node.js). Můžete si přečíst více o používání VoIP s node.js [na blogu Twilio dev][voipnode].

<a id="signup"/>

## <a name="sign-up-for-twilio-microsoft-discount"></a>Zaregistrujte se na Twilio (Microsoft Sleva)
Před použitím služeb Twilio se musíte [nejprve zaregistrovat][signup]k účtu . Zákazníci Microsoft Azure obdrží speciální slevu – [nezapomeňte se zaregistrovat zde!][signup]

<a id="azuresite"/>

## <a name="create-and-deploy-a-nodejs-azure-website"></a>Vytvoření a nasazení webu Azure node.js
Dále budete muset vytvořit web node.js spuštěný v Azure. [Oficiální dokumentace k tomu je umístěna zde][azure_new_site]. Na vysoké úrovni budete dělat následující:

* Registrace účtu Azure, pokud ho ještě nemáte
* Vytvoření nového webu pomocí konzoly pro správu Azure
* Přidání podpory správy zdrojového kódu (budeme předpokládat, že jste použili git)
* Vytvoření souboru `server.js` pomocí jednoduché webové aplikace node.js
* Nasazení této jednoduché aplikace do Azure

<a id="twiliomodule"/>

## <a name="configure-the-twilio-module"></a>Konfigurace modulu Twilio
Dále začneme psát jednoduchou aplikaci node.js, která využívá rozhraní Twilio API. Než začneme, musíme nakonfigurovat pověření účtu Twilio.

### <a name="configuring-twilio-credentials-in-system-environment-variables"></a>Konfigurace pověření Twilio v proměnných systémového prostředí
Aby bylo možné ověřit požadavky proti Back-end Twilio, potřebujeme náš účet SID a auth token, které fungují jako uživatelské jméno a heslo nastavené pro náš účet Twilio. Nejbezpečnější způsob, jak je nakonfigurovat pro použití s modulem uzlů v Azure, je prostřednictvím proměnných prostředí systému, které můžete nastavit přímo v konzole pro správu Azure.

Vyberte web node.js a klikněte na odkaz KONFIGUROVAT.  Pokud se posunete dolů trochu, uvidíte oblast, kde můžete nastavit vlastnosti konfigurace pro vaši aplikaci.  Zadejte pověření svého účtu Twilio[(najdete na konzoli Twilio),][twilio_console]jak je znázorněno na obrázku - nezapomeňte je pojmenovat `TWILIO_ACCOUNT_SID` a `TWILIO_AUTH_TOKEN`, v uvedeném pořadí:

![Konzola pro správu Azure][azure-admin-console]

Po konfiguraci těchto proměnných restartujte aplikaci v konzole Azure.

### <a name="declaring-the-twilio-module-in-packagejson"></a>Deklarování modulu Twilio v souboru package.json
Dále musíme vytvořit package.json pro správu našich závislostí modulu uzlu přes [npm]. Na stejné úrovni `server.js` jako soubor, který jste vytvořili v kurzu *Azure/node.js,* vytvořte soubor s názvem `package.json`.  Uvnitř tohoto souboru umístěte následující:

```json
{
  "name": "application-name",
  "version": "0.0.1",
  "private": true,
  "scripts": {
    "start": "node server"
  },
  "dependencies": {
    "body-parser": "^1.16.1",
    "ejs": "^2.5.5",
    "errorhandler": "^1.5.0",
    "express": "^4.14.1",
    "morgan": "^1.8.1",
    "twilio": "^2.11.1"
  }
}
```

To deklaruje modul twilio jako závislost, stejně jako populární [webový rámec Express][express] a modul šablony EJS.  Dobře, teď jsme všichni připraveni - napíšeme nějaký kód!

<a id="makecall"/>

## <a name="make-an-outbound-call"></a>Volání do odchozího hovoru
Vytvořme jednoduchý formulář, který zavolá na číslo, které si vybereme. Otevřete `server.js`a zadejte následující kód. Všimněte si, kde se píše "CHANGE_ME" - tam uveďte název webu Azure:

```javascript
// Module dependencies
const express = require('express');
const path = require('path');
const http = require('http');
const twilio = require('twilio');
const logger = require('morgan');
const bodyParser = require('body-parser');
const errorHandler = require('errorhandler');
const accountSid = process.env.TWILIO_ACCOUNT_SID;
const authToken = process.env.TWILIO_AUTH_TOKEN;
// Create Express web application
const app = express();

// Express configuration
app.set('port', process.env.PORT || 3000);
app.set('views', __dirname + '/views');
app.set('view engine', 'ejs');
app.use(logger('tiny'));
app.use(bodyParser.urlencoded({ extended: false }))
app.use(bodyParser.json())
app.use(express.static(path.join(__dirname, 'public')));

if (app.get('env') !== 'production') {
  app.use(errorHandler());
}

// Render an HTML user interface for the application's home page
app.get('/', (request, response) => response.render('index'));

// Handle the form POST to place a call
app.post('/call', (request, response) => {
  var client = twilio(accountSid, authToken);

  client.makeCall({
    // make a call to this number
    to:request.body.number,

    // Change to a Twilio number you bought - see:
    // https://www.twilio.com/console/phone-numbers/incoming
    from:'+15558675309',

    // A URL in our app which generates TwiML
    // Change "CHANGE_ME" to your app's name
    url:'https://CHANGE_ME.azurewebsites.net/outbound_call'
  }, () => {
      // Go back to the home page
      response.redirect('/');
  });
});

// Generate TwiML to handle an outbound call
app.post('/outbound_call', (request, response) => {
  var twiml = new twilio.TwimlResponse();

  // Say a message to the call's receiver
  twiml.say('hello - thanks for checking out Twilio and Azure', {
      voice:'woman'
  });

  response.set('Content-Type', 'text/xml');
  response.send(twiml.toString());
});

// Start server
app.listen(app.get('port'), function(){
  console.log(`Express server listening on port ${app.get('port')}`);
});
```

Dále vytvořte adresář `views` s názvem - uvnitř `index.ejs` tohoto adresáře vytvořte soubor pojmenovaný s následujícím obsahem:

```html
<!DOCTYPE html>
<html>
<head>
  <title>Twilio Test</title>
  <style>
    input { height:20px; width:300px; font-size:18px; margin:5px; padding:5px; }
  </style>
</head>
<body>
  <h1>Twilio Test</h1>
  <form action="/call" method="POST">
      <input placeholder="Enter a phone number" name="number"/>
      <br/>
      <input type="submit" value="Call the number above"/>
  </form>
</body>
</html>
```

Teď nasaďte svůj web do Azure a otevřete svůj domov. Měli byste být schopni zadat své telefonní číslo do textového pole a přijmout hovor z vašeho čísla Twilio!

<a id="sendmessage"/>

## <a name="send-an-sms-message"></a>Odeslání SMS zprávy
Nyní nastavíme uživatelské rozhraní a logiku zpracování formulářů pro odeslání textové zprávy. Otevřete `server.js`a po posledním volání přidejte `app.post`následující kód do :

```javascript
app.post('/sms', (request, response) => {
  const client = twilio(accountSid, authToken);

  client.sendSms({
      // send a text to this number
      to:request.body.number,

      // A Twilio number you bought - see:
      // https://www.twilio.com/console/phone-numbers/incoming
      from:'+15558675309',

      // The body of the text message
      body: request.body.message

  }, () => {
      // Go back to the home page
      response.redirect('/');
  });
});
```

V `views/index.ejs`souboru přidejte pod první formulář další formulář, který odešle číslo a textovou zprávu:

```html
<form action="/sms" method="POST">
  <input placeholder="Enter a phone number" name="number"/>
  <br/>
  <input placeholder="Enter a message to send" name="message"/>
  <br/>
  <input type="submit" value="Send text to the number above"/>
</form>
```

Znovu nasadit aplikaci do Azure a nyní byste měli být schopni odeslat tento formulář a odeslat sami (nebo některý z vašich nejbližších přátel) textovou zprávu!

<a id="nextsteps"/>

## <a name="next-steps"></a>Další kroky
Nyní jste se naučili základy použití node.js a Twilio k vytváření aplikací, které komunikují. Ale tyto příklady sotva poškrábat povrch toho, co je možné s Twilio a node.js. Další informace pomocí twilio s node.js, podívejte se na následující zdroje:

* [Oficiální modulové dokumenty][docs]
* [Výuka v oVIP s aplikacemi node.js][voipnode]
* [Votr - aplikace pro hlasování sms v reálném čase s node.js a CouchDB (tři části)][votr]
* [Párování programování v prohlížeči s node.js][pair]

Doufáme, že máte rádi hacking node.js a Twilio v Azure!

[purchase_phone]: https://www.twilio.com/console/phone-numbers/search
[twiml]: https://www.twilio.com/docs/api/twiml
[signup]: https://ahoy.twilio.com/azure
[azure_new_site]: app-service/app-service-web-get-started-nodejs.md
[twilio_console]: https://www.twilio.com/console
[npm]: https://npmjs.org
[express]: https://expressjs.com
[voipnode]: https://www.twilio.com/blog/2013/04/introduction-to-twilio-client-with-node-js.html
[docs]: https://www.twilio.com/docs/libraries/reference/twilio-node/
[votr]: https://www.twilio.com/blog/2012/09/building-a-real-time-sms-voting-app-part-1-node-js-couchdb.html
[pair]: https://www.twilio.com/blog/2013/06/pair-programming-in-the-browser-with-twilio.html
[azure-admin-console]: ./media/partner-twilio-nodejs-how-to-use-voice-sms/twilio_1.png
