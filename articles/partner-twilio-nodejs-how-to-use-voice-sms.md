---
title: Použití Twilia pro hlasové, VoIP a zasílání zpráv SMS v Azure
description: Zjistěte, jak volat a odeslat zprávu SMS s služba Twilio API v Azure. Ukázky kódu napsané v Node.js.
services: ''
documentationcenter: nodejs
author: devinrader
manager: wpickett
editor: ''
ms.assetid: f558cbbd-13d2-416f-b9b1-33a99c426af9
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 11/25/2014
ms.author: wpickett
ms.openlocfilehash: 3434c4712252e52f8a03c729524d116216a64f8d
ms.sourcegitcommit: 7b845d3b9a5a4487d5df89906cc5d5bbdb0507c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/14/2018
ms.locfileid: "42058647"
---
# <a name="using-twilio-for-voice-voip-and-sms-messaging-in-azure"></a>Použití Twilia pro hlasové, VoIP a zasílání zpráv SMS v Azure
Tato příručka ukazuje, jak vytvářet aplikace, které komunikují s Twilio a node.js v Azure.

<a id="whatis"/>

## <a name="what-is-twilio"></a>Co je Twilio?
Twilio je platforma rozhraní API, která usnadňuje vývojářům Ujistěte se a přijímat telefonní hovory, odesílat a přijímat textové zprávy a vložit volání VoIP v prohlížeči a nativní mobilní aplikace. Pojďme krátce projít jak to funguje, než se podíváme.

### <a name="receiving-calls-and-text-messages"></a>Přijímat hovory a textové zprávy
Twilio umožňuje vývojářům [zakoupit programovatelný telefonní čísla] [ purchase_phone] které je možné odesílat i přijímat hovory a textové zprávy. Když číslo Twilio dostane příchozího hovoru nebo textové, Twilio bude odesílat webové aplikace HTTP POST nebo požadavek GET, s výzvou pokyny o tom, jak zpracovat hovor nebo zprávu. Váš server bude reagovat na požadavek HTTP pro Twilio [TwiML][twiml], jednoduchou sadu značky XML, které obsahují pokyny, jak zpracovat hovor nebo zprávu. Příklady TwiML uvidíme právě za chvíli.

### <a name="making-calls-and-sending-text-messages"></a>Volání a posílat textové zprávy
Vývojáře můžou tím, že požadavky HTTP do rozhraní API webové služby Twilio, odeslání textové zprávy nebo iniciovat odchozí telefonních hovorů. Pro odchozí volání musí vývojář také zadejte adresu URL, která vrací TwiML pokyny pro zpracování odchozích volání po připojení.

### <a name="embedding-voip-capabilities-in-ui-code-javascript-ios-or-android"></a>Možnosti VoIP vkládání kódu uživatelského rozhraní (JavaScript, iOS nebo Android)
Twilio poskytuje SDK na straně klienta, která můžete vypnout všechny desktopového webového prohlížeče, aplikace pro iOS nebo aplikace pro Android do telefonu VoIP. V tomto článku se zaměříme na tom, jak používat v prohlížeči volání VoIP. Kromě *Twilio JavaScript SDK* spuštění v prohlížeči, musí se aplikace na straně serveru (naši aplikaci node.js) použít k vydání token"funkce" klientovi JavaScript. Další informace o používání VoIP s využitím node.js [na blogu dev Twilio][voipnode].

<a id="signup"/>

## <a name="sign-up-for-twilio-microsoft-discount"></a>Přihlaste se k Twiliu (Microsoft sleva)
Před použitím služeb Twilio, je nutné nejprve [zaregistrujte si účet][signup]. Microsoft Azure zákazníkům speciální slevy - [je potřeba zaregistrovat][signup]!

<a id="azuresite"/>

## <a name="create-and-deploy-a-nodejs-azure-website"></a>Vytvoření a nasazení webu node.js v Azure
V dalším kroku je potřeba vytvořit web node.js, běžící v Azure. [Zde se nachází v oficiální dokumentaci pro tyto][azure_new_site]. Na vysoké úrovni vám bude možné následujícím způsobem:

* Registrace účtu Azure, pokud již nemáte
* Pomocí konzoly pro správu Azure k vytvoření nového webu
* Přidání podpory zdrojového ovládacího prvku (budeme předpokládat, že jste použili git)
* Vytvoření souboru `server.js` s webovou aplikací jednoduché node.js
* Nasazení Tato jednoduchá aplikace do Azure

<a id="twiliomodule"/>

## <a name="configure-the-twilio-module"></a>Konfigurace modulu Twilio
V dalším kroku začneme psát aplikace jednoduchý node.js, které používají Twilio API. Než začneme, musíme nakonfigurovat přihlašovací údaje k účtu naše Twilio.

### <a name="configuring-twilio-credentials-in-system-environment-variables"></a>Konfigurace přihlašovací údaje k Twiliu v proměnných prostředí systému
Aby bylo možné ověření požadavků na back-endu Twilio, potřebujeme naše SID účtu a ověřovací token, kterou funkci jako uživatelské jméno a heslo nastavené pro náš účet Twilio. Nejbezpečnější způsob konfigurace pro použití s modulem uzlu v Azure je prostřednictvím proměnných prostředí systému, které můžete nastavit přímo v konzole pro správu Azure.

Vyberte svůj web node.js a klikněte na odkaz "Konfigurace".  Pokud se posunete dolů bit, zobrazí se na oblast, kde můžete nastavit vlastnosti konfigurace pro vaši aplikaci.  Zadejte svoje přihlašovací údaje účtu Twilio ([v konzole Twilio][twilio_console]) jak je znázorněno – Ujistěte se, že je název `TWILIO_ACCOUNT_SID` a `TWILIO_AUTH_TOKEN`v uvedeném pořadí:

![Konzole pro správu Azure][azure-admin-console]

Po nakonfigurování těchto proměnných, restartujte aplikaci v konzole Azure.

### <a name="declaring-the-twilio-module-in-packagejson"></a>Deklarování Twilio modul v souboru package.json
Dále je třeba vytvořit soubor package.json ke správě našich závislostí modulů uzlu přes [npm]. Na stejné úrovni jako `server.js` vytvořeného v souboru *Azure/node.js* kurzu, vytvořte soubor s názvem `package.json`.  V tomto souboru umístíte následující:

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

Tento modul twilio deklaruje jako závislost, stejně jako oblíbené [webovou architekturu Express] [ express] a EJS modulu šablon.  Dobře teď můžeme všechno je nastavené – umožňuje napsat kód!

<a id="makecall"/>

## <a name="make-an-outbound-call"></a>Odchozí volání
Pojďme vytvořit jednoduchý formulář, který se zavolat na číslo, že jsme zvolili. Otevřete `server.js`a zadejte následující kód. Mějte na paměti, kde říká "CHANGE_ME" - do názvu webu azure:

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

Dále vytvořte adresář s názvem `views` – v tomto adresáři vytvořte soubor s názvem `index.ejs` s následujícím obsahem:

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

Nyní nasazení webu do Azure a otevřít vaše domovská stránka. Je třeba textového pole zadejte své telefonní číslo a pracovníci své číslo Twilio!

<a id="sendmessage"/>

## <a name="send-an-sms-message"></a>Odeslat zprávu SMS
Teď Pojďme pro vás vytvořit uživatelské rozhraní a logika zpracování formuláře pro odeslání textové zprávy. Otevřete `server.js`a přidejte následující kód za poslední volání `app.post`:

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

V `views/index.ejs`, přidejte jiný formulář pod první z nich pro odeslání čísla a textové zprávy:

```html
<form action="/sms" method="POST">
  <input placeholder="Enter a phone number" name="number"/>
  <br/>
  <input placeholder="Enter a message to send" name="message"/>
  <br/>
  <input type="submit" value="Send text to the number above"/>
</form>
```

Znovu nasadit aplikaci do Azure a nyní byste měli být schopni odeslání, které tvoří a odeslat zprávu SMS sami sebe (nebo některé z vašich přátel nejbližší)!

<a id="nextsteps"/>

## <a name="next-steps"></a>Další kroky
Nyní jste se naučili základy vytváření aplikací, které komunikují pomocí node.js a Twilio. Ale tyto příklady i neziskovky pomocný na plochu, co je možné pomocí platformy Twilio a node.js. Další informace Twilio pomocí node.js najdete v následujících tématech:

* [Modul oficiální dokumentace][docs]
* [Kurz týkající se VoIP s aplikací v node.js][voipnode]
* [Votr – v reálném čase SMS hlasovací aplikaci s využitím node.js a CouchDB (tři části)][votr]
* [Pár programování v prohlížeči s využitím node.js][pair]

Doufáme, že jste si oblíbili hacking node.js s platformou Twilio v Azure!

[purchase_phone]: https://www.twilio.com/console/phone-numbers/search
[twiml]: https://www.twilio.com/docs/api/twiml
[signup]: http://ahoy.twilio.com/azure
[azure_new_site]: app-service/app-service-web-get-started-nodejs.md
[twilio_console]: https://www.twilio.com/console
[npm]: http://npmjs.org
[express]: http://expressjs.com
[voipnode]: http://www.twilio.com/blog/2013/04/introduction-to-twilio-client-with-node-js.html
[docs]: https://www.twilio.com/docs/libraries/reference/twilio-node/
[votr]: http://www.twilio.com/blog/2012/09/building-a-real-time-sms-voting-app-part-1-node-js-couchdb.html
[pair]: http://www.twilio.com/blog/2013/06/pair-programming-in-the-browser-with-twilio.html
[azure-admin-console]: ./media/partner-twilio-nodejs-how-to-use-voice-sms/twilio_1.png
