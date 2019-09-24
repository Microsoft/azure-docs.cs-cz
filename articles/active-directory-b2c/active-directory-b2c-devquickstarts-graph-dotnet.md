---
title: Použití Graph API v Azure Active Directory B2C
description: Jak spravovat uživatele v klientovi Azure AD B2C zavoláním Graph API služby Azure AD a použitím identity aplikace k automatizaci procesu.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 903492d790cdde93dfe84763de139fe85e26b234
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2019
ms.locfileid: "71218285"
---
# <a name="azure-ad-b2c-use-the-azure-ad-graph-api"></a>Azure AD B2C: Použití Graph API v Azure AD

Klienti Azure Active Directory B2C (Azure AD B2C) můžou mít tisíce nebo miliony uživatelů. To znamená, že je třeba provést mnoho běžných úloh správy klientů programově. Primárním příkladem je Správa uživatelů.

Možná budete muset migrovat existující úložiště uživatelů do tenanta B2C. Je možné, že budete chtít hostovat registraci uživatele na vlastní stránce a vytvořit v adresáři Azure AD B2C uživatelské účty na pozadí. Tyto druhy úloh vyžadují možnost vytvářet, číst, aktualizovat a odstraňovat uživatelské účty. Tyto úlohy můžete provádět pomocí Graph API Azure AD.

Pro klienty B2C existují dva primární režimy komunikace s Graph API:

* Pro **interaktivní**úlohy spouštěné v jednom okamžiku byste při provádění úkolů měli v tenantovi B2C fungovat jako účet správce. Tento režim vyžaduje, aby se správce přihlásil s přihlašovacími údaji předtím, než může správce provést jakákoli volání Graph API.
* Pro **automatizované**nepřetržité úkoly byste měli použít nějaký typ účtu služby, který zadáte s potřebnými oprávněními k provádění úloh správy. V Azure AD to můžete udělat tak, že zaregistrujete aplikaci a ověřujete ji ve službě Azure AD. To se provádí pomocí *ID aplikace* , které používá [udělení přihlašovacích údajů klienta OAuth 2,0](../active-directory/develop/service-to-service.md). V tomto případě aplikace funguje stejně jako uživatel, nikoli jako uživatel, pro volání Graph API.

V tomto článku se dozvíte, jak provést automatizovaný případ použití. Vytvoříte rozhraní .NET 4,5 `B2CGraphClient` , které provede operace vytvoření, čtení, aktualizace a odstranění (CRUD) uživatelem. Klient bude mít rozhraní příkazového řádku (CLI) systému Windows, které umožňuje vyvolání různých metod. Kód je však napsán tak, aby se choval v neinteraktivním, automatizovaném způsobem.

>[!IMPORTANT]
> Ke správě uživatelů v adresáři Azure AD B2C je **nutné** použít [Graph API Azure AD](../active-directory/develop/active-directory-graph-api-quickstart.md) . Graph API Azure AD se liší od rozhraní Microsoft Graph API. Další informace najdete v tomto příspěvku na blogu MSDN: [Microsoft Graph nebo Azure AD Graph](https://blogs.msdn.microsoft.com/aadgraphteam/2016/07/08/microsoft-graph-or-azure-ad-graph/).

## <a name="prerequisites"></a>Požadavky

Předtím, než budete moci vytvořit aplikace nebo uživatele, potřebujete klienta Azure AD B2C. Pokud ho ještě nemáte, [Vytvořte klienta Azure Active Directory B2C](tutorial-create-tenant.md).

## <a name="register-an-application"></a>Zaregistrovat aplikaci

Jakmile budete mít klienta Azure AD B2C, je nutné zaregistrovat aplikaci pro správu pomocí [Azure Portal](https://portal.azure.com). Také je potřeba udělit mu oprávnění potřebná k provádění úloh správy jménem automatizovaného skriptu nebo aplikace pro správu.

### <a name="register-application-in-azure-active-directory"></a>Registrovat aplikaci v Azure Active Directory

Pokud chcete používat Graph API Azure AD s vaším klientem B2C, musíte aplikaci zaregistrovat pomocí pracovního postupu Azure Active Directory **Registrace aplikací** .

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a přejděte do adresáře, který obsahuje vašeho tenanta Azure AD B2C.
1. V nabídce vlevo vyberte **Azure Active Directory** (*není* Azure AD B2C). Případně vyberte **všechny služby** a pak vyhledejte a vyberte **Azure Active Directory**.
1. V části **Spravovat** v levé nabídce vyberte **Registrace aplikací (starší verze)** .
1. Vybrat **registraci nové aplikace**
1. Zadejte název aplikace. Například aplikace pro *správu*.
1. Do **přihlašovací adresy URL**zadejte libovolnou platnou adresu URL. Příklad: *https://localhost* . Tento koncový bod nemusí být dosažitelný, ale musí být platnou adresou URL.
1. Vyberte **Vytvořit**.
1. Poznamenejte si **ID aplikace** , které se zobrazí na stránce s přehledem **zaregistrovaných aplikací** . Tuto hodnotu můžete použít pro konfiguraci v pozdějším kroku.

### <a name="assign-api-access-permissions"></a>Přiřazení přístupových oprávnění k rozhraní API

1. Na stránce Přehled **zaregistrovaných aplikací** vyberte **Nastavení**.
1. V části **přístup přes rozhraní API**vyberte **požadovaná oprávnění**.
1. Vyberte **Windows Azure Active Directory**.
1. V části **oprávnění aplikace**vyberte **číst a zapsat data adresáře**.
1. Vyberte **Uložit**.
1. Vyberte **udělit oprávnění**a pak vyberte **Ano**. Aby bylo možné plně šířit oprávnění, může trvat několik minut.

### <a name="create-client-secret"></a>Vytvořit tajný klíč klienta

1. V části **přístup k rozhraní API**vyberte **klíče**.
1. Do pole **Popis klíče** zadejte popis klíče. Například *klíč správy*.
1. Vyberte **dobu** platnosti a pak vyberte **Uložit**.
1. Poznamenejte si **hodnotu**klíče. Tuto hodnotu můžete použít pro konfiguraci v pozdějším kroku.

Nyní máte aplikaci, která má oprávnění k *vytváření*, *čtení*a *aktualizaci* uživatelů ve vašem tenantovi Azure AD B2C. Přejděte k další části a přidejte oprávnění k *odstranění* a *aktualizaci hesla* uživatele.

## <a name="add-user-delete-and-password-update-permissions"></a>Přidání oprávnění k odstranění a aktualizaci hesla uživatele

Oprávnění *ke čtení a zápisu dat adresáře* , které jste předtím udělili, **nezahrnuje možnost** odstraňovat uživatele nebo aktualizovat hesla.

Pokud chcete aplikaci umožnit, aby odstranila uživatele nebo aktualizovala hesla, musíte jí udělit roli *správce uživatele* .

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a přejděte do adresáře, který obsahuje vašeho tenanta Azure AD B2C.
1. V nabídce vlevo vyberte **Azure AD B2C** . Případně vyberte **všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
1. V části **Spravovat**vyberte **role a správci**.
1. Vyberte roli **správce uživatele** .
1. Vyberte **Přidat přiřazení**.
1. Do textového pole pro **Výběr** zadejte název aplikace, kterou jste předtím zaregistrovali, například *aplikaci pro správu*. Vyberte aplikaci, která se zobrazí ve výsledcích hledání.
1. Vyberte **Přidat**. Aby bylo možné plně šířit oprávnění, může trvat několik minut.

Vaše aplikace Azure AD B2C má teď další oprávnění, která jsou nutná k odstraňování uživatelů nebo aktualizaci hesel v tenantovi B2C.

## <a name="get-the-sample-code"></a>Získání ukázkového kódu

Ukázka kódu je Konzolová aplikace .NET, která používá [Active Directory Authentication Library (ADAL)](../active-directory/develop/active-directory-authentication-libraries.md) k interakci s Graph API Azure AD. Jeho kód ukazuje, jak volat rozhraní API pro programovou správu uživatelů v klientovi Azure AD B2C.

Můžete [si stáhnout ukázkový archiv](https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet/archive/master.zip) (\*. zip) nebo klonovat úložiště GitHub:

```cmd
git clone https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet.git
```

Po získání ukázky kódu ji nakonfigurujte pro vaše prostředí a pak Sestavte projekt:

1. Otevřete řešení `B2CGraphClient\B2CGraphClient.sln` v sadě Visual Studio.
1. V projektu **B2CGraphClient** otevřete soubor *App. config* .
1. Nahraďte `<appSettings>` oddíl následujícím kódem XML. Potom nahraďte `{your-b2c-tenant}` názvem vašeho tenanta a `{Application ID}` a `{Client secret}` hodnotami, které jste si poznamenali dříve.

    ```xml
    <appSettings>
        <add key="b2c:Tenant" value="{your-b2c-tenant}.onmicrosoft.com" />
        <add key="b2c:ClientId" value="{Application ID}" />
        <add key="b2c:ClientSecret" value="{Client secret}" />
    </appSettings>
    ```

1. Sestavte řešení. V Průzkumník řešení klikněte pravým tlačítkem na řešení **B2CGraphClient** a pak vyberte znovu **Sestavit řešení**.

Pokud je sestavení úspěšné, `B2C.exe` konzolovou aplikaci lze najít v. `B2CGraphClient\bin\Debug`

## <a name="review-the-sample-code"></a>Revize ukázkového kódu

Chcete-li použít B2CGraphClient, otevřete příkazový řádek (`cmd.exe`) a přejděte do `Debug` adresáře projektu. Potom spusťte `B2C Help` příkaz.

```cmd
cd B2CGraphClient\bin\Debug
B2C Help
```

`B2C Help` Příkaz zobrazí stručný popis dostupných dílčích příkazů. Pokaždé, když vyvoláte jeden z jeho dílčích příkazů, `B2CGraphClient` pošle žádost službě Azure AD Graph API.

Následující části popisují, jak kód aplikace volá Graph API služby Azure AD.

### <a name="get-an-access-token"></a>Získání přístupového tokenu

Jakýkoli požadavek na Graph API Azure AD vyžaduje přístupový token pro ověřování. `B2CGraphClient`pomáhá získat přístupové tokeny pomocí Open Source Active Directory Authentication Library (ADAL). ADAL usnadňuje získání tokenu tím, že poskytuje pomocné rozhraní API a postará se o několik důležitých podrobností, jako jsou přístupové tokeny pro ukládání do mezipaměti. K získání tokenů ale nemusíte používat ADAL. Místo toho můžete získat tokeny ručním vytvářením požadavků HTTP.

> [!NOTE]
> K získání přístupových tokenů, které lze použít s Graph API Azure AD, je nutné použít ADAL v2 nebo vyšší. Nelze použít ADAL v1.

Když `B2CGraphClient` se spustí, vytvoří instanci `B2CGraphClient` třídy. Konstruktor této třídy nastavuje generování uživatelského rozhraní ADAL pro ověřování:

```csharp
public B2CGraphClient(string clientId, string clientSecret, string tenant)
{
    // The client_id, client_secret, and tenant are provided in Program.cs, which pulls the values from App.config
    this.clientId = clientId;
    this.clientSecret = clientSecret;
    this.tenant = tenant;

    // The AuthenticationContext is ADAL's primary class, in which you indicate the tenant to use.
    this.authContext = new AuthenticationContext("https://login.microsoftonline.com/" + tenant);

    // The ClientCredential is where you pass in your client_id and client_secret, which are
    // provided to Azure AD in order to receive an access_token by using the app's identity.
    this.credential = new ClientCredential(clientId, clientSecret);
}
```

Pojďme použít `B2C Get-User` příkaz jako příklad.

Když `B2C Get-User` je vyvolána bez dalších argumentů, aplikace `B2CGraphClient.GetAllUsers()` volá metodu. `GetAllUsers()`pak volání `B2CGraphClient.SendGraphGetRequest()`, která odesílají požadavek HTTP GET do Graph API Azure AD. Před `B2CGraphClient.SendGraphGetRequest()` odesláním žádosti Get nejprve získá přístupový token pomocí knihovny ADAL:

```csharp
public async Task<string> SendGraphGetRequest(string api, string query)
{
    // First, use ADAL to acquire a token by using the app's identity (the credential)
    // The first parameter is the resource we want an access_token for; in this case, the Graph API.
    AuthenticationResult result = authContext.AcquireToken("https://graph.windows.net", credential);
    ...
```

Přístupový token pro Graph API můžete získat voláním metody ADAL `AuthenticationContext.AcquireToken()` . ADAL pak vrátí `access_token` , který představuje identitu aplikace.

### <a name="read-users"></a>Čtení uživatelů

Pokud chcete získat seznam uživatelů nebo získat konkrétního uživatele ze Graph API služby Azure AD, můžete odeslat požadavek HTTP `GET` `/users` na koncový bod. Požadavek na všechny uživatele v tenantovi vypadá takto:

```HTTP
GET https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Chcete-li zobrazit tuto žádost, spusťte příkaz:

 ```cmd
 B2C Get-User
 ```

Pamatujte na to dvě důležité věci:

* Přístupový token získaný pomocí ADAL se přidá do `Authorization` hlavičky `Bearer` pomocí schématu.
* Pro klienty B2C je nutné použít parametr `api-version=1.6`dotazu.

Obě tyto podrobnosti jsou zpracovávány v `B2CGraphClient.SendGraphGetRequest()` metodě:

```csharp
public async Task<string> SendGraphGetRequest(string api, string query)
{
    ...

    // For B2C user management, be sure to use the 1.6 Graph API version.
    HttpClient http = new HttpClient();
    string url = "https://graph.windows.net/" + tenant + api + "?" + "api-version=1.6";
    if (!string.IsNullOrEmpty(query))
    {
        url += "&" + query;
    }

    // Append the access token for the Graph API to the Authorization header of the request by using the Bearer scheme.
    HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, url);
    request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
    HttpResponseMessage response = await http.SendAsync(request);

    ...
```

### <a name="create-consumer-user-accounts"></a>Vytváření uživatelských účtů uživatelů

Při vytváření uživatelských účtů v tenantovi B2C můžete odeslat požadavek HTTP `POST` `/users` na koncový bod. Následující požadavek HTTP `POST` ukazuje ukázkového uživatele, který se má vytvořit v tenantovi.

K vytvoření uživatelů spotřebitelů se vyžaduje většina vlastností v následující žádosti. `//` Komentáře byly zahrnuty pro ilustraci – Nezahrnovat je do skutečné žádosti.

```HTTP
POST https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 338

{
    // All of these properties are required to create consumer users.

    "accountEnabled": true,
    "signInNames": [                           // controls which identifier the user uses to sign in to the account
        {
            "type": "emailAddress",            // can be 'emailAddress' or 'userName'
            "value": "consumer@fabrikam.com"
        }
    ],
    "creationType": "LocalAccount",            // always set to 'LocalAccount'
    "displayName": "Consumer User",            // a value that can be used for displaying to the end user
    "mailNickname": "cuser",                   // an email alias for the user
    "passwordProfile": {
        "password": "P@ssword!",
        "forceChangePasswordNextLogin": false  // always set to false
    },
    "passwordPolicies": "DisablePasswordExpiration"
}
```

Chcete-li zobrazit žádost, spusťte jeden z následujících příkazů:

```cmd
B2C Create-User ..\..\..\usertemplate-email.json
B2C Create-User ..\..\..\usertemplate-username.json
```

`Create-User` Příkaz přijímá jako vstupní parametr soubor JSON, který obsahuje reprezentaci JSON objektu uživatele. Ukázka kódu obsahuje dva ukázkové soubory JSON: `usertemplate-email.json` a. `usertemplate-username.json` Tyto soubory můžete upravit tak, aby vyhovovaly vašim potřebám. Kromě požadovaných polí uvedených výše jsou do souborů zahrnuty několik volitelných polí.

Další informace o požadovaných a volitelných polích naleznete v tématu [entita a komplexní typ reference | Odkaz na Graph API](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference).

Můžete vidět, jak je požadavek POST vytvořen `B2CGraphClient.SendGraphPostRequest()`:

* Připojí přístupový token k `Authorization` hlavičce požadavku.
* Nastaví `api-version=1.6`.
* Obsahuje objekt uživatele JSON v těle žádosti.

> [!NOTE]
> Pokud mají účty, které chcete migrovat z existujícího úložiště uživatele, nižší sílu hesla, než je [silné silné síly, kterou vynutila Azure AD B2C](active-directory-b2c-reference-password-complexity.md), můžete požadavek na silný heslo zakázat pomocí `DisableStrongPassword` hodnoty v části `passwordPolicies`vlastnost. Můžete například upravit předchozí požadavek na vytvoření uživatele následujícím způsobem: `"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"`.

### <a name="update-consumer-user-accounts"></a>Aktualizace uživatelských účtů spotřebitelů

Když aktualizujete uživatelské objekty, bude tento proces podobný jako ten, který používáte k vytvoření objektů uživatele, ale používá metodu http `PATCH` :

```HTTP
PATCH https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 37

{
    "displayName": "Joe Consumer"    // this request updates only the user's displayName
}
```

Zkuste aktualizovat uživatele úpravou některých hodnot ve svých souborech JSON a potom pomocí `B2CGraphClient` spusťte jeden z těchto příkazů:

```cmd
B2C Update-User <user-object-id> ..\..\..\usertemplate-email.json
B2C Update-User <user-object-id> ..\..\..\usertemplate-username.json
```

Podrobnosti o `B2CGraphClient.SendGraphPatchRequest()` tom, jak odeslat tuto žádost, najdete v metodě.

### <a name="search-users"></a>Hledat uživatele

Uživatele v tenantovi B2C můžete vyhledat dvěma způsoby:

* Odkaz na **ID objektu**uživatele
* Odkazujte na jejich přihlašování identifikátorem, `signInNames` vlastnost.

Spusťte jeden z následujících příkazů, který vyhledá uživatele:

```cmd
B2C Get-User <user-object-id>
B2C Get-User <filter-query-expression>
```

Příklad:

```cmd
B2C Get-User 2bcf1067-90b6-4253-9991-7f16449c2d91
B2C Get-User $filter=signInNames/any(x:x/value%20eq%20%27consumer@fabrikam.com%27)
```

### <a name="delete-users"></a>Odstraňování uživatelů

Chcete-li odstranit uživatele, použijte `DELETE` metodu HTTP a vytvořte adresu URL s ID objektu uživatele:

```HTTP
DELETE https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Chcete-li zobrazit příklad, zadejte tento příkaz a zobrazte žádost o odstranění, která je vytištěna do konzoly:

```cmd
B2C Delete-User <object-id-of-user>
```

Podrobnosti o `B2CGraphClient.SendGraphDeleteRequest()` tom, jak odeslat tuto žádost, najdete v metodě.

Kromě správy uživatelů můžete provádět i mnoho dalších akcí s Graph API Azure AD. [Odkaz na Graph API Azure AD](/previous-versions/azure/ad/graph/api/api-catalog) poskytuje podrobné informace o každé akci společně s ukázkovými požadavky.

## <a name="use-custom-attributes"></a>Použití vlastních atributů

Většina aplikací pro spotřebitele potřebuje Uložit nějaký typ vlastních informací o profilu uživatele. Jedním ze způsobů, jak to udělat, je definovat vlastní atribut v tenantovi B2C. Tento atribut pak lze zařadit stejným způsobem jako jakoukoli jinou vlastnost objektu User. Atribut můžete aktualizovat, odstranit atribut, dotaz podle atributu, odeslat atribut jako deklaraci deklarace v tokenech přihlášení a další.

Chcete-li definovat vlastní atribut v tenantovi B2C, přečtěte si [odkaz na vlastní atribut B2C](active-directory-b2c-reference-custom-attr.md).

Vlastní atributy definované v tenantovi B2C můžete zobrazit pomocí následujících `B2CGraphClient` příkazů:

```cmd
B2C Get-B2C-Application
B2C Get-Extension-Attribute <object-id-in-the-output-of-the-above-command>
```

Výstup odhalí podrobnosti každého vlastního atributu. Příklad:

```json
{
      "odata.type": "Microsoft.DirectoryServices.ExtensionProperty",
      "objectType": "ExtensionProperty",
      "objectId": "cec6391b-204d-42fe-8f7c-89c2b1964fca",
      "deletionTimestamp": null,
      "appDisplayName": "",
      "name": "extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number",
      "dataType": "Integer",
      "isSyncedFromOnPremises": false,
      "targetObjects": [
        "User"
      ]
}
```

V objektech uživatelů můžete použít celé jméno, `extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number`například, jako vlastnost. Aktualizujte soubor JSON novou vlastností a hodnotou vlastnosti a pak spusťte:

```cmd
B2C Update-User <object-id-of-user> <path-to-json-file>
```

## <a name="next-steps"></a>Další kroky

Pomocí nástroje `B2CGraphClient`máte aplikaci služby, která může spravovat uživatele klienta B2C programově. `B2CGraphClient`pomocí vlastní identity aplikace ověří Graph API služby Azure AD. Získává taky tokeny pomocí tajného klíče klienta.

Při začleňování této funkce do vlastní aplikace si pamatujte několik klíčových bodů pro aplikace B2C:

* Udělte aplikaci požadovaná oprávnění v tenantovi.
* K získání přístupových tokenů teď potřebujete použít ADAL (ne MSAL). (Můžete také odesílat zprávy protokolu přímo bez použití knihovny.)
* Při volání Graph API použijte `api-version=1.6`.
* Když vytváříte a aktualizujete uživatele spotřebitelů, je potřeba pár vlastností, jak je popsáno výše.
