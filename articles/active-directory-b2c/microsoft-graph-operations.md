---
title: Správa prostředků pomocí Microsoft Graph
titleSuffix: Azure AD B2C
description: Postup správy prostředků v klientovi Azure AD B2C voláním rozhraní API Microsoft Graph a pomocí identity aplikace k automatizaci procesu.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/28/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 41336d59d51685d5daf78a1809ce6c0df2cd6124
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/22/2021
ms.locfileid: "104781309"
---
# <a name="manage-azure-ad-b2c-with-microsoft-graph"></a>Správa Azure AD B2C s využitím Microsoft Graph

Microsoft Graph umožňuje správu prostředků v adresáři Azure AD B2C. Následující operace Microsoft Graph API se podporují pro správu prostředků Azure AD B2C, včetně uživatelů, poskytovatelů identity, toků uživatelů, uživatelských zásad a klíčů zásad. Každé propojení v následujících částech cílí na odpovídající stránku v rámci referenčního Microsoft Graph rozhraní API pro tuto operaci. 

## <a name="prerequisites"></a>Předpoklady

Pokud chcete používat MS Graph API a pracovat s prostředky ve vašem tenantovi Azure AD B2C, budete potřebovat registraci aplikace, která jim uděluje oprávnění. Pokud chcete vytvořit registraci aplikace, kterou může vaše aplikace pro správu používat, postupujte podle pokynů v článku [správa Azure AD B2C s Microsoft Graph](microsoft-graph-get-started.md) . 

## <a name="user-management"></a>Správa uživatelů

- [Vypsání uživatelů](/graph/api/user-list)
- [Vytvoření uživatele spotřebitele](/graph/api/user-post-users)
- [Získat uživatele](/graph/api/user-get)
- [Aktualizace uživatele](/graph/api/user-update)
- [Odstranění uživatele](/graph/api/user-delete)

## <a name="user-phone-number-management-beta"></a>Správa telefonního čísla uživatele (beta verze)

Telefonní číslo, které může uživatel použít k přihlášení pomocí [SMS nebo hlasových hovorů](identity-provider-local.md#phone-sign-in-preview)nebo [vícefaktorového ověřování](multi-factor-authentication.md). Další informace najdete v tématu [rozhraní API metody ověřování Azure AD](/graph/api/resources/phoneauthenticationmethod).

- [Přidat](/graph/api/authentication-post-phonemethods)
- [Seznam](/graph/api/authentication-list-phonemethods)
- [Získat](/graph/api/phoneauthenticationmethod-get)
- [Aktualizace](/graph/api/phoneauthenticationmethod-update)
- [Odstranit](/graph/api/phoneauthenticationmethod-delete)

Všimněte si, že operace [list](/graph/api/authentication-list-phonemethods) vrátí jenom telefonní čísla, která jsou povolená. Následující telefonní číslo by mělo být povoleno pro použití se seznamem operací. 

![Povolit přihlašování telefonem](./media/microsoft-graph-operations/enable-phone-sign-in.png)

## <a name="self-service-password-reset-email-address-beta"></a>E-mailová adresa pro Samoobslužné resetování hesla (beta verze)

E-mailová adresa, kterou může [účet pro přihlášení k uživatelskému jménu](identity-provider-local.md#username-sign-in) použít k resetování hesla. Další informace najdete v tématu [rozhraní API metody ověřování Azure AD](/graph/api/resources/emailauthenticationmethod).

- [Přidat](/graph/api/emailauthenticationmethod-post)
- [Seznam](/graph/api/emailauthenticationmethod-list)
- [Získat](/graph/api/emailauthenticationmethod-get)
- [Aktualizace](/graph/api/emailauthenticationmethod-update)
- [Odstranit](/graph/api/emailauthenticationmethod-delete)

## <a name="identity-providers"></a>Zprostředkovatelé identit

Spravujte [zprostředkovatele identity, kteří](add-identity-provider.md) jsou k dispozici vašim uživatelským tokům v Azure AD B2Cm tenantovi.

- [Seznam zprostředkovatelů identity registrovaných v tenantovi Azure AD B2C](/graph/api/identityprovider-list)
- [Vytvoření zprostředkovatele identity](/graph/api/identityprovider-post-identityproviders)
- [Získat zprostředkovatele identity](/graph/api/identityprovider-get)
- [Aktualizovat zprostředkovatele identity](/graph/api/identityprovider-update)
- [Odstranění zprostředkovatele identity](/graph/api/identityprovider-delete)

## <a name="user-flow"></a>Tok uživatele

Nakonfigurujte předem připravené zásady pro registraci, přihlašování, kombinované registrace a přihlášení, resetování hesla a aktualizaci profilu.

- [Výpis toků uživatelů](/graph/api/identitycontainer-list-b2cuserflows)
- [Vytvoření toku uživatele](/graph/api/identitycontainer-post-b2cuserflows)
- [Získání toku uživatele](/graph/api/b2cidentityuserflow-get)
- [Odstranění toku uživatele](/graph/api/b2cidentityuserflow-delete)

## <a name="user-flow-authentication-methods-beta"></a>Metody ověřování toku uživatele (beta verze)

Vyberte mechanismus, který umožní uživatelům registraci prostřednictvím místních účtů. Místní účty jsou účty, kde Azure AD řídí kontrolní výraz identity. Další informace najdete v tématu [typ prostředku b2cAuthenticationMethodsPolicy](/graph/api/resources/b2cauthenticationmethodspolicy).

- [Získat](/graph/api/b2cauthenticationmethodspolicy-get)
- [Aktualizace](/graph/api/b2cauthenticationmethodspolicy-update)

## <a name="custom-policies"></a>Vlastní zásady

Následující operace umožňují spravovat zásady Azure AD B2C vztahů důvěryhodnosti, označované jako [vlastní zásady](custom-policy-overview.md).

- [Vypíše všechny zásady architektury trustu nakonfigurované v tenantovi.](/graph/api/trustframework-list-trustframeworkpolicies)
- [Vytvořit zásadu architektury vztahu důvěryhodnosti](/graph/api/trustframework-post-trustframeworkpolicy)
- [Načte vlastnosti existující zásady pro pravidlo důvěryhodnosti.](/graph/api/trustframeworkpolicy-get)
- [Aktualizovat nebo vytvořit zásadu architektury trustu](/graph/api/trustframework-put-trustframeworkpolicy)
- [Odstraní existující zásadu pro pravidlo důvěryhodnosti.](/graph/api/trustframeworkpolicy-delete)

## <a name="policy-keys"></a>Klíče zásad

Rozhraní identity Experience Framework ukládá tajné kódy, na které se odkazuje ve vlastních zásadách, aby bylo možné navázat vztah důvěryhodnosti mezi komponentami. Tyto tajné klíče můžou být symetrické nebo asymetrické klíče a hodnoty. V Azure Portal se tyto entity zobrazují jako **klíče zásad**.

Prostředek nejvyšší úrovně pro klíče zásad v rozhraní Microsoft Graph API je [sada klíčů pro důvěryhodné rozhraní](/graph/api/resources/trustframeworkkeyset). Každá **sada klíčů** obsahuje alespoň jeden **klíč**. Pokud chcete vytvořit klíč, nejdřív vytvořte prázdnou sadu klíčů a pak vygenerujte klíč v této sadu klíčů. Můžete vytvořit ruční tajný klíč, nahrát certifikát nebo PKCS12 klíč. Klíčem může být vygenerovaný tajný klíč, řetězec (například tajný klíč aplikace Facebook) nebo certifikát, který nahrajete. Pokud má sada klíčů více klíčů, je aktivní pouze jeden z klíčů.

### <a name="trust-framework-policy-keyset"></a>Sada klíčů zásad pro Trust Framework

- [Seznam sad pro nastavení sady Trust Framework](/graph/api/trustframework-list-keysets)
- [Vytvoření sad pro přístup k architektuře](/graph/api/trustframework-post-keysets)
- [Získání sady klíčů](/graph/api/trustframeworkkeyset-get)
- [Aktualizace sad s architekturou pro vztah důvěryhodnosti](/graph/api/trustframeworkkeyset-update)
- [Odstranění sad s architekturou pro vztah důvěryhodnosti](/graph/api/trustframeworkkeyset-delete)

### <a name="trust-framework-policy-key"></a>Klíč zásad pro Trust Framework

- [Získat aktuálně aktivní klíč v rámci sady klíčů](/graph/api/trustframeworkkeyset-getactivekey)
- [Generování klíče v sady klíčů](/graph/api/trustframeworkkeyset-generatekey)
- [Nahrání tajného kódu založeného na řetězci](/graph/api/trustframeworkkeyset-uploadsecret)
- [Nahrání certifikátu X. 509](/graph/api/trustframeworkkeyset-uploadcertificate)
- [Odeslat certifikát formátu PKCS12](/graph/api/trustframeworkkeyset-uploadpkcs12)

## <a name="applications"></a>Aplikace

- [Výpis aplikací](/graph/api/application-list)
- [Vytvoření aplikace](/graph/api/resources/application)
- [Aktualizovat aplikaci](/graph/api/application-update)
- [Vytvořit servicePrincipal](/graph/api/resources/serviceprincipal)
- [Vytvořit oauth2Permission grant](/graph/api/resources/oauth2permissiongrant)
- [Odstranit aplikaci](/graph/api/application-delete)

## <a name="application-extension-properties"></a>Vlastnosti rozšíření aplikace

- [Seznam vlastností rozšíření](/graph/api/application-list-extensionproperty)

Azure AD B2C poskytuje adresář, který může obsahovat 100 vlastních atributů na uživatele. V případě toků uživatelů se tyto vlastnosti rozšíření [spravují pomocí Azure Portal](user-flow-custom-attributes.md). Pro vlastní zásady Azure AD B2C vytvoří pro vás vlastnost, při prvním zapíše hodnotu do vlastnosti Extension.

## <a name="audit-logs"></a>Protokoly auditu

- [Vypsat protokoly auditu](/graph/api/directoryaudit-list)

Další informace o přístupu k protokolům auditu Azure AD B2C najdete v tématu [přístup k protokolům auditu Azure AD B2C](view-audit-logs.md).

## <a name="conditional-access"></a>Podmíněný přístup

- [Seznam všech zásad podmíněného přístupu](/graph/api/conditionalaccessroot-list-policies?view=graph-rest-beta&tabs=http)
- [Čtení vlastností a vztahů zásad podmíněného přístupu](/graph/api/conditionalaccesspolicy-get)
- [Vytvořit nové zásady podmíněného přístupu](/graph/api/resources/application)
- [Aktualizace zásad podmíněného přístupu](/graph/api/conditionalaccesspolicy-update)
- [Odstranění zásad podmíněného přístupu](/graph/api/conditionalaccesspolicy-delete)

## <a name="code-sample-how-to-programmatically-manage-user-accounts"></a>Ukázka kódu: jak programově spravovat uživatelské účty

Tato ukázka kódu je Konzolová aplikace .NET Core, která používá [sadu SDK Microsoft Graph](/graph/sdks/sdks-overview) k interakci s rozhraním API Microsoft Graph. Jeho kód ukazuje, jak volat rozhraní API pro programovou správu uživatelů v klientovi Azure AD B2C.
Můžete [si stáhnout ukázkový archiv](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management/archive/master.zip) (*. zip), [Procházet úložiště](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management) na GitHubu nebo klonovat úložiště:

```cmd
git clone https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management.git
```

Po získání ukázky kódu ji nakonfigurujte pro vaše prostředí a pak Sestavte projekt:

1. Otevřete projekt v [aplikaci Visual Studio](https://visualstudio.microsoft.com) nebo [Visual Studio Code](https://code.visualstudio.com).
1. Otevřete `src/appsettings.json`.
1. V `appSettings` oddílu nahraďte `your-b2c-tenant` názvem vašeho tenanta a `Application (client) ID` a `Client secret` hodnotami pro registraci aplikace pro správu. Další informace najdete v tématu [Registrace aplikace Microsoft Graph](microsoft-graph-get-started.md).
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

`RunAsync`Metoda v souboru _program. cs_ :

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

Inicializovaná *GraphServiceClient* se pak použije v _userservice předefinovala smazání. cs_ k provedení operací správy uživatelů. Například získání seznamu uživatelských účtů v tenantovi:

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

<!-- LINK -->

[graph-objectIdentity]: /graph/api/resources/objectidentity
[graph-user]: (https://docs.microsoft.com/graph/api/resources/user)
