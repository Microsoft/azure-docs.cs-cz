---
title: Výkon a škálování v Durable Functions – Azure
description: Úvod do rozšíření Durable Functions pro službu Azure Functions.
services: functions
author: cgillum
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/25/2018
ms.author: azfuncdf
ms.openlocfilehash: b7e6a5a4f4e449926bfb63425c2f45bd09f63827
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2018
ms.locfileid: "39214763"
---
# <a name="performance-and-scale-in-durable-functions-azure-functions"></a>Výkon a škálování v Durable Functions (Azure Functions)

K optimalizaci výkonu a škálovatelnosti, je důležité pochopit unikátních vlastnostech škálování [Durable Functions](durable-functions-overview.md).

Informace o tom chování škálování, budete muset pochopit některé podrobnosti podkladového zprostředkovatele služby Azure Storage.

## <a name="history-table"></a>Tabulky historie

**Historie** tabulka je tabulka Azure Storage, který obsahuje historii událostí pro všechny instance Orchestrace úloh centrum. Název této tabulky je ve formě *TaskHubName*historie. Instance spuštění nové řádky se přidají do této tabulky. Klíč oddílu této tabulky je odvozený od ID instance orchestraci. Instance ID je náhodný ve většině případů, které zajišťuje optimální distribuci interních oddílů ve službě Azure Storage.

Když je potřeba spustit instanci Orchestrace, odpovídající řádky v tabulce historie jsou načtena do paměti. Tyto *Historie událostí* se pak znovu přehrát do kódu funkce orchestrátoru jak ji získat zpátky do stavu dříve byl vytvořen kontrolní bod. Použití historie provádění k opětovnému sestavení stavu tímto způsobem je ovlivněno [model Event Sourcing](https://docs.microsoft.com/en-us/azure/architecture/patterns/event-sourcing).

## <a name="instances-table"></a>Instance tabulky

**Instance** jiné tabulky Azure Storage, který obsahuje stavy všech instancí Orchestrace v centru úkolu je tabulka. Jak se vytvářejí instance, nové řádky se přidají do této tabulky. Klíč oddílu v této tabulce je ID instance Orchestrace klíč řádku je dlouhodobý – konstanta Existuje jeden řádek pro každou instanci Orchestrace.

Tato tabulka slouží ke splnění požadavků na dotazy instance z [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_System_String_) rozhraní API také [dotazu na stavovou rozhraní HTTP API](https://docs.microsoft.com/en-us/azure/azure-functions/durable-functions-http-api#get-instance-status). Je udržovat konzistentní s obsahem **historie** tabulka již bylo zmíněno dříve. Použití samostatné tabulky Azure Storage efektivně splňovat operace dotazů instance tímto způsobem je ovlivněno [zodpovědnosti příkazů a dotazů oddělení (CQRS) vzor](https://docs.microsoft.com/en-us/azure/architecture/patterns/cqrs).

## <a name="internal-queue-triggers"></a>Vnitřní fronty aktivační události

Funkce nástroje Orchestrator a funkce aktivity se spouštějí ve vnitřní fronty v centru úloh aplikace function app. Použití fronty tímto způsobem poskytuje spolehlivé zprávy "na alespoň jedno" určitými zárukami. Existují dva typy front v Durable Functions: **řízení fronty** a **pracovní položky fronty**.

### <a name="the-work-item-queue"></a>Pracovní položka fronty

Existuje jedna pracovní položka fronty za Centrum úkolů v Durable Functions. Je základní frontu a chová podobně jako u kteréhokoli jiného `queueTrigger` fronty ve službě Azure Functions. Tato fronta je použít k aktivaci bezstavové *funkce aktivity* podle dequeueing do jedné zprávy najednou. Všechny tyto zprávy obsahují vstupech do aktivity funkce a další metadata, například jaké funkce pro spuštění. Když aplikace Durable Functions škálovat do několika virtuálních počítačů, všechny tyto virtuální počítače soutěžit získat práce z fronty pracovní položku.

### <a name="control-queues"></a>Řízení front

Existuje více *řízení fronty* za úkol Centrum Durable Functions. A *řízení fronty* je složitější než jednodušší fronty pracovní položku. Ovládací prvek fronty slouží k aktivaci funkce stavové orchestrator. Protože funkce instancemi nástroje orchestrator jsou stavová jednotlivých prvků, není možné použít modelu konkurenčních příjemců k distribuci zatížení napříč virtuálními počítači. Místo toho nástroje orchestrator jsou zprávy s vyrovnáváním zatížení napříč řízení front. Další informace o tomto chování najdete v následujících oddílech.

Ovládací prvek fronty obsahují celou řadu typů Orchestrace životní cyklus zpráv. Mezi příklady patří [zpráv s orchestrator ovládacího prvku](durable-functions-instance-management.md), funkce aktivitu *odpovědi* zprávy a zprávy časovače. Až 32 zpráv bude vyjmutou z fronty ovládací prvek v jedné cyklického dotazování. Tyto zprávy obsahují data datové části, jakož i metadata, která instance orchestration je určený pro včetně. Pokud více dequeued zpráv jsou určené pro jednu instanci Orchestrace, budou zpracovávat jako dávku.

## <a name="storage-account-selection"></a>Výběr účtu úložiště

Fronty, tabulky a objekty BLOB využívané Durable Functions jsou vytvořené v nakonfigurovaném účtu Azure Storage. Účet, který chcete použít se dá nastavit pomocí `durableTask/azureStorageConnectionStringName` nastavení **host.json** souboru.

```json
{
  "durableTask": {
    "azureStorageConnectionStringName": "MyStorageAccountAppSetting"
  }
}
```

Pokud není zadán, výchozí `AzureWebJobsStorage` účet úložiště se používá. Pro úlohy náročné na výkon ale účet storage jiné než výchozí konfigurace se doporučuje. Odolná služba Functions často využívá Azure Storage a pomocí vyhrazeného úložiště účtu izoluje využití úložiště Durable Functions z interní využití Azure Functions hostitelem.

## <a name="orchestrator-scale-out"></a>Horizontální navýšení kapacity nástroje Orchestrator

Aktivita funkce jsou bezstavové a horizontálně automaticky přidáním virtuálních počítačů. Funkce nástroje Orchestrator na druhé straně jsou *dělené* napříč jednu či více front ovládacího prvku. Počet front ovládací prvek je definována v **host.json** souboru. Následující příklad host.json fragment kódu nastaví `durableTask/partitionCount` vlastnost `3`.

```json
{
  "durableTask": {
    "partitionCount": 3
  }
}
```
Centra úloh může mít nakonfigurovanou mezi 1 a 16 oddíly. Pokud není zadán, výchozí počet oddílů je **4**.

Při horizontálním navýšení kapacity na několik instancí hostitele – funkce (obvykle v jiných virtuálních počítačů), každá instance získá zámek na jednom front ovládacího prvku. Tyto zámky se implementují interně jako objekt blob úložiště zapůjčení a ujistěte se, že instance Orchestrace pouze na jednom hostiteli instance byla najednou spuštěna. Pokud úloha centrum je nakonfigurováno pomocí tří řízení front, Orchestrace instance může být s vyrovnáváním zatížení ve virtuálních počítačích až tři. Ke zvýšení kapacity pro provedení aktivity funkce lze přidat další virtuální počítače.

Následující diagram znázorňuje interakci hostitele Azure Functions s entitami úložiště v prostředí s horizontálně.

![Diagram škálování](media/durable-functions-perf-and-scale/scale-diagram.png)

Jak je znázorněno na předchozím obrázku, všechny virtuální počítače soutěžit o zprávy do fronty pracovní položku. Však pouze tři virtuální počítače můžete získat zprávy z fronty ovládacího prvku a každý virtuální počítač uzamkne queue jeden ovládací prvek.

Orchestrace instancí se distribuují napříč všemi instancemi fronty ovládacího prvku. Distribuce se provádí pomocí algoritmu hash ID instance orchestraci. ID instance ve výchozím nastavení se náhodný GUID, zajištění, že instance jsou rovnoměrně distribuované napříč všechny fronty ovládacího prvku.

Obecně řečeno funkce nástroje orchestrator by měla být jednoduché a velké množství výpočetní výkon, neměli byste potřebovat. Proto není nutné vytvářet velký počet ovládací prvek oddílů, fronty a zajistit si skvělé propustnost. Většina náročné práce by mělo být provedeno funkce bezstavové aktivity, které lze škálovat nekonečně.

## <a name="auto-scale"></a>Automatické škálování

Jak se všechny funkce Azure běžící v plánu Consumption, Durable Functions podporuje automatické škálování prostřednictvím [řadič škálování Azure Functions](functions-scale.md#runtime-scaling). Měřítka řadiče monitoruje latence všechny fronty pravidelně vydáním _Náhled_ příkazy. Na základě latence peeked zprávy, měřítka řadiče se rozhodnout, jestli se má přidat nebo odebrat virtuální počítače.

Pokud měřítka řadiče zjistí, že jsou příliš vysoká. řízení fronty zpráv latenci, přidá dokud latence zpráva poklesne pod přijatelnou úroveň nebo dosáhne počet oddílů řízení fronty instancí virtuálních počítačů. Obdobně měřítka řadiče se průběžně přidávají instancí virtuálních počítačů Pokud latence fronty pracovních položek jsou vysoká, bez ohledu na to, počet oddílů.

## <a name="thread-usage"></a>Využití vlákna

Funkce nástroje Orchestrator jsou spuštěny v jednom vlákně zajistit, že spuštění může být deterministický napříč mnoha riziko. Z důvodu spuštění tento jednovláknový je důležité, že vlákna funkce produktu orchestrator není provádět úlohy náročné na CPU, provádět vstupně-výstupní operace nebo blokovat z jakéhokoli důvodu. Veškerá práce, která může vyžadovat vstupně-výstupní operace blokuje, nebo přesunout do funkce aktivity více vláken.

Aktivita funkce mají stejné chování jako běžné funkce aktivované fronty. Mohou bezpečně provádět vstupně-výstupních operací, provést operace náročné na CPU a používejte více vláken. Protože aktivity aktivační události jsou bezstavové, se můžou volně horizontální navýšení kapacity na množství virtuálních počítačů.

## <a name="concurrency-throttles"></a>Omezení souběžnosti

Služba Azure Functions podporuje spuštění více funkcí současně v rámci aplikace s jedním instance. Tato souběžné spouštění vám pomůže zvýšit paralelismu a minimalizuje počet "souvisejícím s úplným spuštěním", které Typická aplikace dojde v čase. Vysoká souběžnosti však může vést k využití paměti vysokou jednotlivé virtuální počítače. V závislosti na potřebách aplikace function app může být potřeba omezení souběžnosti jednotlivé instance, aby možnost paměti v situacích, vysokým zatížením.

Oba limity aktivita funkce a nástroje orchestrator funkce souběžnosti se dá nakonfigurovat v **host.json** souboru. Jsou příslušné nastavení `durableTask/maxConcurrentActivityFunctions` a `durableTask/maxConcurrentOrchestratorFunctions` v uvedeném pořadí.

```json
{
  "durableTask": {
    "maxConcurrentActivityFunctions": 10,
    "maxConcurrentOrchestratorFunctions": 10,
  }
}
```

V předchozím příkladu maximálně 10 orchestrator 10 aktivity funkcí a současně na jeden virtuální počítač. Pokud není zadán, počet souběžných spuštění funkce aktivity a orchestrator je omezené na 10 × počet jader virtuálního počítače.

> [!NOTE]
> Tato nastavení jsou užitečné ke správě paměti a využití procesoru na jeden virtuální počítač. Ale při škálované na víc virtuálních počítačů, každý virtuální počítač bude mít svou vlastní sadu omezení. Tato nastavení nelze použít k řízení souběžnosti na globální úrovni.

## <a name="orchestrator-function-replay"></a>Funkce opakování nástroje Orchestrator
Jak už bylo zmíněno dříve, jsou funkcí nástroje orchestrator přehrály pomocí obsahu **historie** tabulky. Ve výchozím nastavení je kód funkce orchestrátoru přehrály pokaždé, když jsou odstraněné z fronty dávku zpráv z fronty ovládacího prvku.

Tím, že se dají zakázat toto chování opakování agresivní **rozšířené relace**. Pokud jsou povolené rozšířené relace, funkce instancemi nástroje orchestrator jsou uložené v paměti, kterou déle a nové zprávy může zpracovat bez úplné opakování. Rozšířené relace se povoluje nastavením `durableTask/extendedSessionsEnabled` k `true` v **host.json** souboru. `durableTask/extendedSessionIdleTimeoutInSeconds` Nastavení se používá k řízení dobu nečinnosti relace se bude vysílat v paměti:

```json
{
  "durableTask": {
    "extendedSessionsEnabled": true,
    "extendedSessionIdleTimeoutInSeconds": 30
  }
}
```

Typické účinku povolení rozšířené relace se snižuje vstupně-výstupní operace proti účtu úložiště Azure a celkové lepší výkon.

Jeden potenciální Nevýhodou této funkce je však této funkce nečinnosti orchestrator instance zůstanou v paměti delší. Existují dva důsledky zajímat:

1. Celkové zvýšení využití paměti v aplikaci funkce.
2. Celkově pokles propustnosti při provádění funkcí mnoho souběžných, krátkodobých nástroje orchestrator.

Například pokud `durableTask/extendedSessionIdleTimeoutInSeconds` nastavená na 30 sekund, funkce epizodě krátkodobou orchestrator, který se spustí v menší než 1 sekundu stále zabírat paměti po dobu 30 sekund. Bude také započítávat `durableTask/maxConcurrentOrchestratorFunctions` kvóty již bylo zmíněno dříve, potenciálně brání spuštění jiné funkce nástroje orchestrator.

> [!NOTE]
> Toto nastavení by měla sloužit pouze po funkce orchestrátoru je úplně vývoji a testování. Výchozí chování agresivní opakování je vhodný pro detekci chyb idempotence funkcí nástroje orchestrator v době vývoje.

## <a name="performance-targets"></a>Cíle výkonnosti

Pokud plánujete použít Durable Functions pro produkční aplikace, je důležité vzít v úvahu požadavky na výkon v rané fázi procesu plánování. Tento oddíl popisuje některé scénáře základní informace o využití a očekávané maximální propustnosti čísla.

* **Sekvenční aktivita provádění**: Tento scénář popisuje funkce orchestrátoru, který spouští řadu funkce aktivity jednu po druhé. Nejlépe odpovídá [řetězení funkce](durable-functions-sequence.md) vzorku.
* **Paralelní provádění aktivity**: Tento scénář popisuje funkce orchestrátoru, který se spustí paralelní pomocí mnoho funkcí aktivity [větveného, jinak](durable-functions-cloud-backup.md) vzor.
* **Paralelní zpracování odpovědi**: Tento scénář je v druhé polovině [větveného, jinak](durable-functions-cloud-backup.md) vzor. Zaměřuje se na výkon jinak. Je důležité si uvědomit, že na rozdíl od větveného, jinak se provádí funkce instancí jedné orchestrator a proto dají spustit jenom na jeden virtuální počítač.
* **Zpracování událostí externí**: Tento scénář představuje instanci funkce nástroje orchestrator jeden, který čeká na [externí události](durable-functions-external-events.md), postupně po jednom.

> [!TIP]
> Na rozdíl od větveného jinak operace jsou omezené na jeden virtuální počítač. Pokud vaše aplikace používá větveného, jinak vzor a máte obavy o výkonu jinak, vezměte v úvahu dílčího dělení větveného funkce aktivity napříč více [dílčí Orchestrace](durable-functions-sub-orchestrations.md).

V následující tabulce jsou uvedeny očekávané *maximální* čísla propustnost pro scénáře bylo popsáno dříve. "Instance" odkazuje na jednu instanci funkce orchestrátoru běžící na jeden malý ([A1](../virtual-machines/windows/sizes-previous-gen.md#a-series)) virtuálních počítačů ve službě Azure App Service. Ve všech případech se předpokládá, že [rozšířené relace](#orchestrator-function-replay) jsou povolené. Skutečné výsledky se mohou lišit v závislosti na využití procesoru nebo vstupně-výstupní operace prováděné pomocí kódu funkce.

| Scénář | Maximální propustnost |
|-|-|
| Provádění sekvenční aktivity | 5 aktivit za sekundu na instanci |
| Spuštění paralelní aktivita (větveného) | 100 aktivit za sekundu na instanci |
| Paralelní zpracování odpovědi (jinak) | 150 odpovědi za sekundu na instanci |
| Zpracování externího událostí | 50 událostí za sekundu na instanci |

> [!NOTE]
> Tato čísla jsou aktuální v době v1.4.0 (GA) verzi rozšíření Durable Functions. Tato čísla může postupem času změnit zrání funkce a optimalizace probíhají.

Pokud nevidíte očekáváte, že čísla propustnost a procesoru a využití paměti, zobrazí se v pořádku, zkontrolujte, jestli příčinou souvisí s [stavu vašeho účtu úložiště](../storage/common/storage-monitoring-diagnosing-troubleshooting.md#troubleshooting-guidance). Durable Functions, které rozšíření můžete umístit významné zatížení na účtu služby Azure Storage a dostatečně vysoké zatížení může vést k omezení účtu úložiště.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Instalace rozšíření Durable Functions a ukázky](durable-functions-install.md)
