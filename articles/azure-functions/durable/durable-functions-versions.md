---
title: Přehled verzí Durable Functions – Azure Functions
description: Přečtěte si o Durable Functions verzích.
author: cgillum
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: azfuncdf
ms.openlocfilehash: 4a117e7f69647af3ad82f9013bfa40556ccc0dbd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "77152886"
---
# <a name="durable-functions-versions-overview"></a>Přehled verzí Durable Functions

*Durable Functions* je rozšíření [Azure Functions](../functions-overview.md) a [Azure WebJobs](../../app-service/web-sites-create-web-jobs.md) , které umožňuje psát stavové funkce v prostředí bez serveru. Toto rozšíření za vás spravuje stav, kontrolní body a restartování. Pokud ještě neznáte Durable Functions, přečtěte si [dokumentaci přehled](durable-functions-overview.md).

## <a name="new-features-in-2x"></a>Nové funkce v 2. x

Tato část popisuje funkce Durable Functions přidaných ve verzi 2. x.

### <a name="durable-entities"></a>Trvalé entity

V Durable Functions 2. x jsme zavedli nový koncept [entity Functions](durable-functions-entities.md) .

Funkce entit definují operace pro čtení a aktualizaci malých částí stavu, označovaných jako *odolné entity*. Podobně jako funkce nástroje Orchestrator jsou funkce entit funkce se speciálním typem triggeru, *triggerem entity*. Na rozdíl od funkcí Orchestrator nemají entity Functions žádná konkrétní omezení kódu. Funkce entit také spravují stav explicitně namísto implicitního reprezentace stavu prostřednictvím řízení toku.

Další informace najdete v článku [odolné entity](durable-functions-entities.md) .

### <a name="durable-http"></a>Trvalá HTTP

V Durable Functions 2. x jsme zavedli novou funkci [trvalého http](durable-functions-http-features.md#consuming-http-apis) , která vám umožní:

* Volání rozhraní API HTTP přímo z funkcí orchestrace (s některými dokumentovanými omezeními).
* Implementujte Automatické dotazování na stav HTTP 202 na straně klienta.
* Integrovaná podpora [spravovaných identit Azure](../../active-directory/managed-identities-azure-resources/overview.md).

Další informace najdete v článku věnovaném [funkcím protokolu HTTP](durable-functions-http-features.md#consuming-http-apis) .

## <a name="migrate-from-1x-to-2x"></a>Migrace od 1. x do 2. x

Tato část popisuje, jak migrovat existující verzi 1. x Durable Functions na verzi 2. x, abyste mohli využít nové funkce.

### <a name="upgrade-the-extension"></a>Upgradovat rozšíření

Nainstalujte ve svém projektu verzi 2. x [rozšíření vazby Durable Functions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) . Další informace najdete v tématu věnovaném [registraci rozšíření vazby Azure Functions](../functions-bindings-register.md) .

### <a name="update-your-code"></a>Aktualizace kódu

Durable Functions 2. x přináší několik přerušujících změn. Aplikace Durable Functions 1. x nejsou kompatibilní s Durable Functions 2. x bez změny kódu. V této části jsou uvedené některé změny, které je třeba provést při upgradu funkcí verze 1. x na 2. x.

#### <a name="hostjson-schema"></a>Schéma Host. JSON

Durable Functions 2. x používá nové schéma Host. JSON. Hlavní změny od 1. x zahrnují:

* `"storageProvider"`(a `"azureStorage"` dílčí část) pro konfiguraci specifickou pro úložiště.
* `"tracing"`pro trasování a konfiguraci protokolování.
* `"notifications"`(a `"eventGrid"` dílčí část) pro konfiguraci oznámení služby Event Grid.

Podrobnosti najdete v [referenční dokumentaci k Durable Functions Host. JSON](durable-functions-bindings.md#durable-functions-2-0-host-json) .

#### <a name="default-taskhub-name-changes"></a>Výchozí změny názvu taskhub

Pokud v Host. JSON není zadaný název centra úloh, ve verzi 1. x se použila výchozí hodnota "DurableFunctionsHub". Ve verzi 2. x je výchozí název centra úloh odvozený od názvu aplikace Function App. Z tohoto důvodu Pokud jste při upgradu na 2. x nezadali název centra úloh, váš kód bude pracovat s novým centrem úloh a všechny jeho orchestrace v letadle již nebudou aplikace zpracovávat. Pokud chcete tento problém obejít, můžete buď výslovně nastavit název centra úloh na hodnotu v1. x ve výchozím nastavení "DurableFunctionsHub", nebo můžete postupovat podle našeho [Průvodce nasazením s nulovými výpadky](durable-functions-zero-downtime-deployment.md) , kde najdete podrobné informace o tom, jak zpracovávat zásadní změny v rámci letů.

#### <a name="public-interface-changes-net-only"></a>Změny veřejného rozhraní (jenom .NET)

V rozhraní verze 1. x různé objekty _kontextu_ podporované Durable Functions mají abstraktní základní třídy určené pro použití při testování částí. Jako součást Durable Functions 2. x jsou tyto abstraktní základní třídy nahrazeny rozhraními.

Následující tabulka představuje hlavní změny:

| verze | 2.x |
|----------|----------|
| `DurableOrchestrationClientBase` | `IDurableOrchestrationClient` nebo `IDurableClient` |
| `DurableOrchestrationContext` nebo `DurableOrchestrationContextBase` | `IDurableOrchestrationContext` |
| `DurableActivityContext` nebo `DurableActivityContextBase` | `IDurableActivityContext` |
| `OrchestrationClientAttribute` | `DurableClientAttribute` |

V případě, kdy abstraktní základní třída obsahovala virtuální metody, byly tyto virtuální metody nahrazeny metodami rozšíření definovanými `DurableContextExtensions`v.

#### <a name="functionjson-changes-javascript-and-c-script"></a>změny funkce Function. JSON (skript JavaScript a C#)

V Durable Functions 1. x používá `type` vazba klienta Orchestration. `orchestrationClient` Místo toho používá `durableClient` verze 2. x.
