---
title: Životní cyklus Reliable Services Azure Service Fabric
description: Přečtěte si informace o událostech životního cyklu v Service Fabric Azure Reliable Services pomocí Java pro stavové a bezstavové služby.
author: PavanKunapareddyMSFT
ms.topic: conceptual
ms.date: 06/30/2017
ms.custom: devx-track-java
ms.author: pakunapa
ms.openlocfilehash: b22c78a0259e4430ac6bfae1c0a9379c4a832cd4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "87324603"
---
# <a name="reliable-services-lifecycle"></a>Životní cyklus Reliable Services
> [!div class="op_single_selector"]
> * [C# v systému Windows](service-fabric-reliable-services-lifecycle.md)
> * [Java v Linuxu](service-fabric-reliable-services-lifecycle-java.md)
>
>

Reliable Services je jedním z programovacích modelů dostupných ve službě Azure Service Fabric. Při získávání informací o životním cyklu Reliable Services je nejdůležitější pochopit základní události životního cyklu. Přesné řazení událostí závisí na podrobnostech o konfiguraci. 

Obecně platí, že životní cyklus Reliable Services zahrnuje následující události:

* Při spuštění:
  * Služby jsou konstruovány.
  * Služby mají příležitost vytvářet a vracet nula nebo více posluchačů.
  * Pro komunikaci se službou jsou otevřené všechny vrácené naslouchací procesy.
  * `runAsync`Volá se metoda služby, takže služba může provádět dlouhotrvající nebo na pozadí.
* Během vypnutí:
  * Token zrušení, který byl předán do `runAsync` , je zrušen a naslouchací procesy jsou uzavřeny.
  * Objekt služby je destrukturovaná.

Pořadí událostí v Reliable Services se může mírně měnit v závislosti na tom, jestli je spolehlivá služba Bezstavová nebo stavová. 

Pro stavové služby je také nutné vyřešit primární scénář prohození. Během této sekvence se role primární přenáší do jiné repliky (nebo se vrátí zpátky) bez vypnutí služby. 

Nakonec je třeba vzít v úvahu chybové nebo chybné podmínky.

## <a name="stateless-service-startup"></a>Spuštění bezstavové služby
Životní cyklus bezstavové služby je poměrně jednoduchá. Tady je pořadí událostí:

1. Služba je vytvořena.
2. K těmto událostem dochází paralelně:
    - `StatelessService.createServiceInstanceListeners()` je vyvolána a všechny vrácené naslouchací procesy jsou otevřeny. `CommunicationListener.openAsync()` je volána u každého naslouchacího procesu.
    - `runAsync`Je volána metoda služby ( `StatelessService.runAsync()` ).
3. Je-li k dispozici, `onOpenAsync` je volána metoda služby. Konkrétně `StatelessService.onOpenAsync()` je volána metoda. Toto je Neběžné přepsání, ale je k dispozici.

Je důležité si uvědomit, že neexistuje žádné řazení mezi voláním pro vytvoření a otevření posluchačů a volání `runAsync` . Posluchače se můžou otevřít předtím, než `runAsync` se spustí. Podobně `runAsync` může být vyvolána před otevřením posluchačů komunikace nebo před tím, než budou provedeny. Pokud je vyžadována nějaká synchronizace, musí být provedena implementací. Tady je několik běžných řešení:

* V některých případech naslouchací procesy nemohou fungovat, dokud nebudou vytvořeny jiné informace nebo jiná práce. U bezstavových služeb se tato práce obvykle dá provést v konstruktoru služby. Lze ji provést během `createServiceInstanceListeners()` volání nebo v rámci konstrukce samotného naslouchacího procesu.
* Někdy se kód v `runAsync` nespustí, dokud nebudou naslouchací procesy otevřeny. V takovém případě je potřeba další koordinace. Běžným řešením je přidat příznak do posluchačů. Příznak označuje, kdy byly naslouchací procesy dokončeny. `runAsync`Metoda je před pokračováním ve skutečné práci zkontrolována.

## <a name="stateless-service-shutdown"></a>Bezstavové vypnutí služby
Při vypínání bezstavové služby se sleduje stejný vzor, ale v opačném případě:

1. K těmto událostem dochází paralelně:
    - Všechny otevřené naslouchací procesy jsou uzavřeny. `CommunicationListener.closeAsync()` je volána u každého naslouchacího procesu.
    - Token zrušení, který byl předán do, byl `runAsync()` zrušen. `isCancelled`Když se vrátí vlastnost tokenu zrušení `true` a když se zavolá, `throwIfCancellationRequested` vyvolá metoda tokenu `CancellationException` .
2. Když se `closeAsync()` dokončí u každého naslouchacího procesu a `runAsync()` také dokončí, `StatelessService.onCloseAsync()` je volána metoda služby, pokud je k dispozici. Znovu se nejedná o společné přepsání, ale dá se použít k bezpečnému zavření prostředků, zastavení zpracování na pozadí, dokončení ukládání externího stavu nebo zavření stávajících připojení.
3. Po `StatelessService.onCloseAsync()` dokončení dojde k destrukturování objektu služby.

## <a name="stateful-service-startup"></a>Spuštění stavové služby
Stavové služby mají model podobný bezstavovým službám s několika změnami.  Tady je pořadí událostí pro spuštění stavové služby:

1. Služba je vytvořena.
2. `StatefulServiceBase.onOpenAsync()` je volána. Toto volání není ve službě obvykle přepsáno.
3. K těmto událostem dochází paralelně:
    - `StatefulServiceBase.createServiceReplicaListeners()` je vyvolána. 
      - Pokud je služba primární službou, otevřou se všechny vrácené naslouchací procesy. `CommunicationListener.openAsync()` je volána u každého naslouchacího procesu.
      - Pokud je služba Sekundární službou, jsou otevřené jenom naslouchací procesy označené jako `listenOnSecondary = true` . Naslouchací procesy, které jsou otevřeny na sekundárních počítačích, jsou méně běžné.
    - Pokud je služba aktuálně primární, `StatefulServiceBase.runAsync()` je volána metoda služby.
4. Po dokončení volání a volání všech volání naslouchacího procesu repliky se zavolá `openAsync()` `runAsync()` `StatefulServiceBase.onChangeRoleAsync()` . Toto volání není ve službě obvykle přepsáno.

Podobně jako bezstavové služby ve stavové službě neexistuje žádná koordinace mezi pořadím, ve kterém jsou posluchače vytvářeny a otevřeny a kdy `runAsync` jsou volány. Pokud potřebujete koordinaci, řešení jsou mnohem stejná. Existuje však pro stavovou službu jeden další případ. Řekněme, že volání, která přicházejí do komunikačních posluchačů, vyžadují informace uchovávané v některých [spolehlivých kolekcích](service-fabric-reliable-services-reliable-collections.md). Vzhledem k tomu, že naslouchací procesy komunikace mohou být otevřeny před tím, než budou spolehlivé kolekce čitelné nebo zapisovatelné a před `runAsync` zahájením spuštění, je nutné provést určitou další koordinaci. Nejjednodušší a nejběžnější řešení je, aby naslouchací procesy komunikace vracely kód chyby. Klient používá kód chyby k pokusům o opakování žádosti.

## <a name="stateful-service-shutdown"></a>Vypnutí stavové služby
Stejně jako bezstavové služby jsou události životního cyklu během vypnutí stejné jako při spuštění, ale stornovány. Při vypnutí stavové služby dojde k následujícím událostem:

1. K těmto událostem dochází paralelně:
    - Všechny otevřené naslouchací procesy jsou uzavřeny. `CommunicationListener.closeAsync()` je volána u každého naslouchacího procesu.
    - Token zrušení, který byl předán do, byl `runAsync()` zrušen. Volání metody tokenu zrušení `isCancelled()` vrátí `true` a při volání metody tokenu `throwIfCancellationRequested()` vyvolá výjimku `OperationCanceledException` .
2. Po `closeAsync()` dokončení každého naslouchacího procesu a `runAsync()` také dokončí službu, `StatefulServiceBase.onChangeRoleAsync()` se zavolá. Toto volání není ve službě obvykle přepsáno.

   > [!NOTE]  
   > Čekání `runAsync` na dokončení je nezbytné jenom v případě, že je tato replika primární replikou.

3. Po `StatefulServiceBase.onChangeRoleAsync()` dokončení metody `StatefulServiceBase.onCloseAsync()` je volána metoda. Toto volání je Neběžné přepsání, ale je k dispozici.
3. Po `StatefulServiceBase.onCloseAsync()` dokončení dojde k destrukturování objektu služby.

## <a name="stateful-service-primary-swaps"></a>Primární swapy stavové služby
Když je spuštěná stavová služba, otevřou se naslouchací procesy komunikace a `runAsync` metoda se zavolá jenom pro primární repliky těchto stavových služeb. Sekundární repliky jsou konstruovány, ale nejsou k dispozici žádné další volání. Když je spuštěná stavová služba, replika, která je aktuálně primární, se může změnit. Události životního cyklu, které může stavová replika zobrazit, závisí na tom, jestli se jedná o repliku, která se během prohození snižuje nebo povýší.

### <a name="for-the-demoted-primary"></a>Pro primární úroveň
Service Fabric potřebuje primární repliku, která je degradována tak, aby zastavila zpracování zpráv a zastavila práci na pozadí. Tento krok je podobný jako při vypnutí služby. Jednou z nich je, že služba není destrukturovaná ani uzavřená, protože zůstává jako sekundární. Dojde k následující chybě:

1. K těmto událostem dochází paralelně:
    - Všechny otevřené naslouchací procesy jsou uzavřeny. `CommunicationListener.closeAsync()` je volána u každého naslouchacího procesu.
    - Token zrušení, který byl předán do, byl `runAsync()` zrušen. Kontroluje návrat metody tokenu zrušení `isCancelled()` `true` . Při volání metoda tokenu `throwIfCancellationRequested()` vyvolá výjimku `OperationCanceledException` .
2. Po `closeAsync()` dokončení každého naslouchacího procesu a `runAsync()` také dokončí službu, `StatefulServiceBase.onChangeRoleAsync()` se zavolá. Toto volání není ve službě obvykle přepsáno.

### <a name="for-the-promoted-secondary"></a>Pro sekundární úroveň
Podobně Service Fabric potřebuje sekundární repliku, která je povýšená na zahájení naslouchání zprávám na lince, a spuštění všech úloh na pozadí, které je potřeba dokončit. Tento postup je podobný jako při vytvoření služby. Rozdílem je, že replika sama o sobě již existuje. Dojde k následující chybě:

1. K těmto událostem dochází paralelně:
    - `StatefulServiceBase.createServiceReplicaListeners()` je vyvolána a jsou otevřeny všechny vracené naslouchací procesy. `CommunicationListener.openAsync()` je volána u každého naslouchacího procesu.
    - `StatefulServiceBase.runAsync()`Volá se metoda služby.
2. Po dokončení volání a volání všech volání naslouchacího procesu repliky se zavolá `openAsync()` `runAsync()` `StatefulServiceBase.onChangeRoleAsync()` . Toto volání není ve službě obvykle přepsáno.

### <a name="common-issues-during-stateful-service-shutdown-and-primary-demotion"></a>Běžné problémy při vypínání stavové služby a primárním snížení úrovně
Service Fabric změní primární stavovou službu z několika důvodů. Nejběžnějšími důvody je [Vyrovnávání zatížení clusteru](service-fabric-cluster-resource-manager-balancing.md) a [Upgrade aplikace](service-fabric-application-upgrade.md). Během těchto operací je důležité, aby služba dodržuje tyto informace `cancellationToken` . To platí také při normálním vypínání služby, například v případě, že byla služba odstraněna.

Služby, které nezpracovávají zrušení čistě, můžou vyskytnout několik problémů. Tyto operace jsou pomalé, protože Service Fabric čeká na řádné zastavení služeb. To může vést k neúspěšným aktualizacím, které vypršel časový limit a vrácení zpět. Nedodržení tokenu zrušení může také způsobit nevyvážené clustery. Clustery se stanou nevyvážené, protože uzly budou mít aktivní. Služby se ale nedají znovu vyrovnávat, protože je trvá příliš dlouho, než je přesunete jinam. 

Vzhledem k tomu, že služby jsou stavové, je také pravděpodobně nutné, aby služby používaly [spolehlivé kolekce](service-fabric-reliable-services-reliable-collections.md). V Service Fabric, když dojde ke snížení úrovně primárního primárního obsahu, nastane jedna z prvních věcí, ke kterým dojde, aby byl přístup pro zápis do základního stavu odvolán. To vede k druhé sadě problémů, které by mohly mít vliv na životní cyklus služby. Kolekce vrací výjimky založené na časování a na tom, zda je replika přesunuta nebo vypnuta. Je důležité tyto výjimky správně zpracovat. 

Výjimky vyvolané Service Fabric jsou buď trvalá [( `FabricException` )](/java/api/system.fabric.exception) , nebo přechodný [( `FabricTransientException` )](/java/api/system.fabric.exception.fabrictransientexception). Trvalé výjimky by měly být protokolovány a vyvolány. Přechodné výjimky lze opakovat na základě logiky opakování.

Důležitou součástí testování a ověřování Reliable Services je zpracování výjimek, které pocházejí z použití `ReliableCollections` ve spojení s událostmi životního cyklu služby. Doporučujeme vždy spustit službu při zatížení. Před nasazením do produkčního prostředí byste také měli provést upgrady a [testování chaos](service-fabric-controlled-chaos.md) . Tyto základní kroky vám pomůžou zajistit správnou implementaci služby a tím, že se události životního cyklu zpracovávají správně.

## <a name="notes-on-service-lifecycle"></a>Poznámky k životnímu cyklu služby
* Jak `runAsync()` metoda, tak `createServiceInstanceListeners/createServiceReplicaListeners` volání jsou volitelné. Služba může mít jednu nebo žádnou. Pokud například služba provádí veškerou práci v reakci na volání uživatele, není nutné ji implementovat `runAsync()` . Jsou nutné pouze naslouchací procesy komunikace a jejich přidružený kód.  Podobně vytváření a vracení komunikačních posluchačů je volitelné. Služba může mít pouze práci na pozadí, takže ji stačí implementovat `runAsync()` .
* Je platný, aby se služba úspěšně dokončila `runAsync()` a vrátila se z ní. To není považováno za stav selhání. Představuje práci na pozadí pro dokončení služby. Stavový Reliable Services `runAsync()` by se znovu volal, pokud se služba sníží z primární úrovně a pak se převýší zpět na primární.
* Pokud se služba ukončí od `runAsync()` vyvolání neočekávané výjimky, jedná se o chybu. Objekt služby je vypnutý a nahlásila se chyba stavu.
* I když při návratu z těchto metod nedochází k žádnému časovému limitu, okamžitě ztratíte možnost napsat. Proto nemůžete dokončit žádnou skutečnou práci. Po přijetí žádosti o zrušení doporučujeme co nejrychleji vracet co nejrychleji. Pokud vaše služba nereaguje na tato volání rozhraní API v rozumné době, Service Fabric může nuceně ukončit vaši službu. Obvykle k tomu dochází pouze během upgradu aplikace nebo při odstraňování služby. Ve výchozím nastavení je tento časový limit 15 minut.
* Chyby ve `onCloseAsync()` výsledku cesty, `onAbort()` který je volán. Toto volání je poslední pravděpodobností, aby služba vyčistila a uvolnila všechny prostředky, které požadoval. Tato operace se obecně volá, když se v uzlu zjistí trvalá chyba, nebo když Service Fabric nemůže spolehlivě spravovat životní cyklus instance služby z důvodu interního selhání.
* `OnChangeRoleAsync()` se volá, když replika stavové služby mění roli (například primární nebo sekundární). Primárním replikám je dán stav zápisu (můžou vytvářet a zapisovat do spolehlivých kolekcí). Sekundárním replikám je předaný stav čtení (dá se číst jenom z existujících spolehlivých kolekcí). Většina práce ve stavové službě se provádí v primární replice. Sekundární repliky můžou provádět ověřování jen pro čtení, generování sestav, dolování dat nebo jiné úlohy jen pro čtení.

## <a name="next-steps"></a>Další kroky
* [Úvod do Reliable Services](service-fabric-reliable-services-introduction.md)
* [Rychlý Start Reliable Services](service-fabric-reliable-services-quick-start-java.md)
