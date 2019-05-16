---
title: Odolná služba Functions funkce – Azure Functions ve verzi preview.
description: Další informace o funkce ve verzi preview pro Durable Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.date: 04/23/2019
ms.author: azfuncdf
ms.openlocfilehash: 8ceb84ab9e9c41ff6a9cbde62571fb12ae67d790
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2019
ms.locfileid: "65596079"
---
# <a name="durable-functions-20-preview-azure-functions"></a>Náhled odolné Functions 2.0 (Azure Functions)

*Odolná služba Functions* je rozšířením [Azure Functions](../functions-overview.md) a [Azure WebJobs](../../app-service/web-sites-create-web-jobs.md) , který umožňuje zapisovat stavové funkce v prostředí bez serveru. Toto rozšíření za vás spravuje stav, kontrolní body a restartování. Pokud již nejste obeznámeni s Durable Functions, přečtěte si článek [přehled dokumentace](durable-functions-overview.md).

Odolná služba Functions je funkce Azure Functions GA (obecně dostupné), ale také obsahuje několik tyto položky, které jsou aktuálně ve verzi public preview. Tento článek popisuje funkce nově vydané ve verzi preview a začnou podrobnosti o tom, jak fungují a jak můžete začít používat je.

> [!NOTE]
> Tyto funkce verze preview jsou součástí vydání trvalý Functions 2.0, která je v současnosti **verze alfa kvality** s několika nejnovější změny. Azure Functions trvalý balíček rozšíření sestavení můžete najít na nuget.org s verzí v podobě **2.0.0-alpha**. Tato sestavení nejsou vhodná pro všechny úlohy v produkčním prostředí a dalších verzích může obsahovat další nejnovější změny.

## <a name="breaking-changes"></a>Změny způsobující chyby

Několik rozbíjející změny jsou uvedeny v trvalé Functions 2.0. Existující aplikace se neočekává kompatibilní s trvalý Functions 2.0 bez jakýchkoli změn kódu. Tato část uvádí některé změny:

### <a name="dropping-net-framework-support"></a>Vyřazení podpory rozhraní .NET Framework

Podpora pro rozhraní .NET Framework (a tedy funkce 1.0) byla vyřazena pro trvalý Functions 2.0. Primárním důvodem, proč je umožnit přispěvatelům bez Windows umožňuje snadno sestavovat a testovat změny, které využívají Durable Functions z platforem Linux a macOS. Sekundární důvod je pomoct podpořit vývojářům přesunout na nejnovější verzi modulu runtime Azure Functions.

### <a name="hostjson-schema"></a>Schéma Host.JSON

Následující fragment kódu ukazuje nové schéma pro host.json. Hlavní změny mít na paměti, je nový `"storageProvider"` části a `"azureStorage"` části pod ním. Tato změna byla provedena pro podporu [alternativní poskytovatelé úložiště](durable-functions-preview.md#alternate-storage-providers).

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": <string>,
      "storageProvider": {
        "azureStorage": {
          "connectionStringName": <string>,
          "controlQueueBatchSize": <int?>,
          "partitionCount": <int?>,
          "controlQueueVisibilityTimeout": <hh:mm:ss?>,
          "workItemQueueVisibilityTimeout": <hh:mm:ss?>,
          "trackingStoreConnectionStringName": <string?>,
          "trackingStoreNamePrefix": <string?>,
          "maxQueuePollingInterval": <hh:mm:ss?>
        }
      },
      "maxConcurrentActivityFunctions": <int?>,
      "maxConcurrentOrchestratorFunctions": <int?>,
      "traceInputAndOutputs": <bool?>,
      "eventGridTopicEndpoint": <string?>,
      "eventGridKeySettingName": <string?>,
      "eventGridPublishRetryCount": <string?>,
      "eventGridPublishRetryInterval": <hh:mm:ss?>,
      "eventGridPublishRetryHttpStatus": <int[]?>,
      "eventgridPublishEventTypes": <string[]?>,
      "customLifeCycleNotificationHelperType"
      "extendedSessionsEnabled": <bool?>,
      "extendedSessionIdleTimeoutInSeconds": <int?>,
      "logReplayEvents": <bool?>
  }
}
```

Trvalý 2.0 funkce se stále stabilizovat a další změny budou zavedeny do `durableTask` části host.json. Další informace o těchto změnách najdete v tématu [tento problém Githubu](https://github.com/Azure/azure-functions-durable-extension/issues/641).

### <a name="public-interface-changes"></a>Změny veřejné rozhraní

Různé objekty "kontext" odolná služba Functions podporuje měly abstraktní základní třídy, které jsou určeny k použití v testování částí. Jako součást trvalý Functions 2.0 tyto abstraktní základní třídy se nahrazuje rozhraní. Kód funkce, která používá přímo na konkrétní typy nejsou ovlivněny.

Následující tabulka představuje hlavní změny:

| Starý typ | Nový typ |
|----------|----------|
| DurableOrchestrationClientBase | IDurableOrchestrationClient |
| DurableOrchestrationContextBase | IDurableOrchestrationContext |
| DurableActivityContextBase | IDurableActivityContext |

V případě, kde jsou obsaženy abstraktní základní třídy virtuální metody, jsme nahradili tyto virtuální metody rozšiřující metody definované v `DurableContextExtensions`.

## <a name="entity-functions"></a>Funkce entity

Funkce entity definování operací pro čtení a aktualizaci malých kousků stav, označuje jako *trvalý entity*. Podobně jako funkce nástroje orchestrator, funkce entity jsou funkce s typem speciální aktivační událost *entity trigger*. Na rozdíl od funkce nástroje orchestrator a funkce entity nemají omezeními konkrétního kódu. Funkce entity také spravovat stav explicitně místo implicitně představující stav prostřednictvím toku řízení.

Následující kód je příklad funkce jednoduchou entitu, která definuje *čítač* entity. Funkce definuje tří operací, `add`, `subtract`, a `reset`, které aktualizace celočíselnou hodnotu, `currentValue`.

```csharp
[FunctionName("Counter")]
public static async Task Counter(
    [EntityTrigger] IDurableEntityContext ctx)
{
    int currentValue = ctx.GetState<int>();
    int operand = ctx.GetInput<int>();

    switch (ctx.OperationName)
    {
        case "add":
            currentValue += operand;
            break;
        case "subtract":
            currentValue -= operand;
            break;
        case "reset":
            await SendResetNotificationAsync();
            currentValue = 0;
            break;
    }

    ctx.SetState(currentValue);
}
```

Entity *instance* jsou přístupné prostřednictvím jedinečný identifikátor *entity ID*. Entity ID je jednoduše dvojice řetězců, které jednoznačně identifikuje instanci entity. Skládá se z:

1. **název entity**: název, který identifikuje typ entity (například "Čítač")
2. **klíč entity**: řetězec, který jednoznačně identifikuje entitu mezi všechny entity se stejným názvem (například identifikátor GUID)

Například *čítač* entity funkce může být použit pro zachování skóre v online hry. Každý výskyt hry budou mít ID jedinečné entity, jako například `@Counter@Game1`, `@Counter@Game2`, a tak dále.

### <a name="comparison-with-virtual-actors"></a>Porovnání s virtuální actors

Návrh odolných entity je výrazně ovlivněno [model objektu actor](https://en.wikipedia.org/wiki/Actor_model). Pokud jste již obeznámeni s objektů actor, musí být známými koncepci trvanlivé entity. Zejména je podobný jako trvalý entity [virtuální actors](https://research.microsoft.com/en-us/projects/orleans/) mnoha způsoby:

* Trvalý entity jsou adresovatelné prostřednictvím *entity ID*.
* Trvalý entity operace spustí sériově, postupně, aby se zabránilo konfliktům časování.
* Trvalý entity jsou vytvořeny automaticky, když se volá nebo signalizován.
* Při provádění není operace, trvalý entity jsou tiše uvolněna z paměti.

Existuje několik důležitých rozdílů, ale které stojí za zmínku:

* Trvalý entity jsou modelovány jako čistě funkce. Tento návrh se liší od Většina objektově orientované rozhraní, které představují aktéry s využitím podpory pro konkrétní jazyk pro třídy, vlastnosti a metody.
* Prioritizujte trvalý entity *odolnosti* přes *latence*a proto nemusí být vhodné pro aplikace s požadavky na latenci strict.
* Zprávy odesílané mezi entitami jsou poskytována spolehlivě a v pořadí.
* Trvalý entity lze použít ve spojení s orchestrací odolné a může sloužit jako distribuované zámky, které jsou popsány dále v tomto článku.
* Žádost/odpověď vzory v entity jsou omezené na Orchestrace. Pro komunikaci entity entity jsou povoleny pouze jednosměrné zprávy (označované také jako "signalizace") jako v původním model objektu actor. Toto chování brání distribuované zablokování.

### <a name="durable-entity-apis"></a>Trvalý Entity rozhraní API

Entita podpora zahrnuje několik rozhraní API. Za prvé je nové rozhraní API pro definici funkce entity, jak je uvedeno výše, která zadejte, co se stane při vyvolání operace s entitou. Navíc stávajících rozhraní API pro klienty a Orchestrace byly aktualizovány s novými funkcemi pro interakci s entitami.

### <a name="implementing-entity-operations"></a>Provádění operací entity

Provádění operací na entity můžete volat tyto členy objektu context (`IDurableEntityContext` v rozhraní .NET):

* **OperationName**: získá název operace.
* **GetInput\<T >**: získá vstupu pro tuto operaci.
* **GetState\<T >**: získá aktuální stav entity.
* **SetState**: aktualizuje stav entity.
* **SignalEntity**: odešle jednosměrná zpráva do entity.
* **Self**: získá ID entity.
* **Vrátí**: vrací hodnotu, klienta nebo Orchestrace, která volá operace.
* **IsNewlyConstructed**: vrátí `true` Pokud entita neexistuje před operaci.
* **DestructOnExit**: Po dokončení operace odstraní entitu.

Operace omezeny méně než Orchestrace:

* Operace může volat externí vstupně-výstupních operací, pomocí synchronní nebo asynchronní rozhraní API (vám doporučujeme používat pouze asynchronní ty).
* Operace může být Nedeterministický. Například je bezpečné volat `DateTime.UtcNow`, `Guid.NewGuid()` nebo `new Random()`.

### <a name="accessing-entities-from-clients"></a>Přístup k entity z klientů

Trvalý entity lze volat z běžné funkce prostřednictvím `orchestrationClient` vazby (`IDurableOrchestrationClient` v rozhraní .NET). Jsou podporovány následující metody:

* **ReadEntityStateAsync\<T >**: načte stav entity.
* **SignalEntityAsync**: odešle jednosměrná zpráva do entity a čeká na jeho bude zařazených do fronty.

Tyto metody upřednostnit výkonu přes konzistence: `ReadEntityStateAsync` může vrátit hodnotu zastaralé a `SignalEntityAsync` může vrátit před dokončením operace. Volání entity z Orchestrace (jak je popsáno dále) je naproti tomu vysoce konzistentní.

### <a name="accessing-entities-from-orchestrations"></a>Přístup k entit na základě Orchestrace

Orchestrace přístupná entit s využitím objektu kontextu. Můžete vybrat mezi jednosměrnou komunikaci (vypal a zapomeň) a obousměrnou komunikaci (požadavku a odpovědi). Jsou příslušné metody

* **SignalEntity**: odešle jednosměrná zpráva do entity.
* **CallEntityAsync**: odešle zprávu do entity a čeká na odpověď označující, že operace byla dokončena.
* **CallEntityAsync\<T >**: odešle zprávu do entity a čeká na odpověď obsahující výsledek typu T.

Při použití obousměrnou komunikaci, zůstanou veškeré výjimky vyvolané při provádění operace jsou také odeslaných zpět do volajícího Orchestrace a znovu vyvolána. Naopak při použití fire a zapomenout, nejsou dodrženy výjimky.

### <a name="locking-entities-from-orchestrations"></a>Uzamčení entity z Orchestrace

Orchestrace lze uzamčení entity. Tato funkce poskytuje jednoduchý způsob, jak zabránit nežádoucí bude pomocí *kritických oddílů*.

Objekt kontextu poskytuje následující metody:

* **LockAsync**: získá uzamčení na jeden nebo více entit.
* **Islocked –**: vrací hodnotu true, pokud v současné době kritický oddíl, false v opačném případě.

Kritická sekce skončí a všech zámků jsou uvolněny, při ukončení orchestraci. V rozhraní .NET `LockAsync` vrátí `IDisposable` , který končí kritický oddíl při uvolnění, což je možné společně s `using` klauzule syntaktické reprezentaci kritický oddíl.

Například vezměte v úvahu Orchestrace, kterou je potřeba otestovat, jestli jsou k dispozici dva hráči a následně je přiřadit k hru. Tato úloha je možné implementovat pomocí kritický oddíl následujícím způsobem:

```csharp
[FunctionName("Orchestrator")]
public static async Task RunOrchestrator(
    [OrchestrationTrigger] IDurableOrchestrationContext ctx)
{
    EntityId player1 = /* ... */;
    EntityId player2 = /* ... */;

    using (await ctx.LockAsync(player1, player2))
    {
        bool available1 = await ctx.CallEntityAsync<bool>(player1, "is-available");
        bool available2 = await ctx.CallEntityAsync<bool>(player2, "is-available");

        if (available1 && available2)
        {
            Guid gameId = ctx.NewGuid();

            await ctx.CallEntityAsync(player1, "assign-game", gameId);
            await ctx.CallEntityAsync(player2, "assign-game", gameId);
        }
    }
}
```

V rámci kritický oddíl obě player entity jsou zamčené, což znamená, že jejich nejsou provádění jakékoli operace než ty, které se volají z v rámci kritický oddíl). Toto chování brání bude s konfliktními operacemi, jako je například hráči přiřazeny jiným hru nebo podpisové vypnuté.

Jsme několik omezení v části Jak kritické je možné. Tato omezení slouží k zabránit zablokování a konflikty vícenásobného přístupu.

* Kritické oddíly nelze vnořit.
* Kritické oddíly nelze vytvořit suborchestrations.
* Kritické oddíly mohou volat pouze entity, které mají uzamčen.
* Kritické oddíly nelze volat stejné entity pomocí více paralelních volání.
* Kritické oddíly mohou signalizovat pouze entity, které nebyly uzamčen.

## <a name="alternate-storage-providers"></a>Poskytovatelé alternativní úložiště

Trvalý Framework úkol podporuje několik poskytovatelů úložiště ještě dnes, včetně [služby Azure Storage](https://github.com/Azure/durabletask/tree/master/src/DurableTask.AzureStorage), [Azure Service Bus](https://github.com/Azure/durabletask/tree/master/src/DurableTask.ServiceBus), [emulátor v paměti](https://github.com/Azure/durabletask/tree/master/src/DurableTask.Emulator)a experimentální [Redis](https://github.com/Azure/durabletask/tree/redis/src/DurableTask.Redis) zprostředkovatele. Ale až doteď trvalý úloh rozšíření pro službu Azure Functions jen zprostředkovatele služby Azure Storage. Počínaje trvalý Functions 2.0, podporu pro poskytovatele alternativní úložiště přidáte, od poskytovatele Redis.

> [!NOTE]
> Trvalý Functions 2.0 podporuje pouze .NET Standard 2.0 kompatibilní zprostředkovatelé. V době psaní zprostředkovatele služby Azure Service Bus nepodporuje .NET Standard 2.0 a není tedy k dispozici jako alternativní úložiště zprostředkovatele.

### <a name="emulator"></a>Emulátor

[DurableTask.Emulator](https://www.nuget.org/packages/Microsoft.Azure.DurableTask.Emulator/) zprostředkovatele je místní paměti, hodí se pro místní scénáře testování poskytovatele krátkodobé úložiště. Můžete ji nakonfigurovat, použijte tento minimální **host.json** schématu:

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": <string>,
      "storageProvider": {
        "emulator": { }
      }
    }
  }
}
```

### <a name="redis-experimental"></a>Redis (experimentální)

[DurableTask.Redis](https://www.nuget.org/packages/Microsoft.Azure.DurableTask.Redis/) poskytovatele nevyřeší všechny stavy Orchestrace do konfigurovaného clusteru Redis.

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": <string>,
      "storageProvider": {
        "redis": {
          "connectionStringName": <string>,
        }
      }
    }
  }
}
```

`connectionStringName` Musí odkazovat na název proměnné aplikace nastavení nebo prostředí. Tato proměnná nastavení nebo prostředí aplikace by měl obsahovat hodnotu připojovacího řetězce Redis ve formě *server: port*. Například `localhost:6379` pro připojení k místnímu clusteru Redis.

> [!NOTE]
> Zprostředkovatel Redis je aktuálně experimentální a podporuje pouze aplikace function App běží na jednom uzlu.
