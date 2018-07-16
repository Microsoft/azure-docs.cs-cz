---
title: Přehled životního cyklu Azure Service Fabric Reliable Services | Dokumentace Microsoftu
description: Další informace o různých životního cyklu události v Service Fabric Reliable Services
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
ms.openlocfilehash: f301c0156265f055f0ebf7cdad8dba7f39f5ba2b
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/14/2018
ms.locfileid: "39044573"
---
# <a name="reliable-services-lifecycle-overview"></a>Přehled životní cyklus Reliable Services
> [!div class="op_single_selector"]
> * [C# v systému Windows](service-fabric-reliable-services-lifecycle.md)
> * [Java v Linuxu](service-fabric-reliable-services-lifecycle-java.md)
>
>

Pokud uvažujete o životního cyklu Azure Service Fabric Reliable Services, jsou základní informace o životní cyklus vašich nejdůležitějších. Obecně platí životní cyklus zahrnuje následující:

- Při spuštění:
  - Služby jsou vytvořeny.
  - Služby mají možnost vytvářet a vrácení nuly nebo více naslouchacích procesů.
  - Žádné vrácené naslouchací procesy jsou otevřené, umožňuje komunikaci se službou.
  - Služby **RunAsync** metoda je volána, umožňuje služba pro provádění dlouhotrvajících úloh nebo práce na pozadí.
- Během vypínání:
  - Předat token zrušení **RunAsync** dojde ke zrušení, a naslouchací procesy jsou zavřené.
  - Po zavření naslouchací procesy samotného objektu služby destrukci.

Existují podrobnosti o řazení těchto událostí. Pořadí událostí můžete trochu změnit v závislosti na tom, jestli je spolehlivé služby Bezstavová nebo stavová. Kromě toho pro stavové služby, musí se zabýváme scénář primárních prohození. Během této sekvence roli z primární přeneseny do jiné repliky (nebo se vrátí zpět) bez vypnutí služby. Nakonec musíte přistupujeme k chybě nebo selhání podmínek.

## <a name="stateless-service-startup"></a>Spuštění bezstavové služby
Životní cyklus bezstavovou službu je jednoduché. Tady je pořadí událostí:

1. Služba je vytvořená.
2. Pak paralelně, stanou dvě věci:
    - `StatelessService.CreateServiceInstanceListeners()` je vyvolána a některé vrátil naslouchací procesy jsou otevřené. `ICommunicationListener.OpenAsync()` je volána na každý naslouchací proces.
    - Služby `StatelessService.RunAsync()` metoda je volána.
3. Pokud jsou k dispozici, služby `StatelessService.OnOpenAsync()` metoda je volána. Toto volání je neobvyklé přepsání, ale je k dispozici. V tuto chvíli můžete spustit úlohy inicializace rozšířené služby.

Pamatujte, že není řazení mezi voláními k vytváření a otevírání naslouchací procesy a **RunAsync**. Naslouchací procesy lze otevřít před **RunAsync** spuštění. Podobně můžete vyvolat **RunAsync** před naslouchací procesy komunikace jsou otevřené nebo dokonce vytvořený. Pokud všechny synchronizace je vyžadována, je ponecháno jako cvičení na implementátora. Tady jsou některá běžná řešení:

  - Někdy naslouchacích procesů nemůže fungovat, dokud některé další informace o vytvoření nebo práce provádí. Pro bezstavové služby, které obvykle práce lze provést v jiných umístěních, jako je následující: 
    - V konstruktoru služby.
    - Během `CreateServiceInstanceListeners()` volání.
    - Jako součást konstrukce naslouchací proces, samotného.
  - V některých případech kód v **RunAsync** nespustí, dokud se naslouchací procesy jsou otevřené. V takovém případě je nutné další koordinace. Běžným řešením je, že je příznak v rámci naslouchací procesy, která určuje, kdy máte dokončena. Tento příznak je pak vrácen se změnami **RunAsync** požádá o samotnou práci.

## <a name="stateless-service-shutdown"></a>Vypnutí bezstavové služby
Pro vypnutí bezstavovou službu, je uveden stejný vzor jenom v opačném pořadí:

1. Paralelní:
    - Všechny otevřené naslouchací procesy jsou uzavřeny. `ICommunicationListener.CloseAsync()` je volána na každý naslouchací proces.
    - Předat token zrušení `RunAsync()` se zruší. Vrácení tokenu zrušení `IsCancellationRequested` vlastnost vrací hodnotu true a pokud je volána, tokenu `ThrowIfCancellationRequested` vyvolá metoda výjimku `OperationCanceledException`.
2. Po `CloseAsync()` dokončen každý naslouchací proces a `RunAsync()` také dokončení služby `StatelessService.OnCloseAsync()` metoda je volána, pokud jsou k dispozici.  OnCloseAsync se volá, když instance bezstavovou službu bude možné řádně vypnout. Tato situace může nastat, když kódu služby se upgraduje, instance služby se přesune kvůli Vyrovnávání zatížení, nebo se detekuje přechodná chyba. Je běžné přepsat `StatelessService.OnCloseAsync()`, ale je možné bezpečně zavřít prostředky, zastavit zpracování na pozadí, dokončení ukládání externího stavu nebo ukončete stávající připojení.
3. Po `StatelessService.OnCloseAsync()` destrukci dokončí, objekt služby.

## <a name="stateful-service-startup"></a>Spuštění stavové služby
Stavové služby mají podobný vzorec pro bezstavové služby s drobnými změnami. Pořadí událostí pro spouštění stavové služby, vypadá takto:

1. Služba je vytvořená.
2. `StatefulServiceBase.OnOpenAsync()` je volána. Toto volání není ve službě běžně přepsána.
3. Paralelní nastat následující věci:
    - `StatefulServiceBase.CreateServiceReplicaListeners()` je vyvolána. 
      - Pokud je služba primární služby, jsou všechny vrácené naslouchacích procesů otevřené. `ICommunicationListener.OpenAsync()` je volána na každý naslouchací proces.
      - Pokud služba je sekundární, pouze tyto moduly pro naslouchání označí jako `ListenOnSecondary = true` jsou otevřené. Naslouchací procesy, které jsou otevřeny v sekundární databáze je méně častý.
    - Pokud služba je aktuálně primární, službu pro `StatefulServiceBase.RunAsync()` metoda je volána.
4. Po všech replik naslouchacího procesu `OpenAsync()` volá dokončit a `RunAsync()` se nazývá `StatefulServiceBase.OnChangeRoleAsync()` je volána. Toto volání není ve službě běžně přepsána.

Podobně jako bezstavové služby neexistuje žádná koordinaci mezi pořadí, ve kterém jsou naslouchací procesy vytvořené a otevřít a kdy **RunAsync** je volána. Pokud potřebujete koordinace, jsou skoro stejné řešení. Existuje jeden další případ pro stavové služby. Řekněme, že volání, které přicházejí na naslouchací procesy komunikace vyžadují informace obsažené v některé [Reliable Collections](service-fabric-reliable-services-reliable-collections.md).

   > [!NOTE]  
   > Protože naslouchací procesy komunikace by bylo možné otevřít před spolehlivé kolekce jsou z něj číst nebo zapisovat a před **RunAsync** by mohl spustit, je nutné některé další koordinace. Nejjednodušším a nejběžnějším řešení je pro naslouchací procesy komunikace na vrátí kód chyby, které klient používá vědět o zkuste požadavek zopakovat.

## <a name="stateful-service-shutdown"></a>Vypnutí stavové služby
Stejně jako bezstavové služby události životního cyklu během vypínání jsou stejné jako při spuštění, ale vrátit zpět. Pokud se právě vypíná stavové služby, dojde k následujícím událostem:

1. Paralelní:
    - Všechny otevřené naslouchací procesy jsou uzavřeny. `ICommunicationListener.CloseAsync()` je volána na každý naslouchací proces.
    - Předat token zrušení `RunAsync()` se zruší. Vrácení tokenu zrušení `IsCancellationRequested` vlastnost vrací hodnotu true a pokud je volána, tokenu `ThrowIfCancellationRequested` vyvolá metoda výjimku `OperationCanceledException`.
2. Po `CloseAsync()` dokončen každý naslouchací proces a `RunAsync()` také dokončení služby `StatefulServiceBase.OnChangeRoleAsync()` je volána. Toto volání není ve službě běžně přepsána.

   > [!NOTE]  
   > Proč čekat pro **RunAsync** na dokončení je nutné, pokud tato replika je primární replikou.

3. Po `StatefulServiceBase.OnChangeRoleAsync()` metoda dokončí, `StatefulServiceBase.OnCloseAsync()` metoda je volána. Toto volání je neobvyklé přepsání, ale je k dispozici.
3. Po `StatefulServiceBase.OnCloseAsync()` destrukci dokončí, objekt služby.

## <a name="stateful-service-primary-swaps"></a>Primární záměna stavové služby
Zatímco stavové služby běží, jenom primární repliky této stavové služby mají jejich naslouchací procesy komunikace otevřít a jejich **RunAsync** metodu s názvem. Sekundární repliky jsou vytvořeny, ale zobrazit žádné další výzvy. Je spuštěn stavové služby, repliky, který je aktuálně primárním změnit. Co to znamená v podmínkách události životního cyklu, které se mohou zobrazit repliky? Chování, které se zobrazí stavová repliky závisí na, jestli je replika se snížena nebo přesunuty během prohození.

### <a name="for-the-primary-thats-demoted"></a>Pro primární, který je snížena
Service Fabric pro primární repliku, která je snížena, musí tato replika zastavit zpracování zpráv a ukončete všechny práce na pozadí, které její probíhající činnosti. Tento krok v důsledku toho vypadá stejně, jako kdyby při vypnutí služby. Jedním rozdílem je, že služba není zničené nebo zavřít, protože zůstává jako sekundární. Při volání rozhraní API následující:

1. Paralelní:
    - Všechny otevřené naslouchací procesy jsou uzavřeny. `ICommunicationListener.CloseAsync()` je volána na každý naslouchací proces.
    - Předat token zrušení `RunAsync()` se zruší. Vrácení tokenu zrušení `IsCancellationRequested` vlastnost vrací hodnotu true a pokud je volána, tokenu `ThrowIfCancellationRequested` vyvolá metoda výjimku `OperationCanceledException`.
2. Po `CloseAsync()` dokončen každý naslouchací proces a `RunAsync()` také dokončení služby `StatefulServiceBase.OnChangeRoleAsync()` je volána. Toto volání není ve službě běžně přepsána.

### <a name="for-the-secondary-thats-promoted"></a>Pro sekundární, která je podporována
Podobně Service Fabric potřebuje sekundární repliky, který je povýšen na zahájit naslouchání zpráv na lince a spuštění úlohy na pozadí, je potřeba dokončit. V důsledku toho tento proces bude vypadat stejně, jako kdyby při vytvoření služby s tím rozdílem, že samotný repliky už existuje. Při volání rozhraní API následující:

1. Paralelní:
    - `StatefulServiceBase.CreateServiceReplicaListeners()` je vyvolána a některé vrátil naslouchací procesy jsou otevřené. `ICommunicationListener.OpenAsync()` je volána na každý naslouchací proces.
    - Služby `StatefulServiceBase.RunAsync()` metoda je volána.
2. Po všech replik naslouchacího procesu `OpenAsync()` volá dokončit a `RunAsync()` se nazývá `StatefulServiceBase.OnChangeRoleAsync()` je volána. Toto volání není ve službě běžně přepsána.

### <a name="common-issues-during-stateful-service-shutdown-and-primary-demotion"></a>Běžné problémy při vypnutí stavové služby a snížení úrovně primární
Service Fabric se změní primární stavové služby pro celou řadu důvodů. Nejběžnější jsou [clusteru vyrovnávání](service-fabric-cluster-resource-manager-balancing.md) a [upgrade aplikace](service-fabric-application-upgrade.md). Během těchto operací (stejně jako při ukončení běžné služby, jako by se zobrazí, pokud byla služba odstraněna), je důležité, že služba respektovat `CancellationToken`. 

Služby, které ke zpracování zrušení čistě může docházet k několik problémů. Tyto operace jsou pomalé, protože Service Fabric počká na zastavení bez výpadku služeb. Takže v konečném důsledku může vést k selhání upgradu tento časový limit a vrátit zpět. Selhání při případném dalším sdílení dodržovat token zrušení, může také způsobit imbalanced clustery. Clustery vyrovnaná, protože uzly získají aktivní, ale služby nemůže být znovu vyrovnána vzhledem k tomu, že to trvá příliš dlouho, a přesunout jej jinde. 

Protože se stavové služby, je také pravděpodobné, že budou používat [Reliable Collections](service-fabric-reliable-services-reliable-collections.md). V Service Fabric při snížení úrovně primární jeden z prvních věcí, které se stane, je, že oprávnění k zápisu do základní stav odvolání. To vede k druhé sadě problémy, které můžou ovlivnit služby životního cyklu. Kolekce výjimky vrácené založené na načasování a určuje, zda je replika přesunutí nebo vypnutí. Tyto výjimky by měl být správně zpracovat. Výjimky vyvolané v Service Fabric spadají do trvalé [(`FabricException`)](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception?view=azure-dotnet) a přechodné [(`FabricTransientException`)](https://docs.microsoft.com/dotnet/api/system.fabric.fabrictransientexception?view=azure-dotnet) kategorií. Trvalé výjimky by zaznamenána a při přechodným výjimkám může opakovat na základě nějaké logiky opakování.

Zpracování výjimek, které pocházejí z používání `ReliableCollections` spolu s událostí životního cyklu služeb je důležitou součástí testování a ověřování spolehlivé služby. Doporučujeme vám vždy spustit služby zatížení při provádění upgradu a [chaos testování](service-fabric-controlled-chaos.md) před nasazením do produkčního prostředí. Těchto základních kroků pomáhají zajistit, že vaše služba se implementuje správně a správně zpracovává události životního cyklu.


## <a name="notes-on-the-service-lifecycle"></a>Poznámky k služby životního cyklu
  - Oba `RunAsync()` metoda a `CreateServiceReplicaListeners/CreateServiceInstanceListeners` volání jsou volitelné. Služba může mít jednu z nich, oběma nebo. Například pokud služba svou práci v reakci na volání na uživatele, není nutné, aby se implementovat `RunAsync()`. Jen pro naslouchací procesy komunikace a jejich přidružené kódu jsou potřeba. Podobně, vytváření a vrácení naslouchací procesy komunikace je nepovinný – služba může mít pouze práce na pozadí a to pouze musí implementovat `RunAsync()`.
  - To platí pro službu k dokončení `RunAsync()` úspěšně a vrácené z něj. Dokončení není chybový stav. Dokončení `RunAsync()` označuje dokončení práce na pozadí služby. Pro stavové služby reliable services `RunAsync()` je znovu volána, když je snížena z primární do sekundární lokality a pak povýšen zpět na primární repliku.
  - Pokud služba ukončí `RunAsync()` vyvoláním některé došlo k neočekávané výjimce to představuje selhání. Objekt služby je vypnutý a hlášení stavu chyby.
  - Ačkoli neexistuje žádný časový limit při návratu z těchto metod, okamžitě ztratit možnost zapisovat do spolehlivé kolekce a proto nelze dokončit všechny skutečnou práci. Doporučujeme, aby se vrátit co nejrychleji obdrží žádost o zrušení. Pokud vaše služba neodpoví na těchto volání rozhraní API v rozumném čase, Service Fabric můžete ukončit nuceně vaší služby. Obvykle tato situace nastane pouze při upgradu aplikace nebo při odstranění služby. Tento časový limit je 15 minut, ve výchozím nastavení.
  - Selhání v `OnCloseAsync()` cesta výsledkem `OnAbort()` volána, což je poslední odpovídající best effort příležitosti, jak služba vyčistit a uvolnit všechny prostředky, které jste uplatnili. Nazývá se obecně při zjištění trvalé chyby na uzlu, nebo když Service Fabric nelze spolehlivě spravovat životní cyklus instance služby z důvodu vnitřní chyby.
  - `OnChangeRoleAsync()` je volána, když replika stavové služby je třeba změna role, na primární nebo sekundární. Primární repliky jsou uvedeny stav zápisu (jsou povoleny pro vytvoření a zápis do spolehlivé kolekce). Sekundární repliky jsou uvedeny další stav (lze číst pouze ze stávající Reliable Collections). Většinu práce do stavové služby se provádí na primární replice. Sekundární repliky můžete provádět ověřování jen pro čtení, generování sestav, dolování dat nebo jiné úlohy jen pro čtení.

## <a name="next-steps"></a>Další postup
- [Úvod do modelu Reliable Services](service-fabric-reliable-services-introduction.md)
- [Rychlý start Reliable Services](service-fabric-reliable-services-quick-start.md)
- [Repliky a instance](service-fabric-concepts-replica-lifecycle.md)
