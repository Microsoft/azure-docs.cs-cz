---
title: Vazeb Microsoft Graphu, Azure functions
description: Naučte se používat Microsoft Graphu triggerů a vazeb ve službě Azure Functions.
services: functions
author: craigshoemaker
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/20/2017
ms.author: cshoe
ms.openlocfilehash: eb05d1b28b80cb7bf48bc1160c251936423763e6
ms.sourcegitcommit: 39397603c8534d3d0623ae4efbeca153df8ed791
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56100778"
---
# <a name="microsoft-graph-bindings-for-azure-functions"></a>Vazeb Microsoft Graphu, Azure functions

Tento článek vysvětluje postup konfigurace a práce s Microsoft Graphem triggerů a vazeb ve službě Azure Functions. Pomocí těchto, můžete použít pro práci s daty, insights a události z Azure Functions [Microsoft Graphu](https://developer.microsoft.com/graph).

Rozšíření Microsoft Graph poskytuje následující vazby:
- [Ověřovací token vstupní vazby](#token-input) umožňuje pracovat s jakékoli rozhraní Microsoft Graph API.
- [Excelovou tabulku vstupní vazby](#excel-input) umožňuje číst data z Excelu.
- [Excelové tabulce Výstupní vazby](#excel-output) umožňuje upravovat data aplikace Excel.
- A [soubor na Onedrivu vstupní vazby](#onedrive-input) umožňuje čtení souborů z Onedrivu.
- A [soubor na Onedrivu výstupní vazby](#onedrive-output) umožňuje zápis do souborů ve Onedrivu.
- [Zprávy Outlooku výstupní vazby](#outlook-output) umožňuje odeslat e-mailu prostřednictvím Outlooku.
- Kolekce [triggerů webhooků Microsoft Graph a vazby](#webhooks) umožňuje reagovat na události z Microsoft Graphu.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!Note]
> Vazeb Microsoft Graphu jsou aktuálně ve verzi preview pro Azure Functions verze 2.x. Nejsou podporované ve verzi Functions 1.x.

## <a name="packages"></a>Balíčky

Ověření tokenu Vstupní vazba je součástí [Microsoft.Azure.WebJobs.Extensions.AuthTokens](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.AuthTokens/) balíček NuGet. Jsou k dispozici v jiných vazeb Microsoft Graphu [Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph/) balíčku. Zdrojový kód pro balíčky se [azure-functions-microsoftgraph-extension](https://github.com/Azure/azure-functions-microsoftgraph-extension/) úložiště GitHub.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="setting-up-the-extensions"></a>Nastavení rozšíření

Jsou dostupné prostřednictvím Microsoft Graphu vazby _vazby rozšíření_. Rozšíření vazby jsou volitelné součásti modulu runtime Azure Functions. Tato část ukazuje, jak nastavit rozšíření ověřování tokenu a Microsoft Graph.

### <a name="enabling-functions-20-preview"></a>Povolení funkce 2.0 preview

Rozšíření vazby jsou k dispozici pouze pro Azure Functions 2.0 ve verzi preview. 

Informace o tom, jak nastavit aplikaci function app používat verzi preview 2.0 modul runtime služby Functions najdete v tématu [způsobu cílení verze modulu runtime Azure Functions](set-runtime-version.md).

### <a name="installing-the-extension"></a>Instalace rozšíření

Chcete-li nainstalovat rozšíření z webu Azure portal, přejděte na šablonu nebo vazby, která na něj odkazuje. Vytvoření nové funkce a v obrazovce pro výběr v šabloně, zvolte scénář "Microsoft Graphu". V tomto scénáři vyberte některou ze šablon. Alternativně můžete přejít na kartu "Integrace" existující funkce a vyberte jednu z vazby popsaná v tomto článku.

V obou případech se zobrazí upozornění, která určuje příponu k instalaci. Klikněte na tlačítko **nainstalovat** získat rozšíření. Každé rozšíření musí jen se dá nainstalovat jednou za aplikace function app. 

> [!Note] 
> Proces instalace na portálu může trvat až 10 minut, než v plánu consumption.

Pokud používáte Visual Studio, můžete získat rozšíření nainstalováním [balíčky NuGet, které jsou uvedeny dříve v tomto článku](#packages).

### <a name="configuring-authentication--authorization"></a>Konfigurace ověřování / autorizace

Vazby uvedených v tomto článku vyžadují identitou, který se má použít. To umožňuje Microsoft Graphu vynutit oprávnění a auditovat jejich interakce. Identita může být uživatele, kteří používají vaše aplikace nebo vlastní aplikace. Pokud chcete nakonfigurovat tuto identitu, nastavení [ověřování pomocí služby App Service / autorizace](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization) službou Azure Active Directory. Je také potřeba žádná oprávnění, které vyžadují vaše funkce.

> [!Note] 
> Rozšíření Microsoft Graphu podporuje jenom ověřování Azure AD. Uživatelé budou muset přihlásit pomocí pracovního nebo školního účtu.

Pokud používáte na webu Azure portal, zobrazí se vám upozornění níže výzvu k instalaci rozšíření. Toto upozornění zobrazí výzvu ke konfiguraci ověřování pomocí služby App Service / autorizace a žádost o oprávnění na šablonu nebo vazba vyžaduje. Klikněte na tlačítko **konfiguraci Azure AD teď** nebo **přidat oprávnění** podle potřeby.



<a name="token-input"></a>
## <a name="auth-token"></a>Ověřovací token

Ověření tokenu Vstupní vazba získá token Azure AD pro daný prostředek a poskytuje kódu jako řetězec. Prostředek může být pro který má oprávnění aplikace. 

Tato část obsahuje následující témata:

* [Příklad](#auth-token---example)
* [Atributy](#auth-token---attributes)
* [Konfigurace](#auth-token---configuration)
* [Použití](#auth-token---usage)

### <a name="auth-token---example"></a>Ověřovací token – příklad

Podívejte se na příklad specifické pro jazyk:

* [C# skript (.csx)](#auth-token---c-script-example)
* [JavaScript](#auth-token---javascript-example)

#### <a name="auth-token---c-script-example"></a>Ověřovací token – příklad skriptu jazyka C#

Následující příklad získá informace o uživatelském profilu.

*Function.json* soubor definuje aktivační událost HTTP s využitím token vstupní vazby:

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

Kód skriptu jazyka C# na základě tokenu pro volání HTTP pro Microsoft Graph a vrátí výsledek:

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

#### <a name="auth-token---javascript-example"></a>Ověřovací token – příklad v jazyce JavaScript

Následující příklad získá informace o uživatelském profilu.

*Function.json* soubor definuje aktivační událost HTTP s využitím token vstupní vazby:

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

Kód jazyka JavaScript na základě tokenu pro volání HTTP pro Microsoft Graph a vrátí výsledek.

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

V [knihoven tříd C#](functions-dotnet-class-library.md), použijte [Token](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/TokenBinding/TokenAttribute.cs) atribut.

### <a name="auth-token---configuration"></a>Ověřovací token – konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru a `Token` atribut.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**Jméno**||Požadovaná: název této proměnné v kódu funkce pro ověřovací token. Zobrazit [používá token pro ověření vstupní vazby z kódu](#token-input-code).|
|**type**||Povinné – musí být nastavena na `token`.|
|**direction**||Povinné – musí být nastavena na `in`.|
|**Identita**|**Identita**|Požadovaná: Identita, která se použije k provedení akce. Může být jedna z následujících hodnot:<ul><li><code>userFromRequest</code> -Jediné platné s [HTTP trigger]. Použije identitu volajícího uživatele.</li><li><code>userFromId</code> -Použije identitu dříve přihlášeného uživatele se zadaným ID. Zobrazit <code>userId</code> vlastnost.</li><li><code>userFromToken</code> -Použije identitu reprezentována zadaného tokenu. Zobrazit <code>userToken</code> vlastnost.</li><li><code>clientCredentials</code> -Použije identitu aplikace function App.</li></ul>|
|**userId**|**UserId**  |Potřebné pokud a pouze v případě _identity_ je nastavena na `userFromId`. ID objektu zabezpečení uživatele přidružené k dříve přihlášenému uživateli.|
|**userToken**|**UserToken**|Potřebné pokud a pouze v případě _identity_ je nastavena na `userFromToken`. Token platný pro danou aplikaci funkcí. |
|**Prostředek**|**resource**|Povinné – adresy URL prostředků Azure AD, pro který je žádá token.|

<a name="token-input-code"></a>
### <a name="auth-token---usage"></a>Ověřovací token - využití

Vazba samotné nevyžaduje žádná oprávnění Azure AD, ale v závislosti na tom, jak se používá token, budete muset požádat o další oprávnění. Zkontrolujte požadavky na prostředku, který chcete s tímto tokenem přístupu.

Token se vždy zobrazí kód jako řetězec.

> [!Note]
> Při vývoji místně s jedním z `userFromId`, `userFromToken` nebo `userFromRequest` možností, může být požadovaný token [získali ručně](https://github.com/Azure/azure-functions-microsoftgraph-extension/issues/54#issuecomment-392865857) a zadané v poli `X-MS-TOKEN-AAD-ID-TOKEN` hlavičku požadavku z volání klientské aplikace.


<a name="excel-input"></a>
## <a name="excel-input"></a>Vstupní aplikace Excel

Vstupní vazby tabulky aplikace Excel přečte obsah Excelové tabulky uložené na OneDrive.

Tato část obsahuje následující témata:

* [Příklad](#excel-input---example)
* [Atributy](#excel-input---attributes)
* [Konfigurace](#excel-input---configuration)
* [Použití](#excel-input---usage)

### <a name="excel-input---example"></a>Aplikace Excel (vstup) – příklad

Podívejte se na příklad specifické pro jazyk:

* [C# skript (.csx)](#excel-input---c-script-example)
* [JavaScript](#excel-input---javascript-example)

#### <a name="excel-input---c-script-example"></a>Vstup - Excelový ukázkový skript jazyka C#

Následující *function.json* soubor definuje aktivační událost HTTP s využitím vstupní vazby Excelu:

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

Následující kód skriptu jazyka C# načte obsah zadané tabulky a vrátí uživateli:

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

#### <a name="excel-input---javascript-example"></a>Excel vstupní – příklad v jazyce JavaScript

Následující *function.json* soubor definuje aktivační událost HTTP s využitím vstupní vazby Excelu:

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

Následující kód jazyka JavaScript načte obsah zadané tabulky a vrátí uživateli.

```js
module.exports = function (context, req) {
    context.res = {
        body: context.bindings.excelTableData
    };
    context.done();
};
```

### <a name="excel-input---attributes"></a>Excel vstupní – atributy

V [knihoven tříd C#](functions-dotnet-class-library.md), použijte [Excel](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/ExcelAttribute.cs) atribut.

### <a name="excel-input---configuration"></a>Aplikace Excel (vstup) – konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru a `Excel` atribut.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**Jméno**||Požadovaná: název této proměnné v kódu funkce pro Excelové tabulky. Zobrazit [pomocí Excelové tabulky. vstupní vazby z kódu](#excel-input-code).|
|**type**||Povinné – musí být nastavena na `excel`.|
|**direction**||Povinné – musí být nastavena na `in`.|
|**Identita**|**Identita**|Požadovaná: Identita, která se použije k provedení akce. Může být jedna z následujících hodnot:<ul><li><code>userFromRequest</code> -Jediné platné s [HTTP trigger]. Použije identitu volajícího uživatele.</li><li><code>userFromId</code> -Použije identitu dříve přihlášeného uživatele se zadaným ID. Zobrazit <code>userId</code> vlastnost.</li><li><code>userFromToken</code> -Použije identitu reprezentována zadaného tokenu. Zobrazit <code>userToken</code> vlastnost.</li><li><code>clientCredentials</code> -Použije identitu aplikace function App.</li></ul>|
|**userId**|**UserId**  |Potřebné pokud a pouze v případě _identity_ je nastavena na `userFromId`. ID objektu zabezpečení uživatele přidružené k dříve přihlášenému uživateli.|
|**userToken**|**UserToken**|Potřebné pokud a pouze v případě _identity_ je nastavena na `userFromToken`. Token platný pro danou aplikaci funkcí. |
|**Cesta**|**Cesta**|Požadovaná: cesta k Excelovému sešitu ve Onedrivu.|
|**worksheetName**|**WorksheetName**|List, ve kterém se nachází tabulka|
|**tableName**|**TableName**|Název tabulky. Pokud není zadán, použije se obsah listu.|

<a name="excel-input-code"></a>
### <a name="excel-input---usage"></a>Excel vstup - využití

Tato vazba vyžaduje následující oprávnění Azure AD:
|Prostředek|Oprávnění|
|--------|--------|
|Microsoft Graph|Umožňuje získat oprávnění ke čtení souborů uživatelů.|

Vazba zveřejňuje následující typy funkcí .NET:
- [String []]
- Microsoft.Graph.WorkbookTable
- Vlastní objekt typů (pomocí vazby modelu struktury)










<a name="excel-output"></a>
## <a name="excel-output"></a>Výstup aplikace Excel

V aplikaci Excel výstupní vazbu upraví obsah Excelové tabulky uložené na OneDrive.

Tato část obsahuje následující témata:

* [Příklad](#excel-output---example)
* [Atributy](#excel-output---attributes)
* [Konfigurace](#excel-output---configuration)
* [Použití](#excel-output---usage)

### <a name="excel-output---example"></a>Aplikace Excel výstup – příklad

Podívejte se na příklad specifické pro jazyk:

* [C# skript (.csx)](#excel-output---c-script-example)
* [JavaScript](#excel-output---javascript-example)

#### <a name="excel-output---c-script-example"></a>Výstup – Excelový ukázkový skript jazyka C#

Následující příklad přidá řádků do Excelové tabulky.

*Function.json* soubor definuje aktivační událost HTTP pomocí aplikace Excel výstupní vazby:

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

Kód skriptu jazyka C# přidá nový řádek do tabulky (předpokládá se, že jedním sloupcem) na základě vstupu z řetězce dotazu:

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

#### <a name="excel-output---javascript-example"></a>Excel výstup – příklad v jazyce JavaScript

Následující příklad přidá řádků do Excelové tabulky.

*Function.json* soubor definuje aktivační událost HTTP pomocí aplikace Excel výstupní vazby:

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

Následující kód jazyka JavaScript přidá nový řádek do tabulky (předpokládá se, že jedním sloupcem) na základě vstupu z řetězce dotazu.

```js
module.exports = function (context, req) {
    context.bindings.newExcelRow = {
        text: req.query.text
        // Add other properties for additional columns here
    }
    context.done();
};
```

### <a name="excel-output---attributes"></a>Excel výstup – atributy

V [knihoven tříd C#](functions-dotnet-class-library.md), použijte [Excel](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/ExcelAttribute.cs) atribut.

### <a name="excel-output---configuration"></a>Aplikace Excel výstup – konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru a `Excel` atribut.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**Jméno**||Požadovaná: název této proměnné v kódu funkce pro ověřovací token. Zobrazit [pomocí Excelové tabulky Výstupní vazbu z kódu](#excel-output-code).|
|**type**||Povinné – musí být nastavena na `excel`.|
|**direction**||Povinné – musí být nastavena na `out`.|
|**Identita**|**Identita**|Požadovaná: Identita, která se použije k provedení akce. Může být jedna z následujících hodnot:<ul><li><code>userFromRequest</code> -Jediné platné s [HTTP trigger]. Použije identitu volajícího uživatele.</li><li><code>userFromId</code> -Použije identitu dříve přihlášeného uživatele se zadaným ID. Zobrazit <code>userId</code> vlastnost.</li><li><code>userFromToken</code> -Použije identitu reprezentována zadaného tokenu. Zobrazit <code>userToken</code> vlastnost.</li><li><code>clientCredentials</code> -Použije identitu aplikace function App.</li></ul>|
|**UserId** |**userId** |Potřebné pokud a pouze v případě _identity_ je nastavena na `userFromId`. ID objektu zabezpečení uživatele přidružené k dříve přihlášenému uživateli.|
|**userToken**|**UserToken**|Potřebné pokud a pouze v případě _identity_ je nastavena na `userFromToken`. Token platný pro danou aplikaci funkcí. |
|**Cesta**|**Cesta**|Požadovaná: cesta k Excelovému sešitu ve Onedrivu.|
|**worksheetName**|**WorksheetName**|List, ve kterém se nachází tabulka|
|**tableName**|**TableName**|Název tabulky. Pokud není zadán, použije se obsah listu.|
|**updateType**|**UpdateType**|Požadovaná: typ změny, která se provede v tabulce. Může být jedna z následujících hodnot:<ul><li><code>update</code> -Nahradí obsah tabulky na Onedrivu.</li><li><code>append</code> -Přidá datová část na konec tabulky na Onedrivu vytvořením nových řádků.</li></ul>|

<a name="excel-output-code"></a>
### <a name="excel-output---usage"></a>Výstup – Excel využití

Tato vazba vyžaduje následující oprávnění Azure AD:
|Prostředek|Oprávnění|
|--------|--------|
|Microsoft Graph|Úplný přístup k souborům uživatele|

Vazba zveřejňuje následující typy funkcí .NET:
- [String []]
- Newtonsoft.Json.Linq.JObject
- Microsoft.Graph.WorkbookTable
- Vlastní objekt typů (pomocí vazby modelu struktury)





<a name="onedrive-input"></a>
## <a name="file-input"></a>Vstupního souboru

Vstupní vazba soubor na Onedrivu přečte obsah souboru uloženém na Onedrivu.

Tato část obsahuje následující témata:

* [Příklad](#file-input---example)
* [Atributy](#file-input---attributes)
* [Konfigurace](#file-input---configuration)
* [Použití](#file-input---usage)

### <a name="file-input---example"></a>Soubor (vstup) – příklad

Podívejte se na příklad specifické pro jazyk:

* [C# skript (.csx)](#file-input---c-script-example)
* [JavaScript](#file-input---javascript-example)

#### <a name="file-input---c-script-example"></a>Vstup - soubor skriptu příklad jazyka C#

Následující příklad načte soubor, který je uložený na Onedrivu.

*Function.json* soubor definuje aktivační událost HTTP s využitím vstupní vazby souborů OneDrive:

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

Kód skriptu jazyka C# přečte soubor zadaný v řetězci dotazu a protokoly jeho délka:

```csharp
using System.Net;
using Microsoft.Extensions.Logging;

public static void Run(HttpRequestMessage req, Stream myOneDriveFile, ILogger log)
{
    log.LogInformation(myOneDriveFile.Length.ToString());
}
```

#### <a name="file-input---javascript-example"></a>Soubor vstupní – příklad v jazyce JavaScript

Následující příklad načte soubor, který je uložený na Onedrivu.

*Function.json* soubor definuje aktivační událost HTTP s využitím vstupní vazby souborů OneDrive:

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

Následující kód jazyka JavaScript přečte soubor zadaný v řetězci dotazu a vrátí jeho délka.

```js
module.exports = function (context, req) {
    context.res = {
        body: context.bindings.myOneDriveFile.length
    };
    context.done();
};
```

### <a name="file-input---attributes"></a>Soubor vstupní – atributy

V [knihoven tříd C#](functions-dotnet-class-library.md), použijte [Onedrivu](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OneDriveAttribute.cs) atribut.

### <a name="file-input---configuration"></a>Soubor (vstup) – konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru a `OneDrive` atribut.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**Jméno**||Požadovaná: název této proměnné v kódu funkce pro soubor. Zobrazit [pomocí souboru Onedrivu vstupní vazby z kódu](#onedrive-input-code).|
|**type**||Povinné – musí být nastavena na `onedrive`.|
|**direction**||Povinné – musí být nastavena na `in`.|
|**Identita**|**Identita**|Požadovaná: Identita, která se použije k provedení akce. Může být jedna z následujících hodnot:<ul><li><code>userFromRequest</code> -Jediné platné s [HTTP trigger]. Použije identitu volajícího uživatele.</li><li><code>userFromId</code> -Použije identitu dříve přihlášeného uživatele se zadaným ID. Zobrazit <code>userId</code> vlastnost.</li><li><code>userFromToken</code> -Použije identitu reprezentována zadaného tokenu. Zobrazit <code>userToken</code> vlastnost.</li><li><code>clientCredentials</code> -Použije identitu aplikace function App.</li></ul>|
|**userId**|**UserId**  |Potřebné pokud a pouze v případě _identity_ je nastavena na `userFromId`. ID objektu zabezpečení uživatele přidružené k dříve přihlášenému uživateli.|
|**userToken**|**UserToken**|Potřebné pokud a pouze v případě _identity_ je nastavena na `userFromToken`. Token platný pro danou aplikaci funkcí. |
|**Cesta**|**Cesta**|Požadovaná: cesta k souboru ve Onedrivu.|

<a name="onedrive-input-code"></a>
### <a name="file-input---usage"></a>Soubor vstupní – využití

Tato vazba vyžaduje následující oprávnění Azure AD:
|Prostředek|Oprávnění|
|--------|--------|
|Microsoft Graph|Umožňuje získat oprávnění ke čtení souborů uživatelů.|

Vazba zveřejňuje následující typy funkcí .NET:
- Byte
- Datový proud
- řetězec
- Microsoft.Graph.DriveItem






<a name="onedrive-output"></a>
## <a name="file-output"></a>Výstupní soubor

Soubor Onedrivu výstupní vazbu upraví obsah souboru uloženém na Onedrivu.

Tato část obsahuje následující témata:

* [Příklad](#file-output---example)
* [Atributy](#file-output---attributes)
* [Konfigurace](#file-output---configuration)
* [Použití](#file-output---usage)

### <a name="file-output---example"></a>Soubor výstup – příklad

Podívejte se na příklad specifické pro jazyk:

* [C# skript (.csx)](#file-output---c-script-example)
* [JavaScript](#file-output---javascript-example)

#### <a name="file-output---c-script-example"></a>Soubor výstupu – příklad skriptu jazyka C#

V následujícím příkladu se zapíše do souboru, který je uložený na Onedrivu.

*Function.json* soubor definuje aktivační událost HTTP s Onedrivu výstupní vazby:

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

Kód skriptu jazyka C# získá text z řetězce dotazu a zapíše jej do textového souboru (FunctionsTest.txt definované v předchozím příkladu) v kořenové složce Onedrivu volající:

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

#### <a name="file-output---javascript-example"></a>Soubor výstupu – příklad v jazyce JavaScript

V následujícím příkladu se zapíše do souboru, který je uložený na Onedrivu.

*Function.json* soubor definuje aktivační událost HTTP s Onedrivu výstupní vazby:

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

Kód jazyka JavaScript získá text z řetězce dotazu a zapisuje je do textového souboru (FunctionsTest.txt definované v konfiguraci výše) v kořenové složce Onedrivu volajícího.

```js
module.exports = function (context, req) {
    context.bindings.myOneDriveFile = req.query.text;
    context.done();
};
```

### <a name="file-output---attributes"></a>Soubor výstupu – atributy

V [knihoven tříd C#](functions-dotnet-class-library.md), použijte [Onedrivu](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OneDriveAttribute.cs) atribut.

### <a name="file-output---configuration"></a>Soubor výstup – konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru a `OneDrive` atribut.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**Jméno**||Požadovaná: název této proměnné v kódu funkce pro soubor. Zobrazit [pomocí souboru Onedrivu výstupní vazbu z kódu](#onedrive-output-code).|
|**type**||Povinné – musí být nastavena na `onedrive`.|
|**direction**||Povinné – musí být nastavena na `out`.|
|**Identita**|**Identita**|Požadovaná: Identita, která se použije k provedení akce. Může být jedna z následujících hodnot:<ul><li><code>userFromRequest</code> -Jediné platné s [HTTP trigger]. Použije identitu volajícího uživatele.</li><li><code>userFromId</code> -Použije identitu dříve přihlášeného uživatele se zadaným ID. Zobrazit <code>userId</code> vlastnost.</li><li><code>userFromToken</code> -Použije identitu reprezentována zadaného tokenu. Zobrazit <code>userToken</code> vlastnost.</li><li><code>clientCredentials</code> -Použije identitu aplikace function App.</li></ul>|
|**UserId** |**userId** |Potřebné pokud a pouze v případě _identity_ je nastavena na `userFromId`. ID objektu zabezpečení uživatele přidružené k dříve přihlášenému uživateli.|
|**userToken**|**UserToken**|Potřebné pokud a pouze v případě _identity_ je nastavena na `userFromToken`. Token platný pro danou aplikaci funkcí. |
|**Cesta**|**Cesta**|Požadovaná: cesta k souboru ve Onedrivu.|

<a name="onedrive-output-code"></a>
#### <a name="file-output---usage"></a>Soubor výstupu – využití

Tato vazba vyžaduje následující oprávnění Azure AD:
|Prostředek|Oprávnění|
|--------|--------|
|Microsoft Graph|Úplný přístup k souborům uživatele|

Vazba zveřejňuje následující typy funkcí .NET:
- Byte
- Datový proud
- řetězec
- Microsoft.Graph.DriveItem





<a name="outlook-output"></a>
## <a name="outlook-output"></a>Výstup aplikace Outlook

Zprávy Outlooku výstupní vazbu odešle e-mailovou zprávu prostřednictvím aplikace Outlook.

Tato část obsahuje následující témata:

* [Příklad](#outlook-output---example)
* [Atributy](#outlook-output---attributes)
* [Konfigurace](#outlook-output---configuration)
* [Použití](#outlook-output---usage)

### <a name="outlook-output---example"></a>Výstup aplikace Outlook – příklad

Podívejte se na příklad specifické pro jazyk:

* [C# skript (.csx)](#outlook-output---c-script-example)
* [JavaScript](#outlook-output---javascript-example)

#### <a name="outlook-output---c-script-example"></a>Aplikace Outlook výstup – příklad skriptu jazyka C#

V následujícím příkladu se odešle e-mailu prostřednictvím Outlooku.

*Function.json* soubor definuje aktivační událost HTTP s aplikace Outlook zpráva výstupní vazby:

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

Kód skriptu jazyka C# odešle e-mailu od volajícího příjemci zadaná v řetězci dotazu:

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

#### <a name="outlook-output---javascript-example"></a>Aplikace Outlook výstup – příklad v jazyce JavaScript

V následujícím příkladu se odešle e-mailu prostřednictvím Outlooku.

*Function.json* soubor definuje aktivační událost HTTP s aplikace Outlook zpráva výstupní vazby:

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

Kód jazyka JavaScript odešle e-mailu od volajícího příjemci zadaná v řetězci dotazu:

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

### <a name="outlook-output---attributes"></a>Aplikace Outlook výstup – atributy

V [knihoven tříd C#](functions-dotnet-class-library.md), použijte [Outlook](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OutlookAttribute.cs) atribut.

### <a name="outlook-output---configuration"></a>Výstup aplikace Outlook – konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru a `Outlook` atribut.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**Jméno**||Požadovaná: název této proměnné v kódu funkce pro e-mailovou zprávu. Zobrazit [pomocí zprávy Outlooku výstupní vazbu z kódu](#outlook-output-code).|
|**type**||Povinné – musí být nastavena na `outlook`.|
|**direction**||Povinné – musí být nastavena na `out`.|
|**Identita**|**Identita**|Požadovaná: Identita, která se použije k provedení akce. Může být jedna z následujících hodnot:<ul><li><code>userFromRequest</code> -Jediné platné s [HTTP trigger]. Použije identitu volajícího uživatele.</li><li><code>userFromId</code> -Použije identitu dříve přihlášeného uživatele se zadaným ID. Zobrazit <code>userId</code> vlastnost.</li><li><code>userFromToken</code> -Použije identitu reprezentována zadaného tokenu. Zobrazit <code>userToken</code> vlastnost.</li><li><code>clientCredentials</code> -Použije identitu aplikace function App.</li></ul>|
|**userId**|**UserId**  |Potřebné pokud a pouze v případě _identity_ je nastavena na `userFromId`. ID objektu zabezpečení uživatele přidružené k dříve přihlášenému uživateli.|
|**userToken**|**UserToken**|Potřebné pokud a pouze v případě _identity_ je nastavena na `userFromToken`. Token platný pro danou aplikaci funkcí. |

<a name="outlook-output-code"></a>
### <a name="outlook-output---usage"></a>Aplikace Outlook výstup – využití

Tato vazba vyžaduje následující oprávnění Azure AD:
|Prostředek|Oprávnění|
|--------|--------|
|Microsoft Graph|Odesílat poštu jménem uživatele|

Vazba zveřejňuje následující typy funkcí .NET:
- Microsoft.Graph.Message
- Newtonsoft.Json.Linq.JObject
- řetězec
- Vlastní objekt typů (pomocí vazby modelu struktury)






## <a name="webhooks"></a>Webhooky

Webhooky umožňují reagovat na události v Microsoft Graphu. Pro podporu webhooky, jsou funkce potřebné k vytvoření, aktualizace a reagovat na _odběrů webhooků_. Webhook kompletní řešení vyžaduje kombinaci následující vazby:
- A [trigger webhooku Microsoft Graph](#webhook-trigger) umožňuje reagovat na příchozí webhooku.
- A [odběr webhooku Microsoft Graph vstupní vazby](#webhook-input) umožňuje seznam stávajících předplatných a obnovit je.
- A [odběr webhooku Microsoft Graph výstupní vazby](#webhook-output) umožňuje vytvořit nebo odstranit odběry webhooků.

Vazby sami se nevyžaduje žádná oprávnění Azure AD, ale budete muset požádat o oprávnění, které jsou relevantní pro typ prostředku, který chcete reagovat na ně. Seznam, které jsou potřeba oprávnění pro jednotlivé typy prostředků najdete v tématu [oprávnění pro předplatné](https://docs.microsoft.com/graph/api/subscription-post-subscriptions?view=graph-rest-1.0).

Další informace o webhooků najdete v tématu [práce s webhooky v aplikaci Microsoft Graph].





## <a name="webhook-trigger"></a>Trigger Webhooku

Trigger webhooku Microsoft Graph umožňuje funkci reagovat na příchozí webhooku z Microsoft Graphu. Každá instance této aktivační události může reagovat na jeden typ prostředku Microsoft Graphu.

Tato část obsahuje následující témata:

* [Příklad](#webhook-trigger---example)
* [Atributy](#webhook-trigger---attributes)
* [Konfigurace](#webhook-trigger---configuration)
* [Použití](#webhook-trigger---usage)

### <a name="webhook-trigger---example"></a>Trigger Webhooku – příklad

Podívejte se na příklad specifické pro jazyk:

* [C# skript (.csx)](#webhook-trigger---c-script-example)
* [JavaScript](#webhook-trigger---javascript-example)

#### <a name="webhook-trigger---c-script-example"></a>Trigger Webhooku – příklad skriptu jazyka C#

V následujícím příkladu zpracovává webhooky pro Outlookové zprávy příchozí. Použít můžete aktivovat webhook [vytvoření odběru](#webhook-output---example), a můžete je [aktualizovat předplatné](#webhook-subscription-refresh) jak zabránit vypršení platnosti.

*Function.json* soubor definuje aktivační událost webhooku:

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

Kód skriptu jazyka C# reaguje na příchozí e-mailové zprávy a protokoly odesílaných příjemce a obsahující "Funkce Azure" v předmětu text:

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

#### <a name="webhook-trigger---javascript-example"></a>Trigger Webhooku – příklad v jazyce JavaScript

V následujícím příkladu zpracovává webhooky pro Outlookové zprávy příchozí. Použít můžete aktivovat webhook [vytvoření odběru](#webhook-output---example), a můžete je [aktualizovat předplatné](#webhook-subscription-refresh) jak zabránit vypršení platnosti.

*Function.json* soubor definuje aktivační událost webhooku:

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

Kód jazyka JavaScript reaguje na příchozí e-mailové zprávy a protokoly odesílaných příjemce a obsahující "Funkce Azure" v předmětu text:

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

V [knihoven tříd C#](functions-dotnet-class-library.md), použijte [GraphWebHookTrigger](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebHookTriggerAttribute.cs) atribut.

### <a name="webhook-trigger---configuration"></a>Trigger Webhooku – konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru a `GraphWebHookTrigger` atribut.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**Jméno**||Požadovaná: název této proměnné v kódu funkce pro e-mailovou zprávu. Zobrazit [pomocí zprávy Outlooku výstupní vazbu z kódu](#outlook-output-code).|
|**type**||Povinné – musí být nastavena na `graphWebhook`.|
|**direction**||Povinné – musí být nastavena na `trigger`.|
|**Typ prostředku**|**Typ prostředku**|Požadovaná: prostředek grafu, pro které tato funkce měla reagovat na webhooky. Může být jedna z následujících hodnot:<ul><li><code>#Microsoft.Graph.Message</code> -změny pro Outlookové zprávy.</li><li><code>#Microsoft.Graph.DriveItem</code> -změny provedené u položek kořenové OneDrive.</li><li><code>#Microsoft.Graph.Contact</code> -změny provedené v osobní kontakty v Outlooku.</li><li><code>#Microsoft.Graph.Event</code> -změny provedené u položek kalendáře aplikace Outlook.</li></ul>|

> [!Note]
> Aplikace function app může mít jenom jednu funkci, která je registrována danou `resourceType` hodnotu.

### <a name="webhook-trigger---usage"></a>Trigger Webhooku – využití

Vazba zveřejňuje následující typy funkcí .NET:
- Sada SDK Microsoft Graphu typy relevantní pro typ prostředku, jako `Microsoft.Graph.Message` nebo `Microsoft.Graph.DriveItem`.
- Vlastní objekt typů (pomocí vazby modelu struktury)




<a name="webhook-input"></a>
## <a name="webhook-input"></a>Vstup Webhooku

Vstupní vazby webhooku Microsoft Graph umožňuje načíst seznam předplatných, které spravuje této aplikace function app. Vazby načítá z úložiště aplikace funkce, takže nereflektuje další předplatná vytvořená z mimo aplikaci.

Tato část obsahuje následující témata:

* [Příklad](#webhook-input---example)
* [Atributy](#webhook-input---attributes)
* [Konfigurace](#webhook-input---configuration)
* [Použití](#webhook-input---usage)

### <a name="webhook-input---example"></a>Vstup Webhook – příklad

Podívejte se na příklad specifické pro jazyk:

* [C# skript (.csx)](#webhook-input---c-script-example)
* [JavaScript](#webhook-input---javascript-example)

#### <a name="webhook-input---c-script-example"></a>Webhook zadejte – příklad skriptu jazyka C#

Následující příklad získá všechna předplatná pro volajícího uživatele a je odstraní.

*Function.json* soubor definuje aktivační událost HTTP s využitím vstupní vazby předplatného a předplatné výstupní vazby, která používá akci odstranění:

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

Kód skriptu jazyka C# získá předplatná a jejich odstraní:

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

#### <a name="webhook-input---javascript-example"></a>Webhook zadejte – příklad v jazyce JavaScript

Následující příklad získá všechna předplatná pro volajícího uživatele a je odstraní.

*Function.json* soubor definuje aktivační událost HTTP s využitím vstupní vazby předplatného a předplatné výstupní vazby, která používá akci odstranění:

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

Kód jazyka JavaScript získá předplatná a jejich odstraní:

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

### <a name="webhook-input---attributes"></a>Webhook zadejte – atributy

V [knihoven tříd C#](functions-dotnet-class-library.md), použijte [GraphWebHookSubscription](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebHookSubscriptionAttribute.cs) atribut.

### <a name="webhook-input---configuration"></a>Webhook (vstup) – konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru a `GraphWebHookSubscription` atribut.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**Jméno**||Požadovaná: název této proměnné v kódu funkce pro e-mailovou zprávu. Zobrazit [pomocí zprávy Outlooku výstupní vazbu z kódu](#outlook-output-code).|
|**type**||Povinné – musí být nastavena na `graphWebhookSubscription`.|
|**direction**||Povinné – musí být nastavena na `in`.|
|**Filtr**|**Filtr**| Pokud hodnotu `userFromRequest`, pak vazba bude načítat jen předplatná ve vlastnictví volajícího uživatele (platné jen spolu s [HTTP trigger]).| 

### <a name="webhook-input---usage"></a>Webhook vstup - využití

Vazba zveřejňuje následující typy funkcí .NET:
- řetězec]
- Vlastní objekt typu pole
- Newtonsoft.Json.Linq.JObject[]
- Microsoft.Graph.Subscription[]





## <a name="webhook-output"></a>Výstup Webhooku

Odběr webhooku výstupní vazbu umožňuje vytvářet, odstraňovat a aktualizovat odběry webhooků v Microsoft Graphu.

Tato část obsahuje následující témata:

* [Příklad](#webhook-output---example)
* [Atributy](#webhook-output---attributes)
* [Konfigurace](#webhook-output---configuration)
* [Použití](#webhook-output---usage)

### <a name="webhook-output---example"></a>Výstup Webhook – příklad

Podívejte se na příklad specifické pro jazyk:

* [C# skript (.csx)](#webhook-output---c-script-example)
* [JavaScript](#webhook-output---javascript-example)

#### <a name="webhook-output---c-script-example"></a>Webhook výstup – příklad skriptu jazyka C#

Následující příklad vytvoří odběr. Je možné [aktualizovat předplatné](#webhook-subscription-refresh) jak zabránit vypršení platnosti.

*Function.json* soubor definuje aktivační událost HTTP s předplatné výstupní vazby pomocí akce vytvoření:

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

Kód skriptu jazyka C# registruje webhook, který tuto aplikaci function app vás upozorní, když obdrží zprávy Outlooku volajícího uživatele:

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

#### <a name="webhook-output---javascript-example"></a>Webhook výstup – příklad v jazyce JavaScript

Následující příklad vytvoří odběr. Je možné [aktualizovat předplatné](#webhook-subscription-refresh) jak zabránit vypršení platnosti.

*Function.json* soubor definuje aktivační událost HTTP s předplatné výstupní vazby pomocí akce vytvoření:

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

Kód jazyka JavaScript registruje webhook, který tuto aplikaci function app vás upozorní, když obdrží zprávy Outlooku volajícího uživatele:

```js
const uuidv4 = require('uuid/v4');

module.exports = function (context, req) {
    context.bindings.clientState = uuidv4();
    context.done();
};
```

### <a name="webhook-output---attributes"></a>Webhook výstup – atributy

V [knihoven tříd C#](functions-dotnet-class-library.md), použijte [GraphWebHookSubscription](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebHookSubscriptionAttribute.cs) atribut.

### <a name="webhook-output---configuration"></a>Výstup Webhook – konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru a `GraphWebHookSubscription` atribut.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**Jméno**||Požadovaná: název této proměnné v kódu funkce pro e-mailovou zprávu. Zobrazit [pomocí zprávy Outlooku výstupní vazbu z kódu](#outlook-output-code).|
|**type**||Povinné – musí být nastavena na `graphWebhookSubscription`.|
|**direction**||Povinné – musí být nastavena na `out`.|
|**Identita**|**Identita**|Požadovaná: Identita, která se použije k provedení akce. Může být jedna z následujících hodnot:<ul><li><code>userFromRequest</code> -Jediné platné s [HTTP trigger]. Použije identitu volajícího uživatele.</li><li><code>userFromId</code> -Použije identitu dříve přihlášeného uživatele se zadaným ID. Zobrazit <code>userId</code> vlastnost.</li><li><code>userFromToken</code> -Použije identitu reprezentována zadaného tokenu. Zobrazit <code>userToken</code> vlastnost.</li><li><code>clientCredentials</code> -Použije identitu aplikace function App.</li></ul>|
|**userId**|**UserId**  |Potřebné pokud a pouze v případě _identity_ je nastavena na `userFromId`. ID objektu zabezpečení uživatele přidružené k dříve přihlášenému uživateli.|
|**userToken**|**UserToken**|Potřebné pokud a pouze v případě _identity_ je nastavena na `userFromToken`. Token platný pro danou aplikaci funkcí. |
|**Akce**|**Akce**|Nepožadováno – Určuje, by měl provádět akce vazby. Může být jedna z následujících hodnot:<ul><li><code>create</code> -Zaregistruje nový odběr.</li><li><code>delete</code> -Odstraní zadaný odběr.</li><li><code>refresh</code> -Aktualizuje zadaný odběr zabránit vypršení platnosti.</li></ul>|
|**subscriptionResource**|**subscriptionResource**|Potřebné pokud a pouze tehdy, pokud _akce_ je nastavena na `create`. Určuje prostředek Microsoft Graphu, který bude monitorovat změny. Zobrazit [práce s webhooky v aplikaci Microsoft Graph]. |
|**changeType**|**ChangeType**|Potřebné pokud a pouze tehdy, pokud _akce_ je nastavena na `create`. Označuje typ změny v zaregistrovaném prostředku, který vyvolá oznámení. Podporované hodnoty jsou: `created`, `updated`, `deleted`. Více hodnot lze spojovat pomocí seznam oddělený čárkami.|

### <a name="webhook-output---usage"></a>Webhook výstup – využití

Vazba zveřejňuje následující typy funkcí .NET:
- řetězec
- Microsoft.Graph.Subscription




<a name="webhook-examples"></a>
## <a name="webhook-subscription-refresh"></a>Aktualizaci odběru Webhooku

Chcete-li aktualizovat předplatná dvěma způsoby:

- Pomocí identity aplikace se všechna předplatná. To bude vyžadovat souhlas ze Správce služby Azure Active Directory. To je možné Azure Functions podporuje všechny jazyky.
- Použít identitu přidruženou každé předplatné ručně vazbou každé ID uživatele. To vyžaduje vlastní kód k provedení vazby. To jde použít jenom funkce .NET.

Tato část obsahuje příklad pro každou z těchto přístupů:

* [Příklad aplikace identity](#webhook-subscription-refresh---app-identity-example)
* [Příklad identity uživatele](#webhook-subscription-refresh---user-identity-example)

### <a name="webhook-subscription-refresh---app-identity-example"></a>Odběr Webhooku aktualizace – příklad aplikace identity

Podívejte se na příklad specifické pro jazyk:

* [C# skript (.csx)](#app-identity-refresh---c-script-example)
* JavaScript

### <a name="app-identity-refresh---c-script-example"></a>Aktualizace identity aplikace – příklad skriptu jazyka C#

Identita aplikace aktualizovat předplatné v následujícím příkladu.

*Function.json* definuje časovacího triggeru s předplatným vstupní vazby a předplatné výstupní vazby:

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

Kód skriptu jazyka C# aktualizuje předplatná:

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

### <a name="app-identity-refresh---c-script-example"></a>Aktualizace identity aplikace – příklad skriptu jazyka C#

Identita aplikace aktualizovat předplatné v následujícím příkladu.

*Function.json* definuje časovacího triggeru s předplatným vstupní vazby a předplatné výstupní vazby:

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

Kód jazyka JavaScript se aktualizuje předplatná:

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

### <a name="webhook-subscription-refresh---user-identity-example"></a>Odběr Webhooku aktualizace – příklad identity uživatele

Následující příklad používá identitu uživatele se aktualizovat předplatné.

*Function.json* soubor definuje časovacího triggeru a odloží předplatné vstupní vazby za účelem kód funkce:

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

Kód skriptu jazyka C# aktualizuje předplatná a vytvoří výstupní vazby v kódu pomocí identity každého uživatele:

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

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Další informace o aktivačních událostech Azure functions a vazby](functions-triggers-bindings.md)

[HTTP trigger]: functions-bindings-http-webhook.md
[práce s webhooky v aplikaci Microsoft Graph]: https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/webhooks
