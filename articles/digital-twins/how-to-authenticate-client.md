---
title: Ověření klientské aplikace
titleSuffix: Azure Digital Twins
description: Podívejte se, jak ověřit klientskou aplikaci proti službě Azure Digital revlákens.
author: baanders
ms.author: baanders
ms.date: 4/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: e52307c92d9371af6479f64841c6f269ed10e4b4
ms.sourcegitcommit: b56226271541e1393a4b85d23c07fd495a4f644d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85390818"
---
# <a name="authenticate-a-client-application-with-azure-digital-twins"></a>Ověřování klientské aplikace pomocí digitálních vláken Azure

Po [vytvoření instance digitálního vlákna Azure](how-to-set-up-instance.md)můžete vytvořit klientskou aplikaci, kterou použijete k interakci s instancí. Po nastavení klientského projektu Starter se v tomto článku dozvíte, jak správně ověřit tuto klientskou aplikaci s instancí digitálních vláken Azure.

To se provádí ve dvou krocích:
1. Vytvoření registrace aplikace
2. Zápis ověřovacího kódu v klientské aplikaci

[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

## <a name="create-an-app-registration"></a>Vytvoření registrace aplikace

K ověřování proti digitálním vazbám Azure z klientské aplikace je potřeba nastavit **registraci aplikace** v [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md).

V této registraci aplikace nakonfigurujete přístupová oprávnění k [rozhraním API pro digitální vlákna Azure](how-to-use-apis-sdks.md). Vaše klientská aplikace se ověřuje proti registraci aplikace a v důsledku toho jsou jim udělena nakonfigurovaná přístupová oprávnění k rozhraním API.

Pokud chcete vytvořit registraci aplikace, musíte zadat ID prostředku pro rozhraní API digitálních vláken Azure a základní oprávnění pro rozhraní API. V pracovním adresáři otevřete nový soubor a zadejte následující fragment kódu JSON pro konfiguraci těchto podrobností: 

```json
[{
    "resourceAppId": "0b07f429-9f4b-4714-9392-cc5e8e80c8b0",
    "resourceAccess": [
     {
       "id": "4589bd03-58cb-4e6c-b17f-b580e39652f8",
       "type": "Scope"
     }
    ]
}]
``` 

Uložte tento soubor jako *manifest.jsna*.

> [!NOTE] 
> K dispozici jsou některá místa, kde je možné použít "popisný" čitelný řetězec `https://digitaltwins.azure.net` pro místo identifikátoru GUID pro ID aplikace prostředků Azure Digital Replaced `0b07f429-9f4b-4714-9392-cc5e8e80c8b0` . Mnoho příkladů v celé této dokumentaci například používá ověřování pomocí knihovny MSAL a popisný řetězec lze použít pro tuto sadu. Během tohoto kroku vytváření registrace aplikace je ale ve formátu identifikátoru GUID IDENTIFIKÁTORu vyžadováno, jak je uvedeno výše. 

V okně Cloud Shell klikněte na ikonu nahrát/stáhnout soubory a vyberte Odeslat.

:::image type="content" source="media/how-to-authenticate-client/upload-extension.png" alt-text="Cloud Shell okno zobrazující výběr možnosti nahrání":::
Přejděte na *manifest.js* právě vytvořeného a stiskněte tlačítko "otevřít".

Potom spuštěním následujícího příkazu vytvořte registraci aplikace (podle potřeby nahraďte zástupné symboly):

```azurecli
az ad app create --display-name <name-for-your-app> --native-app --required-resource-accesses manifest.json --reply-url http://localhost
```

Výstup z tohoto příkazu vypadá nějak takto.

:::image type="content" source="media/how-to-authenticate-client/new-app-registration.png" alt-text="Nová registrace aplikace AAD":::

Po vytvoření registrace aplikace přejděte pomocí [tohoto odkazu](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) na stránku s přehledem registrace aplikace AAD v Azure Portal.

Z tohoto přehledu vyberte registraci aplikace, kterou jste právě vytvořili ze seznamu. Tím se otevřou podrobnosti na stránce, jako je tato:

:::image type="content" source="media/how-to-authenticate-client/get-authentication-ids.png" alt-text="Azure Portal: ID ověřování":::

Poznamenejte si *ID aplikace (klienta)* a *ID adresáře (tenanta)* **zobrazené na stránce** . Tyto hodnoty použijete později k ověření klientské aplikace s využitím rozhraní API pro digitální vlákna Azure.

> [!NOTE]
> V závislosti na vašem scénáři možná budete muset provést další změny v registraci aplikace. Tady jsou některé běžné požadavky, které možná budete muset splnit:
> * Aktivace přístupu veřejného klienta
> * Nastavit konkrétní adresy URL odpovědí pro přístup k webu a desktopu
> * Povolení pro toky implicitního ověřování OAuth2
> * Pokud je vaše předplatné Azure vytvořené pomocí účet Microsoft, jako je Live, Xbox nebo Hotmail, musíte nastavit *signInAudience* na registraci aplikace pro podporu osobních účtů.
> Nejjednodušší způsob, jak nastavit tato nastavení, je použít [Azure Portal](https://portal.azure.com/). Další informace o tomto procesu najdete v tématu [Registrace aplikace s platformou Microsoft Identity](https://docs.microsoft.com/graph/auth-register-app-v2).

## <a name="write-client-app-authentication-code-net-c-sdk"></a>Zápis ověřovacího kódu klientské aplikace: sada SDK .NET (C#)

Tato část popisuje kód, který bude nutné zahrnout do klientské aplikace, aby bylo možné dokončit proces ověřování pomocí sady .NET (C#) SDK.
Sada Azure Digital revlákens C# SDK je součástí sady Azure SDK for .NET. Najdete tady: [Klientská knihovna Azure IoT Digital nevlákenná pro .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core).

### <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte nastavený projekt úvodní klientské aplikace, vytvořte projekt Basic .NET, který se použije v tomto kurzu.

Aby bylo možné používat sadu .NET SDK, budete muset zahrnout do projektu následující balíčky:
* `Azure.DigitalTwins.Core`(verze `1.0.0-preview.2` )
* `Azure.Identity`

V závislosti na zvolených nástrojích můžete to udělat pomocí Správce balíčků sady Visual Studio nebo `dotnet` nástroje příkazového řádku. 

### <a name="authentication-and-client-creation-net"></a>Ověřování a vytváření klientů: .NET

K ověření pomocí sady .NET SDK použijte jednu z metod získání přihlašovacích údajů, které jsou definovány v knihovně [Azure. identity](https://docs.microsoft.com/dotnet/api/azure.identity?view=azure-dotnet) .

Tady jsou dvě běžně používané: 
* [InteractiveBrowserCredential](https://docs.microsoft.com/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet). Tato metoda je určena pro interaktivní aplikace a zobrazí webový prohlížeč pro ověřování.
* [ManagedIdentityCredential](https://docs.microsoft.com/dotnet/api/azure.identity.managedidentitycredential?view=azure-dotnet). Tato metoda funguje skvěle v případech, kdy potřebujete [spravované identity (MSI)](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)– například při práci s Azure Functions. 

Budete také potřebovat následující příkazy using:

```csharp
using Azure.Identity;
using Azure.DigitalTwins.Core;
```

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

Ve funkci Azure můžete použít přihlašovací údaje spravované identity takto:

```csharp
ManagedIdentityCredential cred = new ManagedIdentityCredential(adtAppId);
DigitalTwinsClientOptions opts = 
    new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, opts);
```

Projděte si téma [How to: Set the Azure Function for Data Processing](how-to-create-azure-function.md) pro ucelený příklad, který vysvětluje některé důležité možnosti konfigurace v kontextu funkcí.

Chcete-li použít ověřování ve funkci, nezapomeňte na:
* [Povolení spravované identity](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet)
* [Proměnné prostředí](https://docs.microsoft.com/sandbox/functions-recipes/environment-variables?tabs=csharp)
* Přiřaďte oprávnění k aplikaci Functions, která mu umožní přístup k rozhraním API digitálních vláken. Další informace najdete v tématu [Postupy: nastavení funkce Azure pro zpracování dat](how-to-create-azure-function.md) .

## <a name="authentication-in-an-autorest-generated-sdk"></a>Ověřování v sadě SDK generované AutoRest

Pokud nepoužíváte rozhraní .NET, můžete se rozhodnout vytvořit knihovnu SDK v jazyce podle vašeho výběru, jak je popsáno v tématu [Postupy: vytváření vlastních sad SDK pro digitální vlákna Azure pomocí automatického REST](how-to-create-custom-sdks.md).

V této části se dozvíte, jak ověřit v takovém případě.

### <a name="prerequisites"></a>Požadavky

V tomto příkladu se používá sada TypeScript SDK generovaná s AutoRest. V důsledku toho také vyžaduje:
* [msal-js](https://github.com/AzureAD/microsoft-authentication-library-for-js)
* [MS-REST-js](https://github.com/Azure/ms-rest-js)

### <a name="minimal-authentication-code-sample"></a>Ukázka minimálního ověřovacího kódu

K ověření aplikace .NET se službami Azure můžete v klientské aplikaci použít následující minimální kód.

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

MSAL má mnoho dalších možností, které můžete použít k implementaci takových akcí jako ukládání do mezipaměti a dalších toků ověřování. Další informace najdete v tématu [Přehled knihovny Microsoft Authentication Library (MSAL)](../active-directory/develop/msal-overview.md).

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o tom, jak funguje zabezpečení v Azure Digital autovlákna:
* [Koncepty: zabezpečení pro řešení digitálních vláken Azure](concepts-security.md)

Nebo teď, když je toto ověřování nastavené, přejděte k vytváření modelů ve vaší instanci:
* [Postupy: Správa vlastních modelů](how-to-manage-model.md)