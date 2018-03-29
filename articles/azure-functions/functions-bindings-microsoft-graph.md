---
title: Microsoft Graph vazby pro Azure Functions
description: Pochopit, jak používat Microsoft Graph triggerů a vazeb v Azure Functions.
services: functions
author: mattchenderson
manager: cfowler
editor: ''
ms.service: functions
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 12/20/2017
ms.author: mahender
ms.openlocfilehash: 2de80760484ae1869b340898ea1e5f740fbc2883
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2018
---
# <a name="microsoft-graph-bindings-for-azure-functions"></a>Microsoft Graph vazby pro Azure Functions

Tento článek vysvětluje postup konfigurace a práce s Microsoft Graph triggerů a vazeb v Azure Functions. Pomocí těchto, můžete použít funkce Azure pro práci s daty, přehledy a události z [Microsoft Graph](https://graph.microsoft.io).

Rozšíření Microsoft Graph poskytuje následující vazby:
- [Token ověření vstupu vazby](#token-input) umožňuje pracovat s žádné Microsoft Graph API.
- [Tabulky aplikace Excel vstupní vazby](#excel-input) umožňuje číst data z Excelu.
- [Tabulky aplikace Excel výstup vazby](#excel-output) umožňuje upravovat data z Excelu.
- A [OneDrive souboru vstupní vazby](#onedrive-input) umožňuje číst soubory z Onedrivu.
- A [OneDrive souboru výstup vazby](#onedrive-output) umožňuje zápis do souborů na Onedrivu.
- [Zprávy Outlooku výstup vazby](#outlook-output) umožní vám odesílat e-mailu prostřednictvím aplikace Outlook.
- Kolekce [Microsoft Graph webhooku triggerů a vazeb](#webhooks) umožňuje reagování na události z Microsoft Graph.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!Note]
> Microsoft Graph vazby jsou aktuálně ve verzi preview pro Azure Functions verze 2.x. Některé funkce nejsou podporovány ve verzi funkce 1.x.

## <a name="packages"></a>Balíčky

Ověření tokenu Vstupní vazba je součástí [Microsoft.Azure.WebJobs.Extensions.AuthTokens](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.AuthTokens/) balíček NuGet. Jiné Microsoft Graph vazby jsou součástí [Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph/) balíčku. Zdrojový kód pro balíčky je v [azure funkce microsoftgraph rozšíření](https://github.com/Azure/azure-functions-microsoftgraph-extension/) úložiště GitHub.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="setting-up-the-extensions"></a>Nastavení rozšíření

Microsoft Graph vazby jsou k dispozici prostřednictvím _vazby rozšíření_. Vazba rozšíření jsou volitelné součásti modulu runtime Azure Functions. V této části ukazuje, jak nastavit Microsoft Graph a ověření tokenu rozšíření.

### <a name="enabling-functions-20-preview"></a>Povolení funkce 2.0 preview

Vazba rozšíření jsou dostupné pouze pro 2.0 funkce Azure preview. 

Informace o tom, jak nastavit aplikaci funkce používat verzi preview 2.0 Functions runtime najdete v tématu [jak mít verze modulu runtime Azure Functions](set-runtime-version.md).

### <a name="installing-the-extension"></a>Instalaci rozšíření

Chcete-li nainstalovat rozšíření na portálu Azure, přejděte na šablonu nebo vazbu na ni odkazuje. Vytvoření nové funkce a v obrazovku pro výběr šablony, zvolte scénář "Microsoft Graph". Vyberte jednu z šablony tento scénář. Alternativně můžete přejděte na kartu "Integrací" existující funkce a vyberte jednu z vazby popsaná v tomto článku.

V obou případech se zobrazí upozornění, která určuje příponu k instalaci. Klikněte na tlačítko **nainstalovat** získat rozšíření. Každé rozšíření stačí nainstalovat jednou pro každé aplikaci funkce. 

> [!Note] 
> Proces instalace v portálu může trvat až 10 minut na plánu spotřeby.

Pokud používáte Visual Studio, můžete získat rozšíření nainstalováním [balíčky NuGet, které jsou uvedeny výše v tomto článku](#packages).

### <a name="configuring-authentication--authorization"></a>Konfigurace ověřování / autorizace

Vazby uvedených v tomto článku vyžadují identity má být použit. To umožňuje Microsoft Graph vynutit oprávnění a auditovat interakce. Identita může být uživatel přístup k aplikaci nebo vlastní aplikace. Při konfiguraci tuto identitu, nastavit [aplikace služby ověřování / autorizace](https://docs.microsoft.com/azure/app-service/app-service-authentication-overview) službou Azure Active Directory. Musíte se také s žádostí o oprávnění všech prostředků, které vyžadují funkcí.

> [!Note] 
> Rozšíření Microsoft Graph podporuje jenom ověřování Azure AD. Uživatelé se muset přihlásit pomocí pracovního nebo školního účtu.

Pokud používáte portál Azure, se zobrazí upozornění níže výzvu k instalaci rozšíření. Toto upozornění vás vyzve, abyste konfigurace aplikace služby ověřování / autorizace a žádost o všechna oprávnění, šablony nebo vazba vyžaduje. Klikněte na tlačítko **konfigurovat Azure AD teď** nebo **teď přidejte oprávnění** podle potřeby.



<a name="token-input"></a>
## <a name="auth-token"></a>Ověřovací token

Ověření tokenu vstupní vazby získá token Azure AD pro daný prostředek a poskytuje ji jako řetězec v kódu. Prostředek může být libovolná, pro který má oprávnění aplikace. 

Tato část obsahuje následující témata:

* [Příklad](#auth-token---example)
* [Atributy](#auth-token---attributes)
* [Konfigurace](#auth-token---configuration)
* [Využití](#auth-token---usage)

### <a name="auth-token---example"></a>Token auth – příklad

Podívejte se na konkrétní jazyk příklad:

* [C# skript (.csx)](#auth-token---c-script-example)
* [JavaScript](#auth-token---javascript-example)

#### <a name="auth-token---c-script-example"></a>Token auth – příklad skriptu jazyka C#

Následující příklad získá informace o profilu uživatele.

*Function.json* soubor definuje aktivační procedury HTTP pomocí tokenu vstupní vazby:

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

Kód skriptu jazyka C# na základě tokenu pro volání protokolu HTTP pro Microsoft Graph a vrátí výsledek:

```csharp
using System.Net; 
using System.Net.Http; 
using System.Net.Http.Headers; 

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, string graphToken, TraceWriter log)
{
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", graphToken);
    return await client.GetAsync("https://graph.microsoft.com/v1.0/me/");
}
```

#### <a name="auth-token---javascript-example"></a>Token auth – příklad v jazyce JavaScript

Následující příklad získá informace o profilu uživatele.

*Function.json* soubor definuje aktivační procedury HTTP pomocí tokenu vstupní vazby:

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

Kód jazyka JavaScript na základě tokenu pro volání protokolu HTTP pro Microsoft Graph a vrátí výsledek.

```js
const rp = require('request-promise');

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

### <a name="auth-token---attributes"></a>Token auth – atributy

V [knihovny tříd jazyka C#](functions-dotnet-class-library.md), použijte [tokenu](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/TokenBinding/TokenAttribute.cs) atribut.

### <a name="auth-token---configuration"></a>Token auth – konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru a `Token` atribut.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**name**||Požadovaná proměnná používá v kódu funkce pro ověření tokenu. V tématu [pomocí tokenu ověření vstupu vazba z kódu](#token-input-code).|
|**Typ**||Vyžaduje - musí být nastavena na `token`.|
|**Směr**||Vyžaduje - musí být nastavena na `in`.|
|**Identity**|**Identita**|Požadovaná identity, která se použije k provedení akce. Může být jedna z následujících hodnot:<ul><li><code>userFromRequest</code> -Jedinými platnými s [triggeru protokolu HTTP]. Použije identitu volajícího uživatele.</li><li><code>userFromId</code> -Použije identitu dříve přihlášeného uživatele se zadaným ID. Najdete v článku <code>userId</code> vlastnost.</li><li><code>userFromToken</code> -Použije identitu reprezentována zadaný token. Najdete v článku <code>userToken</code> vlastnost.</li><li><code>clientCredentials</code> -Použije identitu aplikace funkce.</li></ul>|
|**userId**|**UserId**  |V případě potřeby a pouze v případě _identity_ je nastaven na `userFromId`. ID objektu zabezpečení uživatele přidružené k dříve přihlášeného uživatele.|
|**userToken**|**UserToken**|V případě potřeby a pouze v případě _identity_ je nastaven na `userFromToken`. Token platný pro funkce aplikace. |
|**Prostředek**|**Prostředek**|Požadovaná adresu URL prostředku Azure AD, pro které je požadováno token.|

<a name="token-input-code"></a>
### <a name="auth-token---usage"></a>Token auth – použití

Vazba, samotné nevyžaduje žádné oprávnění Azure AD, ale v závislosti na tom, jak se používá token, budete muset požádat o další oprávnění. Zkontrolujte požadavky na prostředku, který chcete s tímto tokenem přístupu.

Token se vždy zobrazí kód jako řetězec.




<a name="excel-input"></a>
## <a name="excel-input"></a>Vstupní aplikace Excel

Vazba vstupní tabulky aplikace Excel přečte obsah tabulky aplikace Excel uložených na Onedrivu.

Tato část obsahuje následující témata:

* [Příklad](#excel-input---example)
* [Atributy](#excel-input---attributes)
* [Konfigurace](#excel-input---configuration)
* [Využití](#excel-input---usage)

### <a name="excel-input---example"></a>V aplikaci Excel (vstup) – příklad

Podívejte se na konkrétní jazyk příklad:

* [C# skript (.csx)](#excel-input---c-script-example)
* [JavaScript](#excel-input---javascript-example)

#### <a name="excel-input---c-script-example"></a>Excel vstup – příklad skriptu jazyka C#

Následující *function.json* soubor definuje aktivační procedury HTTP s vazbou vstupní aplikace Excel:

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

Následující kód skriptu jazyka C# načte obsah zadané tabulky a vrátí je pro uživatele:

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives; 

public static IActionResult Run(HttpRequest req, string[][] excelTableData, TraceWriter log)
{
    return new OkObjectResult(excelTableData);
}
```

#### <a name="excel-input---javascript-example"></a>Excel vstup – příklad v jazyce JavaScript

Následující *function.json* soubor definuje aktivační procedury HTTP s vazbou vstupní aplikace Excel:

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

Následující kód v JavaScriptu přečte obsah zadané tabulky a vrátí je pro uživatele.

```js
module.exports = function (context, req) {
    context.res = {
        body: context.bindings.excelTableData
    };
    context.done();
};
```

### <a name="excel-input---attributes"></a>Excel vstup – atributy

V [knihovny tříd jazyka C#](functions-dotnet-class-library.md), použijte [Excel](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/ExcelAttribute.cs) atribut.

### <a name="excel-input---configuration"></a>V aplikaci Excel (vstup) – konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru a `Excel` atribut.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**name**||Požadovaná proměnná používá v kódu funkce pro tabulky aplikace Excel. V tématu [pomocí tabulky aplikace Excel Vstupní vazba z kódu](#excel-input-code).|
|**Typ**||Vyžaduje - musí být nastavena na `excel`.|
|**Směr**||Vyžaduje - musí být nastavena na `in`.|
|**Identity**|**Identita**|Požadovaná identity, která se použije k provedení akce. Může být jedna z následujících hodnot:<ul><li><code>userFromRequest</code> -Jedinými platnými s [triggeru protokolu HTTP]. Použije identitu volajícího uživatele.</li><li><code>userFromId</code> -Použije identitu dříve přihlášeného uživatele se zadaným ID. Najdete v článku <code>userId</code> vlastnost.</li><li><code>userFromToken</code> -Použije identitu reprezentována zadaný token. Najdete v článku <code>userToken</code> vlastnost.</li><li><code>clientCredentials</code> -Použije identitu aplikace funkce.</li></ul>|
|**userId**|**UserId**  |V případě potřeby a pouze v případě _identity_ je nastaven na `userFromId`. ID objektu zabezpečení uživatele přidružené k dříve přihlášeného uživatele.|
|**userToken**|**UserToken**|V případě potřeby a pouze v případě _identity_ je nastaven na `userFromToken`. Token platný pro funkce aplikace. |
|**path**|**Cesta**|Požadovaná cesta k sešitu aplikace Excel na OneDrive.|
|**worksheetName**|**WorksheetName**|Na listu, ve které se nacházejí v tabulce.|
|**tableName**|**TableName**|Název tabulky. Pokud není zadaný, použije se obsah listu.|

<a name="excel-input-code"></a>
### <a name="excel-input---usage"></a>Excel vstup - využití

Tuto vazbu, musí mít následující oprávnění Azure AD:
|Prostředek|Oprávnění|
|--------|--------|
|Microsoft Graph|Umožňuje získat oprávnění ke čtení souborů uživatelů.|

Vazba zveřejňuje následující typy, které mají funkce .NET:
- řetězec [] –]
- Microsoft.Graph.WorkbookTable
- Typy vlastních objektů (pomocí vazby modelu strukturální)










<a name="excel-output"></a>
## <a name="excel-output"></a>Výstup aplikace Excel

Aplikace Excel výstup vazby změní obsah tabulky aplikace Excel uložených na Onedrivu.

Tato část obsahuje následující témata:

* [Příklad](#excel-output---example)
* [Atributy](#excel-output---attributes)
* [Konfigurace](#excel-output---configuration)
* [Využití](#excel-output---usage)

### <a name="excel-output---example"></a>V aplikaci Excel výstup – příklad

Podívejte se na konkrétní jazyk příklad:

* [C# skript (.csx)](#excel-output---c-script-example)
* [JavaScript](#excel-output---javascript-example)

#### <a name="excel-output---c-script-example"></a>Excel výstupní – příklad skriptu jazyka C#

Následující příklad přidá řádky s tabulkou aplikace Excel.

*Function.json* soubor definuje aktivační procedury HTTP pomocí aplikace Excel výstup vazby:

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

Kód skriptu jazyka C# přidá nový řádek v tabulce (předpokládá, že jeden sloupec) na základě žádostí ze řetězec dotazu:

```csharp
using System.Net;
using System.Text;

public static async Task Run(HttpRequest req, IAsyncCollector<object> newExcelRow, TraceWriter log)
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

#### <a name="excel-output---javascript-example"></a>Excel výstupní – příklad v jazyce JavaScript

Následující příklad přidá řádky s tabulkou aplikace Excel.

*Function.json* soubor definuje aktivační procedury HTTP pomocí aplikace Excel výstup vazby:

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

Následující kód v JavaScriptu přidá nový řádek do tabulky (předpokládá, že jeden sloupec) založených na vstupu z řetězce dotazu.

```js
module.exports = function (context, req) {
    context.bindings.newExcelRow = {
        text: req.query.text
        // Add other properties for additional columns here
    }
    context.done();
};
```

### <a name="excel-output---attributes"></a>Excel výstupní – atributy

V [knihovny tříd jazyka C#](functions-dotnet-class-library.md), použijte [Excel](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/ExcelAttribute.cs) atribut.

### <a name="excel-output---configuration"></a>V aplikaci Excel výstup - konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru a `Excel` atribut.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**name**||Požadovaná proměnná používá v kódu funkce pro ověření tokenu. V tématu [pomocí tabulky aplikace Excel výstup vazba z kódu](#excel-output-code).|
|**Typ**||Vyžaduje - musí být nastavena na `excel`.|
|**Směr**||Vyžaduje - musí být nastavena na `out`.|
|**Identity**|**Identita**|Požadovaná identity, která se použije k provedení akce. Může být jedna z následujících hodnot:<ul><li><code>userFromRequest</code> -Jedinými platnými s [triggeru protokolu HTTP]. Použije identitu volajícího uživatele.</li><li><code>userFromId</code> -Použije identitu dříve přihlášeného uživatele se zadaným ID. Najdete v článku <code>userId</code> vlastnost.</li><li><code>userFromToken</code> -Použije identitu reprezentována zadaný token. Najdete v článku <code>userToken</code> vlastnost.</li><li><code>clientCredentials</code> -Použije identitu aplikace funkce.</li></ul>|
|**UserId** |**userId** |V případě potřeby a pouze v případě _identity_ je nastaven na `userFromId`. ID objektu zabezpečení uživatele přidružené k dříve přihlášeného uživatele.|
|**userToken**|**UserToken**|V případě potřeby a pouze v případě _identity_ je nastaven na `userFromToken`. Token platný pro funkce aplikace. |
|**path**|**Cesta**|Požadovaná cesta k sešitu aplikace Excel na OneDrive.|
|**worksheetName**|**WorksheetName**|Na listu, ve které se nacházejí v tabulce.|
|**tableName**|**TableName**|Název tabulky. Pokud není zadaný, použije se obsah listu.|
|**updateType**|**UpdateType**|Požadovaná typ změny, aby se do tabulky. Může být jedna z následujících hodnot:<ul><li><code>update</code> -Nahradí obsah tabulky ve Onedrivu.</li><li><code>append</code> -Přidá datové části na konec tabulky OneDrive vytvořením nové řádky.</li></ul>|

<a name="excel-output-code"></a>
### <a name="excel-output---usage"></a>Výstup - Excel využití

Tuto vazbu, musí mít následující oprávnění Azure AD:
|Prostředek|Oprávnění|
|--------|--------|
|Microsoft Graph|Úplný přístup k souborům uživatele|

Vazba zveřejňuje následující typy, které mají funkce .NET:
- řetězec [] –]
- Newtonsoft.Json.Linq.JObject
- Microsoft.Graph.WorkbookTable
- Typy vlastních objektů (pomocí vazby modelu strukturální)





<a name="onedrive-input"></a>
## <a name="file-input"></a>Vstupní soubor

Vazba vstupní soubor OneDrive přečte obsah souboru uložených na Onedrivu.

Tato část obsahuje následující témata:

* [Příklad](#file-input---example)
* [Atributy](#file-input---attributes)
* [Konfigurace](#file-input---configuration)
* [Využití](#file-input---usage)

### <a name="file-input---example"></a>Soubor (vstup) – příklad

Podívejte se na konkrétní jazyk příklad:

* [C# skript (.csx)](#file-input---c-script-example)
* [JavaScript](#file-input---javascript-example)

#### <a name="file-input---c-script-example"></a>Soubor vstup – příklad skriptu jazyka C#

Následující příklad načte soubor, který je uložený na OneDrive.

*Function.json* soubor definuje aktivační procedury HTTP s následující vazbou vstupní soubor OneDrive:

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

Kód skriptu jazyka C# načte soubor zadaný v řetězci dotazu a jeho délka protokoly:

```csharp
using System.Net;

public static void Run(HttpRequestMessage req, Stream myOneDriveFile, TraceWriter log)
{
    log.Info(myOneDriveFile.Length.ToString());
}
```

#### <a name="file-input---javascript-example"></a>Soubor vstup – příklad v jazyce JavaScript

Následující příklad načte soubor, který je uložený na OneDrive.

*Function.json* soubor definuje aktivační procedury HTTP s následující vazbou vstupní soubor OneDrive:

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

Následující kód v JavaScriptu načte soubor zadaný v řetězci dotazu a vrátí jeho délka.

```js
module.exports = function (context, req) {
    context.res = {
        body: context.bindings.myOneDriveFile.length
    };
    context.done();
};
```

### <a name="file-input---attributes"></a>Soubor vstup – atributy

V [knihovny tříd jazyka C#](functions-dotnet-class-library.md), použijte [OneDrive](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OneDriveAttribute.cs) atribut.

### <a name="file-input---configuration"></a>Soubor (vstup) – konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru a `OneDrive` atribut.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**name**||Požadovaná proměnná používá v kódu funkce pro soubor. V tématu [pomocí souboru OneDrive Vstupní vazba z kódu](#onedrive-input-code).|
|**Typ**||Vyžaduje - musí být nastavena na `onedrive`.|
|**Směr**||Vyžaduje - musí být nastavena na `in`.|
|**Identity**|**Identita**|Požadovaná identity, která se použije k provedení akce. Může být jedna z následujících hodnot:<ul><li><code>userFromRequest</code> -Jedinými platnými s [triggeru protokolu HTTP]. Použije identitu volajícího uživatele.</li><li><code>userFromId</code> -Použije identitu dříve přihlášeného uživatele se zadaným ID. Najdete v článku <code>userId</code> vlastnost.</li><li><code>userFromToken</code> -Použije identitu reprezentována zadaný token. Najdete v článku <code>userToken</code> vlastnost.</li><li><code>clientCredentials</code> -Použije identitu aplikace funkce.</li></ul>|
|**userId**|**UserId**  |V případě potřeby a pouze v případě _identity_ je nastaven na `userFromId`. ID objektu zabezpečení uživatele přidružené k dříve přihlášeného uživatele.|
|**userToken**|**UserToken**|V případě potřeby a pouze v případě _identity_ je nastaven na `userFromToken`. Token platný pro funkce aplikace. |
|**path**|**Cesta**|Požadovaná cesta k souboru na OneDrive.|

<a name="onedrive-input-code"></a>
### <a name="file-input---usage"></a>Soubor vstup - využití

Tuto vazbu, musí mít následující oprávnění Azure AD:
|Prostředek|Oprávnění|
|--------|--------|
|Microsoft Graph|Umožňuje získat oprávnění ke čtení souborů uživatelů.|

Vazba zveřejňuje následující typy, které mají funkce .NET:
- Byte
- Stream
- řetězec
- Microsoft.Graph.DriveItem






<a name="onedrive-output"></a>
## <a name="file-output"></a>Výstupní soubor

Soubor OneDrive výstup vazby změní obsah souboru uložených na Onedrivu.

Tato část obsahuje následující témata:

* [Příklad](#file-output---example)
* [Atributy](#file-output---attributes)
* [Konfigurace](#file-output---configuration)
* [Využití](#file-output---usage)

### <a name="file-output---example"></a>Soubor výstupu – příklad

Podívejte se na konkrétní jazyk příklad:

* [C# skript (.csx)](#file-output---c-script-example)
* [JavaScript](#file-output---javascript-example)

#### <a name="file-output---c-script-example"></a>Obsah souboru – příklad skriptu jazyka C#

V následujícím příkladu se zapíše do souboru, který je uložený na OneDrive.

*Function.json* soubor definuje aktivační procedury HTTP s OneDrive výstup vazby:

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

Kód skriptu jazyka C# načte text z řetězce dotazu a zapíše ho do textového souboru (FunctionsTest.txt definovaným v předchozím příkladu) v kořenovém adresáři volajícího onedrivu:

```csharp
using System.Net;
using System.Text;

public static async Task Run(HttpRequest req, TraceWriter log, Stream myOneDriveFile)
{
    string data = req.Query
        .FirstOrDefault(q => string.Compare(q.Key, "text", true) == 0)
        .Value;
    await myOneDriveFile.WriteAsync(Encoding.UTF8.GetBytes(data), 0, data.Length);
    return;
}
```

#### <a name="file-output---javascript-example"></a>Obsah souboru – příklad v jazyce JavaScript

V následujícím příkladu se zapíše do souboru, který je uložený na OneDrive.

*Function.json* soubor definuje aktivační procedury HTTP s OneDrive výstup vazby:

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

Kód jazyka JavaScript načte text z řetězce dotazu a zapíše ho do textového souboru (FunctionsTest.txt jak je definována v konfiguračním výše) v kořenovém adresáři volajícího onedrivu.

```js
module.exports = function (context, req) {
    context.bindings.myOneDriveFile = req.query.text;
    context.done();
};
```

### <a name="file-output---attributes"></a>Obsah souboru – atributy

V [knihovny tříd jazyka C#](functions-dotnet-class-library.md), použijte [OneDrive](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OneDriveAttribute.cs) atribut.

### <a name="file-output---configuration"></a>Soubor výstupu - konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru a `OneDrive` atribut.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**name**||Požadovaná proměnná používá v kódu funkce pro soubor. V tématu [pomocí souboru OneDrive výstup vazba z kódu](#onedrive-output-code).|
|**Typ**||Vyžaduje - musí být nastavena na `onedrive`.|
|**Směr**||Vyžaduje - musí být nastavena na `out`.|
|**Identity**|**Identita**|Požadovaná identity, která se použije k provedení akce. Může být jedna z následujících hodnot:<ul><li><code>userFromRequest</code> -Jedinými platnými s [triggeru protokolu HTTP]. Použije identitu volajícího uživatele.</li><li><code>userFromId</code> -Použije identitu dříve přihlášeného uživatele se zadaným ID. Najdete v článku <code>userId</code> vlastnost.</li><li><code>userFromToken</code> -Použije identitu reprezentována zadaný token. Najdete v článku <code>userToken</code> vlastnost.</li><li><code>clientCredentials</code> -Použije identitu aplikace funkce.</li></ul>|
|**UserId** |**userId** |V případě potřeby a pouze v případě _identity_ je nastaven na `userFromId`. ID objektu zabezpečení uživatele přidružené k dříve přihlášeného uživatele.|
|**userToken**|**UserToken**|V případě potřeby a pouze v případě _identity_ je nastaven na `userFromToken`. Token platný pro funkce aplikace. |
|**path**|**Cesta**|Požadovaná cesta k souboru na OneDrive.|

<a name="onedrive-output-code"></a>
#### <a name="file-output---usage"></a>Obsah souboru - využití

Tuto vazbu, musí mít následující oprávnění Azure AD:
|Prostředek|Oprávnění|
|--------|--------|
|Microsoft Graph|Úplný přístup k souborům uživatele|

Vazba zveřejňuje následující typy, které mají funkce .NET:
- Byte
- Stream
- řetězec
- Microsoft.Graph.DriveItem





<a name="outlook-output"></a>
## <a name="outlook-output"></a>Výstup aplikace Outlook

Zprávy Outlooku Výstupní vazba zasílá e-mailovou zprávu prostřednictvím aplikace Outlook.

Tato část obsahuje následující témata:

* [Příklad](#outlook-output---example)
* [Atributy](#outlook-output---attributes)
* [Konfigurace](#outlook-output---configuration)
* [Využití](#outlook-outnput---usage)

### <a name="outlook-output---example"></a>Výstup aplikace Outlook – příklad

Podívejte se na konkrétní jazyk příklad:

* [C# skript (.csx)](#outlook-output---c-script-example)
* [JavaScript](#outlook-output---javascript-example)

#### <a name="outlook-output---c-script-example"></a>Outlook výstupní – příklad skriptu jazyka C#

Následující příklad odešle e-mailu prostřednictvím aplikace Outlook.

*Function.json* soubor definuje aktivační procedury HTTP pomocí aplikace Outlook zpráva výstup vazby:

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

Kód skriptu jazyka C# odešle poštu volající příjemce určeného v řetězci dotazu:

```csharp
using System.Net;

public static void Run(HttpRequest req, out Message message, TraceWriter log)
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

#### <a name="outlook-output---javascript-example"></a>Outlook výstupní – příklad v jazyce JavaScript

Následující příklad odešle e-mailu prostřednictvím aplikace Outlook.

*Function.json* soubor definuje aktivační procedury HTTP pomocí aplikace Outlook zpráva výstup vazby:

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

Kód jazyka JavaScript odešle poštu volající příjemce určeného v řetězci dotazu:

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

### <a name="outlook-output---attributes"></a>Outlook výstupní – atributy

V [knihovny tříd jazyka C#](functions-dotnet-class-library.md), použijte [Outlook](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OutlookAttribute.cs) atribut.

### <a name="outlook-output---configuration"></a>Výstup aplikace Outlook - konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru a `Outlook` atribut.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**name**||Požadovaná proměnná používá v kódu funkce pro e-mailovou zprávu. V tématu [pomocí zprávy Outlooku výstup vazba z kódu](#outlook-output-code).|
|**Typ**||Vyžaduje - musí být nastavena na `outlook`.|
|**Směr**||Vyžaduje - musí být nastavena na `out`.|
|**Identity**|**Identita**|Požadovaná identity, která se použije k provedení akce. Může být jedna z následujících hodnot:<ul><li><code>userFromRequest</code> -Jedinými platnými s [triggeru protokolu HTTP]. Použije identitu volajícího uživatele.</li><li><code>userFromId</code> -Použije identitu dříve přihlášeného uživatele se zadaným ID. Najdete v článku <code>userId</code> vlastnost.</li><li><code>userFromToken</code> -Použije identitu reprezentována zadaný token. Najdete v článku <code>userToken</code> vlastnost.</li><li><code>clientCredentials</code> -Použije identitu aplikace funkce.</li></ul>|
|**userId**|**UserId**  |V případě potřeby a pouze v případě _identity_ je nastaven na `userFromId`. ID objektu zabezpečení uživatele přidružené k dříve přihlášeného uživatele.|
|**userToken**|**UserToken**|V případě potřeby a pouze v případě _identity_ je nastaven na `userFromToken`. Token platný pro funkce aplikace. |

<a name="outlook-output-code"></a>
### <a name="outlook-output---usage"></a>Výstup - Outlook využití

Tuto vazbu, musí mít následující oprávnění Azure AD:
|Prostředek|Oprávnění|
|--------|--------|
|Microsoft Graph|Odesílat poštu jménem uživatele|

Vazba zveřejňuje následující typy, které mají funkce .NET:
- Microsoft.Graph.Message
- Newtonsoft.Json.Linq.JObject
- řetězec
- Typy vlastních objektů (pomocí vazby modelu strukturální)






## <a name="webhooks"></a>Webhooky

Webhooky umožňují reagování na události v aplikaci Microsoft Graph. Pro podporu webhooků, jsou funkce potřebné k vytvoření, aktualizace a reagovat na _webhooku odběry_. Dokončení webhooku řešení vyžaduje kombinaci následující vazby:
- A [aktivační události webhooku Microsoft Graph](#webhook-trigger) umožňuje reagovat na příchozí webhooku.
- A [webhooku předplatného Microsoft Graph vstupní vazby](#webhook-input) umožňuje seznamu existující odběry a můžete je aktualizovat.
- A [webhooku předplatného Microsoft Graph výstup vazby](#webhook-output) umožňuje vytvořit nebo odstranit odběry webhooku.

Vazby sami nevyžadují žádné oprávnění Azure AD, ale budete muset požádat o oprávnění, které jsou relevantní pro typ prostředku, který chcete reagovat na. Seznam, které jsou potřeba oprávnění pro každý typ prostředku, naleznete v části [předplatné oprávnění](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/subscription_post_subscriptions#permissions).

Další informace o webhooky najdete v tématu [práce s webhooky v aplikaci Microsoft Graph].





## <a name="webhook-trigger"></a>Aktivační události Webhooku

Aktivační události webhooku Microsoft Graph umožňuje funkce, která se reagovat na příchozí webhooku z Microsoft Graph. Každá instance této aktivační události může reagovat na některý typ prostředku Microsoft Graph.

Tato část obsahuje následující témata:

* [Příklad](#webhook-trigger---example)
* [Atributy](#webhook-trigger---attributes)
* [Konfigurace](#webhook-trigger---configuration)
* [Využití](#webhook-trigger---usage)

### <a name="webhook-trigger---example"></a>Aktivační události Webhooku – příklad

Podívejte se na konkrétní jazyk příklad:

* [C# skript (.csx)](#webhook-trigger---c-script-example)
* [JavaScript](#webhook-trigger---javascript-example)

#### <a name="webhook-trigger---c-script-example"></a>Aktivační události Webhooku – příklad skriptu jazyka C#

Následující příklad zpracovává webhooky pro příchozí zprávy Outlooku. Použít můžete aktivovat webhook, jehož [vytvořit odběr](#webhook-output---example), a můžete [aktualizovat předplatné](#webhook-subscription-refresh) chcete zabránit vypršení platnosti.

*Function.json* soubor definuje aktivační události webhooku:

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

Kód skriptu jazyka C# reaguje na příchozí e-mailových zpráv a protokoly text těchto poslal příjemce a obsahuje "Azure Functions" v předmětu:

```csharp
#r "Microsoft.Graph"
using Microsoft.Graph;
using System.Net;

public static async Task Run(Message msg, TraceWriter log)  
{
    log.Info("Microsoft Graph webhook trigger function processed a request.");

    // Testable by sending oneself an email with the subject "Azure Functions" and some text body
    if (msg.Subject.Contains("Azure Functions") && msg.From.Equals(msg.Sender)) {
        log.Info($"Processed email: {msg.BodyPreview}");
    }
}
```

#### <a name="webhook-trigger---javascript-example"></a>Aktivační události Webhooku – příklad v jazyce JavaScript

Následující příklad zpracovává webhooky pro příchozí zprávy Outlooku. Použít můžete aktivovat webhook, jehož [vytvořit odběr](#webhook-output---example), a můžete [aktualizovat předplatné](#webhook-subscription-refresh) chcete zabránit vypršení platnosti.

*Function.json* soubor definuje aktivační události webhooku:

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

Kód jazyka JavaScript reaguje na příchozí e-mailových zpráv a protokoly text těchto poslal příjemce a obsahuje "Azure Functions" v předmětu:

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

### <a name="webhook-trigger---attributes"></a>Aktivační události Webhooku – atributy

V [knihovny tříd jazyka C#](functions-dotnet-class-library.md), použijte [GraphWebHookTrigger](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebHookTriggerAttribute.cs) atribut.

### <a name="webhook-trigger---configuration"></a>Aktivační události Webhooku - konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru a `GraphWebHookTrigger` atribut.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**name**||Požadovaná proměnná používá v kódu funkce pro e-mailovou zprávu. V tématu [pomocí zprávy Outlooku výstup vazba z kódu](#outlook-output-code).|
|**Typ**||Vyžaduje - musí být nastavena na `graphWebhook`.|
|**Směr**||Vyžaduje - musí být nastavena na `trigger`.|
|**Typ prostředku**|**Typ prostředku**|Požadovaná prostředků grafu, pro kterou má tato funkce Odpovědět na webhooky. Může být jedna z následujících hodnot:<ul><li><code>#Microsoft.Graph.Message</code> -změny provedené v aplikaci Outlook zprávy.</li><li><code>#Microsoft.Graph.DriveItem</code> -změny provedené v OneDrive kořenové položky.</li><li><code>#Microsoft.Graph.Contact</code> -změny provedené v osobních kontaktů v aplikaci Outlook.</li><li><code>#Microsoft.Graph.Event</code> -změny položky kalendáře aplikace Outlook.</li></ul>|

> [!Note]
> Funkce aplikace může mít pouze jednu funkci, která je registrována danou `resourceType` hodnotu.

### <a name="webhook-trigger---usage"></a>Aktivační události Webhooku - využití

Vazba zveřejňuje následující typy, které mají funkce .NET:
- Microsoft Graph SDK typy relevantní pro typ prostředku, jako například `Microsoft.Graph.Message` nebo `Microsoft.Graph.DriveItem`.
- Typy vlastních objektů (pomocí vazby modelu strukturální)




<a name="webhook-input"></a>
## <a name="webhook-input"></a>Vstup Webhooku

Vstupní vazba webhooku Microsoft Graph umožňuje načíst seznam předplatných spravuje této funkce aplikace. Vazby čte z funkce aplikace úložiště, takže se nemusí odpovídat odběr vytvořené z mimo aplikaci.

Tato část obsahuje následující témata:

* [Příklad](#webhook-input---example)
* [Atributy](#webhook-input---attributes)
* [Konfigurace](#webhook-input---configuration)
* [Využití](#webhook-input---usage)

### <a name="webhook-input---example"></a>Webhooku (vstup) – příklad

Podívejte se na konkrétní jazyk příklad:

* [C# skript (.csx)](#webhook-input---c-script-example)
* [JavaScript](#webhook-input---javascript-example)

#### <a name="webhook-input---c-script-example"></a>Webhooku vstup – příklad skriptu jazyka C#

V následujícím příkladu získá všechna předplatná pro volání uživatele a je odstraní.

*Function.json* soubor definuje aktivační procedury HTTP vstupní vazbou předplatného a předplatné výstup, vazby, které používá akci odstranění:

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

Kód skriptu jazyka C# získá předplatná a je odstraní:

```csharp
using System.Net;

public static async Task Run(HttpRequest req, string[] existingSubscriptions, IAsyncCollector<string> subscriptionsToDelete, TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");
    foreach (var subscription in existingSubscriptions)
    {
        log.Info($"Deleting subscription {subscription}");
        await subscriptionsToDelete.AddAsync(subscription);
    }
}
```

#### <a name="webhook-input---javascript-example"></a>Webhooku vstup – příklad v jazyce JavaScript

V následujícím příkladu získá všechna předplatná pro volání uživatele a je odstraní.

*Function.json* soubor definuje aktivační procedury HTTP vstupní vazbou předplatného a předplatné výstup, vazby, které používá akci odstranění:

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

Kód jazyka JavaScript získá předplatná a je odstraní:

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

### <a name="webhook-input---attributes"></a>Webhooku vstup – atributy

V [knihovny tříd jazyka C#](functions-dotnet-class-library.md), použijte [GraphWebHookSubscription](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebHookSubscriptionAttribute.cs) atribut.

### <a name="webhook-input---configuration"></a>Webhooku (vstup) – konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru a `GraphWebHookSubscription` atribut.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**name**||Požadovaná proměnná používá v kódu funkce pro e-mailovou zprávu. V tématu [pomocí zprávy Outlooku výstup vazba z kódu](#outlook-output-code).|
|**Typ**||Vyžaduje - musí být nastavena na `graphWebhookSubscription`.|
|**Směr**||Vyžaduje - musí být nastavena na `in`.|
|**Filtr**|**Filtr**| Pokud nastavena na `userFromRequest`, pak vazby pouze načte odběry vlastněných uživatelem volání (platný pouze s [triggeru protokolu HTTP]).| 

### <a name="webhook-input---usage"></a>Vstup - Webhooku využití

Vazba zveřejňuje následující typy, které mají funkce .NET:
- řetězec]
- Typ pole vlastních objektů
- Newtonsoft.Json.Linq.JObject[]
- Microsoft.Graph.Subscription[]





## <a name="webhook-output"></a>Výstup Webhooku

Předplatné webhooku výstup vazba umožňuje vytvořit, odstranit a aktualizovat webhook odběry v aplikaci Microsoft Graph.

Tato část obsahuje následující témata:

* [Příklad](#webhook-output---example)
* [Atributy](#webhook-output---attributes)
* [Konfigurace](#webhook-output---configuration)
* [Využití](#webhook-output---usage)

### <a name="webhook-output---example"></a>Výstup Webhooku – příklad

Podívejte se na konkrétní jazyk příklad:

* [C# skript (.csx)](#webhook-output---c-script-example)
* [JavaScript](#webhook-output---javascript-example)

#### <a name="webhook-output---c-script-example"></a>Výstup - Webhooku příklad skriptu jazyka C#

Následující příklad vytvoří odběr. Můžete [aktualizovat předplatné](#webhook-subscription-refresh) chcete zabránit vypršení platnosti.

*Function.json* soubor definuje aktivační procedury HTTP s výkonem předplatné vazby pomocí akce vytvořit:

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

Kód skriptu jazyka C# zaregistruje webhook, jehož oznámí této funkce aplikace při volání uživatel obdrží zprávu aplikace Outlook:

```csharp
using System;
using System.Net;

public static HttpResponseMessage run(HttpRequestMessage req, out string clientState, TraceWriter log)
{
  log.Info("C# HTTP trigger function processed a request.");
    clientState = Guid.NewGuid().ToString();
    return new HttpResponseMessage(HttpStatusCode.OK);
}
```

#### <a name="webhook-output---javascript-example"></a>Výstup - Webhooku příklad v jazyce JavaScript

Následující příklad vytvoří odběr. Můžete [aktualizovat předplatné](#webhook-subscription-refresh) chcete zabránit vypršení platnosti.

*Function.json* soubor definuje aktivační procedury HTTP s výkonem předplatné vazby pomocí akce vytvořit:

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

Kód jazyka JavaScript zaregistruje webhook, jehož oznámí této funkce aplikace při volání uživatel obdrží zprávu aplikace Outlook:

```js
const uuidv4 = require('uuid/v4');

module.exports = function (context, req) {
    context.bindings.clientState = uuidv4();
    context.done();
};
```

### <a name="webhook-output---attributes"></a>Webhooku výstup – atributy

V [knihovny tříd jazyka C#](functions-dotnet-class-library.md), použijte [GraphWebHookSubscription](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebHookSubscriptionAttribute.cs) atribut.

### <a name="webhook-output---configuration"></a>Výstup Webhooku – konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru a `GraphWebHookSubscription` atribut.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**name**||Požadovaná proměnná používá v kódu funkce pro e-mailovou zprávu. V tématu [pomocí zprávy Outlooku výstup vazba z kódu](#outlook-output-code).|
|**Typ**||Vyžaduje - musí být nastavena na `graphWebhookSubscription`.|
|**Směr**||Vyžaduje - musí být nastavena na `out`.|
|**Identity**|**Identita**|Požadovaná identity, která se použije k provedení akce. Může být jedna z následujících hodnot:<ul><li><code>userFromRequest</code> -Jedinými platnými s [triggeru protokolu HTTP]. Použije identitu volajícího uživatele.</li><li><code>userFromId</code> -Použije identitu dříve přihlášeného uživatele se zadaným ID. Najdete v článku <code>userId</code> vlastnost.</li><li><code>userFromToken</code> -Použije identitu reprezentována zadaný token. Najdete v článku <code>userToken</code> vlastnost.</li><li><code>clientCredentials</code> -Použije identitu aplikace funkce.</li></ul>|
|**userId**|**UserId**  |V případě potřeby a pouze v případě _identity_ je nastaven na `userFromId`. ID objektu zabezpečení uživatele přidružené k dříve přihlášeného uživatele.|
|**userToken**|**UserToken**|V případě potřeby a pouze v případě _identity_ je nastaven na `userFromToken`. Token platný pro funkce aplikace. |
|**Akce**|**Akce**|Požadováno – určuje má provést akce vazby. Může být jedna z následujících hodnot:<ul><li><code>create</code> -Zaregistruje nové předplatné.</li><li><code>delete</code> -Odstraní určený odběr.</li><li><code>refresh</code> -Aktualizuje určený odběr Zabraňte vypršení platnosti.</li></ul>|
|**subscriptionResource**|**SubscriptionResource**|V případě potřeby a pouze v případě _akce_ je nastaven na `create`. Určuje prostředek Microsoft Graph, které se budou monitorovat změny. V tématu [práce s webhooky v aplikaci Microsoft Graph]. |
|**changeType**|**ChangeType –**|V případě potřeby a pouze v případě _akce_ je nastaven na `create`. Určuje typ změny v odebírané prostředek, který vyvolá oznámení. Podporované hodnoty jsou: `created`, `updated`, `deleted`. Více hodnot lze spojovat pomocí seznam oddělený čárkami.|

### <a name="webhook-output---usage"></a>Výstup - Webhooku využití

Vazba zveřejňuje následující typy, které mají funkce .NET:
- řetězec
- Microsoft.Graph.Subscription




<a name="webhook-examples"></a>
## <a name="webhook-subscription-refresh"></a>Aktualizace předplatného Webhooku

Aktualizace odběrů dvěma způsoby:

- Pomocí identity aplikace řešit všechny odběry. To bude vyžadovat souhlas ze Správce Azure Active Directory. Tímto lze ve všech jazycích, které podporuje Azure Functions.
- Použít identitu přidruženou každé předplatné pomocí ruční vytvoření vazby každé ID uživatele. To bude vyžadovat některé vlastní kód k provedení vazby. Můžete použít pouze pomocí funkce .NET.

Tato část obsahuje příklad pro každou z těchto postupů:

* [Příklad identity aplikace](#webhook-subscription-refresh---app-identity-example)
* [Příklad identity uživatele](#webhook-subscription-refresh---user-identity-example)

### <a name="webhook-subscription-refresh---app-identity-example"></a>Aktualizace předplatného Webhooku – příklad identity aplikace

Podívejte se na konkrétní jazyk příklad:

* [C# skript (.csx)](#app-identity-refresh---c-script-example)
* [JavaScript](#app-identity-refresh---javascript-example)

### <a name="app-identity-refresh---c-script-example"></a>Aktualizace identity aplikace – příklad skriptu jazyka C#

Následující příklad použije identitu aplikace aktualizovat předplatné.

*Function.json* definuje aktivační událost časovače s předplatným vstupní vazby a předplatné výstupní vazby:

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

public static void Run(TimerInfo myTimer, string[] existingSubscriptions, ICollector<string> subscriptionsToRefresh, TraceWriter log)
{
    // This template uses application permissions and requires consent from an Azure Active Directory admin.
    // See https://go.microsoft.com/fwlink/?linkid=858780
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
    foreach (var subscription in existingSubscriptions)
    {
      log.Info($"Refreshing subscription {subscription}");
      subscriptionsToRefresh.Add(subscription);
    }
}
```

### <a name="app-identity-refresh---c-script-example"></a>Aktualizace identity aplikace – příklad skriptu jazyka C#

Následující příklad použije identitu aplikace aktualizovat předplatné.

*Function.json* definuje aktivační událost časovače s předplatným vstupní vazby a předplatné výstupní vazby:

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

Kód jazyka JavaScript aktualizuje odběry:

```js
// This template uses application permissions and requires consent from an Azure Active Directory admin.
// See https://go.microsoft.com/fwlink/?linkid=858780

module.exports = function (context) {
    const existing = context.bindings.existingSubscriptions;
    var toRefresh = [];
    for (var i = 0; i < existing.length; i++) {
        context.log(`Deleting subscription ${existing[i]}`);
        todelete.push(existing[i]);
    }
    context.bindings.subscriptionsToRefresh = toRefresh;
    context.done();
};
```

### <a name="webhook-subscription-refresh---user-identity-example"></a>Aktualizace předplatného Webhooku – příklad identity uživatele

Následující příklad používá k aktualizaci předplatné identity uživatele.

*Function.json* souboru definuje aktivační událost časovače a odkládat údaje předplatného vstupní vazby kód funkce:

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

public static async Task Run(TimerInfo myTimer, UserSubscription[] existingSubscriptions, IBinder binder, TraceWriter log)
{
  log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
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
            log.Info($"Refreshing subscription {subscription}");
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
> [Další informace o Azure functions triggerů a vazeb](functions-triggers-bindings.md)

[triggeru protokolu HTTP]: functions-bindings-http-webhook.md
[práce s webhooky v aplikaci Microsoft Graph]: https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/webhooks
