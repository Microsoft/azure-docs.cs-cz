---
title: Přehled verzí Durable Functions – Azure Functions
description: Přečtěte si o Durable Functions verzích.
author: cgillum
ms.topic: conceptual
ms.date: 12/23/2020
ms.author: azfuncdf
ms.openlocfilehash: c4d10bab06428295bbc8c5319bd47787d7b1fb34
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97763366"
---
# <a name="durable-functions-versions-overview"></a>Přehled verzí Durable Functions

*Durable Functions* je rozšíření [Azure Functions](../functions-overview.md) a [Azure WebJobs](../../app-service/webjobs-create.md) , které umožňuje psát stavové funkce v prostředí bez serveru. Toto rozšíření za vás spravuje stav, kontrolní body a restartování. Pokud ještě neznáte Durable Functions, přečtěte si [dokumentaci přehled](durable-functions-overview.md).

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

Do projektu nainstalujte nejnovější verzi 2. x rozšíření Durable Functions vazby.

#### <a name="javascript-python-and-powershell"></a>JavaScript, Python a PowerShell

Durable Functions 2. x je k dispozici ve verzi 2. x sady [rozšíření Azure Functions](../functions-bindings-register.md#extension-bundles).

Podpora Pythonu v Durable Functions vyžaduje Durable Functions 2. x.

Chcete-li aktualizovat verzi balíčku rozšíření v projektu, otevřete host.jsv části a aktualizujte `extensionBundle` oddíl na použití verze 2. x ( `[2.*, 3.0.0)` ).

```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[2.*, 3.0.0)"
    }
}
```

> [!NOTE]
> Pokud Visual Studio Code po změně verze balíčku rozšíření nezobrazuje správné šablony, znovu načtěte okno spuštěním příkazu *vývojář: znovu načíst okno* (<kbd>CTRL + R</kbd> v systému Windows a Linux, <kbd>Command + R</kbd> v MacOS).

#### <a name="net"></a>.NET

Aktualizujte projekt .NET tak, aby používal nejnovější verzi [rozšíření Durable Functions vazby](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask).

Další informace najdete v tématu věnovaném [registraci rozšíření vazby Azure Functions](../functions-bindings-register.md#local-csharp) .

### <a name="update-your-code"></a>Aktualizace kódu

Durable Functions 2. x přináší několik přerušujících změn. Aplikace Durable Functions 1. x nejsou kompatibilní s Durable Functions 2. x bez změny kódu. V této části jsou uvedené některé změny, které je třeba provést při upgradu funkcí verze 1. x na 2. x.

#### <a name="hostjson-schema"></a>Host.jsve schématu

Durable Functions 2. x používá nový host.jsve schématu. Hlavní změny od 1. x zahrnují:

* `"storageProvider"` (a `"azureStorage"` dílčí část) pro konfiguraci specifickou pro úložiště.
* `"tracing"` pro trasování a konfiguraci protokolování.
* `"notifications"` (a `"eventGrid"` dílčí část) pro konfiguraci oznámení služby Event Grid.

Podrobnosti najdete v tématu [Durable Functions host.jsv referenční dokumentaci](durable-functions-bindings.md#durable-functions-2-0-host-json) .

#### <a name="default-taskhub-name-changes"></a>Výchozí změny názvu taskhub

Pokud ve verzi 1. x není název centra úloh zadaný v host.jsna, byl nastaven jako výchozí na hodnotu "DurableFunctionsHub". Ve verzi 2. x je výchozí název centra úloh odvozený od názvu aplikace Function App. Z tohoto důvodu Pokud jste při upgradu na 2. x nezadali název centra úloh, váš kód bude pracovat s novým centrem úloh a všechny jeho orchestrace v letadle již nebudou aplikace zpracovávat. Pokud chcete tento problém obejít, můžete buď výslovně nastavit název centra úloh na hodnotu v1. x ve výchozím nastavení "DurableFunctionsHub", nebo můžete postupovat podle našeho [Průvodce nasazením s nulovými výpadky](durable-functions-zero-downtime-deployment.md) , kde najdete podrobné informace o tom, jak zpracovávat zásadní změny v rámci letů.

#### <a name="public-interface-changes-net-only"></a>Změny veřejného rozhraní (jenom .NET)

V rozhraní verze 1. x různé objekty _kontextu_ podporované Durable Functions mají abstraktní základní třídy určené pro použití při testování částí. Jako součást Durable Functions 2. x jsou tyto abstraktní základní třídy nahrazeny rozhraními.

Následující tabulka představuje hlavní změny:

| verze | 2.x |
|----------|----------|
| `DurableOrchestrationClientBase` | `IDurableOrchestrationClient` nebo `IDurableClient` |
| `DurableOrchestrationContext` nebo `DurableOrchestrationContextBase` | `IDurableOrchestrationContext` |
| `DurableActivityContext` nebo `DurableActivityContextBase` | `IDurableActivityContext` |
| `OrchestrationClientAttribute` | `DurableClientAttribute` |

V případě, kdy abstraktní základní třída obsahovala virtuální metody, byly tyto virtuální metody nahrazeny metodami rozšíření definovanými v `DurableContextExtensions` .

#### <a name="functionjson-changes-javascript-and-c-script"></a>function.jspři změnách (skript JavaScript a C#)

V Durable Functions 1. x používá vazba klienta Orchestration `type` `orchestrationClient` . Místo toho používá verze 2. x `durableClient` .

#### <a name="raise-event-changes"></a>Vyvolat změny událostí

V Durable Functions 1. x volání rozhraní API pro [vyvolání události](durable-functions-external-events.md#send-events) a zadání instance, která neexistovala, vedlo k chybě v tichém režimu. Počínaje 2. x je výsledkem vyvolání události neexistující orchestrace výjimka.
