---
title: Microsoft Graph vazby pro Azure Functions
description: Naučte se používat Microsoft Graph triggery a vazby v Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 12/20/2017
ms.author: cshoe
ms.openlocfilehash: 290765b17cf34417176930dc9116309bdfd754c8
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2020
ms.locfileid: "86506532"
---
# <a name="microsoft-graph-bindings-for-azure-functions"></a>Microsoft Graph vazby pro Azure Functions

Tento článek vysvětluje, jak nakonfigurovat a pracovat s Microsoft Graph triggery a vazbami v Azure Functions. V těchto případech můžete použít Azure Functions pro práci s daty, přehledy a událostmi z [Microsoft Graph](https://developer.microsoft.com/graph).

Rozšíření Microsoft Graph poskytuje následující vazby:
- [Vstupní vazba ověřovacího tokenu](#token-input) umožňuje interakci s jakýmkoli Microsoft Graph API.
- [Vstupní vazba tabulky v Excelu](#excel-input) umožňuje číst data z Excelu.
- [Výstupní vazba tabulky v Excelu](#excel-output) umožňuje upravovat data Excelu.
- [Vazba vstupu na soubor na OneDrivu](#onedrive-input) umožňuje číst soubory z OneDrivu.
- [Vazba na výstup souboru na OneDrivu](#onedrive-output) umožňuje zapisovat do souborů na OneDrivu.
- [Výstupní vazba zprávy Outlooku](#outlook-output) umožňuje odeslat E-mail přes Outlook.
- Kolekce [triggerů a vazeb Microsoft Graph webhooků](#webhooks) umožňuje reagovat na události z Microsoft Graph.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!Note]
> Microsoft Graph vazby jsou momentálně ve verzi Preview pro Azure Functions verze 2. x a vyšší. Ve funkcích verze 1. x nejsou podporovány.

## <a name="packages"></a>Balíčky

Vstupní vazba tokenu ověřování je k dispozici v balíčku NuGet [Microsoft. Azure. WebJobs. Extensions. AuthTokens](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.AuthTokens/) . Ostatní vazby Microsoft Graph jsou k dispozici v balíčku [Microsoft. Azure. WebJobs. Extensions. MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph/) . Zdrojový kód pro balíčky je v úložišti GitHub [Azure-Functions-microsoftgraph-Extension](https://github.com/Azure/azure-functions-microsoftgraph-extension/) .

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="setting-up-the-extensions"></a>Nastavení rozšíření

Vazby Microsoft Graph jsou k dispozici prostřednictvím _rozšíření vazby_. Rozšíření vazby jsou volitelné součásti modulu runtime Azure Functions. V této části se dozvíte, jak nastavit Microsoft Graph a rozšíření ověřovacích tokenů.

### <a name="enabling-functions-20"></a>Povolování funkcí 2,0

Rozšíření vazby jsou k dispozici pouze pro Azure Functions 2,0. 

Informace o tom, jak nastavit aplikaci funkcí tak, aby používala verzi 2,0 modulu runtime Functions, najdete v tématu [jak cílit na verze Azure Functions runtime](set-runtime-version.md).

### <a name="installing-the-extension"></a>Instalace rozšíření

Chcete-li nainstalovat rozšíření z Azure Portal, přejděte na šablonu nebo vazbu, která na ni odkazuje. Vytvořte novou funkci a na obrazovce Výběr šablony vyberte scénář "Microsoft Graph". Vyberte jednu z šablon z tohoto scénáře. Alternativně můžete přejít na kartu integrace existující funkce a vybrat jednu ze vazeb popsaných v tomto článku.

V obou případech se zobrazí upozornění, které určuje rozšíření, které se má nainstalovat. Rozšíření získáte kliknutím na **instalovat** . Každé rozšíření se musí nainstalovat jenom jednou pro každou aplikaci Function App. 

> [!Note] 
> Proces instalace na portálu může trvat až 10 minut v plánu spotřeby.

Pokud používáte aplikaci Visual Studio, můžete získat rozšíření instalací [balíčků NuGet, které jsou uvedeny dříve v tomto článku](#packages).

### <a name="configuring-authentication--authorization"></a>Konfigurace ověřování/autorizace

Vazby uvedené v tomto článku vyžadují identitu, která se má použít. To umožňuje Microsoft Graph vymáhat interakce oprávnění a auditu. Identitou může být uživatel, který přistupuje k aplikaci nebo samotné aplikaci. Pokud chcete tuto identitu nakonfigurovat, nastavte [App Service ověřování/autorizaci](../app-service/overview-authentication-authorization.md) pomocí Azure Active Directory. Budete taky muset požádat o všechna oprávnění prostředků, která vaše funkce vyžadují.

> [!Note] 
> Rozšíření Microsoft Graph podporuje jenom ověřování Azure AD. Uživatelé se musí přihlásit pomocí pracovního nebo školního účtu.

Pokud používáte Azure Portal, zobrazí se upozornění pod výzvou k instalaci rozšíření. Upozornění vás vyzve ke konfiguraci App Service ověřování/autorizaci a vyžádání všech oprávnění, které šablona nebo vazba vyžaduje. V případě potřeby klikněte na **Konfigurovat Azure AD hned** nebo **Přidat oprávnění** .



<a name="token-input"></a>
## <a name="auth-token"></a>Ověřovací token

Vstupní vazba tokenu ověřování získá token Azure AD pro daný prostředek a poskytne ho vašemu kódu jako řetězec. Prostředek může být libovolný, pro který má aplikace oprávnění. 

Tato část obsahuje následující pododdíly:

* [Příklad](#auth-token---example)
* [Atributy](#auth-token---attributes)
* [Konfigurace](#auth-token---configuration)
* [Použití](#auth-token---usage)

### <a name="auth-token---example"></a>Ověřovací token – příklad

Podívejte se na příklad konkrétního jazyka:

* [Skript jazyka C# (.csx)](#auth-token---c-script-example)
* [JavaScript](#auth-token---javascript-example)

#### <a name="auth-token---c-script-example"></a>Ověřovací token – příklad skriptu C#

Následující příklad získá informace o profilu uživatele.

*function.jsv* souboru definuje aktivační událost HTTP pomocí vstupní vazby tokenu:

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

Kód skriptu jazyka C# používá token k vytvoření volání HTTP do Microsoft Graph a vrátí výsledek:

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

#### <a name="auth-token---javascript-example"></a>Ověřovací token – příklad JavaScriptu

Následující příklad získá informace o profilu uživatele.

*function.jsv* souboru definuje aktivační událost HTTP pomocí vstupní vazby tokenu:

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

Kód jazyka JavaScript používá token k vytvoření volání HTTP do Microsoft Graph a vrátí výsledek.

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

### <a name="auth-token---attributes"></a>Ověřovací token – atributy

V [knihovnách tříd jazyka C#](functions-dotnet-class-library.md)použijte atribut [token](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/TokenBinding/TokenAttribute.cs) .

### <a name="auth-token---configuration"></a>Ověřovací token – konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.jspro* soubor a `Token` atribut.

|function.jsvlastnost | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**Jméno**| Není k dispozici |Požadováno – název proměnné použitý v kódu funkce pro ověřovací token. Viz [použití vstupní vazby ověřovacího tokenu z kódu](#token-input-code).|
|**textový**| Není k dispozici |Požadováno – musí být nastavené na `token` .|
|**směr**| Není k dispozici |Požadováno – musí být nastavené na `in` .|
|**odcizen**|**Identita**|Požadováno – identita, která se použije k provedení této akce. Může to být jedna z následujících hodnot:<ul><li><code>userFromRequest</code>-Je platná pouze s [triggerem protokolu HTTP]. Používá identitu volajícího uživatele.</li><li><code>userFromId</code>– Používá identitu dříve přihlášeného uživatele se zadaným ID. Podívejte se na <code>userId</code> vlastnost.</li><li><code>userFromToken</code>– Používá identitu představovanou zadaným tokenem. Podívejte se na <code>userToken</code> vlastnost.</li><li><code>clientCredentials</code>– Používá identitu aplikace Function App.</li></ul>|
|**userId**|**UserId**  |Nutné pouze v případě, že je _Identita_ nastavena na `userFromId` . ID instančního uživatele přidruženého k dříve přihlášenému uživateli|
|**userToken**|**UserToken**|Nutné pouze v případě, že je _Identita_ nastavena na `userFromToken` . Token platný pro aplikaci Function App |
|**Prostředek**|**partner**|Požadováno – adresa URL prostředku služby Azure AD, pro kterou je požadován token.|

<a name="token-input-code"></a>
### <a name="auth-token---usage"></a>Ověřovací token – využití

Vazba sama o sobě nevyžaduje žádná oprávnění služby Azure AD, ale v závislosti na tom, jak se token používá, možná budete muset požádat o další oprávnění. Ověřte požadavky prostředku, ke kterému máte přístup pomocí tokenu.

Token se vždy prezentuje kódu jako řetězec.

> [!Note]
> Při místním vývoji s některým z `userFromId` `userFromToken` možností nebo `userFromRequest` můžete požadovaný token [získat ručně](https://github.com/Azure/azure-functions-microsoftgraph-extension/issues/54#issuecomment-392865857) a zadat v `X-MS-TOKEN-AAD-ID-TOKEN` hlavičce požadavku z volající klientské aplikace.


<a name="excel-input"></a>
## <a name="excel-input"></a>Vstup v Excelu

Vstupní vazba tabulky Excelu přečte obsah excelové tabulky uložené na OneDrivu.

Tato část obsahuje následující pododdíly:

* [Příklad](#excel-input---example)
* [Atributy](#excel-input---attributes)
* [Konfigurace](#excel-input---configuration)
* [Použití](#excel-input---usage)

### <a name="excel-input---example"></a>Vstupní příklad v Excelu

Podívejte se na příklad konkrétního jazyka:

* [Skript jazyka C# (.csx)](#excel-input---c-script-example)
* [JavaScript](#excel-input---javascript-example)

#### <a name="excel-input---c-script-example"></a>Ukázka vstupu Excelu – příklad skriptu C#

Následující *function.jsv* souboru definuje aktivační událost HTTP pomocí vstupní vazby v aplikaci Excel:

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

Následující kód skriptu jazyka C# načte obsah zadané tabulky a vrátí je uživateli:

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

#### <a name="excel-input---javascript-example"></a>Ukázka v Excelu – příklad JavaScriptu

Následující *function.jsv* souboru definuje aktivační událost HTTP pomocí vstupní vazby v aplikaci Excel:

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

Následující kód jazyka JavaScript načte obsah zadané tabulky a vrátí je uživateli.

```js
module.exports = function (context, req) {
    context.res = {
        body: context.bindings.excelTableData
    };
    context.done();
};
```

### <a name="excel-input---attributes"></a>Vstupní atributy Excelu

V [knihovnách tříd jazyka C#](functions-dotnet-class-library.md)použijte atribut [aplikace Excel](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/ExcelAttribute.cs) .

### <a name="excel-input---configuration"></a>Vstup v Excelu – konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.jspro* soubor a `Excel` atribut.

|function.jsvlastnost | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**Jméno**| Není k dispozici |Required – název proměnné použitý v kódu funkce pro excelovou tabulku. Viz [použití vstupní vazby z tabulky v aplikaci Excel z kódu](#excel-input-code).|
|**textový**| Není k dispozici |Požadováno – musí být nastavené na `excel` .|
|**směr**| Není k dispozici |Požadováno – musí být nastavené na `in` .|
|**odcizen**|**Identita**|Požadováno – identita, která se použije k provedení této akce. Může to být jedna z následujících hodnot:<ul><li><code>userFromRequest</code>-Je platná pouze s [triggerem protokolu HTTP]. Používá identitu volajícího uživatele.</li><li><code>userFromId</code>– Používá identitu dříve přihlášeného uživatele se zadaným ID. Podívejte se na <code>userId</code> vlastnost.</li><li><code>userFromToken</code>– Používá identitu představovanou zadaným tokenem. Podívejte se na <code>userToken</code> vlastnost.</li><li><code>clientCredentials</code>– Používá identitu aplikace Function App.</li></ul>|
|**userId**|**UserId**  |Nutné pouze v případě, že je _Identita_ nastavena na `userFromId` . ID instančního uživatele přidruženého k dříve přihlášenému uživateli|
|**userToken**|**UserToken**|Nutné pouze v případě, že je _Identita_ nastavena na `userFromToken` . Token platný pro aplikaci Function App |
|**dílčí**|**Cesta**|Požadováno – cesta k sešitu aplikace Excel v OneDrivu|
|**list**|**List**|List, ve kterém se tabulka nachází|
|**tableName**|**Tabulky**|Název tabulky Pokud není zadaný, použije se obsah listu.|

<a name="excel-input-code"></a>
### <a name="excel-input---usage"></a>Vstup z Excelu – použití

Tato vazba vyžaduje následující oprávnění služby Azure AD:

|Prostředek|Oprávnění|
|--------|--------|
|Microsoft Graph|Čtení uživatelských souborů|

Vazba zpřístupňuje následující typy pro funkce .NET:
- řetězec [] []
- Microsoft. Graph. Workbook – sešit
- Vlastní typy objektů (pomocí vazby strukturálního modelu)










<a name="excel-output"></a>
## <a name="excel-output"></a>Výstup z Excelu

Vazba na výstupu z Excelu upraví obsah excelové tabulky uložené na OneDrivu.

Tato část obsahuje následující pododdíly:

* [Příklad](#excel-output---example)
* [Atributy](#excel-output---attributes)
* [Konfigurace](#excel-output---configuration)
* [Použití](#excel-output---usage)

### <a name="excel-output---example"></a>Výstup z aplikace Excel – příklad

Podívejte se na příklad konkrétního jazyka:

* [Skript jazyka C# (.csx)](#excel-output---c-script-example)
* [JavaScript](#excel-output---javascript-example)

#### <a name="excel-output---c-script-example"></a>Výstup z Excelu – příklad skriptu C#

Následující příklad přidá řádky do excelové tabulky.

*function.jsv* souboru definuje aktivační událost HTTP pomocí výstupní vazby aplikace Excel:

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

Kód skriptu jazyka C# přidá do tabulky nový řádek (předpokládá se, že se jedná o jeden sloupec) na základě vstupu z řetězce dotazu:

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

#### <a name="excel-output---javascript-example"></a>Výstup z Excelu – příklad JavaScriptu

Následující příklad přidá řádky do excelové tabulky.

*function.jsv* souboru definuje aktivační událost HTTP pomocí výstupní vazby aplikace Excel:

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

Následující kód jazyka JavaScript přidá nový řádek do tabulky (předpokládá se, že se jedná o jeden sloupec) na základě vstupu z řetězce dotazu.

```js
module.exports = function (context, req) {
    context.bindings.newExcelRow = {
        text: req.query.text
        // Add other properties for additional columns here
    }
    context.done();
};
```

### <a name="excel-output---attributes"></a>Výstupní atributy Excelu

V [knihovnách tříd jazyka C#](functions-dotnet-class-library.md)použijte atribut [aplikace Excel](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/ExcelAttribute.cs) .

### <a name="excel-output---configuration"></a>Výstup aplikace Excel – konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.jspro* soubor a `Excel` atribut.

|function.jsvlastnost | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**Jméno**| Není k dispozici |Požadováno – název proměnné použitý v kódu funkce pro ověřovací token. Viz [použití výstupní vazby tabulky aplikace Excel z kódu](#excel-output-code).|
|**textový**| Není k dispozici |Požadováno – musí být nastavené na `excel` .|
|**směr**| Není k dispozici |Požadováno – musí být nastavené na `out` .|
|**odcizen**|**Identita**|Požadováno – identita, která se použije k provedení této akce. Může to být jedna z následujících hodnot:<ul><li><code>userFromRequest</code>-Je platná pouze s [triggerem protokolu HTTP]. Používá identitu volajícího uživatele.</li><li><code>userFromId</code>– Používá identitu dříve přihlášeného uživatele se zadaným ID. Podívejte se na <code>userId</code> vlastnost.</li><li><code>userFromToken</code>– Používá identitu představovanou zadaným tokenem. Podívejte se na <code>userToken</code> vlastnost.</li><li><code>clientCredentials</code>– Používá identitu aplikace Function App.</li></ul>|
|**UserId** |**userId** |Nutné pouze v případě, že je _Identita_ nastavena na `userFromId` . ID instančního uživatele přidruženého k dříve přihlášenému uživateli|
|**userToken**|**UserToken**|Nutné pouze v případě, že je _Identita_ nastavena na `userFromToken` . Token platný pro aplikaci Function App |
|**dílčí**|**Cesta**|Požadováno – cesta k sešitu aplikace Excel v OneDrivu|
|**list**|**List**|List, ve kterém se tabulka nachází|
|**tableName**|**Tabulky**|Název tabulky Pokud není zadaný, použije se obsah listu.|
|**Typ aktualizace**|**Typ aktualizace**|Required – typ změny, která se má provést v tabulce Může to být jedna z následujících hodnot:<ul><li><code>update</code>– Nahradí obsah tabulky na OneDrivu.</li><li><code>append</code>– Datovou část přidá na konec tabulky na OneDrivu tím, že se vytvoří nové řádky.</li></ul>|

<a name="excel-output-code"></a>
### <a name="excel-output---usage"></a>Výstup z aplikace Excel – využití

Tato vazba vyžaduje následující oprávnění služby Azure AD:

|Prostředek|Oprávnění|
|--------|--------|
|Microsoft Graph|Úplný přístup k uživatelským souborům|

Vazba zpřístupňuje následující typy pro funkce .NET:
- řetězec [] []
- Newtonsoft.Js. LINQ. JObject
- Microsoft. Graph. Workbook – sešit
- Vlastní typy objektů (pomocí vazby strukturálního modelu)





<a name="onedrive-input"></a>
## <a name="file-input"></a>Vstup souboru

Vazba vstupu na soubor na OneDrivu čte obsah souboru uloženého na OneDrivu.

Tato část obsahuje následující pododdíly:

* [Příklad](#file-input---example)
* [Atributy](#file-input---attributes)
* [Konfigurace](#file-input---configuration)
* [Použití](#file-input---usage)

### <a name="file-input---example"></a>Vstup ze souboru – příklad

Podívejte se na příklad konkrétního jazyka:

* [Skript jazyka C# (.csx)](#file-input---c-script-example)
* [JavaScript](#file-input---javascript-example)

#### <a name="file-input---c-script-example"></a>Vstup ze souboru – Příklad skriptu C#

V následujícím příkladu se přečte soubor, který je uložený na OneDrivu.

*function.jsv* souboru definuje aktivační událost http se vstupní vazbou souboru OneDrive:

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

Kód skriptu jazyka C# načte soubor zadaný v řetězci dotazu a zaznamená jeho délku:

```csharp
using System.Net;
using Microsoft.Extensions.Logging;

public static void Run(HttpRequestMessage req, Stream myOneDriveFile, ILogger log)
{
    log.LogInformation(myOneDriveFile.Length.ToString());
}
```

#### <a name="file-input---javascript-example"></a>Vstup ze souboru – příklad JavaScriptu

V následujícím příkladu se přečte soubor, který je uložený na OneDrivu.

*function.jsv* souboru definuje aktivační událost http se vstupní vazbou souboru OneDrive:

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

Následující kód JavaScriptu načte soubor zadaný v řetězci dotazu a vrátí jeho délku.

```js
module.exports = function (context, req) {
    context.res = {
        body: context.bindings.myOneDriveFile.length
    };
    context.done();
};
```

### <a name="file-input---attributes"></a>Vstup souboru – atributy

V [knihovnách tříd C#](functions-dotnet-class-library.md)použijte atribut [OneDrive](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OneDriveAttribute.cs) .

### <a name="file-input---configuration"></a>Vstup souboru – konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.jspro* soubor a `OneDrive` atribut.

|function.jsvlastnost | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**Jméno**| Není k dispozici |Požadováno – název proměnné použitý v kódu funkce souboru. Viz [použití vazby vstupu souboru na OneDrivu z kódu](#onedrive-input-code).|
|**textový**| Není k dispozici |Požadováno – musí být nastavené na `onedrive` .|
|**směr**| Není k dispozici |Požadováno – musí být nastavené na `in` .|
|**odcizen**|**Identita**|Požadováno – identita, která se použije k provedení této akce. Může to být jedna z následujících hodnot:<ul><li><code>userFromRequest</code>-Je platná pouze s [triggerem protokolu HTTP]. Používá identitu volajícího uživatele.</li><li><code>userFromId</code>– Používá identitu dříve přihlášeného uživatele se zadaným ID. Podívejte se na <code>userId</code> vlastnost.</li><li><code>userFromToken</code>– Používá identitu představovanou zadaným tokenem. Podívejte se na <code>userToken</code> vlastnost.</li><li><code>clientCredentials</code>– Používá identitu aplikace Function App.</li></ul>|
|**userId**|**UserId**  |Nutné pouze v případě, že je _Identita_ nastavena na `userFromId` . ID instančního uživatele přidruženého k dříve přihlášenému uživateli|
|**userToken**|**UserToken**|Nutné pouze v případě, že je _Identita_ nastavena na `userFromToken` . Token platný pro aplikaci Function App |
|**dílčí**|**Cesta**|Požadováno – cesta k souboru na OneDrivu|

<a name="onedrive-input-code"></a>
### <a name="file-input---usage"></a>Vstup ze souboru – použití

Tato vazba vyžaduje následující oprávnění služby Azure AD:

|Prostředek|Oprávnění|
|--------|--------|
|Microsoft Graph|Čtení uživatelských souborů|

Vazba zpřístupňuje následující typy pro funkce .NET:
- Byte []
- Stream
- řetězec
- Microsoft. Graph. DriveItem






<a name="onedrive-output"></a>
## <a name="file-output"></a>Výstup souboru

Vazba výstupu souboru na OneDrivu upraví obsah souboru uloženého na OneDrivu.

Tato část obsahuje následující pododdíly:

* [Příklad](#file-output---example)
* [Atributy](#file-output---attributes)
* [Konfigurace](#file-output---configuration)
* [Použití](#file-output---usage)

### <a name="file-output---example"></a>Výstup souboru – příklad

Podívejte se na příklad konkrétního jazyka:

* [Skript jazyka C# (.csx)](#file-output---c-script-example)
* [JavaScript](#file-output---javascript-example)

#### <a name="file-output---c-script-example"></a>Výstup souboru – Příklad skriptu C#

Následující příklad zapisuje do souboru, který je uložený na OneDrivu.

*function.jsv* souboru definuje Trigger http s výstupní vazbou na OneDrivu:

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

Kód skriptu jazyka C# získá text z řetězce dotazu a zapíše jej do textového souboru (FunctionsTest.txt jak je definováno v předchozím příkladu) v kořenovém adresáři OneDrivu volajícího:

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

Následující příklad zapisuje do souboru, který je uložený na OneDrivu.

*function.jsv* souboru definuje Trigger http s výstupní vazbou na OneDrivu:

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

Kód jazyka JavaScript získá text z řetězce dotazu a zapíše jej do textového souboru (FunctionsTest.txt jak je definováno v konfiguraci výše) v kořenovém adresáři OneDrivu volajícího.

```js
module.exports = function (context, req) {
    context.bindings.myOneDriveFile = req.query.text;
    context.done();
};
```

### <a name="file-output---attributes"></a>Výstup souboru – atributy

V [knihovnách tříd C#](functions-dotnet-class-library.md)použijte atribut [OneDrive](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OneDriveAttribute.cs) .

### <a name="file-output---configuration"></a>Výstup souboru – konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.jspro* soubor a `OneDrive` atribut.

|function.jsvlastnost | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**Jméno**| Není k dispozici |Požadováno – název proměnné použitý v kódu funkce pro soubor. Viz [použití vazby výstupu souboru na OneDrivu z kódu](#onedrive-output-code).|
|**textový**| Není k dispozici |Požadováno – musí být nastavené na `onedrive` .|
|**směr**| Není k dispozici |Požadováno – musí být nastavené na `out` .|
|**odcizen**|**Identita**|Požadováno – identita, která se použije k provedení této akce. Může to být jedna z následujících hodnot:<ul><li><code>userFromRequest</code>-Je platná pouze s [triggerem protokolu HTTP]. Používá identitu volajícího uživatele.</li><li><code>userFromId</code>– Používá identitu dříve přihlášeného uživatele se zadaným ID. Podívejte se na <code>userId</code> vlastnost.</li><li><code>userFromToken</code>– Používá identitu představovanou zadaným tokenem. Podívejte se na <code>userToken</code> vlastnost.</li><li><code>clientCredentials</code>– Používá identitu aplikace Function App.</li></ul>|
|**UserId** |**userId** |Nutné pouze v případě, že je _Identita_ nastavena na `userFromId` . ID instančního uživatele přidruženého k dříve přihlášenému uživateli|
|**userToken**|**UserToken**|Nutné pouze v případě, že je _Identita_ nastavena na `userFromToken` . Token platný pro aplikaci Function App |
|**dílčí**|**Cesta**|Požadováno – cesta k souboru na OneDrivu|

<a name="onedrive-output-code"></a>
#### <a name="file-output---usage"></a>Výstup souboru – využití

Tato vazba vyžaduje následující oprávnění služby Azure AD:

|Prostředek|Oprávnění|
|--------|--------|
|Microsoft Graph|Úplný přístup k uživatelským souborům|

Vazba zpřístupňuje následující typy pro funkce .NET:
- Byte []
- Stream
- řetězec
- Microsoft. Graph. DriveItem





<a name="outlook-output"></a>
## <a name="outlook-output"></a>Výstup aplikace Outlook

Výstupní vazba zprávy Outlooku pošle e-mailovou zprávu přes Outlook.

Tato část obsahuje následující pododdíly:

* [Příklad](#outlook-output---example)
* [Atributy](#outlook-output---attributes)
* [Konfigurace](#outlook-output---configuration)
* [Použití](#outlook-output---usage)

### <a name="outlook-output---example"></a>Výstup z Outlooku – příklad

Podívejte se na příklad konkrétního jazyka:

* [Skript jazyka C# (.csx)](#outlook-output---c-script-example)
* [JavaScript](#outlook-output---javascript-example)

#### <a name="outlook-output---c-script-example"></a>Výstup Outlooku – příklad skriptu C#

V následujícím příkladu se pošle e-mail přes Outlook.

*function.jsv* souboru definuje aktivační událost HTTP pomocí výstupní vazby zprávy Outlooku:

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

Kód skriptu jazyka C# pošle poštu od volajícího k příjemci zadaném v řetězci dotazu:

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

#### <a name="outlook-output---javascript-example"></a>Výstup z Outlooku – příklad JavaScriptu

V následujícím příkladu se pošle e-mail přes Outlook.

*function.jsv* souboru definuje aktivační událost HTTP pomocí výstupní vazby zprávy Outlooku:

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

Kód JavaScriptu pošle poštu od volajícího k příjemci zadaném v řetězci dotazu:

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

### <a name="outlook-output---attributes"></a>Výstupní atributy Outlooku

V [knihovnách tříd jazyka C#](functions-dotnet-class-library.md)použijte atribut [Outlook](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OutlookAttribute.cs) .

### <a name="outlook-output---configuration"></a>Výstup aplikace Outlook – konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.jspro* soubor a `Outlook` atribut.

|function.jsvlastnost | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**Jméno**| Není k dispozici |Požadováno – název proměnné použitý v kódu funkce pro e-mailovou zprávu. Viz [použití výstupní vazby zprávy Outlooku z kódu](#outlook-output-code).|
|**textový**| Není k dispozici |Požadováno – musí být nastavené na `outlook` .|
|**směr**| Není k dispozici |Požadováno – musí být nastavené na `out` .|
|**odcizen**|**Identita**|Požadováno – identita, která se použije k provedení této akce. Může to být jedna z následujících hodnot:<ul><li><code>userFromRequest</code>-Je platná pouze s [triggerem protokolu HTTP]. Používá identitu volajícího uživatele.</li><li><code>userFromId</code>– Používá identitu dříve přihlášeného uživatele se zadaným ID. Podívejte se na <code>userId</code> vlastnost.</li><li><code>userFromToken</code>– Používá identitu představovanou zadaným tokenem. Podívejte se na <code>userToken</code> vlastnost.</li><li><code>clientCredentials</code>– Používá identitu aplikace Function App.</li></ul>|
|**userId**|**UserId**  |Nutné pouze v případě, že je _Identita_ nastavena na `userFromId` . ID instančního uživatele přidruženého k dříve přihlášenému uživateli|
|**userToken**|**UserToken**|Nutné pouze v případě, že je _Identita_ nastavena na `userFromToken` . Token platný pro aplikaci Function App |

<a name="outlook-output-code"></a>
### <a name="outlook-output---usage"></a>Výstup aplikace Outlook – využití

Tato vazba vyžaduje následující oprávnění služby Azure AD:

|Prostředek|Oprávnění|
|--------|--------|
|Microsoft Graph|Odeslat e-mail jako uživatel|

Vazba zpřístupňuje následující typy pro funkce .NET:
- Microsoft. Graph. Message
- Newtonsoft.Js. LINQ. JObject
- řetězec
- Vlastní typy objektů (pomocí vazby strukturálního modelu)






## <a name="webhooks"></a>Webhooky

Webhooky umožňují reagovat na události v Microsoft Graph. Pro podporu webhooků jsou funkce potřeba k vytváření, aktualizaci a reakci na _odběry Webhooku_. Kompletní řešení Webhooku vyžaduje kombinaci následujících vazeb:
- [Aktivační událost webhooku Microsoft Graph](#webhook-trigger) umožňuje reagovat na příchozí Webhook.
- [Vstupní vazba předplatného webhooku Microsoft Graph](#webhook-input) umožňuje zobrazit seznam existujících předplatných a volitelně je aktualizovat.
- [Výstupní vazba předplatného webhooku Microsoft Graph](#webhook-output) umožňuje vytvořit nebo odstranit odběry webhooků.

Samotné vazby nevyžadují žádná oprávnění Azure AD, ale musíte požádat o oprávnění, která jsou relevantní pro typ prostředku, na který chcete reagovat. Seznam oprávnění, která jsou potřebná pro jednotlivé typy prostředků, najdete v tématu [oprávnění k předplatnému](/graph/api/subscription-post-subscriptions?view=graph-rest-1.0).

Další informace o webhookech najdete v tématu [práce s Webhooky v Microsoft Graph].





## <a name="webhook-trigger"></a>Trigger Webhooku

Trigger Microsoft Graph Webhooku umožňuje funkci reagovat na příchozí Webhook z Microsoft Graph. Každá instance této aktivační události může reagovat na jeden typ prostředku služby Microsoft Graph.

Tato část obsahuje následující pododdíly:

* [Příklad](#webhook-trigger---example)
* [Atributy](#webhook-trigger---attributes)
* [Konfigurace](#webhook-trigger---configuration)
* [Použití](#webhook-trigger---usage)

### <a name="webhook-trigger---example"></a>Trigger Webhooku – příklad

Podívejte se na příklad konkrétního jazyka:

* [Skript jazyka C# (.csx)](#webhook-trigger---c-script-example)
* [JavaScript](#webhook-trigger---javascript-example)

#### <a name="webhook-trigger---c-script-example"></a>Trigger Webhooku – příklad skriptu C#

Následující příklad zpracovává Webhooky pro příchozí zprávy Outlooku. Pokud chcete použít Trigger Webhooku, můžete [Vytvořit předplatné](#webhook-output---example)a [předplatné aktualizovat](#webhook-subscription-refresh) , abyste zabránili jeho vypršení platnosti.

*function.jsv* souboru definuje Trigger Webhooku:

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

Kód skriptu jazyka C# reaguje na příchozí e-mailové zprávy a protokoluje tělo těch odesílaných příjemcem a obsahující Azure Functions v předmětu:

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

#### <a name="webhook-trigger---javascript-example"></a>Trigger Webhooku – příklad JavaScriptu

Následující příklad zpracovává Webhooky pro příchozí zprávy Outlooku. Pokud chcete použít Trigger Webhooku, můžete [Vytvořit předplatné](#webhook-output---example)a [předplatné aktualizovat](#webhook-subscription-refresh) , abyste zabránili jeho vypršení platnosti.

*function.jsv* souboru definuje Trigger Webhooku:

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

Kód JavaScriptu reaguje na příchozí e-mailové zprávy a protokoluje tělo odesílatelů odesílaných příjemcem, který obsahuje text "Azure Functions" v předmětu:

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

### <a name="webhook-trigger---attributes"></a>Trigger Webhooku – atributy

V [knihovnách tříd jazyka C#](functions-dotnet-class-library.md)použijte atribut [GraphWebhookTrigger](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebhookTriggerAttribute.cs) .

### <a name="webhook-trigger---configuration"></a>Trigger Webhooku – konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.jspro* soubor a `GraphWebhookTrigger` atribut.

|function.jsvlastnost | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**Jméno**| Není k dispozici |Požadováno – název proměnné použitý v kódu funkce pro e-mailovou zprávu. Viz [použití výstupní vazby zprávy Outlooku z kódu](#outlook-output-code).|
|**textový**| Není k dispozici |Požadováno – musí být nastavené na `graphWebhook` .|
|**směr**| Není k dispozici |Požadováno – musí být nastavené na `trigger` .|
|**resourceType**|**ResourceType**|Požadováno – prostředek grafu, pro který by měla tato funkce reagovat na Webhooky. Může to být jedna z následujících hodnot:<ul><li><code>#Microsoft.Graph.Message</code>-změny provedené ve zprávách aplikace Outlook.</li><li><code>#Microsoft.Graph.DriveItem</code>-změny provedené na kořenových položkách OneDrivu.</li><li><code>#Microsoft.Graph.Contact</code>-změny provedené u osobních kontaktů v aplikaci Outlook.</li><li><code>#Microsoft.Graph.Event</code>-změny provedené v položkách kalendáře aplikace Outlook.</li></ul>|

> [!Note]
> Aplikace Function App může mít jenom jednu funkci, která je zaregistrovaná na danou `resourceType` hodnotu.

### <a name="webhook-trigger---usage"></a>Trigger Webhooku – použití

Vazba zpřístupňuje následující typy pro funkce .NET:
- Microsoft Graph typy SDK relevantní pro typ prostředku, například `Microsoft.Graph.Message` nebo `Microsoft.Graph.DriveItem` .
- Vlastní typy objektů (pomocí vazby strukturálního modelu)




<a name="webhook-input"></a>
## <a name="webhook-input"></a>Vstup Webhooku

Vstupní vazba Webhooku Microsoft Graph umožňuje načíst seznam předplatných spravovaných touto aplikací Function App. Vazba se přečte z App App Storage, takže neodráží jiná předplatná vytvořená mimo aplikaci.

Tato část obsahuje následující pododdíly:

* [Příklad](#webhook-input---example)
* [Atributy](#webhook-input---attributes)
* [Konfigurace](#webhook-input---configuration)
* [Použití](#webhook-input---usage)

### <a name="webhook-input---example"></a>Vstup Webhooku – příklad

Podívejte se na příklad konkrétního jazyka:

* [Skript jazyka C# (.csx)](#webhook-input---c-script-example)
* [JavaScript](#webhook-input---javascript-example)

#### <a name="webhook-input---c-script-example"></a>Vstup Webhooku – příklad skriptu C#

Následující příklad načte všechna předplatná pro volajícího uživatele a odstraní je.

*function.jsv* souboru definuje aktivační událost http se vstupní vazbou předplatného a výstupní vazbou předplatného, které používá akci odstranit:

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

Kód skriptu jazyka C# získá odběry a odstraní je:

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

#### <a name="webhook-input---javascript-example"></a>Vstup Webhooku – příklad JavaScriptu

Následující příklad načte všechna předplatná pro volajícího uživatele a odstraní je.

*function.jsv* souboru definuje aktivační událost http se vstupní vazbou předplatného a výstupní vazbou předplatného, které používá akci odstranit:

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

Kód jazyka JavaScript získá odběry a odstraní je:

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

### <a name="webhook-input---attributes"></a>Vstupní atributy Webhooku

V [knihovnách tříd jazyka C#](functions-dotnet-class-library.md)použijte atribut [GraphWebhookSubscription](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebhookSubscriptionAttribute.cs) .

### <a name="webhook-input---configuration"></a>Vstup Webhooku – konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.jspro* soubor a `GraphWebhookSubscription` atribut.

|function.jsvlastnost | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**Jméno**| Není k dispozici |Požadováno – název proměnné použitý v kódu funkce pro e-mailovou zprávu. Viz [použití výstupní vazby zprávy Outlooku z kódu](#outlook-output-code).|
|**textový**| Není k dispozici |Požadováno – musí být nastavené na `graphWebhookSubscription` .|
|**směr**| Není k dispozici |Požadováno – musí být nastavené na `in` .|
|**filtrovací**|**Filtr**| Pokud je nastaveno na `userFromRequest` , vazba bude načítat pouze odběry vlastněné volajícím uživatelem (platné pouze s [triggerem http]).| 

### <a name="webhook-input---usage"></a>Vstup Webhooku – použití

Vazba zpřístupňuje následující typy pro funkce .NET:
- řetězec []
- Vlastní pole typu objektu
- Newtonsoft.Js. LINQ. JObject []
- Microsoft. Graph. Subscription []





## <a name="webhook-output"></a>Výstup Webhooku

Výstupní vazba předplatného Webhooku umožňuje vytvořit, odstranit a aktualizovat odběry Webhooku v Microsoft Graph.

Tato část obsahuje následující pododdíly:

* [Příklad](#webhook-output---example)
* [Atributy](#webhook-output---attributes)
* [Konfigurace](#webhook-output---configuration)
* [Použití](#webhook-output---usage)

### <a name="webhook-output---example"></a>Výstup Webhooku – příklad

Podívejte se na příklad konkrétního jazyka:

* [Skript jazyka C# (.csx)](#webhook-output---c-script-example)
* [JavaScript](#webhook-output---javascript-example)

#### <a name="webhook-output---c-script-example"></a>Výstup Webhooku – příklad skriptu C#

V následujícím příkladu se vytvoří předplatné. Předplatné můžete [aktualizovat](#webhook-subscription-refresh) , abyste zabránili jeho vypršení platnosti.

*function.jsv* souboru definuje aktivační událost http s výstupní vazbou předplatného pomocí akce vytvořit:

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

Kód skriptu jazyka C# zaregistruje Webhook, který upozorní tuto aplikaci funkcí, když volající uživatel obdrží zprávu Outlooku:

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

#### <a name="webhook-output---javascript-example"></a>Výstup Webhooku – příklad JavaScriptu

V následujícím příkladu se vytvoří předplatné. Předplatné můžete [aktualizovat](#webhook-subscription-refresh) , abyste zabránili jeho vypršení platnosti.

*function.jsv* souboru definuje aktivační událost http s výstupní vazbou předplatného pomocí akce vytvořit:

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

Kód JavaScriptu zaregistruje Webhook, který upozorní tuto aplikaci funkcí, když volající uživatel obdrží zprávu Outlooku:

```js
const uuidv4 = require('uuid/v4');

module.exports = function (context, req) {
    context.bindings.clientState = uuidv4();
    context.done();
};
```

### <a name="webhook-output---attributes"></a>Webhook – atributy výstupu

V [knihovnách tříd jazyka C#](functions-dotnet-class-library.md)použijte atribut [GraphWebhookSubscription](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebhookSubscriptionAttribute.cs) .

### <a name="webhook-output---configuration"></a>Výstup Webhooku – konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.jspro* soubor a `GraphWebhookSubscription` atribut.

|function.jsvlastnost | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**Jméno**| Není k dispozici |Požadováno – název proměnné použitý v kódu funkce pro e-mailovou zprávu. Viz [použití výstupní vazby zprávy Outlooku z kódu](#outlook-output-code).|
|**textový**| Není k dispozici |Požadováno – musí být nastavené na `graphWebhookSubscription` .|
|**směr**| Není k dispozici |Požadováno – musí být nastavené na `out` .|
|**odcizen**|**Identita**|Požadováno – identita, která se použije k provedení této akce. Může to být jedna z následujících hodnot:<ul><li><code>userFromRequest</code>-Je platná pouze s [triggerem protokolu HTTP]. Používá identitu volajícího uživatele.</li><li><code>userFromId</code>– Používá identitu dříve přihlášeného uživatele se zadaným ID. Podívejte se na <code>userId</code> vlastnost.</li><li><code>userFromToken</code>– Používá identitu představovanou zadaným tokenem. Podívejte se na <code>userToken</code> vlastnost.</li><li><code>clientCredentials</code>– Používá identitu aplikace Function App.</li></ul>|
|**userId**|**UserId**  |Nutné pouze v případě, že je _Identita_ nastavena na `userFromId` . ID instančního uživatele přidruženého k dříve přihlášenému uživateli|
|**userToken**|**UserToken**|Nutné pouze v případě, že je _Identita_ nastavena na `userFromToken` . Token platný pro aplikaci Function App |
|**kroky**|**Akce**|Required – Určuje akci, kterou má vazba provádět. Může to být jedna z následujících hodnot:<ul><li><code>create</code>– Registruje nové předplatné.</li><li><code>delete</code>– Odstraní zadané předplatné.</li><li><code>refresh</code>-Aktualizuje zadaný odběr, aby se zajistilo jeho vypršení platnosti.</li></ul>|
|**subscriptionResource**|**SubscriptionResource**|Nutné pouze v případě, že je _Akce_ nastavena na `create` . Určuje Microsoft Graph prostředek, který bude monitorován pro změny. Viz [práce s Webhooky v Microsoft Graph]. |
|**changeType**|**ChangeType**|Nutné pouze v případě, že je _Akce_ nastavena na `create` . Určuje typ změny v odebíraném prostředku, který vyvolá oznámení. Podporované hodnoty jsou: `created` , `updated` , `deleted` . Pomocí čárkami odděleného seznamu lze kombinovat více hodnot.|

### <a name="webhook-output---usage"></a>Výstup Webhooku – využití

Vazba zpřístupňuje následující typy pro funkce .NET:
- řetězec
- Microsoft. Graph. Subscription




<a name="webhook-examples"></a>
## <a name="webhook-subscription-refresh"></a>Aktualizace předplatného Webhooku

Existují dva přístupy k aktualizaci předplatných:

- Pomocí identity aplikace se zabývat všemi předplatnými. To bude vyžadovat souhlas od správce Azure Active Directory. To může používat všechny jazyky, které podporuje Azure Functions.
- Použijte identitu přidruženou ke každému předplatnému, a to ruční vazbou každého ID uživatele. K provedení vazby bude nutné použít nějaký vlastní kód. To může být použito pouze funkcemi .NET.

Tato část obsahuje příklad pro každý z těchto přístupů:

* [Příklad identity aplikace](#webhook-subscription-refresh---app-identity-example)
* [Příklad identity uživatele](#webhook-subscription-refresh---user-identity-example)

### <a name="webhook-subscription-refresh---app-identity-example"></a>Aktualizace předplatného Webhooku – příklad identity aplikace

Podívejte se na příklad konkrétního jazyka:

* [Skript jazyka C# (.csx)](#app-identity-refresh---c-script-example)
* JavaScript

### <a name="app-identity-refresh---c-script-example"></a>Aktualizace identity aplikace – příklad skriptu C#

Následující příklad používá identitu aplikace k aktualizaci předplatného.

*function.jsv* definuje aktivační událost časovače se vstupní vazbou předplatného a výstupní vazbou předplatného:

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

Kód skriptu jazyka C# aktualizuje odběry:

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

### <a name="app-identity-refresh---c-script-example"></a>Aktualizace identity aplikace – příklad skriptu C#

Následující příklad používá identitu aplikace k aktualizaci předplatného.

*function.jsv* definuje aktivační událost časovače se vstupní vazbou předplatného a výstupní vazbou předplatného:

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

Kód JavaScriptu aktualizuje odběry:

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

### <a name="webhook-subscription-refresh---user-identity-example"></a>Příklad aktualizace předplatného Webhooku – identita uživatele

Následující příklad používá identitu uživatele k aktualizaci předplatného.

*function.jsv* souboru definuje aktivační událost časovače a odloží vstupní vazbu předplatného na kód funkce:

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

Kód skriptu jazyka C# aktualizuje odběry a vytvoří výstupní vazbu v kódu pomocí identity každého uživatele:

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
> [Další informace o aktivačních událostech a vazbách Azure Functions](functions-triggers-bindings.md)

[Trigger HTTP]: functions-bindings-http-webhook.md
[Práce s Webhooky v Microsoft Graph]: https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/webhooks
