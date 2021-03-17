---
title: Použití Twilio pro hlasové zprávy, VoIP a SMS v Azure
description: Naučte se, jak uskutečnit telefonní hovor a poslat zprávu SMS pomocí služby Twilio API v Azure. Ukázky kódu napsané v Node.js.
documentationcenter: nodejs
author: georgewallace
ms.assetid: f558cbbd-13d2-416f-b9b1-33a99c426af9
ms.service: multiple
ms.devlang: nodejs
ms.topic: article
ms.date: 11/25/2014
ms.author: gwallace
ms.openlocfilehash: c4b1c506ff0e78e5e304ceb1682c73f9cd784c95
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88080537"
---
# <a name="using-twilio-for-voice-voip-and-sms-messaging-in-azure"></a>Použití Twilio pro hlasové zprávy, VoIP a SMS v Azure
Tato příručka ukazuje, jak vytvářet aplikace, které komunikují s Twilio a node.js v Azure.

<a name="whatis"></a>

## <a name="what-is-twilio"></a>Co je Twilio?
Twilio je platforma rozhraní API usnadňující vývojářům vytvářet a přijímat telefonní hovory, odesílat a přijímat textové zprávy a vkládat volání VoIP do aplikací založených na prohlížeči a nativních mobilních aplikacích. Podíváme se, jak to funguje před začnete v.

### <a name="receiving-calls-and-text-messages"></a>Přijímání volání a textových zpráv
Twilio umožňuje vývojářům [koupit programovatelné telefonní číslo][purchase_phone] , které se dá použít k odesílání i přijímání hovorů a textových zpráv. Když číslo Twilio přijme příchozí volání nebo text, Twilio odešle webovou aplikaci požadavek HTTP POST nebo GET, který vás vyzve k zadání pokynů pro zpracování volání nebo textu. Váš server odpoví na požadavek HTTP Twilio s [TwiML][twiml], jednoduchou sadou značek XML, která obsahuje pokyny, jak zpracovat volání nebo text. V jednom okamžiku se zobrazí příklady TwiML.

### <a name="making-calls-and-sending-text-messages"></a>Volání a posílání textových zpráv
Díky odeslání požadavků HTTP na rozhraní API webové služby Twilio můžou vývojáři posílat textové zprávy nebo iniciovat odchozí telefonní hovory. Pro odchozí volání musí vývojář zadat také adresu URL, která vrátí TwiML pokyny pro zpracování odchozího volání, jakmile je připojen.

### <a name="embedding-voip-capabilities-in-ui-code-javascript-ios-or-android"></a>Vkládání možností VoIP do kódu uživatelského rozhraní (JavaScript, iOS nebo Android)
Twilio poskytuje sadu SDK na straně klienta, která umožňuje v telefonu VoIP zapnout libovolný desktopový webový prohlížeč, aplikaci pro iOS nebo aplikaci pro Android. V tomto článku se zaměříme na použití volání VoIP v prohlížeči. Kromě *Twilio JavaScript SDK* spuštěné v prohlížeči je nutné použít aplikaci na straně serveru (node.js aplikace) k vydání "tokenu schopností" klientovi jazyka JavaScript. Další informace o používání VoIP s node.js najdete [na blogu vývoj Twilio][voipnode].

<a name="signup"></a>

## <a name="sign-up-for-twilio-microsoft-discount"></a>Zaregistrujte se do Twilio (sleva Microsoftu)
Než začnete používat služby Twilio Services, musíte se nejdřív [zaregistrovat k účtu][signup]. Zákazníci, kteří Microsoft Azure obdrží speciální slevu, [si zaregistrují][signup].

<a name="azuresite"></a>

## <a name="create-and-deploy-a-nodejs-azure-website"></a>Vytvoření a nasazení node.js webu Azure
V dalším kroku budete muset vytvořit node.js web běžící v Azure. [Oficiální dokumentaci k tomuto umístění najdete tady][azure_new_site]. Na nejvyšší úrovni budete provádět následující akce:

* Registrace účtu Azure, pokud ho ještě nemáte
* Vytvoření nového webu pomocí konzoly pro správu Azure
* Přidává se podpora správy zdrojového kódu (předpokládáme, že jste použili Git).
* Vytvoření souboru `server.js` pomocí jednoduché webové aplikace node.js
* Nasazení této jednoduché aplikace do Azure

<a name="twiliomodule"></a>

## <a name="configure-the-twilio-module"></a>Konfigurace modulu Twilio
V dalším kroku začneme psát jednoduchou node.js aplikaci, která využívá rozhraní API Twilio. Než začneme, musíme nakonfigurovat přihlašovací údaje k účtu Twilio.

### <a name="configuring-twilio-credentials-in-system-environment-variables"></a>Konfigurace přihlašovacích údajů Twilio v proměnných prostředí systému
Abychom mohli provádět ověřené požadavky na back-end Twilio, potřebujeme naše SID účtu a ověřovací token, které fungují jako uživatelské jméno a heslo nastavené pro náš účet Twilio. Nejbezpečnější způsob, jak je nakonfigurovat pro použití s modulem Node v Azure, je přes proměnné prostředí systému, které můžete nastavit přímo v konzole pro správu Azure.

Vyberte web node.js a klikněte na odkaz konfigurace.  Pokud se posunete o bit, zobrazí se oblast, kde můžete nastavit vlastnosti konfigurace aplikace.  Zadejte přihlašovací údaje účtu Twilio ([najdete v konzole Twilio][twilio_console]), jak je znázorněné – nezapomeňte je pojmenovat `TWILIO_ACCOUNT_SID` a v `TWILIO_AUTH_TOKEN` uvedeném pořadí:

![Konzola pro správu Azure][azure-admin-console]

Po nakonfigurování těchto proměnných restartujte aplikaci v konzole Azure.

### <a name="declaring-the-twilio-module-in-packagejson"></a>Deklarování modulu Twilio v package.js
Dále je potřeba vytvořit package.jspro správu závislostí modulu uzlu prostřednictvím [npm]. Na stejné úrovni jako soubor, `server.js` který jste vytvořili v kurzu *Azure/node.js* vytvořte soubor s názvem `package.json` .  V tomto souboru umístěte následující:

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

Tato deklarace deklaruje modul Twilio jako závislost a také oblíbené rozhraní [Express Web Framework][express] a modul šablon EJS.  V pořádku, teď máme všechno nastavené – Pojďme napsat nějaký kód!

<a name="makecall"></a>

## <a name="make-an-outbound-call"></a>Uskutečnit odchozí volání
Pojďme vytvořit jednoduchou formu, která umístí volání na číslo, které si vybereme. Otevřete `server.js` a zadejte následující kód. Všimněte si, že "CHANGE_ME" – sem zadejte název vašeho webu Azure:

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

Dále vytvořte adresář nazvaný `views` v tomto adresáři a vytvořte soubor `index.ejs` s názvem s následujícím obsahem:

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

Teď Nasaďte svůj web do Azure a otevřete domovskou stránku. Měli byste být schopni zadat do textového pole své telefonní číslo a získat hovor od čísla Twilio!

<a name="sendmessage"></a>

## <a name="send-an-sms-message"></a>Odeslat zprávu SMS
Nyní nastavili jsme pro odeslání textové zprávy logiku zpracování uživatelského rozhraní a formuláře. Otevřete `server.js` a přidejte následující kód po posledním volání `app.post` :

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

V portálu `views/index.ejs` přidejte do prvního pole další formulář, abyste odeslali číslo a textovou zprávu:

```html
<form action="/sms" method="POST">
  <input placeholder="Enter a phone number" name="number"/>
  <br/>
  <input placeholder="Enter a message to send" name="message"/>
  <br/>
  <input type="submit" value="Send text to the number above"/>
</form>
```

Znovu nasaďte aplikaci do Azure a teď byste měli být schopni odeslat tento formulář a poslat sami sobě (nebo libovolnému z vašich nejbližších přátel) textovou zprávu!

<a name="nextsteps"></a>

## <a name="next-steps"></a>Další kroky
Nyní jste se naučili základy používání node.js a Twilio k vytváření aplikací, které komunikují. Tyto příklady ale zlomekou povrch s tím, co je možné u Twilio a node.js. Další informace o použití Twilio s node.js najdete v následujících zdrojích informací:

* [Oficiální dokumentace modulu][docs]
* [Kurz pro VoIP s aplikacemi node.js][voipnode]
* [Votr – hlasovací aplikace SMS v reálném čase s node.js a CouchDB (tři části)][votr]
* [Párování programování v prohlížeči pomocí node.js][pair]

Doufáme, že máte v Azure rádiy node.js a Twilio.

[purchase_phone]: https://www.twilio.com/console/phone-numbers/search
[twiml]: https://www.twilio.com/docs/api/twiml
[signup]: https://ahoy.twilio.com/azure
[azure_new_site]: app-service/quickstart-nodejs.md
[twilio_console]: https://www.twilio.com/console
[npm]: https://npmjs.org
[express]: https://expressjs.com
[voipnode]: https://www.twilio.com/blog/2013/04/introduction-to-twilio-client-with-node-js.html
[docs]: https://www.twilio.com/docs/libraries/reference/twilio-node/
[votr]: https://www.twilio.com/blog/2012/09/building-a-real-time-sms-voting-app-part-1-node-js-couchdb.html
[pair]: https://www.twilio.com/blog/2013/06/pair-programming-in-the-browser-with-twilio.html
[azure-admin-console]: ./media/partner-twilio-nodejs-how-to-use-voice-sms/twilio_1.png
