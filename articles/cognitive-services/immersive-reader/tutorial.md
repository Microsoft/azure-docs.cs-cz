---
title: 'Kurz: Spusťte atraktivní čtečky pomocí Node.js'
titleSuffix: Azure Cognitive Services
description: V tomto kurzu vytvoříte aplikaci Node.js, které spouští atraktivní čtečky.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: f8697042ed46e0ff333f736454346908d76cf039
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2019
ms.locfileid: "67718372"
---
# <a name="tutorial-launch-the-immersive-reader-nodejs"></a>Kurz: Spuštění Asistivní čtečky (Node.js)

V [přehled](./overview.md), jste se dozvěděli o tom, co je atraktivní čtečky a způsob implementace osvědčené techniky zlepšit míru porozumění čtení pro jazyk inteligentních algoritmů, nově vznikající čtečky a studentům učení rozdíly. Tento kurz obsahuje postup pro vytvoření webové aplikace Node.js, která spustí atraktivní čtečky. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření webové aplikace v Node.js s Express
> * Získání přístupového tokenu
> * Spusťte atraktivní čtečky s ukázkovým obsahem
> * Zadejte jazyk obsahu
> * Zadejte jazyk rozhraní atraktivní čtecího zařízení
> * Spusťte atraktivní čtečky s obsahem math

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

* Klíč předplatného pro atraktivní čtecí zařízení. Získejte ji pomocí následujících [tyto pokyny](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account).
* [Node.js](https://nodejs.org/) a [Yarn](https://yarnpkg.com)
* Integrované vývojové prostředí, jako [Visual Studio Code](https://code.visualstudio.com/)

## <a name="create-a-nodejs-web-app-with-express"></a>Vytvoření webové aplikace v Node.js s Express

Vytvoření webové aplikace v Node.js s `express-generator` nástroj.

```bash
npm install express-generator -g
express --view=pug myapp
cd myapp
```

Instalace závislostí yarn a přidat závislosti `request` a `dotenv`, který se použije v pozdější části kurzu.

```bash
yarn
yarn add request
yarn add dotenv
```

## <a name="acquire-an-access-token"></a>Získání přístupového tokenu

Dále napište back-endového rozhraní API k načtení přístupového tokenu pomocí váš klíč předplatného. Budete potřebovat předplatné key a koncového bodu pro tento další krok. Váš klíč předplatného najdete na stránce klíče atraktivní čtečky prostředku na webu Azure Portal. Vyhledání vašeho koncového bodu na stránce Přehled.

Jakmile budete mít klíč předplatného a koncového bodu, vytvořte nový soubor s názvem _.env_a vložte následující kód do ní nahrazení `{YOUR_SUBSCRIPTION_KEY}` a `{YOUR_ENDPOINT}` se váš klíč předplatného a koncový bod, v uvedeném pořadí.

```text
SUBSCRIPTION_KEY={YOUR_SUBSCRIPTION_KEY}
ENDPOINT={YOUR_ENDPOINT}
```

Se ujistěte, že potvrzení tohoto souboru do správy zdrojového kódu, protože obsahuje tajné kódy, které by neměly být zveřejněny.

Dále otevřete _app.js_ a na začátek souboru přidejte následující. Tento kód načte klíč předplatného a koncový bod jako proměnné prostředí do uzlu.

```javascript
require('dotenv').config();
```

Otevřít _routes\index.js_ Souborová služba a následující importu v horní části souboru:

```javascript
var request = require('request');
```

Dále přidejte následující kód přímo pod tento řádek. Tento kód vytvoří koncový bod rozhraní API, která získá přístupový token pomocí váš klíč předplatného a potom vrátí token.

```javascript
router.get('/token', function(req, res, next) {
  request.post({
    headers: {
        'Ocp-Apim-Subscription-Key': process.env.SUBSCRIPTION_KEY,
        'content-type': 'application/x-www-form-urlencoded'
    },
    url: process.env.ENDPOINT
  },
  function(err, resp, token) {
    return res.send(token);
  });
});
```

Tento koncový bod rozhraní API by měl být zabezpečený za nějakou formu ověřování (například [OAuth](https://oauth.net/2/)); že práce je nad rámec tohoto kurzu.

## <a name="launch-the-immersive-reader-with-sample-content"></a>Spusťte atraktivní čtečky s ukázkovým obsahem

1. Otevřít _views\layout.pug_a přidejte následující kód `head` označit, než `body` značky. Tyto `script` načíst značky [atraktivní SDK čtečky](https://github.com/Microsoft/immersive-reader-sdk) a knihovnu jQuery.

    ```pug
    script(src='https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.0.0.1.js')
    script(src='https://code.jquery.com/jquery-3.3.1.min.js')
    ```

2. Otevřít _views\index.pug_a nahraďte jeho obsah následujícím kódem. Tento kód naplní stránku obsahem některé ukázky a přidá tlačítko, které spustí atraktivní čtečky.

    ```pug
    extends layout

    block content
      h2(id='title') Geography
      p(id='content') The study of Earth's landforms is called physical geography. Landforms can be mountains and valleys. They can also be glaciers, lakes or rivers.
      div(class='immersive-reader-button' data-button-style='iconAndText' data-locale='en-US' onclick='launchImmersiveReader()')
      script.
        function launchImmersiveReader() {
          // First, get a token using our /token endpoint
          $.ajax('/token', { success: token => {
            // Second, grab the content from the page
            const content = {
              title: document.getElementById('title').innerText,
              chunks: [ {
                content: document.getElementById('content').innerText + '\n\n',
                lang: 'en'
              } ]
            };

            // Third, launch the Immersive Reader
            ImmersiveReader.launchAsync(token, content);
          }});
        }
    ```

3. Naše webové aplikace je teď připravený. Spusťte aplikaci spuštěním:

    ```bash
    npm start
    ```

4. Otevřete prohlížeč a přejděte do _http://localhost:3000_ . Na stránce, měli byste vidět na obsah výše. Klikněte na tlačítko **atraktivní čtečky** tlačítko Spustit atraktivní čtečky s vaším obsahem.

## <a name="specify-the-language-of-your-content"></a>Zadejte jazyk obsahu

Dokonalé čtečky nabízí podporu mnoha různých jazyků. Pomocí následujícího postupu můžete určit jazyk obsahu.

1. Otevřít _views\index.pug_ a přidejte následující kód uvedený níže `p(id=content)` značku, kterou jste přidali v předchozím kroku. Tento kód přidá nějaký obsah Španělština obsah na stránku.

    ```pug
    p(id='content-spanish') El estudio de las formas terrestres de la Tierra se llama geografía física. Los accidentes geográficos pueden ser montañas y valles. También pueden ser glaciares, lagos o ríos.
    ```

2. V kódu jazyka JavaScript přidejte následující kód nad volání `ImmersiveReader.launchAsync`. Tento kód předá Španělština obsah do atraktivní čtečky.

    ```pug
    content.chunks.push({
      content: document.getElementById('content-spanish').innerText + '\n\n',
      lang: 'es'
    });
    ```

3. Přejděte do _http://localhost:3000_ znovu. Byste měli vidět Španělština text na stránce, a po kliknutí na **atraktivní čtečky**, se zobrazí v moderním Reader.

## <a name="specify-the-language-of-the-immersive-reader-interface"></a>Zadejte jazyk rozhraní atraktivní čtecího zařízení

Ve výchozím nastavení jazyk rozhraní atraktivní Reader odpovídá nastavení jazyka prohlížeče. Můžete také určit jazyk rozhraní atraktivní čtečky následujícím kódem.

1. V _views\index.pug_, nahraďte volání `ImmersiveReader.launchAsync(token, content)` s následujícím kódem.

    ```javascript
    const options = {
        uiLang: 'fr',
    }
    ImmersiveReader.launchAsync(token, content, options);
    ```

2. Přejděte do _http://localhost:3000_ . Při spuštění atraktivní čtečky rozhraní se nezobrazí ve francouzštině.

## <a name="launch-the-immersive-reader-with-math-content"></a>Spusťte atraktivní čtečky s obsahem math

Můžete zahrnout obsah matematické atraktivní čtečky pomocí [MathML](https://developer.mozilla.org/en-US/docs/Web/MathML).

1. Upravit _views\index.pug_ zahrnout následující kód nad volání `ImmersiveReader.launchAsync`:

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

2. Přejděte do _http://localhost:3000_ . Když spustíte atraktivní čtečky a přejděte do dolní části, zobrazí se vám matematické vzorce.

## <a name="next-steps"></a>Další postup

* Prozkoumejte [atraktivní čtečky SDK](https://github.com/Microsoft/immersive-reader-sdk) a [atraktivní čtečky odkaz na sadu SDK](./reference.md)
* Zobrazit ukázky kódu na [Githubu](https://github.com/microsoft/immersive-reader-sdk/samples/advanced-csharp)