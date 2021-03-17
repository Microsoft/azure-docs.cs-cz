---
title: Azure Functions výstupní vazby HTTP
description: Naučte se vracet odpovědi HTTP v Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: a25658677e436edf4d001599bb4981f527016596
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84697438"
---
# <a name="azure-functions-http-output-bindings"></a>Azure Functions výstupní vazby HTTP

K reakci na odesílatele požadavku HTTP použijte vazbu výstupu HTTP. Tato vazba vyžaduje aktivační událost protokolu HTTP a umožňuje přizpůsobit odpověď přidruženou k požadavku aktivační události.

Výchozí návratová hodnota funkce aktivované protokolem HTTP je:

- `HTTP 204 No Content` prázdné tělo ve funkcích 2. x a vyšší
- `HTTP 200 OK` prázdné tělo ve funkcích 1. x

## <a name="configuration"></a>Konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.js* v souboru. Pro knihovny tříd jazyka C# nejsou k dispozici žádné vlastnosti atributů, které by odpovídaly těmto *function.js* vlastností.

|Vlastnost  |Popis  |
|---------|---------|
| **textový** |Musí být nastaven na hodnotu `http` . |
| **směr** | Musí být nastaven na hodnotu `out` . |
| **Jméno** | Název proměnné použitý v kódu funkce pro odpověď nebo `$return` pro použití návratové hodnoty. |

## <a name="usage"></a>Využití

K odeslání odpovědi HTTP použijte standardní vzory odezvy jazyka. V jazyce C# nebo C#, nastavte návratový typ funkce `IActionResult` nebo `Task<IActionResult>` . V jazyce C# není atribut návratové hodnoty požadován.

Například odpovědi najdete v [příkladu triggeru](./functions-bindings-http-webhook-trigger.md#example).

## <a name="hostjson-settings"></a>host.jsnastavení

Tato část popisuje globální nastavení konfigurace, která jsou k dispozici pro tuto vazbu ve verzích 2. x a vyšší. Příklad host.jsv souboru níže obsahuje pouze nastavení verze 2. x + pro tuto vazbu. Další informace o globálních nastaveních konfigurace ve verzích 2. x a novějších naleznete v tématu [host.jsv referenčních informacích pro Azure Functions](functions-host-json.md).

> [!NOTE]
> Odkaz na host.jspro ve funkcích 1. x naleznete v tématu [host.json reference for Azure Functions 1. x](functions-host-json-v1.md#http).

```json
{
    "extensions": {
        "http": {
            "routePrefix": "api",
            "maxOutstandingRequests": 200,
            "maxConcurrentRequests": 100,
            "dynamicThrottlesEnabled": true,
            "hsts": {
                "isEnabled": true,
                "maxAge": "10"
            },
            "customHeaders": {
                "X-Content-Type-Options": "nosniff"
            }
        }
    }
}
```

|Vlastnost  |Výchozí | Description |
|---------|---------|---------| 
| customHeaders|žádné|Umožňuje nastavit vlastní hlavičky v odpovědi HTTP. Předchozí příklad přidá `X-Content-Type-Options` hlavičku k odpovědi, aby nedocházelo ke sledování obsahu typu obsahu. |
|dynamicThrottlesEnabled|podmínka<sup>\*</sup>|Pokud je toto nastavení povolené, bude v kanálu zpracování požadavků pravidelně kontrolovat čítače výkonu systému `connections/threads/processes/memory/cpu/etc` , jako a pokud některý z těchto čítačů překročí vestavěnou vysokou prahovou hodnotu (80%), požadavky budou odmítnuty s `429 "Too Busy"` odpovědí, dokud se čítače nevrátí na normální úrovně.<br/><sup>\*</sup>Výchozí hodnota v plánu spotřeby je `true` . Výchozí hodnota ve vyhrazeném plánu je `false` .|
|HSTS|Nepovoleno|Když `isEnabled` je nastavená na `true` , vynutilo se [chování HSTS (http Strict Transport Security) .NET Core](/aspnet/core/security/enforcing-ssl?view=aspnetcore-3.0&tabs=visual-studio#hsts) , jak je definované ve [ `HstsOptions` třídě](/dotnet/api/microsoft.aspnetcore.httpspolicy.hstsoptions?view=aspnetcore-3.0). Výše uvedený příklad nastaví [`maxAge`](/dotnet/api/microsoft.aspnetcore.httpspolicy.hstsoptions.maxage?view=aspnetcore-3.0#Microsoft_AspNetCore_HttpsPolicy_HstsOptions_MaxAge) vlastnost na hodnotu 10 dní. Podporované vlastnosti `hsts` : <table><tr><th>Vlastnost</th><th>Popis</th></tr><tr><td>excludedHosts</td><td>Pole řetězců názvů hostitelů, pro které není přidáno záhlaví HSTS.</td></tr><tr><td>includeSubDomains</td><td>Logická hodnota, která označuje, zda je povolen parametr includeSubDomain hlavičky Strict-Transport-Security.</td></tr><tr><td>maxAge</td><td>Řetězec definující parametr maximálního stáří záhlaví Strict-Transport-Security.</td></tr><tr><td>preload</td><td>Logická hodnota, která označuje, zda je povolen parametr přednačtení záhlaví Strict-Transport-Security.</td></tr></table>|
|maxConcurrentRequests|100<sup>\*</sup>|Maximální počet funkcí HTTP, které jsou spouštěny paralelně. Tato hodnota vám umožní řídit souběžnost, která může pomáhat spravovat využití prostředků. Můžete mít například funkci HTTP, která používá velký počet systémových prostředků (paměť/procesor/sokety), což způsobí, že dojde k problémům, pokud je souběžnost příliš vysoká. Nebo může být funkce, která vytváří odchozí požadavky na službu třetí strany, a tato volání musí být omezená. V těchto případech vám může pomáhat použití omezení. <br/><sup>*</sup>Výchozí hodnota pro plán spotřeby je 100. Výchozí hodnota pro vyhrazený plán je neohraničená ( `-1` ).|
|maxOutstandingRequests|200<sup>\*</sup>|Maximální počet nezpracovaných požadavků, které jsou v daném okamžiku uchovávány. Tento limit zahrnuje požadavky, které jsou ve frontě, ale nezačaly běžet, a také jakékoli probíhající provádění. Všechny příchozí žádosti přes toto omezení se odmítnou s 429 "příliš zaneprázdněnou" odezvou. Umožňuje volajícím využívat strategie opakování na základě času a také vám pomůže řídit maximální latenci žádostí. Tato možnost řídí služby Řízení front zpráv, ke kterým dochází v cestě spuštění hostitele skriptu. Další fronty, například fronta žádostí ASP.NET, budou stále platit a nebudou ovlivněny tímto nastavením. <br/><sup>\*</sup>Výchozí hodnota pro plán spotřeby je 200. Výchozí hodnota pro vyhrazený plán je neohraničená ( `-1` ).|
|routePrefix|api|Předpona trasy, která se vztahuje na všechny trasy. K odebrání výchozí předpony použijte prázdný řetězec. |

## <a name="next-steps"></a>Další kroky

- [Spuštění funkce z požadavku HTTP](./functions-bindings-http-webhook-trigger.md)