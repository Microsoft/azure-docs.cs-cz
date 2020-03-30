---
title: 'Vytvoření první aplikace Service Fabric v C #'
description: Úvod k vytvoření aplikace Microsoft Azure Service Fabric s bezstavové a stavové služby.
ms.topic: conceptual
ms.date: 07/10/2019
ms.custom: sfrev
ms.openlocfilehash: 15dd9bf6ac19bdac7bc8b50fc70e0b3b0a4e9a83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77083767"
---
# <a name="get-started-with-reliable-services"></a>Začínáme s Reliable Services

> [!div class="op_single_selector"]
> * [C# v systému Windows](service-fabric-reliable-services-quick-start.md)
> * [Java v Linuxu](service-fabric-reliable-services-quick-start-java.md)

Aplikace Azure Service Fabric obsahuje jednu nebo více služeb, které spouštějí váš kód. Tato příručka ukazuje, jak vytvořit bezstavové a stavové aplikace Service Fabric se [spolehlivými službami](service-fabric-reliable-services-introduction.md).  

## <a name="basic-concepts"></a>Základní koncepty

Chcete-li začít se spolehlivými službami, stačí pochopit několik základních pojmů:

* **Typ služby**: Toto je implementace služby. Je definovántřídou, kterou napíšete, která rozšiřuje `StatelessService` a všechny další kód nebo závislosti v něm použité, spolu s názvem a číslem verze.
* **Named service instance**: Chcete-li spustit službu, vytvořte pojmenované instance typu služby, podobně jako vytváříte instance objektů typu třídy. Instance služby má název ve formě identifikátoru URI pomocí "fabric:/" například "fabric:/MyApp/MyService".
* **Hostitel služby**: Pojmenované instance služby, které vytvoříte, musí být spuštěny uvnitř hostitelského procesu. Hostitel služby je pouze proces, kde lze spustit instance služby.
* **Registrace služby**: Registrace spojuje vše dohromady. Typ služby musí být registrován s runtime Service Fabric v hostiteli služby, aby service fabric mohl vytvářet instance, které mají být spuštěny.  

## <a name="create-a-stateless-service"></a>Vytvoření bezstavové služby

Bezstavová služba je typ služby, která je v současné době normou v cloudových aplikacích. Je považován za bezstavové, protože služba sama o sobě neobsahuje data, která je třeba uložit spolehlivě nebo vysoce dostupné. Pokud se instance služby bez stavů vypne, dojde ke ztrátě všech vnitřních stavů. V tomto typu služby musí být stav trvalý do externího úložiště, jako jsou tabulky Azure nebo databáze SQL, aby byl vysoce dostupný a spolehlivý.

Spusťte Visual Studio 2017 nebo Visual Studio 2019 jako správce a vytvořte nový projekt aplikace Service Fabric s názvem *HelloWorld*:

![Vytvoření nové aplikace Service Fabric pomocí dialogového okna Nový projekt](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject.png)

Potom vytvořte bezstavový projekt služby pomocí **rozhraní .NET Core 2.0** s názvem *HelloWorldStateless*:

![V druhém dialogovém okně vytvořte projekt bezstavové služby](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject2.png)

Vaše řešení nyní obsahuje dva projekty:

* *HelloWorld*. Toto je *aplikační* projekt, který obsahuje vaše *služby*. Obsahuje také manifest aplikace, který popisuje aplikaci, stejně jako řadu skriptů prostředí PowerShell, které vám pomohou nasadit aplikaci.
* *HelloWorldStateless*. Toto je projekt služby. Obsahuje implementaci bezstavové služby.

## <a name="implement-the-service"></a>Implementace služby

Otevřete soubor **HelloWorldStateless.cs** v projektu servisu. V Service Fabric služby můžete spustit libovolnou obchodní logiku. Rozhraní API služby poskytuje dva vstupní body pro váš kód:

* Metoda otevřeného vstupního bodu nazvaná *RunAsync*, kde můžete začít s pouštět všechny úlohy, včetně dlouhotrvajících výpočetních úloh.

```csharp
protected override async Task RunAsync(CancellationToken cancellationToken)
{
    ...
}
```

* Vstupní bod komunikace, do kterého můžete připojit příchozí zásobník, například ASP.NET Core. Toto je místo, kde můžete začít přijímat požadavky od uživatelů a dalších služeb.

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    ...
}
```

V tomto kurzu se zaměříme na metodu vstupního `RunAsync()` bodu. Toto je místo, kde můžete okamžitě spustit kód.
Šablona projektu obsahuje ukázkovou implementaci tohoto `RunAsync()` přírůstku postupného počtu.

> [!NOTE]
> Podrobnosti o tom, jak pracovat s komunikačním zásobníkem, najdete [v tématu Komunikace služby s ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md)

### <a name="runasync"></a>Synchronizace runasync

```csharp
protected override async Task RunAsync(CancellationToken cancellationToken)
{
    // TODO: Replace the following sample code with your own logic
    //       or remove this RunAsync override if it's not needed in your service.

    long iterations = 0;

    while (true)
    {
        cancellationToken.ThrowIfCancellationRequested();

        ServiceEventSource.Current.ServiceMessage(this.Context, "Working-{0}", ++iterations);

        await Task.Delay(TimeSpan.FromSeconds(1), cancellationToken);
    }
}
```

Platforma volá tuto metodu, když je umístěna instance služby a připravena ke spuštění. Pro službu bez stavů, to jednoduše znamená, když je otevřena instance služby. Token zrušení je k dispozici pro koordinaci, když je třeba zavřít instanci služby. V Service Fabric tento otevřený/zavřít cyklus instance služby může dojít mnohokrát po dobu životnosti služby jako celku. K tomu může dojít z různých důvodů, včetně:

* Systém přesune instance služby pro vyrovnávání prostředků.
* V kódu dochází k chybám.
* Aplikace nebo systém je inovován.
* Základní hardware dochází k výpadku.

Tato orchestrace je spravována systémem, aby vaše služba byla vysoce dostupná a správně vyvážená.

`RunAsync()`by neměla blokovat synchronně. Implementace RunAsync by měla vrátit Task nebo čekat na všechny dlouhotrvající nebo blokovací operace povolit modul runtime pokračovat. Poznámka: `while(true)` ve smyčce v předchozím příkladu `await Task.Delay()` task-returning se používá. Pokud vaše úloha musí blokovat synchronně, měli `Task.Run()` byste `RunAsync` naplánovat nový úkol s ve vaší implementaci.

Zrušení úlohy je kooperativní úsilí řízené zadaným tokenem zrušení. Systém bude čekat na ukončení úlohy (úspěšným dokončením, zrušením nebo chybou), než se přesune dál. Je důležité ctít zrušení token, dokončit jakoukoli `RunAsync()` práci a ukončit co nejrychleji, když systém požaduje zrušení.

V tomto příkladu bezstavové služby je počet uložen v místní proměnné. Ale protože se jedná o bezstavovou službu, hodnota, která je uložena existuje pouze pro aktuální životní cyklus jeho instance služby. Při přesunu nebo restartování služby dojde ke ztrátě hodnoty.

## <a name="create-a-stateful-service"></a>Vytvoření stavové služby

Service Fabric zavádí nový druh služby, která je stavová. Stavová služba může spolehlivě udržovat stav v rámci samotné služby, která je umístěna společně s kódem, který ji používá. Stav je vysoce k dispozici Service Fabric bez nutnosti zachovat stav do externího úložiště.

Chcete-li převést hodnotu čítače z bezstavové na vysoce dostupné a trvalé, i když se služba přesune nebo restartuje, potřebujete stavovou službu.

In the same *HelloWorld* application, you can add a new service by right-clicking on the Services references in the application project and selecting **Add -> New Service Fabric Service**.

![Přidání služby do aplikace Service Fabric](media/service-fabric-reliable-services-quick-start/hello-stateful-NewService.png)

Vyberte **.NET Core 2.0 -> stavové služby** a pojmenujte ji *HelloWorldStateful*. Klikněte na tlačítko **OK**.

![Vytvoření nové stavové služby Service Fabric pomocí dialogového okna Nový projekt](media/service-fabric-reliable-services-quick-start/hello-stateful-NewProject.png)

Vaše aplikace by nyní měla mít dvě služby: bezstavovou službu *HelloWorldStateless* a stavovou službu *HelloWorldStateful*.

Stavová služba má stejné vstupní body jako bezstavová služba. Hlavním rozdílem je dostupnost *zprostředkovatele stavu,* který může spolehlivě ukládat stav. Service Fabric je dodáván s implementací zprostředkovatele stavu s názvem [Spolehlivé kolekce](service-fabric-reliable-services-reliable-collections.md), která umožňuje vytvářet replikované datové struktury prostřednictvím správce spolehlivého stavu. Stavová spolehlivá služba používá tohoto poskytovatele stavu ve výchozím nastavení.

Otevřete **HelloWorldStateful.cs** v *HelloWorldStateful*, který obsahuje následující metodu RunAsync:

```csharp
protected override async Task RunAsync(CancellationToken cancellationToken)
{
    // TODO: Replace the following sample code with your own logic
    //       or remove this RunAsync override if it's not needed in your service.

    var myDictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");

    while (true)
    {
        cancellationToken.ThrowIfCancellationRequested();

        using (var tx = this.StateManager.CreateTransaction())
        {
            var result = await myDictionary.TryGetValueAsync(tx, "Counter");

            ServiceEventSource.Current.ServiceMessage(this.Context, "Current Counter Value: {0}",
                result.HasValue ? result.Value.ToString() : "Value does not exist.");

            await myDictionary.AddOrUpdateAsync(tx, "Counter", 0, (key, value) => ++value);

            // If an exception is thrown before calling CommitAsync, the transaction aborts, all changes are
            // discarded, and nothing is saved to the secondary replicas.
            await tx.CommitAsync();
        }

        await Task.Delay(TimeSpan.FromSeconds(1), cancellationToken);
    }
```

### <a name="runasync"></a>Synchronizace runasync

`RunAsync()`funguje podobně ve stavových a bezstavových službách. Však ve stavu služby `RunAsync()`platformy provádí další práci vaším jménem před tím, než provede . Tato práce může zahrnovat zajištění, že spolehlivé správce stavu a spolehlivé kolekce jsou připraveny k použití.

### <a name="reliable-collections-and-the-reliable-state-manager"></a>Spolehlivé kolekce a spolehlivý správce stavu

```csharp
var myDictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");
```

[IReliableDictionary](https://msdn.microsoft.com/library/dn971511.aspx) je implementace slovníku, který můžete použít ke spolehlivému ukládání stavu ve službě. Pomocí service fabric a spolehlivé kolekce, můžete ukládat data přímo ve vaší službě bez nutnosti externí trvalé úložiště. Spolehlivé kolekce, aby vaše data vysoce dostupné. Service Fabric toho dosáhne vytvořením a správou více *replik služby* za vás. Poskytuje také rozhraní API, které abstrahuje pryč složitosti správy těchto replik a jejich přechody stavu.

Spolehlivé kolekce můžete uložit libovolný typ .NET, včetně vlastní typy, s několika upozornění:

* Service Fabric umožňuje váš stav vysoce dostupné *replikací* stavu mezi uzly a spolehlivé kolekce ukládat data na místní disk na každé replice. To znamená, že vše, co je uloženo v spolehlivé kolekce musí být *serializovatelné*. Ve výchozím nastavení spolehlivé kolekce použít [DataContract](https://msdn.microsoft.com/library/system.runtime.serialization.datacontractattribute%28v=vs.110%29.aspx) pro serializaci, takže je důležité se ujistit, že vaše typy jsou [podporovány serializátorem smlouvy dat](https://msdn.microsoft.com/library/ms731923%28v=vs.110%29.aspx) při použití výchozíserializátor.
* Objekty jsou replikovány pro vysokou dostupnost při potvrzení transakcí na spolehlivé kolekce. Objekty uložené v spolehlivé kolekce jsou uloženy v místní paměti ve vaší službě. To znamená, že máte místní odkaz na objekt.
  
   Je důležité, abyste nemutovali místní instance těchto objektů bez provedení operace aktualizace spolehlivé kolekce v transakci. Důvodem je, že změny místních instancí objektů nebudou replikovány automaticky. Je nutné znovu vložit objekt zpět do slovníku nebo použít jednu z metod *aktualizace* ve slovníku.

Správce spolehlivého stavu spravuje spolehlivé kolekce za vás. Můžete jednoduše požádat správce spolehlivého stavu o spolehlivou sbírku podle jména kdykoli a na jakémkoli místě ve vaší službě. Správce spolehlivého stavu zajišťuje, že získáte odkaz zpět. Nedoporučujeme ukládat odkazy na spolehlivé instance kolekce v proměnných nebo vlastnostech členů třídy. Zvláštní pozornost je třeba dbát na to, aby byl odkaz nastaven na instanci po celou dobu životního cyklu služby. Správce spolehlivého stavu zpracovává tuto práci za vás a je optimalizován pro opakované návštěvy.

### <a name="transactional-and-asynchronous-operations"></a>Transakční a asynchronní operace

```csharp
using (ITransaction tx = this.StateManager.CreateTransaction())
{
    var result = await myDictionary.TryGetValueAsync(tx, "Counter-1");

    await myDictionary.AddOrUpdateAsync(tx, "Counter-1", 0, (k, v) => ++v);

    await tx.CommitAsync();
}
```

Spolehlivé kolekce mají mnoho stejné operace, které jejich `System.Collections.Generic` a `System.Collections.Concurrent` protějšky dělat, s výjimkou jazyka integrovaný dotaz (LINQ). Operace na spolehlivé kolekce jsou asynchronní. Důvodem je, že operace zápisu se spolehlivými kolekcemi provádějí vstupně-vanové operace k replikaci a zachování dat na disk.

Spolehlivé operace kolekce jsou *transakční*, takže můžete udržovat stav konzistentní ve více spolehlivé kolekce a operace. Například můžete dequeue pracovní položky ze spolehlivé fronty, provést operaci na něm a uložit výsledek ve spolehlivém slovníku, to vše v rámci jedné transakce. To je považováno za atomické operace a zaručuje, že celá operace bude úspěšná nebo celá operace se vrátí zpět. Pokud dojde k chybě po vyřazení položky z fronty, ale před uložením výsledku, celá transakce je vrácena zpět a položka zůstane ve frontě pro zpracování.

## <a name="run-the-application"></a>Spuštění aplikace
Nyní se vracíme do aplikace *HelloWorld.* Nyní můžete vytvářet a nasazovat služby. Po stisknutí **klávesy F5**bude aplikace vytvořena a nasazena do místního clusteru.

Po spuštění služby můžete zobrazit generované události trasování událostí pro Windows (ETW) v okně **diagnostické události.** Všimněte si, že zobrazené události jsou z bezstavové služby a stavové služby v aplikaci. Datový proud můžete pozastavit kliknutím na tlačítko **Pozastavit.** Potom můžete prozkoumat podrobnosti zprávy rozbalením této zprávy.

> [!NOTE]
> Před spuštěním aplikace se ujistěte, že máte spuštěný cluster místního vývoje. Informace o nastavení místního prostředí najdete v [příručce Začínáme.](service-fabric-get-started.md)
> 
> 

![Zobrazení diagnostických událostí v sadě Visual Studio](media/service-fabric-reliable-services-quick-start/hello-stateful-Output.png)

## <a name="next-steps"></a>Další kroky
[Ladění aplikace Service Fabric v sadě Visual Studio](service-fabric-debugging-your-application.md)

[Začínáme: Služby Service Fabric Web API se samoobslužným hostingem OWIN](service-fabric-reliable-services-communication-webapi.md)

[Další informace o spolehlivých kolekcích](service-fabric-reliable-services-reliable-collections.md)

[Nasazení aplikace](service-fabric-deploy-remove-applications.md)

[Upgrade aplikací](service-fabric-application-upgrade.md)

[Odkaz pro vývojáře pro spolehlivé služby](https://msdn.microsoft.com/library/azure/dn706529.aspx)

