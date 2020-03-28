---
title: 'Úvodní příručka: Vytvořte webovou aplikaci, která spustí immersive Reader s C #'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu vytvoříte webovou aplikaci od začátku a přidáte funkci rozhraní API pro immersive Reader.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: quickstart
ms.date: 01/14/2020
ms.author: metan
ms.openlocfilehash: 8dd8459922caa9f765d59bc28fbf050b86834b46
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76845240"
---
# <a name="quickstart-create-a-web-app-that-launches-the-immersive-reader-c"></a>Úvodní příručka: Vytvoření webové aplikace, která spustí immersive Reader (C#)

[Immersive Reader](https://www.onenote.com/learningtools) je inkluzivně navržený nástroj, který implementuje osvědčené techniky pro zlepšení porozumění čtení.

V tomto rychlém startu vytvoříte webovou aplikaci od začátku a integrujete immersive Reader pomocí sady Immersive Reader SDK. Úplný pracovní vzorek tohoto rychlého startu je k dispozici [zde](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/quickstart-csharp).

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="prerequisites"></a>Požadavky

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads)
* Prostředek immersive Reader nakonfigurovaný pro ověřování Azure Active Directory. Podle [těchto pokynů](./how-to-create-immersive-reader.md) se připravte. Při konfiguraci vlastností ukázkového projektu budete potřebovat některé hodnoty vytvořené zde. Uložte výstup relace do textového souboru pro budoucí použití.

## <a name="create-a-web-app-project"></a>Vytvoření projektu webové aplikace

Vytvořte nový projekt v sadě Visual Studio pomocí šablony ASP.NET základní webové aplikace s integrovaným modelovým kontrolorem a ASP.NET jádrem 2.1. Pojmenujte projekt "QuickstartSampleWebApp".

![Nový projekt](./media/quickstart-csharp/1-createproject.png)

![Konfigurace nového projektu](./media/quickstart-csharp/2-configureproject.png)

![Nová ASP.NET základní webová aplikace](./media/quickstart-csharp/3-createmvc.png)

## <a name="set-up-authentication"></a>Nastavení ověřování

### <a name="configure-authentication-values"></a>Konfigurace hodnot ověřování

Klikněte pravým tlačítkem myši na projekt v _Průzkumníku řešení_ a zvolte **Spravovat tajné klíče uživatelů**. Tím se otevře soubor s názvem _secrets.json_. Tento soubor není zaškrtnuto do správy zdrojového kódu. Více se dozvíte [zde](https://docs.microsoft.com/aspnet/core/security/app-secrets?view=aspnetcore-3.1&tabs=windows). Nahraďte obsah _souboru secrets.json_ následujícím a zadejte hodnoty uvedené při vytváření prostředku imersive reader.

```json
{
  "TenantId": "YOUR_TENANT_ID",
  "ClientId": "YOUR_CLIENT_ID",
  "ClientSecret": "YOUR_CLIENT_SECRET",
  "Subdomain": "YOUR_SUBDOMAIN"
}
```

### <a name="add-the-microsoftidentitymodelclientsactivedirectory-nuget-package"></a>Přidání balíčku Microsoft.IdentityModel.Clients.ActiveDirectory NuGet

Následující kód používá objekty z balíčku **Microsoft.IdentityModel.Clients.ActiveDirectory** NuGet, takže budete muset přidat odkaz na tento balíček v projektu.

Otevřete konzolu Správce balíčků NuGet z **tools -> NuGet Správce balíčků -> konzoli Správce balíčků** a spusťte následující příkaz:

```powershell
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 5.2.0
```

### <a name="update-the-controller-to-acquire-the-token"></a>Aktualizace řadiče pro získání tokenu 

Otevřete _controllers\HomeController.cs_a přidejte následující kód za _příkazy using_ v horní části souboru.

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Nyní nakonfigurujeme řadič tak, aby získával hodnoty Azure AD z _souboru secrets.json_. V horní části _HomeController_ třídy po ```public class HomeController : Controller {```přidejte následující kód.

```csharp
private readonly string TenantId;     // Azure subscription TenantId
private readonly string ClientId;     // Azure AD ApplicationId
private readonly string ClientSecret; // Azure AD Application Service Principal password
private readonly string Subdomain;    // Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')

public HomeController(Microsoft.Extensions.Configuration.IConfiguration configuration)
{
    TenantId = configuration["TenantId"];
    ClientId = configuration["ClientId"];
    ClientSecret = configuration["ClientSecret"];
    Subdomain = configuration["Subdomain"];

    if (string.IsNullOrWhiteSpace(TenantId))
    {
        throw new ArgumentNullException("TenantId is null! Did you add that info to secrets.json?");
    }

    if (string.IsNullOrWhiteSpace(ClientId))
    {
        throw new ArgumentNullException("ClientId is null! Did you add that info to secrets.json?");
    }

    if (string.IsNullOrWhiteSpace(ClientSecret))
    {
        throw new ArgumentNullException("ClientSecret is null! Did you add that info to secrets.json?");
    }

    if (string.IsNullOrWhiteSpace(Subdomain))
    {
        throw new ArgumentNullException("Subdomain is null! Did you add that info to secrets.json?");
    }
}

/// <summary>
/// Get an Azure AD authentication token
/// </summary>
private async Task<string> GetTokenAsync()
{
    string authority = $"https://login.windows.net/{TenantId}";
    const string resource = "https://cognitiveservices.azure.com/";

    AuthenticationContext authContext = new AuthenticationContext(authority);
    ClientCredential clientCredential = new ClientCredential(ClientId, ClientSecret);

    AuthenticationResult authResult = await authContext.AcquireTokenAsync(resource, clientCredential);

    return authResult.AccessToken;
}

[HttpGet]
public async Task<JsonResult> GetTokenAndSubdomain()
{
    try
    {
        string tokenResult = await GetTokenAsync();

        return new JsonResult(new { token = tokenResult, subdomain = Subdomain });
    }
    catch (Exception e)
    {
        string message = "Unable to acquire Azure AD token. Check the debugger for more information.";
        Debug.WriteLine(message, e);
        return new JsonResult(new { error = message });
    }
}
```

## <a name="add-sample-content"></a>Přidání ukázkového obsahu
Nejprve otevřete _zobrazení\Shared\Layout.cshtml_. Před řádek ```</head>```přidejte následující kód:

```html
@RenderSection("Styles", required: false)
```

Nyní přidáme ukázkový obsah do této webové aplikace. Otevřete _zobrazení\Home\Index.cshtml_ a nahraďte všechny automaticky generované kódy touto ukázkou:

```html
@{
    ViewData["Title"] = "Immersive Reader C# Quickstart";
}

@section Styles {
    <style type="text/css">
        .immersive-reader-button {
            background-color: white;
            margin-top: 5px;
            border: 1px solid black;
            float: right;
        }
    </style>
}

<div class="container">
    <button class="immersive-reader-button" data-button-style="iconAndText" data-locale="en"></button>

    <h1 id="ir-title">About Immersive Reader</h1>
    <div id="ir-content" lang="en-us">
        <p>
            Immersive Reader is a tool that implements proven techniques to improve reading comprehension for emerging readers, language learners, and people with learning differences.
            The Immersive Reader is designed to make reading more accessible for everyone. The Immersive Reader
            <ul>
                <li>
                    Shows content in a minimal reading view
                </li>
                <li>
                    Displays pictures of commonly used words
                </li>
                <li>
                    Highlights nouns, verbs, adjectives, and adverbs
                </li>
                <li>
                    Reads your content out loud to you
                </li>
                <li>
                    Translates your content into another language
                </li>
                <li>
                    Breaks down words into syllables
                </li>
            </ul>
        </p>
        <h3>
            The Immersive Reader is available in many languages.
        </h3>
        <p lang="es-es">
            El Lector inmersivo está disponible en varios idiomas.
        </p>
        <p lang="zh-cn">
            沉浸式阅读器支持许多语言
        </p>
        <p lang="de-de">
            Der plastische Reader ist in vielen Sprachen verfügbar.
        </p>
        <p lang="ar-eg" dir="rtl" style="text-align:right">
            يتوفر \"القارئ الشامل\" في العديد من اللغات.
        </p>
    </div>
</div>
```

Všimněte si, že celý text má **atribut lang,** který popisuje jazyky textu. Tento atribut pomáhá immersive Reader poskytnout příslušné funkce jazyka a gramatiky.

## <a name="add-javascript-to-handle-launching-the-immersive-reader"></a>Přidat JavaScript pro zpracování spuštění Immersive Reader

Knihovna Immersive Reader poskytuje funkce, jako je například spuštění immersive readeru a vykreslování tlačítek Immersive Reader. Více se dozvíte [zde](https://docs.microsoft.com/azure/cognitive-services/immersive-reader/reference).

V dolní části _položky Zobrazení\Home\Index.cshtml_přidejte následující kód:

```html
@section Scripts
{
    <script src="https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.1.0.0.js"></script>
    <script>
        function getTokenAndSubdomainAsync() {
            return new Promise(function (resolve, reject) {
                $.ajax({
                    url: "@Url.Action("GetTokenAndSubdomain", "Home")",
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
    
                    // Learn more about chunk usage and supported MIME types https://docs.microsoft.com/en-us/azure/cognitive-services/immersive-reader/reference#chunk
                    const data = {
                        title: $("#ir-title").text(),
                        chunks: [{
                            content: $("#ir-content").html(),
                            mimeType: "text/html"
                        }]
                    };
    
                    // Learn more about options https://docs.microsoft.com/en-us/azure/cognitive-services/immersive-reader/reference#options
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
    </script>
}
```

## <a name="build-and-run-the-app"></a>Sestavení a spuštění aplikace

Na řádku nabídek vyberte **Možnost Ladění > Spustit ladění**nebo spusťte aplikaci stisknutím **klávesy F5.**

V prohlížeči byste měli vidět:

![Ukázková aplikace](./media/quickstart-csharp/4-buildapp.png)

## <a name="launch-the-immersive-reader"></a>Spuštění immersive Reader

Když kliknete na tlačítko "Immersive Reader", uvidíte Immersive Reader zahájena s obsahem na stránce.

![Asistivní čtečka](./media/quickstart-csharp/5-viewimmersivereader.png)

## <a name="next-steps"></a>Další kroky

* Zobrazení [rychlého startu node.js](./quickstart-nodejs.md) zobrazíte další, co dalšího můžete dělat se sadou Immersive Reader SDK pomocí souboru Node.js
* Podívejte se na [kurz v Pythonu](./tutorial-python.md) a zjistěte, co dalšího můžete dělat s sadou Immersive Reader SDK pomocí Pythonu
* Podívejte se na [kurz pro iOS](./tutorial-ios-picture-immersive-reader.md) a zjistěte, co dalšího můžete dělat s sadou Immersive Reader SDK pomocí Swiftu
* Seznamte se s [sadou Immersive Reader SDK](https://github.com/microsoft/immersive-reader-sdk) a [referenční sadou Immersive Reader SDK](./reference.md)