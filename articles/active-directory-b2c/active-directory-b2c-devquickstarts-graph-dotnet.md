---
title: Použití rozhraní Graph API v Azure Active Directory B2C | Dokumentace Microsoftu
description: Postup volání rozhraní Graph API pro tenanta B2C s využitím identity aplikací k automatizaci procesu.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/07/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: da8aac2968ba020dd2b98253b12e8c9f223966e5
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37442491"
---
# <a name="azure-ad-b2c-use-the-azure-ad-graph-api"></a>Azure AD B2C: Použijte rozhraní Azure AD Graph API

>[!NOTE]
> Je nutné použít [Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-operations-overview?f=255&MSPPError=-2147217396) ke správě uživatelů v adresáři Azure AD B2C. Tím se liší od rozhraní Microsoft Graph API. Další informace najdete [tady](https://blogs.msdn.microsoft.com/aadgraphteam/2016/07/08/microsoft-graph-or-azure-ad-graph/).

Azure Active Directory (Azure AD) B2C tenanti jsou často na velmi velké. To znamená, že mnoho běžných úloh správy klientů je třeba provést prostřednictvím kódu programu. Primární příklad je Správa uživatelů. Můžete potřebovat k migraci existujícího úložiště uživatele do tenanta B2C. Můžete hostovat registrace uživatele na vlastní stránce a vytvořte uživatelské účty ve vašem adresáři Azure AD B2C na pozadí. Tyto druhy úkonů vyžadují možnost vytvářet, číst, aktualizovat a odstraňovat uživatelské účty. Můžete provádět tyto úlohy s využitím Azure AD Graph API.

Tenanti B2C existují dva primární režimy komunikaci s rozhraním Graph API.

* Pro interaktivní, jedno spuštění úlohy by měl fungovat jako účet správce v tenantovi B2C při provádění úlohy. Tento režim vyžaduje správce přihlásit pomocí přihlašovacích údajů, aby mohli provádět jakékoli volání rozhraní Graph API.
* Pro automatizované, nepřetržité úlohy měli byste použít nějaký typ účtu služby poskytnout potřebná oprávnění k provádění úloh správy. Ve službě Azure AD můžete to provedete tak, že registrace aplikace a ověřování do služby Azure AD. To se provádí pomocí **ID aplikace** , která používá [udělení přihlašovacích údajů klienta OAuth 2.0](../active-directory/develop/active-directory-authentication-scenarios.md#daemon-or-server-application-to-web-api). V tomto případě aplikace slouží jako samostatně, nikoli jako uživatel, pro volání rozhraní Graph API.

V tomto článku se dozvíte, jak k provádění případ automatické použití. Vytvoříte .NET 4.5 `B2CGraphClient` , který provede uživatele vytvoření, čtení, aktualizace a odstranění (CRUD) operací. Klient bude mít Windows rozhraní příkazového řádku (CLI), který umožňuje volat různé metody. Kód se však zapíše neinteraktivní, automatizované způsobem chovat.

## <a name="get-an-azure-ad-b2c-tenant"></a>Získání tenanta služby Azure AD B2C
Než budete moct vytvořit aplikacím nebo uživatelům, budete potřebovat tenanta služby Azure AD B2C. Pokud nemáte tenanta už, [Začínáme s Azure AD B2C](active-directory-b2c-get-started.md).

## <a name="register-your-application-in-your-tenant"></a>Registrace vaší aplikace ve vašem tenantovi
Jakmile budete mít tenanta B2C, budete muset zaregistrovat aplikaci pomocí [webu Azure portal](https://portal.azure.com).

> [!IMPORTANT]
> K používání rozhraní Graph API s tenanta B2C, budete muset zaregistrovat aplikace s využitím *registrace aplikací* služby na webu Azure Portal, **není** Azure AD B2C *aplikace*nabídky. Postupujte podle následujících pokynů vás přesměruje na příslušná nabídku. Nelze znovu použít existující aplikace B2C, které jste zaregistrovali v Azure AD B2C *aplikací* nabídky.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Výběrem účtu v pravém horním rohu stránky zvolte tenanta Azure AD B2C.
3. V levém navigačním podokně zvolte **všechny služby**, klikněte na tlačítko **registrace aplikací**a klikněte na tlačítko **přidat**.
4. Postupujte podle zobrazených výzev a vytvořte novou aplikaci. 
    1. Vyberte **webová aplikace / rozhraní API** jako typ aplikace.    
    2. Zadejte **libovolnou adresu URL přihlašování** (třeba https://B2CGraphAPI) není relevantní pro účely tohoto příkladu.  
5. Budou aplikace nyní zobrazí v seznamu aplikací, klikněte na něj získat **ID aplikace** (také označované jako ID klienta). Zkopírujte jej, protože ho budete potřebovat v další části.
6. V nabídce nastavení, klikněte na tlačítko **klíče**.
7. V **hesla** části, zadejte popis klíče a vyberte dobu trvání a potom klikněte na tlačítko **Uložit**. Zkopírujte hodnotu klíče (označované také jako tajný kód klienta) pro použití v další části.

## <a name="configure-create-read-and-update-permissions-for-your-application"></a>Konfigurace vytvářet, číst a aktualizovat oprávnění pro vaši aplikaci
Nyní je třeba konfigurace aplikace pro všechna požadovaná oprávnění ke vytvářet, číst, aktualizovat a odstraňovat users.

1. Pokud budete pokračovat v nabídce registrace aplikací webu Azure portal, vyberte svou aplikaci.
2. V nabídce nastavení, klikněte na **požadovaná oprávnění**.
3. V nabídce požadovaná oprávnění klikněte na **Windows Azure Active Directory**.
4. V nabídce povolit přístup, vyberte **pro čtení a zápis dat adresáře** svolením **oprávnění aplikace** a klikněte na tlačítko **Uložit**.
5. Nakonec zpět v nabídce požadovaná oprávnění klikněte na **udělit oprávnění** tlačítko.

Teď máte aplikaci, který má oprávnění vytvářet, číst a aktualizovat uživatele z vašeho tenanta B2C.

> [!NOTE]
> Udělení oprávnění provést trvat několik minut na úplné zpracování.
> 
> 

## <a name="configure-delete-permissions-for-your-application"></a>Konfigurace oprávnění pro vaši aplikaci
V současné době *pro čtení a zápis dat adresáře* nemá oprávnění **není** patří schopnost provádět všechny odstraněné položky, jako je počet uživatelů se odstraňuje. Pokud chcete aplikaci umožnit odstranit uživatele, budete muset provést tyto dodatečné kroky, které se týkají prostředí PowerShell, jinak můžete přeskočit k další části.

Nejprve, pokud ještě nemáte nainstalovaný, nainstalujte [modulu Azure AD PowerShell v1 (MSOnline)](https://docs.microsoft.com/powershell/azure/active-directory/install-msonlinev1?view=azureadps-1.0):

```powershell
Install-Module MSOnline
```

Po instalaci modulu prostředí PowerShell připojení k vašemu tenantovi Azure AD B2C.

> [!IMPORTANT]
> Je třeba použít účet správce tenanta B2C, který je **místní** do tenanta B2C. Tyto účty vypadat nějak takto: myusername@myb2ctenant.onmicrosoft.com.

```powershell
Connect-MsolService
```

Nyní použijeme **ID aplikace** ve níže přiřazení role správce účtu uživatele, který vám umožní odstranit uživatele aplikace skriptu. Tyto role mají dobře známé identifikátory, takže všechno, co potřebujete udělat po zadání vaší **ID aplikace** v níže uvedeném skriptu.

```powershell
$applicationId = "<YOUR_APPLICATION_ID>"
$sp = Get-MsolServicePrincipal -AppPrincipalId $applicationId
Add-MsolRoleMember -RoleObjectId fe930be7-5e62-47db-91af-98c3a49a38b1 -RoleMemberObjectId $sp.ObjectId -RoleMemberType servicePrincipal
```

Aplikace teď má také oprávnění k odstranění uživatele z vašeho tenanta B2C.

## <a name="download-configure-and-build-the-sample-code"></a>Stáhněte si, konfigurace a sestavte ukázkový kód
Nejprve stáhnout ukázkový kód a získat jeho spuštění. Potom my podnikneme na ně podívat.  Je možné [stažení ukázkového kódu ve formě souboru .zip](https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet/archive/master.zip). Také ho můžete klonovat do adresáře podle vašeho výběru:

```cmd
git clone https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet.git
```

Otevřít `B2CGraphClient\B2CGraphClient.sln` řešení sady Visual Studio v sadě Visual Studio. V `B2CGraphClient` projektu, otevřete soubor `App.config`. Nastavení aplikace tři nahraďte vlastními hodnotami:

```xml
<appSettings>
    <add key="b2c:Tenant" value="{Your Tenant Name}" />
    <add key="b2c:ClientId" value="{The ApplicationID from above}" />
    <add key="b2c:ClientSecret" value="{The Key from above}" />
</appSettings>
```

[!INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Pak klikněte pravým tlačítkem na `B2CGraphClient` řešení a znovu sestavte ukázku. Pokud budete úspěšní, teď byste měli mít `B2C.exe` spustitelný soubor umístěn v `B2CGraphClient\bin\Debug`.

## <a name="build-user-crud-operations-by-using-the-graph-api"></a>Uživatel CRUD operací sestavení s využitím rozhraní Graph API
Chcete-li použít B2CGraphClient, otevřete `cmd` Windows příkazového řádku a přejděte do adresáře `Debug` adresáře. Spusťte `B2C Help` příkazu.

```cmd
cd B2CGraphClient\bin\Debug
B2C Help
```

Tato akce zobrazí stručný popis jednotlivých příkazů. Pokaždé, když některý z těchto příkazů vyvoláte `B2CGraphClient` odešle požadavek do Azure AD Graph API.

### <a name="get-an-access-token"></a>Získání přístupového tokenu
Jakoukoli žádost na rozhraní Graph API vyžaduje přístupového tokenu pro ověřování. `B2CGraphClient` pomocí open-source Active Directory Authentication Library (ADAL) k získání přístupových tokenů. ADAL usnadňuje získání tokenu služby poskytují jednoduché rozhraní API a přitom se bezpečně postará o některé důležité podrobnosti, jako je například ukládání přístupových tokenů. Není nutné k získání tokenů, i když používají knihovnu ADAL. Můžete také získat tokeny vytváření požadavků HTTP.

> [!NOTE]
> Tento vzorový kód používá ADAL v2, chcete-li komunikovat s rozhraním Graph API.  Pokud chcete získat přístupové tokeny, které je možné použít s Azure AD Graph API je nutné použít ADAL v2 nebo v3.
> 
> 

Když `B2CGraphClient` spustí, vytvoří instanci `B2CGraphClient` třídy. Konstruktor pro tuto třídu nastaví generování uživatelského rozhraní ověřování pomocí knihovny ADAL:

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

Použijeme `B2C Get-User` příkaz jako příklad. Když `B2C Get-User` je vyvolán bez jakékoli další vstupy volání rozhraní příkazového řádku `B2CGraphClient.GetAllUsers(...)` metody. Tato metoda volá `B2CGraphClient.SendGraphGetRequest(...)`, který odešle požadavek HTTP GET pro rozhraní Graph API. Před `B2CGraphClient.SendGraphGetRequest(...)` odešle požadavek GET, nejprve obdrží přístupového tokenu pomocí ADAL:

```csharp
public async Task<string> SendGraphGetRequest(string api, string query)
{
    // First, use ADAL to acquire a token by using the app's identity (the credential)
    // The first parameter is the resource we want an access_token for; in this case, the Graph API.
    AuthenticationResult result = authContext.AcquireToken("https://graph.windows.net", credential);

    ...

```

Vám může získání přístupového tokenu pro rozhraní Graph API pomocí volání ADAL `AuthenticationContext.AcquireToken(...)` metody. Vrátí ADAL `access_token` , který reprezentuje identitu aplikace.

### <a name="read-users"></a>Přečíst uživatele
Pokud chcete získat seznam uživatelů nebo získání konkrétního uživatele z rozhraní Graph API, můžete odeslat HTTP `GET` žádat o `/users` koncového bodu. Žádost o pro všechny uživatele v tenantovi vypadá takto:

```
GET https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Pokud chcete zobrazit tento požadavek, spusťte:

 ```cmd
 B2C Get-User
 ```

Všimněte si dvě důležité věci:

* Přístupový token opatřené ADAL se přidá do `Authorization` záhlaví `Bearer` schéma.
* U klientů B2C, musíte použít parametr dotazu `api-version=1.6`.

Obě tyto podrobnosti jsou zpracovány v `B2CGraphClient.SendGraphGetRequest(...)` metody:

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

### <a name="create-consumer-user-accounts"></a>Vytvořte uživatelské účty uživatelů
Při vytváření uživatelských účtů ve vašem tenantovi B2C, můžete odeslat HTTP `POST` žádat o `/users` koncový bod:

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

Většina těchto vlastností v této žádosti je potřeba vytvořit spotřebitelské uživatele. Další informace, klikněte na tlačítko [tady](https://msdn.microsoft.com/library/azure/ad/graph/api/users-operations#CreateLocalAccountUser). Všimněte si, že `//` komentáře k dipozici ukázky. Nezahrnujte je do aktuálního požadavku.

Chcete-li zobrazit žádost, spusťte jeden z následujících příkazů:

```cmd
B2C Create-User ..\..\..\usertemplate-email.json
B2C Create-User ..\..\..\usertemplate-username.json
```

`Create-User` Příkaz má soubor .json, který jako vstupní parametr. Tato položka obsahuje reprezentaci JSON objektu user. Existují dva ukázkové soubory .json ve vzorovém kódu: `usertemplate-email.json` a `usertemplate-username.json`. Můžete upravit tyto soubory tak, aby odpovídala vašim potřebám. Kromě výše uvedených požadovaných polí jsou zahrnuty několik volitelných polí, které můžete použít v těchto souborech. Podrobnosti o volitelná pole najdete v [odkaz na entitu Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity).

Můžete vidět, jak je požadavek POST vytvořený v `B2CGraphClient.SendGraphPostRequest(...)`.

* Připojí přístupový token pro `Authorization` záhlaví požadavku.
* Nastaví `api-version=1.6`.
* Obsahuje objekt uživatele ve formátu JSON v textu požadavku.

> [!NOTE]
> Pokud má nižší síly hesla, než jsou účty, které chcete provést migraci z existující úložiště uživatele [sílu silné heslo, které vynucuje Azure AD B2C](https://msdn.microsoft.com/library/azure/jj943764.aspx), požadavek na silné heslo pomocí můžete zakázat `DisableStrongPassword` Hodnota v `passwordPolicies` vlastnost. Například můžete upravit požadavek na vytvoření uživatele jste zadali výše pro následujícím způsobem: `"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"`.
> 
> 

### <a name="update-consumer-user-accounts"></a>Aktualizace příjemců uživatelské účty
Při aktualizaci uživatelských objektů proces je podobný ten, který použijete k vytvoření uživatelské objekty. Tento proces používá HTTP, ale `PATCH` metody:

```
PATCH https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 37

{
    "displayName": "Joe Consumer",                // this request updates only the user's displayName
}
```

Zkuste provést aktualizaci uživatele prostřednictvím aktualizace souborů JSON s novými daty. Pak můžete použít `B2CGraphClient` na některou z těchto příkazů:

```cmd
B2C Update-User <user-object-id> ..\..\..\usertemplate-email.json
B2C Update-User <user-object-id> ..\..\..\usertemplate-username.json
```

Zkontrolujte `B2CGraphClient.SendGraphPatchRequest(...)` metoda podrobnosti o tom, jak odeslat tuto žádost.

### <a name="search-users"></a>Vyhledávat uživatele
Můžete hledat uživatele ve vašem tenantovi B2C v několika způsoby. Jednoho uživatele pomocí objektu ID nebo dvěma pomocí uživatele přihlašovací identifikátor (to znamená, `signInNames` vlastnost).

Spusťte následující příkazy k vyhledání konkrétního uživatele:

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
Proces odstranění uživatele je jednoduché. Používat HTTP `DELETE` metody a konstruktoru adresu URL se správnými ID objektu:

```
DELETE https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

K prohlédnutí příkladu, zadejte tento příkaz a zobrazení žádosti o odstranění tisk do konzoly:

```cmd
B2C Delete-User <object-id-of-user>
```

Zkontrolujte `B2CGraphClient.SendGraphDeleteRequest(...)` metoda podrobnosti o tom, jak odeslat tuto žádost.

Můžete provádět mnoho dalších akcí s Azure AD Graph API kromě správy uživatelů. [Referenční informace k Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) poskytuje podrobnosti pro každou akci, společně s požadavky na ukázky.

## <a name="use-custom-attributes"></a>Použití vlastních atributů
Většina uživatelů aplikací je nutné uložit nějaký typ informace o vlastních uživatelském profilu. To lze provést jedním ze způsobů je definování vlastního atributu ve vašem tenantovi B2C. Tento atribut lze považovat pak stejným způsobem zpracovávat jiné vlastnosti v objektu user. Můžete aktualizovat atribut, odstraňte atribut, dotazovat pomocí atributu, atribut odeslat jako deklarace identity v tokeny přihlášení a další.

Definování vlastního atributu ve vašem tenantovi B2C, najdete v článku [odkaz vlastního atributu B2C](active-directory-b2c-reference-custom-attr.md).

Můžete zobrazit vlastní atributy definované ve vašem tenantovi B2C s využitím `B2CGraphClient`:

```cmd
B2C Get-B2C-Application
B2C Get-Extension-Attribute <object-id-in-the-output-of-the-above-command>
```

Výstup z těchto funkcí zobrazí podrobnosti o jednotlivých vlastních atributů, jako například:

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

Úplný název, můžete použít jako `extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number`, jako vlastnost pro uživatelské objekty.  Aktualizujte si soubor .json nové vlastnosti a hodnotu pro vlastnost a potom spusťte:

```cmd
B2C Update-User <object-id-of-user> <path-to-json-file>
```

S použitím `B2CGraphClient`, budete mít aplikaci služby, která může spravovat vaši uživatelé tenanta B2C prostřednictvím kódu programu. `B2CGraphClient` vlastní identity aplikace používá k ověření Azure AD Graph API. Získá také tokeny pomocí tajný kód klienta. Jak tuto funkci můžete zahrnout do vaší aplikace, mějte na paměti několik klíčových bodů pro aplikace B2C:

* Budete muset bude aplikacím udělena příslušná oprávnění v tenantovi.
* Teď budete muset použít k získání přístupových tokenů ADAL (ne MSAL). (Můžete také odesílat zprávy protokolu přímo, bez použití knihovny.)
* Při volání rozhraní Graph API použít `api-version=1.6`.
* Při vytváření a aktualizaci spotřebitelské uživatele několik vlastností jsou povinné, jak je popsáno výše.

Pokud máte jakékoli dotazy nebo požadavky pro akce, že byste chtěli provést pomocí rozhraní Graph API ve svém tenantovi B2C, napište komentář v tomto článku nebo založte problém v kódu ukázkového úložiště GitHub.

