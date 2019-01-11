---
title: Životní cyklus Azure Service Fabric Reliable Services | Dokumentace Microsoftu
description: Další informace o události životního cyklu v Service Fabric Reliable Services.
services: service-fabric
documentationcenter: java
author: PavanKunapareddyMSFT
manager: timlt
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/30/2017
ms.author: pakunapa
ms.openlocfilehash: 3254b29ed380b526be6d5fe5f671adeccbd8ea46
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2019
ms.locfileid: "54196701"
---
# <a name="reliable-services-lifecycle"></a>Životní cyklus Reliable Services
> [!div class="op_single_selector"]
> * [C# v systému Windows](service-fabric-reliable-services-lifecycle.md)
> * [Java v Linuxu](service-fabric-reliable-services-lifecycle-java.md)
>
>

Reliable Services je jedním z programovacích modelů, které jsou k dispozici v Azure Service Fabric. Při získávání informací o životní cyklus Reliable Services, je nejdůležitější pochopit životní cyklus základní události. Řazení událostí závisí na podrobnosti o konfiguraci. 

Obecně platí životní cyklus Reliable Services zahrnuje následující události:

* Při spuštění:
  * Služby jsou vytvořeny.
  * Služby mají možnost vytvářet a vrácení nuly nebo více naslouchacích procesů.
  * Žádné vrácené naslouchací procesy jsou otevřené pro komunikaci se službou.
  * Služby `runAsync` metoda je volána, tak službu to můžete provést dlouho běžící nebo práce na pozadí.
* Během vypínání:
  * Token zrušení, který byl předán `runAsync` dojde ke zrušení, a naslouchací procesy jsou zavřené.
  * Samotný objekt služby destrukci.

Pořadí událostí v modelu Reliable Services se může mírně změnit v závislosti na tom, jestli je spolehlivé služby Bezstavová nebo stavová. 

Kromě toho pro stavové služby, musí řešit scénář primárních prohození. Během této sekvence roli z primární přeneseny do jiné repliky (nebo se vrátí zpět) bez vypnutí služby. 

Nakonec budete muset uvažovat o chybě nebo selhání podmínek.

## <a name="stateless-service-startup"></a>Spuštění bezstavové služby
Životní cyklus bezstavovou službu je poměrně jednoduché. Tady je pořadí událostí:

1. Služba je vytvořená.
2. Tyto události se generují paralelně:
    - `StatelessService.createServiceInstanceListeners()` je vyvolána, a některé vrátil naslouchací procesy jsou otevřené. `CommunicationListener.openAsync()` je volána na každý naslouchací proces.
    - Služby `runAsync` – metoda (`StatelessService.runAsync()`) je volána.
3. Pokud jsou k dispozici, služby vlastní `onOpenAsync` metoda je volána. Konkrétně `StatelessService.onOpenAsync()` je volána. To je běžné přepsání, ale je k dispozici.

Je důležité si uvědomit, že neexistuje žádný výsledek řazení mezi volání k vytváření a otevírání naslouchací procesy a volání `runAsync`. Naslouchací procesy může otevřít před `runAsync` spuštění. Obdobně `runAsync` může být vyvolána předtím, než jsou otevřené naslouchací procesy komunikace nebo před i byl vytvořen. Pokud všechny synchronizace se požaduje, musí se provádí implementátor. Tady jsou některá běžná řešení:

* Někdy naslouchacích procesů nemůže fungovat, dokud se vytvoří další informace nebo se provádí další práci. Pro bezstavové služby, které práce lze provést obvykle v konstruktoru služby. Můžete to provést během `createServiceInstanceListeners()` volání, nebo jako součást konstrukce naslouchací proces, samotného.
* V některých případech kód v `runAsync` nespustí, dokud naslouchací procesy jsou otevřené. V takovém případě je nutné další koordinace. Běžným řešením je přidat příznak v naslouchacím procesům. Příznak určuje, po dokončení naslouchací procesy. `runAsync` Tato metoda zkontroluje před pokračováním samotnou práci.

## <a name="stateless-service-shutdown"></a>Vypnutí bezstavové služby
Při vypínání bezstavovou službu, je stejný vzor po něm, ale v opačném pořadí:

1. Tyto události se generují paralelně:
    - Všechny otevřené naslouchací procesy jsou uzavřeny. `CommunicationListener.closeAsync()` je volána na každý naslouchací proces.
    - Token zrušení, který byl předán `runAsync()` se zruší. Kontroluje se token zrušení `isCancelled` vrátí vlastnost `true`a pokud je volána, tokenu `throwIfCancellationRequested` vyvolá metoda výjimku `CancellationException`.
2. Když `closeAsync()` dokončen každý naslouchací proces a `runAsync()` také dokončení služby `StatelessService.onCloseAsync()` metoda je volána, pokud je k dispozici. Znovu nejedná se o běžné přepsání, ale je možné bezpečně zavřít prostředky, zastavit zpracování na pozadí, dokončení ukládání externího stavu nebo ukončete stávající připojení.
3. Po `StatelessService.onCloseAsync()` destrukci dokončí, objekt služby.

## <a name="stateful-service-startup"></a>Spuštění stavové služby
Stavové služby mají vzor, který je podobný bezstavové služby s drobnými změnami.  Tady je pořadí událostí pro spouštění stavové služby:

1. Služba je vytvořená.
2. `StatefulServiceBase.onOpenAsync()` je volána. Toto volání není ve službě běžně přepsána.
3. Tyto události se generují paralelně:
    - `StatefulServiceBase.createServiceReplicaListeners()` je vyvolána. 
      - Pokud je služba primární služby, jsou všechny vrácené naslouchacích procesů otevřené. `CommunicationListener.openAsync()` je volána na každý naslouchací proces.
      - Pokud služba je sekundární, pouze naslouchacích procesů označí jako `listenOnSecondary = true` jsou otevřené. Naslouchací procesy, které jsou otevřeny v sekundární databáze je méně častý.
    - Pokud služba je aktuálně primární, službu pro `StatefulServiceBase.runAsync()` metoda je volána.
4. Po všech replik naslouchacího procesu `openAsync()` volá dokončit a `runAsync()` se nazývá `StatefulServiceBase.onChangeRoleAsync()` je volána. Toto volání není ve službě běžně přepsána.

Podobně jako bezstavové služby stavové služby, neexistuje žádný koordinaci mezi pořadí, ve kterém naslouchací procesy jsou a otevře a kdy `runAsync` je volána. Pokud potřebujete koordinace, jsou skoro stejné řešení. Existuje ale jeden další případ pro stavové služby. Řekněme, že volání, které přicházejí na naslouchací procesy komunikace vyžadují informace obsažené v některé [Reliable Collections](service-fabric-reliable-services-reliable-collections.md). Protože naslouchací procesy komunikace může otevřít před spolehlivé kolekce jsou z něj číst nebo zapisovat a před `runAsync` spustí, některé další koordinace je nezbytné. Nejjednodušším a nejběžnějším řešení je pro naslouchací procesy komunikace vrátit chybový kód. Klient používá kód chyby: vědět o zkuste požadavek zopakovat.

## <a name="stateful-service-shutdown"></a>Vypnutí stavové služby
Stejně jako bezstavové služby události životního cyklu během vypínání jsou stejné jako při spuštění, ale vrátit zpět. Pokud se právě vypíná stavové služby, dojde k následujícím událostem:

1. Tyto události se generují paralelně:
    - Všechny otevřené naslouchací procesy jsou uzavřeny. `CommunicationListener.closeAsync()` je volána na každý naslouchací proces.
    - Token zrušení, který byl předán `runAsync()` se zrušila. Token zrušení volání `isCancelled()` vrátí metoda `true`a pokud je volána, tokenu `throwIfCancellationRequested()` vyvolá metoda výjimku `OperationCanceledException`.
2. Po `closeAsync()` dokončen každý naslouchací proces a `runAsync()` také dokončení služby `StatefulServiceBase.onChangeRoleAsync()` je volána. Toto volání není ve službě běžně přepsána.

   > [!NOTE]  
   > Čekání na `runAsync` dokončete je nezbytné pouze v případě, že tato replika je primární replikou.

3. Po `StatefulServiceBase.onChangeRoleAsync()` metoda dokončí, `StatefulServiceBase.onCloseAsync()` metoda je volána. Toto volání je neobvyklé přepsání, ale je k dispozici.
3. Po `StatefulServiceBase.onCloseAsync()` destrukci dokončí, objekt služby.

## <a name="stateful-service-primary-swaps"></a>Primární záměna stavové služby
Je spuštěn stavové služby, jsou otevřené naslouchací procesy komunikace a `runAsync` metoda je volána pouze pro primární repliky této stavové služby. Sekundární repliky jsou vytvořeny, ale zobrazit žádné další výzvy. Je spuštěn stavové služby, repliky, který je aktuálně primárním změnit. Události životního cyklu, které můžete zobrazit stavové repliky, závisí na, jestli je replika se snížena nebo přesunuty během prohození.

### <a name="for-the-demoted-primary"></a>Pro primární snížila.
Service Fabric potřebuje primární repliky, který je převeden na zastavení zpracování zpráv a zastavte všechny práce na pozadí. Tento krok je podobný při vypnutí služby. Jedním rozdílem je, že služba není zničené nebo closed, protože zůstává jako sekundární. Dojde k následující chybě:

1. Tyto události se generují paralelně:
    - Všechny otevřené naslouchací procesy jsou uzavřeny. `CommunicationListener.closeAsync()` je volána na každý naslouchací proces.
    - Token zrušení, který byl předán `runAsync()` se zruší. Vrácení tokenu zrušení `isCancelled()` vrátí metoda `true`. Pokud je volána, tokenu `throwIfCancellationRequested()` vyvolá metoda výjimku `OperationCanceledException`.
2. Po `closeAsync()` dokončen každý naslouchací proces a `runAsync()` také dokončení služby `StatefulServiceBase.onChangeRoleAsync()` je volána. Toto volání není ve službě běžně přepsána.

### <a name="for-the-promoted-secondary"></a>Pro sekundární zvýšenou úrovní
Podobně Service Fabric potřebuje sekundární repliky, který je propagována započít naslouchání zpráv na lince a ke spuštění libovolné úlohy na pozadí, které jsou potřebné k dokončení. Tento proces je podobný při vytvoření služby. Rozdíl je, že samotný repliky už existuje. Dojde k následující chybě:

1. Tyto události se generují paralelně:
    - `StatefulServiceBase.createServiceReplicaListeners()` je vyvolána a některé vrátil naslouchací procesy jsou otevřené. `CommunicationListener.openAsync()` je volána na každý naslouchací proces.
    - Služby `StatefulServiceBase.runAsync()` metoda je volána.
2. Po všech replik naslouchacího procesu `openAsync()` volá dokončit a `runAsync()` se nazývá `StatefulServiceBase.onChangeRoleAsync()` je volána. Toto volání není ve službě běžně přepsána.

### <a name="common-issues-during-stateful-service-shutdown-and-primary-demotion"></a>Běžné problémy při vypnutí stavové služby a snížení úrovně primární
Service Fabric se změní primární stavové služby z několika důvodů. Mezi nejběžnější důvody jsou [clusteru vyrovnávání](service-fabric-cluster-resource-manager-balancing.md) a [upgrade aplikace](service-fabric-application-upgrade.md). Během těchto operací, je důležité, že služba respektuje `cancellationToken`. To platí i během vypínání běžné služby, třeba když služba se odstranila.

Služby, které nezpracovávají zrušení čistě může docházet k několik problémů. Tyto operace jsou pomalé, protože Service Fabric počká na zastavení bez výpadku služeb. To může nakonec vést k selhání upgradu, které vypršení časového limitu a vrácení zpět. Selhání při případném dalším sdílení dodržovat token rušení, který může také způsobit imbalanced clustery. Protože získat aktivní uzly clusterů vyrovnaná. Ale služby nemůže být znovu vyrovnána vzhledem k tomu, že to trvá příliš dlouho, a přesunout jej jinde. 

Protože se stavové služby, je také pravděpodobnost, že služby využije [Reliable Collections](service-fabric-reliable-services-reliable-collections.md). V Service Fabric při snížení úrovně primární jeden z prvních věcí, které se stane, je, že oprávnění k zápisu do základní stav odvolání. To vede k druhé sadě problémy, které by mohly ovlivnit služby životního cyklu. Kolekce výjimky vrácené založené na načasování a určuje, zda je replika přesunutí nebo vypnutí. Je důležité, abyste správně zpracovat tyto výjimky. 

Výjimky vyvolané v Service Fabric jsou buď trvalé [(`FabricException`)](https://docs.microsoft.com/java/api/system.fabric.exception) nebo přechodném [(`FabricTransientException`)](https://docs.microsoft.com/java/api/system.fabric.exception.fabrictransientexception). Trvalé výjimky by zaznamenána a vyvolána výjimka. Přechodným výjimkám může opakovat, na základě logiky opakování.

Důležitou součástí testování a ověřování modelu Reliable Services zpracovává výjimky, které pocházejí z používání `ReliableCollections` spolu s událostí životního cyklu služeb. Doporučujeme vždy spustit služby pod zátěží. By měl také provést upgrady a [chaos testování](service-fabric-controlled-chaos.md) před nasazením do produkčního prostředí. Těchto základních kroků pomáhají zajistit, že vaše služba se implementuje správně a zda správně zpracovává události životního cyklu.

## <a name="notes-on-service-lifecycle"></a>Poznámky k služby životního cyklu
* Oba `runAsync()` metoda a `createServiceInstanceListeners/createServiceReplicaListeners` volání jsou volitelné. Služba může mít jeden, obou nebo ani jedna. Například pokud služba svou práci v reakci na volání na uživatele, není nutné, aby se implementovat `runAsync()`. Jen pro naslouchací procesy komunikace a jejich přidružené kódu jsou potřeba.  Podobně vytváření a vrací naslouchací procesy komunikace je volitelné. Služba může mít pouze na pozadí pracovní provedete, proto pouze musí implementovat `runAsync()`.
* To platí pro službu k dokončení `runAsync()` úspěšně a vrácené z něj. Toto není považováno za chybový stav. Představuje službu dokončení práce na pozadí. Pro stavové služby Reliable Services `runAsync()` být volána znovu, pokud je služba snížena z primární a pak povýšen zpět na primární.
* Pokud služba ukončí `runAsync()` vyvoláním některé Neočekávaná výjimka. to je selhání. Objekt služby je vypnutý, a oznámí se chyba stavu.
* Ačkoli neexistuje žádný časový limit při návratu z těchto metod, okamžitě ztratit možnost zapisovat. Proto nelze dokončit všechny skutečnou práci. Doporučujeme, abyste se vrátit co nejrychleji obdrží žádost o zrušení. Pokud vaše služba nereaguje na těchto volání rozhraní API v rozumném čase, Service Fabric může ukončit nuceně vaší služby. K tomu obvykle dochází pouze během upgradu aplikace nebo při odstranění služby. Tento časový limit je 15 minut, ve výchozím nastavení.
* Selhání v `onCloseAsync()` cesta výsledkem `onAbort()` volána. Toto volání je poslední možnost, best effort příležitosti pro službu vyčistit a uvolnit všechny prostředky, které jste uplatnili. Nazývá se obecně při zjištění trvalé chyby na uzlu, nebo když Service Fabric nelze spolehlivě spravovat životní cyklus instance služby z důvodu vnitřní chyby.
* `OnChangeRoleAsync()` je volána, když replika stavové služby je třeba změna role, na primární nebo sekundární. Primární repliky jsou uvedeny stav zápisu (jsou povoleny pro vytvoření a zápis do spolehlivé kolekce). Sekundární repliky jsou uvedeny další stav (lze číst pouze ze stávající Reliable Collections). Většinu práce do stavové služby se provádí na primární replice. Sekundární repliky můžete provádět ověřování jen pro čtení, generování sestav, dolování dat nebo jiné úlohy jen pro čtení.

## <a name="next-steps"></a>Další postup
* [Úvod do modelu Reliable Services](service-fabric-reliable-services-introduction.md)
* [Rychlý start Reliable Services](service-fabric-reliable-services-quick-start-java.md)

