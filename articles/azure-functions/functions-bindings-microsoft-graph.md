---
title: Vazby Microsoft Graphu pro funkce Azure
description: Zjistěte, jak používat aktivační události a vazby Microsoft Graphu ve funkcích Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 12/20/2017
ms.author: cshoe
ms.openlocfilehash: 770187693e5bac6e059dfd20455099fcc695b74b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76715030"
---
# <a name="microsoft-graph-bindings-for-azure-functions"></a>Vazby Microsoft Graphu pro funkce Azure

Tento článek vysvětluje, jak nakonfigurovat a pracovat s aktivační mise A Vazby Microsoft Graphu v Azure Functions. Pomocí těchto funkcí můžete pomocí funkcí Azure pracovat s daty, přehledy a událostmi z [Microsoft Graphu](https://developer.microsoft.com/graph).

Rozšíření Microsoft Graph poskytuje následující vazby:
- [Auth token vstupní vazby](#token-input) umožňuje interakci s libovolným rozhraním API Microsoft Graph.
- [Vstupní vazba tabulky aplikace Excel](#excel-input) umožňuje číst data z aplikace Excel.
- [Výstupní vazba tabulky aplikace Excel](#excel-output) umožňuje měnit data aplikace Excel.
- [Vstupní vazba souboru OneDrive](#onedrive-input) umožňuje číst soubory z OneDrivu.
- [Výstupní vazba souboru OneDrivu](#onedrive-output) umožňuje zapisovat do souborů na OneDrivu.
- [Vazby výstupu zprávy aplikace Outlook](#outlook-output) umožňuje odesílat e-maily prostřednictvím aplikace Outlook.
- Kolekce [microsoft graph webhooku aktivační události a vazby](#webhooks) umožňuje reagovat na události z Microsoft Graphu.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!Note]
> Vazby Microsoft Graphu jsou aktuálně ve verzi Preview pro Funkce Azure verze 2.x a vyšší. Nejsou podporovány ve funkcích verze 1.x.

## <a name="packages"></a>Balíčky

Vstupní vazba tokenu auth je k dispozici v balíčku [Microsoft.Azure.WebJobs.Extensions.AuthTokens](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.AuthTokens/) NuGet. Ostatní vazby aplikace Microsoft Graph jsou k dispozici v balíčku [Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph.](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph/) Zdrojový kód pro balíčky je v [úložišti GitHub azure-functions-microsoftgraph-extension.](https://github.com/Azure/azure-functions-microsoftgraph-extension/)

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="setting-up-the-extensions"></a>Nastavení rozšíření

Vazby aplikace Microsoft Graph jsou k dispozici prostřednictvím _rozšíření vazby_. Rozšíření vazby jsou volitelné součásti runtime Azure Functions. Tato část ukazuje, jak nastavit Microsoft Graph a rozšíření tokenu ověřování.

### <a name="enabling-functions-20-preview"></a>Povolení náhledu funkcí 2.0

Rozšíření vazby jsou k dispozici pouze pro Azure Functions 2.0 preview. 

Informace o tom, jak nastavit aplikaci funkce pro použití verze preview 2.0 funkce runtime, najdete v [tématu Jak cílit na verze runtime Funkce Azure](set-runtime-version.md).

### <a name="installing-the-extension"></a>Instalace rozšíření

Pokud chcete nainstalovat rozšíření z webu Azure Portal, přejděte na šablonu nebo vazbu, která na něj odkazuje. Vytvořte novou funkci a na obrazovce výběru šablony zvolte scénář "Microsoft Graph". Vyberte jednu ze šablon z tohoto scénáře. Případně můžete přejít na kartu "Integrovat" existující funkce a vyberte jednu z vazeb uvedených v tomto článku.

V obou případech se zobrazí upozornění, které určuje rozšíření, které má být nainstalováno. Chcete-li získat rozšíření, klepněte na tlačítko **Nainstalovat.** Každé rozšíření je třeba nainstalovat pouze jednou za funkci aplikace. 

> [!Note] 
> Proces instalace na portálu může trvat až 10 minut u plánu spotřeby.

Pokud používáte Visual Studio, můžete získat rozšíření instalací [nuget balíčky, které jsou uvedeny výše v tomto článku](#packages).

### <a name="configuring-authentication--authorization"></a>Konfigurace ověřování / autorizace

Vazby popsané v tomto článku vyžadují identitu, která má být použita. To umožňuje Microsoft Graph vynutit oprávnění a audit interakce. Identita může být uživatel, který přistupuje k vaší aplikaci nebo k samotné aplikaci. Chcete-li tuto identitu nakonfigurovat, nastavte [ověřování / autorizaci služby App Service](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization) pomocí služby Azure Active Directory. Budete také muset požádat o oprávnění k prostředkům, které vaše funkce vyžadují.

> [!Note] 
> Rozšíření Microsoft Graph podporuje jenom ověřování Azure AD. Uživatelé se musí přihlásit pomocí pracovního nebo školního účtu.

Pokud používáte portál Azure, zobrazí se upozornění pod výzvou k instalaci rozšíření. Toto upozornění vás vyzve ke konfiguraci ověřování / autorizace služby App Service a vyžádání všech oprávnění, která šablona nebo vazba vyžaduje. Klikněte na **Konfigurovat Azure AD teď** nebo Přidat oprávnění **nyní** podle potřeby.



<a name="token-input"></a>
## <a name="auth-token"></a>Auth token

Auth token vstupní vazby získá token Azure AD pro daný prostředek a poskytuje jej do kódu jako řetězec. Prostředek může být libovolný, pro který má aplikace oprávnění. 

Tato část obsahuje následující pododdíly:

* [Příklad](#auth-token---example)
* [Atributy](#auth-token---attributes)
* [Konfigurace](#auth-token---configuration)
* [Použití](#auth-token---usage)

### <a name="auth-token---example"></a>Auth token - příklad

Podívejte se na příklad pro konkrétní jazyk:

* [Skript jazyka C# (.csx)](#auth-token---c-script-example)
* [Javascript](#auth-token---javascript-example)

#### <a name="auth-token---c-script-example"></a>Auth token - příklad skriptu Jazyka C#

Následující příklad získá informace o profilu uživatele.

Soubor *function.json* definuje aktivační událost HTTP se vstupní vazbou tokenu:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "token",
      "direction": "in",
      "name": "graphToken",
      "resource": "https://graph.microsoft.com",
      "identity": "userFromRequest"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Kód skriptu Jazyka C# používá token k volání HTTP do aplikace Microsoft Graph a vrátí výsledek:

```csharp
using System.Net; 
using System.Net.Http; 
using System.Net.Http.Headers;
using Microsoft.Extensions.Logging; 

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, string graphToken, ILogger log)
{
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", graphToken);
    return await client.GetAsync("https://graph.microsoft.com/v1.0/me/");
}
```

#### <a name="auth-token---javascript-example"></a>Auth token - příklad JavaScriptu

Následující příklad získá informace o profilu uživatele.

Soubor *function.json* definuje aktivační událost HTTP se vstupní vazbou tokenu:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "token",
      "direction": "in",
      "name": "graphToken",
      "resource": "https://graph.microsoft.com",
      "identity": "userFromRequest"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Kód Jazyka JavaScript používá token k volání HTTP do aplikace Microsoft Graph a vrátí výsledek.

```js
const rp = require('request-promise');

module.exports = function (context, req) {
    let token = "Bearer " + context.bindings.graphToken;

    let options = {
        uri: 'https://graph.microsoft.com/v1.0/me/',
        headers: {
            'Authorization': token
        }
    };
    
    rp(options)
        .then(function(profile) {
            context.res = {
                body: profile
            };
            context.done();
        })
        .catch(function(err) {
            context.res = {
                status: 500,
                body: err
            };
            context.done();
        });
};
```

### <a name="auth-token---attributes"></a>Auth token - atributy

V [knihovnách tříd Jazyka C#](functions-dotnet-class-library.md)použijte atribut [Token.](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/TokenBinding/TokenAttribute.cs)

### <a name="auth-token---configuration"></a>Auth token - konfigurace

Následující tabulka vysvětluje vlastnosti konfigurace vazby, které jste nastavili `Token` v souboru *function.json* a atributu.

|vlastnost function.json | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**Jméno**| neuvedeno |Povinné - název proměnné použitý v kódu funkce pro token auth. Viz [Použití vstupní vazby tokenu auth z kódu](#token-input-code).|
|**Typ**| neuvedeno |Povinné - musí `token`být nastavena na .|
|**direction**| neuvedeno |Povinné - musí `in`být nastavena na .|
|**Identity**|**Identita**|Povinné - Identita, která bude použita k provedení akce. Může se na nich vyvěšovat jedna z následujících hodnot:<ul><li><code>userFromRequest</code>- Platí pouze s [aktivační událostí HTTP]. Používá identitu volajícího uživatele.</li><li><code>userFromId</code>- Používá identitu dříve přihlášeného uživatele se zadaným ID. Podívejte <code>userId</code> se na nemovitost.</li><li><code>userFromToken</code>- Používá identitu reprezentovanou zadaným tokenem. Podívejte <code>userToken</code> se na nemovitost.</li><li><code>clientCredentials</code>- Používá identitu aplikace funkce.</li></ul>|
|**Userid**|**Userid**  |Potřebné pouze v případě, že `userFromId` _je identita_ nastavena na . ID objektu uživatele přidružené k dříve přihlášenému uživateli.|
|**Usertoken**|**Usertoken**|Potřebné pouze v případě, že `userFromToken` _je identita_ nastavena na . Token platný pro aplikaci funkce. |
|**Zdrojů**|**Zdrojů**|Povinné – adresa URL prostředku Azure AD, pro kterou je požadován token.|

<a name="token-input-code"></a>
### <a name="auth-token---usage"></a>Auth token - využití

Samotná vazba nevyžaduje žádná oprávnění Azure AD, ale v závislosti na tom, jak se používá token, budete muset požádat o další oprávnění. Zkontrolujte požadavky prostředku, který chcete získat přístup pomocí tokenu.

Token je vždy prezentován kódu jako řetězec.

> [!Note]
> Při vývoji místně `userFromId`s `userFromToken` `userFromRequest` buď , nebo možnosti, požadovaný `X-MS-TOKEN-AAD-ID-TOKEN` token lze získat [ručně](https://github.com/Azure/azure-functions-microsoftgraph-extension/issues/54#issuecomment-392865857) a zadejte v hlavičce požadavku z volající klientské aplikace.


<a name="excel-input"></a>
## <a name="excel-input"></a>Vstup aplikace Excel

Vstupní vazba excelové tabulky přečte obsah excelové tabulky uložené na OneDrivu.

Tato část obsahuje následující pododdíly:

* [Příklad](#excel-input---example)
* [Atributy](#excel-input---attributes)
* [Konfigurace](#excel-input---configuration)
* [Použití](#excel-input---usage)

### <a name="excel-input---example"></a>Vstup aplikace Excel – příklad

Podívejte se na příklad pro konkrétní jazyk:

* [Skript jazyka C# (.csx)](#excel-input---c-script-example)
* [Javascript](#excel-input---javascript-example)

#### <a name="excel-input---c-script-example"></a>Příklad skriptu aplikace Excel – skript jazyka C#

Následující soubor *function.json* definuje aktivační událost HTTP se vstupní vazbou aplikace Excel:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "excel",
      "direction": "in",
      "name": "excelTableData",
      "path": "{query.workbook}",
      "identity": "UserFromRequest",
      "tableName": "{query.table}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Následující kód skriptu Jazyka C# přečte obsah zadané tabulky a vrátí je uživateli:

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;
using Microsoft.Extensions.Logging;

public static IActionResult Run(HttpRequest req, string[][] excelTableData, ILogger log)
{
    return new OkObjectResult(excelTableData);
}
```

#### <a name="excel-input---javascript-example"></a>Vstup aplikace Excel – příklad JavaScriptu

Následující soubor *function.json* definuje aktivační událost HTTP se vstupní vazbou aplikace Excel:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "excel",
      "direction": "in",
      "name": "excelTableData",
      "path": "{query.workbook}",
      "identity": "UserFromRequest",
      "tableName": "{query.table}"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Následující kód JavaScriptu přečte obsah zadané tabulky a vrátí je uživateli.

```js
module.exports = function (context, req) {
    context.res = {
        body: context.bindings.excelTableData
    };
    context.done();
};
```

### <a name="excel-input---attributes"></a>Vstup aplikace Excel - atributy

V [knihovnách tříd Jazyka C#](functions-dotnet-class-library.md)použijte atribut [Excel.](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/ExcelAttribute.cs)

### <a name="excel-input---configuration"></a>Vstup aplikace Excel – konfigurace

Následující tabulka vysvětluje vlastnosti konfigurace vazby, které jste nastavili `Excel` v souboru *function.json* a atributu.

|vlastnost function.json | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**Jméno**| neuvedeno |Povinné - název proměnné použitý v kódu funkce pro tabulku aplikace Excel. Viz [Použití vstupní vazby tabulky aplikace Excel z kódu](#excel-input-code).|
|**Typ**| neuvedeno |Povinné - musí `excel`být nastavena na .|
|**direction**| neuvedeno |Povinné - musí `in`být nastavena na .|
|**Identity**|**Identita**|Povinné - Identita, která bude použita k provedení akce. Může se na nich vyvěšovat jedna z následujících hodnot:<ul><li><code>userFromRequest</code>- Platí pouze s [aktivační událostí HTTP]. Používá identitu volajícího uživatele.</li><li><code>userFromId</code>- Používá identitu dříve přihlášeného uživatele se zadaným ID. Podívejte <code>userId</code> se na nemovitost.</li><li><code>userFromToken</code>- Používá identitu reprezentovanou zadaným tokenem. Podívejte <code>userToken</code> se na nemovitost.</li><li><code>clientCredentials</code>- Používá identitu aplikace funkce.</li></ul>|
|**Userid**|**Userid**  |Potřebné pouze v případě, že `userFromId` _je identita_ nastavena na . ID objektu uživatele přidružené k dříve přihlášenému uživateli.|
|**Usertoken**|**Usertoken**|Potřebné pouze v případě, že `userFromToken` _je identita_ nastavena na . Token platný pro aplikaci funkce. |
|**Cestu**|**Cesta**|Povinné – cesta na OneDrivu k excelovému sešitu|
|**název_listu**|**Název listu**|List, ve kterém je tabulka nalezena.|
|**Tablename**|**TableName**|Název tabulky. Pokud není zadán, bude použit obsah listu.|

<a name="excel-input-code"></a>
### <a name="excel-input---usage"></a>Vstup aplikace Excel – využití

Tato vazba vyžaduje následující oprávnění Azure AD:

|Prostředek|Oprávnění|
|--------|--------|
|Microsoft Graph|Čtení uživatelských souborů|

Vazba zveřejňuje následující typy .NET funkce:
- řetězec[][]
- Microsoft.Graph.WorkbookTable
- Vlastní typy objektů (pomocí vazby konstrukčního modelu)










<a name="excel-output"></a>
## <a name="excel-output"></a>Výstup aplikace Excel

Výstupní vazba Excelu upravuje obsah excelové tabulky uložené na OneDrivu.

Tato část obsahuje následující pododdíly:

* [Příklad](#excel-output---example)
* [Atributy](#excel-output---attributes)
* [Konfigurace](#excel-output---configuration)
* [Použití](#excel-output---usage)

### <a name="excel-output---example"></a>Výstup aplikace Excel – příklad

Podívejte se na příklad pro konkrétní jazyk:

* [Skript jazyka C# (.csx)](#excel-output---c-script-example)
* [Javascript](#excel-output---javascript-example)

#### <a name="excel-output---c-script-example"></a>Excel ový výstup – příklad skriptu Jazyka C#

Následující příklad přidá řádky do excelové tabulky.

Soubor *function.json* definuje aktivační událost HTTP s výstupní vazbou aplikace Excel:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "newExcelRow",
      "type": "excel",
      "direction": "out",
      "identity": "userFromRequest",
      "updateType": "append",
      "path": "{query.workbook}",
      "tableName": "{query.table}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Kód skriptu Jazyka C# přidá do tabulky nový řádek (předpokládá se, že je jednosloupcový) na základě vstupu z řetězce dotazu:

```csharp
using System.Net;
using System.Text;
using Microsoft.Extensions.Logging;

public static async Task Run(HttpRequest req, IAsyncCollector<object> newExcelRow, ILogger log)
{
    string input = req.Query
        .FirstOrDefault(q => string.Compare(q.Key, "text", true) == 0)
        .Value;
    await newExcelRow.AddAsync(new {
        Text = input
        // Add other properties for additional columns here
    });
    return;
}
```

#### <a name="excel-output---javascript-example"></a>Výstup aplikace Excel – příklad JavaScriptu

Následující příklad přidá řádky do excelové tabulky.

Soubor *function.json* definuje aktivační událost HTTP s výstupní vazbou aplikace Excel:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "newExcelRow",
      "type": "excel",
      "direction": "out",
      "identity": "userFromRequest",
      "updateType": "append",
      "path": "{query.workbook}",
      "tableName": "{query.table}"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Následující kód Jazyka JavaScript přidá do tabulky nový řádek (předpokládá se, že je jednosloupcový) na základě vstupu z řetězce dotazu.

```js
module.exports = function (context, req) {
    context.bindings.newExcelRow = {
        text: req.query.text
        // Add other properties for additional columns here
    }
    context.done();
};
```

### <a name="excel-output---attributes"></a>Výstup aplikace Excel - atributy

V [knihovnách tříd Jazyka C#](functions-dotnet-class-library.md)použijte atribut [Excel.](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/ExcelAttribute.cs)

### <a name="excel-output---configuration"></a>Výstup aplikace Excel - konfigurace

Následující tabulka vysvětluje vlastnosti konfigurace vazby, které jste nastavili `Excel` v souboru *function.json* a atributu.

|vlastnost function.json | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**Jméno**| neuvedeno |Povinné - název proměnné použitý v kódu funkce pro token auth. Viz [Použití výstupní vazby tabulky aplikace Excel z kódu](#excel-output-code).|
|**Typ**| neuvedeno |Povinné - musí `excel`být nastavena na .|
|**direction**| neuvedeno |Povinné - musí `out`být nastavena na .|
|**Identity**|**Identita**|Povinné - Identita, která bude použita k provedení akce. Může se na nich vyvěšovat jedna z následujících hodnot:<ul><li><code>userFromRequest</code>- Platí pouze s [aktivační událostí HTTP]. Používá identitu volajícího uživatele.</li><li><code>userFromId</code>- Používá identitu dříve přihlášeného uživatele se zadaným ID. Podívejte <code>userId</code> se na nemovitost.</li><li><code>userFromToken</code>- Používá identitu reprezentovanou zadaným tokenem. Podívejte <code>userToken</code> se na nemovitost.</li><li><code>clientCredentials</code>- Používá identitu aplikace funkce.</li></ul>|
|**Userid** |**Userid** |Potřebné pouze v případě, že `userFromId` _je identita_ nastavena na . ID objektu uživatele přidružené k dříve přihlášenému uživateli.|
|**Usertoken**|**Usertoken**|Potřebné pouze v případě, že `userFromToken` _je identita_ nastavena na . Token platný pro aplikaci funkce. |
|**Cestu**|**Cesta**|Povinné – cesta na OneDrivu k excelovému sešitu|
|**název_listu**|**Název listu**|List, ve kterém je tabulka nalezena.|
|**Tablename**|**TableName**|Název tabulky. Pokud není zadán, bude použit obsah listu.|
|**updateType**|**Typ aktualizace**|Povinné - Typ změny, která má být v tabulce. Může se na nich vyvěšovat jedna z následujících hodnot:<ul><li><code>update</code>- Nahradí obsah tabulky na OneDrivu.</li><li><code>append</code>- Přidá datovou část na konec tabulky na OneDrivu vytvořením nových řádků.</li></ul>|

<a name="excel-output-code"></a>
### <a name="excel-output---usage"></a>Výstup aplikace Excel - využití

Tato vazba vyžaduje následující oprávnění Azure AD:

|Prostředek|Oprávnění|
|--------|--------|
|Microsoft Graph|Mít plný přístup k uživatelským souborům|

Vazba zveřejňuje následující typy .NET funkce:
- řetězec[][]
- Newtonsoft.Json.Linq.JObjekt
- Microsoft.Graph.WorkbookTable
- Vlastní typy objektů (pomocí vazby konstrukčního modelu)





<a name="onedrive-input"></a>
## <a name="file-input"></a>Vstup do souboru

Vstupní vazba souboru OneDrivu přečte obsah souboru uloženého na OneDrivu.

Tato část obsahuje následující pododdíly:

* [Příklad](#file-input---example)
* [Atributy](#file-input---attributes)
* [Konfigurace](#file-input---configuration)
* [Použití](#file-input---usage)

### <a name="file-input---example"></a>Vstup souboru – příklad

Podívejte se na příklad pro konkrétní jazyk:

* [Skript jazyka C# (.csx)](#file-input---c-script-example)
* [Javascript](#file-input---javascript-example)

#### <a name="file-input---c-script-example"></a>Příklad vstupu do souboru – příklad skriptu jazyka C#

Následující příklad přečte soubor, který je uložený na OneDrivu.

Soubor *function.json* definuje aktivační událost HTTP se vstupní vazbou souboru OneDrivu:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "myOneDriveFile",
      "type": "onedrive",
      "direction": "in",
      "path": "{query.filename}",
      "identity": "userFromRequest"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Kód skriptu Jazyka C# přečte soubor zadaný v řetězci dotazu a zaznamená jeho délku:

```csharp
using System.Net;
using Microsoft.Extensions.Logging;

public static void Run(HttpRequestMessage req, Stream myOneDriveFile, ILogger log)
{
    log.LogInformation(myOneDriveFile.Length.ToString());
}
```

#### <a name="file-input---javascript-example"></a>Vstup do souboru – příklad JavaScriptu

Následující příklad přečte soubor, který je uložený na OneDrivu.

Soubor *function.json* definuje aktivační událost HTTP se vstupní vazbou souboru OneDrivu:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "myOneDriveFile",
      "type": "onedrive",
      "direction": "in",
      "path": "{query.filename}",
      "identity": "userFromRequest"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Následující kód Jazyka JavaScript přečte soubor zadaný v řetězci dotazu a vrátí jeho délku.

```js
module.exports = function (context, req) {
    context.res = {
        body: context.bindings.myOneDriveFile.length
    };
    context.done();
};
```

### <a name="file-input---attributes"></a>Vstup souboru - atributy

V [knihovnách tříd C#](functions-dotnet-class-library.md)použijte atribut [OneDrive.](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OneDriveAttribute.cs)

### <a name="file-input---configuration"></a>Vstup souboru - konfigurace

Následující tabulka vysvětluje vlastnosti konfigurace vazby, které jste nastavili `OneDrive` v souboru *function.json* a atributu.

|vlastnost function.json | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**Jméno**| neuvedeno |Povinné - název proměnné použitý v kódu funkce pro soubor. Viz [Použití vstupní vazby souboru OneDrivu z kódu](#onedrive-input-code).|
|**Typ**| neuvedeno |Povinné - musí `onedrive`být nastavena na .|
|**direction**| neuvedeno |Povinné - musí `in`být nastavena na .|
|**Identity**|**Identita**|Povinné - Identita, která bude použita k provedení akce. Může se na nich vyvěšovat jedna z následujících hodnot:<ul><li><code>userFromRequest</code>- Platí pouze s [aktivační událostí HTTP]. Používá identitu volajícího uživatele.</li><li><code>userFromId</code>- Používá identitu dříve přihlášeného uživatele se zadaným ID. Podívejte <code>userId</code> se na nemovitost.</li><li><code>userFromToken</code>- Používá identitu reprezentovanou zadaným tokenem. Podívejte <code>userToken</code> se na nemovitost.</li><li><code>clientCredentials</code>- Používá identitu aplikace funkce.</li></ul>|
|**Userid**|**Userid**  |Potřebné pouze v případě, že `userFromId` _je identita_ nastavena na . ID objektu uživatele přidružené k dříve přihlášenému uživateli.|
|**Usertoken**|**Usertoken**|Potřebné pouze v případě, že `userFromToken` _je identita_ nastavena na . Token platný pro aplikaci funkce. |
|**Cestu**|**Cesta**|Povinné – cesta na OneDrivu k souboru.|

<a name="onedrive-input-code"></a>
### <a name="file-input---usage"></a>Vstup souboru - využití

Tato vazba vyžaduje následující oprávnění Azure AD:

|Prostředek|Oprávnění|
|--------|--------|
|Microsoft Graph|Čtení uživatelských souborů|

Vazba zveřejňuje následující typy .NET funkce:
- bajt[]
- Datový proud
- řetězec
- Položka Microsoft.Graph.DriveItem






<a name="onedrive-output"></a>
## <a name="file-output"></a>Výstup souboru

Výstupní vazba souboru OneDrivu upravuje obsah souboru uloženého na OneDrivu.

Tato část obsahuje následující pododdíly:

* [Příklad](#file-output---example)
* [Atributy](#file-output---attributes)
* [Konfigurace](#file-output---configuration)
* [Použití](#file-output---usage)

### <a name="file-output---example"></a>Výstup souboru – příklad

Podívejte se na příklad pro konkrétní jazyk:

* [Skript jazyka C# (.csx)](#file-output---c-script-example)
* [Javascript](#file-output---javascript-example)

#### <a name="file-output---c-script-example"></a>Výstup souboru – příklad skriptu Jazyka C#

Následující příklad zapíše do souboru, který je uložený na OneDrivu.

Soubor *function.json* definuje aktivační událost HTTP s výstupní vazbou OneDrivu:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "myOneDriveFile",
      "type": "onedrive",
      "direction": "out",
      "path": "FunctionsTest.txt",
      "identity": "userFromRequest"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Kód skriptu Jazyka C# získá text z řetězce dotazu a zapíše jej do textového souboru (FunctionsTest.txt, jak je definovánv předchozím příkladu) v kořenovém adresáři onedrive volajícího:

```csharp
using System.Net;
using System.Text;
using Microsoft.Extensions.Logging;

public static async Task Run(HttpRequest req, ILogger log, Stream myOneDriveFile)
{
    string data = req.Query
        .FirstOrDefault(q => string.Compare(q.Key, "text", true) == 0)
        .Value;
    await myOneDriveFile.WriteAsync(Encoding.UTF8.GetBytes(data), 0, data.Length);
    myOneDriveFile.Close();
    return;
}
```

#### <a name="file-output---javascript-example"></a>Výstup souboru – příklad JavaScriptu

Následující příklad zapíše do souboru, který je uložený na OneDrivu.

Soubor *function.json* definuje aktivační událost HTTP s výstupní vazbou OneDrivu:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "myOneDriveFile",
      "type": "onedrive",
      "direction": "out",
      "path": "FunctionsTest.txt",
      "identity": "userFromRequest"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Kód Jazyka JavaScript získá text z řetězce dotazu a zapíše jej do textového souboru (FunctionsTest.txt, jak je definováno v konfiguraci výše) v kořenovém adresáři onedrive volajícího.

```js
module.exports = function (context, req) {
    context.bindings.myOneDriveFile = req.query.text;
    context.done();
};
```

### <a name="file-output---attributes"></a>Výstup souboru - atributy

V [knihovnách tříd C#](functions-dotnet-class-library.md)použijte atribut [OneDrive.](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OneDriveAttribute.cs)

### <a name="file-output---configuration"></a>Výstup souboru - konfigurace

Následující tabulka vysvětluje vlastnosti konfigurace vazby, které jste nastavili `OneDrive` v souboru *function.json* a atributu.

|vlastnost function.json | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**Jméno**| neuvedeno |Povinné - název proměnné použitý v kódu funkce pro soubor. Viz [Použití výstupní vazby souboru OneDrivu z kódu](#onedrive-output-code).|
|**Typ**| neuvedeno |Povinné - musí `onedrive`být nastavena na .|
|**direction**| neuvedeno |Povinné - musí `out`být nastavena na .|
|**Identity**|**Identita**|Povinné - Identita, která bude použita k provedení akce. Může se na nich vyvěšovat jedna z následujících hodnot:<ul><li><code>userFromRequest</code>- Platí pouze s [aktivační událostí HTTP]. Používá identitu volajícího uživatele.</li><li><code>userFromId</code>- Používá identitu dříve přihlášeného uživatele se zadaným ID. Podívejte <code>userId</code> se na nemovitost.</li><li><code>userFromToken</code>- Používá identitu reprezentovanou zadaným tokenem. Podívejte <code>userToken</code> se na nemovitost.</li><li><code>clientCredentials</code>- Používá identitu aplikace funkce.</li></ul>|
|**Userid** |**Userid** |Potřebné pouze v případě, že `userFromId` _je identita_ nastavena na . ID objektu uživatele přidružené k dříve přihlášenému uživateli.|
|**Usertoken**|**Usertoken**|Potřebné pouze v případě, že `userFromToken` _je identita_ nastavena na . Token platný pro aplikaci funkce. |
|**Cestu**|**Cesta**|Povinné – cesta na OneDrivu k souboru.|

<a name="onedrive-output-code"></a>
#### <a name="file-output---usage"></a>Výstup souboru - využití

Tato vazba vyžaduje následující oprávnění Azure AD:

|Prostředek|Oprávnění|
|--------|--------|
|Microsoft Graph|Mít plný přístup k uživatelským souborům|

Vazba zveřejňuje následující typy .NET funkce:
- bajt[]
- Datový proud
- řetězec
- Položka Microsoft.Graph.DriveItem





<a name="outlook-output"></a>
## <a name="outlook-output"></a>Výstup aplikace Outlook

Výstupní vazba zprávy aplikace Outlook odešle e-mailovou zprávu prostřednictvím aplikace Outlook.

Tato část obsahuje následující pododdíly:

* [Příklad](#outlook-output---example)
* [Atributy](#outlook-output---attributes)
* [Konfigurace](#outlook-output---configuration)
* [Použití](#outlook-output---usage)

### <a name="outlook-output---example"></a>Výstup aplikace Outlook – příklad

Podívejte se na příklad pro konkrétní jazyk:

* [Skript jazyka C# (.csx)](#outlook-output---c-script-example)
* [Javascript](#outlook-output---javascript-example)

#### <a name="outlook-output---c-script-example"></a>Příklad skriptu aplikace Outlook – skript jazyka C#

Následující příklad odešle e-mail prostřednictvím aplikace Outlook.

Soubor *function.json* definuje aktivační událost HTTP s vazbou výstupu zprávy aplikace Outlook:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "message",
      "type": "outlook",
      "direction": "out",
      "identity": "userFromRequest"
    }
  ],
  "disabled": false
}
```

Kód skriptu Jazyka C# odešle poštu od volajícího příjemci určenému v řetězci dotazu:

```csharp
using System.Net;
using Microsoft.Extensions.Logging;

public static void Run(HttpRequest req, out Message message, ILogger log)
{ 
    string emailAddress = req.Query["to"];
    message = new Message(){
        subject = "Greetings",
        body = "Sent from Azure Functions",
        recipient = new Recipient() {
            address = emailAddress
        }
    };
}

public class Message {
    public String subject {get; set;}
    public String body {get; set;}
    public Recipient recipient {get; set;}
}

public class Recipient {
    public String address {get; set;}
    public String name {get; set;}
}
```

#### <a name="outlook-output---javascript-example"></a>Výstup aplikace Outlook – příklad JavaScriptu

Následující příklad odešle e-mail prostřednictvím aplikace Outlook.

Soubor *function.json* definuje aktivační událost HTTP s vazbou výstupu zprávy aplikace Outlook:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "message",
      "type": "outlook",
      "direction": "out",
      "identity": "userFromRequest"
    }
  ],
  "disabled": false
}
```

Kód Jazyka JavaScript odešle poštu od volajícího příjemci určenému v řetězci dotazu:

```js
module.exports = function (context, req) {
    context.bindings.message = {
        subject: "Greetings",
        body: "Sent from Azure Functions with JavaScript",
        recipient: {
            address: req.query.to 
        } 
    };
    context.done();
};
```

### <a name="outlook-output---attributes"></a>Výstup aplikace Outlook - atributy

V [knihovnách tříd jazyka C#](functions-dotnet-class-library.md)použijte atribut [Outlook.](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OutlookAttribute.cs)

### <a name="outlook-output---configuration"></a>Výstup aplikace Outlook - konfigurace

Následující tabulka vysvětluje vlastnosti konfigurace vazby, které jste nastavili `Outlook` v souboru *function.json* a atributu.

|vlastnost function.json | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**Jméno**| neuvedeno |Povinné - název proměnné použitý v kódu funkce pro e-mailovou zprávu. Viz [Použití výstupní vazby zprávy aplikace Outlook z kódu](#outlook-output-code).|
|**Typ**| neuvedeno |Povinné - musí `outlook`být nastavena na .|
|**direction**| neuvedeno |Povinné - musí `out`být nastavena na .|
|**Identity**|**Identita**|Povinné - Identita, která bude použita k provedení akce. Může se na nich vyvěšovat jedna z následujících hodnot:<ul><li><code>userFromRequest</code>- Platí pouze s [aktivační událostí HTTP]. Používá identitu volajícího uživatele.</li><li><code>userFromId</code>- Používá identitu dříve přihlášeného uživatele se zadaným ID. Podívejte <code>userId</code> se na nemovitost.</li><li><code>userFromToken</code>- Používá identitu reprezentovanou zadaným tokenem. Podívejte <code>userToken</code> se na nemovitost.</li><li><code>clientCredentials</code>- Používá identitu aplikace funkce.</li></ul>|
|**Userid**|**Userid**  |Potřebné pouze v případě, že `userFromId` _je identita_ nastavena na . ID objektu uživatele přidružené k dříve přihlášenému uživateli.|
|**Usertoken**|**Usertoken**|Potřebné pouze v případě, že `userFromToken` _je identita_ nastavena na . Token platný pro aplikaci funkce. |

<a name="outlook-output-code"></a>
### <a name="outlook-output---usage"></a>Výstup aplikace Outlook – využití

Tato vazba vyžaduje následující oprávnění Azure AD:

|Prostředek|Oprávnění|
|--------|--------|
|Microsoft Graph|Odeslat poštu jako uživatel|

Vazba zveřejňuje následující typy .NET funkce:
- Microsoft.Graph.Message
- Newtonsoft.Json.Linq.JObjekt
- řetězec
- Vlastní typy objektů (pomocí vazby konstrukčního modelu)






## <a name="webhooks"></a>Webhooky

Webhooky umožňují reagovat na události v microsoft graphu. Pro podporu webhooků jsou potřebné funkce k vytvoření, aktualizaci a reakci na _odběry webhooku_. Kompletní řešení webhooku vyžaduje kombinaci následujících vazeb:
- [Aktivační událost webhooku aplikace Microsoft Graph](#webhook-trigger) umožňuje reagovat na příchozí webhook.
- [Vstupní vazba předplatného webhooku aplikace Microsoft Graph](#webhook-input) umožňuje vypsat existující odběry a volitelně je aktualizovat.
- Výstupová [vazba předplatného webhooku aplikace Microsoft Graph](#webhook-output) umožňuje vytvářet nebo odstraňovat odběry webhooku.

Samotné vazby nevyžadují žádná oprávnění Azure AD, ale je třeba požádat o oprávnění relevantní pro typ prostředku, na který chcete reagovat. Seznam, která oprávnění jsou potřebná pro každý typ prostředku, naleznete v [tématu oprávnění k odběru](https://docs.microsoft.com/graph/api/subscription-post-subscriptions?view=graph-rest-1.0).

Další informace o webhooky naleznete v [tématu Práce s webhooky v microsoft graphu].





## <a name="webhook-trigger"></a>Spoušť webhooku

Aktivační událost webhooku aplikace Microsoft Graph umožňuje funkci reagovat na příchozí webhook z microsoft graphu. Každá instance této aktivační události může reagovat na jeden typ prostředku služby Microsoft Graph.

Tato část obsahuje následující pododdíly:

* [Příklad](#webhook-trigger---example)
* [Atributy](#webhook-trigger---attributes)
* [Konfigurace](#webhook-trigger---configuration)
* [Použití](#webhook-trigger---usage)

### <a name="webhook-trigger---example"></a>Webhooková spoušť – příklad

Podívejte se na příklad pro konkrétní jazyk:

* [Skript jazyka C# (.csx)](#webhook-trigger---c-script-example)
* [Javascript](#webhook-trigger---javascript-example)

#### <a name="webhook-trigger---c-script-example"></a>Webhook trigger - příklad skriptu Jazyka C#

Následující příklad zpracovává webhooky pro příchozí zprávy aplikace Outlook. Chcete-li použít aktivační událost [webhooku, vytvořte předplatné](#webhook-output---example)a můžete [aktualizovat odběr,](#webhook-subscription-refresh) abyste zabránili vypršení jeho platnosti.

Soubor *function.json* definuje aktivační událost webhooku:

```json
{
  "bindings": [
    {
      "name": "msg",
      "type": "GraphWebhookTrigger",
      "direction": "in",
      "resourceType": "#Microsoft.Graph.Message"
    }
  ],
  "disabled": false
}
```

Kód skriptu Jazyka C# reaguje na příchozí e-mailové zprávy a zaznamenává tělo těch odeslaných příjemcem a obsahující "Funkce Azure" v předmětu:

```csharp
#r "Microsoft.Graph"
using Microsoft.Graph;
using System.Net;
using Microsoft.Extensions.Logging;

public static async Task Run(Message msg, ILogger log)  
{
    log.LogInformation("Microsoft Graph webhook trigger function processed a request.");

    // Testable by sending oneself an email with the subject "Azure Functions" and some text body
    if (msg.Subject.Contains("Azure Functions") && msg.From.Equals(msg.Sender)) {
        log.LogInformation($"Processed email: {msg.BodyPreview}");
    }
}
```

#### <a name="webhook-trigger---javascript-example"></a>Webhooková aktivační událost – příklad JavaScriptu

Následující příklad zpracovává webhooky pro příchozí zprávy aplikace Outlook. Chcete-li použít aktivační událost [webhooku, vytvořte předplatné](#webhook-output---example)a můžete [aktualizovat odběr,](#webhook-subscription-refresh) abyste zabránili vypršení jeho platnosti.

Soubor *function.json* definuje aktivační událost webhooku:

```json
{
  "bindings": [
    {
      "name": "msg",
      "type": "GraphWebhookTrigger",
      "direction": "in",
      "resourceType": "#Microsoft.Graph.Message"
    }
  ],
  "disabled": false
}
```

Kód JavaScript reaguje na příchozí e-mailové zprávy a zaznamenává tělo těch odeslaných příjemcem a obsahující "Funkce Azure" v předmětu:

```js
module.exports = function (context) {
    context.log("Microsoft Graph webhook trigger function processed a request.");
    const msg = context.bindings.msg
    // Testable by sending oneself an email with the subject "Azure Functions" and some text body
    if((msg.subject.indexOf("Azure Functions") > -1) && (msg.from === msg.sender) ) {
      context.log(`Processed email: ${msg.bodyPreview}`);
    }
    context.done();
};
```

### <a name="webhook-trigger---attributes"></a>Webhooková aktivační událost - atributy

V [knihovnách tříd jazyka C#](functions-dotnet-class-library.md)použijte atribut [GraphWebhookTrigger.](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebhookTriggerAttribute.cs)

### <a name="webhook-trigger---configuration"></a>Webhooková spoušť - konfigurace

Následující tabulka vysvětluje vlastnosti konfigurace vazby, které jste nastavili `GraphWebhookTrigger` v souboru *function.json* a atributu.

|vlastnost function.json | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**Jméno**| neuvedeno |Povinné - název proměnné použitý v kódu funkce pro e-mailovou zprávu. Viz [Použití výstupní vazby zprávy aplikace Outlook z kódu](#outlook-output-code).|
|**Typ**| neuvedeno |Povinné - musí `graphWebhook`být nastavena na .|
|**direction**| neuvedeno |Povinné - musí `trigger`být nastavena na .|
|**resourceType**|**Resourcetype**|Povinné - prostředek grafu, pro který by tato funkce měla reagovat na webhooky. Může se na nich vyvěšovat jedna z následujících hodnot:<ul><li><code>#Microsoft.Graph.Message</code>- změny provedené ve zprávách aplikace Outlook.</li><li><code>#Microsoft.Graph.DriveItem</code>- změny provedené v kořenových položkách OneDrivu.</li><li><code>#Microsoft.Graph.Contact</code>- změny osobních kontaktů v aplikaci Outlook.</li><li><code>#Microsoft.Graph.Event</code>- změny provedené v položkách kalendáře aplikace Outlook.</li></ul>|

> [!Note]
> Aplikace funkce může mít pouze jednu funkci, která je registrována proti dané `resourceType` hodnotě.

### <a name="webhook-trigger---usage"></a>Webhooktrigger - využití

Vazba zveřejňuje následující typy .NET funkce:
- Typy sad Microsoft Graph SDK relevantní `Microsoft.Graph.Message` pro `Microsoft.Graph.DriveItem`typ prostředku, například nebo .
- Vlastní typy objektů (pomocí vazby konstrukčního modelu)




<a name="webhook-input"></a>
## <a name="webhook-input"></a>Vstup webového háku

Vstupní vazba webhooku aplikace Microsoft Graph umožňuje načíst seznam odběrů spravovaných touto aplikací funkce. Vazba čte z úložiště aplikace funkce, takže neodráží jiné odběry vytvořené z mimo aplikaci.

Tato část obsahuje následující pododdíly:

* [Příklad](#webhook-input---example)
* [Atributy](#webhook-input---attributes)
* [Konfigurace](#webhook-input---configuration)
* [Použití](#webhook-input---usage)

### <a name="webhook-input---example"></a>Vstup webhooku – příklad

Podívejte se na příklad pro konkrétní jazyk:

* [Skript jazyka C# (.csx)](#webhook-input---c-script-example)
* [Javascript](#webhook-input---javascript-example)

#### <a name="webhook-input---c-script-example"></a>Webhook input - příklad skriptu Jazyka C#

Následující příklad získá všechna předplatná pro volajícího uživatele a odstraní je.

Soubor *function.json* definuje aktivační událost PROTOKOLU HTTP se vstupní vazbou odběru a výstupní vazbu odběru, která používá akci delete:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "existingSubscriptions",
      "direction": "in",
      "filter": "userFromRequest"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "subscriptionsToDelete",
      "direction": "out",
      "action": "delete",
      "identity": "userFromRequest"
    },
    {
      "type": "http",
      "name": "res",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Kód skriptu Jazyka C# získá odběry a odstraní je:

```csharp
using System.Net;
using Microsoft.Extensions.Logging;

public static async Task Run(HttpRequest req, string[] existingSubscriptions, IAsyncCollector<string> subscriptionsToDelete, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");
    foreach (var subscription in existingSubscriptions)
    {
        log.LogInformation($"Deleting subscription {subscription}");
        await subscriptionsToDelete.AddAsync(subscription);
    }
}
```

#### <a name="webhook-input---javascript-example"></a>Vstup webhooku – příklad JavaScriptu

Následující příklad získá všechna předplatná pro volajícího uživatele a odstraní je.

Soubor *function.json* definuje aktivační událost PROTOKOLU HTTP se vstupní vazbou odběru a výstupní vazbu odběru, která používá akci delete:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "existingSubscriptions",
      "direction": "in",
      "filter": "userFromRequest"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "subscriptionsToDelete",
      "direction": "out",
      "action": "delete",
      "identity": "userFromRequest"
    },
    {
      "type": "http",
      "name": "res",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Kód JavaScript získá odběry a odstraní je:

```js
module.exports = function (context, req) {
    const existing = context.bindings.existingSubscriptions;
    var toDelete = [];
    for (var i = 0; i < existing.length; i++) {
        context.log(`Deleting subscription ${existing[i]}`);
        todelete.push(existing[i]);
    }
    context.bindings.subscriptionsToDelete = toDelete;
    context.done();
};
```

### <a name="webhook-input---attributes"></a>Webhook vstup - atributy

V [knihovnách tříd jazyka C#](functions-dotnet-class-library.md)použijte atribut [GraphWebhookSubscription.](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebhookSubscriptionAttribute.cs)

### <a name="webhook-input---configuration"></a>Vstup webhooku - konfigurace

Následující tabulka vysvětluje vlastnosti konfigurace vazby, které jste nastavili `GraphWebhookSubscription` v souboru *function.json* a atributu.

|vlastnost function.json | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**Jméno**| neuvedeno |Povinné - název proměnné použitý v kódu funkce pro e-mailovou zprávu. Viz [Použití výstupní vazby zprávy aplikace Outlook z kódu](#outlook-output-code).|
|**Typ**| neuvedeno |Povinné - musí `graphWebhookSubscription`být nastavena na .|
|**direction**| neuvedeno |Povinné - musí `in`být nastavena na .|
|**Filtr**|**Filtr**| Pokud je `userFromRequest`nastavena na , bude vazba načítat pouze odběry vlastněné volajícím uživatelem (platné pouze s [aktivační událostí HTTP).]| 

### <a name="webhook-input---usage"></a>Vstup webhooku - využití

Vazba zveřejňuje následující typy .NET funkce:
- řetězec[]
- Pole typu vlastníobjekt
- Newtonsoft.Json.Linq.JObject[]
- Microsoft.Graph.Subscription[]





## <a name="webhook-output"></a>Výstup webhooku

Webhook u tvořivá výstupní vazba umožňuje vytvářet, odstraňovat a aktualizovat odběry webhooku v Microsoft Graphu.

Tato část obsahuje následující pododdíly:

* [Příklad](#webhook-output---example)
* [Atributy](#webhook-output---attributes)
* [Konfigurace](#webhook-output---configuration)
* [Použití](#webhook-output---usage)

### <a name="webhook-output---example"></a>Výstup webhooku - příklad

Podívejte se na příklad pro konkrétní jazyk:

* [Skript jazyka C# (.csx)](#webhook-output---c-script-example)
* [Javascript](#webhook-output---javascript-example)

#### <a name="webhook-output---c-script-example"></a>Webhook výstup - příklad skriptu Jazyka C#

Následující příklad vytvoří předplatné. Můžete [aktualizovat odběr zabránit](#webhook-subscription-refresh) jeho vypršení platnosti.

Soubor *function.json* definuje aktivační událost HTTP s výstupní vazbou odběrpomocí akce create:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "clientState",
      "direction": "out",
      "action": "create",
      "subscriptionResource": "me/mailFolders('Inbox')/messages",
      "changeTypes": [
        "created"
      ],
      "identity": "userFromRequest"
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Kód skriptu Jazyka C# registruje webhook, který upozorní tuto aplikaci funkce, když volající uživatel obdrží zprávu aplikace Outlook:

```csharp
using System;
using System.Net;
using Microsoft.Extensions.Logging;

public static HttpResponseMessage run(HttpRequestMessage req, out string clientState, ILogger log)
{
  log.LogInformation("C# HTTP trigger function processed a request.");
    clientState = Guid.NewGuid().ToString();
    return new HttpResponseMessage(HttpStatusCode.OK);
}
```

#### <a name="webhook-output---javascript-example"></a>Výstup webhooku – příklad JavaScriptu

Následující příklad vytvoří předplatné. Můžete [aktualizovat odběr zabránit](#webhook-subscription-refresh) jeho vypršení platnosti.

Soubor *function.json* definuje aktivační událost HTTP s výstupní vazbou odběrpomocí akce create:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "clientState",
      "direction": "out",
      "action": "create",
      "subscriptionResource": "me/mailFolders('Inbox')/messages",
      "changeTypes": [
        "created"
      ],
      "identity": "userFromRequest"
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Kód JavaScript uvede webhook, který upozorní tuto aplikaci funkce, když volající uživatel obdrží zprávu aplikace Outlook:

```js
const uuidv4 = require('uuid/v4');

module.exports = function (context, req) {
    context.bindings.clientState = uuidv4();
    context.done();
};
```

### <a name="webhook-output---attributes"></a>Webhook výstup - atributy

V [knihovnách tříd jazyka C#](functions-dotnet-class-library.md)použijte atribut [GraphWebhookSubscription.](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebhookSubscriptionAttribute.cs)

### <a name="webhook-output---configuration"></a>Výstup webhooku - konfigurace

Následující tabulka vysvětluje vlastnosti konfigurace vazby, které jste nastavili `GraphWebhookSubscription` v souboru *function.json* a atributu.

|vlastnost function.json | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**Jméno**| neuvedeno |Povinné - název proměnné použitý v kódu funkce pro e-mailovou zprávu. Viz [Použití výstupní vazby zprávy aplikace Outlook z kódu](#outlook-output-code).|
|**Typ**| neuvedeno |Povinné - musí `graphWebhookSubscription`být nastavena na .|
|**direction**| neuvedeno |Povinné - musí `out`být nastavena na .|
|**Identity**|**Identita**|Povinné - Identita, která bude použita k provedení akce. Může se na nich vyvěšovat jedna z následujících hodnot:<ul><li><code>userFromRequest</code>- Platí pouze s [aktivační událostí HTTP]. Používá identitu volajícího uživatele.</li><li><code>userFromId</code>- Používá identitu dříve přihlášeného uživatele se zadaným ID. Podívejte <code>userId</code> se na nemovitost.</li><li><code>userFromToken</code>- Používá identitu reprezentovanou zadaným tokenem. Podívejte <code>userToken</code> se na nemovitost.</li><li><code>clientCredentials</code>- Používá identitu aplikace funkce.</li></ul>|
|**Userid**|**Userid**  |Potřebné pouze v případě, že `userFromId` _je identita_ nastavena na . ID objektu uživatele přidružené k dříve přihlášenému uživateli.|
|**Usertoken**|**Usertoken**|Potřebné pouze v případě, že `userFromToken` _je identita_ nastavena na . Token platný pro aplikaci funkce. |
|**Akce**|**Akce**|Povinné - určuje akci, kterou by měla vazba provést. Může se na nich vyvěšovat jedna z následujících hodnot:<ul><li><code>create</code>- Zaregistruje nové předplatné.</li><li><code>delete</code>- Odstraní zadané předplatné.</li><li><code>refresh</code>- Aktualizuje zadané předplatné, aby jeho platnost nevypršela.</li></ul>|
|**zdroj předplatného**|**Zdroj předplatného**|Potřebné pouze v případě, že `create`je _akce_ nastavena na . Určuje prostředek aplikace Microsoft Graph, který bude sledován z důvodu změn. Viz [Práce s webovými háčky v Microsoft Graphu]. |
|**Changetype**|**Changetype**|Potřebné pouze v případě, že `create`je _akce_ nastavena na . Označuje typ změny v odebíraném prostředku, který vyvolá oznámení. Podporované hodnoty `created`jsou: `updated` `deleted`, , . Pomocí seznamu odděleného čárkami lze kombinovat více hodnot.|

### <a name="webhook-output---usage"></a>Webhook výstup - využití

Vazba zveřejňuje následující typy .NET funkce:
- řetězec
- Microsoft.Graph.Subscription




<a name="webhook-examples"></a>
## <a name="webhook-subscription-refresh"></a>Aktualizace předplatného Webhooku

Existují dva přístupy k aktualizaci odběrů:

- Použijte identitu aplikace pro řešení všech předplatných. To bude vyžadovat souhlas správce služby Azure Active Directory. To lze použít ve všech jazycích podporovaných funkcemi Azure.
- Použijte identitu přidruženou ke každému předplatnému ručním vázáním každého ID uživatele. To bude vyžadovat některé vlastní kód k provedení vazby. To lze použít pouze funkce mise .NET.

Tato část obsahuje příklad pro každý z těchto přístupů:

* [Příklad identity aplikace](#webhook-subscription-refresh---app-identity-example)
* [Příklad identity uživatele](#webhook-subscription-refresh---user-identity-example)

### <a name="webhook-subscription-refresh---app-identity-example"></a>Aktualizace předplatného Webhook – příklad identity aplikace

Podívejte se na příklad pro konkrétní jazyk:

* [Skript jazyka C# (.csx)](#app-identity-refresh---c-script-example)
* JavaScript

### <a name="app-identity-refresh---c-script-example"></a>Příklad skriptu identity aplikace – příklad skriptu Jazyka C#

Následující příklad používá identitu aplikace k aktualizaci předplatného.

*Function.json* definuje aktivační událost časovače se vstupní vazbou předplatného a výstupní vazbou předplatného:

```json
{
  "bindings": [
    {
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in",
      "schedule": "0 * * */2 * *"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "existingSubscriptions",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "subscriptionsToRefresh",
      "direction": "out",
      "action": "refresh",
      "identity": "clientCredentials"
    }
  ],
  "disabled": false
}
```

Kód skriptu Jazyka C# aktualizuje odběry:

```csharp
using System;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, string[] existingSubscriptions, ICollector<string> subscriptionsToRefresh, ILogger log)
{
    // This template uses application permissions and requires consent from an Azure Active Directory admin.
    // See https://go.microsoft.com/fwlink/?linkid=858780
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    foreach (var subscription in existingSubscriptions)
    {
      log.LogInformation($"Refreshing subscription {subscription}");
      subscriptionsToRefresh.Add(subscription);
    }
}
```

### <a name="app-identity-refresh---c-script-example"></a>Příklad skriptu identity aplikace – příklad skriptu Jazyka C#

Následující příklad používá identitu aplikace k aktualizaci předplatného.

*Function.json* definuje aktivační událost časovače se vstupní vazbou předplatného a výstupní vazbou předplatného:

```json
{
  "bindings": [
    {
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in",
      "schedule": "0 * * */2 * *"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "existingSubscriptions",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "subscriptionsToRefresh",
      "direction": "out",
      "action": "refresh",
      "identity": "clientCredentials"
    }
  ],
  "disabled": false
}
```

Kód JavaScript aktualizuje odběry:

```js
// This template uses application permissions and requires consent from an Azure Active Directory admin.
// See https://go.microsoft.com/fwlink/?linkid=858780

module.exports = function (context) {
    const existing = context.bindings.existingSubscriptions;
    var toRefresh = [];
    for (var i = 0; i < existing.length; i++) {
        context.log(`Refreshing subscription ${existing[i]}`);
        toRefresh.push(existing[i]);
    }
    context.bindings.subscriptionsToRefresh = toRefresh;
    context.done();
};
```

### <a name="webhook-subscription-refresh---user-identity-example"></a>Aktualizace předplatného Webhook – příklad identity uživatele

Následující příklad používá identitu uživatele k aktualizaci předplatného.

Soubor *function.json* definuje aktivační událost časovače a odkládá vstupní vazbu odběru na kód funkce:

```json
{
  "bindings": [
    {
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in",
      "schedule": "0 * * */2 * *"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "existingSubscriptions",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

Kód skriptu Jazyka C# aktualizuje odběry a vytvoří výstupní vazbu v kódu pomocí identity každého uživatele:

```csharp
using System;
using Microsoft.Extensions.Logging;

public static async Task Run(TimerInfo myTimer, UserSubscription[] existingSubscriptions, IBinder binder, ILogger log)
{
  log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    foreach (var subscription in existingSubscriptions)
    {
        // binding in code to allow dynamic identity
        using (var subscriptionsToRefresh = await binder.BindAsync<IAsyncCollector<string>>(
            new GraphWebhookSubscriptionAttribute() {
                Action = "refresh",
                Identity = "userFromId",
                UserId = subscription.UserId
            }
        ))
        {
            log.LogInformation($"Refreshing subscription {subscription}");
            await subscriptionsToRefresh.AddAsync(subscription);
        }

    }
}

public class UserSubscription {
    public string UserId {get; set;}
    public string Id {get; set;}
}
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Další informace o aktivačních událostech a vazbách funkcí Azure](functions-triggers-bindings.md)

[Aktivační událost PROTOKOLU HTTP]: functions-bindings-http-webhook.md
[Práce s webovými háčky v Microsoft Graphu]: https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/webhooks
