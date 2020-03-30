---
title: Výkon a škálování v odolných funkcích – Azure
description: Úvod k rozšíření Trvalé funkce pro funkce Azure.
author: cgillum
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 260811c4ae15b45de6f7bc1b22e3ed6dcea44259
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277905"
---
# <a name="performance-and-scale-in-durable-functions-azure-functions"></a>Výkon a škálování v Durable Functions (Azure Functions)

Chcete-li optimalizovat výkon a škálovatelnost, je důležité porozumět jedinečným charakteristikám škálování [trvalých funkcí](durable-functions-overview.md).

Chcete-li pochopit chování škálování, musíte pochopit některé podrobnosti o podkladovém zprostředkovateli úložiště Azure.

## <a name="history-table"></a>Tabulka historie

Tabulka **Historie** je tabulka Azure Storage, která obsahuje události historie pro všechny instance orchestrace v centru úloh. Název této tabulky je ve formuláři *TaskHubName*History. Při spuštění instancí jsou do této tabulky přidány nové řádky. Klíč oddílu této tabulky je odvozen z ID instance orchestrace. ID instance je ve většině případů náhodné, což zajišťuje optimální distribuci interních oddílů ve službě Azure Storage.

Když je třeba spustit instanci orchestrace, příslušné řádky tabulky Historie jsou načteny do paměti. Tyto *události historie* jsou pak přehrány do kódu funkce orchestrator získat zpět do jeho dříve kontrolních bodů stavu. Použití historie provádění k obnovení stavu tímto způsobem je ovlivněno [vzorem Získávání událostí](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing).

## <a name="instances-table"></a>Tabulka instancí

Tabulka **Instance** je další tabulka Azure Storage, která obsahuje stavy všech orchestračních instancí a instancí entit v centru úloh. Při vytváření instancí jsou do této tabulky přidány nové řádky. Klíč oddílu této tabulky je ID instance orchestrace nebo klíč entity a klíč řádku je pevná konstanta. Existuje jeden řádek na orchestraci nebo instanci entity.

Tato tabulka se používá ke splnění `GetStatusAsync` požadavků na dotazy `getStatus` instancí z rozhraní API (.NET) a (JavaScript) a [také ze stavového dotazu HTTP API](durable-functions-http-api.md#get-instance-status). Je udržována nakonec v souladu s **obsahem** historie tabulky uvedené výše. Použití samostatné tabulky Azure Storage efektivně uspokojovat operace dotazů instance tímto způsobem je ovlivněno [vzorem cqrs (Command and Query Responsibility Segregation).](https://docs.microsoft.com/azure/architecture/patterns/cqrs)

## <a name="internal-queue-triggers"></a>Aktivační události interní fronty

Funkce orchestratoru a funkce aktivity jsou spouštěny interními frontami v centru úloh aplikace funkce. Použití fronty tímto způsobem poskytuje spolehlivé záruky doručení zpráv "alespoň jednou". Existují dva typy front v trvalé funkce: **fronta ovládacích prvku** a **fronty pracovních položek**.

### <a name="the-work-item-queue"></a>Fronta pracovních položek

V centru trvalé funkce je jedna fronta pracovních položek na centrum úloh. Je to základní fronta a chová se `queueTrigger` podobně jako všechny ostatní fronty v Azure Functions. Tato fronta se používá k aktivaci *funkce aktivity* bez stavů vyřazením jedné zprávy současně. Každá z těchto zpráv obsahuje vstupy funkce aktivity a další metadata, například které funkce spustit. Když se aplikace durable functions škáluje na více virtuálních počítačích, všechny tyto virtuální počítačové soutěží o získání práce z fronty pracovních položek.

### <a name="control-queues"></a>Kontrolní fronty

Existuje více *front řízení* na centrum úloh v trvalé funkce. *Řídicí fronta* je složitější než jednodušší fronta pracovních položek. Řídicí fronty se používají k aktivaci stavového orchestrátoru a entit. Vzhledem k tomu, že instance funkce orchestrator a entity jsou stavové singletons, není možné použít konkurenční model příjemce k distribuci zatížení mezi virtuálními aplikacemi. Místo toho orchestrator a entity zprávy jsou vyrovnávány zatížení v rámci fronty ovládacího prvku. Další podrobnosti o tomto chování naleznete v následujících částech.

Řídicí fronty obsahují různé typy zpráv životního cyklu orchestrace. Mezi příklady patří [řídicí zprávy orchestratoru](durable-functions-instance-management.md), zprávy *o odezvě* funkce aktivity a zprávy časovače. Až 32 zpráv bude vyřazeno z fronty ovládacích prvku v jednom hlasování. Tyto zprávy obsahují data datové části, stejně jako metadata včetně, které instance orchestrace je určena pro. Pokud více zpráv dequeued jsou určeny pro stejnou instanci orchestrace, budou zpracovány jako dávka.

### <a name="queue-polling"></a>Dotazování fronty

Trvalé rozšíření úloh implementuje algoritmus náhodnéexponenciální back-off snížit vliv dotazování fronty nečinnosti na náklady na transakce úložiště. Při nalezení zprávy za běhu okamžitě zkontroluje jinou zprávu; Pokud není nalezena žádná zpráva, čeká na určitou dobu, než se pokusí znovu. Po následných neúspěšných pokusech o získání zprávy fronty se čekací doba nadále zvyšuje, dokud nedosáhne maximální čekací doby, která je výchozí na 30 sekund.

Maximální prodleva dotazování `maxQueuePollingInterval` je konfigurovatelná prostřednictvím vlastnosti v [souboru host.json](../functions-host-json.md#durabletask). Nastavení této vlastnosti na vyšší hodnotu může mít za následek vyšší latence zpracování zpráv. Vyšší latence by se očekávalo až po období nečinnosti. Nastavení této vlastnosti na nižší hodnotu může mít za následek vyšší náklady na úložiště z důvodu zvýšené transakce úložiště.

> [!NOTE]
> Když běží v azure functions consumption a premium plány, [řadič škály funkcí Azure](../functions-scale.md#how-the-consumption-and-premium-plans-work) bude dotazování každý ovládací prvek a fronty pracovních položek jednou za 10 sekund. Toto další dotazování je nezbytné k určení, kdy aktivovat instance aplikace funkce a provádět rozhodnutí škálování. V době zápisu je tento interval 10 sekund konstantní a nelze jej konfigurovat.

## <a name="storage-account-selection"></a>Výběr účtu úložiště

Fronty, tabulky a objekty BLOB používané trvalé funkce jsou vytvořeny v nakonfigurované matné služby Azure Storage účtu. Účet, který chcete použít, `durableTask/storageProvider/connectionStringName` lze `durableTask/azureStorageConnectionStringName` zadat pomocí nastavení (nebo nastavení v části Trvalé funkce 1.x) v souboru **host.json.**

### <a name="durable-functions-2x"></a>Odolné funkce 2.x

```json
{
  "extensions": {
    "durableTask": {
      "storageProvider": {
        "connectionStringName": "MyStorageAccountAppSetting"
      }
    }
  }
}
```

### <a name="durable-functions-1x"></a>Odolné funkce 1.x

```json
{
  "extensions": {
    "durableTask": {
      "azureStorageConnectionStringName": "MyStorageAccountAppSetting"
    }
  }
}
```

Pokud není zadán, `AzureWebJobsStorage` použije se výchozí účet úložiště. Pro úlohy citlivé na výkon se však doporučuje konfigurace účtu úložiště, který není výchozí. Trvalé funkce používá Azure Storage těžce a pomocí vyhrazeného účtu úložiště izoluje trvalé funkce využití úložiště z interního využití hostitele Azure Functions.

## <a name="orchestrator-scale-out"></a>Horizontální navýšení kapacity orchestratoru

Funkce aktivity jsou bezstavové a škálovat automaticky přidáním virtuálních aplikací. Funkce orchestrator a entity, na druhé straně jsou *rozděleny* do jedné nebo více front ovládacích procesů. Počet řídicích front je definován v souboru **host.json.** Následující příklad fragmentu host.json nastaví `durableTask/storageProvider/partitionCount` vlastnost `durableTask/partitionCount` (nebo v trvalých `3`funkcích 1.x) na .

### <a name="durable-functions-2x"></a>Odolné funkce 2.x

```json
{
  "extensions": {
    "durableTask": {
      "storageProvider": {
          "partitionCount": 3
      }
    }
  }
}
```

### <a name="durable-functions-1x"></a>Odolné funkce 1.x

```json
{
  "extensions": {
    "durableTask": {
      "partitionCount": 3
    }
  }
}
```

Rozbočovač úloh lze nakonfigurovat s 1 až 16 oddíly. Pokud není zadán, výchozí počet oddílů je **4**.

Při škálování na více instancí hostitele funkce (obvykle na různých virtuálních počítačích), každá instance získá zámek na jedné z front ovládacího prvku. Tyto zámky jsou interně implementovány jako zapůjčení úložiště objektů blob a zajišťují, že instance orchestrace nebo entita běží pouze na jedné instanci hostitele najednou. Pokud je centrum úloh nakonfigurováno se třemi frontami ovládacích procesů, mohou být instance orchestrace a entity vyrovnány zatížením až na třech virtuálních počítačech. Další virtuální virtuální aplikace lze přidat ke zvýšení kapacity pro provádění funkce aktivity.

Následující diagram znázorňuje, jak hostitel Azure Functions spolupracuje s entitami úložiště v prostředí s horizontálním navýšením kapacity.

![Diagram měřítka](./media/durable-functions-perf-and-scale/scale-diagram.png)

Jak je znázorněno v předchozím diagramu, všechny virtuální počítačové soutěžit o zprávy ve frontě pracovních položek. Však pouze tři virtuální chodů můžete získat zprávy z front řízení a každý virtuální ms uzamkne jednu frontu ovládacího prvku.

Instance orchestrace a entity jsou distribuovány ve všech instancích fronty řízení. Distribuce se provádí hašováním ID instance orchestrace nebo názvu entity a páru klíčů. ID instanci orchestrace jsou ve výchozím nastavení náhodné identifikátory GUID, které zajišťují, že instance jsou rovnoměrně rozloženy ve všech frontách ovládacího prvku.

Obecně řečeno, funkce orchestrator jsou určeny k odlehčení a neměly by vyžadovat velké množství výpočetního výkonu. Proto není nutné vytvářet velký počet oddílů fronty řízení získat velkou propustnost pro orchestrace. Většina těžké práce by měla být provedena ve funkcích aktivity bez státní příslušnosti, které lze škálovat nekonečně.

## <a name="auto-scale"></a>Automatické škálování

Stejně jako u všech funkcí Azure spuštěných v plánech Spotřeba a Elastic Premium podporuje trvalé funkce automatické škálování prostřednictvím [řadiče škálování Azure Functions](../functions-scale.md#runtime-scaling). Řadič škálování monitoruje latenci všech front pravidelným vydáváním příkazů _náhledu._ Na základě latence inkresovaných zpráv se řadič škálování rozhodne, jestli má virtuální počítač přidat nebo odebrat.

Pokud řadič škálování určuje, že latence zpráv fronty řízení jsou příliš vysoké, přidá instance virtuálních zařízení, dokud se latence zprávy nesníží na přijatelnou úroveň nebo nedosáhne počtu oddílů front řízení. Podobně řadič škálování bude průběžně přidávat instance virtuálních zařízení, pokud jsou čekací doba fronty pracovních položek vysoká, bez ohledu na počet oddílů.

> [!NOTE]
> Počínaje trvanlivé funkce 2.0, aplikace funkcí lze nakonfigurovat tak, aby spouštět v rámci koncových bodů služby chráněné v nettu v plánu Elastic Premium. V této konfiguraci trvalé funkce aktivuje zahájit požadavky škálování namísto řadiče škálování.

## <a name="thread-usage"></a>Využití vlákna

Funkce Orchestrator jsou spouštěny v jednom vlákně, aby bylo zajištěno, že provádění může být deterministické v mnoha replayích. Z důvodu tohoto jednovláknového spuštění je důležité, aby podprocesy funkce orchestrator neprováděly úlohy náročné na procesor, vstupně-bloudění nebo blokování z jakéhokoli důvodu. Všechny práce, které mohou vyžadovat vstupně-out, blokování nebo více vláken by měly být přesunuty do funkce aktivity.

Funkce aktivity mají všechny stejné chování jako běžné funkce spouštěné frontou. Mohou bezpečně provádět vstupně-up, provádět operace náročné na procesor a používat více vláken. Vzhledem k tomu, že aktivační události aktivity jsou bezstavové, mohou volně škálovat na neomezený počet virtuálních aplikací.

Funkce entity jsou také spouštěny v jednom vlákně a operace jsou zpracovávány jednou za čas. Funkce entity však nemají žádná omezení na typ kódu, který lze spustit.

## <a name="concurrency-throttles"></a>Omezení souběžnosti

Funkce Azure podporuje provádění více funkcí současně v rámci jedné instance aplikace. Toto souběžné spuštění pomáhá zvýšit paralelismus a minimalizuje počet "studené starty", které typické aplikace bude zažívat v průběhu času. Vysoká souběžnost však může vyčerpat systémové prostředky na virtuální ho disponibilní virtuální min. jako jsou síťová připojení nebo dostupná paměť. V závislosti na potřebách aplikace funkce může být nutné omezit souběžnost na instanci, aby se zabránilo možnosti nedostatku paměti v situacích s vysokým zatížením.

Omezení souběžnosti funkce aktivity, orchestratoru a entity lze nakonfigurovat v souboru **host.json.** Příslušná nastavení `durableTask/maxConcurrentActivityFunctions` jsou pro `durableTask/maxConcurrentOrchestratorFunctions` funkce aktivity a pro orchestrátor i entity funkce.

### <a name="functions-20"></a>Funkce 2.0

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

### <a name="functions-1x"></a>Functions 1.x

```json
{
  "durableTask": {
    "maxConcurrentActivityFunctions": 10,
    "maxConcurrentOrchestratorFunctions": 10
  }
}
```

V předchozím příkladu maximálně 10 funkcí orchestrator nebo entity a 10 funkcí aktivity lze spustit na jednom virtuálním počítači současně. Pokud není zadán, počet souběžných aktivit a orchestrator nebo entity funkce provádění je omezena na 10x počet jader na virtuálním počítači.

> [!NOTE]
> Tato nastavení jsou užitečná pro správu využití paměti a procesoru na jednom virtuálním počítači. Když se však škálovat navíce mezi více virtuálních počítačích, každý virtuální počítač má vlastní sadu limitů. Tato nastavení nelze použít k řízení souběžnosti na globální úrovni.

## <a name="extended-sessions"></a>Rozšířené relace

Rozšířené relace je nastavení, které udržuje orchestrations a entity v paměti i po dokončení zpracování zpráv. Typický efekt povolení rozšířených relací je snížena vstupně-v platnosti proti účtu Azure Storage a celkově lepší propustnost.

Rozšířené relace můžete povolit nastavením `durableTask/extendedSessionsEnabled` `true` v souboru **host.json.** Toto `durableTask/extendedSessionIdleTimeoutInSeconds` nastavení lze použít k řízení, jak dlouho bude nečinná relace v paměti uchovávána:

**Funkce 2.0**
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

**Funkce 1.0**
```json
{
  "durableTask": {
    "extendedSessionsEnabled": true,
    "extendedSessionIdleTimeoutInSeconds": 30
  }
}
```

Existují dvě potenciální nevýhody tohoto nastavení být vědomi:

1. Je tu celkové zvýšení využití paměti aplikace funkce.
2. Může dojít k celkovému snížení propustnost, pokud existuje mnoho souběžných, krátkodobé orchestrator nebo entity funkce provádění.

Jako příklad, `durableTask/extendedSessionIdleTimeoutInSeconds` pokud je nastavena na 30 sekund, pak krátký trvání orchestrator nebo entity funkce epizoda, která se spustí za méně než 1 sekundu stále zabírá paměť po dobu 30 sekund. Počítá také proti `durableTask/maxConcurrentOrchestratorFunctions` kvóty uvedené výše, potenciálně brání spuštění jiných orchestrátornebo entity funkce.

Specifické účinky rozšířené relace na orchestrátor a entity funkce jsou popsány v následujících částech.

### <a name="orchestrator-function-replay"></a>Přehrávání funkce Orchestrtor

Jak již bylo zmíněno dříve, funkce orchestrator jsou přehrány pomocí obsahu **historie** tabulky. Ve výchozím nastavení je kód funkce orchestrator přehrán pokaždé, když je dávka zpráv vyřazena z fronty ovládacího prvku. I v případě, že používáte fan-out, fan-in vzor a čekají na `Task.WhenAll` dokončení všech `context.df.Task.all` úkolů (například pomocí v .NET nebo v JavaScriptu), bude přeháňky, které se vyskytují jako dávky odpovědí na úkol jsou zpracovávány v průběhu času. Pokud jsou povoleny rozšířené relace, instance funkcí orchestrator jsou uchovávány v paměti déle a nové zprávy mohou být zpracovány bez úplného přehrání historie.

Zlepšení výkonu rozšířených relací je nejčastěji pozorováno v následujících situacích:

* Pokud existuje omezený počet instancí orchestrace spuštěných současně.
* Když orchestrations mají velký počet sekvenční akce (např. stovky volání funkce aktivity), které se dokončí rychle.
* Když orchestrace fan-out a fan-in velký počet akcí, které dokončí přibližně ve stejnou dobu.
* Když funkce orchestrator potřebují zpracovat velké zprávy nebo provést jakékoli zpracování dat náročné na procesor.

Ve všech ostatních situacích obvykle neexistuje žádné pozorovatelné zlepšení výkonu pro funkce orchestratoru.

> [!NOTE]
> Tato nastavení by měla být použita pouze po plně vyvinuté a testované funkci orchestratoru. Výchozí chování agresivní ho přehrání může být užitečné pro detekci porušení [omezení kódu funkce orchestrátoru](durable-functions-code-constraints.md) v době vývoje, a proto je ve výchozím nastavení zakázáno.

### <a name="entity-function-unloading"></a>Uvolnění funkce entity

Funkce entity zpracovávají až 20 operací v jedné dávce. Jakmile entita dokončí zpracování dávky operací, zachová svůj stav a uvolní se z paměti. Uvolnění entit z paměti můžete zpozdit pomocí nastavení rozšířených relací. Entity nadále zachovat jejich změny stavu jako dříve, ale zůstávají v paměti po nakonfigurované časové období snížit počet zatížení z Azure Storage. Toto snížení zatížení z Azure Storage může zlepšit celkovou propustnost často používaných entit.

## <a name="performance-targets"></a>Výkonnostní cíle

Při plánování použití trvalé funkce pro produkční aplikace, je důležité zvážit požadavky na výkon v rané fázi procesu plánování. Tato část popisuje některé základní scénáře použití a očekávaná čísla maximální propustnost.

* **Sekvenční provádění aktivity**: Tento scénář popisuje funkci orchestrator, která spouští řadu funkcí aktivity jeden po druhém. Nejvíce se podobá ukázku [řetězení funkcí.](durable-functions-sequence.md)
* **Paralelní provádění aktivity**: Tento scénář popisuje funkci orchestrator, která provádí mnoho funkcí aktivity paralelně pomocí [Fan-out, Fan-in](durable-functions-cloud-backup.md) vzor.
* **Paralelní zpracování odezvy**: Tento scénář je druhá polovina [Fan-out, Fan-in](durable-functions-cloud-backup.md) vzor. Zaměřuje se na výkon fan-in. Je důležité si uvědomit, že na rozdíl od fan-out, fan-in se provádí podle jedné instance funkce orchestrator, a proto lze spustit pouze na jednom virtuálním počítači.
* **Externí zpracování událostí**: Tento scénář představuje jednu instanci funkce orchestrator, která čeká na [externí události](durable-functions-external-events.md), jeden po druhém.
* **Zpracování operace entity**: Tento scénář testuje, jak rychle může _jedna_ [entita čítače](durable-functions-entities.md) zpracovat konstantní tok operací.

> [!TIP]
> Na rozdíl od fan-out, fanouškovské operace jsou omezeny na jeden virtuální virtuální mk. Pokud vaše aplikace používá fan-out, fan-in vzor a máte obavy o fan-in výkon, zvažte sub-dělení funkce aktivity fan-out přes více [sub-orchestrations](durable-functions-sub-orchestrations.md).

V následující tabulce jsou uvedena očekávaná *čísla maximální* propustnost pro dříve popsané scénáře. "Instance" odkazuje na jednu instanci funkce orchestrator spuštěné na jednom malém virtuálním počítači[(A1)](../../virtual-machines/sizes-previous-gen.md)ve službě Azure App Service. Ve všech případech se předpokládá, že [rozšířené relace](#orchestrator-function-replay) jsou povoleny. Skutečné výsledky se mohou lišit v závislosti na procesoru nebo vstupně-va práce prováděné kódem funkce.

| Scénář | Maximální propustnost |
|-|-|
| Provádění sekvenční aktivity | 5 aktivit za sekundu na instanci |
| Paralelní provádění aktivity (fan-out) | 100 aktivit za sekundu na instanci |
| Zpracování paralelní odezvy (fan-in) | 150 odpovědí za sekundu na instanci |
| Externí zpracování událostí | 50 událostí za sekundu na instanci |
| Zpracování operace entity | 64 operací za sekundu |

> [!NOTE]
> Tato čísla jsou aktuální od verze v1.4.0 (GA) rozšíření Trvalé funkce. Tato čísla se mohou v průběhu času měnit podle toho, jak funkce dozrává a jak jsou prováděny optimalizace.

Pokud nevidíte čísla propustnosti, která očekáváte, a využití procesoru a paměti se zobrazí v pořádku, zkontrolujte, zda příčina souvisí se [stavem vašeho účtu úložiště](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#troubleshooting-guidance). Rozšíření trvalé funkce můžete umístit významné zatížení na účet azure úložiště a dostatečně vysoké zatížení může mít za následek omezení účtu úložiště.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Další informace o zotavení po havárii a geografické distribuci](durable-functions-disaster-recovery-geo-distribution.md)
