---
title: 'Úvodní příručka: Vytvoření webové aplikace, která spustí immersive Reader s Node.js'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu vytvoříte webovou aplikaci od začátku a přidáte funkci rozhraní API pro immersive Reader.
author: pasta
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: quickstart
ms.date: 01/14/2020
ms.author: pasta
ms.openlocfilehash: 749e75fed409632c613713a49154e4cd8dc265b3
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75946324"
---
# <a name="quickstart-create-a-web-app-that-launches-the-immersive-reader-nodejs"></a>Úvodní příručka: Vytvoření webové aplikace, která spustí immersive Reader (Node.js)

[Immersive Reader](https://www.onenote.com/learningtools) je inkluzivně navržený nástroj, který implementuje osvědčené techniky pro zlepšení porozumění čtení.

V tomto rychlém startu vytvoříte webovou aplikaci od začátku a integrujete immersive Reader pomocí sady Immersive Reader SDK. Úplný pracovní vzorek tohoto rychlého startu je k dispozici [zde](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/quickstart-nodejs).

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="prerequisites"></a>Požadavky

* Prostředek immersive Reader nakonfigurovaný pro ověřování Azure Active Directory. Podle [těchto pokynů](./how-to-create-immersive-reader.md) se připravte. Při konfiguraci vlastností prostředí budete potřebovat některé hodnoty vytvořené zde. Uložte výstup relace do textového souboru pro budoucí použití.
* [Node.js](https://nodejs.org/) a [příze](https://yarnpkg.com)
* IDE, jako je [například visual studio kód](https://code.visualstudio.com/)

## <a name="create-a-nodejs-web-app-with-express"></a>Vytvoření webové aplikace Node.js pomocí expressu

Vytvořte pomocí `express-generator` nástroje webovou aplikaci Node.js.

```bash
npm install express-generator -g
express --view=pug quickstart-nodejs
cd quickstart-nodejs
```

Nainstalujte závislosti příze a přidejte `dotenv`závislosti a , které budou použity `request` později v rychlém startu.

```bash
yarn
yarn add request
yarn add dotenv
```

## <a name="set-up-authentication"></a>Nastavení ověřování

### <a name="configure-authentication-values"></a>Konfigurace hodnot ověřování

Vytvořte nový soubor s názvem _ENV_ v kořenovém adresáři projektu. Vložte do něj následující kód a zadejte hodnoty dané při vytváření prostředku imersive Reader.
Nezahrnejte uvozovky ani znaky {a "}".

```text
TENANT_ID={YOUR_TENANT_ID}
CLIENT_ID={YOUR_CLIENT_ID}
CLIENT_SECRET={YOUR_CLIENT_SECRET}
SUBDOMAIN={YOUR_SUBDOMAIN}
```

Ujistěte se, že tento soubor není potvrzena do správy zdrojového kódu, protože obsahuje tajné klíče, které by neměly být zveřejněny.

Dále otevřete _soubor app.js_ a přidejte následující do horní části souboru. Tím se vlastnosti definované v souboru ENV jako proměnné prostředí načtou do uzlu.

```javascript
require('dotenv').config();
```

### <a name="update-the-router-to-acquire-the-token"></a>Aktualizace směrovače za účelem získání tokenu
Otevřete soubor _routes\index.js_ a nahraďte automaticky generovaný kód následujícím kódem.

Tento kód vytvoří koncový bod rozhraní API, který získá ověřovací token Azure AD pomocí hesla instančního objektu. Také načte subdoménu. Potom vrátí objekt obsahující token a subdoménu.

```javascript
var express = require('express');
var router = express.Router();
var request = require('request');

/* GET home page. */
router.get('/', function(req, res, next) {
  res.render('index', { title: 'Express' });
});

router.get('/GetTokenAndSubdomain', function(req, res) {
    try {
        request.post({
            headers: {
                'content-type': 'application/x-www-form-urlencoded'
            },
            url: `https://login.windows.net/${process.env.TENANT_ID}/oauth2/token`,
            form: {
                grant_type: 'client_credentials',
                client_id: process.env.CLIENT_ID,
                client_secret: process.env.CLIENT_SECRET,
                resource: 'https://cognitiveservices.azure.com/'
            }
        },
        function(err, resp, tokenResult) {
            if (err) {
                console.log(err);
                return res.status(500).send('CogSvcs IssueToken error');
            }

            var tokenResultParsed = JSON.parse(tokenResult);

            if (tokenResultParsed.error) {
                console.log(tokenResult);
                return res.send({error :  "Unable to acquire Azure AD token. Check the debugger for more information."})
            }

            var token = tokenResultParsed.access_token;
            var subdomain = process.env.SUBDOMAIN;
            return res.send({token, subdomain});
        });
    } catch (err) {
        console.log(err);
        return res.status(500).send('CogSvcs IssueToken error');
    }
});

module.exports = router;
```

Koncový bod rozhraní API **GetTokenAndSubdomain** by měl být zabezpečen za nějakou formou ověřování (například [OAuth),](https://oauth.net/2/)aby se zabránilo neoprávněným uživatelům získat tokeny pro použití proti službě Immersive Reader a fakturaci; práce je nad rámec tohoto rychlého startu.

## <a name="add-sample-content"></a>Přidání ukázkového obsahu

Nyní přidáme ukázkový obsah do této webové aplikace. Otevřete _zobrazení\index.pug_ a nahraďte automaticky generovaný kód touto ukázkou:

```pug
doctype html
html
   head
      title Immersive Reader Quickstart Node.js

      link(rel='stylesheet', href='https://stackpath.bootstrapcdn.com/bootstrap/3.4.1/css/bootstrap.min.css')

      // A polyfill for Promise is needed for IE11 support.
      script(src='https://cdn.jsdelivr.net/npm/promise-polyfill@8/dist/polyfill.min.js')

      script(src='https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.1.0.0.js')
      script(src='https://code.jquery.com/jquery-3.3.1.min.js')

      style(type="text/css").
        .immersive-reader-button {
          background-color: white;
          margin-top: 5px;
          border: 1px solid black;
          float: right;
        }
   body
      div(class="container")
        button(class="immersive-reader-button" data-button-style="iconAndText" data-locale="en")

        h1(id="ir-title") About Immersive Reader
        div(id="ir-content" lang="en-us")
          p Immersive Reader is a tool that implements proven techniques to improve reading comprehension for emerging readers, language learners, and people with learning differences. The Immersive Reader is designed to make reading more accessible for everyone. The Immersive Reader

            ul
                li Shows content in a minimal reading view
                li Displays pictures of commonly used words
                li Highlights nouns, verbs, adjectives, and adverbs
                li Reads your content out loud to you
                li Translates your content into another language
                li Breaks down words into syllables

          h3 The Immersive Reader is available in many languages.

          p(lang="es-es") El Lector inmersivo está disponible en varios idiomas.
          p(lang="zh-cn") 沉浸式阅读器支持许多语言
          p(lang="de-de") Der plastische Reader ist in vielen Sprachen verfügbar.
          p(lang="ar-eg" dir="rtl" style="text-align:right") يتوفر \"القارئ الشامل\" في العديد من اللغات.

script(type="text/javascript").
  function getTokenAndSubdomainAsync() {
        return new Promise(function (resolve, reject) {
            $.ajax({
                url: "/GetTokenAndSubdomain",
                type: "GET",
                success: function (data) {
                    if (data.error) {
                        reject(data.error);
                    } else {
                        resolve(data);
                    }
                },
                error: function (err) {
                    reject(err);
                }
            });
        });
    }

    $(".immersive-reader-button").click(function () {
        handleLaunchImmersiveReader();
    });

    function handleLaunchImmersiveReader() {
        getTokenAndSubdomainAsync()
            .then(function (response) {
                const token = response["token"];
                const subdomain = response["subdomain"];
                // Learn more about chunk usage and supported MIME types https://docs.microsoft.com/azure/cognitive-services/immersive-reader/reference#chunk
                const data = {
                    title: $("#ir-title").text(),
                    chunks: [{
                        content: $("#ir-content").html(),
                        mimeType: "text/html"
                    }]
                };
                // Learn more about options https://docs.microsoft.com/azure/cognitive-services/immersive-reader/reference#options
                const options = {
                    "onExit": exitCallback,
                    "uiZIndex": 2000
                };
                ImmersiveReader.launchAsync(token, subdomain, data, options)
                    .catch(function (error) {
                        alert("Error in launching the Immersive Reader. Check the console.");
                        console.log(error);
                    });
            })
            .catch(function (error) {
                alert("Error in getting the Immersive Reader token and subdomain. Check the console.");
                console.log(error);
            });
    }

    function exitCallback() {
        console.log("This is the callback function. It is executed when the Immersive Reader closes.");
    }
```


Všimněte si, že celý text má **atribut lang,** který popisuje jazyky textu. Tento atribut pomáhá immersive Reader poskytnout příslušné funkce jazyka a gramatiky.

## <a name="build-and-run-the-app"></a>Sestavení a spuštění aplikace

Naše webová aplikace je nyní připravena. Spuštění aplikace spuštěním:

```bash
npm start
```

Otevřete prohlížeč a _http://localhost:3000_přejděte na . Měli byste vidět následující:

![Ukázková aplikace](./media/quickstart-nodejs/1-buildapp.png)

## <a name="launch-the-immersive-reader"></a>Spuštění immersive Reader

Když kliknete na tlačítko "Immersive Reader", uvidíte Immersive Reader zahájena s obsahem na stránce.

![Asistivní čtečka](./media/quickstart-nodejs/2-viewimmersivereader.png)

## <a name="next-steps"></a>Další kroky

* Seznamte se s [sadou Immersive Reader SDK](https://github.com/microsoft/immersive-reader-sdk) a [referenční sadou Immersive Reader SDK](./reference.md)