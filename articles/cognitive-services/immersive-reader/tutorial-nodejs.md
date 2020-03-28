---
title: 'Kurz: Spuštění immersive Reader pomocí Node.js'
titleSuffix: Azure Cognitive Services
description: V tomto kurzu vytvoříte aplikaci Node.js, která spustí immersive Reader.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 01/14/2020
ms.author: metan
ms.openlocfilehash: 139dd2ebdabbc91a6de3b0a1eb921b110d47c3f3
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76842023"
---
# <a name="tutorial-launch-the-immersive-reader-nodejs"></a>Kurz: Spuštění immersive Reader (Node.js)

V [přehledu](./overview.md)jste se dozvěděli o tom, co je Immersive Reader a jak implementuje osvědčené techniky ke zlepšení porozumění čtení pro studenty jazyků, začínající čtenáře a studenty s rozdíly v učení. Tento kurz popisuje, jak vytvořit webovou aplikaci Node.js, která spustí immersive Reader. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření webové aplikace Node.js pomocí expressu
> * Získání přístupového tokenu
> * Spuštění čtečky Immersive Reader s ukázkovým obsahem
> * Určení jazyka obsahu
> * Určení jazyka rozhraní Immersive Reader
> * Spuštění immersive Reader s matematickým obsahem

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="prerequisites"></a>Požadavky

* Prostředek immersive Reader nakonfigurovaný pro ověřování Azure Active Directory. Podle [těchto pokynů](./how-to-create-immersive-reader.md) se připravte. Při konfiguraci vlastností prostředí budete potřebovat některé hodnoty vytvořené zde. Uložte výstup relace do textového souboru pro budoucí použití.
* [Node.js](https://nodejs.org/) a [příze](https://yarnpkg.com)
* IDE, jako je [například visual studio kód](https://code.visualstudio.com/)

## <a name="create-a-nodejs-web-app-with-express"></a>Vytvoření webové aplikace Node.js pomocí expressu

Vytvořte pomocí `express-generator` nástroje webovou aplikaci Node.js.

```bash
npm install express-generator -g
express --view=pug myapp
cd myapp
```

Nainstalujte závislosti příze a přidejte `dotenv`závislosti a , které budou použity `request` později v kurzu.

```bash
yarn
yarn add request
yarn add dotenv
```

## <a name="acquire-an-azure-ad-authentication-token"></a>Získání ověřovacího tokenu Azure AD

Dále zapište back-endové rozhraní API pro načtení ověřovacího tokenu Azure AD.

Potřebujete některé hodnoty z předpokladu konfigurace Azure AD předpoklad u této části. Vraťte se k textovému souboru, který jste uložili z této relace.

````text
TenantId     => Azure subscription TenantId
ClientId     => Azure AD ApplicationId
ClientSecret => Azure AD Application Service Principal password
Subdomain    => Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')
````

Jakmile budete mít tyto hodnoty, vytvořte nový soubor s názvem _.env_a vložte do něj následující kód a zadejte hodnoty vlastních vlastností shora. Nezahrnejte uvozovky ani znaky {a "}".

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

Otevřete soubor _routes\index.js_ a nahraďte jeho obsah následujícím kódem.

Tento kód vytvoří koncový bod rozhraní API, který získá ověřovací token Azure AD pomocí hesla instančního objektu. Také načte subdoménu. Potom vrátí objekt obsahující token a subdoménu.

```javascript
var request = require('request');
var express = require('express');
var router = express.Router();

router.get('/getimmersivereaderlaunchparams', function(req, res) {
    request.post ({
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
        function(err, resp, tokenResponse) {
                if (err) {
                    return res.status(500).send('CogSvcs IssueToken error');
                }

                const token = JSON.parse(tokenResponse).access_token;
                const subdomain = process.env.SUBDOMAIN;
                return res.send({token: token, subdomain: subdomain});
        }
  );
});

/* GET home page. */
router.get('/', function(req, res, next) {
  res.render('index', { title: 'Express' });
});

module.exports = router;

```

Koncový bod rozhraní API **getimmersivereaderlaunchparams** by měl být zabezpečen za nějakou formou ověřování (například [OAuth),](https://oauth.net/2/)aby se zabránilo neoprávněným uživatelům získat tokeny pro použití proti službě Immersive Reader a fakturaci; že práce je nad rámec tohoto kurzu.

## <a name="launch-the-immersive-reader-with-sample-content"></a>Spuštění čtečky Immersive Reader s ukázkovým obsahem

1. Otevřete _zobrazení\layout.pug_a před `head` `body` značku přidejte následující kód pod značku. Tyto `script` značky načítají [sadu Immersive Reader SDK](https://github.com/microsoft/immersive-reader-sdk) a jQuery.

    ```pug
    script(src='https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.0.0.2.js')
    script(src='https://code.jquery.com/jquery-3.3.1.min.js')
    ```

2. Otevřete _zobrazení\index.pug_a nahraďte jeho obsah následujícím kódem. Tento kód naplní stránku s některé ukázkový obsah a přidá tlačítko, které spustí Immersive Reader.

    ```pug
    extends layout

    block content
          h2(id='title') Geography
          p(id='content') The study of Earth's landforms is called physical geography. Landforms can be mountains and valleys. They can also be glaciers, lakes or rivers.
          div(class='immersive-reader-button' data-button-style='iconAndText' data-locale='en-US' onclick='launchImmersiveReader()')
          script.

            function getImmersiveReaderLaunchParamsAsync() {
                    return new Promise((resolve, reject) => {
                        $.ajax({
                                url: '/getimmersivereaderlaunchparams',
                                type: 'GET',
                                success: data => {
                                        resolve(data);
                                },
                                error: err => {
                                        console.log('Error in getting token and subdomain!', err);
                                        reject(err);
                                }
                        });
                    });
            }

            async function launchImmersiveReader() {
                    const content = {
                            title: document.getElementById('title').innerText,
                            chunks: [{
                                    content: document.getElementById('content').innerText + '\n\n',
                                    lang: 'en'
                            }]
                    };

                    const launchParams = await getImmersiveReaderLaunchParamsAsync();
                    const token = launchParams.token;
                    const subdomain = launchParams.subdomain;

                    ImmersiveReader.launchAsync(token, subdomain, content);
            }
    ```

3. Naše webová aplikace je nyní připravena. Spuštění aplikace spuštěním:

    ```bash
    npm start
    ```

4. Otevřete prohlížeč a _http://localhost:3000_přejděte na . Měli byste vidět výše uvedený obsah na stránce. Kliknutím na tlačítko **Immersive Reader** spusťte immersive Reader s vaším obsahem.

## <a name="specify-the-language-of-your-content"></a>Určení jazyka obsahu

Immersive Reader má podporu pro mnoho různých jazyků. Jazyk obsahu můžete určit podle následujících kroků.

1. Otevřete _zobrazení\index.pug_ a přidejte `p(id=content)` následující kód pod značku, kterou jste přidali v předchozím kroku. Tento kód přidá na stránku nějaký obsah španělského obsahu.

    ```pug
    p(id='content-spanish') El estudio de las formas terrestres de la Tierra se llama geografía física. Los accidentes geográficos pueden ser montañas y valles. También pueden ser glaciares, lagos o ríos.
    ```

2. V kódu Jazyka JavaScript přidejte následující `ImmersiveReader.launchAsync`nad voláním do . Tento kód předává španělský obsah do immersive Reader.

    ```pug
    content.chunks.push({
      content: document.getElementById('content-spanish').innerText + '\n\n',
      lang: 'es'
    });
    ```

3. Přejděte _http://localhost:3000_ znovu. Měli byste vidět španělský text na stránce, a když kliknete na **Immersive Reader**, to se objeví v Immersive Reader stejně.

## <a name="specify-the-language-of-the-immersive-reader-interface"></a>Určení jazyka rozhraní Immersive Reader

Ve výchozím nastavení se jazyk rozhraní Immersive Reader shoduje s nastavením jazyka prohlížeče. Můžete také zadat jazyk rozhraní Immersive Reader s následujícím kódem.

1. V _zobrazení\index.pug_nahraďte `ImmersiveReader.launchAsync(token, subdomain, content)` volání níže uvedeným kódem.

    ```javascript
    const options = {
        uiLang: 'fr',
    }
    ImmersiveReader.launchAsync(token, subdomain, content, options);
    ```

2. Přejděte _http://localhost:3000_na . Při spuštění immersive Reader, rozhraní se zobrazí ve francouzštině.

## <a name="launch-the-immersive-reader-with-math-content"></a>Spuštění immersive Reader s matematickým obsahem

Matematický obsah můžete zahrnout do immersive Reader pomocí [MathML](https://developer.mozilla.org/en-US/docs/Web/MathML).

1. Upravte _zobrazení\index.pug_ tak, aby obsahoval `ImmersiveReader.launchAsync`a následující kód nad voláním :

    ```javascript
    const mathML = '<math xmlns="https://www.w3.org/1998/Math/MathML" display="block"> \
      <munderover> \
        <mo>∫</mo> \
        <mn>0</mn> \
        <mn>1</mn> \
      </munderover> \
      <mrow> \
        <msup> \
          <mi>x</mi> \
          <mn>2</mn> \
        </msup> \
        <mo>ⅆ</mo> \
        <mi>x</mi> \
      </mrow> \
    </math>';

    content.chunks.push({
      content: mathML,
      mimeType: 'application/mathml+xml'
    });
    ```

2. Přejděte _http://localhost:3000_na . Když spustíte immersive Reader a posunete se dolů, uvidíte matematický vzorec.

## <a name="next-steps"></a>Další kroky

* Seznamte se s [sadou Immersive Reader SDK](https://github.com/microsoft/immersive-reader-sdk) a [referenční sadou Immersive Reader SDK](./reference.md)
* Zobrazení ukázek kódu na [GitHubu](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/advanced-csharp)
