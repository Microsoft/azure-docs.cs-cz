---
title: Rychlý Start Node.js klientské knihovny pro moderní čtečku
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu vytvoříte webovou aplikaci od začátku a přidáte funkce rozhraní API pro moderní čtečku.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: include
ms.date: 09/14/2020
ms.author: nitinme
ms.custom: devx-track-js
ms.openlocfilehash: ffef03457f9d3faf385bd0a924474dfa6efc4121
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102619203"
---
[Moderní čtečka](https://www.onenote.com/learningtools) je často navržený nástroj, který implementuje osvědčené techniky pro zlepšení porozumění čtení pro nové čtenáře, jazyky jazyků a lidi s rozdíly v učení, jako je dyslexia. Pomocí moderního čtecího zařízení ve svých aplikacích můžete izolovat text a vylepšit tak fokus, zobrazit obrázky pro běžně používaná slova, zvýraznit části řeči, číst vybraný text hlasitě, překládat slova a fráze v reálném čase a další.

V tomto rychlém startu vytvoříte webovou aplikaci od začátku a integrujete moderní čtečku pomocí klientské knihovny pro moderní čtenáře. Kompletní pracovní vzorek tohoto rychlého startu je k dispozici [na GitHubu](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/quickstart-nodejs).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/cognitive-services) .
* Prostředek moderního čtecího zařízení nakonfigurovaný pro ověřování Azure Active Directory. Pomocí [těchto pokynů](../../how-to-create-immersive-reader.md) si můžete nastavit. Při konfiguraci vlastností prostředí budete potřebovat některé z hodnot, které jsou zde vytvořeny. Uložte výstup vaší relace do textového souboru pro budoucí referenci.
* [Node.js](https://nodejs.org/) a [příze](https://yarnpkg.com)
* Rozhraní IDE, jako je například [Visual Studio Code](https://code.visualstudio.com/)

## <a name="create-a-nodejs-web-app-with-express"></a>Vytvoření Node.js webové aplikace pomocí Expressu

Pomocí nástroje vytvořte webovou aplikaci v Node.js `express-generator` .

```bash
npm install express-generator -g
express --view=pug quickstart-nodejs
cd quickstart-nodejs
```

Nainstalujte závislosti příze a přidejte závislosti `request` a `dotenv` , které se použijí později v rychlém startu.

```bash
yarn
yarn add request
yarn add dotenv
```

## <a name="set-up-authentication"></a>Nastavení ověřování

### <a name="configure-authentication-values"></a>Konfigurovat hodnoty ověřování

V kořenovém adresáři projektu vytvořte nový soubor s názvem _. env_ . Vložte do něj následující kód a zadejte hodnoty, které jste zadali při vytváření prostředku pro moderní čtečku.
Nezahrnujte uvozovky nebo znaky "{" a "}".

```text
TENANT_ID={YOUR_TENANT_ID}
CLIENT_ID={YOUR_CLIENT_ID}
CLIENT_SECRET={YOUR_CLIENT_SECRET}
SUBDOMAIN={YOUR_SUBDOMAIN}
```

Ujistěte se, že tento soubor nechcete potvrdit do správy zdrojových kódů, protože obsahuje tajné klíče, které by neměly být zveřejněny.

Dále otevřete _app.js_ a na začátek souboru přidejte následující. Tím se načte vlastnosti definované v souboru. env jako proměnné prostředí do uzlu.

```javascript
require('dotenv').config();
```

### <a name="update-the-router-to-acquire-the-token"></a>Aktualizace směrovače pro získání tokenu
Otevřete soubor _routes\index.js_ a nahraďte automaticky generovaný kód následujícím kódem.

Tento kód vytvoří koncový bod rozhraní API, který získá ověřovací token Azure AD pomocí vašeho hesla instančního objektu. Také načte subdoménu. Pak vrátí objekt obsahující token a subdoménu.

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

Koncový bod rozhraní API **GetTokenAndSubdomain** by měl být zabezpečený za určitou formou ověřování (například [OAuth](https://oauth.net/2/)), aby se zabránilo neoprávněným uživatelům ve získávání tokenů k použití proti vaší službě s moderní čtečkou a fakturaci. Tato práce překračuje rámec tohoto rychlého startu.

## <a name="add-sample-content"></a>Přidat ukázkový obsah

Nyní přidáme do této webové aplikace vzorový obsah. Otevřete _views\index.pug_ a nahraďte automaticky generovaný kód touto ukázkou:

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


Všimněte si, že veškerý text má atribut **lang** , který popisuje jazyky textu. Tento atribut pomáhá modernímu čtečce poskytovat relevantní jazykové a gramatické funkce.

## <a name="build-and-run-the-app"></a>Sestavte a spusťte aplikaci.

Naše webová aplikace je teď připravená. Spusťte aplikaci spuštěním:

```bash
npm start
```

Otevřete prohlížeč a přejděte na _http://localhost:3000_ . Měli byste vidět následující:

![Ukázková aplikace – Node.js](../../media/quickstart-nodejs/1-buildapp.png)

## <a name="launch-the-immersive-reader"></a>Spuštění moderního čtecího zařízení

Po kliknutí na tlačítko "moderní čtečka" se zobrazí moderní čtečka, která se spustí s obsahem na stránce.

![Moderní čtečka – Node.js](../../media/quickstart-nodejs/2-viewimmersivereader.png)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření prostředku a konfigurace AAD](../../how-to-create-immersive-reader.md)