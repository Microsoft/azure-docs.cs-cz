---
title: Výkon a škálování v Durable Functions – Azure
description: Přečtěte si o jedinečných vlastnostech škálování rozšíření Durable Functions pro Azure Functions.
author: cgillum
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 120335a7bce83bc3d4771ea64f665d67c7d1079a
ms.sourcegitcommit: 65cef6e5d7c2827cf1194451c8f26a3458bc310a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/19/2021
ms.locfileid: "98572795"
---
# <a name="performance-and-scale-in-durable-functions-azure-functions"></a>Výkon a škálování v Durable Functions (Azure Functions)

Pro optimalizaci výkonu a škálovatelnosti je důležité pochopit jedinečné charakteristiky škálování [Durable Functions](durable-functions-overview.md).

Pro pochopení chování škálování je třeba pochopit některé podrobnosti o základním poskytovateli Azure Storage.

## <a name="history-table"></a>Tabulka historie

Tabulka **Historie** je Azure Storage tabulka, která obsahuje události historie pro všechny instance orchestrace v rámci centra úloh. Název této tabulky je ve formátu *TaskHubName* historie. Po spuštění instancí se do této tabulky přidají nové řádky. Klíč oddílu této tabulky je odvozen z ID instance orchestrace. ID instance je ve většině případů náhodné, což zajišťuje optimální distribuci vnitřních oddílů v Azure Storage.

Je-li nutné spustit instanci orchestrace, jsou do paměti načteny příslušné řádky tabulky historie. Tyto *události historie* se pak přehrají do kódu funkce Orchestrator a vrátí se zpátky do předchozího kontrolního stavu. Použití historie spouštění k opětovnému sestavení tohoto způsobu je ovlivněno [vzorem zdroje události](/azure/architecture/patterns/event-sourcing).

## <a name="instances-table"></a>Tabulka instancí

Tabulka **Instances** je další Azure Storage tabulka, která obsahuje stavy všech orchestrací a instancí entit v rámci centra úloh. Při vytváření instancí se do této tabulky přidají nové řádky. Klíč oddílu této tabulky je ID instance orchestrace nebo klíč entity a klíč řádku je prázdný řetězec. Existuje jeden řádek na orchestraci nebo instanci entity.

Tato tabulka slouží k uspokojení požadavků na dotazování instance z `GetStatusAsync` rozhraní API (.NET) a `getStatus` (JavaScript) a také pro [dotaz na stavový protokol HTTP API](durable-functions-http-api.md#get-instance-status). Je trvale konzistentní s obsahem výše zmíněné tabulky **Historie** . Použití samostatné Azure Storage tabulky k efektivnímu uspokojení operací dotazů na instance tímto způsobem je ovlivněno [vzorem CQRS (Command and Query Responsibility segregation) (CQRS)](/azure/architecture/patterns/cqrs).

## <a name="internal-queue-triggers"></a>Interní triggery front

Funkce a funkce nástroje Orchestrator jsou spouštěny interními frontami v centru úloh aplikace Function App. Použití front tímto způsobem poskytuje spolehlivé záruky doručení zpráv "nejméně jednou". Existují dva typy front v Durable Functions: **frontu řízení** a **frontu pracovních položek**.

### <a name="the-work-item-queue"></a>Fronta pracovních položek

K dispozici je jedna fronta pracovních položek na každé centrum úloh v Durable Functions. Jedná se o základní frontu, která se chová podobně jako jakákoli jiná `queueTrigger` fronta v Azure Functions. Tato fronta se používá k aktivaci bezstavových *funkcí aktivity* tím, že v jednom okamžiku odřadí jedinou zprávu. Každá z těchto zpráv obsahuje vstupy funkcí aktivity a další metadata, jako je například funkce, kterou je třeba provést. Když Durable Functions aplikace škáluje na více virtuálních počítačů, všechny tyto virtuální počítače budou chtít získat práci z fronty pracovních položek.

### <a name="control-queues"></a>Počet front ovládacích prvků:

K dispozici je více *řídicích front* pro každé centrum úloh v Durable Functions. *Fronta ovládacího prvku* je výkonnější než jednodušší fronta pracovních položek. Řídicí fronty se používají ke spuštění stavových funkcí Orchestrator a entity. Vzhledem k tomu, že instance nástroje Orchestrator a entity Function jsou stavové nejednoznačné, není možné použít konkurenční model uživatele k distribuci zatížení napříč virtuálními počítači. Místo toho se zprávy Orchestrator a entity vyrovnávají zatížením napříč řídicími frontami. Další podrobnosti o tomto chování najdete v následujících oddílech.

Řídicí fronty obsahují nejrůznější typy zpráv o životním cyklu orchestrace. Příklady zahrnují [zprávy řízení nástroje Orchestrator](durable-functions-instance-management.md), zprávy s *odezvou* funkcí aktivity a zprávy časovače. Ve frontě ovládacích prvků v jednom cyklickém dotazování bude z fronty ovládacího prvku odřazena spousta zpráv 32. Tyto zprávy obsahují data datové části i metadata, včetně toho, pro kterou instanci orchestrace je určena. Je-li pro stejnou instanci orchestrace určena více zpráv ve frontě, budou zpracovány jako dávka.

### <a name="queue-polling"></a>Cyklické dotazování front

Rozšíření odolné úlohy implementuje náhodný exponenciální algoritmus pro snížení vlivu nečinnosti na transakční dotaz na náklady transakce úložiště. Po nalezení zprávy modul runtime hned vyhledá jinou zprávu; Pokud se nenajde žádná zpráva, počká na určitou dobu, než se zkusí znovu. Po následných neúspěšných pokusech o získání zprávy fronty se doba čekání zvětšuje, dokud nedosáhne maximální čekací doby, která má výchozí hodnotu 30 sekund.

Maximální zpoždění cyklického dotazování lze konfigurovat prostřednictvím `maxQueuePollingInterval` vlastnosti v [host.jsv souboru](../functions-host-json.md#durabletask). Nastavení této vlastnosti na vyšší hodnotu může mít za následek vyšší latence při zpracování zpráv. Vyšší latence by se měly očekávat jenom po období nečinnosti. Nastavení této vlastnosti na nižší hodnotu může mít za následek vyšší náklady na úložiště kvůli zvýšeným transakcím úložiště.

> [!NOTE]
> Při spuštění v rámci plánů Azure Functions a Premium se [kontroler Azure Functions Scale](../event-driven-scaling.md) bude dotazovat každý ovládací prvek a frontu pracovních položek každých 10 sekund. Toto další cyklické dotazování je nezbytné k určení, kdy se mají aktivovat instance aplikace Function App a provádět rozhodování o škálování. V době psaní je tento 10 sekundový interval konstantní a nedá se nakonfigurovat.

### <a name="orchestration-start-delays"></a>Zpoždění zahájení orchestrace
Instance orchestrace se spouští vložením `ExecutionStarted` zprávy do jedné z front ovládacích prvků centra úloh. Za určitých podmínek můžete sledovat prodlevy s více sekundami mezi tím, kdy je naplánováno spuštění Orchestrace a kdy je ve skutečnosti spuštěna. Během tohoto časového intervalu zůstane instance orchestrace ve `Pending` stavu. Existují dva možné příčiny tohoto zpoždění:

1. **Fronty nevyřízených ovládacích prvků**: Pokud řídicí fronta pro tuto instanci obsahuje velký počet zpráv, může trvat nějakou dobu, než se `ExecutionStarted` zpráva přijme a zpracuje modulem runtime. Zprávy nevyřízených položek se mohou vyskytnout, pokud orchestrace zpracovává velké množství událostí současně. Mezi události, které patří do fronty řízení, patří události zahájení orchestrace, dokončování aktivit, trvalá časovače, ukončení a externí události. Pokud tato prodleva nastane za normálních okolností, zvažte vytvoření nového centra úloh s větším počtem oddílů. Konfigurace více oddílů způsobí, že modul runtime vytvoří více řídicích front pro distribuci zatížení.

2. **Zpoždění při cyklickém dotazování**: Další běžnou příčinu zpoždění orchestrace je [dřív popsané chování při cyklickém dotazování pro kontrolní fronty](#queue-polling). Tato prodleva se však očekává pouze při horizontálním navýšení kapacity aplikace na dvě nebo více instancí. Pokud existuje jenom jedna instance aplikace nebo pokud instance aplikace, která spouští orchestraci, je taky stejná jako instance, která se dotazuje na cílovou frontu ovládacích prvků, neproběhne zpoždění cyklického dotazování fronty. Zpoždění cyklického dotazování se dá snížit aktualizací **host.jsna** nastavení, jak je popsáno výše.

## <a name="storage-account-selection"></a>Výběr účtu úložiště

Fronty, tabulky a objekty blob používané nástrojem Durable Functions jsou vytvořeny v nakonfigurovaném Azure Storagem účtu. Účet, který se má použít, se dá zadat pomocí `durableTask/storageProvider/connectionStringName` nastavení (nebo `durableTask/azureStorageConnectionStringName` nastavení v Durable Functions 1. x) v **host.jsv** souboru.

### <a name="durable-functions-2x"></a>Durable Functions 2. x

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

### <a name="durable-functions-1x"></a>Durable Functions 1. x

```json
{
  "extensions": {
    "durableTask": {
      "azureStorageConnectionStringName": "MyStorageAccountAppSetting"
    }
  }
}
```

Pokud není zadaný, použije se výchozí `AzureWebJobsStorage` účet úložiště. Pro úlohy citlivé na výkon se ale doporučuje nakonfigurovat účet úložiště, který není výchozí. Durable Functions používá Azure Storage silně a používání vyhrazeného účtu úložiště izoluje Durable Functions využití úložiště od interního využití hostitelem Azure Functions.

## <a name="orchestrator-scale-out"></a>Škálování na více instancí Orchestrator

Funkce aktivity jsou bezstavové a automaticky se škálují s přidáním virtuálních počítačů. Funkce a entity nástroje Orchestrator jsou naopak *rozdělené do oddílů* v jedné nebo několika řídicích frontách. Počet front řízení je definován v **host.jsv** souboru. Následující příklad host.jsve fragmentu kódu nastaví `durableTask/storageProvider/partitionCount` vlastnost (nebo `durableTask/partitionCount` v Durable Functions 1. x) na `3` .

### <a name="durable-functions-2x"></a>Durable Functions 2. x

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

### <a name="durable-functions-1x"></a>Durable Functions 1. x

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

Při horizontálním navýšení kapacity na více instancí hostitelů funkcí (obvykle na různých virtuálních počítačích) získá každá instance zámek jedné z řídicích front. Tyto zámky se interně implementují jako zapůjčení úložiště objektů BLOB a zajišťují, že instance orchestrace nebo entita běží jenom na jedné instanci hostitele najednou. Pokud je Centrum úkolů nakonfigurované se třemi řídicími frontami, instance Orchestrace a entity je možné vyrovnávat zatížení až po třech virtuálních počítačích. Další virtuální počítače je možné přidat za účelem zvýšení kapacity pro provádění funkcí aktivity.

Následující diagram znázorňuje, jak se hostitel Azure Functions komunikuje s entitami úložiště v prostředí s možností horizontálního rozšíření kapacity.

![Měřítko diagramu](./media/durable-functions-perf-and-scale/scale-diagram.png)

Jak je znázorněno na předchozím diagramu, všechny virtuální počítače soutěží o zprávy ve frontě pracovní položky. Z řídicích front ale můžou získat zprávy jenom tři virtuální počítače a každý virtuální počítač zamkne jednu frontu ovládacích prvků.

Instance Orchestrace a entity jsou distribuovány napříč všemi instancemi fronty ovládacích prvků. Distribuce se provádí pomocí hashování ID instance orchestrace nebo názvu entity a páru klíčů. Identifikátory instancí orchestrace jsou ve výchozím nastavení náhodné identifikátory GUID, které zajišťují, aby se instance rovnoměrně rozdělují mezi všechny fronty ovládacích prvků.

Obecně řečeno, funkce nástroje Orchestrator mají být odlehčené a neměly by vyžadovat velké množství výpočetní síly. Proto není nutné vytvářet velký počet oddílů front řízení, aby bylo možné dosáhnout Skvělé propustnosti pro orchestraci. Většina těžkých prací by se měla provádět v bezstavových funkcích aktivity, které se dají škálovat nekonečně.

## <a name="auto-scale"></a>Automatické škálování

Stejně jako u všech Azure Functions běžících v plánech spotřeby a elastické Premium Durable Functions podporuje automatické škálování prostřednictvím [řadiče Azure Functions škálování](../event-driven-scaling.md#runtime-scaling). Kontroler škálování monitoruje latenci všech front tím, že pravidelně vydává příkazy pro _prohlížení_ . V závislosti na latencích prohlížených zpráv se kontroler škálování rozhodne, jestli se mají přidat nebo odebrat virtuální počítače.

Pokud kontroler škálování zjistí, že latence zpráv řízení fronty je příliš vysoká, přidá instance virtuálních počítačů, dokud se latence zprávy nesníží na přijatelnou úroveň, nebo dosáhne počtu oddílů fronty řízení. Podobně, kontroler škálování průběžně přidá instance virtuálních počítačů, pokud jsou vysoké latence fronty pracovních položek, bez ohledu na počet oddílů.

> [!NOTE]
> Počínaje Durable Functions 2,0 se aplikace funkcí dají nakonfigurovat tak, aby se spouštěly v rámci koncových bodů služby chráněných VIRTUÁLNÍmi sítěmi v plánu elastické Premium. V této konfiguraci Durable Functions triggery iniciují žádosti o škálování místo řadiče škálování.

## <a name="thread-usage"></a>Využití vlákna

Funkce nástroje Orchestrator jsou spouštěny v jednom vlákně, aby bylo zajištěno, že provádění může být deterministické v mnoha opakováních. Z důvodu tohoto spuštění s jedním vláknem je důležité, aby vlákna funkcí Orchestrator neprováděla úlohy náročné na procesor, prováděla vstupně-výstupní operace nebo blokovat z jakéhokoli důvodu. Všechny práce, které mohou vyžadovat vstupně-výstupní operace, blokování nebo více vláken, by měly být přesunuty do funkcí aktivity.

Funkce aktivit mají stejné chování jako běžné funkce aktivované frontou. Můžou bezpečně dělat vstupně-výstupní operace, provádět operace náročné na procesor a používat víc vláken. Protože triggery aktivit jsou bezstavové, můžou volně škálovat na neohraničený počet virtuálních počítačů.

Funkce entit jsou také spouštěny v jednom vlákně a operace jsou zpracovávány po jednom čase. Funkce entit však nemají žádná omezení typu kódu, který lze spustit.

## <a name="concurrency-throttles"></a>Omezení souběžnosti

Azure Functions podporuje souběžné provádění více funkcí v rámci jedné instance aplikace. Toto souběžné provádění pomáhá zvýšit paralelismus a minimalizuje počet "studených startů", na které bude Typická aplikace v průběhu času docházet. Vysoká souběžnost však může vyčerpat systémové prostředky pro virtuální počítače, například Síťová připojení nebo dostupná paměť. V závislosti na potřebách aplikace Function App může být nutné omezit souběžnost na jednotlivé instance, aby nedocházelo k možnosti vycházet z paměti v situacích s vysokým zatížením.

Omezení souběžnosti funkcí Activity, Orchestrator a entity je možné nakonfigurovat v **host.jsv** souboru. Příslušná nastavení jsou `durableTask/maxConcurrentActivityFunctions` pro funkce aktivity a `durableTask/maxConcurrentOrchestratorFunctions` pro funkce Orchestrator i entity.

### <a name="functions-20"></a>Funkce 2,0

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

V předchozím příkladu může být v jednom virtuálním počítači souběžně spuštěných maximálně 10 funkcí Orchestrator nebo entity functions a 10 funkcí aktivit. Není-li tento parametr zadán, je počet souběžných spuštění a funkce Orchestrator nebo entity Function omezené na 10X počet jader virtuálního počítače.

> [!NOTE]
> Tato nastavení jsou užitečná při správě paměti a využití CPU na jednom virtuálním počítači. Ale při horizontálním navýšení kapacity mezi několika virtuálními počítači má každý virtuální počítač vlastní sadu omezení. Tato nastavení se nedají použít k řízení souběžnosti na globální úrovni.

## <a name="extended-sessions"></a>Rozšířené relace

Rozšířené relace jsou nastavení, které udržuje Orchestrace a entity v paměti, i když dokončí zpracování zpráv. Typický efekt povolení rozšířených relací se snižuje v/v proti účtu Azure Storage a celkově lepší propustnost.

Můžete povolit rozšířené relace nastavením `durableTask/extendedSessionsEnabled` na `true` v **host.jsv** souboru. `durableTask/extendedSessionIdleTimeoutInSeconds`Nastavení lze použít k určení, jak dlouho bude nečinná relace uchovávána v paměti:

**Funkce 2,0**
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

**Funkce 1,0**
```json
{
  "durableTask": {
    "extendedSessionsEnabled": true,
    "extendedSessionIdleTimeoutInSeconds": 30
  }
}
```

Existují dva potenciální downsides tohoto nastavení, které je potřeba mít na paměti:

1. Došlo k celkovému nárůstu využití paměti aplikace Function App.
2. Může dojít k celkovému snížení propustnosti v případě, že existuje mnoho souběžných, krátkodobých nebo nenáročnéch spuštění funkcí Orchestrator nebo entity.

Příklad: Pokud `durableTask/extendedSessionIdleTimeoutInSeconds` je nastavené na 30 sekund, pak krátká nestálá funkce Orchestrator nebo entity, která se provede za méně než 1 sekundou, zabírá paměť po dobu 30 sekund. Také se počítá oproti `durableTask/maxConcurrentOrchestratorFunctions` výše uvedené kvótě, která potenciálně brání spuštění dalších funkcí Orchestrator nebo entity.

Konkrétní účinky rozšířených relací na nástroje Orchestrator a funkce entit jsou popsány v následujících částech.

> [!NOTE]
> Rozšířené relace jsou aktuálně podporované jenom v jazycích .NET, jako je C# nebo F #. Nastavení `extendedSessionsEnabled` na `true` pro jiné platformy může vést k problémům za běhu, jako je například tiché selhání při provádění aktivit a funkcí aktivovaných pro orchestraci.


### <a name="orchestrator-function-replay"></a>Funkce Orchestrator – přehrání

Jak bylo zmíněno dříve, funkce Orchestrator se přehrávají pomocí obsahu tabulky **Historie** . Ve výchozím nastavení se kód funkce nástroje Orchestrator přehraje pokaždé, když se dávka zpráv z fronty ovládacího prvku odřadí. I když používáte vzorek ventilátoru pro ventilátor a čekáte na dokončení všech úloh (například pomocí `Task.WhenAll` technologie v rozhraní .NET nebo `context.df.Task.all` v JavaScriptu), dojde k přehrání, ke kterému dochází, když se v průběhu času zpracovávají dávky odpovědí na úlohy. Pokud jsou povoleny rozšířené relace, instance funkcí nástroje Orchestrator jsou uchovávány v paměti déle a nové zprávy lze zpracovat bez nutnosti opětovného přehrání celé historie.

Zlepšení výkonu rozšířených relací je nejčastěji pozorováno v následujících situacích:

* V případě, že je současně spuštěn omezený počet instancí Orchestration.
* Pokud orchestrace mají velký počet sekvenčních akcí (např. stovky volání funkcí aktivity), které jsou dokončeny rychle.
* V případě orchestrace ventilátorů a ventilátorů se zobrazí velký počet akcí, které se dokončí přibližně ve stejnou dobu.
* Když funkce Orchestrator potřebuje zpracovávat velké zprávy nebo provádět zpracování dat náročných na procesor.

Ve všech ostatních situacích obvykle neexistuje žádné pozorovatelské zlepšení výkonu pro funkce nástroje Orchestrator.

> [!NOTE]
> Tato nastavení by měla být použita pouze poté, co byla funkce Orchestrator plně vyvinuta a testována. Výchozí agresivní chování při přehrání může být užitečné při zjišťování porušení [omezení kódu funkce Orchestrator](durable-functions-code-constraints.md) v době vývoje, a proto je ve výchozím nastavení zakázané.

### <a name="entity-function-unloading"></a>Uvolnění funkce entity

Funkce entit zpracovávají až 20 operací v rámci jedné dávky. Jakmile entita dokončí zpracování dávky operací, zůstane její stav a uvolní se z paměti. Uvolnění entit z paměti můžete zpozdit pomocí nastavení rozšířené relace. Entity nadále ukládají změny stavu jako dříve, ale zůstávají v paměti pro nakonfigurované časové období, aby se snížil počet zatížení od Azure Storage. Toto omezení zatížení z Azure Storage může zlepšit celkovou propustnost často používaných entit.

## <a name="performance-targets"></a>Cíle výkonnosti

Když plánujete použít Durable Functions pro produkční aplikaci, je důležité zvážit požadavky na výkon v brzké době v procesu plánování. Tato část se zabývá některými základními scénáři použití a očekávanými maximálními čísly propustnosti.

* **Sekvenční provádění aktivity**: Tento scénář popisuje funkci nástroje Orchestrator, která spouští řadu funkcí aktivity jednu po druhé. Nejlépe se podobá ukázce [řetězení funkcí](durable-functions-sequence.md) .
* **Paralelní provádění aktivit**: Tento scénář popisuje funkci Orchestrator, která paralelně spouští mnoho funkcí aktivity s použitím modelu [ventilátoru s ventilátory](durable-functions-cloud-backup.md) .
* **Paralelní zpracování odpovědí**: Tento scénář je druhá polovina vzoru ventilátoru [, ventilátoru](durable-functions-cloud-backup.md) . Zaměřuje se na výkon ventilátoru. Je důležité si uvědomit, že na rozdíl od ventilátoru se ventilátor používá v jedné instanci funkce Orchestrator, a proto může běžet jenom na jednom virtuálním počítači.
* **Zpracování externích událostí**: Tento scénář představuje jednu instanci funkce Orchestrator, která čeká na [externí události](durable-functions-external-events.md), a to v jednom okamžiku.
* **Zpracování operací entity**: Tento scénář testuje, jak rychle může _jedna_ [entita čítače](durable-functions-entities.md) zpracovat konstantní datový proud operací.

> [!TIP]
> Na rozdíl od ventilátorů jsou operace ventilátoru omezené na jeden virtuální počítač. Pokud vaše aplikace využívá vzorek ventilátoru a máte obavy o výkon ventilátoru, zvažte rozdělení funkce aktivity na více [dílčích orchestrací](durable-functions-sub-orchestrations.md).

Následující tabulka uvádí očekávaná *maximální* čísla propustnosti pro dříve popsané scénáře. "Instance" odkazuje na jednu instanci funkce Orchestrator běžící na jednom malém virtuálním počítači ([a1](../../virtual-machines/sizes-previous-gen.md)) v Azure App Service. Ve všech případech se předpokládá, že jsou povolené [Rozšířené relace](#orchestrator-function-replay) . Skutečné výsledky se můžou lišit v závislosti na využití procesoru nebo vstupně-výstupní práci prováděné kódem funkce.

| Scenario | Maximální propustnost |
|-|-|
| Provádění sekvenční aktivity | 5 aktivit za sekundu, na instanci |
| Paralelní provádění aktivit (ventilátor-out) | 100 aktivit za sekundu, na instanci |
| Paralelní zpracování odpovědí (ventilátor-in) | 150 odpovědí za sekundu, na instanci |
| Externí zpracování událostí | 50 událostí za sekundu, na instanci |
| Zpracování operací entity | 64 operací za sekundu |

> [!NOTE]
> Tato čísla jsou aktuální pro vydání rozšíření Durable Functions v 1.4.0 (GA). Tato čísla se můžou v průběhu času měnit, protože se tato funkce prosazuje a optimalizace se provedou.

Pokud se vám nezobrazují počty propustnosti, které očekáváte, a využití procesoru a paměti se zobrazí v pořádku, zkontrolujte, jestli příčina souvisí s [stavem vašeho účtu úložiště](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#troubleshooting-guidance). Rozšíření Durable Functions může vést k výraznému zatížení pro Azure Storage účet a dostatečně vysoké zatížení může mít za následek omezení účtu úložiště.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Informace o zotavení po havárii a geografické distribuci](durable-functions-disaster-recovery-geo-distribution.md)
