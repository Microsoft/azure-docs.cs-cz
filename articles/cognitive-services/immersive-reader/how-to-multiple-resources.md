---
title: Integrace více prostředků moderního čtecího zařízení
titleSuffix: Azure Cognitive Services
description: V tomto kurzu vytvoříte aplikaci Node.js, která spouští moderní čtečku s využitím více prostředků moderního čtecího zařízení.
author: skamal
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: how-to
ms.date: 01/14/2020
ms.author: skamal
ms.custom: devx-track-js
ms.openlocfilehash: dc6c7bd9e38aa21ab10f55fca2500b0c12194d43
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102620017"
---
# <a name="integrate-multiple-immersive-reader-resources"></a>Integrace více prostředků moderního čtecího zařízení

V tomto [přehledu](./overview.md)jste se dozvěděli o tom, co je moderní čtečka a jak implementuje osvědčené techniky pro zlepšení porozumění čtení pro jazykové učení, vznikající čtenáři a studenty s rozdíly v učení. V [rychlém](./quickstarts/client-libraries.md)startu jste zjistili, jak používat moderní čtečku s jedním prostředkem. V tomto kurzu se dozvíte, jak integrovat více prostředků moderního čtecího zařízení do stejné aplikace. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření více prostředků moderního čtecího zařízení v existující skupině prostředků
> * Spuštění moderního čtečky s využitím několika prostředků

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/cognitive-services/).

## <a name="prerequisites"></a>Požadavky

* Postupujte podle pokynů v [rychlém](./quickstarts/client-libraries.md?pivots=programming-language-nodejs) startu a vytvořte webovou aplikaci, která spustí moderní čtečku pomocí NodeJS. V tomto rychlém startu nakonfigurujete jeden prostředek pro moderní čtečku. V tomto kurzu se budeme sestavovat nahoru.

## <a name="create-the-immersive-reader-resources"></a>Vytvoření prostředků pro moderní čtečku

Podle [těchto pokynů](./how-to-create-immersive-reader.md) vytvořte každý prostředek pro moderní čtečku. Ve skriptu **Create-ImmersiveReaderResource** jsou `ResourceName` `ResourceSubdomain` parametry, a `ResourceLocation` . Ty by měly být pro každý vytvořený prostředek jedinečné. Zbývající parametry by měly být stejné jako ty, které jste použili při nastavování prvního prostředku pro moderní čtečku. Tímto způsobem je možné každý prostředek propojit se stejnou skupinou prostředků Azure a aplikací Azure AD.

Následující příklad ukazuje, jak vytvořit dva prostředky, jeden v WestUS a druhý v EastUS. Všimněte si, že jedinečné hodnoty pro `ResourceName` , `ResourceSubdomain` a `ResourceLocation` .

```azurepowershell-interactive
Create-ImmersiveReaderResource
  -SubscriptionName <SUBSCRIPTION_NAME> `
  -ResourceName Resource_name_wus `
  -ResourceSubdomain resource-subdomain-wus `
  -ResourceSKU <RESOURCE_SKU> `
  -ResourceLocation westus `
  -ResourceGroupName <RESOURCE_GROUP_NAME> `
  -ResourceGroupLocation <RESOURCE_GROUP_LOCATION> `
  -AADAppDisplayName <AAD_APP_DISPLAY_NAME> `
  -AADAppIdentifierUri <AAD_APP_IDENTIFIER_URI> `
  -AADAppClientSecret <AAD_APP_CLIENT_SECRET>

Create-ImmersiveReaderResource
  -SubscriptionName <SUBSCRIPTION_NAME> `
  -ResourceName Resource_name_eus `
  -ResourceSubdomain resource-subdomain-eus `
  -ResourceSKU <RESOURCE_SKU> `
  -ResourceLocation eastus `
  -ResourceGroupName <RESOURCE_GROUP_NAME> `
  -ResourceGroupLocation <RESOURCE_GROUP_LOCATION> `
  -AADAppDisplayName <AAD_APP_DISPLAY_NAME> `
  -AADAppIdentifierUri <AAD_APP_IDENTIFIER_URI> `
  -AADAppClientSecret <AAD_APP_CLIENT_SECRET>
```

## <a name="add-resources-to-environment-configuration"></a>Přidat prostředky do konfigurace prostředí

V rychlém startu jste vytvořili konfigurační soubor prostředí, který obsahuje `TenantId` parametry, `ClientId` , `ClientSecret` a `Subdomain` . Vzhledem k tomu, že všechny vaše prostředky používají stejnou aplikaci Azure AD, můžeme použít stejné hodnoty pro `TenantId` , `ClientId` a `ClientSecret` . Jedinou změnou, kterou je třeba udělat, je vypsat každou subdoménu pro každý prostředek.

Nový soubor __. env__ by teď měl vypadat nějak takto:

```text
TENANT_ID={YOUR_TENANT_ID}
CLIENT_ID={YOUR_CLIENT_ID}
CLIENT_SECRET={YOUR_CLIENT_SECRET}
SUBDOMAIN_WUS={YOUR_WESTUS_SUBDOMAIN}
SUBDOMAIN_EUS={YOUR_EASTUS_SUBDOMAIN}
```

Ujistěte se, že tento soubor nechcete potvrdit do správy zdrojových kódů, protože obsahuje tajné klíče, které by neměly být zveřejněny.

V dalším kroku upravíte soubor  _routes\index.js_ , který jsme vytvořili pro podporu našich více prostředků. Nahraďte jeho obsah následujícím kódem.

Stejně jako dřív tento kód vytvoří koncový bod rozhraní API, který získá ověřovací token Azure AD pomocí vašeho hesla instančního objektu. Tentokrát umožňuje uživateli zadat umístění prostředku a předat ho jako parametr dotazu. Pak vrátí objekt obsahující token a odpovídající subdoménu.

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

            var subdomain = "";
            var region = req.query && req.query.region;
            switch (region) {
                case "eus":
                    subdomain = process.env.SUBDOMAIN_EUS
                    break;
                case "wus":
                default:
                    subdomain = process.env.SUBDOMAIN_WUS
            }

            return res.send({token, subdomain});
        });
    } catch (err) {
        console.log(err);
        return res.status(500).send('CogSvcs IssueToken error');
    }
});

module.exports = router;
```

Koncový bod rozhraní API **getimmersivereaderlaunchparams** by měl být zabezpečený za určitou formou ověřování (například [OAuth](https://oauth.net/2/)), aby se zabránilo neoprávněným uživatelům ve získávání tokenů k použití proti vaší službě s moderní čtečkou a fakturaci. Tato práce překračuje rámec tohoto kurzu.

## <a name="launch-the-immersive-reader-with-sample-content"></a>Spuštění moderního čtecího zařízení s ukázkovým obsahem

1. Otevřete _views\index.pug_ a nahraďte jeho obsah následujícím kódem. Tento kód naplní stránku pomocí nějakého ukázkového obsahu a přidá dvě tlačítka, která spustí moderní čtecí zařízení. Jednu pro spuštění moderního čtecího zařízení pro prostředek EastUS a další pro prostředek WestUS.

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
                button(class="immersive-reader-button" data-button-style="icon" data-locale="en" onclick='handleLaunchImmersiveReader("wus")') WestUS Immersive Reader
                button(class="immersive-reader-button" data-button-style="icon" data-locale="en" onclick='handleLaunchImmersiveReader("eus")') EastUS Immersive Reader

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
    function getTokenAndSubdomainAsync(region) {
            return new Promise(function (resolve, reject) {
                $.ajax({
                    url: "/GetTokenAndSubdomain",
                    type: "GET",
                    data: {
                        region: region
                    },
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

        function handleLaunchImmersiveReader(region) {
            getTokenAndSubdomainAsync(region)
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

3. Naše webová aplikace je teď připravená. Spusťte aplikaci spuštěním:

    ```bash
    npm start
    ```

4. Otevřete prohlížeč a přejděte na `http://localhost:3000` . Na stránce byste měli vidět výše uvedený obsah. Klikněte na tlačítko **EastUS pro moderní čtení** nebo na tlačítko **WestUS pro moderní čtení** a spusťte moderní čtečku s využitím příslušných prostředků.

## <a name="next-steps"></a>Další kroky

* Prozkoumejte [sadu moderních čtenářů](https://github.com/microsoft/immersive-reader-sdk) a [referenční materiály k sadě pro moderní čtečku](./reference.md)
* Zobrazit ukázky kódu na [GitHubu](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/advanced-csharp)