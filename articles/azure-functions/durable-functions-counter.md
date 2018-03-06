---
title: "Stavová jednotlivé prvky v trvanlivý funkce – Azure"
description: "Zjistěte, jak implementovat stavová jednotlivý prvek v rozšíření trvanlivý funkce pro Azure Functions."
services: functions
author: cgillum
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 46cdd8523117e1100e7ce2a29ade9eb2dc0afe75
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2018
---
# <a name="stateful-singletons-in-durable-functions---counter-sample"></a>Stavová jednotlivé prvky v trvanlivý funkce – Ukázka čítače

> [!NOTE]
> Tato ukázka jsme jsou přepisování. Fragmenty kódu byly odebrány a nový článek ukázku nové je nahradí.

Stavová jednotlivé prvky jsou dlouho běžící (potenciálně eternal) orchestrator funkce, které lze uložit stav a vyvolat a zadají dotaz dalších funkcí. Stavová jednotlivé prvky jsou podobné [modelu objektu Actor](https://en.wikipedia.org/wiki/Actor_model) v distribuovaných počítačů.

Při není správné "objektu actor" implementace funkcí orchestrator má mnoho společných vlastností s stejné modulu runtime. Například jsou stavová, spolehlivým, jedním podprocesem, transparentní pro umístění a globálně adresovatelné. Jedná se o charakteristiky, které skutečné objektu actor implementace obzvláště užitečná, ale bez nutnosti samostatné rozhraní.

Tento článek ukazuje, jak spouštět *čítač* ukázka. Ukázka ukazuje objekt typu singleton, která podporuje *přírůstek* a *snížení* operace a aktualizuje jeho vnitřní stav odpovídajícím způsobem.

## <a name="prerequisites"></a>Požadavky

* Postupujte podle pokynů v [nainstalovat trvanlivý funkce](durable-functions-install.md) nastavit vzorku.
* Tento článek předpokládá, že jste již prošli [Hello pořadí](durable-functions-sequence.md) ukázka návod.

## <a name="scenario-overview"></a>Přehled scénáře

Čítač scénář je překvapivě obtížné implementovat pomocí regulárních bezstavové funkcí. Jedním z hlavních výzev máte spravuje **souběžnosti**. Operace, jako *přírůstek* a *snížení* musí být Atomický nebo jinak může dojít ke časování, které způsobí operace vzájemně přepsaly.

Použití jeden virtuální počítač pro hostování data čítače je jednou z možností, ale toto je nákladné a správa **spolehlivost** může být složité, protože jeden virtuální počítač může pravidelně restartovat. Můžete taky použít distribuované platformy s synchronizace nástroje, jako objekt blob zapůjčení ke správě souběžnosti, ale vzniká značnou část **složitost**.

Trvanlivý funkce usnadňuje tento druh scénář trivial implementovat, protože instance orchestration jsou spřažené s jeden virtuální počítač a provádění funkce orchestrator je vždy jednovláknové. Nejenom, ale jsou dlouho běžící, stavová a mohly reagovat na externí události. Následující ukázkový kód ukazuje, jak implementovat čítač, jako dlouho běžící orchestrator funkce.

## <a name="the-sample-function"></a>Funkci sample

Tento článek vás provede **E3_Counter** funkce v ukázkové aplikace.



## <a name="the-counter-orchestration"></a>Čítač orchestration

Následující části popisují kód, který se používá pro vývoj pro Visual Studio Code a portálu Azure.

### <a name="c-script"></a>Skript jazyka C#

Soubor function.json:

<!-- [!code-json[Main](~/samples-durable-functions/samples/csx/E3_Counter/function.json)] -->

Soubor run.csx:

<!-- [!code-csharp[Main](~/samples-durable-functions/samples/csx/E3_Counter/run.csx)] -->

### <a name="precompiled-c"></a>Předkompilované C# 

Následující části popisují kód, který se používá pro vývoj v sadě Visual Studio.

Tady je kód, který implementuje funkce orchestrator:

<!-- [!code-csharp[Main](~/samples-durable-functions/samples/precompiled/Counter.cs)] -->

### <a name="explanation-of-the-code"></a>Vysvětlení kódu

Tato funkce orchestrator v podstatě provede následující akce:

1. Čeká na externí událost s názvem *operace* pomocí [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_).
2. Zvýší nebo sníží `counterState` místní proměnné v závislosti na požadovanou operaci.
3. Restartuje orchestrator pomocí [ContinueAsNew](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_ContinueAsNew_) metodu, nastavení nejnovější hodnotu `counterState` jako nový vstupní.
4. Pokračuje navždy nebo dokud se *end* přijata zpráva.

Toto je příklad *eternal orchestration* &mdash; tedy ten, který končí potenciálně nikdy. Reaguje na zprávy odeslané do ní pomocí [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) metodu, která je možné volat v žádné jiné orchestrator funkce.

Jeden jedinečný znakem tuto funkci orchestrator je, že efektivně nemá žádná historie: `ContinueAsNew` způsob, obnovíte historii po každém zpracování události. Toto je upřednostňovaný způsob implementace orchestrator, který má libovolný životního cyklu. Použití `while` smyčky by mohlo způsobit orchestrator funkce historie narůstá, což vede k zbytečně velké množství paměti.

> [!NOTE]
> `ContinueAsNew` Metoda má jiné případy použití kromě eternal orchestrations. Další informace najdete v tématu [Eternal Orchestrations](durable-functions-eternal-orchestrations.md).

## <a name="run-the-sample"></a>Spuštění ukázky

Orchestration můžete spustit odesláním následující požadavku HTTP POST. Povolit `counterState` spustit na nulu (výchozí hodnota pro `int`), neexistuje žádný obsah v této žádosti.

```
POST http://{host}/orchestrators/E3_Counter
Content-Length: 0
```

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

{
  "id":"bcf6fb5067b046fbb021b52ba7deae5a",
  "statusQueryGetUri":"http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}",
  "sendEventPostUri":"http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}",
  "terminatePostUri":"http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/terminate?reason={text}&taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}"}
```

**E3_Counter** instance spustí a potom okamžitě čeká na událost k odeslání do pomocí `RaiseEventAsync` nebo pomocí **sendEventUrl** webhooku HTTP POST, kterou se odkazuje v 202 odpovědi. Platný `eventName` hodnoty zahrnují *incr*, *decr –*, a *end*.

```
POST http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/raiseEvent/operation?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
Content-Type: application/json
Content-Length: 6

"incr"
```

Se zobrazí výsledky operace "incr" prohlížením protokoly funkcí na portálu Azure Functions.

```
2017-06-29T18:54:53.998 Function started (Id=34e34a61-38b3-4eac-b6e2-98b85e32eec8)
2017-06-29T18:54:53.998 Current counter state is 0. Waiting for next operation.
2017-06-29T18:58:01.458 Function started (Id=b45d6c2f-39f3-42a2-b904-7761b2614232)
2017-06-29T18:58:01.458 Current counter state is 0. Waiting for next operation.
2017-06-29T18:58:01.458 Received 'incr' operation.
2017-06-29T18:58:01.458 Function completed (Success, Id=b45d6c2f-39f3-42a2-b904-7761b2614232, Duration=8ms)
2017-06-29T18:58:11.518 Function started (Id=e1f38cb2-546a-404d-ab22-1ac8f81a93d9)
2017-06-29T18:58:11.518 Current counter state is 1. Waiting for next operation.
```

Podobně pokud můžete zkontrolovat stav orchestrator, zobrazí `input` pole byla nastavena na hodnotu aktualizované (1).

```
GET http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey} HTTP/1.1
```

```
HTTP/1.1 202 Accepted
Content-Length: 129
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

{"runtimeStatus":"Running","input":1,"output":null,"createdTime":"2017-06-29T18:58:01Z","lastUpdatedTime":"2017-06-29T18:58:11Z"}
```

Můžete pokračovat v odesílání nových operací k této instanci a pozorovat, že získá stav příslušným způsobem aktualizuje. Pokud chcete ukončit instanci, můžete tak učinit odesláním *end* operaci.

> [!WARNING]
> V době psaní, jsou známy časování při volání metody `ContinueAsNew` při současné zpracování zprávy, jako je například externí události nebo ukončení požadavky. Nejnovější informace o těchto časování, najdete [potíže Githubu](https://github.com/Azure/azure-functions-durable-extension/issues/67).

## <a name="next-steps"></a>Další postup

Tato ukázka vám ukázal, jak bude zpracováván [externí události](durable-functions-external-events.md) a implementovat [eternal orchestrations](durable-functions-eternal-orchestrations.md) v [stavová jednotlivých prvků](durable-functions-singletons.md). Další příklad ukazuje, jak používat externí události a [trvanlivý časovače](durable-functions-timers.md) pro zpracování zásahem ze strany obsluhy.

> [!div class="nextstepaction"]
> [Spustit ukázku zásahem ze strany](durable-functions-phone-verification.md)
