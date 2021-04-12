---
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: include
ms.date: 03/04/2021
ms.author: erhopf
ms.openlocfilehash: 1b0d929e667023640906baa87a2fd25c61c5d488
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102620025"
---
## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/cognitive-services) .
* Prostředek moderního čtecího zařízení nakonfigurovaný pro ověřování Azure Active Directory. Pomocí [těchto pokynů](../../how-to-create-immersive-reader.md) si můžete nastavit.  Při konfiguraci vlastností prostředí budete potřebovat některé z hodnot, které jsou zde vytvořeny. Uložte výstup vaší relace do textového souboru pro budoucí referenci.
* [Node.js](https://nodejs.org/) a [příze](https://yarnpkg.com)
* Rozhraní IDE, jako je například [Visual Studio Code](https://code.visualstudio.com/)

## <a name="create-a-nodejs-web-app-with-express"></a>Vytvoření Node.js webové aplikace pomocí Expressu

Pomocí nástroje vytvořte webovou aplikaci v Node.js `express-generator` .

```bash
npm install express-generator -g
express --view=pug myapp
cd myapp
```

Nainstalujte závislosti příze a přidejte závislosti `request` a `dotenv` , které se použijí později v tomto kurzu.

```bash
yarn
yarn add request
yarn add dotenv
```

## <a name="acquire-an-azure-ad-authentication-token"></a>Získání ověřovacího tokenu Azure AD

Potom napíšete back-end rozhraní API pro načtení ověřovacího tokenu Azure AD.

Pro tuto část potřebujete od výše uvedeného kroku požadavků konfigurace ověřování Azure AD nějaké hodnoty. Vraťte se zpět k textovému souboru, který jste si uložili do této relace.

````text
TenantId     => Azure subscription TenantId
ClientId     => Azure AD ApplicationId
ClientSecret => Azure AD Application Service Principal password
Subdomain    => Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')
````

Jakmile máte tyto hodnoty, vytvořte nový soubor s názvem _. env_ a vložte do něj následující kód a zadejte hodnoty vlastních vlastností z výše uvedeného. Nezahrnujte uvozovky nebo znaky "{" a "}".

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

Otevřete soubor _routes\index.js_ a nahraďte jeho obsah následujícím kódem.

Tento kód vytvoří koncový bod rozhraní API, který získá ověřovací token Azure AD pomocí vašeho hesla instančního objektu. Také načte subdoménu. Pak vrátí objekt obsahující token a subdoménu.

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

Koncový bod rozhraní API **getimmersivereaderlaunchparams** by měl být zabezpečený za určitou formou ověřování (například [OAuth](https://oauth.net/2/)), aby se zabránilo neoprávněným uživatelům ve získávání tokenů k použití proti vaší službě s moderní čtečkou a fakturaci. Tato práce překračuje rámec tohoto kurzu.

## <a name="launch-the-immersive-reader-with-sample-content"></a>Spuštění moderního čtecího zařízení s ukázkovým obsahem

1. Otevřete _views\layout.pug_ a přidejte následující kód pod `head` značku před `body` značku. Tyto `script` značky načtou [sadu pro moderní čtečku](https://github.com/microsoft/immersive-reader-sdk) a jQuery.

    ```pug
    script(src='https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.0.0.2.js')
    script(src='https://code.jquery.com/jquery-3.3.1.min.js')
    ```

2. Otevřete _views\index.pug_ a nahraďte jeho obsah následujícím kódem. Tento kód naplní stránku nějakým ukázkovým obsahem a přidá tlačítko, které spustí moderní čtečku.

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

3. Naše webová aplikace je teď připravená. Spusťte aplikaci spuštěním:

    ```bash
    npm start
    ```

4. Otevřete prohlížeč a přejděte na _http://localhost:3000_ . Na stránce byste měli vidět výše uvedený obsah. Kliknutím na tlačítko pro **moderní čtečku** spustíte moderní čtečku s vaším obsahem.

## <a name="specify-the-language-of-your-content"></a>Zadejte jazyk vašeho obsahu

Moderní čtečka podporuje řadu různých jazyků. Jazyk vašeho obsahu můžete určit podle následujících kroků.

1. Otevřete _views\index.pug_ a přidejte následující kód pod `p(id=content)` značku, kterou jste přidali v předchozím kroku. Tento kód přidá do stránky obsah pro španělštinu obsahu.

    ```pug
    p(id='content-spanish') El estudio de las formas terrestres de la Tierra se llama geografía física. Los accidentes geográficos pueden ser montañas y valles. También pueden ser glaciares, lagos o ríos.
    ```

2. V kódu JavaScriptu přidejte následující nad volání `ImmersiveReader.launchAsync` . Tento kód předá španělský obsah do moderního čtecího zařízení.

    ```pug
    content.chunks.push({
      content: document.getElementById('content-spanish').innerText + '\n\n',
      lang: 'es'
    });
    ```

3. Znovu přejděte na _http://localhost:3000_ . Na stránce byste měli vidět španělský text a když kliknete na **moderní čtečku**, zobrazí se i v moderní čtečce.

## <a name="specify-the-language-of-the-immersive-reader-interface"></a>Zadejte jazyk rozhraní moderního čtecího zařízení.

Standardně se jazyk moderního čtecího rozhraní shoduje s nastavením jazyka prohlížeče. Jazyk moderního čtecího rozhraní lze také určit pomocí následujícího kódu.

1. V _views\index.pug_ nahraďte volání `ImmersiveReader.launchAsync(token, subdomain, content)` kódem níže.

    ```javascript
    const options = {
        uiLang: 'fr',
    }
    ImmersiveReader.launchAsync(token, subdomain, content, options);
    ```

2. Přejděte na _http://localhost:3000_ . Při spuštění moderního čtecího zařízení se rozhraní zobrazí ve francouzštině.

## <a name="launch-the-immersive-reader-with-math-content"></a>Spuštění moderního čtečky s využitím matematického obsahu

Do moderního čtecího zařízení můžete zahrnout matematický obsah pomocí formátu [MathML](https://developer.mozilla.org/en-US/docs/Web/MathML).

1. Upravte _views\index.pug_ tak, aby zahrnoval následující kód nad volání `ImmersiveReader.launchAsync` :

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

2. Přejděte na _http://localhost:3000_ . Když spustíte moderní čtečku a posuňte se k dolnímu okraji, uvidíte matematický vzorec.

## <a name="next-steps"></a>Další kroky

* Prozkoumejte [sadu moderních čtenářů](https://github.com/microsoft/immersive-reader-sdk) a [referenční materiály k sadě pro moderní čtečku](../../reference.md)
* Zobrazit ukázky kódu na [GitHubu](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/advanced-csharp)
