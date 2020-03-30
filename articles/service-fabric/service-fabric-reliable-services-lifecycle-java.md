---
title: Azure Service Fabric spolehlivý chod životní cyklus
description: Další informace o událostech životního cyklu v aplikaci Azure Service Fabric Reliable Services pomocí Jazyka Java pro stavové a bezstavové služby.
author: PavanKunapareddyMSFT
ms.topic: conceptual
ms.date: 06/30/2017
ms.author: pakunapa
ms.openlocfilehash: 1d3be958a0649ed3e80df2d63adbdf0b91831dbd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75639594"
---
# <a name="reliable-services-lifecycle"></a>Životní cyklus Reliable Services
> [!div class="op_single_selector"]
> * [C# v systému Windows](service-fabric-reliable-services-lifecycle.md)
> * [Java v Linuxu](service-fabric-reliable-services-lifecycle-java.md)
>
>

Spolehlivé služby je jedním z programovacích modelů dostupných ve službě Azure Service Fabric. Když se dozvídáme o životním cyklu spolehlivých služeb, je nejdůležitější porozumět základním událostem životního cyklu. Přesné pořadí událostí závisí na podrobnostech konfigurace. 

Obecně platí, že životní cyklus spolehlivých služeb zahrnuje následující události:

* Při spuštění:
  * Služby jsou konstruovány.
  * Služby mají možnost vytvořit a vrátit nula nebo více naslouchacích procesy.
  * Všechny vrácené posluchače jsou otevřeny pro komunikaci se službou.
  * Metoda služby `runAsync` je volána, takže služba může provést dlouhotrvající nebo práce na pozadí.
* Během vypnutí:
  * Token zrušení, který `runAsync` byl předán do je zrušena a naslouchací procesy jsou uzavřeny.
  * Samotný objekt služby je zničen.

Pořadí událostí ve spolehlivých službách se může mírně změnit v závislosti na tom, zda je spolehlivá služba bezstavová nebo stavová. 

Také pro stavové služby je nutné řešit primární scénář odkládacího stavu. Během této sekvence role primární je převedena do jiné repliky (nebo vrátí zpět) bez vypnutí služby. 

Nakonec musíte přemýšlet o chybách nebo chybách.

## <a name="stateless-service-startup"></a>Spuštění bezstavové služby
Životní cyklus služby bez státní příslušnosti je poměrně jednoduchý. Zde je pořadí událostí:

1. Služba je vytvořena.
2. K těmto událostem dochází paralelně:
    - `StatelessService.createServiceInstanceListeners()`je vyvolána a všechny vrácené posluchače jsou otevřeny. `CommunicationListener.openAsync()`je volána na každém naslouchací proces.
    - Nazývá se `runAsync` metoda`StatelessService.runAsync()`služby ( ).
3. Pokud je k dispozici, `onOpenAsync` je volána vlastní metoda služby. Konkrétně `StatelessService.onOpenAsync()` se nazývá. Toto je neobvyklé přepsání, ale je k dispozici.

Je důležité si uvědomit, že neexistuje žádné řazení mezi volání k vytvoření `runAsync`a otevření naslouchací procesy a volání . Posluchače může otevřít `runAsync` před spuštěním. Podobně `runAsync` může být vyvolána před otevřením naslouchací procesy komunikace nebo před jejich vytvoření. Pokud je vyžadována jakákoli synchronizace, musí ji provést implementátor. Zde jsou některá běžná řešení:

* Někdy posluchači nemohou fungovat, dokud nejsou vytvořeny další informace nebo jiná práce. Pro bezstavové služby, že práce obvykle lze provést v konstruktoru služby. To lze provést `createServiceInstanceListeners()` během hovoru, nebo jako součást konstrukce posluchače samotného.
* Někdy kód `runAsync` v nespustí, dokud posluchači jsou otevřené. V tomto případě je nutná další koordinace. Běžným řešením je přidat příznak v naslouchacích procesech. Příznak označuje dokončení posluchačů. Metoda `runAsync` zkontroluje to před pokračováním skutečné práce.

## <a name="stateless-service-shutdown"></a>Vypnutí služby bez státní příslušnosti
Při vypínání bezstavové služby je dodržen stejný vzor, ale naopak:

1. K těmto událostem dochází paralelně:
    - Všechny otevřené naslouchací procesy jsou uzavřeny. `CommunicationListener.closeAsync()`je volána na každém naslouchací proces.
    - Token zrušení, který `runAsync()` byl předán, je zrušen. Kontrola `isCancelled` vlastnosti tokenu zrušení `true`vrátí a pokud je `throwIfCancellationRequested` volána, `CancellationException`metoda tokenu vyvolá .
2. Po `closeAsync()` dokončení na každém `runAsync()` naslouchací `StatelessService.onCloseAsync()` proces a také dokončí, je volána metoda služby, pokud je k dispozici. Opět se nejedná o běžné přepsání, ale lze jej použít k bezpečnému zavření prostředků, zastavení zpracování na pozadí, dokončení ukládání externího stavu nebo zavření existujících připojení.
3. Po `StatelessService.onCloseAsync()` dokončení je objekt služby zničen.

## <a name="stateful-service-startup"></a>Spuštění stavové služby
Stavové služby mají vzor, který je podobný bezstavové služby, s několika změnami.  Zde je pořadí událostí pro spuštění stavové služby:

1. Služba je vytvořena.
2. `StatefulServiceBase.onOpenAsync()`se nazývá. Toto volání není běžně přepsána ve službě.
3. K těmto událostem dochází paralelně:
    - `StatefulServiceBase.createServiceReplicaListeners()`je vyvolána. 
      - Pokud je služba primární službou, jsou otevřeny všechny vrácené posluchače. `CommunicationListener.openAsync()`je volána na každém naslouchací proces.
      - Pokud je služba sekundární službou, jsou `listenOnSecondary = true` otevřeny pouze naslouchací procesy označené jako otevřené. S naslouchací procesy, které jsou otevřené na sekundární je méně časté.
    - Pokud je služba aktuálně primární, je `StatefulServiceBase.runAsync()` volána metoda služby.
4. Po dokončení všech volání `openAsync()` naslouchací proces repliky a `runAsync()` je volána, `StatefulServiceBase.onChangeRoleAsync()` se nazývá. Toto volání není běžně přepsána ve službě.

Podobně jako bezstavové služby, ve stavové službě, neexistuje žádná koordinace mezi pořadí, ve kterém jsou vytvořeny a otevřeny posluchače a kdy `runAsync` je volána. Pokud potřebujete koordinaci, řešení jsou v podstatě stejná. Ale je tu ještě jeden případ pro stavové služby. Řekněme, že volání, které dorazí na naslouchací procesy komunikace vyžadují informace uchovávané uvnitř některé [spolehlivé kolekce](service-fabric-reliable-services-reliable-collections.md). Vzhledem k tomu, že naslouchací procesy komunikace `runAsync` může otevřít před spolehlivé kolekce jsou čitelné nebo zapisovatelné a před spuštěním, některé další koordinace je nezbytné. Nejjednodušší a nejběžnější řešení je pro naslouchací procesy komunikace vrátit kód chyby. Klient používá kód chyby vědět opakovat požadavek.

## <a name="stateful-service-shutdown"></a>Vypnutí stavové služby
Stejně jako bezstavové služby jsou události životního cyklu během vypnutí stejné jako při spuštění, ale obrácené. Při vypnutí stavové služby dochází k následujícím událostem:

1. K těmto událostem dochází paralelně:
    - Všechny otevřené naslouchací procesy jsou uzavřeny. `CommunicationListener.closeAsync()`je volána na každém naslouchací proces.
    - Token zrušení, který `runAsync()` byl předán, je zrušen. Volání `isCancelled()` metody tokenu zrušení vrátí `true`, a pokud je `throwIfCancellationRequested()` volána, `OperationCanceledException`metoda tokenu vyvolá .
2. Po `closeAsync()` dokončení na každém `runAsync()` naslouchací `StatefulServiceBase.onChangeRoleAsync()` proces a také dokončí služby je volána. Toto volání není běžně přepsána ve službě.

   > [!NOTE]  
   > Čekání `runAsync` na dokončení je nutné pouze v případě, že tato replika je primární replika.

3. Po `StatefulServiceBase.onChangeRoleAsync()` dokončení metody je `StatefulServiceBase.onCloseAsync()` metoda volána. Toto volání je neobvyklé přepsání, ale je k dispozici.
3. Po `StatefulServiceBase.onCloseAsync()` dokončení je objekt služby zničen.

## <a name="stateful-service-primary-swaps"></a>Primární swapy stavové služby
Při spuštění stavové služby jsou otevřeny `runAsync` naslouchací procesy komunikace a metoda je volána pouze pro primární repliky těchto stavových služeb. Sekundární repliky jsou vytvořeny, ale zobrazit žádné další volání. Při spuštění stavové služby, replika, která je aktuálně primární můžete změnit. Události životního cyklu, které může zobrazit stavová replika, závisí na tom, zda je replika, která je během odkládání snížena nebo povýšena.

### <a name="for-the-demoted-primary"></a>Pro degradované primární
Service Fabric potřebuje primární repliku, která je snížena zastavit zpracování zpráv a zastavit všechny práce na pozadí. Tento krok je podobný při vypnutí služby. Jeden rozdíl je, že služba není zničena nebo uzavřena, protože zůstává jako sekundární. Dojde k následující chybě:

1. K těmto událostem dochází paralelně:
    - Všechny otevřené naslouchací procesy jsou uzavřeny. `CommunicationListener.closeAsync()`je volána na každém naslouchací proces.
    - Token zrušení, který `runAsync()` byl předán, je zrušen. Kontrola `isCancelled()` metody tokenu zrušení vrátí `true`. Pokud je volána, `throwIfCancellationRequested()` metoda tokenu `OperationCanceledException`vyvolá .
2. Po `closeAsync()` dokončení na každém `runAsync()` naslouchací `StatefulServiceBase.onChangeRoleAsync()` proces a také dokončí služby je volána. Toto volání není běžně přepsána ve službě.

### <a name="for-the-promoted-secondary"></a>Pro povýšené sekundární
Podobně Service Fabric potřebuje sekundární repliku, která je povýšen a začít naslouchat zprávy v drátě a spustit všechny úlohy na pozadí, které potřebuje k dokončení. Tento proces je podobný při vytvoření služby. Rozdíl je v tom, že samotná replika již existuje. Dojde k následující chybě:

1. K těmto událostem dochází paralelně:
    - `StatefulServiceBase.createServiceReplicaListeners()`je vyvolána a všechny vrácené posluchače jsou otevřeny. `CommunicationListener.openAsync()`je volána na každém naslouchací proces.
    - Je volána `StatefulServiceBase.runAsync()` metoda služby.
2. Po dokončení všech volání `openAsync()` naslouchací proces repliky a `runAsync()` je volána, `StatefulServiceBase.onChangeRoleAsync()` se nazývá. Toto volání není běžně přepsána ve službě.

### <a name="common-issues-during-stateful-service-shutdown-and-primary-demotion"></a>Běžné problémy během vypnutí stavové služby a primární snížení úrovně
Service Fabric změní primární stavové služby z několika důvodů. Nejběžnějšídůvody jsou [vyvažování clusteru](service-fabric-cluster-resource-manager-balancing.md) a [upgrade aplikace](service-fabric-application-upgrade.md). Během těchto operací je důležité, aby služba `cancellationToken`respektovala . To platí také během normálního vypnutí služby, například pokud byla služba odstraněna.

Služby, které nezpracovávají zrušení čistě, mohou zaznamenat několik problémů. Tyto operace jsou pomalé, protože Service Fabric čeká na řádné ukončení služeb. To může nakonec vést k neúspěšné upgrady, které časový čas a vrácení zpět. Selhání ctít zrušení token usoudit také může způsobit nevyvážené clustery. Clustery se stanou nevyváženými, protože uzly se zahřívají. Služby však nelze znovu vyvážit, protože jejich přesunutí jinam trvá příliš dlouho. 

Vzhledem k tomu, že služby jsou stavové, je také pravděpodobné, že [služby](service-fabric-reliable-services-reliable-collections.md)používají spolehlivé kolekce . V Service Fabric, když je primární snížena, jedna z prvních věcí, které se stane, je, že přístup pro zápis do základního stavu je odvolán. To vede k druhé sadě problémů, které mohou ovlivnit životní cyklus služby. Kolekce vrátit výjimky na základě časování a zda je replika přesouvána nebo ukončena. Je důležité správně zpracovat tyto výjimky. 

Výjimky vyzývané service fabric jsou buď trvalé [(`FabricException`)](https://docs.microsoft.com/java/api/system.fabric.exception) nebo přechodné [(`FabricTransientException`).](https://docs.microsoft.com/java/api/system.fabric.exception.fabrictransientexception) Trvalé výjimky by měly být zaznamenány a vyvolány. Přechodné výjimky lze opakovat na základě logiky opakování.

Důležitou součástí testování a ověřování spolehlivých služeb je zpracování `ReliableCollections` výjimek, které pocházejí z použití ve spojení s událostmi životního cyklu služby. Doporučujeme vždy spustit službu pod zatížením. Měli byste také provést upgrady a [chaos testování](service-fabric-controlled-chaos.md) před nasazením do produkčního prostředí. Tyto základní kroky pomáhají zajistit, že vaše služba je implementována správně a že zpracovává události životního cyklu správně.

## <a name="notes-on-service-lifecycle"></a>Poznámky k životnímu cyklu služby
* `runAsync()` Metoda i `createServiceInstanceListeners/createServiceReplicaListeners` volání jsou volitelné. Služba může mít obojí nebo ani jedno. Například pokud služba provádí veškerou svou práci v reakci na volání uživatele, není nutné pro jeho implementaci `runAsync()`. Jsou nezbytné pouze naslouchací procesy komunikace a jejich přidružený kód.  Podobně vytváření a vracející se naslouchací procesy komunikace je volitelné. Služba může mít pouze práci na pozadí, takže `runAsync()`je třeba pouze implementovat .
* Je platný pro službu `runAsync()` úspěšně dokončit a vrátit se z ní. To to není považováno za poruchový stav. Představuje práci na pozadí dokončení služby. Pro stavové spolehlivé `runAsync()` služby by se voláznovu, pokud je služba snížena z primární a pak povýšen zpět na primární.
* Pokud služba ukončí `runAsync()` vyvoláním některé neočekávané výjimky, jedná se o selhání. Objekt služby je vypnut a je hlášena chyba stavu.
* I když neexistuje žádný časový limit pro návrat z těchto metod, okamžitě ztratíte schopnost psát. Proto nemůžete dokončit žádnou skutečnou práci. Doporučujeme, abyste se vrátili co nejrychleji po obdržení žádosti o zrušení. Pokud vaše služba nereaguje na tato volání rozhraní API v přiměřeném čase, service fabric může násilně ukončit vaši službu. Obvykle k tomu dochází pouze během upgradů aplikací nebo při odstraňování služby. Tento časový limit je ve výchozím nastavení 15 minut.
* Selhání v `onCloseAsync()` cestě za `onAbort()` následek volání. Toto volání je poslední šance, nejlepší-úsilí příležitost pro službu vyčistit a uvolnit všechny prostředky, které tvrdily. To se obecně nazývá, když je zjištěna trvalá chyba v uzlu nebo když Service Fabric nemůže spolehlivě spravovat životní cyklus instance služby z důvodu vnitřních selhání.
* `OnChangeRoleAsync()`je volána, když replika stavové služby mění roli, například na primární nebo sekundární. Primární repliky jsou uvedeny stav zápisu (je povoleno vytvářet a zapisovat do spolehlivé kolekce). Sekundární repliky jsou uvedeny stav čtení (lze číst pouze z existující spolehlivé kolekce). Většina práce ve stavové službě se provádí v primární replice. Sekundární repliky můžete provádět ověřování jen pro čtení, generování sestavy, dolování dat nebo jiné úlohy jen pro čtení.

## <a name="next-steps"></a>Další kroky
* [Úvod do spolehlivých služeb](service-fabric-reliable-services-introduction.md)
* [Rychlý start spolehlivých služeb](service-fabric-reliable-services-quick-start-java.md)

