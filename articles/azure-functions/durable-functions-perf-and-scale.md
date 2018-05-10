---
title: Výkonu a možností škálování trvanlivý funkcí – Azure
description: Úvod do rozšíření trvanlivý funkce pro Azure Functions.
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
ms.openlocfilehash: 18a3f3080c58e01117e0fc73adad76d28c298536
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="performance-and-scale-in-durable-functions-azure-functions"></a>Výkonu a možností škálování trvanlivý funkcí (Azure Functions)

K optimalizaci výkonu a škálovatelnosti, je důležité si uvědomit, jedinečné škálování charakteristika [trvanlivý funkce](durable-functions-overview.md).

Pochopit chování škálování, je nutné pochopit podrobnosti odpovídající zprostředkovatel úložiště Azure.

## <a name="history-table"></a>Tabulka historie

**Historie** tabulka je tabulka Azure Storage, která obsahuje historie událostí pro všechny instance orchestration v centru úloh. Název této tabulky je ve formátu *TaskHubName*historie. Jak spustit instance, přidávání řádků do této tabulky. Klíč oddílu této tabulky je odvozený od instance ID orchestration. Instance ID je náhodný ve většině případů, které zajistí optimální distribuční interní oddílů ve službě Azure Storage.

Pokud orchestration instance je potřeba spustit, jsou do paměti bylo načteno odpovídající řádky v tabulce historie. Tyto *Historie událostí* jsou poté znovu nahrány do kódu funkce orchestrator získat zpět do stavu dříve vytvořeného kontrolního bodu. Použití historie provádění znovu sestavit stavu tímto způsobem je ovlivněno [Sourcing událostí vzor](https://docs.microsoft.com/en-us/azure/architecture/patterns/event-sourcing).

## <a name="instances-table"></a>Instance tabulky

**Instance** tabulka je jiný Azure Storage tabulku, která obsahuje stavy všech instancí orchestration v centru úloh. Při vytváření instancí, se řešení přidá nové řádky do této tabulky. Klíč oddílu v této tabulce je orchestration instance ID a klíč řádku je argumentem pevnou konstanta. Neexistuje jeden řádek pro každou instanci orchestration.

Tato tabulka se používá pro uspokojení požadavků na dotazy instance z [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_System_String_) rozhraní API a taky [dotazu na stav rozhraní API HTTP](https://docs.microsoft.com/en-us/azure/azure-functions/durable-functions-http-api#get-instance-status). Je udržováno nakonec byl konzistentní s obsahem **historie** tabulka již bylo zmíněno dříve. Použití samostatné tabulky Azure Storage efektivně vyhovět operace dotazů instance tímto způsobem je ovlivněno [příkazů a dotazů odpovědnost oddělení (CQRS) vzor](https://docs.microsoft.com/en-us/azure/architecture/patterns/cqrs).

## <a name="internal-queue-triggers"></a>Aktivační události vnitřní fronty

Orchestrator funkce a funkce aktivity se spouštějí ve vnitřní fronty v aplikaci funkce úkolů rozbočovače. Pomocí front tímto způsobem poskytuje spolehlivé zpráva "v aspoň jednou" zárukami. Existují dva typy front trvanlivý funkcí: **fronty řízení** a **fronty pracovní položku**.

### <a name="the-work-item-queue"></a>Fronty pracovní položku

Je jedna fronta pracovní položky na Centrum úkolů trvanlivý funkcí. Je základní frontu a žádné jiné se chová podobně jako `queueTrigger` fronty v Azure Functions. Tato fronta se používá k aktivaci bezstavové *aktivita funkce* podle dequeueing do jedné zprávy najednou. Všechny tyto zprávy obsahují funkce vstupech do aktivity a další metadata, jako je například funkce, jejíž spuštění. Pokud aplikace trvanlivý funkce horizontálně navýší kapacitu na víc virtuálních počítačů, všechny tyto virtuální počítače se pokouší získat pracovní z fronty pracovní položku.

### <a name="control-queues"></a>Ovládací prvek fronty

Existuje více *řízení front* za úloh centra trvanlivý funkce. A *fronty řízení* je složitější než jednodušší fronty pracovní položku. Řízení front se používají k aktivaci funkce stavová orchestrator. Protože instance funkce orchestrator stavová jednotlivých prvků, není možné použít model konkurence mezi spotřebiteli distribuovat zatížení napříč virtuálními počítači. Místo toho orchestrator zprávy jsou vyrovnávání zatížení napříč řízení front. Další informace o toto chování naleznete v následujících částech.

Řízení front obsahovat celou řadu typů zpráv orchestration životního cyklu. Mezi příklady patří [zprávy ovládacího prvku orchestrator](durable-functions-instance-management.md), aktivita funkce *odpovědi* zprávy a časovač zprávy. Až 32 zprávy budou vyjmutou z fronty ovládací prvek v jednom dotazování. Tyto zprávy obsahují datové části, jakož i včetně kterou instanci orchestration je určený pro metadata. Pokud více dequeued zpráv jsou určené pro stejnou instanci orchestration, budou zpracovány jako dávku.

## <a name="storage-account-selection"></a>Výběr účtu úložiště

Fronty, tabulky a objekty BLOB využívané trvanlivý funkce jsou vytvořené v nakonfigurovaném účtu úložiště Azure. Účet, který chcete použít, je možné zadat pomocí `durableTask/azureStorageConnectionStringName` nastavení v **host.json** souboru.

```json
{
  "durableTask": {
    "azureStorageConnectionStringName": "MyStorageAccountAppSetting"
  }
}
```

Pokud není zadáno, výchozí `AzureWebJobsStorage` se používá účet úložiště. Pro úlohy náročné na výkon ale konfigurace účtu jiné než výchozí úložiště doporučujeme. Trvanlivý funkce výraznou používá Azure Storage a pomocí účtu vyhrazeného úložiště izoluje využití úložiště odolné funkce z interní použití hostitelem Azure Functions.

## <a name="orchestrator-scale-out"></a>Orchestrator Škálováním na více systémů

Aktivita funkce jsou bezstavové a škálovaný automaticky přidáním virtuálních počítačů. Funkce produktu Orchestrator na druhé straně jsou *oddílů* napříč jednu či více front ovládacího prvku. Počet front ovládací prvek je definována v **host.json** souboru. Následující příklad host.json fragment kódu nastavuje `durableTask/partitionCount` vlastnost `3`.

```json
{
  "durableTask": {
    "partitionCount": 3
  }
}
```
Rozbočovač úloh se dá nakonfigurovat s mezi 1 a 16 oddíly. Pokud není zadáno, je výchozí počet oddílu **4**.

Při zmenšování měřítka na více instancí hostitele – funkce (obvykle na různé virtuální počítače), každá instance získá zámku na jednom front ovládacího prvku. Tyto zámky interně jsou implementované jako objekt blob úložiště zapůjčení a ujistěte se, že orchestration instance pouze na jednom hostiteli instanci najednou spuštěna. Pokud rozbočovač úkolů je nakonfigurované tři řízení front, orchestration instance může být vyrovnávání zatížení napříč až tři virtuální počítače. Další virtuální počítače mohou být přidány do zvýšit kapacitu pro provedení funkce aktivity.

Následující diagram znázorňuje, jak Azure Functions hostitele komunikuje s entity úložiště v prostředí s škálovaný.

![Diagram škálování](media/durable-functions-perf-and-scale/scale-diagram.png)

Jak je vidět na předchozím obrázku, všech virtuálních počítačů se pokouší o zprávy do fronty pracovní položku. Ale jenom tři virtuální počítače můžete získat z řízení front zpráv a každý virtuální počítač zamkne fronty jeden ovládací prvek.

Instance orchestration jsou distribuovány na všechny instance fronty ovládacích prvků. Rozdělení se provádí pomocí algoritmu hash instance ID orchestration. ID instance ve výchozím nastavení jsou náhodné identifikátory GUID, zajistíte, že instance rovnoměrně rozděleny mezi všechny fronty ovládacího prvku.

Obecně řečeno funkce orchestrator by měla být jednoduché a by neměly vyžadovat velké množství výpočetní výkon. Proto není nutné vytvořit velký počet řízení oddíly fronty získat skvělé propustnost. Většinu práce velkou by mělo být provedeno v bezstavové aktivita funkce, které lze škálovat nekonečnou.

## <a name="auto-scale"></a>Automatické škálování

Jako s spuštěn v plánu spotřeby všechny funkce Azure, trvanlivý Functions podporuje automatické škálování prostřednictvím [řadiče škálování Azure Functions](functions-scale.md#runtime-scaling). Škálování řadiče monitoruje latence všechny fronty pravidelně vydáním _funkce Náhled_ příkazy. Založená na latenci peeked zpráv, řadičem škálování se rozhodněte, jestli chcete přidat nebo odebrat virtuální počítače.

Pokud řadič škálování zjistí, že řízení fronty zpráv latence je příliš vysoká., přidá instance virtuálních počítačů dokud buď snižuje latence zpráva na přijatelnou úroveň, nebo dorazí počet oddílů fronty ovládací prvek. Podobně řadičem škálování bude průběžně přidávají instance virtuálních počítačů Pokud latence fronty pracovní položku Vysoká, bez ohledu na to počet oddílů.

## <a name="thread-usage"></a>Využití přístup z více vláken

Funkce Orchestrator jsou spouštěny na jedno vlákno k zajištění, že provádění mohou být deterministický napříč mnoha opětovná přehrání. Z důvodu spuštění tohoto jednovláknové je důležité, aby funkce vláken orchestrator není provádět úlohy náročné na prostředky procesoru, proveďte vstupně-výstupních operací nebo blokovat z jakéhokoli důvodu. Veškerou práci, který může vyžadovat vstupně-výstupních operací blokování, nebo do funkce aktivity přesunout více vláken.

Aktivita funkce mají stejné chování jako běžné funkce aktivované fronty. Můžou bezpečně provést vstupně-výstupních operací, provedení operace náročné na prostředky procesoru a používat více vláken. Vzhledem k aktivační události aktivity jsou bezstavové, že můžete volně horizontální navýšení kapacity bez vazby počet virtuálních počítačů.

## <a name="concurrency-throttles"></a>Omezení souběžnosti

Azure Functions podporuje provádění více funkcí současně v rámci instance jediné aplikaci. Spuštění tohoto souběžných pomáhá zvýšit paralelismus a minimalizuje počet "cold spustí", které typické aplikace bude mít v čase. Využití paměti vysoké jednotlivé virtuální počítače ale může způsobit vysoké souběžnosti. V závislosti na potřebách funkce aplikace může být potřeba omezení souběžnosti jednotlivých instancí předejdete možnost vyčerpání paměti v situacích, vysokým zatížením.

Oba limity aktivita funkce a orchestrator funkce souběžnosti se dá nakonfigurovat v **host.json** souboru. Jsou relevantní nastavení `durableTask/maxConcurrentActivityFunctions` a `durableTask/maxConcurrentOrchestratorFunctions` v uvedeném pořadí.

```json
{
  "durableTask": {
    "maxConcurrentActivityFunctions": 10,
    "maxConcurrentOrchestratorFunctions": 10,
  }
}
```

V předchozím příkladu maximálně 10 funkce orchestrator a 10 aktivita funkce můžete současně spustit na jednom virtuálním počítači. Pokud není zadáno, je počet souběžných funkce spuštění aktivity a orchestrator limitován 10 X počet jader na virtuálním počítači.

> [!NOTE]
> Tato nastavení jsou užitečné při správě paměti a využití procesoru na jednom virtuálním počítači. Ale když škálovat na více systémů mezi více virtuálních počítačů, každý virtuální počítač bude mít vlastní sadu sadu omezení. Tato nastavení nelze použít k řízení souběžnosti na globální úrovni.

## <a name="orchestrator-function-replay"></a>Přehrání funkce produktu Orchestrator
Jak je uvedeno nahoře, jsou funkce orchestrator přehrány pomocí obsah **historie** tabulky. Ve výchozím nastavení je kód funkce orchestrator přehrány pokaždé, když jsou dávku zpráv vyjmutou z fronty ovládacího prvku.

Toto chování agresivní opětovného přehrání lze zakázat pomocí povolení **rozšířené relace**. Pokud jsou povolené rozšířené relace, funkce instancemi nástroje orchestrator jsou uložené v paměti, kterou zpracovat déle a nové zprávy bez úplné opakování. Rozšířené relace jsou povoleny nastavením `durableTask/extendedSessionsEnabled` k `true` v **host.json** souboru. `durableTask/extendedSessionIdleTimeoutInSeconds` Nastavení slouží k řízení dobu nečinnosti relace bude uchovávat v paměti:

```json
{
  "durableTask": {
    "extendedSessionsEnabled": true,
    "extendedSessionIdleTimeoutInSeconds": 30
  }
}
```

Tím se snižuje typické účinku povolení rozšířené relace vstupně-výstupních operací na účet úložiště Azure a vylepšené celkovou propustnost.

Jeden potenciální Nevýhodou této funkce je však tento nečinnosti orchestrator funkce, které instance zůstanou v paměti delší. Existují dva efekty zajímat:

1. Celkové zvýšení využití paměti v aplikaci funkce.
2. Celkové snížit propustnost, pokud máte mnoho spuštěních funkce souběžných, krátkodobou orchestrator.

Jako příklad Pokud `durableTask/extendedSessionIdleTimeoutInSeconds` nastaven na 30 sekund, pak díl funkce krátkodobou orchestrator, který se spustí v menší než 1 sekunda stále zabírají paměť pro 30 sekund. Se také započítává `durableTask/maxConcurrentOrchestratorFunctions` kvóty již bylo zmíněno dříve, potenciálně brání spuštění dalších funkcí produktu orchestrator.

> [!NOTE]
> Tato nastavení lze používat pouze po funkce orchestrator má plně vývoji a testování. Výchozí chování agresivní opětovného přehrání je užitečné pro zjišťování chyb idempotenci orchestrator funkcí v době vývoje.

## <a name="performance-targets"></a>Cíle výkonnosti

Při plánování použití trvanlivý funkce pro produkční aplikace, je důležité vzít v úvahu požadavky na výkon brzy v procesu plánování. Tato část popisuje některé scénáře základní informace o využití a čísla očekávané maximální propustnost.

* **Sekvenční aktivita provádění**: Tento scénář popisuje funkce orchestrator, která spouští řadu funkcí aktivity jedna po druhé. Nejvíce podobá [funkce řetězení](durable-functions-sequence.md) ukázka.
* **Paralelní spuštění aktivity**: Tento scénář popisuje, orchestrator funkce, která spustí mnoho funkcí aktivity v paralelní pomocí [Fan-out, Fan-in](durable-functions-cloud-backup.md) vzor.
* **Paralelní zpracování odpovědi**: Tento scénář je druhé polovině tématu [Fan-out, Fan-in](durable-functions-cloud-backup.md) vzor. Zaměřuje se na výkon fan-in. Je důležité si uvědomit, že na rozdíl od fan-out, fan-in se provádí funkce instancí jedné orchestrator a proto lze spustit jen u jednoho virtuálního počítače.
* **Zpracování událostí externí**: Tento scénář představuje jeden orchestrator instance funkce, kterou čeká na [externí události](durable-functions-external-events.md), po jednom.

> [!TIP]
> Na rozdíl od fan-out fan-in operace jsou omezená na jeden virtuální počítač. Pokud vaše aplikace používá fan-out, fan-in vzor a máte obavy o fan-in výkonu, zvažte dílčí dělení fan-out funkce aktivity napříč více [dílčí orchestrations](durable-functions-sub-orchestrations.md).

V následující tabulce jsou uvedeny očekávané *maximální* čísla propustnost pro dříve popsané scénáře. "Instance" odkazuje na jednu instanci orchestrator funkce spuštěné v jednom malá ([A1](../virtual-machines/windows/sizes-general.md#a-series)) virtuálních počítačů v Azure App Service. Ve všech případech se předpokládá, že [rozšířené relace](#orchestrator-function-replay) jsou povoleny. Skutečný výsledek se může lišit v závislosti na procesor nebo vstupy/výstupy práce prováděné kód funkce.

| Scénář | Maximální propustnost |
|-|-|
| Provedení sekvenční aktivity | 5 aktivity za sekundu na instanci |
| Paralelní aktivita provádění (fan-out) | 100 aktivity za sekundu na instanci |
| Paralelní zpracování odpovědi (fan-in) | 150 odpovědi za sekundu na instanci |
| Zpracování externích událostí | 50 událostí za sekundu na instanci |

> [!NOTE]
> Tato čísla jsou aktuální v1.4.0 (GA) verzi rozšíření trvanlivý funkce. Tato čísla v průběhu času mění během funkci existence a jak jsou vytvářeny optimalizace.

Pokud nevidíte čísla propustnost očekáváte a procesoru a využití paměti, zobrazí se v pořádku, zkontrolujte, zda je příčinou související s [stav svého účtu úložiště](../storage/common/storage-monitoring-diagnosing-troubleshooting.md#troubleshooting-guidance). Trvanlivý funkce, které můžete vložit rozšíření významné zatížení účtu Azure Storage a dostatečně vysoké zatížení může mít za následek omezení účtu úložiště.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Nainstalujte rozšíření trvanlivý funkce a ukázky](durable-functions-install.md)
