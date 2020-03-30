---
title: Azure Functions HTTP výstupní vazby
description: Zjistěte, jak vrátit odpovědi HTTP v Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: a25658677e436edf4d001599bb4981f527016596
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277619"
---
# <a name="azure-functions-http-output-bindings"></a>Azure Functions HTTP výstupní vazby

Pomocí výstupní vazby PROTOKOLU HTTP můžete reagovat na odesílatele požadavku HTTP. Tato vazba vyžaduje aktivační událost protokolu HTTP a umožňuje přizpůsobit odpověď přidruženou k požadavku aktivační události.

Výchozí vrácená hodnota pro funkci spouštěnou protokolem HTTP je:

- `HTTP 204 No Content`s prázdným tělem ve funkcích 2.x a vyšší
- `HTTP 200 OK`s prázdným tělem ve funkcích 1.x

## <a name="configuration"></a>Konfigurace

Následující tabulka vysvětluje vlastnosti konfigurace vazby, které jste nastavili v souboru *function.json.* Pro knihovny tříd jazyka C# neexistují žádné vlastnosti atributů, které odpovídají těmto vlastnostem *function.json.*

|Vlastnost  |Popis  |
|---------|---------|
| **Typ** |Musí být `http`nastavena na . |
| **direction** | Musí být `out`nastavena na . |
| **Jméno** | Název proměnné použitý v kódu funkce `$return` pro odpověď nebo použít vrácenou hodnotu. |

## <a name="usage"></a>Využití

Chcete-li odeslat odpověď HTTP, použijte vzory standardní chod jazyka. Ve skriptu Jazyka C# nebo C# proveďte návratový typ `IActionResult` funkce nebo `Task<IActionResult>`. V c#není atribut vrácené hodnoty vyžadován.

Například odpovědi naleznete v [příkladu aktivační události](./functions-bindings-http-webhook-trigger.md#example).

## <a name="hostjson-settings"></a>nastavení host.json

Tato část popisuje globální nastavení konfigurace, která jsou k dispozici pro tuto vazbu ve verzích 2.x a vyšších. Příklad souboru host.json níže obsahuje pouze nastavení verze 2.x+ pro tuto vazbu. Další informace o nastavení globální konfigurace ve verzích 2.x a dále naleznete [v odkazu host.json pro funkce Azure](functions-host-json.md).

> [!NOTE]
> Odkaz na host.json ve funkcích 1.x najdete v [tématu reference host.json pro Funkce Azure 1.x](functions-host-json-v1.md#http).

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
| vlastní záhlaví|Žádná|Umožňuje nastavit vlastní záhlaví v odpovědi HTTP. Předchozí příklad přidá `X-Content-Type-Options` záhlaví k odpovědi, aby se zabránilo sledování typu obsahu. |
|dynamicThrottlesEnabled|Pravda<sup>\*</sup>|Pokud je povoleno, toto nastavení způsobí, že kanál `connections/threads/processes/memory/cpu/etc` zpracování požadavků pravidelně kontroluje čítače výkonu systému, jako je, a pokud `429 "Too Busy"` některý z těchto čítačů překročí předdefinovanou vysokou prahovou hodnotu (80 %), budou požadavky odmítnuty s odpovědí, dokud se čítače nevrátí na normální úroveň.<br/><sup>\*</sup>Výchozí hodnota v plánu `true`spotřeby je . Výchozí hodnota v vyhrazeném plánu je `false`.|
|hsts|není povoleno|Pokud `isEnabled` je `true`nastavena na , [http přísné zabezpečení přenosu (HSTS) chování .NET Core](/aspnet/core/security/enforcing-ssl?view=aspnetcore-3.0&tabs=visual-studio#hsts) je vynuceno, jak je definováno ve [ `HstsOptions` třídě](/dotnet/api/microsoft.aspnetcore.httpspolicy.hstsoptions?view=aspnetcore-3.0). Výše uvedený příklad [`maxAge`](/dotnet/api/microsoft.aspnetcore.httpspolicy.hstsoptions.maxage?view=aspnetcore-3.0#Microsoft_AspNetCore_HttpsPolicy_HstsOptions_MaxAge) také nastaví vlastnost na 10 dní. Podporované vlastnosti `hsts` jsou: <table><tr><th>Vlastnost</th><th>Popis</th></tr><tr><td>vyloučeniHostitelé</td><td>Pole řetězec názvů hostitelů, pro které není přidána hlavička HSTS.</td></tr><tr><td>includeSubDomains</td><td>Logická hodnota, která označuje, zda je povolen parametr includeSubDomain v hlavičce Strict-Transport-Security.</td></tr><tr><td>Maxage</td><td>Řetězec, který definuje parametr maximálního stáří hlavičky Strict-Transport-Security.</td></tr><tr><td>preload</td><td>Logická hodnota, která označuje, zda je povolen parametr předběžného načtení hlavičky Strict-Transport-Security.</td></tr></table>|
|maxConcurrentRequests|100<sup>\*</sup>|Maximální počet funkcí PROTOKOLU HTTP, které jsou spouštěny paralelně. Tato hodnota umožňuje řídit souběžnost, která může pomoci spravovat využití prostředků. Můžete mít například funkci HTTP, která používá velký počet systémových prostředků (paměť/procesor/sokety), takže způsobuje problémy, když je souběžnost příliš vysoká. Nebo můžete mít funkci, která provádí odchozí požadavky na službu jiného výrobce a tato volání musí být omezena. V těchto případech může pomoci použití škrticí klapky. <br/><sup>*</sup>Výchozí hodnota plánu spotřeby je 100. Výchozí hodnota vyhrazeného plánu je`-1`neohraničená ( ).|
|maxOutstandingRequests|200<sup>\*</sup>|Maximální počet nevyřízených požadavků, které jsou v daném okamžiku uchovávány. Toto omezení zahrnuje požadavky, které jsou zařazeny do fronty, ale nebyly spuštěny, stejně jako všechny probíhající spuštění. Všechny příchozí požadavky nad tento limit jsou odmítnuty s odpovědí 429 "Příliš zaneprázdněn". To umožňuje volajícím využívat strategie opakování založené na čase a také vám pomůže řídit maximální latenci požadavků. To to pouze řídí fronty, ke kterému dochází v rámci cesty spuštění hostitele skriptu. Ostatní fronty, například ASP.NET fronty požadavků, budou tímto nastavením stále platné a nebudou ovlivněny. <br/><sup>\*</sup>Výchozí hodnota plánu spotřeby je 200. Výchozí hodnota vyhrazeného plánu je`-1`neohraničená ( ).|
|routePrefix|rozhraní api|Předpona trasy, která platí pro všechny trasy. K odebrání výchozí předpony použijte prázdný řetězec. |

## <a name="next-steps"></a>Další kroky

- [Spuštění funkce z požadavku HTTP](./functions-bindings-http-webhook-trigger.md)