---
title: Přehled životního cyklu spolehlivých služeb
description: Další informace o událostech životního cyklu v aplikaci Azure Service Fabric Reliable Services pro stavové a bezstavové služby.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: fe338ca3f25cd606da7f95f6c9437a3cd3dc4e69
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258275"
---
# <a name="reliable-services-lifecycle-overview"></a>Přehled spolehlivého životního cyklu služeb
> [!div class="op_single_selector"]
> * [C# v systému Windows](service-fabric-reliable-services-lifecycle.md)
> * [Java v Linuxu](service-fabric-reliable-services-lifecycle-java.md)
>
>

Když přemýšlíte o životních cyklech spolehlivých služeb Azure Service Fabric, nejdůležitější jsou základy životního cyklu. Obecně platí, že životní cyklus zahrnuje následující:

- Při spuštění:
  - Služby jsou konstruovány.
  - Služby mají možnost vytvořit a vrátit nula nebo více naslouchacích procesy.
  - Všechny vrácené posluchače jsou otevřeny, což umožňuje komunikaci se službou.
  - Služba **RunAsync** metoda je volána, což umožňuje službě dělat dlouhotrvající úlohy nebo práce na pozadí.
- Během vypnutí:
  - Token zrušení předaný **RunAsync** je zrušena a posluchači jsou uzavřeny.
  - Po zavření naslouchací procesy objekt služby sám je zničen.

Existují podrobnosti o přesném pořadí těchto událostí. Pořadí událostí se může mírně změnit v závislosti na tom, zda je spolehlivá služba bezstavová nebo stavová. Kromě toho pro stavové služby, musíme řešit primární swap scénář. Během této sekvence role Primární je převedena do jiné repliky (nebo vrátí zpět) bez vypnutí služby. A konečně musíme přemýšlet o chybách nebo podmínkách selhání.

## <a name="stateless-service-startup"></a>Spuštění bezstavové služby
Životní cyklus služby bez státní příslušnosti je jednoduchý. Zde je pořadí událostí:

1. Služba je vytvořena.
2. Pak se paralelně dějí dvě věci:
    - `StatelessService.CreateServiceInstanceListeners()`je vyvolána a všechny vrácené posluchače jsou otevřeny. `ICommunicationListener.OpenAsync()`je volána na každém naslouchací proces.
    - Je volána `StatelessService.RunAsync()` metoda služby.
3. Pokud je k dispozici, je volána metoda služby. `StatelessService.OnOpenAsync()` Toto volání je neobvyklé přepsání, ale je k dispozici. V tomto okamžiku lze spustit úlohy inicializace rozšířené služby.

Mějte na paměti, že neexistuje žádné řazení mezi volání k vytvoření a otevření naslouchací procesy a **RunAsync**. Naslouchací procesy lze otevřít před **spuštěním RunAsync.** Podobně můžete vyvolat **RunAsync** před naslouchací procesy komunikace jsou otevřené nebo dokonce konstruovány. Pokud je vyžadována jakákoli synchronizace, je ponechána jako cvičení implementátoru. Zde jsou některá běžná řešení:

  - Někdy posluchači nemohou fungovat, dokud nejsou vytvořeny některé další informace nebo práce. U služeb bez státní příslušnosti, že práce lze obvykle provést v jiných lokalitách, například následující: 
    - V konstruktoru služby.
    - Během `CreateServiceInstanceListeners()` hovoru.
    - V rámci výstavby samotného posluchače.
  - Někdy kód v **RunAsync** nespustí, dokud jsou otevřeny posluchače. V tomto případě je nutná další koordinace. Jedním z běžných řešení je, že je příznak v rámci naslouchací procesy, který označuje, když byly dokončeny. Tento příznak je pak kontrolována v **RunAsync** před pokračováním skutečné práce.

## <a name="stateless-service-shutdown"></a>Vypnutí služby bez státní příslušnosti
Pro vypnutí služby bez stavů je stejný vzor je dodržován, jen v opačném směru:

1. Souběžně:
    - Všechny otevřené naslouchací procesy jsou uzavřeny. `ICommunicationListener.CloseAsync()`je volána na každém naslouchací proces.
    - Token zrušení předaný do `RunAsync()` je zrušen. Kontrola `IsCancellationRequested` vlastnosti tokenu zrušení vrátí hodnotu true, a `ThrowIfCancellationRequested` pokud je `OperationCanceledException`volána, metoda tokenu vyvolá .
2. Po `CloseAsync()` dokončení na každém `RunAsync()` naslouchací `StatelessService.OnCloseAsync()` proces a také dokončí, je volána metoda služby, pokud je k dispozici.  OnCloseAsync je volána, když bezstavová instance služby bude řádně vypnuta. Tato situace může nastat, když je kód služby upgradován, instance služby je přesouvána z důvodu vyrovnávání zatížení nebo je zjištěna přechodná chyba. Je neobvyklé přepsat `StatelessService.OnCloseAsync()`, ale lze jej bezpečně zavřít prostředky, zastavit zpracování na pozadí, dokončit ukládání externího stavu nebo zavřít existující připojení.
3. Po `StatelessService.OnCloseAsync()` dokončení je objekt služby zničen.

## <a name="stateful-service-startup"></a>Spuštění stavové služby
Stavové služby mají podobný vzor jako služby bez stav, s několika změnami. Pro spuštění stavové služby je pořadí událostí následující:

1. Služba je vytvořena.
2. `StatefulServiceBase.OnOpenAsync()`se nazývá. Toto volání není běžně přepsána ve službě.
3. Následující věci se dějí paralelně:
    - `StatefulServiceBase.CreateServiceReplicaListeners()`je vyvolána. 
      - Pokud je služba primární služby, jsou otevřeny všechny vrácené posluchače. `ICommunicationListener.OpenAsync()`je volána na každém naslouchací proces.
      - Pokud je služba sekundární služby, pouze ty `ListenOnSecondary = true` naslouchací procesy označeny jako otevřené. S naslouchací procesy, které jsou otevřené na sekundární je méně časté.
    - Pokud je služba aktuálně Primární, `StatefulServiceBase.RunAsync()` je volána metoda služby.
4. Po dokončení všech volání `OpenAsync()` naslouchací proces repliky a `RunAsync()` je volána, `StatefulServiceBase.OnChangeRoleAsync()` se nazývá. Toto volání není běžně přepsána ve službě.

Podobně jako bezstavové služby neexistuje žádná koordinace mezi pořadí, ve kterém jsou vytvořeny a otevřeny naslouchací procesy a při **runasync** je volána. Pokud potřebujete koordinaci, řešení jsou v podstatě stejná. Existuje jeden další případ pro stavové služby. Řekněme, že volání, které dorazí na naslouchací procesy komunikace vyžadují informace uchovávané uvnitř některé [spolehlivé kolekce](service-fabric-reliable-services-reliable-collections.md).

   > [!NOTE]  
   > Vzhledem k tomu, že naslouchací procesy komunikace může otevřít před spolehlivé kolekce jsou čitelné nebo zapisovatelné a před **RunAsync spustit,** některé další koordinace je nezbytné. Nejjednodušší a nejběžnější řešení je pro naslouchací procesy komunikace vrátit kód chyby, který klient používá vědět opakovat požadavek.

## <a name="stateful-service-shutdown"></a>Vypnutí stavové služby
Stejně jako bezstavové služby jsou události životního cyklu během vypnutí stejné jako při spuštění, ale obrácené. Při vypnutí stavové služby dochází k následujícím událostem:

1. Souběžně:
    - Všechny otevřené naslouchací procesy jsou uzavřeny. `ICommunicationListener.CloseAsync()`je volána na každém naslouchací proces.
    - Token zrušení předaný do `RunAsync()` je zrušen. Kontrola `IsCancellationRequested` vlastnosti tokenu zrušení vrátí hodnotu true, a `ThrowIfCancellationRequested` pokud je `OperationCanceledException`volána, metoda tokenu vyvolá .
2. Po `CloseAsync()` dokončení na každém `RunAsync()` naslouchací `StatefulServiceBase.OnChangeRoleAsync()` proces a také dokončí služby je volána. Toto volání není běžně přepsána ve službě.

   > [!NOTE]  
   > Je nutné čekat na dokončení **runasync** je nutné pouze v případě, že tato replika je primární replika.

3. Po `StatefulServiceBase.OnChangeRoleAsync()` dokončení metody je `StatefulServiceBase.OnCloseAsync()` metoda volána. Toto volání je neobvyklé přepsání, ale je k dispozici.
3. Po `StatefulServiceBase.OnCloseAsync()` dokončení je objekt služby zničen.

## <a name="stateful-service-primary-swaps"></a>Stavová služba Primární swapy
Zatímco je spuštěna stavová služba, pouze primární repliky těchto stavových služeb mají otevřené posluchače komunikace a jejich metodu **RunAsync.** Sekundární repliky jsou vytvořeny, ale zobrazit žádné další volání. Při spuštění stavové služby, replika, která je aktuálně Primární můžete změnit v důsledku poruchy nebo optimalizace vyrovnávání clusteru. Co to znamená z hlediska událostí životního cyklu, které replika může zobrazit? Chování, které se zobrazí stavovou replikou, závisí na tom, zda je replika snížena nebo povýšena během odkládání.

### <a name="for-the-primary-thats-demoted"></a>Pro primárky, které jsou degradovány
Pro primární repliku, která je snížena, Service Fabric potřebuje tuto repliku zastavit zpracování zpráv a ukončit všechny práce na pozadí, které dělá. V důsledku toho tento krok vypadá, jako tomu bylo při vypnutí služby. Jeden rozdíl je, že služba není zničena nebo uzavřena, protože zůstává jako sekundární. Následující api se nazývají:

1. Souběžně:
    - Všechny otevřené naslouchací procesy jsou uzavřeny. `ICommunicationListener.CloseAsync()`je volána na každém naslouchací proces.
    - Token zrušení předaný do `RunAsync()` je zrušen. Kontrola `IsCancellationRequested` vlastnosti tokenu zrušení vrátí hodnotu true, a `ThrowIfCancellationRequested` pokud je `OperationCanceledException`volána, metoda tokenu vyvolá .
2. Po `CloseAsync()` dokončení na každém `RunAsync()` naslouchací `StatefulServiceBase.OnChangeRoleAsync()` proces a také dokončí služby je volána. Toto volání není běžně přepsána ve službě.

### <a name="for-the-secondary-thats-promoted"></a>Pro sekundární, který je povýšen
Podobně Service Fabric potřebuje sekundární repliku, která je povýšen a začít naslouchat zprávy na drátě a spustit všechny úlohy na pozadí, které potřebuje k dokončení. V důsledku toho tento proces vypadá jako při vytvoření služby, s tím rozdílem, že replika sama již existuje. Následující api se nazývají:

1. Souběžně:
    - `StatefulServiceBase.CreateServiceReplicaListeners()`je vyvolána a všechny vrácené posluchače jsou otevřeny. `ICommunicationListener.OpenAsync()`je volána na každém naslouchací proces.
    - Je volána `StatefulServiceBase.RunAsync()` metoda služby.
2. Po dokončení všech volání `OpenAsync()` naslouchací proces repliky a `RunAsync()` je volána, `StatefulServiceBase.OnChangeRoleAsync()` se nazývá. Toto volání není běžně přepsána ve službě.

### <a name="common-issues-during-stateful-service-shutdown-and-primary-demotion"></a>Běžné problémy během vypnutí stavové služby a primární snížení úrovně
Service Fabric změní primární stavové služby z různých důvodů. Nejběžnější jsou [vyvažování clusteru](service-fabric-cluster-resource-manager-balancing.md) a [upgrade aplikace](service-fabric-application-upgrade.md). Během těchto operací (stejně jako při normálním vypnutí služby, jako byste viděli, pokud `CancellationToken`služba byla odstraněna), je důležité, aby služba respektovat . 

Služby, které nezpracovávají zrušení čistě může dojít k několika problémům. Tyto operace jsou pomalé, protože Service Fabric čeká na řádné ukončení služeb. To může nakonec vést k neúspěšné upgrady, které časový čas a vrátit zpět. Nesplnění tokenu zrušení může také způsobit nevyvážené clustery. Clustery se stanou nevyváženými, protože uzly se zahřívají, ale služby nelze znovu vyvážit, protože jejich přesunutí jinam trvá příliš dlouho. 

Vzhledem k tomu, že služby jsou stavové, je také pravděpodobné, že používají [spolehlivé kolekce](service-fabric-reliable-services-reliable-collections.md). V Service Fabric, když primární je snížena, jedna z prvních věcí, které se stane, je, že přístup pro zápis do základního stavu je odvolán. To vede k druhé sadě problémů, které mohou ovlivnit životní cyklus služby. Kolekce vrátit výjimky na základě časování a zda je replika přesouvána nebo ukončena. Tyto výjimky by měly být zpracovány správně. Výjimky vyzývané service fabric spadají do [trvalých (`FabricException`)](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception?view=azure-dotnet) a přechodné ( [`FabricTransientException`)](https://docs.microsoft.com/dotnet/api/system.fabric.fabrictransientexception?view=azure-dotnet) kategorie. Trvalé výjimky by měly být zaznamenány a vyvolány, zatímco přechodné výjimky mohou být opakovány na základě některé logiky opakování.

Zpracování výjimek, které pocházejí `ReliableCollections` z použití ve spojení s událostmi životního cyklu služby je důležitou součástí testování a ověřování spolehlivé služby. Doporučujeme vždy spustit službu pod zatížením při provádění upgradů a [testování chaosu](service-fabric-controlled-chaos.md) před nasazením do produkčního prostředí. Tyto základní kroky pomáhají zajistit, že vaše služba je správně implementována a zpracovává události životního cyklu správně.


## <a name="notes-on-the-service-lifecycle"></a>Poznámky k životnímu cyklu služby
  - `RunAsync()` Metoda i `CreateServiceReplicaListeners/CreateServiceInstanceListeners` volání jsou volitelné. Služba může mít jeden z nich, obojí, nebo ani jeden. Například pokud služba provádí veškerou svou práci v reakci na volání `RunAsync()`uživatele, není nutné pro jeho implementaci . Jsou nezbytné pouze naslouchací procesy komunikace a jejich přidružený kód. Podobně vytváření a vracející se naslouchací procesy komunikace je volitelné, protože služba `RunAsync()`může mít pouze práci na pozadí, a proto je třeba pouze implementovat .
  - Je platný pro službu `RunAsync()` úspěšně dokončit a vrátit se z ní. Dokončení není podmínkou selhání. Dokončení `RunAsync()` označuje, že práce na pozadí služby byla dokončena. Pro stavové spolehlivé `RunAsync()` služby je volána znovu, pokud je replika snížena z primární sekundární a pak povýšen zpět na primární.
  - Pokud služba ukončí `RunAsync()` vyvoláním některé neočekávané výjimky, to představuje selhání. Objekt služby je vypnut a je hlášena chyba stavu.
  - Přestože neexistuje žádný časový limit pro návrat z těchto metod, okamžitě ztratíte možnost zápisu do spolehlivé kolekce a proto nelze dokončit žádnou skutečnou práci. Doporučujeme vám, abyste se vrátili co nejrychleji po obdržení žádosti o zrušení. Pokud vaše služba nereaguje na tato volání rozhraní API v přiměřeném čase, service fabric můžete násilně ukončit službu. Obvykle k tomu dochází pouze během upgradů aplikací nebo při odstraňování služby. Tento časový limit je ve výchozím nastavení 15 minut.
  - Selhání v `OnCloseAsync()` cestě za `OnAbort()` následek volání, což je poslední šance nejlepší úsilí příležitost pro službu vyčistit a uvolnit všechny prostředky, které tvrdily. To se obecně nazývá, když je zjištěna trvalá chyba v uzlu nebo když Service Fabric nemůže spolehlivě spravovat životní cyklus instance služby z důvodu vnitřních selhání.
  - `OnChangeRoleAsync()`je volána, když replika stavové služby mění roli, například na primární nebo sekundární. Primární repliky jsou uvedeny stav zápisu (je povoleno vytvářet a zapisovat do spolehlivé kolekce). Sekundární repliky jsou uvedeny stav čtení (lze číst pouze z existující spolehlivé kolekce). Většina práce ve stavové službě se provádí v primární replice. Sekundární repliky můžete provádět ověřování jen pro čtení, generování sestavy, dolování dat nebo jiné úlohy jen pro čtení.

## <a name="next-steps"></a>Další kroky
- [Úvod do spolehlivých služeb](service-fabric-reliable-services-introduction.md)
- [Spolehlivý rychlý start služeb](service-fabric-reliable-services-quick-start.md)
- [Repliky a instance](service-fabric-concepts-replica-lifecycle.md)
