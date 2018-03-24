---
title: Pomocí rozhraní Graph API – Azure AD B2C | Microsoft Docs
description: Postup pro volání rozhraní Graph API pro klienta B2C identity aplikací pro automatizaci procesu.
services: active-directory-b2c
documentationcenter: .net
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 08/07/2017
ms.author: davidmu
ms.openlocfilehash: 2f95df26abcd2c0d5b62c395f92c359170d6d701
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="azure-ad-b2c-use-the-azure-ad-graph-api"></a>Azure AD B2C: Azure AD Graph API pomocí

>[!NOTE]
> Je nutné použít [Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-operations-overview?f=255&MSPPError=-2147217396) ke správě uživatelů v adresáři služby Azure AD B2C. To se liší od společnosti Microsoft Graph API. Další informace najdete [tady](https://blogs.msdn.microsoft.com/aadgraphteam/2016/07/08/microsoft-graph-or-azure-ad-graph/).

Azure Active Directory (Azure AD) B2C klientů jsou obvykle velké. To znamená, že mnoho běžné úlohy správy klienta musí být provedeny prostřednictvím kódu programu. Primární příkladem je Správa uživatelů. Možná budete muset migrovat stávající úložiště uživatele do klienta B2C. Můžete pro hostování registrace uživatele na vlastní stránky a vytvářet uživatelské účty ve svém adresáři Azure AD B2C na pozadí. Tyto typy úloh vyžadovat schopnost vytvářet, číst, aktualizovat a odstraňovat uživatelské účty. Můžete provést tyto úlohy pomocí rozhraní Azure AD Graph API.

U klientů B2C existují dva primární režimy komunikaci pomocí rozhraní Graph API.

* Pro interaktivní, jedno spuštění úlohy by měla fungovat jako účet správce v klientovi B2C při provádění úlohy. Tento režim vyžaduje správce a přihlaste se pomocí přihlašovacích údajů, před kterou správce může provádět volání rozhraní Graph API.
* Pro automatizované, nepřetržité úlohy měli byste použít nějaký typ účtu služby, abyste poskytli potřebná oprávnění k provádění úloh správy. Ve službě Azure AD to provedete tak, že registrace aplikace a ověřování do služby Azure AD. To se provádí pomocí **ID aplikace** používající [udělení pověření klienta OAuth 2.0](../active-directory/develop/active-directory-authentication-scenarios.md#daemon-or-server-application-to-web-api). V takovém případě aplikace funguje jako samostatně, nikoli jako uživatel, k volání rozhraní Graph API.

V tomto článku probereme, jak provádět případ automatizované použití. K předvedení, jsme budete sestavení rozhraní .NET 4.5 `B2CGraphClient` který provede uživatele vytvořit, číst, aktualizovat a odstranit operace. Klient bude mít rozhraní příkazového řádku Windows (CLI), který umožňuje vyvolání různé metody. Kód je však zapsána do chovat neinteraktivní, automatizované způsobem.

## <a name="get-an-azure-ad-b2c-tenant"></a>Získání klienta Azure AD B2C
Předtím, než můžete vytvořit aplikace nebo uživatele, nebo vůbec komunikovat s Azure AD, budete potřebovat klienta Azure AD B2C a účet globálního správce v klientovi. Pokud nemáte klienta již, [Začínáme s Azure AD B2C](active-directory-b2c-get-started.md).

## <a name="register-your-application-in-your-tenant"></a>Registrace vaší aplikace ve vašem klientovi
Až budete mít klienta B2C, budete muset registrace vaší aplikace pomocí [portálu Azure](https://portal.azure.com).

> [!IMPORTANT]
> Použít rozhraní Graph API pomocí svého klienta B2C, budete muset registraci vyhrazené aplikace pomocí Obecné *registrace aplikace* nabídky na portálu Azure **není** Azure AD B2C  *Aplikace* nabídky. Nelze znovu použít už existující B2C aplikace, které jste zaregistrovali v Azure AD B2C *aplikace* nabídky.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Výběrem účtu v pravém horním rohu stránky zvolte vašeho klienta Azure AD B2C.
3. V levém navigačním podokně zvolte **všechny služby**, klikněte na tlačítko **registrace aplikace**a klikněte na tlačítko **přidat**.
4. Postupujte podle zobrazených výzev a vytvořte novou aplikaci. 
    1. Vyberte **webová aplikace nebo rozhraní API** jako typ aplikace.    
    2. Zadejte **žádný identifikátor URI přesměrování** (např. https://B2CGraphAPI) jako není relevantní pro tento příklad.  
5. Budou aplikace teď objeví v seznamu aplikací, klikněte na ho získat **ID aplikace** (také označované jako ID klienta). Zkopírujte jej, protože ho budete potřebovat v další části.
6. V nabídce nastavení, klikněte na **klíče** a přidejte nový klíč (také označovaný jako sdílený tajný klíč klienta). Také zkopírujte jej pro použití v další části.

## <a name="configure-create-read-and-update-permissions-for-your-application"></a>Konfigurace vytvářet, číst a aktualizovat oprávnění pro vaši aplikaci
Teď je potřeba nakonfigurovat vaše aplikace a získejte potřebná oprávnění k vytvářet, číst, aktualizovat a odstraňovat uživatele.

1. Pokračování v nabídce portálu Azure registrace aplikace, vyberte svou aplikaci.
2. V nabídce nastavení, klikněte na **požadovaná oprávnění**.
3. V nabídce požadovaná oprávnění, klikněte na **Windows Azure Active Directory**.
4. Povolit přístup z nabídky vyberte **pro čtení a zápis dat adresáře** oprávnění z **oprávnění aplikací** a klikněte na tlačítko **Uložit**.
5. Nakonec zpět v nabídce požadovaná oprávnění, klikněte na **udělit oprávnění** tlačítko.

Teď máte aplikaci, která má oprávnění k vytváření, čtení a aktualizace uživatelů ze svého klienta B2C.

> [!NOTE]
> Udělení oprávnění Zkontrolujte trvat několik minut na úplné zpracování.
> 
> 

## <a name="configure-delete-permissions-for-your-application"></a>Konfigurace odstranění oprávnění pro vaši aplikaci
V současné době *pro čtení a zápis dat adresáře* nemá oprávnění **není** zahrnují možnost udělat všechny odstranění například odstraňování uživatelů. Pokud chcete poskytnout vaše aplikace umožňuje odstranit uživatele, je potřeba provést tyto další kroky, které se týkají prostředí PowerShell, jinak, můžete přeskočit k další části.

První, pokud ještě nemáte nainstalováno, instalaci [modulu Azure AD PowerShell v1 (MSOnline)](https://docs.microsoft.com/powershell/azure/active-directory/install-msonlinev1?view=azureadps-1.0):

```powershell
Install-Module MSOnline
```

Po instalaci modulu PowerShell se připojit ke klientovi Azure AD B2C.

> [!IMPORTANT]
> Je nutné použít účet správce klienta B2C, který je **místní** klienta B2C. Tyto účty vypadat takto: myusername@myb2ctenant.onmicrosoft.com.

```powershell
Connect-MsolService
```

Nyní použijeme **ID aplikace** v níže přiřadit role správce účtu uživatele, která umožní odstranit uživatele aplikace skriptu. Tyto role mají dobře známé identifikátory, takže všechny musíte udělat není vstupní vaše **ID aplikace** v níže uvedeném skriptu.

```powershell
$applicationId = "<YOUR_APPLICATION_ID>"
$sp = Get-MsolServicePrincipal -AppPrincipalId $applicationId
Add-MsolRoleMember -RoleObjectId fe930be7-5e62-47db-91af-98c3a49a38b1 -RoleMemberObjectId $sp.ObjectId -RoleMemberType servicePrincipal
```

Aplikaci teď má také oprávnění k odstranění uživatelů z vašeho klienta B2C.

## <a name="download-configure-and-build-the-sample-code"></a>Stáhněte si, konfigurace a sestavte ukázkový kód
Nejprve stáhnout ukázkový kód a získat běh aplikace. Potom jsme bude trvat bližší pohled na ho.  Můžete [stáhnout ukázkový kód v souboru ZIP](https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet/archive/master.zip). Můžete ho také klonovat do adresáře podle vašeho výběru:

```cmd
git clone https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet.git
```

Otevřete `B2CGraphClient\B2CGraphClient.sln` řešení sady Visual Studio v sadě Visual Studio. V `B2CGraphClient` projektu, otevřete soubor `App.config`. Nastavení tři aplikace nahraďte vlastními hodnotami:

```xml
<appSettings>
    <add key="b2c:Tenant" value="{Your Tenant Name}" />
    <add key="b2c:ClientId" value="{The ApplicationID from above}" />
    <add key="b2c:ClientSecret" value="{The Key from above}" />
</appSettings>
```

[!INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Pak klikněte pravým tlačítkem na `B2CGraphClient` řešení a znovu sestavit ukázku. Pokud jste úspěšné, teď byste měli mít `B2C.exe` spustitelný soubor umístěný ve `B2CGraphClient\bin\Debug`.

## <a name="build-user-crud-operations-by-using-the-graph-api"></a>Vytvoření operace CRUD uživatele pomocí rozhraní Graph API
Chcete-li použít B2CGraphClient, otevřete `cmd` Windows příkazového řádku a přejděte do adresáře `Debug` adresáře. Spusťte `B2C Help` příkaz.

```cmd
cd B2CGraphClient\bin\Debug
B2C Help
```

Tato akce zobrazí stručný popis každého příkazu. Pokaždé, když vyvolat jeden z těchto příkazů `B2CGraphClient` odešle požadavek do Azure AD Graph API.

### <a name="get-an-access-token"></a>Získání přístupového tokenu
Každá žádost o rozhraní Graph API vyžaduje token přístupu pro ověřování. `B2CGraphClient` pomocí open source Active Directory Authentication Library (ADAL) k získání přístupových tokenů. ADAL usnadňuje získávání tokenu poskytuje jednoduché rozhraní API a postará o některé důležité podrobnosti, jako je například ukládání do mezipaměti přístupových tokenů. Nemáte vám pomůže získat tokeny, když ADAL. Můžete také získat tokeny tím, že vytvoří požadavky HTTP.

> [!NOTE]
> Tento příklad používá ADAL v2 komunikovat s rozhraní Graph API.  Pokud chcete získat přístupové tokeny, které se dají použít s Azure AD Graph API je nutné použít ADAL verze 2 nebo 3.
> 
> 

Když `B2CGraphClient` spustí, vytvoří instanci `B2CGraphClient` třídy. V konstruktoru pro tuto třídu nastaví generování uživatelského rozhraní ověřování pomocí knihovny ADAL:

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

Použijeme `B2C Get-User` příkaz jako příklad. Když `B2C Get-User` je volána bez jakékoli další vstupy volání rozhraní příkazového řádku `B2CGraphClient.GetAllUsers(...)` metoda. Tato metoda volá `B2CGraphClient.SendGraphGetRequest(...)`, který odesílá požadavek HTTP GET pro rozhraní Graph API. Před `B2CGraphClient.SendGraphGetRequest(...)` odešle požadavek GET, nejdřív získá přístup tokenu pomocí ADAL:

```csharp
public async Task<string> SendGraphGetRequest(string api, string query)
{
    // First, use ADAL to acquire a token by using the app's identity (the credential)
    // The first parameter is the resource we want an access_token for; in this case, the Graph API.
    AuthenticationResult result = authContext.AcquireToken("https://graph.windows.net", credential);

    ...

```

Můžete získat přístup tokenu pro rozhraní Graph API voláním knihovnu ADAL `AuthenticationContext.AcquireToken(...)` metoda. Vrátí ADAL `access_token` představující identitu aplikace.

### <a name="read-users"></a>Uživatelé pro čtení
Pokud chcete získat seznam uživatelů, nebo získat určitého uživatele z rozhraní Graph API, můžete odeslat HTTP `GET` žádost o `/users` koncový bod. Žádost o pro všechny uživatele v klienta vypadá takto:

```
GET https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Pokud chcete zobrazit tento požadavek, spusťte příkaz:

 ```cmd
 B2C Get-User
 ```

Všimněte si, dvě důležité věci:

* Přístupový token získaných prostřednictvím ADAL je přidán do `Authorization` záhlaví s použitím `Bearer` schéma.
* U klientů B2C, musíte použít parametr dotazu `api-version=1.6`.

Obě tyto podrobnosti jsou zpracovávány v `B2CGraphClient.SendGraphGetRequest(...)` metoda:

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

### <a name="create-consumer-user-accounts"></a>Vytvoření příjemce uživatelské účty
Při vytváření uživatelských účtů v svého klienta B2C, můžete odeslat HTTP `POST` žádost o `/users` koncový bod:

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

Většina těchto vlastností v této žádosti o nutné vytvořit spotřebitelské uživatele. Chcete-li získat další informace, klikněte na tlačítko [zde](https://msdn.microsoft.com/library/azure/ad/graph/api/users-operations#CreateLocalAccountUser). Všimněte si, že `//` komentáře byly zahrnuty pro obrázek. V skutečné požadavku je nezahrnujte.

Pokud chcete zobrazit žádost, spusťte jeden z následujících příkazů:

```cmd
B2C Create-User ..\..\..\usertemplate-email.json
B2C Create-User ..\..\..\usertemplate-username.json
```

`Create-User` Příkaz má soubor .json jako vstupní parametr. Tato položka obsahuje reprezentaci JSON objektu uživatele. Existují dva ukázkové soubory .json v ukázkovém kódu: `usertemplate-email.json` a `usertemplate-username.json`. Můžete upravit tyto soubory tak, aby vyhovovala vašim potřebám. Kromě výše uvedených povinná pole jsou zahrnuty několik volitelná pole, které můžete použít v těchto souborech. Podrobnosti o volitelná pole lze nalézt v [odkaz na Azure AD Graph API entity](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity).

Můžete vidět, jak je požadavek POST vytvořený v `B2CGraphClient.SendGraphPostRequest(...)`.

* Přiloží přístupového tokenu k `Authorization` hlavičky žádosti.
* Nastaví `api-version=1.6`.
* Obsahuje uživatele objekt JSON v textu požadavku.

> [!NOTE]
> Pokud účty, které chcete provést migraci z existující úložiště uživatele má nižší síly hesla, než [silné heslo sílu vynucováno Azure AD B2C](https://msdn.microsoft.com/library/azure/jj943764.aspx), můžete zakázat pomocí požadavek na silné heslo `DisableStrongPassword` Hodnota v `passwordPolicies` vlastnost. Například můžete upravit požadavek na vytvoření uživatele výše uvedeného následujícím způsobem: `"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"`.
> 
> 

### <a name="update-consumer-user-accounts"></a>Aktualizovat příjemce uživatelské účty
Při aktualizaci uživatelských objektů proces je podobný je ta, kterou použijete k vytvoření uživatelské objekty. Tento proces používá HTTP, ale `PATCH` metoda:

```
PATCH https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 37

{
    "displayName": "Joe Consumer",                // this request updates only the user's displayName
}
```

Zkuste provést aktualizaci uživatele tím, že aktualizuje vaše soubory JSON se nová data. Pak můžete použít `B2CGraphClient` spustit jeden z těchto příkazů:

```cmd
B2C Update-User <user-object-id> ..\..\..\usertemplate-email.json
B2C Update-User <user-object-id> ..\..\..\usertemplate-username.json
```

Zkontrolujte `B2CGraphClient.SendGraphPatchRequest(...)` metoda podrobnosti o tom, jak odeslat tuto žádost.

### <a name="search-users"></a>Vyhledávat uživatele
Můžete hledat uživatele v svého klienta B2C v několika způsoby. Jednoho uživatele pomocí objektu ID nebo dva pomocí uživatele přihlašovací identifikátor (tj, `signInNames` vlastnost).

Spusťte jeden z následujících příkazů k vyhledání konkrétního uživatele:

```cmd
B2C Get-User <user-object-id>
B2C Get-User <filter-query-expression>
```

Tady je několik příkladů:

```cmd
B2C Get-User 2bcf1067-90b6-4253-9991-7f16449c2d91
B2C Get-User $filter=signInNames/any(x:x/value%20eq%20%27joeconsumer@gmail.com%27)
```

### <a name="delete-users"></a>Odstranit uživatele
Proces odstranění uživatele je jednoduchá. Pomocí HTTP `DELETE` metoda a konstrukce adresu URL s správné ID objektu:

```
DELETE https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Pokud chcete zobrazit příklad, zadejte tento příkaz a zobrazit odstranit požadavek, který je vytištěno do konzoly:

```cmd
B2C Delete-User <object-id-of-user>
```

Zkontrolujte `B2CGraphClient.SendGraphDeleteRequest(...)` metoda podrobnosti o tom, jak odeslat tuto žádost.

Můžete provádět mnoho dalších akcí s Azure AD Graph API kromě správy uživatelů. [Referenční dokumentace rozhraní API Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) poskytuje podrobnosti pro každou akci, společně s požadavky na ukázky.

## <a name="use-custom-attributes"></a>Použití vlastních atributů
Většina uživatelských aplikací je nutné uložit nějaký typ informace vlastního uživatelského profilu. Jedním ze způsobů, můžete k tomu je k definování vlastní atribut v svého klienta B2C. Poté můžete ke tento atribut stejným způsobem jako považovat všechny ostatní vlastnosti v objektu user. Můžete aktualizovat atribut, odstranit atribut, dotaz v atributu, odesílat atribut jako deklaraci v tokeny přihlášení a další.

K definování vlastní atribut v svého klienta B2C, najdete v článku [odkaz vlastní atribut B2C](active-directory-b2c-reference-custom-attr.md).

Můžete zobrazit vlastní atributy definované ve vašem klientovi B2C pomocí `B2CGraphClient`:

```cmd
B2C Get-B2C-Application
B2C Get-Extension-Attribute <object-id-in-the-output-of-the-above-command>
```

Výstup z těchto funkcí zobrazí podrobnosti o jednotlivých vlastních atributů, například:

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

Úplný název, můžete použít jako `extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number`, jako vlastnost na uživatelské objekty.  Aktualizujte si soubor .json nové vlastnosti a hodnotu pro vlastnost a potom spusťte:

```cmd
B2C Update-User <object-id-of-user> <path-to-json-file>
```

Pomocí `B2CGraphClient`, máte aplikaci služby, která můžete spravovat vaše uživatele klienta B2C prostřednictvím kódu programu. `B2CGraphClient` vlastní identity aplikace se používá k ověření Azure AD Graph API. Je také získá tokeny pomocí tajný klíč klienta. Protože tato funkce se začlenit do vaší aplikace, mějte na paměti několik klíčových bodů pro B2C aplikace:

* Je třeba udělit příslušná oprávnění v klientovi aplikace.
* Nyní potřebujete získat přístupové tokeny pomocí knihovny ADAL (ne MSAL). (Můžete také odeslat zprávy protokolu přímo, bez použití knihovny.)
* Při volání rozhraní Graph API, použijte `api-version=1.6`.
* Při vytváření a aktualizovat spotřebitelské uživatele, jsou povinné, několik vlastností, jak je popsáno výše.

Pokud máte jakékoli dotazy nebo požadavků pro akce, že kterou chcete provést pomocí rozhraní Graph API na svého klienta B2C, poznámky v tomto článku nebo v úložišti GitHub ukázkový kód soubor problém.

