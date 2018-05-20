---
title: Vytvoření první aplikace Service Fabric v jazyce C# | Microsoft Docs
description: Úvod do vytváření aplikace Microsoft Azure Service Fabric s bezzstavovými i stavovými službami.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''
ms.assetid: d9b44d75-e905-468e-b867-2190ce97379a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/16/2018
ms.author: vturecek
ms.openlocfilehash: 6977fa0a62767cebbd1000335c6c3a33a5991c2c
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="get-started-with-reliable-services"></a>Začínáme s Reliable Services
> [!div class="op_single_selector"]
> * [C# v systému Windows](service-fabric-reliable-services-quick-start.md)
> * [Java v Linuxu](service-fabric-reliable-services-quick-start-java.md)
> 
> 

Aplikace Azure Service Fabric obsahuje jednu nebo více služeb, které spustíte kód. Tento průvodce vám ukáže, jak vytvořit bezzstavovými i stavovými aplikací Service Fabric pomocí [spolehlivé služby](service-fabric-reliable-services-introduction.md).  Video tento Microsoft Virtual Academy také ukazuje postup vytvoření bezstavové spolehlivé služby: <center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=s39AO76yC_7206218965">  
<img src="./media/service-fabric-reliable-services-quick-start/ReliableServicesVid.png" WIDTH="360" HEIGHT="244">  
</a></center>

## <a name="basic-concepts"></a>Základní koncepty
Pokud chcete začít se službami Reliable Services, potřebujete jenom pochopit několik základní koncepty:

* **Typ služby**: Toto je implementace služby. Je definována v třídě napíšete, který rozšiřuje `StatelessService` a ostatní kódu nebo v něm, použít název a číslo verze závislosti.
* **S názvem instance služby**: ke spuštění služby, vytvoříte pojmenované instance typu služby mnohem jako vytvoření instance objektu typu třídy. Instance služby má název ve formě identifikátoru URI pomocí "fabric: /", jako například scheme "fabric: / MyApp/Moje_služba".
* **Hostitel služby**: pojmenované instance vytvoříte muset spustit v hostitelském procesu. Hostitel služby je právě proces, kde můžete spustit instance služby.
* **Registrace služby**: registrace soustřeďuje všechny informace dohromady. Typ služby musí být zaregistrován u modulu runtime Service Fabric v hostitele služby umožňující Service Fabric k vytvoření instance ho spustit.  

## <a name="create-a-stateless-service"></a>Vytvoření bezstavové služby
Bezstavové služby je typ služby, který je aktuálně norm v cloudových aplikacích. Považuje bezstavové, protože samotné služby neobsahuje data, která musí být uložené spolehlivě nebo vysoké dostupnosti. Pokud dojde instanci bezstavové služby, všechny jeho vnitřní stav bude ztracena. V tomto typu služby musí být stav ukládaný na externí úložiště, jako jsou tabulky Azure nebo databázi SQL, mohla provést vysoce dostupné a spolehlivé.

Spusťte Visual Studio 2015 nebo 2017 Visual Studio jako správce a vytvořit nový projekt aplikace Service Fabric s názvem *HelloWorld*:

![Pomocí dialogového okna Nový projekt pro vytvoření nové aplikace Service Fabric](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject.png)

Pak vytvořte projekt bezstavové služby pomocí **základní rozhraní .net 2.0** s názvem *HelloWorldStateless*:

![V dialogovém okně druhý vytvořte projekt bezstavové služby](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject2.png)

Řešení nyní obsahuje dva projekty:

* *Hello World*. Toto je *aplikace* projekt, který obsahuje vaše *služby*. Obsahuje taky manifest aplikace, která popisuje aplikace a také řadu skriptů prostředí PowerShell, které pomáhají při nasazení aplikace.
* *HelloWorldStateless*. Toto je projekt služby. Obsahuje implementace bezstavové služby.

## <a name="implement-the-service"></a>Tuto službu implementovat
Otevřete **HelloWorldStateless.cs** souboru v projektu služby. V Service Fabric můžete službu spustit veškeré obchodní logiky. Rozhraní API služby obsahuje dvě vstupní body pro váš kód:

* Metodu zprostředkovává vstupního bodu, názvem *RunAsync*, kde můžete začít provádění jakékoli úlohy, včetně dlouho běžící výpočetních úloh.

```csharp
protected override async Task RunAsync(CancellationToken cancellationToken)
{
    ...
}
```

* Bod položka komunikace, kde můžete zařadit do zásobníku komunikace podle vlastní volby, jako je ASP.NET Core. Toto je, kde můžete spustit přijímá žádosti od uživatelů a dalších služeb.

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    ...
}
```

V tomto kurzu se zaměříme na `RunAsync()` metody vstupní bod. Toto je, kde můžete okamžitě začít kód spuštěný.
Šablona projektu obsahuje ukázkové provádění `RunAsync()` který zvětší kumulativní počet.

> [!NOTE]
> Podrobnosti o tom, jak pracovat s komunikačního balíku najdete v tématu [Service Fabric webového rozhraní API služby s vlastním hostování OWIN](service-fabric-reliable-services-communication-webapi.md)
> 
> 

### <a name="runasync"></a>RunAsync
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

Platforma volá tuto metodu, když je umístěný a připravené ke spuštění instance služby. Bezstavové služby, která jednoduše znamená, když je otevřen v instanci služby. Token zrušení je k dispozici pro koordinaci při instanci služby musí být uzavřen. V Service Fabric tento cyklus otevření nebo uzavření instance služby může docházet k tolikrát, kolikrát za dobu existence služby jako celek. Toto může nastat z různých důvodů, včetně:

* Systém přesune vaší instance služby pro vyrovnávání prostředků.
* K chybám dochází v kódu.
* Aplikace nebo systému byla upgradována.
* Základní hardware dojde k výpadku.

Tato orchestration je spravován systémem vysoce dostupné a správně vyrovnáváním zachovat služby.

`RunAsync()` by neměly blokovat synchronně. Implementaci RunAsync by měla vrátit úlohu nebo await na jakékoli operace dlouhotrvající nebo blokování umožňující pokračovat modulu runtime. Poznámka: v `while(true)` smyčky v předchozím příkladu, úloha vrácení `await Task.Delay()` se používá. Pokud vaše úlohy musí blokovat synchronně, měli byste naplánovat novou úlohu s `Task.Run()` ve vaší `RunAsync` implementace.

Zrušení úlohy je spolupráci úsilí řízená token poskytnutý zrušení. Systém bude počkejte na ukončení (podle úspěšné dokončení, zrušení nebo selhání), než ji přesune vaše úlohy. Je důležité respektovat token zrušení, Dokončit veškerou práci a ukončete `RunAsync()` provést co nejrychleji, pokud systém požadavky zrušení.

V tomto příkladu bezstavové služby je počet uložené v místní proměnné. Ale protože je bezstavové služby, hodnotu, která je uložená existuje pouze pro aktuální životní cyklus jeho instanci služby. Pokud službu přesune nebo restartuje, hodnota je ztraceny.

## <a name="create-a-stateful-service"></a>Vytvoření stavové služby
Service Fabric zavádí nový typ služby, která je stavový. Stavové služby můžete udržovat stav spolehlivě v rámci služby samostatně, umístěn společně s kód, který se používá. Stav je vysoké dostupnosti pomocí Service Fabric bez nutnosti zachování stavu na externím obchodu.

Chcete-li převést hodnotu čítače bezstavové na vysoce dostupné a trvalé, i když služba přesune nebo restartování, je třeba stavové služby.

Ve stejném *HelloWorld* aplikace, můžete přidat nové služby tak, že kliknete pravým tlačítkem na službách odkazů v projektu aplikace a výběr **Přidat -> Nový Service Fabric Service**.

![Přidání služby do aplikace Service Fabric](media/service-fabric-reliable-services-quick-start/hello-stateful-NewService.png)

Vyberte **rozhraní .net Core 2.0 -> Služba Stateful** a pojmenujte ji *HelloWorldStateful*. Klikněte na **OK**.

![Pomocí dialogového okna Nový projekt pro vytvoření nové stavové služby Service Fabric](media/service-fabric-reliable-services-quick-start/hello-stateful-NewProject.png)

Aplikace musí mít teď dvě služby: bezstavové služby *HelloWorldStateless* a stavové služby *HelloWorldStateful*.

Stavová služba má stejné vstupní body jako bezstavové služby. Hlavní rozdíl je dostupnost *zprostředkovatele stavu* který spolehlivě uložení stavu. Service Fabric se dodává s implementace zprostředkovatele stav názvem [spolehlivé kolekce](service-fabric-reliable-services-reliable-collections.md), což umožňuje vytváření replikované datové struktury prostřednictvím spolehlivé správce stavu. Stavové spolehlivé služby pomocí zprostředkovatele stavu ve výchozím nastavení.

Otevřete **HelloWorldStateful.cs** v *HelloWorldStateful*, který obsahuje následující metodě RunAsync:

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

### <a name="runasync"></a>RunAsync
`RunAsync()` funguje podobně jako v stavová a Bezstavová služby. Ale v stavové služby platformy provede další práci vaším jménem předtím, než se provede `RunAsync()`. Tento pracovní mohou zahrnovat kontrolu, spolehlivé správce stavu a spolehlivé kolekce jsou připravené k použití.

### <a name="reliable-collections-and-the-reliable-state-manager"></a>Spolehlivé kolekce a spolehlivé správce stavu
```csharp
var myDictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");
```

[IReliableDictionary](https://msdn.microsoft.com/library/dn971511.aspx) je implementace slovník, který můžete použít k spolehlivě uložení stavu ve službě. Service Fabric a spolehlivé kolekcí mohou ukládat data přímo ve službě bez nutnosti externí trvalého úložiště. Spolehlivé kolekce dají vašim datům vysoce dostupný. Service Fabric dosahuje tak, že vytváření a správu více *repliky* služby za vás. Také poskytuje rozhraní API, které abstrahuje rychle složitosti správy tyto repliky a jejich přechodů mezi stavy.

Spolehlivé kolekce můžete ukládat jakýkoli typ rozhraní .NET, včetně vlastních typů, pomocí několika upozornění:

* Service Fabric umožňuje vašemu stavu vysoce dostupné podle *replikace* stavu napříč uzly a spolehlivé kolekce ukládat data na místní disk na jednotlivé repliky. To znamená, že vše, co je uložen v spolehlivé kolekce musí být *serializovatelný*. Ve výchozím nastavení, použijte spolehlivé kolekce [kontraktu](https://msdn.microsoft.com/library/system.runtime.serialization.datacontractattribute%28v=vs.110%29.aspx) pro serializaci, proto je důležité se ujistit, že jsou vaše typy [nepodporuje serializátor kontraktu dat](https://msdn.microsoft.com/library/ms731923%28v=vs.110%29.aspx) při použití výchozí serializátor.
* Objekty jsou replikovány pro zajištění vysoké dostupnosti při potvrzení transakce na spolehlivé kolekce. Objekty uložené v kolekcích spolehlivé udržovaly v místní paměti ve službě. To znamená, že máte místní odkaz na objekt.
  
   Je důležité, neprovádějte místní instancí těchto objektů bez provádění operace aktualizace na kolekci spolehlivé v transakci. To je proto nebude automaticky replikovat změny místní instance objektů. Musíte znovu vložit objekt zpět do slovníku nebo použijte jednu z *aktualizace* metody ve slovníku.

Spolehlivé správce stavu spravuje spolehlivé kolekce za vás. Můžete jednoduše pokládat spolehlivé správce stavu pro kolekci spolehlivé podle názvu kdykoli a kdekoli v službě. Spolehlivé správce stavu zajišťuje, získejte odkaz na zpět. Doporučujeme si uložit odkazy na spolehlivé kolekci instancí v člen třídy, proměnné nebo vlastnosti. Musí dát zvláštní pozor zajistit, že je odkaz nastavený na instanci za všech okolností v průběhu životního cyklu služby. Spolehlivé správce stavu zpracuje tato práce pro uživatele a je optimalizovaný pro opakování návštěvách.

### <a name="transactional-and-asynchronous-operations"></a>Transakční a asynchronní operace
```csharp
using (ITransaction tx = this.StateManager.CreateTransaction())
{
    var result = await myDictionary.TryGetValueAsync(tx, "Counter-1");

    await myDictionary.AddOrUpdateAsync(tx, "Counter-1", 0, (k, v) => ++v);

    await tx.CommitAsync();
}
```

Spolehlivé kolekce mají mnoho z operací, která jejich `System.Collections.Generic` a `System.Collections.Concurrent` svými protějšky, s výjimkou LINQ. Operace na spolehlivé kolekce jsou asynchronní. Je to proto, že operace zápisu ke kolekcím, spolehlivé provádění vstupně-výstupních operací se budou replikovat a zachovat data na disk.

Spolehlivé operace kolekce jsou *transakcí*, takže můžete zachovat stav konzistentní napříč více spolehlivé kolekce a operace. Může například dequeue – pracovní položky z fronty spolehlivé, proveďte operaci na něm a výsledek uložit jako slovník spolehlivé, vše v rámci jedné transakce. To je považován za atomická operace, a zaručuje, že buď celé operace proběhne úspěšně, nebo celou operaci vrátíte zpět. Pokud dojde k chybě po dequeue položku, ale před uložením výsledek, celá transakce bude vrácena zpět a položka zůstane ve frontě pro zpracování.

## <a name="run-the-application"></a>Spuštění aplikace
Nemůžeme se teď vrátit do *HelloWorld* aplikace. Teď můžete sestavit a nasazení služeb. Po stisknutí klávesy **F5**, vaše aplikace bude vytvořené a nasazené na místním clusteru.

Po službu spustit, můžete zobrazit vygenerované události trasování událostí pro Windows (ETW) v **diagnostických událostí** okno. Všimněte si, že zobrazených událostí z službu bezstavové a stavové služby v aplikaci. Datový proud je možné pozastavit kliknutím **pozastavit** tlačítko. Poté můžete prozkoumat podrobnosti zprávy rozbalením této zprávě.

> [!NOTE]
> Než spustíte aplikaci, ujistěte se, že máte místního vývojového clusteru se systémem. Podívejte se [Příručka Začínáme](service-fabric-get-started.md) informace o nastavení vašeho místního prostředí.
> 
> 

![Zobrazení diagnostických událostí v sadě Visual Studio](media/service-fabric-reliable-services-quick-start/hello-stateful-Output.png)

## <a name="next-steps"></a>Další postup
[Ladění aplikace Service Fabric v sadě Visual Studio](service-fabric-debugging-your-application.md)

[Začínáme: Service Fabric webového rozhraní API služby s vlastním hostování OWIN](service-fabric-reliable-services-communication-webapi.md)

[Další informace o spolehlivé kolekce](service-fabric-reliable-services-reliable-collections.md)

[Nasazení aplikace](service-fabric-deploy-remove-applications.md)

[Upgrade aplikací](service-fabric-application-upgrade.md)

[Referenční informace pro vývojáře pro spolehlivé služby](https://msdn.microsoft.com/library/azure/dn706529.aspx)

