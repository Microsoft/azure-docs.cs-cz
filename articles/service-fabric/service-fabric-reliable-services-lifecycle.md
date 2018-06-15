---
title: Přehled životního cyklu Azure Service Fabric spolehlivé služby | Microsoft Docs
description: Další informace o různých životního cyklu události v Service Fabric spolehlivé služby
services: Service-Fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: vturecek;
ms.assetid: ''
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 42833323cbebf25ce2ca14e6ab7ec4fa5adbfd15
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34206940"
---
# <a name="reliable-services-lifecycle-overview"></a>Přehled životního cyklu spolehlivé služby
> [!div class="op_single_selector"]
> * [C# v systému Windows](service-fabric-reliable-services-lifecycle.md)
> * [Java v Linuxu](service-fabric-reliable-services-lifecycle-java.md)
>
>

Pokud uvažujete o časově omezené služby Azure Service Fabric spolehlivé, měly by být důležité základní informace o životním cyklu. Obecně platí životní cyklus zahrnuje následující:

- Při spuštění:
  - Služby se vytvářejí.
  - Služeb mají možnost pro vytvoření a návrat nula nebo více naslouchací procesy.
  - Všechny vrácený naslouchací procesy jsou otevřené, povolení komunikace se službou.
  - Služby **RunAsync** metoda je volána, povolení služby, které provádějí dlouhodobé úlohy nebo pracovní pozadí.
- Při vypnutí:
  - Token zrušení předaný **RunAsync** se zruší, a naslouchací procesy jsou uzavřeny.
  - Po posluchače zavřít, je destructed samotného objektu služby.

Nejsou podrobnosti kolem řazení tyto události. Pořadí událostí můžete trochu změnit v závislosti na tom, jestli je spolehlivá služba bezstavové nebo stavová. Kromě toho pro stavové služby, jsme musí řešit scénář primárních prohození. Během tohoto pořadí roli primární se přenese do jiné repliky (nebo, je vrácen) bez vypnutí služby. Nakonec jsme musí vzít v úvahu podmínek chyb nebo selhání.

## <a name="stateless-service-startup"></a>Spuštění bezstavové služby
Životní cyklus bezstavové služby je jednoduchá. Tady je pořadí událostí:

1. Služba je vytvořená.
2. Pak paralelně, provedou se dvě věci:
    - `StatelessService.CreateServiceInstanceListeners()` je voláno, a všechny vrátí, moduly pro naslouchání jsou otevřené. `ICommunicationListener.OpenAsync()` je volána v každé naslouchací proces.
    - Služby `StatelessService.RunAsync()` metoda je volána.
3. Pokud je k dispozici, služby `StatelessService.OnOpenAsync()` metoda je volána. Toto volání je neobvyklé přepsání, ale je k dispozici. V tuto chvíli můžete spustit úlohy inicializace služby rozšířených.

Mějte na paměti, že neexistuje žádná řazení mezi volání k vytváření a otevírání posluchače a **RunAsync**. Můžete otevřít posluchače před **RunAsync** je spuštěná. Podobně můžete vyvolat **RunAsync** předtím, než naslouchací procesy komunikace jsou otevřené nebo i vytvořený. Pokud žádné synchronizace je vyžadována, je ponechán jako cvičení na implementátor. Tady jsou některá běžná řešení:

  - Někdy naslouchací procesy nemůže fungovat, dokud se vytvoří některé další informace nebo práci. Pro bezstavové služby, které pracovní lze provést obvykle v jiných umístěních, jako jsou následující: 
    - V konstruktoru služby.
    - Během `CreateServiceInstanceListeners()` volání.
    - Jako součást konstrukce naslouchací proces sám sebe.
  - Někdy kód v **RunAsync** nelze spustit, dokud posluchače jsou otevřené. V takovém případě je nutné další spolupráce. Běžným řešením je, že je v rámci posluchače příznak, který určuje, kdy máte dokončena. Tento příznak se pak změnami **RunAsync** před pokračováním na samotnou práci.

## <a name="stateless-service-shutdown"></a>Vypnutí bezstavové služby
Pro vypíná bezstavové služby, je následovaný stejného vzoru právě obráceným:

1. Paralelní:
    - Jsou zavřeny všechny otevřené naslouchací procesy. `ICommunicationListener.CloseAsync()` je volána v každé naslouchací proces.
    - Token zrušení předaný `RunAsync()` je zrušená. Kontrolu token zrušení `IsCancellationRequested` vlastnost vrací hodnotu true a pokud je volána, je token `ThrowIfCancellationRequested` metoda vrátí `OperationCanceledException`.
2. Po `CloseAsync()` skončí u každé naslouchací proces a `RunAsync()` také dokončení služby `StatelessService.OnCloseAsync()` metoda je volána, pokud je k dispozici.  OnCloseAsync je volána, když bude instance bezstavové služby korektně vypnout. To může dojít, když probíhá upgrade služby kódu, instance služby přesouvá kvůli Vyrovnávání zatížení nebo se detekuje přechodná chyba. Neobvyklé přepsat `StatelessService.OnCloseAsync()`, ale můžou se používat bezpečně zavřít prostředky, zastavit zpracování na pozadí, dokončení ukládání externí stavu nebo ukončete stávající připojení.
3. Po `StatelessService.OnCloseAsync()` je destructed dokončení objektu služby.

## <a name="stateful-service-startup"></a>Spuštění stavové služby
Stavová služba mají podobný Princip pro bezstavové služby s několik změn. Pořadí událostí pro spuštění stavové služby, vypadá takto:

1. Služba je vytvořená.
2. `StatefulServiceBase.OnOpenAsync()` je volána. Toto volání není ve službě běžně přepsat.
3. Paralelní dojít v následujících akcí:
    - `StatefulServiceBase.CreateServiceReplicaListeners()` je volána. 
      - Pokud služba je primární služba, jsou otevřené všechny vrácený naslouchací procesy. `ICommunicationListener.OpenAsync()` je volána v každé naslouchací proces.
      - Pokud služba je sekundární službu, pouze tyto moduly pro naslouchání označen jako `ListenOnSecondary = true` jsou otevřené. Naslouchací procesy, které jsou otevřené na sekundárních hodnot je méně častých.
    - Pokud služba je aktuálně primárního serveru, službu na `StatefulServiceBase.RunAsync()` metoda je volána.
4. Po všechny repliky naslouchací proces pro `OpenAsync()` volá dokončit a `RunAsync()` je volána, `StatefulServiceBase.OnChangeRoleAsync()` je volána. Toto volání není ve službě běžně přepsat.

Podobně jako bezstavové služby, neexistuje žádný koordinovat pořadí, ve kterém se vytváří a otevřít naslouchací procesy a kdy **RunAsync** je volána. Pokud potřebujete koordinaci, řešení jsou podobné. Neexistuje jeden další případ pro stavové služby. Řekněme, že volání, které přicházejí na naslouchací procesy komunikace vyžadovat informace uložené v produktu některé [spolehlivé kolekce](service-fabric-reliable-services-reliable-collections.md). Protože naslouchací procesy komunikace by bylo možné otevřít před spolehlivé kolekce jsou z něj číst nebo zapisovat a před **RunAsync** může spustit, je nutné některé další spolupráce. Nejjednodušší a nejběžnější řešení je pro naslouchací procesy komunikace vrátit chybový kód, který klient používá vědět, opakujte žádost.

## <a name="stateful-service-shutdown"></a>Vypnutí stavové služby
Podobně jako bezstavové služby události životního cyklu během vypnutí jsou stejné jako při spuštění, ale vrátit zpět. Když se právě vypíná stavové služby, dojde k následujícím událostem:

1. Paralelní:
    - Jsou zavřeny všechny otevřené naslouchací procesy. `ICommunicationListener.CloseAsync()` je volána v každé naslouchací proces.
    - Token zrušení předaný `RunAsync()` je zrušená. Kontrolu token zrušení `IsCancellationRequested` vlastnost vrací hodnotu true a pokud je volána, je token `ThrowIfCancellationRequested` metoda vrátí `OperationCanceledException`.
2. Po `CloseAsync()` skončí u každé naslouchací proces a `RunAsync()` také dokončení služby `StatefulServiceBase.OnChangeRoleAsync()` je volána. Toto volání není ve službě běžně přepsat.

   > [!NOTE]  
   > Potřeba čekat na **RunAsync** ukončíte je nutné, pokud je tato replika primární repliky.

3. Po `StatefulServiceBase.OnChangeRoleAsync()` metoda skončí, `StatefulServiceBase.OnCloseAsync()` metoda je volána. Toto volání je neobvyklé přepsání, ale je k dispozici.
3. Po `StatefulServiceBase.OnCloseAsync()` je destructed dokončení objektu služby.

## <a name="stateful-service-primary-swaps"></a>Primární záměna stavové služby
Je-li spuštěn stavové služby, je pouze primární repliky této stavové služby mají jejich naslouchací procesy komunikace otevřít a jejich **RunAsync** metodu s názvem. Sekundární repliky se vytvářejí, ale zobrazit žádná další volání. Při spuštěné stavové služby, můžete repliky, který je aktuálně primární změnit. Co to znamená v podmínkách události životního cyklu, které můžete zobrazit repliku? Chování, které se zobrazí stavová repliky závisí na tom, jestli je replika se degradaci nebo povýší během prohození.

### <a name="for-the-primary-thats-demoted"></a>Pro primární, kterého je snížení úrovně
Pro primární repliku, která je k degradaci Service Fabric musí tato replika zastavit zpracování zpráv a ukončete všechny pozadí práce, kterou její probíhající činnosti. V důsledku toho tento krok vypadá to, když je služba vypnuta. Jedním rozdílem je, že služba není destructed nebo zavřená, protože zůstane jako sekundární. Následující rozhraní API se označují jako:

1. Paralelní:
    - Jsou zavřeny všechny otevřené naslouchací procesy. `ICommunicationListener.CloseAsync()` je volána v každé naslouchací proces.
    - Token zrušení předaný `RunAsync()` je zrušená. Kontrolu token zrušení `IsCancellationRequested` vlastnost vrací hodnotu true a pokud je volána, je token `ThrowIfCancellationRequested` metoda vrátí `OperationCanceledException`.
2. Po `CloseAsync()` skončí u každé naslouchací proces a `RunAsync()` také dokončení služby `StatefulServiceBase.OnChangeRoleAsync()` je volána. Toto volání není ve službě běžně přepsat.

### <a name="for-the-secondary-thats-promoted"></a>Pro sekundární která vyzval
Podobně Service Fabric musí sekundární repliky, který je propagována do zahájit naslouchání pro zprávy v drátové síti a spustit úlohy na pozadí, které potřebuje k dokončení. V důsledku toho tento proces vypadá stejně při vytváření služby s tím rozdílem, že replika samotné již existuje jako. Následující rozhraní API se označují jako:

1. Paralelní:
    - `StatefulServiceBase.CreateServiceReplicaListeners()` je voláno, a všechny vrátí, moduly pro naslouchání jsou otevřené. `ICommunicationListener.OpenAsync()` je volána v každé naslouchací proces.
    - Služby `StatefulServiceBase.RunAsync()` metoda je volána.
2. Po všechny repliky naslouchací proces pro `OpenAsync()` volá dokončit a `RunAsync()` je volána, `StatefulServiceBase.OnChangeRoleAsync()` je volána. Toto volání není ve službě běžně přepsat.

### <a name="common-issues-during-stateful-service-shutdown-and-primary-demotion"></a>Běžné problémy při vypnutí stavové služby a primární snížení úrovně
Service Fabric změní primární stavové služby z různých důvodů. Nejběžnější jsou [clusteru vyrovnává](service-fabric-cluster-resource-manager-balancing.md) a [upgradu aplikace](service-fabric-application-upgrade.md). Během těchto operací (i během vypnutí normální služby, jako by se zobrazí, pokud byla služba odstraněna), je důležité, že služba respektují `CancellationToken`. 

Služby, které zpracovávají zrušení řádně může mít několik problémy. Tyto operace jsou pomalé, protože Service Fabric čeká na služby, aby řádně ukončena. Nakonec to může vést k selhání upgrady tohoto časového limitu a vrátit zpět. Selhání respektovat token zrušení může také způsobit imbalanced clustery. Clustery stát nevyváženou, protože uzly získat aktivní, ale služby nemůže být znovu vyrovnána protože trvá příliš dlouho přesunout na jiné místo. 

Protože se stavové služby, je také pravděpodobné, že se používají [spolehlivé kolekce](service-fabric-reliable-services-reliable-collections.md). V Service Fabric při snížení úrovně primární jeden z nejdůležitějších věcí, které se stane, se zruší se oprávnění k zápisu do základní stav. To vede ke druhé sadě problémy, které můžou ovlivnit služby životního cyklu. Kolekce návratový výjimky založené na načasování a jestli je přesouvání repliky nebo vypnutí. Tyto výjimky by měl být správně zpracovat. Výjimky vyvolané Service Fabric spadají do trvalého [(`FabricException`)](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception?view=azure-dotnet) a přechodný [(`FabricTransientException`)](https://docs.microsoft.com/dotnet/api/system.fabric.fabrictransientexception?view=azure-dotnet) kategorií. Trvalé výjimky by měl zaznamenána a vyvolána při přechodné výjimky můžete zkusit znovu na základě logiky některé opakování.

Zpracování výjimek, které pocházejí z použití `ReliableCollections` ve spojení s událostmi služby životního cyklu, je důležitou součástí testování a ověření spolehlivě. Doporučujeme vždy spustit služby zatížení při provádění upgradu a [chaos testování](service-fabric-controlled-chaos.md) před nasazením do produkčního prostředí. Tyto základní postup pomůže, zajistěte, aby služby je implementovaná správně a zpracovává události životního cyklu správně.


## <a name="notes-on-the-service-lifecycle"></a>Poznámky k služby životního cyklu
  - Obě `RunAsync()` metoda a `CreateServiceReplicaListeners/CreateServiceInstanceListeners` volání jsou volitelné. Služba může mít jednu z nich, obě nebo ani jeden z nich. Například pokud je služba svou práci na základě výzvy pro uživatele, není třeba pro něj k implementaci `RunAsync()`. Pouze naslouchací procesy komunikace a jejich přidružené kód jsou nezbytné. Podobně, vytváření a vrácení naslouchací procesy komunikace je volitelná, jak služba může mít pouze pozadí práci udělat a proto pouze musí implementovat `RunAsync()`.
  - Je platná pro službu k dokončení `RunAsync()` úspěšně a zpět z něj. Dokončení není podmínce chyby. Dokončení `RunAsync()` označuje, že práce na pozadí služby byla dokončena. Pro stavové služby spolehlivé `RunAsync()` je volána znovu, pokud je degradaci z primární na sekundární a poté vyzval zpět na primární repliku.
  - Pokud služba ukončí z `RunAsync()` po vyvolání výjimky neočekávané výjimce, to se považuje za selhání. Objekt služby se vypne a hlášení stavu chyby.
  - I když není žádný časový limit na vrácení z těchto metod, okamžitě nebude možné zapisovat do spolehlivé kolekcí a proto nelze dokončit všechna skutečná práce. Doporučujeme, aby vrátíte co nejrychleji po přijetí žádost o zrušení. Pokud vaše služba neodpoví na těchto volání rozhraní API v přiměřené době, Service Fabric může vynucené ukončení služby. Obvykle tato situace nastane pouze během upgradu aplikace nebo když se odstraňuje služby. Tento časový limit je 15 minut ve výchozím nastavení.
  - Selhání v `OnCloseAsync()` cesta povede `OnAbort()` volané, což je možnost best effort poslední chance pro službu vyčištění a uvolnit všechny prostředky, které se mají vyžádaná. Obecně se používá při zjištění trvalé selhání na uzlu, nebo když Service Fabric se nedají spravovat spolehlivě životního cyklu instance služby z důvodu vnitřní chyby.
  - `OnChangeRoleAsync()` je volána, když replika stavové služby je například změna role na primární nebo sekundární. Primární repliky mají stav zápisu (je povoleno vytvoření a zápis do spolehlivé kolekcí). Sekundární repliky jsou uvedeny čtení stav (můžete číst jenom z existující spolehlivé kolekce). Většinu práce v stavové služby se provádí na primární replice. Sekundární repliky lze provést ověření jen pro čtení, generování sestav, dolování dat nebo jiné úlohy jen pro čtení.

## <a name="next-steps"></a>Další postup
- [Úvod do spolehlivé služby](service-fabric-reliable-services-introduction.md)
- [Spolehlivé služby rychlý start](service-fabric-reliable-services-quick-start.md)
- [Repliky a instance](service-fabric-concepts-replica-lifecycle.md)
