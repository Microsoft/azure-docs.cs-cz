---
title: Zápis ověřovacího kódu aplikace
titleSuffix: Azure Digital Twins
description: Viz jak psát ověřovací kód v klientské aplikaci.
author: baanders
ms.author: baanders
ms.date: 4/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.custom: devx-track-js
ms.openlocfilehash: dd0d3e462f0b2d8b525e63d65d657a8f056d01a9
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91331858"
---
# <a name="write-client-app-authentication-code"></a>Zápis ověřovacího kódu klientské aplikace

Po [Nastavení instance a ověřování digitálních vláken Azure](how-to-set-up-instance-portal.md)můžete vytvořit klientskou aplikaci, kterou použijete k interakci s instancí. Po nastavení projektu počátečního klienta v tomto článku se dozvíte, **jak v této klientské aplikaci psát kód pro ověření v** instanci digitálních vláken Azure.

Existují dva přístupy k ukázkovému kódu v tomto článku. Můžete použít tu, která je pro vás nejvhodnější, v závislosti na zvoleném jazyce:
* První část ukázkového kódu používá sadu SDK Azure Digital .NET (C#). Sada SDK je součástí sady Azure SDK pro .NET a je umístěná v tomto umístění: [*Klientská knihovna Azure IoT s Nevlákenou pro .NET*](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core).
* Druhá část ukázkového kódu je určena pro uživatele, kteří nepoužívají sadu .NET SDK, a místo toho používají sady SDK generované pomocí automatického REST v jiných jazycích. Další informace o této strategii najdete v tématu [*Postupy: vytváření vlastních sad SDK pro digitální vlákna Azure pomocí automatického REST*](how-to-create-custom-sdks.md).

Další informace o rozhraních API a sadách SDK pro digitální vlákna Azure najdete v tématu [*Postupy: použití rozhraní API a sad SDK pro digitální vlákna Azure*](how-to-use-apis-sdks.md).

## <a name="prerequisites"></a>Požadavky

Nejdřív dokončete kroky nastavení v tématu [*Postupy: nastavení instance a ověřování*](how-to-set-up-instance-portal.md). Tím se zajistí, že máte instanci digitálního vlákna Azure, ke které má váš uživatel oprávnění pro přístup, a Vy jste nastavili oprávnění pro klientské aplikace. Po dokončení všech těchto nastavení jste připraveni k psaní kódu klientské aplikace.

Chcete-li pokračovat, budete potřebovat projekt klientské aplikace, ve kterém budete psát kód. Pokud ještě nemáte nastavený projekt klientské aplikace, vytvořte si základní projekt v jazyce zvoleném pro použití v tomto kurzu.

## <a name="authentication-and-client-creation-net-c-sdk"></a>Ověřování a vytváření klientů: sada SDK .NET (C#)

Nejprve zahrňte do projektu následující balíčky, aby bylo možné použít sadu .NET SDK a nástroje ověřování pro tento postup:
* `Azure.DigitalTwins.Core`
* `Azure.Identity`

V závislosti na zvolených nástrojích můžete balíčky zahrnout pomocí Správce balíčků sady Visual Studio nebo `dotnet` nástroje příkazového řádku. 

Budete také potřebovat následující příkazy using:

```csharp
using Azure.Identity;
using Azure.DigitalTwins.Core;
```
K ověření pomocí sady .NET SDK použijte jednu z metod získání přihlašovacích údajů, které jsou definovány v knihovně [Azure. identity](https://docs.microsoft.com/dotnet/api/azure.identity?view=azure-dotnet&preserve-view=true) . Tady jsou dvě běžně používané (dokonce společně ve stejné aplikaci):

* [InteractiveBrowserCredential](https://docs.microsoft.com/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet&preserve-view=true) je určený pro interaktivní aplikace a dá se použít k vytvoření ověřeného klienta SDK.
* [ManagedIdentityCredential](https://docs.microsoft.com/dotnet/api/azure.identity.managedidentitycredential?view=azure-dotnet&preserve-view=true) funguje skvěle v případech, kdy potřebujete spravované identity (MSI), a je vhodným kandidátem na práci s Azure Functions

### <a name="interactivebrowsercredential-method"></a>Metoda InteractiveBrowserCredential
Metoda [InteractiveBrowserCredential](https://docs.microsoft.com/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet&preserve-view=true) je určena pro interaktivní aplikace a otevře webový prohlížeč pro ověřování.

Chcete-li použít přihlašovací údaje interaktivního prohlížeče k vytvoření ověřeného klienta sady SDK, přidejte tento kód:

```csharp
// Your client / app registration ID
private static string clientId = "<your-client-ID>"; 
// Your tenant / directory ID
private static string tenantId = "<your-tenant-ID>";
// The URL of your instance, starting with the protocol (https://)
private static string adtInstanceUrl = "<your-Azure-Digital-Twins-instance-URL>";

//...

DigitalTwinsClient client;
try
{
    var credential = new InteractiveBrowserCredential(tenantId, clientId);
    client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credential);
} catch(Exception e)
{
    Console.WriteLine($"Authentication or client creation error: {e.Message}");
    Environment.Exit(0);
}
```

>[!NOTE]
> I když můžete umístit ID klienta, ID tenanta a adresu URL instance přímo do kódu, jak je znázorněno výše, je vhodné, aby kód získal tyto hodnoty z konfiguračního souboru nebo proměnné prostředí.

### <a name="managedidentitycredential-method"></a>Metoda ManagedIdentityCredential
 Metoda [ManagedIdentityCredential](https://docs.microsoft.com/dotnet/api/azure.identity.managedidentitycredential?view=azure-dotnet&preserve-view=true) funguje skvěle v případech, kdy potřebujete [spravované identity (MSI)](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)– například při práci s Azure Functions.
Ve funkci Azure můžete použít přihlašovací údaje spravované identity podobně jako toto:

```csharp
ManagedIdentityCredential cred = new ManagedIdentityCredential(adtAppId);
DigitalTwinsClientOptions opts = 
    new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, opts);
```

Projděte si téma [*How to: Set the Azure Function for Data Processing*](how-to-create-azure-function.md) pro ucelený příklad, který vysvětluje některé důležité možnosti konfigurace v kontextu funkcí.

Chcete-li použít ověřování ve funkci, nezapomeňte na:
* [Povolení spravované identity](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet)
* Použijte [proměnné prostředí](https://docs.microsoft.com/sandbox/functions-recipes/environment-variables?tabs=csharp) podle potřeby.
* Přiřaďte oprávnění k aplikaci Functions, která mu umožní přístup k rozhraním API digitálních vláken. Další informace o procesech Azure Functions najdete v tématu [*Postup: nastavení funkce Azure pro zpracování dat*](how-to-create-azure-function.md).

## <a name="authentication-with-an-autorest-generated-sdk"></a>Ověřování pomocí sady SDK generované AutoRest

Pokud nepoužíváte rozhraní .NET, můžete se rozhodnout vytvořit knihovnu SDK v jazyce podle vašeho výběru, jak je popsáno v tématu [*Postupy: vytváření vlastních sad SDK pro digitální vlákna Azure pomocí automatického REST*](how-to-create-custom-sdks.md).

V této části se dozvíte, jak ověřit v takovém případě.

### <a name="prerequisites"></a>Požadavky

Nejdřív byste měli provést kroky pro vytvoření vlastní sady SDK s AutoRest pomocí kroků v tématu [*Postupy: vytváření vlastních sad SDK pro digitální vlákna Azure*](how-to-create-custom-sdks.md)pomocí automatického REST.

V tomto příkladu se používá sada TypeScript SDK generovaná s AutoRest. V důsledku toho také vyžaduje:
* [msal-js](https://github.com/AzureAD/microsoft-authentication-library-for-js)
* [MS-REST-js](https://github.com/Azure/ms-rest-js)

### <a name="minimal-authentication-code-sample"></a>Ukázka minimálního ověřovacího kódu

K ověření aplikace se službami Azure můžete v klientské aplikaci použít následující minimální kód.

Budete potřebovat ID *aplikace (klienta)* a *ID adresáře (tenant)* z dřívější verze a také adresu URL instance digitálního vlákna Azure.

> [!TIP]
> Adresa URL instance digitálních vláken Azure se provede přidáním *https://* na začátek *názvu hostitele*instance digitálního vlákna Azure. Chcete-li zobrazit *název hostitele*spolu se všemi vlastnostmi vaší instance, můžete spustit `az dt show --dt-name <your-Azure-Digital-Twins-instance>` . Pomocí `az account show --query tenantId` příkazu můžete zobrazit své *ID adresáře (tenant)*. 

```javascript
import * as Msal from "msal";
import { TokenCredentials } from "@azure/ms-rest-js";
// Autorest-generated SDK
import { AzureDigitalTwinsAPI } from './azureDigitalTwinsAPI';

// Client / app registration ID
var ClientId = "<your-client-ID>";
// Azure tenant / directory ID
var TenantId = "<your-tenant-ID>";
// URL of the Azure Digital Twins instance
var AdtInstanceUrl = "<your-instance-URL>"; 

var AdtAppId = "https://digitaltwins.azure.net";

let client = null;

export async function login() {

    const msalConfig = {
        auth: {
            clientId: ClientId,
            redirectUri: "http://localhost:3000",
            authority: "https://login.microsoftonline.com/"+TenantId
        }
    };

    const msalInstance = new Msal.UserAgentApplication(msalConfig);

    msalInstance.handleRedirectCallback((error, response) => {
        // handle redirect response or error
    });

    var loginRequest = {
        scopes: [AdtAppId + "/.default"] 
    };

    try {
        await msalInstance.loginPopup(loginRequest)
        var accessToken;
        // if the user is already logged in you can acquire a token
        if (msalInstance.getAccount()) {
            var tokenRequest = {
                scopes: [AdtAppId + "/.default"]
            };
            try {
                const response = await msalInstance.acquireTokenSilent(tokenRequest);
                accessToken = response.accessToken;
            } catch (err) {
                if (err.name === "InteractionRequiredAuthError") {
                    const response = await msalInstance.acquireTokenPopup(tokenRequest)
                    accessToken = response.accessToken;
                }
            }
        }
        if (accessToken!=null)
        {
            var tokenCredentials = new TokenCredentials(accessToken);
                
            // Add token and server URL to service instance
            const clientConfig = {
                baseUri: AdtInstanceUrl
            };
            client = new AzureDigitalTwinsAPI(tokenCredentials, clientConfig);
            appDataStore.client = client;
        }
    } catch (err) {
        ...
    }
}
```

Všimněte si, že pokud kód výše umístí ID klienta, ID tenanta a adresu URL instance přímo do kódu pro jednoduchost, je vhodné, aby kód získal tyto hodnoty z konfiguračního souboru nebo proměnné prostředí.

MSAL má mnoho dalších možností, které můžete použít k implementaci takových akcí jako ukládání do mezipaměti a dalších toků ověřování. Další informace najdete v tématu [*Přehled knihovny Microsoft Authentication Library (MSAL)*](../active-directory/develop/msal-overview.md).

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o tom, jak funguje zabezpečení v Azure Digital autovlákna:
* [*Koncepty: zabezpečení pro řešení digitálních vláken Azure*](concepts-security.md)

Nebo teď, když je toto ověřování nastavené, přejděte k vytváření modelů ve vaší instanci:
* [*Postupy: Správa vlastních modelů*](how-to-manage-model.md)