---
title: 'Rychlý Start: Vytvoření webové aplikace, která spustí moderní čtečku sC#'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu vytvoříte webovou aplikaci od začátku a přidáte funkce rozhraní API pro moderní čtečku.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: quickstart
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: dd3e1e6de886b24a2912fe6a12b47a852d8956d0
ms.sourcegitcommit: 49e14e0d19a18b75fd83de6c16ccee2594592355
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2020
ms.locfileid: "75945345"
---
# <a name="quickstart-create-a-web-app-that-launches-the-immersive-reader-c"></a>Rychlý Start: Vytvoření webové aplikace, která spustí moderní čtečku (C#)

[Moderní čtečka](https://www.onenote.com/learningtools) je celkově navržený nástroj, který implementuje osvědčené techniky pro zlepšení porozumění čtení.

V tomto rychlém startu vytvoříte webovou aplikaci od začátku a integrujete moderní čtečku pomocí sady moderních čtenářů. Kompletní pracovní vzorek tohoto rychlého startu je k dispozici [zde](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/quickstart-csharp).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads)
* Prostředek moderního čtecího zařízení nakonfigurovaný pro ověřování Azure Active Directory. Pomocí [těchto pokynů](./how-to-create-immersive-reader.md) si můžete nastavit. Při konfiguraci ukázkových vlastností projektu budete potřebovat některé z hodnot, které jsou zde vytvořeny. Uložte výstup vaší relace do textového souboru pro budoucí referenci.

## <a name="create-a-web-app-project"></a>Vytvoření projektu webové aplikace

Vytvořte nový projekt v aplikaci Visual Studio pomocí šablony ASP.NET Core webové aplikace s integrovaným modelem-View-Controller a ASP.NET Core 2,1. Pojmenujte projekt "QuickstartSampleWebApp".

![Nový projekt](./media/quickstart-csharp/1-createproject.png)

![Konfigurovat nový projekt](./media/quickstart-csharp/2-configureproject.png)

![Nová ASP.NET Core webové aplikace](./media/quickstart-csharp/3-createmvc.png)

## <a name="set-up-authentication"></a>Nastavení ověřování

### <a name="configure-authentication-values"></a>Konfigurovat hodnoty ověřování

V _Průzkumník řešení_ klikněte pravým tlačítkem myši na projekt a vyberte možnost **spravovat tajné klíče uživatele**. Tím se otevře soubor s názvem _tajných_kódů. JSON. Tento soubor není zkontrolován do správy zdrojového kódu. Další informace najdete [tady](https://docs.microsoft.com/aspnet/core/security/app-secrets?view=aspnetcore-3.1&tabs=windows). Obsah _tajných kódů. JSON_ nahraďte následujícím kódem a zadejte hodnoty, které jste zadali při vytváření prostředku pro moderní čtečku.

```json
{
  "TenantId": "YOUR_TENANT_ID",
  "ClientId": "YOUR_CLIENT_ID",
  "ClientSecret": "YOUR_CLIENT_SECRET",
  "Subdomain": "YOUR_SUBDOMAIN"
}
```

### <a name="add-the-microsoftidentitymodelclientsactivedirectory-nuget-package"></a>Přidejte balíček NuGet Microsoft. IdentityModel. clients. Active.

Následující kód používá objekty z balíčku NuGet **Microsoft. IdentityModel. clients. AD** , takže budete muset do svého projektu přidat odkaz na tento balíček.

Otevřete konzolu Správce balíčků NuGet z **nástrojů – > správce balíčků NuGet – > konzolu Správce balíčků** a spusťte následující příkaz:

```powershell
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 5.1.0
```

### <a name="update-the-controller-to-acquire-the-token"></a>Aktualizace kontroleru pro získání tokenu 

Otevřete _souboru controllers\homecontroller.cs_a za příkazy _using_ v horní části souboru přidejte následující kód.

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Teď nakonfigurujeme kontroler tak, aby získal hodnoty Azure AD z _tajných kódů. JSON_. V horní části třídy _HomeController_ přidejte po ```public class HomeController : Controller {```následující kód.

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

## <a name="add-sample-content"></a>Přidat ukázkový obsah
Nejdřív otevřete _Views\Shared\Layout.cshtml_. Před ```</head>```řádku přidejte následující kód:

```html
@RenderSection("Styles", required: false)
```

Nyní přidáme do této webové aplikace vzorový obsah. Otevřete _Views\Home\Index.cshtml_ a nahraďte veškerý automaticky generovaný kód touto ukázkou:

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

Všimněte si, že veškerý text má atribut **lang** , který popisuje jazyky textu. Tento atribut pomáhá modernímu čtečce poskytovat relevantní jazykové a gramatické funkce.

## <a name="add-javascript-to-handle-launching-the-immersive-reader"></a>Přidání JavaScriptu pro zpracování moderního čtecího zařízení

Knihovna moderního čtecího zařízení poskytuje funkce, jako je například spuštění moderního čtecího zařízení a vykreslování tlačítek pro moderní čtečku. Další informace najdete [tady](https://docs.microsoft.com/azure/cognitive-services/immersive-reader/reference).

V dolní části _Views\Home\Index.cshtml_přidejte následující kód:

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

V řádku nabídek vyberte možnost **ladění > spustit ladění**, nebo stiskněte klávesu **F5** a spusťte aplikaci.

V prohlížeči byste měli vidět:

![Ukázková aplikace](./media/quickstart-csharp/4-buildapp.png)

## <a name="launch-the-immersive-reader"></a>Spuštění moderního čtecího zařízení

Po kliknutí na tlačítko "moderní čtečka" se zobrazí moderní čtečka, která se spustí s obsahem na stránce.

![Asistivní čtečka](./media/quickstart-csharp/5-viewimmersivereader.png)

## <a name="next-steps"></a>Další kroky

* Podívejte se na úvodní seznam pro [Node. js](./quickstart-nodejs.md) , kde vidíte, co dalšího můžete dělat se sadou moderního čtecího zařízení pomocí Node. js.
* Podívejte se na [kurz Pythonu](./tutorial-python.md) , abyste viděli, co dalšího můžete dělat se sadou moderní čtečky pomocí Pythonu.
* Podívejte se na [kurz pro iOS](./tutorial-ios-picture-immersive-reader.md) , abyste viděli, co dalšího můžete dělat se sadou pro moderní čtečku pomocí SWIFT.
* Prozkoumejte [sadu moderních čtenářů](https://github.com/microsoft/immersive-reader-sdk) a [referenční materiály k sadě pro moderní čtečku](./reference.md)