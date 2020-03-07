---
title: Azure Functions výstupní vazby HTTP
description: Naučte se vracet odpovědi HTTP v Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: a25658677e436edf4d001599bb4981f527016596
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78356797"
---
# <a name="azure-functions-http-output-bindings"></a>Azure Functions výstupní vazby HTTP

K reakci na odesílatele požadavku HTTP použijte vazbu výstupu HTTP. Tato vazba vyžaduje Trigger HTTP a umožňuje přizpůsobit odpověď přidruženou k žádosti triggeru.

Výchozí návratová hodnota funkce aktivované protokolem HTTP je:

- `HTTP 204 No Content` s prázdným textem ve funkcích 2. x a vyšší
- `HTTP 200 OK` s prázdným textem ve funkcích 1. x

## <a name="configuration"></a>Konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v souboru *Function. JSON* . Pro C# knihovny tříd nejsou k dispozici žádné vlastnosti atributů, které by odpovídaly těmto vlastnostem *Functions. JSON* .

|Vlastnost  |Popis  |
|---------|---------|
| **type** |Musí být nastavené na `http`. |
| **direction** | Musí být nastavené na `out`. |
| **Jméno** | Název proměnné použitý v kódu funkce pro odpověď nebo `$return` pro použití návratové hodnoty. |

## <a name="usage"></a>Využití

K odeslání odpovědi HTTP použijte standardní vzory odezvy jazyka. V C# nebo C# skriptu, nastavte návratový typ funkce `IActionResult` nebo `Task<IActionResult>`. V C#nástroji není atribut návratové hodnoty požadován.

Například odpovědi najdete v [příkladu triggeru](./functions-bindings-http-webhook-trigger.md#example).

## <a name="hostjson-settings"></a>nastavení Host.JSON

Tato část popisuje globální nastavení konfigurace, která jsou k dispozici pro tuto vazbu ve verzích 2. x a vyšší. Ukázkový soubor host. JSON níže obsahuje pouze nastavení verze 2. x + pro tuto vazbu. Další informace o globálních nastaveních konfigurace ve verzích 2. x a novějších naleznete v tématu [reference Host. JSON pro Azure Functions](functions-host-json.md).

> [!NOTE]
> Odkaz na Host. JSON ve funkcích 1. x najdete v [referenčních informacích k host. JSON pro Azure Functions 1. x](functions-host-json-v1.md#http).

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

|Vlastnost  |Výchozí | Popis |
|---------|---------|---------| 
| customHeaders|Žádná|Umožňuje nastavit vlastní hlavičky v odpovědi HTTP. Předchozí příklad přidá hlavičku `X-Content-Type-Options` k odpovědi, aby nedocházelo ke sledování obsahu typu obsahu. |
|dynamicThrottlesEnabled|true<sup>\*</sup>|Když je toto nastavení povolené, bude kanál zpracování žádostí pravidelně kontrolovat čítače výkonu systému, jako je `connections/threads/processes/memory/cpu/etc`, a pokud některý z těchto čítačů překročí vestavěnou vysokou prahovou hodnotu (80%), žádosti se odmítnou s `429 "Too Busy"`ou odpovědí, dokud se čítače nevrátí na normální úrovně.<br/><sup>\*</sup> Výchozí hodnota v plánu spotřeby je `true`. Výchozí hodnota ve vyhrazeném plánu je `false`.|
|HSTS|Nepovoleno|Pokud je `isEnabled` nastaveno na `true`, vynutilo se [chování HSTS (http Strict Transport Security) .NET Core](/aspnet/core/security/enforcing-ssl?view=aspnetcore-3.0&tabs=visual-studio#hsts) , jak je definováno ve [třídě`HstsOptions`](/dotnet/api/microsoft.aspnetcore.httpspolicy.hstsoptions?view=aspnetcore-3.0). Výše uvedený příklad také nastaví vlastnost [`maxAge`](/dotnet/api/microsoft.aspnetcore.httpspolicy.hstsoptions.maxage?view=aspnetcore-3.0#Microsoft_AspNetCore_HttpsPolicy_HstsOptions_MaxAge) na hodnotu 10 dnů. Podporované vlastnosti `hsts` jsou: <table><tr><th>Vlastnost</th><th>Popis</th></tr><tr><td>excludedHosts</td><td>Pole řetězců názvů hostitelů, pro které není přidáno záhlaví HSTS.</td></tr><tr><td>includeSubDomains</td><td>Logická hodnota, která označuje, zda je povolen parametr includeSubDomain hlavičky Strict-Transport-Security.</td></tr><tr><td>maxAge</td><td>Řetězec definující parametr maximálního stáří záhlaví Strict-Transport-Security.</td></tr><tr><td>přednačtení</td><td>Logická hodnota, která označuje, zda je povolen parametr přednačtení záhlaví Strict-Transport-Security.</td></tr></table>|
|maxConcurrentRequests|100<sup>\*</sup>|Maximální počet funkcí HTTP, které jsou spouštěny paralelně. Tato hodnota vám umožní řídit souběžnost, která může pomáhat spravovat využití prostředků. Můžete mít například funkci HTTP, která používá velký počet systémových prostředků (paměť/procesor/sokety), což způsobí, že dojde k problémům, pokud je souběžnost příliš vysoká. Nebo může být funkce, která vytváří odchozí požadavky na službu třetí strany, a tato volání musí být omezená. V těchto případech vám může pomáhat použití omezení. <br/><sup>*</sup> Výchozí hodnota pro plán spotřeby je 100. Výchozí hodnota pro vyhrazený plán je nevázaná (`-1`).|
|maxOutstandingRequests|200<sup>\*</sup>|Maximální počet nezpracovaných požadavků, které jsou v daném okamžiku uchovávány. Tento limit zahrnuje požadavky, které jsou ve frontě, ale nezačaly běžet, a také jakékoli probíhající provádění. Všechny příchozí žádosti přes toto omezení se odmítnou s 429 "příliš zaneprázdněnou" odezvou. Umožňuje volajícím využívat strategie opakování na základě času a také vám pomůže řídit maximální latenci žádostí. Tato možnost řídí služby Řízení front zpráv, ke kterým dochází v cestě spuštění hostitele skriptu. Další fronty, například fronta žádostí ASP.NET, budou stále platit a nebudou ovlivněny tímto nastavením. <br/><sup>\*</sup> Výchozí hodnota pro plán spotřeby je 200. Výchozí hodnota pro vyhrazený plán je nevázaná (`-1`).|
|routePrefix|rozhraní api|Předpona trasy, která se vztahuje na všechny trasy. K odebrání výchozí předpony použijte prázdný řetězec. |

## <a name="next-steps"></a>Další kroky

- [Spuštění funkce z požadavku HTTP](./functions-bindings-http-webhook-trigger.md)