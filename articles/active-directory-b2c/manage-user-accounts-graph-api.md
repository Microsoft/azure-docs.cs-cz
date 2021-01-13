---
title: Správa uživatelů pomocí rozhraní Microsoft Graph API
titleSuffix: Azure AD B2C
description: Postup správy uživatelů v klientovi Azure AD B2C voláním rozhraní API Microsoft Graph a pomocí identity aplikace k automatizaci procesu.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/13/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ff3cd858de86d21637f4a7a9ab9d9a83c7022f5a
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/13/2021
ms.locfileid: "98178870"
---
# <a name="manage-azure-ad-b2c-user-accounts-with-microsoft-graph"></a>Správa Azure AD B2C uživatelských účtů pomocí Microsoft Graph

Microsoft Graph umožňuje spravovat uživatelské účty v adresáři Azure AD B2C tím, že poskytuje metody vytváření, čtení, aktualizace a odstraňování v rozhraní API Microsoft Graph. Stávající úložiště uživatelů můžete migrovat do tenanta Azure AD B2C a provádět další operace správy uživatelských účtů voláním rozhraní API Microsoft Graph.

V následujících částech jsou uvedeny klíčové aspekty Azure AD B2C správy uživatelů pomocí Microsoft Graphho rozhraní API. Microsoft Graphch operací, typů a vlastností rozhraní API, které jsou zde uvedeny, jsou podmnožinou, která se zobrazí v referenční dokumentaci rozhraní API pro Microsoft Graph.

## <a name="register-a-management-application"></a>Registrace aplikace pro správu

Předtím, než aplikace pro správu uživatelů nebo skript napíše, může komunikovat s prostředky ve vašem tenantovi Azure AD B2C, budete potřebovat registraci aplikace, která jim uděluje oprávnění.

Pokud chcete vytvořit registraci aplikace, kterou může vaše aplikace pro správu používat, postupujte podle pokynů v tomto článku.

[Správa Azure AD B2C s využitím Microsoft Graph](microsoft-graph-get-started.md)

## <a name="user-management-microsoft-graph-operations"></a>Operace Microsoft Graph správy uživatelů

V [rozhraní Microsoft Graph API](/graph/api/resources/user)jsou k dispozici následující operace správy uživatelů:

- [Získat seznam uživatelů](/graph/api/user-list)
- [Vytvoření uživatele](/graph/api/user-post-users)
- [Získat uživatele](/graph/api/user-get)
- [Aktualizace uživatele](/graph/api/user-update)
- [Odstranění uživatele](/graph/api/user-delete)


## <a name="code-sample-how-to-programmatically-manage-user-accounts"></a>Ukázka kódu: jak programově spravovat uživatelské účty

Tato ukázka kódu je Konzolová aplikace .NET Core, která používá [sadu SDK Microsoft Graph](/graph/sdks/sdks-overview) k interakci s rozhraním API Microsoft Graph. Jeho kód ukazuje, jak volat rozhraní API pro programovou správu uživatelů v klientovi Azure AD B2C.
Můžete [si stáhnout ukázkový archiv](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management/archive/master.zip) (*. zip), [Procházet úložiště](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management) na GitHubu nebo klonovat úložiště:

```cmd
git clone https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management.git
```

Po získání ukázky kódu ji nakonfigurujte pro vaše prostředí a pak Sestavte projekt:

1. Otevřete projekt v [aplikaci Visual Studio](https://visualstudio.microsoft.com) nebo [Visual Studio Code](https://code.visualstudio.com).
1. Otevřete `src/appsettings.json`.
1. V `appSettings` oddílu nahraďte `your-b2c-tenant` názvem vašeho tenanta a `Application (client) ID` a `Client secret` hodnotami pro registraci aplikace pro správu (viz část [Registrace aplikace pro správu](#register-a-management-application) v tomto článku).
1. Otevřete okno konzoly v rámci svého místního klonu úložiště, přejděte do `src` adresáře a sestavte projekt:
    ```console
    cd src
    dotnet build
    ```
1. Spusťte aplikaci pomocí `dotnet` příkazu:

    ```console
    dotnet bin/Debug/netcoreapp3.1/b2c-ms-graph.dll
    ```

Aplikace zobrazí seznam příkazů, které lze spustit. Můžete například získat všechny uživatele, získat jednoho uživatele, odstranit uživatele, aktualizovat heslo uživatele a hromadný import.

### <a name="code-discussion"></a>Diskuze o kódu

Vzorový kód používá [sadu Microsoft Graph SDK](/graph/sdks/sdks-overview), která je navržená tak, aby zjednodušila vytváření vysoce kvalitních, efektivních a odolných aplikací, které přistupují k Microsoft Graph.

Jakýkoli požadavek na rozhraní Microsoft Graph API vyžaduje přístupový token pro ověřování. Řešení využívá balíček NuGet [Microsoft. Graph. auth](https://www.nuget.org/packages/Microsoft.Graph.Auth/) , který poskytuje obálku Microsoft Authentication Library (MSAL) založenou na scénáři ověřování pro použití s Microsoft Graph SDK.

`RunAsync`Metoda v souboru _program.cs_ :

1. Přečte nastavení aplikace z _appsettings.jsv_ souboru.
1. Inicializuje poskytovatele ověřování pomocí procesu [udělení přihlašovacích údajů klienta OAuth 2,0](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md) . Pomocí toku udělení přihlašovacích údajů klienta může aplikace získat přístupový token pro volání rozhraní Microsoft Graph API.
1. Nastaví klienta služby Microsoft Graph u poskytovatele ověřování:

    ```csharp
    // Read application settings from appsettings.json (tenant ID, app ID, client secret, etc.)
    AppSettings config = AppSettingsFile.ReadFromJsonFile();

    // Initialize the client credential auth provider
    IConfidentialClientApplication confidentialClientApplication = ConfidentialClientApplicationBuilder
        .Create(config.AppId)
        .WithTenantId(config.TenantId)
        .WithClientSecret(config.ClientSecret)
        .Build();
    ClientCredentialProvider authProvider = new ClientCredentialProvider(confidentialClientApplication);

    // Set up the Microsoft Graph service client with client credentials
    GraphServiceClient graphClient = new GraphServiceClient(authProvider);
    ```

Inicializovaná *GraphServiceClient* se pak použije v _UserService.cs_ k provádění operací správy uživatelů. Například získání seznamu uživatelských účtů v tenantovi:

```csharp
public static async Task ListUsers(GraphServiceClient graphClient)
{
    Console.WriteLine("Getting list of users...");

    // Get all users (one page)
    var result = await graphClient.Users
        .Request()
        .Select(e => new
        {
            e.DisplayName,
            e.Id,
            e.Identities
        })
        .GetAsync();

    foreach (var user in result.CurrentPage)
    {
        Console.WriteLine(JsonConvert.SerializeObject(user));
    }
}
```

[Volání rozhraní API pomocí Microsoft Graph sady SDK](/graph/sdks/create-requests) obsahují informace o tom, jak číst a zapisovat informace z Microsoft Graph, použít `$select` k řízení vrácených vlastností, zadání vlastních parametrů dotazu a použití `$filter` `$orderBy` parametrů dotazu a.

## <a name="next-steps"></a>Další kroky

Úplný index operací rozhraní Microsoft Graph API podporovaných pro Azure AD B2C prostředky najdete v tématu [Microsoft Graph operací, které jsou k dispozici pro Azure AD B2C](microsoft-graph-operations.md).

<!-- LINK -->

[graph-objectIdentity]: /graph/api/resources/objectidentity
[graph-user]: (https://docs.microsoft.com/graph/api/resources/user)
