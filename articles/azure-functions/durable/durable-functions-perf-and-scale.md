---
title: Výkon a škálování v Durable Functions – Azure
description: Úvod do rozšíření Durable Functions pro Azure Functions
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: azfuncdf
ms.openlocfilehash: ed0fe22903412d4164fb3a85dbd9afafdc7023e6
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70098002"
---
# <a name="performance-and-scale-in-durable-functions-azure-functions"></a>Výkon a škálování v Durable Functions (Azure Functions)

Pro optimalizaci výkonu a škálovatelnosti je důležité pochopit jedinečné charakteristiky škálování [Durable Functions](durable-functions-overview.md).

Pro pochopení chování škálování je třeba pochopit některé podrobnosti o základním poskytovateli Azure Storage.

## <a name="history-table"></a>Tabulka historie

Tabulka **Historie** je Azure Storage tabulka, která obsahuje události historie pro všechny instance orchestrace v rámci centra úloh. Název této tabulky je ve formátu *TaskHubName*historie. Po spuštění instancí se do této tabulky přidají nové řádky. Klíč oddílu této tabulky je odvozen z ID instance orchestrace. ID instance je ve většině případů náhodné, což zajišťuje optimální distribuci vnitřních oddílů v Azure Storage.

Je-li nutné spustit instanci orchestrace, jsou do paměti načteny příslušné řádky tabulky historie. Tyto *události historie* se pak přehrají do kódu funkce Orchestrator a vrátí se zpátky do předchozího kontrolního stavu. Použití historie spouštění k opětovnému sestavení tohoto způsobu je ovlivněno [vzorem zdroje události](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing).

## <a name="instances-table"></a>Tabulka instancí

Tabulka **Instances** je další Azure Storage tabulka, která obsahuje stavy všech instancí orchestrace v rámci centra úloh. Při vytváření instancí se do této tabulky přidají nové řádky. Klíč oddílu této tabulky je ID instance Orchestrace a klíč řádku je pevná konstanta. Existuje jeden řádek na instanci orchestrace.

Tato tabulka slouží k uspokojení požadavků na dotazování instance z rozhraní API [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_System_String_) (.NET `getStatus` ) a (JavaScript) a také pro [dotaz na stavový protokol HTTP API](durable-functions-http-api.md#get-instance-status). Je trvale konzistentní s obsahem výše zmíněné tabulky **Historie** . Použití samostatné Azure Storage tabulky k efektivnímu uspokojení operací dotazů na instance tímto způsobem je ovlivněno [vzorem CQRS (Command and Query Responsibility segregation) (CQRS)](https://docs.microsoft.com/azure/architecture/patterns/cqrs).

## <a name="internal-queue-triggers"></a>Interní triggery front

Funkce a funkce nástroje Orchestrator jsou spouštěny interními frontami v centru úloh aplikace Function App. Použití front tímto způsobem poskytuje spolehlivé záruky doručení zpráv "nejméně jednou". Existují dva typy front v Durable Functions: **frontu řízení** a **frontu pracovních položek**.

### <a name="the-work-item-queue"></a>Fronta pracovních položek

K dispozici je jedna fronta pracovních položek na každé centrum úloh v Durable Functions. Jedná se o základní frontu, která se chová podobně jako `queueTrigger` jakákoli jiná fronta v Azure Functions. Tato fronta se používá k aktivaci bezstavových *funkcí aktivity* tím, že v jednom okamžiku odřadí jedinou zprávu. Každá z těchto zpráv obsahuje vstupy funkcí aktivity a další metadata, jako je například funkce, kterou je třeba provést. Když Durable Functions aplikace škáluje na více virtuálních počítačů, všechny tyto virtuální počítače budou chtít získat práci z fronty pracovních položek.

### <a name="control-queues"></a>Počet front ovládacích prvků:

K dispozici je více *řídicích front* pro každé centrum úloh v Durable Functions. *Fronta ovládacího prvku* je výkonnější než jednodušší fronta pracovních položek. Řídicí fronty se používají ke spuštění stavových funkcí nástroje Orchestrator. Vzhledem k tomu, že instance funkcí Orchestrator jsou stavové singleton, není možné použít konkurenční model uživatele k distribuci zatížení napříč virtuálními počítači. Místo toho jsou zprávy nástroje Orchestrator vyrovnávány napříč řídicími frontami. Další podrobnosti o tomto chování najdete v následujících oddílech.

Řídicí fronty obsahují nejrůznější typy zpráv o životním cyklu orchestrace. Příklady zahrnují [zprávy řízení nástroje Orchestrator](durable-functions-instance-management.md), zprávy s odezvou funkcí aktivity a zprávy časovače. Ve frontě ovládacích prvků v jednom cyklickém dotazování bude z fronty ovládacího prvku odřazena spousta zpráv 32. Tyto zprávy obsahují data datové části i metadata, včetně toho, pro kterou instanci orchestrace je určena. Je-li pro stejnou instanci orchestrace určena více zpráv ve frontě, budou zpracovány jako dávka.

### <a name="queue-polling"></a>Cyklické dotazování front

Rozšíření odolné úlohy implementuje náhodný exponenciální algoritmus pro snížení vlivu nečinnosti na transakční dotaz na náklady transakce úložiště. Po nalezení zprávy modul runtime hned vyhledá jinou zprávu; Pokud se nenajde žádná zpráva, počká na určitou dobu, než se zkusí znovu. Po následných neúspěšných pokusech o získání zprávy fronty se doba čekání zvětšuje, dokud nedosáhne maximální čekací doby, která má výchozí hodnotu 30 sekund.

Maximální zpoždění cyklického dotazování lze konfigurovat prostřednictvím `maxQueuePollingInterval` vlastnosti v [souboru Host. JSON](../functions-host-json.md#durabletask). Nastavení této hodnoty na vyšší hodnotu může mít za následek vyšší latence při zpracování zpráv. Vyšší latence by se měly očekávat jenom po období nečinnosti. Nastavení této hodnoty na nižší hodnotu může mít za následek vyšší náklady na úložiště kvůli zvýšeným transakcím úložiště.

> [!NOTE]
> Při spuštění v rámci plánů Azure Functions a Premium se kontroler [Azure Functions Scale](../functions-scale.md#how-the-consumption-and-premium-plans-work) bude dotazovat každý ovládací prvek a frontu pracovních položek každých 10 sekund. Toto další cyklické dotazování je nezbytné k určení, kdy se mají aktivovat instance aplikace Function App a provádět rozhodování o škálování. V době psaní je tento 10 sekundový interval konstantní a nedá se nakonfigurovat.

## <a name="storage-account-selection"></a>Výběr účtu úložiště

Fronty, tabulky a objekty blob používané nástrojem Durable Functions jsou vytvořeny v nakonfigurovaném Azure Storagem účtu. Účet, který se má použít, se dá `durableTask/azureStorageConnectionStringName` zadat pomocí nastavení v souboru **Host. JSON** .

### <a name="functions-1x"></a>Functions 1.x

```json
{
  "durableTask": {
    "azureStorageConnectionStringName": "MyStorageAccountAppSetting"
  }
}
```

### <a name="functions-2x"></a>Functions 2.x

```json
{
  "extensions": {
    "durableTask": {
      "azureStorageConnectionStringName": "MyStorageAccountAppSetting"
    }
  }
}
```

Pokud není zadaný, použije se `AzureWebJobsStorage` výchozí účet úložiště. Pro úlohy citlivé na výkon se ale doporučuje nakonfigurovat účet úložiště, který není výchozí. Durable Functions používá Azure Storage silně a používání vyhrazeného účtu úložiště izoluje Durable Functions využití úložiště od interního využití hostitelem Azure Functions.

## <a name="orchestrator-scale-out"></a>Škálování na více instancí Orchestrator

Funkce aktivity jsou bezstavové a automaticky se škálují s přidáním virtuálních počítačů. Funkce Orchestrator funguje na druhé straně v rámci jedné nebo více front ovládacích prvků. Počet front řízení je definován v souboru **Host. JSON** . Následující příklad fragmentu Host. JSON nastaví `durableTask/partitionCount` vlastnost na `3`hodnotu.

### <a name="functions-1x"></a>Functions 1.x

```json
{
  "durableTask": {
    "partitionCount": 3
  }
}
```

### <a name="functions-2x"></a>Functions 2.x

```json
{
  "extensions": {
    "durableTask": {
      "partitionCount": 3
    }
  }
}
```

Centrum úloh se dá nakonfigurovat s mezi 1 a 16 oddíly. Pokud není zadaný, výchozí počet oddílů je **4**.

Při horizontálním navýšení kapacity na více instancí hostitelů funkcí (obvykle na různých virtuálních počítačích) získá každá instance zámek jedné z řídicích front. Tyto zámky se interně implementují jako zapůjčení úložiště objektů BLOB a zajišťují, že instance orchestrace se spouští jenom na jedné instanci hostitele v jednom okamžiku. Pokud je Centrum úkolů nakonfigurované se třemi řídicími frontami, instance orchestrace se můžou vyrovnávat zatížením až po třech virtuálních počítačích. Další virtuální počítače je možné přidat za účelem zvýšení kapacity pro provádění funkcí aktivity.

Následující diagram znázorňuje, jak se hostitel Azure Functions komunikuje s entitami úložiště v prostředí s možností horizontálního rozšíření kapacity.

![Měřítko diagramu](./media/durable-functions-perf-and-scale/scale-diagram.png)

Jak je znázorněno na předchozím diagramu, všechny virtuální počítače soutěží o zprávy ve frontě pracovní položky. Z řídicích front ale můžou získat zprávy jenom tři virtuální počítače a každý virtuální počítač zamkne jednu frontu ovládacích prvků.

Instance orchestrace jsou distribuovány napříč všemi instancemi fronty ovládacích prvků. Distribuce se provádí pomocí hashování ID instance orchestrace. ID instancí ve výchozím nastavení jsou náhodné identifikátory GUID, které zajišťují, aby se instance rovnoměrně rozdělují mezi všechny fronty ovládacích prvků.

Obecně řečeno, funkce nástroje Orchestrator mají být odlehčené a neměly by vyžadovat velké množství výpočetní síly. Proto není nutné vytvářet velký počet oddílů front řízení pro dosažení skvělé propustnosti. Většina těžkých prací by se měla provádět v bezstavových funkcích aktivity, které se dají škálovat nekonečně.

## <a name="auto-scale"></a>Automatické škálování

Stejně jako u všech Azure Functions běžících v plánu spotřeby Durable Functions podporuje automatické škálování prostřednictvím [řadiče Azure Functions škálování](../functions-scale.md#runtime-scaling). Kontroler škálování monitoruje latenci všech front tím, že pravidelně vydává příkazy pro _prohlížení_ . V závislosti na latencích prohlížených zpráv se kontroler škálování rozhodne, jestli se mají přidat nebo odebrat virtuální počítače.

Pokud kontroler škálování zjistí, že latence zpráv řízení fronty je příliš vysoká, přidá instance virtuálních počítačů, dokud se latence zprávy nesníží na přijatelnou úroveň, nebo dosáhne počtu oddílů fronty řízení. Podobně, kontroler škálování průběžně přidá instance virtuálních počítačů, pokud jsou vysoké latence fronty pracovních položek, bez ohledu na počet oddílů.

## <a name="thread-usage"></a>Využití vlákna

Funkce nástroje Orchestrator jsou spouštěny v jednom vlákně, aby bylo zajištěno, že provádění může být deterministické v mnoha opakováních. Z důvodu tohoto spuštění s jedním vláknem je důležité, aby vlákna funkcí Orchestrator neprováděla úlohy náročné na procesor, prováděla vstupně-výstupní operace nebo blokovat z jakéhokoli důvodu. Všechny práce, které mohou vyžadovat vstupně-výstupní operace, blokování nebo více vláken, by měly být přesunuty do funkcí aktivity.

Funkce aktivit mají stejné chování jako běžné funkce aktivované frontou. Můžou bezpečně dělat vstupně-výstupní operace, provádět operace náročné na procesor a používat víc vláken. Protože triggery aktivit jsou bezstavové, můžou volně škálovat na neohraničený počet virtuálních počítačů.

## <a name="concurrency-throttles"></a>Omezení souběžnosti

Azure Functions podporuje souběžné provádění více funkcí v rámci jedné instance aplikace. Toto souběžné provádění pomáhá zvýšit paralelismus a minimalizuje počet "studených startů", na které bude Typická aplikace v průběhu času docházet. Vysoká souběžnost ale může mít za následek vysoké využití paměti na virtuálním počítači. V závislosti na potřebách aplikace Function App může být nutné omezit souběžnost na jednotlivé instance, aby nedocházelo k možnosti vycházet z paměti v situacích s vysokým zatížením.

V souboru **Host. JSON** lze konfigurovat jak funkci aktivity, tak omezení souběžnosti funkcí Orchestrator. Příslušná nastavení jsou `durableTask/maxConcurrentActivityFunctions` `durableTask/maxConcurrentOrchestratorFunctions` v uvedeném pořadí.

### <a name="functions-1x"></a>Functions 1.x

```json
{
  "durableTask": {
    "maxConcurrentActivityFunctions": 10,
    "maxConcurrentOrchestratorFunctions": 10
  }
}
```

### <a name="functions-2x"></a>Functions 2.x

```json
{
  "extensions": {
    "durableTask": {
      "maxConcurrentActivityFunctions": 10,
      "maxConcurrentOrchestratorFunctions": 10
    }
  }
}
```

V předchozím příkladu může maximálně 10 funkcí Orchestrator a 10 funkcí pro aktivity běžet na jednom virtuálním počítači současně. Pokud není zadán, počet souběžných aktivit a spuštění funkcí Orchestrator je omezené na 10X počet jader virtuálního počítače.

> [!NOTE]
> Tato nastavení jsou užitečná při správě paměti a využití CPU na jednom virtuálním počítači. Ale při horizontálním navýšení kapacity mezi několika virtuálními počítači bude mít každý virtuální počítač vlastní sadu omezení. Tato nastavení nelze použít k řízení souběžnosti na globální úrovni.

## <a name="orchestrator-function-replay"></a>Funkce Orchestrator – přehrání

Jak bylo zmíněno dříve, funkce Orchestrator se přehrávají pomocí obsahu tabulky **Historie** . Ve výchozím nastavení se kód funkce nástroje Orchestrator přehraje pokaždé, když se dávka zpráv z fronty ovládacího prvku odřadí.

Toto agresivní chování při opakovaném přehrání lze zakázat povolením **rozšířených relací**. Když je povolená rozšířená relace, instance funkcí Orchestrator se uchovávají v paměti déle a nové zprávy je možné zpracovat bez úplného přehrání. Rozšířené relace jsou povolené nastavením `durableTask/extendedSessionsEnabled` na `true` v souboru **Host. JSON** . `durableTask/extendedSessionIdleTimeoutInSeconds` Nastavení slouží k řízení doby, po kterou se nečinná relace uchovává v paměti:

### <a name="functions-1x"></a>Functions 1.x

```json
{
  "durableTask": {
    "extendedSessionsEnabled": true,
    "extendedSessionIdleTimeoutInSeconds": 30
  }
}
```

### <a name="functions-2x"></a>Functions 2.x

```json
{
  "extensions": {
    "durableTask": {
      "extendedSessionsEnabled": true,
      "extendedSessionIdleTimeoutInSeconds": 30
    }
  }
}
```

Typický efekt povolení rozšířených relací se snižuje v/v proti účtu Azure Storage a celkově lepší propustnost.

Ale jedním z možných Nevýhodou této funkce je, že nečinné instance funkcí Orchestrator zůstanou v paměti déle. Existují dva účinky, které je potřeba mít na paměti:

1. Celkové zvýšení využití paměti aplikace Function App
2. Celkové snížení propustnosti v případě, že existuje mnoho souběžných a krátkodobých spuštění funkcí Orchestrator.

Pokud `durableTask/extendedSessionIdleTimeoutInSeconds` je například nastaveno na 30 sekund, pak krátká funkce Orchestrator, která se provede za méně než 1 sekundou, bude i nadále zabírat paměť po dobu 30 sekund. Také se bude počítat s `durableTask/maxConcurrentOrchestratorFunctions` výše uvedenou kvótou, která potenciálně brání spuštění dalších funkcí nástroje Orchestrator.

> [!NOTE]
> Tato nastavení by měla být použita pouze poté, co byla funkce Orchestrator plně vyvinuta a testována. Výchozí agresivní chování při přehrání je užitečné pro detekci chyb idempotence ve funkcích Orchestrator v době vývoje.

## <a name="performance-targets"></a>Cíle výkonnosti

Když plánujete použít Durable Functions pro produkční aplikaci, je důležité zvážit požadavky na výkon v brzké době v procesu plánování. Tato část se zabývá některými základními scénáři použití a očekávanými maximálními čísly propustnosti.

* **Provádění sekvenční aktivity**: Tento scénář popisuje funkci nástroje Orchestrator, která spouští řadu funkcí aktivity jednu po druhé. Nejlépe se podobá ukázce [řetězení funkcí](durable-functions-sequence.md) .
* **Paralelní provádění aktivit**: Tento scénář popisuje funkci Orchestrator, která paralelně spouští mnoho funkcí aktivity s využitím modelu ventilátoru s [ventilátory](durable-functions-cloud-backup.md) .
* **Paralelní zpracování odpovědí**: Tento scénář je druhá polovina vzoru ventilátoru [a ventilátoru](durable-functions-cloud-backup.md) . Zaměřuje se na výkon ventilátoru. Je důležité si uvědomit, že na rozdíl od ventilátoru se ventilátor používá v jedné instanci funkce Orchestrator, a proto může běžet jenom na jednom virtuálním počítači.
* **Externí zpracování událostí**: Tento scénář představuje jednu instanci funkce Orchestrator, která čeká na [externí události](durable-functions-external-events.md), a to v jednom okamžiku.

> [!TIP]
> Na rozdíl od ventilátorů jsou operace ventilátoru omezené na jeden virtuální počítač. Pokud vaše aplikace využívá vzorek ventilátoru a máte obavy o výkon ventilátoru, zvažte rozdělení funkce aktivity na více [dílčích orchestrací](durable-functions-sub-orchestrations.md).

Následující tabulka uvádí očekávaná *maximální* čísla propustnosti pro dříve popsané scénáře. "Instance" odkazuje na jednu instanci funkce Orchestrator běžící na jednom malém virtuálním počítači ([a1](../../virtual-machines/windows/sizes-previous-gen.md#a-series)) v Azure App Service. Ve všech případech se předpokládá, že jsou povolené [Rozšířené relace](#orchestrator-function-replay) . Skutečné výsledky se můžou lišit v závislosti na využití procesoru nebo vstupně-výstupní práci prováděné kódem funkce.

| Scénář | Maximální propustnost |
|-|-|
| Provádění sekvenční aktivity | 5 aktivit za sekundu, na instanci |
| Paralelní provádění aktivit (ventilátor-out) | 100 aktivit za sekundu, na instanci |
| Paralelní zpracování odpovědí (ventilátor-in) | 150 odpovědí za sekundu, na instanci |
| Externí zpracování událostí | 50 událostí za sekundu, na instanci |

> [!NOTE]
> Tato čísla jsou aktuální pro vydání rozšíření Durable Functions v 1.4.0 (GA). Tato čísla se můžou v průběhu času měnit, protože se tato funkce prosazuje a optimalizace se provedou.

Pokud se vám nezobrazují počty propustnosti, které očekáváte, a využití procesoru a paměti se zobrazí v pořádku, zkontrolujte, jestli příčina souvisí s [stavem vašeho účtu úložiště](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#troubleshooting-guidance). Rozšíření Durable Functions může vést k výraznému zatížení pro Azure Storage účet a dostatečně vysoké zatížení může mít za následek omezení účtu úložiště.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Vytvoření první odolné funkce v jazyce C#](durable-functions-create-first-csharp.md)
