---
title: 'Rychlý start: Vytvořte webovou aplikaci, která spustí moderní čtečku sC#'
titlesuffix: Azure Cognitive Services
description: V tomto rychlém startu vytvoříte webovou aplikaci od začátku a přidáte funkce rozhraní API pro moderní čtečku.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: quickstart
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: 0d67385975db13539c28745eede4fb288a0daedd
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2019
ms.locfileid: "68989144"
---
# <a name="quickstart-create-a-web-app-that-launches-the-immersive-reader-c"></a>Rychlý start: Vytvoření webové aplikace, která spustí moderní čtečku (C#)

[Moderní čtečka](https://www.onenote.com/learningtools) je celkově navržený nástroj, který implementuje osvědčené techniky pro zlepšení porozumění čtení.

V tomto rychlém startu vytvoříte webovou aplikaci od začátku a integrujete moderní čtečku pomocí sady moderních čtenářů. Kompletní pracovní vzorek tohoto rychlého startu je k dispozici [zde](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/quickstart-csharp).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads)
* Prostředek moderního čtecího zařízení nakonfigurovaný pro ověřování Azure Active Directory (Azure AD). Pomocí [těchto pokynů](./azure-active-directory-authentication.md) si můžete nastavit. Při konfiguraci ukázkových vlastností projektu budete potřebovat některé z hodnot, které jsou zde vytvořeny. Uložte výstup vaší relace do textového souboru pro budoucí referenci.

## <a name="create-a-web-app-project"></a>Vytvoření projektu webové aplikace

Vytvořte nový projekt v aplikaci Visual Studio pomocí šablony ASP.NET Core webové aplikace s integrovaným modelem zobrazení modelu.

![Nový projekt](./media/vswebapp.png)

![Nová ASP.NET Core webové aplikace](./media/vsmvc.png)

## <a name="acquire-an-azure-ad-authentication-token"></a>Získání ověřovacího tokenu Azure AD

Pro tuto část budete potřebovat některé hodnoty z kroku podmínka konfigurace ověřování Azure AD výše. Vraťte se zpět k textovému souboru, který jste si uložili do této relace.

````text
TenantId     => Azure subscription TenantId
ClientId     => Azure AD ApplicationId
ClientSecret => Azure AD Application Service Principal password
Subdomain    => Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')
````

V _Průzkumník řešení_ klikněte pravým tlačítkem myši na projekt a vyberte možnost **spravovat tajné klíče uživatele**. Tím se otevře soubor s názvem tajných kódů. _JSON_. Obsah tohoto souboru nahraďte následujícím souborem a zadejte hodnoty vašich vlastních vlastností.

```json
{
  "TenantId": YOUR_TENANT_ID,
  "ClientId": YOUR_CLIENT_ID,
  "ClientSecret": YOUR_CLIENT_SECRET,
  "Subdomain": YOUR_SUBDOMAIN
}
```

Otevřete _souboru controllers\homecontroller.cs_a nahraďte soubor následujícím kódem.

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.IdentityModel.Clients.ActiveDirectory;

namespace QuickstartSampleWebApp.Controllers
{
    public class HomeController : Controller
    {
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

        public IActionResult Index()
        {
            return View();
        }

        [Route("subdomain")]
        public string GetSubdomain()
        {
            return Subdomain;
        }

        [Route("token")]
        public async Task<string> GetToken()
        {
            return await GetTokenAsync();
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
    }
}
```

## <a name="add-the-microsoftidentitymodelclientsactivedirectory-nuget-package"></a>Přidejte balíček NuGet Microsoft. IdentityModel. clients. Active.

Výše uvedený kód používá objekty z balíčku NuGet **Microsoft. IdentityModel. clients. Active Directory** , takže budete muset do svého projektu přidat odkaz na tento balíček.

Otevřete konzolu Správce balíčků NuGet z **nástrojů – > správce balíčků NuGet – > konzolu Správce balíčků** a zadejte následující:

```powershell
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 5.1.0
```

## <a name="add-sample-content"></a>Přidat ukázkový obsah

Teď přidáme do této webové aplikace nějaký ukázkový obsah. Otevřete _Views\Home\Index.cshtml_ a nahraďte automaticky generovaný kód touto ukázkou:

```html
<h1 id='title'>Geography</h1>
<span id='content'>
    <p>The study of Earth's landforms is called physical geography. Landforms can be mountains and valleys. They can also be glaciers, lakes or rivers. Landforms are sometimes called physical features. It is important for students to know about the physical geography of Earth. The seasons, the atmosphere and all the natural processes of Earth affect where people are able to live. Geography is one of a combination of factors that people use to decide where they want to live.</p>
</span>

<div class='immersive-reader-button' data-button-style='iconAndText' onclick='launchImmersiveReader()'></div>

@section scripts {
    <script type='text/javascript' src='https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.0.0.2.js'></script>
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

    function getSubdomainAsync() {
        return new Promise((resolve) => {
            $.ajax({
                url: '/subdomain',
                type: 'GET',
                success: subdomain => {
                    resolve(subdomain);
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
        var subdomain = await getSubdomainAsync();

        ImmersiveReader.launchAsync(token, subdomain, content, { uiZIndex: 1000000 });
    }
    </script>
}
```

## <a name="build-and-run-the-app"></a>Sestavení a spuštění aplikace

V řádku nabídek vyberte možnost **ladění > spustit ladění**, nebo stiskněte klávesu **F5** a spusťte aplikaci.

V prohlížeči byste měli vidět:

![Ukázková aplikace](./media/quickstart-result.png)

Po kliknutí na tlačítko "moderní čtečka" se zobrazí moderní čtečka, která se spustí s obsahem na stránce.

![Asistivní čtečka](./media/quickstart-immersive-reader.png)

## <a name="next-steps"></a>Další postup

* Podívejte se na [kurz Node. js](./tutorial-nodejs.md) , kde se můžete podívat, co se dá dělat se sadou moderní čtečky pomocí Node. js.
* Podívejte se na [kurz Pythonu](./tutorial-python.md) , abyste viděli, co dalšího můžete dělat se sadou moderní čtečky pomocí Pythonu.
* Podívejte se na [kurz pro iOS](./tutorial-ios-picture-immersive-reader.md) , abyste viděli, co dalšího můžete dělat se sadou pro moderní čtečku pomocí SWIFT.
* Prozkoumejte [sadu moderních čtenářů](https://github.com/microsoft/immersive-reader-sdk) a [referenční materiály k sadě pro moderní čtečku](./reference.md)
