---
title: Přehled verzí trvalých funkcí – Funkce Azure
description: Další informace o verzích durable functions.
author: cgillum
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: azfuncdf
ms.openlocfilehash: 4a117e7f69647af3ad82f9013bfa40556ccc0dbd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77152886"
---
# <a name="durable-functions-versions-overview"></a>Přehled verzí trvalých funkcí

*Trvalé funkce* je rozšíření [Azure Functions](../functions-overview.md) a [Azure WebJobs,](../../app-service/web-sites-create-web-jobs.md) které vám umožní psát stavové funkce v prostředí bez serveru. Toto rozšíření za vás spravuje stav, kontrolní body a restartování. Pokud ještě nejste obeznámeni s trvanlivé funkce, naleznete v [dokumentaci k přehledu](durable-functions-overview.md).

## <a name="new-features-in-2x"></a>Nové funkce v 2.x

Tato část popisuje funkce trvalé funkce, které jsou přidány ve verzi 2.x.

### <a name="durable-entities"></a>Trvalé entity

V aplikaci Durable Functions 2.x jsme představili novou [koncepci funkcí entity.](durable-functions-entities.md)

Funkce entity definují operace pro čtení a aktualizaci malých částí stavu, označované jako *trvalé entity*. Podobně jako funkce orchestratoru jsou funkce entity funkce mise se speciálním typem aktivační události, *aktivační událostí entity*. Na rozdíl od funkcí orchestrator funkce entity nemají žádné konkrétní omezení kódu. Funkce entity také spravovat stav explicitně, nikoli implicitně představující stav prostřednictvím toku řízení.

Další informace najdete v článku [trvalé entity.](durable-functions-entities.md)

### <a name="durable-http"></a>Odolný HTTP

V aplikaci Durable Functions 2.x jsme představili novou [odolnou](durable-functions-http-features.md#consuming-http-apis) funkci HTTP, která umožňuje:

* Volání http api přímo z funkcí orchestrace (s některými zdokumentovanými omezeními).
* Implementujte automatické dotazování stavu HTTP 202 na straně klienta.
* Integrovaná podpora [spravovaných identit Azure](../../active-directory/managed-identities-azure-resources/overview.md).

Další informace najdete v článku [funkce protokolu HTTP.](durable-functions-http-features.md#consuming-http-apis)

## <a name="migrate-from-1x-to-2x"></a>Migrace z 1.x na 2.x

Tato část popisuje, jak migrovat existující verzi 1.x Durable Functions na verzi 2.x, abyste využili výhod nových funkcí.

### <a name="upgrade-the-extension"></a>Upgrade rozšíření

Nainstalujte verzi 2.x [rozšíření vazby trvalé funkce](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) v projektu. Další informace najdete [v tématu Register Azure Functions binding extensions](../functions-bindings-register.md) for more information.

### <a name="update-your-code"></a>Aktualizace kódu

Odolné funkce 2.x přináší několik průlomových změn. Odolné aplikace 1.x nejsou kompatibilní s durable functions 2.x bez změn kódu. V této části jsou uvedeny některé změny, které je třeba provést při inovaci funkcí verze 1.x na verzi 2.x.

#### <a name="hostjson-schema"></a>Schéma hostitele.json

Trvalé funkce 2.x používá nové schéma host.json. Mezi hlavní změny od 1.x patří:

* `"storageProvider"`(a `"azureStorage"` podsekce) pro konfiguraci specifickou pro úložiště.
* `"tracing"`pro trasování a protokolování konfigurace.
* `"notifications"`(a `"eventGrid"` podsekce) pro konfiguraci oznámení mřížky událostí.

Podrobnosti naleznete v [referenční dokumentaci k tématu Durable Functions host.json.](durable-functions-bindings.md#durable-functions-2-0-host-json)

#### <a name="default-taskhub-name-changes"></a>Výchozí změny názvu taskhubu

Ve verzi 1.x pokud název centra úloh nebyl zadán v host.json, byl výchozí "DurableFunctionsHub". Ve verzi 2.x je nyní výchozí název centra úloh odvozen od názvu aplikace funkce. Z tohoto důvodu pokud jste nezadali název centra úloh při upgradu na 2.x, váš kód bude pracovat s novým centrem úloh a všechny in-flight orchestrations již nebude mít aplikaci jejich zpracování. Chcete-li tento problém vyřešit, můžete buď explicitně nastavit název centra úloh na výchozí v1.x "DurableFunctionsHub", nebo můžete postupovat podle našich pokynů pro [nasazení s nulovými prostoji,](durable-functions-zero-downtime-deployment.md) kde najdete podrobnosti o tom, jak zpracovat narušující změny pro orchestraci za letu.

#### <a name="public-interface-changes-net-only"></a>Změny veřejného rozhraní (pouze rozhraní.NET)

Ve verzi 1.x mají různé _kontextové_ objekty podporované funkcemi Durable abstraktní základní třídy určené pro použití při testování částí. Jako součást trvanlivé funkce 2.x tyto abstraktní základní třídy jsou nahrazeny rozhraní.

Následující tabulka představuje hlavní změny:

| 1.x | 2.x |
|----------|----------|
| `DurableOrchestrationClientBase` | `IDurableOrchestrationClient` nebo `IDurableClient` |
| `DurableOrchestrationContext` nebo `DurableOrchestrationContextBase` | `IDurableOrchestrationContext` |
| `DurableActivityContext` nebo `DurableActivityContextBase` | `IDurableActivityContext` |
| `OrchestrationClientAttribute` | `DurableClientAttribute` |

V případě, kdy abstraktní základní třída obsahovala virtuální metody, byly tyto `DurableContextExtensions`virtuální metody nahrazeny rozšiřujícími metodami definovanými v .

#### <a name="functionjson-changes-javascript-and-c-script"></a>function.json changes (JavaScript a C# Script)

V trvalé funkce 1.x, orchestrace `type` klienta vazba používá a . `orchestrationClient` Místo toho používá `durableClient` verzi 2.x.
