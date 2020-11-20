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
ms.date: 08/03/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a5fcbf80850fd9de77e6f9a431afea6d48cb14d1
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94949407"
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

## <a name="user-properties"></a>Uživatelské vlastnosti

### <a name="display-name-property"></a>Vlastnost zobrazovaného názvu

`displayName`Je název, který se má zobrazit v Azure Portal Správa uživatelů pro uživatele, a v přístupovém tokenu Azure AD B2C se vrátí do aplikace. Tato vlastnost je povinná.

### <a name="identities-property"></a>Vlastnost identity

K těmto typům identit se dá přidružit účet zákazníka, který může být příjemcem, partnerem nebo občanem:

- **Místní** identita – uživatelské jméno a heslo jsou uložené lokálně v adresáři Azure AD B2C. Na tyto identity často odkazujeme jako na místní účty.
- **Federované** identity – označované taky jako *sociální* nebo *podnikové* účty, je identita uživatele spravovaná pomocí federovaného poskytovatele identity, jako je Facebook, Microsoft, ADFS nebo Salesforce.

Uživatel s účtem zákazníka se může přihlásit s více identitami. Například uživatelské jméno, e-mail, ID zaměstnance, ID státní správy a další. Jeden účet může mít více identit, místní i sociální, se stejným heslem.

V rozhraní Microsoft Graph API jsou místní i federované identity uloženy v `identities` atributu uživatele, který je typu [objectIdentity][graph-objectIdentity]. `identities`Kolekce představuje sadu identit, které se používají pro přihlášení k uživatelskému účtu. Tato kolekce umožňuje uživateli přihlásit se k uživatelskému účtu pomocí kterékoli z jeho přidružených identit.

| Vlastnost   | Typ |Popis|
|:---------------|:--------|:----------|
|signInType|řetězec| Určuje typy přihlašování uživatelů v adresáři. Pro místní účet:  `emailAddress` , `emailAddress1` , `emailAddress2` , `emailAddress3` ,  `userName` nebo jakýkoli jiný typ, který chcete. Účet sociálních sítí musí být nastavený na  `federated` .|
|issuer|řetězec|Určuje vystavitele identity. U místních účtů (kde **signInType** není `federated` ) Tato vlastnost je výchozí název domény místního B2C tenanta, například `contoso.onmicrosoft.com` . Pro sociální identity (kde **signInType** je  `federated` ) hodnota je název vystavitele, například `facebook.com`|
|issuerAssignedId|řetězec|Určuje jedinečný identifikátor přiřazený uživateli vystavitelem. Kombinace **vystavitele** a **issuerAssignedId** musí být ve vašem tenantovi jedinečná. Pro místní účet, pokud je **signInType** nastaveno na `emailAddress` nebo `userName` , představuje přihlašovací jméno uživatele.<br>Když je **signInType** nastaveno na: <ul><li>`emailAddress` (nebo začíná `emailAddress` jako `emailAddress1` ) **issuerAssignedId** musí být platná e-mailová adresa.</li><li>`userName`(nebo jakákoli jiná hodnota) musí být **issuerAssignedId** platná [místní část e-mailové adresy](https://tools.ietf.org/html/rfc3696#section-3) .</li><li>`federated`, **issuerAssignedId** představuje jedinečný identifikátor federovaného účtu.</li></ul>|

Následující vlastnost **identity** s identitou místního účtu s přihlašovacím jménem, e-mailovou adresou jako přihlašování a sociální identitou. 

 ```json
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

Pro federované identity v závislosti na zprostředkovateli identity je **issuerAssignedId** jedinečná hodnota pro daného uživatele podle aplikace nebo vývojového účtu. Nakonfigurujte zásady Azure AD B2C se stejným ID aplikace, které dříve přiřadil poskytovatel sociálních sítí nebo jiná aplikace v rámci stejného vývojového účtu.

### <a name="password-profile-property"></a>Vlastnost profilu hesla

Pro místní identitu je vyžadována vlastnost **passwordProfile** a obsahuje heslo uživatele. `forceChangePasswordNextSignIn`Vlastnost musí být nastavena na hodnotu `false` .

Pro federované (sociální) identitu se nepožaduje vlastnost **passwordProfile** .

```json
"passwordProfile" : {
    "password": "password-value",
    "forceChangePasswordNextSignIn": false
  }
```

### <a name="password-policy-property"></a>Vlastnost zásad hesel

Zásady hesla Azure AD B2C (pro místní účty) jsou založené na zásadách Azure Active Directory [silného hesla](../active-directory/authentication/concept-sspr-policy.md) . Zásady Azure AD B2C pro registraci nebo přihlášení a resetování hesla vyžadují tuto silnou sílu hesla a nevypršení platnosti hesla.

Pokud účty, které chcete migrovat, mají slabší sílu hesla, než je [silná síla hesla](../active-directory/authentication/concept-sspr-policy.md) vynutila Azure AD B2C, můžete v scénářích migrace uživatelů zakázat požadavek na silný heslo. Chcete-li změnit výchozí zásady hesla, nastavte `passwordPolicies` vlastnost na hodnotu `DisableStrongPassword` . Požadavek na vytvoření uživatele můžete například upravit následujícím způsobem:

```json
"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"
```

### <a name="extension-properties"></a>Vlastnosti rozšíření

Každá aplikace pro zákazníky má jedinečné požadavky na shromažďování informací. Váš tenant Azure AD B2C obsahuje integrovanou sadu informací uložených ve vlastnostech, jako je křestní jméno, příjmení, město a poštovní směrovací číslo. Pomocí Azure AD B2C můžete roztáhnout sadu vlastností uložených v každém účtu zákazníka. Další informace o definování vlastních atributů najdete v tématech [vlastní atributy (toky uživatelů)](user-flow-custom-attributes.md) a [vlastní atributy (vlastní zásady)](custom-policy-custom-attributes.md).

Rozhraní Microsoft Graph API podporuje vytváření a aktualizaci uživatele s atributy rozšíření. Atributy rozšíření v Graph API jsou pojmenovány pomocí konvence `extension_ApplicationClientID_attributename` , kde `ApplicationClientID` je **ID aplikace (klienta)** `b2c-extensions-app` aplikace (nalezené v **Registrace aplikací**  >  **všech aplikací** v Azure Portal). Všimněte si, že **ID aplikace (klienta)** , jak je znázorněno v názvu atributu rozšíření, neobsahuje spojovníky. Například:

```json
"extension_831374b3bd5041bfaa54263ec9e050fc_loyaltyNumber": "212342"
```

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