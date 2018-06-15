---
title: Použití Twilio pro hlasové, VoIP a zasílání zpráv SMS v Azure
description: Naučte se telefonní hovor a odeslání zprávy SMS službou Twilio rozhraní API v Azure. Ukázky kódu jsou vytvořeny v Node.js.
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
ms.openlocfilehash: f347540c78be712fc46d1d36b47ca4e23a62e28a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23865880"
---
# <a name="using-twilio-for-voice-voip-and-sms-messaging-in-azure"></a>Použití Twilio pro hlasové, VoIP a zasílání zpráv SMS v Azure
Tato příručka ukazuje, jak vytvářet aplikace, které komunikují s Twilio a node.js v Azure.

<a id="whatis"/>

## <a name="what-is-twilio"></a>Co je Twilio?
Twilio je platforma rozhraní API, která usnadňuje vývojářům zkontrolujte a příjem telefonních hovorů, odesílat a přijímat textové zprávy a vložení volání VoIP do mobilní aplikace založené na prohlížeči a nativní. Pojďme se stručně přenášeny po tom, jak to funguje než začnete.

### <a name="receiving-calls-and-text-messages"></a>Přijímá volání a textové zprávy
Twilio umožňuje vývojářům [zakoupit programovatelný telefonní čísla] [ purchase_phone] kterého lze odesílat i přijímat volání a textové zprávy. Když číslo Twilio obdrží příchozí volání nebo text, Twilio odešle vaši webovou aplikaci na HTTP POST nebo GET požadavku, žádostí pokyny o tom, jak zpracovat hovor nebo textovou. Váš server bude reagovat na požadavek HTTP je Twilio s [TwiML][twiml], jednoduchou sadou značky XML, které obsahují pokyny, jak zpracovat hovor nebo textovou. Příklady TwiML jsme se zobrazí pouze za chvíli.

### <a name="making-calls-and-sending-text-messages"></a>Volání a posílat textové zprávy
Vývojáře můžou pomocí požadavků protokolu HTTP k Twilio web service API, odeslání textové zprávy nebo zahájit odchozí telefonní hovory. Pro odchozí volání musí vývojář také zadejte adresu URL, která vrátí TwiML pokyny, jak zpracovat odchozí volání po připojení.

### <a name="embedding-voip-capabilities-in-ui-code-javascript-ios-or-android"></a>Vložení VoIP možnosti v kódu uživatelského rozhraní (JavaScript, iOS nebo Android)
Twilio poskytuje klienta SDK, který můžete upravit všechny plochy webový prohlížeč, aplikace pro iOS nebo Android aplikace na telefon VoIP. V tomto článku se zaměříme na tom, jak používat VoIP volání v prohlížeči. Kromě *Twilio JavaScript SDK* spuštěný v prohlížeči, musí se aplikace na straně serveru (naše aplikace node.js) použít k vydání token"funkce" klientovi JavaScript. Další informace o používání VoIP s node.js [na blog vývojářů Twilio][voipnode].

<a id="signup"/>

## <a name="sign-up-for-twilio-microsoft-discount"></a>Zaregistrovat se k Twilio (Microsoft slevy)
Před použitím služby Twilio, musíte nejdřív [zaregistrujte si účet][signup]. Microsoft Azure zákazníků získávání speciální slevy - [nezapomeňte zaregistrujte si zde][signup]!

<a id="azuresite"/>

## <a name="create-and-deploy-a-nodejs-azure-website"></a>Vytvoření a nasazení webu Azure node.js
Dále musíte vytvořit web node.js spuštěné v Azure. [Zde se nachází v oficiální dokumentaci tohoto postupu][azure_new_site]. Na vysoké úrovni je bude možné následujícím způsobem:

* Zaregistrujete účet Azure, pokud již nemáte
* Pomocí konzole pro správu Azure k vytvoření nového webu
* Přidání podpory zdroj ovládacího prvku (budeme předpokládat, že jste použili git)
* Vytvoření souboru `server.js` s webovou aplikací jednoduché node.js
* Nasazení této jednoduché aplikace do Azure

<a id="twiliomodule"/>

## <a name="configure-the-twilio-module"></a>Konfigurace modulu Twilio
V dalším kroku začneme psát jednoduché node.js aplikace, které využívá rozhraní API Twilio. Než začneme, je potřeba nakonfigurovat naše přihlašovací údaje účtu Twilio.

### <a name="configuring-twilio-credentials-in-system-environment-variables"></a>Konfigurace přihlašovacích údajů pro Twilio v proměnných prostředí systému
Aby bylo možné ověření požadavků na back-end Twilio, potřebujeme naše SID účtu a ověřování tokenem, které slouží jako uživatelské jméno a heslo pro účet naše Twilio. Nejbezpečnější způsob nakonfigurujete pro použití s modulem uzlu v Azure je prostřednictvím systémové proměnné prostředí, které můžete nastavit přímo v konzole pro správu Azure.

Vyberte svůj web node.js a klikněte na odkaz "Konfigurace".  Posuňte se dolů bit, zobrazí se oblast, kde můžete nastavit vlastnosti konfigurace pro vaši aplikaci.  Zadejte přihlašovací údaje účtu Twilio ([nalezen na konzole Twilio][twilio_console]) jak je znázorněno - zkontrolujte, zda je název `TWILIO_ACCOUNT_SID` a `TWILIO_AUTH_TOKEN`, v uvedeném pořadí:

![Konzola pro správu Azure][azure-admin-console]

Po nakonfigurování těchto proměnných restartujte aplikaci v konzole Azure.

### <a name="declaring-the-twilio-module-in-packagejson"></a>Deklarování Twilio modul v souboru package.json
Dále je nutné vytvořit package.json ke správě závislosti modulu naše uzlu prostřednictvím [npm]. Na stejné úrovni jako `server.js` jste vytvořili v souboru *Azure/node.js* kurzu, vytvořte soubor s názvem `package.json`.  Uvnitř tohoto souboru umístíte následující:

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

Tento modul twilio deklaruje jako závislost, stejně jako oblíbených [Express webová architektura] [ express] a modul EJS šablony.  V pořádku teď jsme je vše připraveno – umožňuje napsat kód!

<a id="makecall"/>

## <a name="make-an-outbound-call"></a>Odchozí volání
Umožňuje vytvořit jednoduché formulář, který bude zavolat na číslo, že jsme vyberete. Otevřete `server.js`a zadejte následující kód. Všimněte si, kam se říká "CHANGE_ME" - uveďte název vašeho webu azure existuje:

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

Dále vytvořte adresář s názvem `views` – v tomto adresáři vytvořte soubor s názvem `index.ejs` s tímto obsahem:

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

Nyní nasazení webu do Azure a otevřete vaším domovem. Nyní byste měli mít do textového pole zadejte vaše telefonní číslo a přijímat volání z vaše číslo Twilio!

<a id="sendmessage"/>

## <a name="send-an-sms-message"></a>Odeslání zprávy SMS
Nyní nastavíme uživatelské rozhraní a zpracování logiky formulář pro odeslání textové zprávy. Otevřete `server.js`a přidejte následující kód po posledním volání `app.post`:

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

V `views/index.ejs`, přidejte jiný formulář pod první z nich pro odeslání číslo a textové zprávy:

```html
<form action="/sms" method="POST">
  <input placeholder="Enter a phone number" name="number"/>
  <br/>
  <input placeholder="Enter a message to send" name="message"/>
  <br/>
  <input type="submit" value="Send text to the number above"/>
</form>
```

Znovu nasaďte aplikaci do Azure a má teď nebudete moct odeslání, které tvoří a odeslání textové zprávy sami (nebo některého z vašich přátel nejbližší)!

<a id="nextsteps"/>

## <a name="next-steps"></a>Další kroky
Jste nyní se naučili základy používání node.js a Twilio můžete vytvářet aplikace, které komunikují. Ale tyto příklady sotva scratch prostor co je možné pomocí Twilio a node.js. Další informace pomocí node.js Twilio najdete v následujících zdrojích informací:

* [Oficiální modulu dokumentace][docs]
* [Kurz týkající se VoIP s aplikacemi node.js][voipnode]
* [Votr – v reálném čase SMS hlasování aplikace s node.js a CouchDB (tři části)][votr]
* [Pár programování v prohlížeči s node.js][pair]

Doufáme, že rádi hackerům node.js a Twilio v Azure!

[purchase_phone]: https://www.twilio.com/console/phone-numbers/search
[twiml]: https://www.twilio.com/docs/api/twiml
[signup]: http://ahoy.twilio.com/azure
[azure_new_site]: app-service/app-service-web-get-started-nodejs.md
[twilio_console]: https://www.twilio.com/console
[npm]: http://npmjs.org
[express]: http://expressjs.com
[voipnode]: http://www.twilio.com/blog/2013/04/introduction-to-twilio-client-with-node-js.html
[docs]: http://twilio.github.io/twilio-node/
[votr]: http://www.twilio.com/blog/2012/09/building-a-real-time-sms-voting-app-part-1-node-js-couchdb.html
[pair]: http://www.twilio.com/blog/2013/06/pair-programming-in-the-browser-with-twilio.html
[azure-admin-console]: ./media/partner-twilio-nodejs-how-to-use-voice-sms/twilio_1.png
