---
title: Správa uživatelů pomocí rozhraní Microsoft Graph API
titleSuffix: Azure AD B2C
description: Jak spravovat uživatele v tenantovi Azure AD B2C voláním rozhraní Microsoft Graph API a pomocí identity aplikace k automatizaci procesu.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 42596ba5470c6062efba4fd1050c1c9745b76e80
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637331"
---
# <a name="manage-azure-ad-b2c-user-accounts-with-microsoft-graph"></a>Správa uživatelských účtů Azure AD B2C pomocí Microsoft Graphu

Microsoft Graph umožňuje spravovat uživatelské účty v adresáři Azure AD B2C tím, že poskytuje metody vytváření, čtení, aktualizace a odstraňování v rozhraní MICROSOFT Graph API. Existující úložiště uživatelů můžete migrovat do klienta Azure AD B2C a provádět další operace správy uživatelských účtů voláním rozhraní Microsoft Graph API.

V následujících částech jsou uvedeny klíčové aspekty správy uživatelů Azure AD B2C pomocí rozhraní Microsoft Graph API. Zde uvedené operace, typy a vlastnosti rozhraní MICROSOFT Graph API jsou podmnožinou těchto operací, typů a vlastností, které se zobrazí v referenční dokumentaci rozhraní MICROSOFT Graph API.

## <a name="register-a-management-application"></a>Registrace aplikace pro správu

Před jakoukoli aplikací pro správu uživatelů nebo skript, který píšete, můžete pracovat s prostředky v tenantovi Azure AD B2C, potřebujete registraci aplikace, která k tomu uděluje oprávnění.

Postupujte podle pokynů v tomto článku s postupy a vytvořte registraci aplikace, kterou může vaše aplikace pro správu použít:

[Správa Azure AD B2C pomocí Microsoft Graphu](microsoft-graph-get-started.md)

## <a name="user-management-microsoft-graph-operations"></a>Správa uživatelů microsoft graph u operací

V rozhraní [Microsoft Graph API](https://docs.microsoft.com/graph/api/resources/user)jsou k dispozici následující operace správy uživatelů :

- [Získání seznamu uživatelů](https://docs.microsoft.com/graph/api/user-list)
- [Vytvoření uživatele](https://docs.microsoft.com/graph/api/user-post-users)
- [Získání uživatele](https://docs.microsoft.com/graph/api/user-get)
- [Aktualizace uživatele](https://docs.microsoft.com/graph/api/user-update)
- [Odstranit uživatele](https://docs.microsoft.com/graph/api/user-delete)

## <a name="user-properties"></a>Uživatelské vlastnosti

### <a name="display-name-property"></a>Vlastnost zobrazované jméno

Je `displayName` název, který se má zobrazit ve správě uživatelů na portálu Azure portal a v přístupovém tokenu Azure AD B2C se vrátí do aplikace. Tato vlastnost je povinná.

### <a name="identities-property"></a>Vlastnost identity

K těmto typům identit může být přidružen účet zákazníka, který může být spotřebitelem, partnerem nebo občanem:

- **Místní** identita – uživatelské jméno a heslo jsou uloženy místně v adresáři Azure AD B2C. Často označujeme tyto identity jako "místní účty".
- **Federovaná** identita – identita uživatele, známá také jako *účty sociálních* sítí nebo *podniku,* je spravována poskytovatelem federované identity, jako je Facebook, Microsoft, ADFS nebo Salesforce.

Uživatel s účtem zákazníka se může přihlásit s více identitami. Například uživatelské jméno, e-mail, ID zaměstnance, ID státní správy a další. Jeden účet může mít více identit, místních i sociálních, se stejným heslem.

V rozhraní MICROSOFT Graph API jsou místní i federované `identities` identity uloženy v atributu user, který je typu [objectIdentity][graph-objectIdentity]. Kolekce `identities` představuje sadu identit používaných k přihlášení k uživatelskému účtu. Tato kolekce umožňuje uživateli přihlásit se k uživatelskému účtu s některou z jeho přidružené identity.

| Vlastnost   | Typ |Popis|
|:---------------|:--------|:----------|
|signInType|řetězec| Určuje typy přihlášení uživatele v adresáři. Pro místní `emailAddress`účet: `emailAddress2` `emailAddress3`, `userName` `emailAddress1`, , , , nebo jakýkoli jiný typ, který se vám líbí. Sociální účet musí `federated`být nastaven na .|
|issuer|řetězec|Určuje vystavittele identity. Pro místní účty (kde **signInType** není `federated`) je tato vlastnost výchozím názvem `contoso.onmicrosoft.com`domény místního klienta B2C, například . Pro sociální identitu (kde `federated` **signInType** je) je hodnota název vystavittele, například`facebook.com`|
|issuerAssignedId|řetězec|Určuje jedinečný identifikátor přiřazený uživateli vydavatelem. Kombinace **vystavita** a **issuerAssignedId** musí být jedinečný v rámci vašeho tenanta. Pro místní účet, pokud je **signInType** nastavena na `emailAddress` nebo `userName`, představuje přihlašovací jméno pro uživatele.<br>Když je **signInType** nastavena na: <ul><li>`emailAddress`(nebo začíná `emailAddress` `emailAddress1`jako ) **issuerAssignedId** musí být platnou e-mailovou adresou.</li><li>`userName`(nebo jakoukoli jinou hodnotu), **emitentAssignedId** musí být platnou [místní součástí e-mailové adresy](https://tools.ietf.org/html/rfc3696#section-3)</li><li>`federated`, **issuerAssignedId** představuje jedinečný identifikátor federovaného účtu</li></ul>|

Následující **identity** vlastnost s identitou místního účtu s přihlašovací m- name, e-mailovou adresu jako přihlášení a sociální identity. 

 ```JSON
 "identities": [
     {
       "signInType": "userName",
       "issuer": "contoso.onmicrosoft.com",
       "issuerAssignedId": "johnsmith"
     },
     {
       "signInType": "emailAddress",
       "issuer": "contoso.onmicrosoft.com",
       "issuerAssignedId": "jsmith@yahoo.com"
     },
     {
       "signInType": "federated",
       "issuer": "facebook.com",
       "issuerAssignedId": "5eecb0cd"
     }
   ]
 ```

Pro federované identity, v závislosti na zprostředkovateli identity **issuerAssignedId** je jedinečná hodnota pro daného uživatele na aplikaci nebo vývojový účet. Nakonfigurujte zásady Azure AD B2C se stejným ID aplikace, které bylo dříve přiřazeno poskytovatelem sociální sítě nebo jinou aplikací v rámci stejného vývojového účtu.

### <a name="password-profile-property"></a>Vlastnost profilu hesla

Pro místní identitu **passwordProfile** vlastnost je vyžadována a obsahuje heslo uživatele. Vlastnost `forceChangePasswordNextSignIn` musí být `false`nastavena na .

Pro federované (sociální) identity **passwordProfile** vlastnost není vyžadována.

```JSON
"passwordProfile" : {
    "password": "password-value",
    "forceChangePasswordNextSignIn": false
  }
```

### <a name="password-policy-property"></a>Vlastnost zásad hesla

Zásady hesel Azure AD B2C (pro místní účty) jsou založené na zásadách [silné síly hesla](../active-directory/authentication/concept-sspr-policy.md) služby Azure Active Directory. Zásady registrace nebo přihlášení a resetování hesla Azure AD B2C vyžadují tuto silnou sílu hesla a nevyprší platnost hesel.

Ve scénářích migrace uživatelů, pokud účty, které chcete migrovat mají slabší sílu hesla než [silné heslo síla](../active-directory/authentication/concept-sspr-policy.md) vynucená Azure AD B2C, můžete zakázat požadavek silné heslo. Chcete-li změnit výchozí zásady hesel, nastavte `passwordPolicies` vlastnost na . `DisableStrongPassword` Můžete například upravit požadavek vytvořit uživatele takto:

```JSON
"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"
```

### <a name="extension-properties"></a>Vlastnosti rozšíření

Každá aplikace orientovaná na zákazníka má jedinečné požadavky na informace, které mají být shromažďovány. Váš tenant Azure AD B2C je dodáván s integrovanou sadou informací uložených ve vlastnostech, jako je křestní jméno, příjmení, město a PSČ. S Azure AD B2C můžete rozšířit sadu vlastností uložených v každém účtu zákazníka. Další informace o definování vlastních atributů naleznete v [tématu vlastní atributy (toky uživatelů)](user-flow-custom-attributes.md) a [vlastní atributy (vlastní zásady).](custom-policy-custom-attributes.md)

Rozhraní Microsoft Graph API podporuje vytváření a aktualizaci uživatele s atributy rozšíření. Atributy rozšíření v rozhraní GRAPH API `extension_ApplicationObjectID_attributename`jsou pojmenovány pomocí konvence . Například:

```JSON
"extension_831374b3bd5041bfaa54263ec9e050fc_loyaltyNumber": "212342"
```

## <a name="code-sample"></a>Ukázka kódu

Tato ukázka kódu je konzolová aplikace .NET Core, která používá sadu [Microsoft Graph SDK](https://docs.microsoft.com/graph/sdks/sdks-overview) k interakci s rozhraním Microsoft Graph API. Jeho kód ukazuje, jak volat rozhraní API programově spravovat uživatele v tenantovi Azure AD B2C.
Ukázkový archiv (*.zip) si můžete [stáhnout,](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management/archive/master.zip) [procházet úložiště](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management) na GitHubu nebo naklonovat úložiště:

```cmd
git clone https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management.git
```

Po získání ukázky kódu ji nakonfigurujte pro vaše prostředí a pak vytvořte projekt:

1. Otevřete projekt v [sadě Visual Studio](https://visualstudio.microsoft.com) nebo Visual Studio [Code](https://code.visualstudio.com).
1. Otevřete `src/appsettings.json`.
1. V `appSettings` části nahraďte `your-b2c-tenant` názvem vašeho tenanta `Client secret` a `Application (client) ID` s hodnotami pro registraci aplikace pro správu (viz Registrace aplikace pro [správu](#register-a-management-application) části tohoto článku).
1. Otevřete okno konzoly v místním klonu úložiště, `src` přepněte do adresáře a vytvořte projekt:
    ```console
    cd src
    dotnet build
    ```
1. Spusťte aplikaci pomocí příkazu: `dotnet`

    ```console
    dotnet bin/Debug/netcoreapp3.0/b2c-ms-graph.dll
    ```

Aplikace zobrazí seznam příkazů, které můžete spustit. Můžete například získat všechny uživatele, získat jednoho uživatele, odstranit uživatele, aktualizovat heslo uživatele a hromadně importovat.

### <a name="code-discussion"></a>Diskuse o kódu

Ukázkový kód používá [sadu Microsoft Graph SDK](https://docs.microsoft.com/graph/sdks/sdks-overview), která je určena ke zjednodušení vytváření vysoce kvalitních, efektivních a odolných aplikací, které přistupují k aplikaci Microsoft Graph.

Jakýkoli požadavek na rozhraní Microsoft Graph API vyžaduje přístupový token pro ověřování. Řešení využívá balíček [Microsoft.Graph.Auth](https://www.nuget.org/packages/Microsoft.Graph.Auth/) NuGet, který poskytuje obálku knihovny Microsoft Authentication Library (MSAL) založenou na scénáři ověřování pro použití s sadou Microsoft Graph SDK.

Metoda `RunAsync` v _souboru Program.cs:_

1. Čte nastavení aplikace ze souboru _appsettings.json_
1. Inicializuje zprostředkovatele ověřování pomocí toku [udělení pověření klienta OAuth 2.0.](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md) S tokem udělení pověření klienta je aplikace schopna získat přístupový token pro volání rozhraní Microsoft Graph API.
1. Nastaví klienta služby Microsoft Graph u zprostředkovatele ověřování:

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

Inicializovaný *GraphServiceClient* se pak používá v _UserService.cs_ k provedení operací správy uživatelů. Například získání seznamu uživatelských účtů v tenantovi:

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

[Volání rozhraní API pomocí sad Microsoft Graph SDK obsahuje](https://docs.microsoft.com/graph/sdks/create-requests) informace o tom, jak číst a zapisovat informace z aplikace Microsoft Graph, slouží `$select` k řízení vrácených vlastností, poskytnutí vlastních parametrů dotazu a použití parametrů `$filter` a `$orderBy` dotazu.

## <a name="next-steps"></a>Další kroky

Úplný index operací rozhraní Microsoft Graph API podporovaných pro prostředky Azure AD B2C najdete v [tématu Operace Microsoft Graphu dostupné pro Azure AD B2C](microsoft-graph-operations.md).

<!-- LINK -->

[graph-objectIdentity]: https://docs.microsoft.com/graph/api/resources/objectidentity
[graph-user]: (https://docs.microsoft.com/graph/api/resources/user)
