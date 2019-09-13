---
title: Funkce ve verzi Preview Durable Functions – Azure Functions
description: Přečtěte si o funkcích verze Preview pro Durable Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: article
ms.date: 09/04/2019
ms.author: azfuncdf
ms.openlocfilehash: 8f2560141eac4e7d9fed267d347990e65bfe9c26
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2019
ms.locfileid: "70933242"
---
# <a name="durable-functions-20-preview-azure-functions"></a>Durable Functions 2,0 Preview (Azure Functions)

*Durable Functions* je rozšíření [Azure Functions](../functions-overview.md) a [Azure WebJobs](../../app-service/web-sites-create-web-jobs.md) , které umožňuje psát stavové funkce v prostředí bez serveru. Toto rozšíření za vás spravuje stav, kontrolní body a restartování. Pokud ještě neznáte Durable Functions, přečtěte si [dokumentaci přehled](durable-functions-overview.md).

Durable Functions 1. x je funkce Azure Functions GA (všeobecně dostupná), ale také obsahuje několik dílčích funkcí, které jsou aktuálně ve verzi Public Preview. Tento článek popisuje nově vydané funkce verze Preview a obsahuje podrobnosti o tom, jak fungují a jak je můžete začít používat.

> [!NOTE]
> Tyto funkce verze Preview jsou součástí vydání Durable Functions 2,0, což je aktuálně **verze Preview** s několika zásadními změnami. Sestavení balíčku Azure Functions trvalého rozšíření lze najít v nuget.org s verzemi ve formě **2.0.0-betaX**. Tato sestavení nejsou určena pro produkční úlohy a následné verze mohou obsahovat další zásadní změny.

## <a name="breaking-changes"></a>Změny způsobující chyby

V Durable Functions 2,0 se zavádí několik přerušujících změn. Neočekává se, že existující aplikace budou kompatibilní s Durable Functions 2,0 bez změny kódu. V této části jsou uvedené některé změny:

### <a name="hostjson-schema"></a>Schéma Host. JSON

Následující fragment kódu ukazuje nové schéma pro Host. JSON. Hlavní změny, které je třeba znát, jsou nové pododdíly:

* `"storageProvider"`(a `"azureStorage"` dílčí část) pro konfiguraci specifickou pro úložiště
* `"tracking"`pro sledování a konfiguraci protokolování
* `"notifications"`(a `"eventGrid"` dílčí část) pro konfiguraci oznámení služby Event Grid

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
      "tracking": {
        "traceInputsAndOutputs": <bool?>,
        "traceReplayEvents": <bool?>,
      },
      "notifications": {
        "eventGrid": {
          "topicEndpoint": <string?>,
          "keySettingName": <string?>,
          "publishRetryCount": <string?>,
          "publishRetryInterval": <hh:mm:ss?>,
          "publishRetryHttpStatus": <int[]?>,
          "publishEventTypes": <string[]?>,
        }
      },
      "maxConcurrentActivityFunctions": <int?>,
      "maxConcurrentOrchestratorFunctions": <int?>,
      "extendedSessionsEnabled": <bool?>,
      "extendedSessionIdleTimeoutInSeconds": <int?>,
      "customLifeCycleNotificationHelperType": <string?>
  }
}
```

Jak Durable Functions 2,0 pokračuje ve stabilizaci, do `durableTask` oddílu Host. JSON budou zavedeny další změny. Další informace o těchto změnách najdete v [tomto problému GitHubu](https://github.com/Azure/azure-functions-durable-extension/issues/641).

### <a name="public-interface-changes"></a>Změny veřejného rozhraní

Různé "kontextové" objekty podporované Durable Functions měly abstraktní základní třídy určené pro použití při testování částí. Jako součást Durable Functions 2,0 byly tyto abstraktní základní třídy nahrazeny rozhraními. Kód funkce, který používá konkrétní typy přímo, není ovlivněn.

Následující tabulka představuje hlavní změny:

| Starý typ | Nový typ |
|----------|----------|
| DurableOrchestrationClientBase | IDurableOrchestrationClient |
| DurableOrchestrationContextBase | IDurableOrchestrationContext |
| DurableActivityContextBase | IDurableActivityContext |
| OrchestrationClientAttribute | DurableClientAttribute |

V případě, kdy abstraktní základní třída obsahovala virtuální metody, byly tyto virtuální metody nahrazeny metodami rozšíření definovanými `DurableContextExtensions`v.

## <a name="entity-functions"></a>Funkce entit

Počínaje Durable Functions v 2.0.0-alfa jsme představili nový koncept [funkcí entity Functions](durable-functions-entities.md) .

Funkce entit definují operace pro čtení a aktualizaci malých částí stavu, označovaných jako *odolné entity*. Podobně jako funkce nástroje Orchestrator jsou funkce entit funkce se speciálním typem triggeru, *triggerem entity*. Na rozdíl od funkcí Orchestrator nemají entity Functions žádná konkrétní omezení kódu. Funkce entit také spravují stav explicitně namísto implicitního reprezentace stavu prostřednictvím řízení toku.

Na základě úvodní zpětné vazby od uživatele jsme později přidali podporu pro programovací model založený na třídě pro entity v Durable Functions v 2.0.0-Beta1.

Další informace najdete v článku věnovaném [funkcím entit](durable-functions-entities.md) .

## <a name="durable-http"></a>Trvalá HTTP

Počínaje Durable Functions v 2.0.0-beta2 jsme zavedli novou funkci [trvalého http](durable-functions-http-features.md) , která vám umožní:

* Volání rozhraní API HTTP přímo z funkcí orchestrace (s některými dokumentovanými omezeními)
* Implementuje Automatické dotazování na stav HTTP 202 na straně klienta.
* Integrovaná podpora [spravovaných identit Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

Další informace najdete v článku věnovaném [funkcím protokolu HTTP](durable-functions-http-features.md#consuming-http-apis) .

## <a name="alternate-storage-providers"></a>Alternativní poskytovatelé úložiště

Prostředí trvalého úkolu podporuje více poskytovatelů úložiště, včetně [Azure Storage](https://github.com/Azure/durabletask/tree/master/src/DurableTask.AzureStorage), [Azure Service Bus](https://github.com/Azure/durabletask/tree/master/src/DurableTask.ServiceBus), emulátoru [v paměti](https://github.com/Azure/durabletask/tree/master/src/DurableTask.Emulator)a experimentálního poskytovatele [Redis](https://github.com/Azure/durabletask/tree/redis/src/DurableTask.Redis) . Nicméně až do této chvíle bude rozšíření trvalé úlohy pro Azure Functions podporovalo pouze poskytovatele Azure Storage. Počínaje Durable Functions 2,0 se přidávají podpora alternativních poskytovatelů úložiště, počínaje poskytovatelem Redis.

> [!NOTE]
> Durable Functions 2,0 podporuje pouze poskytovatele kompatibilního s .NET Standard 2,0.

### <a name="emulator"></a>Hlavní

Zprostředkovatel [DurableTask. emulátoru](https://www.nuget.org/packages/Microsoft.Azure.DurableTask.Emulator/) je místní paměť, neodolný poskytovatel úložiště vhodný pro místní testovací scénáře. Dá se nakonfigurovat pomocí následujícího schématu minimale **Host. JSON** :

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": <string>,
      "storageProvider": {
        "emulator": { "enabled": true }
      }
    }
  }
}
```

### <a name="redis-experimental"></a>Redis (experimentální)

Poskytovatel [DurableTask. Redis](https://www.nuget.org/packages/Microsoft.Azure.DurableTask.Redis/) uchovává všechny stavy orchestrace v nakonfigurovaném clusteru Redis.

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

`connectionStringName` Musí odkazovat na název nastavení aplikace nebo proměnné prostředí. Toto nastavení aplikace nebo proměnná prostředí by měla obsahovat hodnotu připojovacího řetězce Redis ve formě *serveru: port*. Například `localhost:6379` pro připojení k místnímu clusteru Redis.

> [!NOTE]
> Poskytovatel Redis je aktuálně experimentální a podporuje jenom aplikace Function App běžící na jednom uzlu. Není zaručeno, že poskytovatel Redis bude někdy zpřístupněn všeobecně k dispozici a bude možné ho v budoucí verzi odebrat.
