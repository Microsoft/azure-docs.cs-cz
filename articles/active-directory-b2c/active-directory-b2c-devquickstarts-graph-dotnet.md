---
title: Použít Graph API v Azure Active Directory B2C | Microsoft Docs
description: Postup volání Graph API pro tenanta B2C pomocí identity aplikace pro automatizaci procesu.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/07/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: c7fcbbbfcc2192160ca852538c015a365518e448
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2019
ms.locfileid: "71065945"
---
# <a name="azure-ad-b2c-use-the-azure-ad-graph-api"></a>Azure AD B2C: Použití Graph API v Azure AD

>[!NOTE]
> Ke správě uživatelů v adresáři Azure AD B2C je nutné použít [Graph API Azure AD](/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-operations-overview) . To se liší od rozhraní Microsoft Graph API. Další informace najdete [tady](https://blogs.msdn.microsoft.com/aadgraphteam/2016/07/08/microsoft-graph-or-azure-ad-graph/).

Klienti Azure Active Directory B2C (Azure AD B2C) mají hodně velký počet. To znamená, že je třeba provést mnoho běžných úloh správy klientů programově. Primárním příkladem je Správa uživatelů. Možná budete muset migrovat existující úložiště uživatelů do tenanta B2C. Je možné, že budete chtít hostovat registraci uživatele na vlastní stránce a vytvořit v adresáři Azure AD B2C uživatelské účty na pozadí. Tyto typy úloh vyžadují možnost vytvářet, číst, aktualizovat a odstraňovat uživatelské účty. Tyto úlohy můžete provádět pomocí Graph API služby Azure AD.

Pro klienty B2C existují dva primární režimy komunikace s Graph API.

* Pro interaktivní úlohy spouštěné v jednom okamžiku byste při provádění úkolů měli v tenantovi B2C fungovat jako účet správce. Tento režim vyžaduje, aby se správce přihlásil s přihlašovacími údaji předtím, než může správce provést jakákoli volání Graph API.
* Pro automatizované nepřetržité úkoly byste měli použít nějaký typ účtu služby, který zadáte s potřebnými oprávněními k provádění úloh správy. V Azure AD to můžete udělat tak, že zaregistrujete aplikaci a ověřujete ji ve službě Azure AD. To se provádí pomocí **ID aplikace** , které používá [udělení přihlašovacích údajů klienta OAuth 2,0](../active-directory/develop/service-to-service.md). V tomto případě aplikace funguje stejně jako uživatel, nikoli jako uživatel, pro volání Graph API.

V tomto článku se dozvíte, jak použít případ automatizovaného použití. Vytvoříte rozhraní .NET 4,5 `B2CGraphClient` , které provede operace vytvoření, čtení, aktualizace a odstranění (CRUD) uživatelem. Klient bude mít rozhraní příkazového řádku (CLI) systému Windows, které umožňuje vyvolání různých metod. Kód je však napsán tak, aby se choval neinteraktivním, automatizovaným způsobem.

## <a name="get-an-azure-ad-b2c-tenant"></a>Získat klienta Azure AD B2C
Předtím, než budete moci vytvořit aplikace nebo uživatele, potřebujete klienta Azure AD B2C. Pokud už tenant nemáte, [začněte s Azure AD B2C](active-directory-b2c-get-started.md).

## <a name="register-your-application-in-your-tenant"></a>Registrace aplikace ve vašem tenantovi
Až budete mít klienta B2C, musíte aplikaci zaregistrovat pomocí [Azure Portal](https://portal.azure.com).

> [!IMPORTANT]
> Pokud chcete použít Graph API s vaším klientem B2C, musíte aplikaci zaregistrovat pomocí služby *Registrace aplikací* v nabídce Azure Portal, **ne** v *aplikacích* Azure AD B2C's. Následující pokyny vás zavedou do příslušné nabídky. Nemůžete znovu použít existující aplikace B2C, které jste zaregistrovali v nabídce *aplikace* Azure AD B2C's.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyberte svého tenanta Azure AD B2C tím, že v pravém horním rohu stránky vyberete svůj účet.
3. V levém navigačním podokně vyberte **všechny služby**, klikněte na **Registrace aplikací**a pak klikněte na **Nová registrace**.
4. Postupujte podle zobrazených výzev a vytvořte novou aplikaci.
    1. Přidat vhodný název
    2. Vybrat **účty jenom v tomto organizačním adresáři**
    3. Jako typ aplikace vyberte **Web** a zadejte **adresu URL pro přihlášení** (např. `https://B2CGraphAPI`), protože to není pro tento příklad relevantní.
    4. Klikněte na zaregistrovat.
5. Aplikace se nyní zobrazí v seznamu aplikací, kliknutím na ni získáte **ID aplikace** (označované také jako ID klienta). Zkopírujte ho, jak ho budete potřebovat v pozdější části.
6. V nabídce nastavení klikněte na **certifikáty & tajných**kódů.
7. V části **tajné klíče klienta** klikněte na **nový tajný klíč klienta**, zadejte popis tajného kódu a vyberte dobu trvání a pak klikněte na **Přidat**. Zkopírujte hodnotu tajného klíče (označuje se také jako tajný klíč klienta) pro použití v pozdější části.

## <a name="configure-create-read-and-update-permissions-for-your-application"></a>Konfigurace oprávnění vytvořit, číst a aktualizovat pro vaši aplikaci
Nyní je nutné nakonfigurovat aplikaci tak, aby získala všechna požadovaná oprávnění k vytváření, čtení, aktualizaci a odstraňování uživatelů.

1. V nabídce registrace aplikací Azure Portal vyberte svou aplikaci.
2. V nabídce nastavení klikněte na **požadovaná oprávnění**.
3. V nabídce požadovaná oprávnění klikněte na **Windows Azure Active Directory**.
4. V nabídce Povolit přístup vyberte oprávnění **číst a zapisovat data adresáře** z **oprávnění aplikace** a klikněte na **Uložit**.
5. Nakonec zpátky v nabídce požadovaná oprávnění klikněte na tlačítko **udělit oprávnění** .

Nyní máte aplikaci, která má oprávnění k vytváření, čtení a aktualizaci uživatelů z vašeho tenanta B2C.

> [!NOTE]
> Udělení oprávnění může trvat několik minut, než se plně zpracuje.
>
>

## <a name="configure-delete-or-update-password-permissions-for-your-application"></a>Konfigurace oprávnění k odstranění nebo aktualizaci hesla pro vaši aplikaci
V současné době oprávnění *číst a zapisovat data adresáře* nezahrnují možnost odstraňovat uživatele nebo aktualizovat hesla uživatelů. Pokud chcete, aby vaše aplikace měla možnost odstraňovat uživatele nebo aktualizovat hesla, musíte provést tyto kroky navíc, které zahrnují PowerShell, jinak můžete přejít k další části.

Napřed nainstalujte [modul Azure AD PowerShell V1 (MSOnline)](https://docs.microsoft.com/powershell/azure/active-directory/install-msonlinev1?view=azureadps-1.0), pokud ho ještě nemáte nainstalovaný.

```powershell
Install-Module MSOnline
```

Po instalaci modulu PowerShellu se připojte k vašemu Azure AD B2C tenantovi.

> [!IMPORTANT]
> Musíte použít účet správce klienta B2C, který je **místní** pro tenanta B2C. Tyto účty vypadají takto: myusername@myb2ctenant.onmicrosoft.com.

```powershell
Connect-MsolService
```

Nyní pomocí **ID aplikace** ve skriptu níže přiřadíte aplikaci roli správce uživatelských účtů. Tyto role mají dobře známé identifikátory, takže stačí zadat **ID aplikace** ve skriptu níže.

```powershell
$applicationId = "<YOUR_APPLICATION_ID>"
$sp = Get-MsolServicePrincipal -AppPrincipalId $applicationId
Add-MsolRoleMember -RoleObjectId fe930be7-5e62-47db-91af-98c3a49a38b1 -RoleMemberObjectId $sp.ObjectId -RoleMemberType servicePrincipal
```

Vaše aplikace má teď taky oprávnění odstraňovat uživatele nebo aktualizovat hesla z vašeho tenanta B2C.

## <a name="download-configure-and-build-the-sample-code"></a>Stažení, konfigurace a sestavení ukázkového kódu
Nejdřív Stáhněte vzorový kód a spusťte ho. Pak se o to podíváme podrobněji.  [Vzorový kód si můžete stáhnout jako soubor. zip](https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet/archive/master.zip). Můžete ho také klonovat do adresáře podle vašeho výběru:

```cmd
git clone https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet.git
```

Otevřete řešení `B2CGraphClient\B2CGraphClient.sln` sady Visual Studio v aplikaci Visual Studio. V projektu otevřete soubor `App.config`. `B2CGraphClient` Nahraďte tři nastavení aplikace vlastními hodnotami:

```xml
<appSettings>
    <add key="b2c:Tenant" value="{Your Tenant Name}" />
    <add key="b2c:ClientId" value="{The ApplicationID from above}" />
    <add key="b2c:ClientSecret" value="{The Key from above}" />
</appSettings>
```

[!INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Potom klikněte pravým tlačítkem `B2CGraphClient` na řešení a znovu sestavte ukázku. V případě úspěchu byste teď měli mít `B2C.exe` spustitelný soubor umístěný v. `B2CGraphClient\bin\Debug`

## <a name="build-user-crud-operations-by-using-the-graph-api"></a>Sestavování operací CRUD uživatelem pomocí Graph API
Chcete-li použít B2CGraphClient, otevřete `cmd` příkazový řádek systému Windows a změňte adresář `Debug` na adresář. Pak spusťte `B2C Help` příkaz.

```cmd
cd B2CGraphClient\bin\Debug
B2C Help
```

Tím se zobrazí stručný popis jednotlivých příkazů. Pokaždé, když vyvoláte jeden z `B2CGraphClient` těchto příkazů, vytvoří se žádost o Graph API služby Azure AD.

### <a name="get-an-access-token"></a>Získání přístupového tokenu
Jakýkoli požadavek na Graph API vyžaduje přístupový token pro ověřování. `B2CGraphClient`pomocí Open Source Active Directory Authentication Library (ADAL) pomůžete získat přístupové tokeny. ADAL usnadňuje získání tokenu tím, že poskytuje jednoduché rozhraní API a postará se o některé důležité podrobnosti, jako jsou přístupové tokeny pro ukládání do mezipaměti. Nemusíte používat ADAL k získání tokenů, ale. Tokeny můžete získat i tak, že provedete požadavky HTTP.

> [!NOTE]
> Tato ukázka kódu používá ADAL v2, aby mohl komunikovat s Graph API.  Abyste získali přístupové tokeny, které se dají použít s Graph API Azure AD, musíte použít ADAL v2 nebo V3.
>
>

Při `B2CGraphClient` spuštění vytvoří instanci `B2CGraphClient` třídy. Konstruktor této třídy nastavuje generování uživatelského rozhraní ADAL pro ověřování:

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

`B2C Get-User` Příkaz použijeme jako příklad. Při `B2C Get-User` vyvolání bez jakýchkoli dalších vstupů `B2CGraphClient.GetAllUsers(...)` volá rozhraní příkazového řádku metodu. Tato metoda volá `B2CGraphClient.SendGraphGetRequest(...)`, která odešle požadavek HTTP GET do Graph API. Před `B2CGraphClient.SendGraphGetRequest(...)` odesláním žádosti Get nejprve získá přístupový token pomocí knihovny ADAL:

```csharp
public async Task<string> SendGraphGetRequest(string api, string query)
{
    // First, use ADAL to acquire a token by using the app's identity (the credential)
    // The first parameter is the resource we want an access_token for; in this case, the Graph API.
    AuthenticationResult result = authContext.AcquireToken("https://graph.windows.net", credential);

    ...

```

Přístupový token pro Graph API můžete získat voláním metody ADAL `AuthenticationContext.AcquireToken(...)` . ADAL pak vrátí `access_token` , který představuje identitu aplikace.

### <a name="read-users"></a>Čtení uživatelů
Pokud chcete získat seznam uživatelů nebo získat konkrétního uživatele z Graph API, můžete odeslat požadavek HTTP `GET` `/users` na koncový bod. Požadavek na všechny uživatele v tenantovi vypadá takto:

```
GET https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Chcete-li zobrazit tuto žádost, spusťte příkaz:

 ```cmd
 B2C Get-User
 ```

Pamatujte na to dvě důležité věci:

* Přístupový token získaný přes ADAL se přidá do `Authorization` hlavičky `Bearer` pomocí schématu.
* Pro klienty B2C je nutné použít parametr `api-version=1.6`dotazu.

Obě tyto podrobnosti jsou zpracovávány v `B2CGraphClient.SendGraphGetRequest(...)` metodě:

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
Při vytváření uživatelských účtů v tenantovi B2C můžete odeslat požadavek HTTP `POST` `/users` na koncový bod:

```
POST https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 338

{
    // All of these properties are required to create consumer users.

    "accountEnabled": true,
    "signInNames": [                            // controls which identifier the user uses to sign in to the account
        {
            "type": "emailAddress",             // can be 'emailAddress' or 'userName'
            "value": "joeconsumer@gmail.com"
        }
    ],
    "creationType": "LocalAccount",            // always set to 'LocalAccount'
    "displayName": "Joe Consumer",                // a value that can be used for displaying to the end user
    "mailNickname": "joec",                        // an email alias for the user
    "passwordProfile": {
        "password": "P@ssword!",
        "forceChangePasswordNextLogin": false   // always set to false
    },
    "passwordPolicies": "DisablePasswordExpiration"
}
```

Většina těchto vlastností v této žádosti je nutná k vytvoření uživatelů spotřebitelů. Pokud se chcete dozvědět víc, klikněte [sem](/previous-versions/azure/ad/graph/api/users-operations#CreateLocalAccountUser). Všimněte si, `//` že komentáře byly zahrnuty pro ilustraci. Nezahrnujte je do skutečné žádosti.

Chcete-li zobrazit žádost, spusťte jeden z následujících příkazů:

```cmd
B2C Create-User ..\..\..\usertemplate-email.json
B2C Create-User ..\..\..\usertemplate-username.json
```

`Create-User` Příkaz jako vstupní parametr bere soubor. JSON. Obsahuje reprezentace JSON objektu uživatele. V ukázkovém kódu jsou dva soubory Sample. JSON: `usertemplate-email.json` a. `usertemplate-username.json` Tyto soubory můžete upravit tak, aby vyhovovaly vašim potřebám. Kromě požadovaných polí uvedených výše jsou do těchto souborů zahrnuty několik volitelných polí, která můžete použít. Podrobnosti o nepovinných polích najdete v referenčních informacích k [entitě Azure AD Graph API](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#user-entity).

Můžete vidět, jak je požadavek POST vytvořen `B2CGraphClient.SendGraphPostRequest(...)`.

* Připojí přístupový token k `Authorization` hlavičce požadavku.
* Nastaví `api-version=1.6`.
* Obsahuje objekt uživatele JSON v těle žádosti.

> [!NOTE]
> Pokud účty, které chcete migrovat z existujícího úložiště uživatele, mají nižší sílu hesla než [silné síly](/previous-versions/azure/jj943764(v=azure.100)), kterou vynutila Azure AD B2C, můžete požadavek na silné heslo zakázat pomocí `DisableStrongPassword` hodnoty v části `passwordPolicies`vlastnost. Můžete například upravit žádost o vytvoření uživatele uvedenou výše, a to takto: `"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"`.
>
>

### <a name="update-consumer-user-accounts"></a>Aktualizace uživatelských účtů spotřebitelů
Když aktualizujete uživatelské objekty, bude tento proces podobný jako ten, který používáte k vytvoření uživatelských objektů. Tento proces ale používá metodu http `PATCH` :

```
PATCH https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 37

{
    "displayName": "Joe Consumer"                // this request updates only the user's displayName
}
```

Zkuste aktualizovat uživatele tak, že aktualizujete soubory JSON s novými daty. Pak můžete použít `B2CGraphClient` ke spuštění jednoho z těchto příkazů:

```cmd
B2C Update-User <user-object-id> ..\..\..\usertemplate-email.json
B2C Update-User <user-object-id> ..\..\..\usertemplate-username.json
```

Podrobnosti o `B2CGraphClient.SendGraphPatchRequest(...)` tom, jak odeslat tuto žádost, najdete v metodě.

### <a name="search-users"></a>Hledat uživatele
Uživatele v tenantovi B2C můžete vyhledat několika způsoby. Jedna, pomocí ID objektu uživatele nebo dvě použijte přihlašovací jméno uživatele identifikátorem (tj. `signInNames` vlastnost).

Pokud chcete vyhledat konkrétního uživatele, spusťte jeden z následujících příkazů:

```cmd
B2C Get-User <user-object-id>
B2C Get-User <filter-query-expression>
```

Tady je několik příkladů:

```cmd
B2C Get-User 2bcf1067-90b6-4253-9991-7f16449c2d91
B2C Get-User $filter=signInNames/any(x:x/value%20eq%20%27joeconsumer@gmail.com%27)
```

### <a name="delete-users"></a>Odstraňování uživatelů
Proces odstranění uživatele je jednoduchý. Použijte metodu http `DELETE` a vytvořte adresu URL se správným ID objektu:

```
DELETE https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Chcete-li zobrazit příklad, zadejte tento příkaz a zobrazte žádost o odstranění, která je vytištěna do konzoly:

```cmd
B2C Delete-User <object-id-of-user>
```

Podrobnosti o `B2CGraphClient.SendGraphDeleteRequest(...)` tom, jak odeslat tuto žádost, najdete v metodě.

Kromě správy uživatelů můžete provádět i mnoho dalších akcí s Graph API Azure AD. [Odkaz na Graph API Azure AD](/previous-versions/azure/ad/graph/api/api-catalog) poskytuje podrobné informace o každé akci společně s ukázkovými požadavky.

## <a name="use-custom-attributes"></a>Použití vlastních atributů
Většina aplikací pro spotřebitele potřebuje Uložit nějaký typ vlastních informací o profilu uživatele. Jedním ze způsobů, jak to provést, je definovat vlastní atribut v tenantovi B2C. Tento atribut pak můžete považovat za stejný způsob, jakým pracujete s jinou vlastností objektu User. Atribut můžete aktualizovat, odstranit atribut, dotaz podle atributu, odeslat atribut jako deklaraci deklarace v tokenech přihlášení a další.

Chcete-li definovat vlastní atribut v tenantovi B2C, přečtěte si [odkaz na vlastní atribut B2C](active-directory-b2c-reference-custom-attr.md).

Vlastní atributy definované v tenantovi B2C můžete zobrazit pomocí `B2CGraphClient`:

```cmd
B2C Get-B2C-Application
B2C Get-Extension-Attribute <object-id-in-the-output-of-the-above-command>
```

Výstup těchto funkcí odhalí podrobnosti každého vlastního atributu, například:

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

V objektech uživatelů můžete použít celé jméno, `extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number`například, jako vlastnost.  Aktualizujte soubor. JSON novou vlastností a hodnotou vlastnosti a pak spusťte:

```cmd
B2C Update-User <object-id-of-user> <path-to-json-file>
```

Pomocí nástroje `B2CGraphClient`máte aplikaci služby, která může spravovat uživatele klienta B2C programově. `B2CGraphClient`pomocí vlastní identity aplikace ověří Graph API služby Azure AD. Získává taky tokeny pomocí tajného klíče klienta. Při začleňování této funkce do aplikace si pamatujte několik klíčových bodů pro aplikace B2C:

* Aplikaci musíte udělit správná oprávnění v tenantovi.
* K získání přístupových tokenů teď potřebujete použít ADAL (ne MSAL). (Můžete také odesílat zprávy protokolu přímo bez použití knihovny.)
* Při volání Graph API použijte `api-version=1.6`.
* Když vytváříte a aktualizujete uživatele spotřebitelů, je potřeba pár vlastností, jak je popsáno výše.

Pokud máte nějaké dotazy nebo žádosti o akce, které chcete provést, pomocí Graph API ve vašem tenantovi B2C, ponechte Tento článek komentář nebo zapište problém v úložišti ukázek kódu GitHubu.

