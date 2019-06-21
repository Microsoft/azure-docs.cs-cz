---
title: 'Rychlý start: Vytvoření webové aplikace, které spouští atraktivní Reader (C#)'
titlesuffix: Azure Cognitive Services
description: V tomto rychlém startu vytvoření webové aplikace od začátku a přidejte funkce atraktivní čtečky.
services: cognitive-services
author: metanMSFT
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: quickstart
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: 7074511d16d157d67a67a2c40383c9909a4942bd
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2019
ms.locfileid: "67296771"
---
# <a name="quickstart-create-a-web-app-that-launches-the-immersive-reader-c"></a>Rychlý start: Vytvoření webové aplikace, které spouští atraktivní Reader (C#)

[Atraktivní čtečky](https://www.onenote.com/learningtools) je (včetně) navržené nástroj, který implementuje osvědčené techniky zlepšit míru porozumění čtení.

V tomto rychlém startu vytvoření webové aplikace od začátku a integrovat atraktivní čtečky pomocí sady SDK atraktivní čtecí zařízení. Plně funkční ukázka v tomto rychlém startu je k dispozici [tady](https://github.com/Microsoft/immersive-reader-sdk/samples/quickstart-csharp).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads)
* Klíč předplatného pro atraktivní čtecí zařízení. Získejte ji pomocí následujících [tyto pokyny](https://docs.microsoft.com/en-us/azure/cognitive-services/cognitive-services-apis-create-account).

## <a name="create-a-web-app-project"></a>Vytvoření projektu webové aplikace

Vytvoření nového projektu v sadě Visual Studio pomocí šablony webové aplikace ASP.NET Core s integrovanou Model-View-Controller.

![Nový projekt](./media/vswebapp.png)

![Nová webová aplikace ASP.NET Core](./media/vsmvc.png)

## <a name="acquire-an-access-token"></a>Získání přístupového tokenu

Budete potřebovat předplatné key a koncového bodu pro tento další krok. Můžete najít tyto informace v https://azure.microsoft.com/try/cognitive-services/my-apis/.

Klikněte pravým tlačítkem na projekt v _Průzkumníka řešení_ a zvolte **spravovat tajné klíče uživatelů**. Otevře se soubor s názvem _secrets.json_. Obsah tohoto souboru nahraďte následujícím kódem, včetně klíč předplatného a koncový bod, kde je to vhodné.

```json
{
  "SubscriptionKey": YOUR_SUBSCRIPTION_KEY,
  "Endpoint": YOUR_ENDPOINT
}
```

Otevřít _Controllers\HomeController.cs_a nahraďte `HomeController` třídy následujícím kódem.

```csharp
public class HomeController : Controller
{
    private readonly string SubscriptionKey;
    private readonly string Endpoint;

    public HomeController(Microsoft.Extensions.Configuration.IConfiguration configuration)
    {
        SubscriptionKey = configuration["SubscriptionKey"];
        Endpoint = configuration["Endpoint"];

        if (string.IsNullOrEmpty(Endpoint) || string.IsNullOrEmpty(SubscriptionKey))
        {
            throw new ArgumentNullException("Endpoint or subscriptionKey is null!");
        }
    }

    public IActionResult Index()
    {
        return View();
    }

    [Route("token")]
    public async Task<string> Token()
    {
        return await GetTokenAsync();
    }

    /// <summary>
    /// Exchange your Azure subscription key for an access token
    /// </summary>
    private async Task<string> GetTokenAsync()
    {
        using (var client = new System.Net.Http.HttpClient())
        {
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", SubscriptionKey);
            using (var response = await client.PostAsync($"{Endpoint}/issueToken", null))
            {
                return await response.Content.ReadAsStringAsync();
            }
        }
    }
}
```

## <a name="add-sample-content"></a>Přidejte ukázkový obsah

Teď přidáme několik ukázkový obsah do téhle webové aplikace. Otevřít _Views\Home\Index.cshtml_ a automaticky generovaného kódu nahraďte tuto ukázku:

```html
<h1 id='title'>Geography</h1>
<span id='content'>
    <p>The study of Earth's landforms is called physical geography. Landforms can be mountains and valleys. They can also be glaciers, lakes or rivers. Landforms are sometimes called physical features. It is important for students to know about the physical geography of Earth. The seasons, the atmosphere and all the natural processes of Earth affect where people are able to live. Geography is one of a combination of factors that people use to decide where they want to live.</p>
</span>

<div class='immersive-reader-button' data-button-style='iconAndText' onclick='launchImmersiveReader()'></div>

@section scripts {
<script type='text/javascript' src='https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.1.0.0.js'></script>
<script type='text/javascript' src='https://code.jquery.com/jquery-3.3.1.min.js'></script>
<script type='text/javascript'>
    function getImmersiveReaderTokenAsync() {
        return new Promise((resolve) => {
            $.ajax({
                url: '/token',
                type: 'GET',
                success: token => {
                    resolve(token);
                }
            });
        });
    }

    async function launchImmersiveReader() {
        const content = {
            title: document.getElementById('title').innerText,
            chunks: [ {
                content: document.getElementById('content').innerText,
                lang: 'en'
            } ]
        };

        const token = await getImmersiveReaderTokenAsync();
        ImmersiveReader.launchAsync(token, null, content, { uiZIndex: 1000000 });
    }
</script>
}
```

## <a name="build-and-run-the-app"></a>Sestavení a spuštění aplikace

V panelu nabídky vyberte **ladit > Spustit ladění**, nebo stiskněte klávesu **F5** ke spuštění aplikace.

V prohlížeči byste měli vidět:

![Ukázková aplikace](./media/quickstart-result.png)

Když kliknete na tlačítko "Atraktivní čtečky", zobrazí se vám atraktivní čtecí modul spuštěn s obsahem na stránce.

![Dokonalé čtečky](./media/quickstart-immersive-reader.png)

## <a name="next-steps"></a>Další postup

* Zobrazení [kurzu](./tutorial.md) chcete zobrazit, co dalšího vám pomůžou s moderním čtečky SDK
* Prozkoumejte [atraktivní čtečky SDK](https://github.com/Microsoft/immersive-reader-sdk) a [atraktivní čtečky odkaz na sadu SDK](./reference.md)