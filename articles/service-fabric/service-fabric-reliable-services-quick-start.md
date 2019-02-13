---
title: Vytvoření první aplikace Service Fabric v C# | Dokumentace Microsoftu
description: Úvod do vytváření aplikace Microsoft Azure Service Fabric s bezstavové a stavové služby.
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
ms.openlocfilehash: eb0fd7e4feb28d60173b638a15dbce598f78e6bf
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56182948"
---
# <a name="get-started-with-reliable-services"></a>Začínáme s Reliable Services
> [!div class="op_single_selector"]
> * [C# v systému Windows](service-fabric-reliable-services-quick-start.md)
> * [Java v Linuxu](service-fabric-reliable-services-quick-start-java.md)
> 
> 

Aplikace Azure Service Fabric obsahuje jednu nebo víc služeb, na kterých běží váš kód. Tento průvodce vám ukáže, jak vytvořit bezstavových a stavových aplikací Service Fabric s [Reliable Services](service-fabric-reliable-services-introduction.md).  

## <a name="basic-concepts"></a>Základní koncepty
Chcete-li začít s Reliable Services, stačí pochopit pár základních konceptů:

* **Typ služby**: Toto je vaše implementace služby. Je definován třídou napíšete, která rozšiřuje `StatelessService` a jakékoli další kód nebo závislosti v něm používat společně s název a číslo verze.
* **Instance služby s názvem**: Ke spuštění služby, můžete vytvořit pojmenované instance typu služby podobně jako vytvoříte instance objektů typu třídy. Instance služby má název ve formě identifikátoru URI pomocí "fabric: /" schéma, jako například "fabric: / MyApp/Moje_služba".
* **Hostitel služby**: Instance s názvem služby, které vytvoříte, třeba ke spouštění uvnitř hostitelského procesu. Hostitel služby je právě proces, ve kterém můžete spustit instance vaší služby.
* **Registrace služby**: Registrace spojuje všechno. Typ služby musí být zaregistrovaná s modulem runtime Service Fabric v hostiteli služby umožňuje Service Fabric pro vytvoření instancí jeho spuštění.  

## <a name="create-a-stateless-service"></a>Vytvoření bezstavové služby
Bezstavová služba je typ služby, která je aktuálně norm v cloudových aplikacích. Považuje bezstavové, protože samotné služby neobsahuje data, která se ukládají spolehlivě nebo nastavit na vysokou dostupnost. Vypnutí instance bezstavovou službu všechny jeho vnitřní stavů, dojde ke ztrátě. U tohoto typu služby musí být stav ukládaný na externím úložišti, jako jsou tabulky Azure nebo databázi SQL pro něj má být provedeno vysoce dostupné a spolehlivé.

Spusťte Visual Studio 2015 nebo Visual Studio 2017 jako správce a vytvořte nový projekt aplikace Service Fabric s názvem *HelloWorld*:

![Pomocí dialogového okna Nový projekt pro vytvoření nové aplikace Service Fabric](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject.png)

Potom vytvořte projekt bezstavové služby pomocí **.Net Core 2.0** s názvem *HelloWorldStateless*:

![V dialogovém okně druhý vytvoření bezstavové služby v projektu](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject2.png)

Vaše řešení teď obsahuje dva projekty:

* *HelloWorld*. Toto je *aplikace* projekt, který obsahuje vaše *služby*. Také obsahuje manifest aplikace, která popisuje aplikace, jakož i několik skriptů prostředí PowerShell, které vám umožní nasadit vaši aplikaci.
* *HelloWorldStateless*. Jedná se o projekt služby. Obsahuje implementace bezstavové služby.

## <a name="implement-the-service"></a>Implementaci této služby
Otevřít **HelloWorldStateless.cs** soubor v projektu služby. V Service Fabric můžete službu spustit veškeré obchodní logiky. Rozhraní API služby poskytuje dva vstupní body pro váš kód:

* Metodu neuzavřenou vstupního bodu, volá *RunAsync*, kde můžete zahájit provádění všech úloh, včetně dlouho běžící výpočetní úlohy.

```csharp
protected override async Task RunAsync(CancellationToken cancellationToken)
{
    ...
}
```

* Komunikaci vstupního bodu ve kterém můžete zařadit do zásobníku komunikace podle vlastní volby, například ASP.NET Core. Je to, kde můžete začít přijímat žádosti od uživatelů a dalších služeb.

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    ...
}
```

V tomto kurzu se zaměříme na `RunAsync()` metodu vstupního bodu. To je, kde můžete okamžitě začít spouštět kód.
Šablona projektu zahrnuje ukázková implementace `RunAsync()` , která zvýší kumulativní počet.

> [!NOTE]
> Podrobnosti o tom, jak pracovat s komunikačního balíku najdete v tématu [služeb Service Fabric webových rozhraní API s vlastním hostováním OWIN](service-fabric-reliable-services-communication-webapi.md)
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

Platforma volá tuto metodu, když je umístěný a připravené ke spuštění instance služby. Pro bezstavovou službu, která jednoduše znamená, že při otevření instance služby. Token zrušení slouží ke koordinaci při vaší instance služby je potřeba ho zavřít. V Service Fabric tento cyklus otevřít nebo Zavřít instance služby situace může nastat v mnoha případech během životního cyklu služby jako celek. To může dojít z různých důvodů, včetně:

* Systém přesune vaší instance služby pro vyrovnávání prostředků.
* K chybám dochází ve vašem kódu.
* Aplikace nebo systému se upgraduje.
* Základní hardware dojde k výpadku.

Tuto orchestraci spravuje v systému zachovat vaše služba s vysokou dostupností a správně vyvážené.

`RunAsync()` by neměla blokovat synchronně. Implementace RunAsync by měly vrátit a Task nebo čekat na jakékoli dlouho běžící nebo blokující operace umožňuje modulu runtime, abyste mohli pokračovat. Všimněte si `while(true)` smyčky v předchozím příkladu vracející úlohy `await Task.Delay()` se používá. Pokud vaše úloha musí blokovat synchronně, měli byste naplánovat novou úlohu s `Task.Run()` ve vašich `RunAsync` implementace.

Zrušení úlohy je kooperativní úsilí orchestrované systémem poskytnutého rušícího tokenu. Systém bude čekat úlohy na konec (podle úspěšné dokončení, zrušení nebo selhání) předtím, než se přesune. Je potřeba vyhovět token zrušení, Dokončit veškerou práci a ukončit `RunAsync()` nejkratší možné době, když systém požaduje zrušení.

V tomto příkladu bezstavovou službu počet uloženy v místní proměnné. Ale vzhledem k tomu, že toto je Bezstavová služba, existuje hodnota, která je uložena pouze pro aktuální životní cyklus její instance služby. Když služba přesune nebo se restartuje, hodnota je ztraceny.

## <a name="create-a-stateful-service"></a>Vytvoření stavové služby
Service Fabric představuje nový typ služby, která je stavový. Stavové služby můžete udržovat stav spolehlivě v rámci samotné služby společně umístěné s kódem, který ji používá. Stav je provedené s vysokou dostupností pomocí Service Fabric bez nutnosti k uchování stavu na externím úložišti.

Chcete-li převést hodnotu čítače bezstavové na vysoce dostupné a trvalé, i když službu přesune nebo se restartuje, musíte do stavové služby.

Ve stejném *HelloWorld* aplikace, můžete přidat nové služby kliknutím pravým tlačítkem na službách odkazů v projektu aplikace a výběr **Přidat -> Nová služba Service Fabric**.

![Přidat službu do aplikace Service Fabric](media/service-fabric-reliable-services-quick-start/hello-stateful-NewService.png)

Vyberte **.Net Core 2.0 -> Stavová služba** a pojmenujte ho *HelloWorldStateful*. Klikněte na **OK**.

![Pomocí dialogového okna nového projektu k vytvoření nové stavové služby Service Fabric](media/service-fabric-reliable-services-quick-start/hello-stateful-NewProject.png)

Vaše aplikace by měla mít teď dvě služby: bezstavovou službu *HelloWorldStateless* a stavové služby *HelloWorldStateful*.

Stavová služba má stejný vstupní body jako bezstavové služby. Hlavní rozdíl je dostupnost *zprostředkovatele stavu* , který spolehlivě ukládat stavu. Service Fabric se dodává s implementací zprostředkovatele stavu volá [Reliable Collections](service-fabric-reliable-services-reliable-collections.md), který umožňuje vytvářet replikované datové struktury prostřednictvím Reliable State Manager. Ve výchozím nastavení spolehlivou stavovou službu používá tento stav poskytovatele.

Otevřít **HelloWorldStateful.cs** v *HelloWorldStateful*, který obsahuje následující metodě RunAsync:

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
`RunAsync()` pracuje podobně jako v stavové a bezstavové služby. Ale stavové služby platformy další práci za vás provede předtím, než se provede `RunAsync()`. Tato práce může zahrnovat zajistit, aby Reliable State Manager a Reliable Collections jsou připravené k použití.

### <a name="reliable-collections-and-the-reliable-state-manager"></a>Spolehlivé kolekce a Reliable State Manager
```csharp
var myDictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");
```

[IReliableDictionary](https://msdn.microsoft.com/library/dn971511.aspx) je implementaci slovníku, který můžete použít ke spolehlivému uložení stavu ve službě. S platformou Service Fabric a Reliable Collections můžete ukládat data přímo do vaší služby bez nutnosti externí trvalého úložiště. Reliable Collections vytvořit data s vysokou dostupností. Service Fabric dosahuje tak, že vytváření a správě více *repliky* služby za vás. Také poskytuje rozhraní API, který vyčleňuje složitých úkolů při správě tyto repliky a jejich přechodů mezi stavy.

Reliable Collections můžete ukládat libovolný typ rozhraní .NET, včetně vašich vlastních typů pomocí několika upozornění:

* Service Fabric zajišťuje svůj stav s vysokou dostupností pomocí *replikaci* stavu mezi uzly a Reliable Collections ukládat data na místní disk na jednotlivé repliky. To znamená, že musí být vše, co je uložen v Reliable Collections *serializovatelný*. Ve výchozím nastavení, použijte Reliable Collections [kontraktu dat DataContract](https://msdn.microsoft.com/library/system.runtime.serialization.datacontractattribute%28v=vs.110%29.aspx) pro serializaci, takže je důležité se ujistit, že jsou vaše typy [podporované serializátorem kontraktu dat](https://msdn.microsoft.com/library/ms731923%28v=vs.110%29.aspx) při použití výchozí serializátor.
* Při potvrzení transakce v Reliable Collections objekty replikují pro zajištění vysoké dostupnosti. Objekty uložené v Reliable Collections jsou uloženy v místní paměti ve své službě. To znamená, že máte místní odkaz na objekt.
  
   Je důležité, neprovádějte místní instancí těchto objektů bez provedení operace aktualizace na spolehlivé kolekce v transakci. Je to proto, že změny v místní instance objektů, se replikovat nebudou automaticky. Musíte znovu objekt vložit zpět do slovníku nebo použijte jednu z *aktualizovat* metody ve slovníku.

Reliable Collections Reliable State Manager spravuje za vás. Můžete jednoduše požádat o Reliable State Manager spolehlivé kolekci podle názvu kdykoli a kdekoli ve své službě. Reliable State Manager zajišťuje, získejte odkaz zpět. Není vhodné uložit odkazy na instance spolehlivé kolekce v členu třídy proměnné nebo vlastnosti. Zvláštní pozornost musí provést k zajištění, že je odkaz nastavený na instanci za všech okolností v životního cyklu služeb. Reliable State Manager zpracovává tuto práci za vás a je optimalizovaný pro opakování návštěvy.

### <a name="transactional-and-asynchronous-operations"></a>Transakční a asynchronní operace
```csharp
using (ITransaction tx = this.StateManager.CreateTransaction())
{
    var result = await myDictionary.TryGetValueAsync(tx, "Counter-1");

    await myDictionary.AddOrUpdateAsync(tx, "Counter-1", 0, (k, v) => ++v);

    await tx.CommitAsync();
}
```

Reliable Collections máte spoustu stejných operací, které jejich `System.Collections.Generic` a `System.Collections.Concurrent` protějšky, s výjimkou LINQ. Operace s Reliable Collections jsou asynchronní. Je to proto, že operace zápisu s Reliable Collections provádět vstupně-výstupních operací pro replikaci a uložení dat na disk.

Operace spolehlivé kolekce jsou *transakční*, takže můžete zachovat stav konzistentní napříč několika Reliable Collections a operace. Může například odstranění z fronty pracovní položku z fronty spolehlivé, provádění operací na něj a uložit výsledek ve spolehlivém slovníku, vše v rámci jedné transakce. Je zpracovaná jako atomickou operaci a zaručuje, že buď celá operace proběhne úspěšně, nebo bude celá operace vrátit zpět. Pokud dojde k chybě po odstranění z fronty položky, ale před uložením výsledku, celá transakce bude vrácena zpět a položka zůstane ve frontě pro zpracování.

## <a name="run-the-application"></a>Spuštění aplikace
Můžeme nyní se vraťte *HelloWorld* aplikace. Teď můžete vytvářet a nasazovat vaše služby. Když stisknete klávesu **F5**, vaše aplikace bude vytvořené a nasazené do místního clusteru.

Poté, co služba spuštěna, můžete zobrazit vygenerované události trasování událostí pro Windows (ETW) v **diagnostické události** okna. Všimněte si, že zobrazené události z službu bezstavové a stavové služby v aplikaci. Datový proud je možné pozastavit kliknutím **pozastavit** tlačítko. Poté můžete prozkoumat podrobnosti zprávy rozbalením této zprávy.

> [!NOTE]
> Před spuštěním aplikace, ujistěte se, že máte místního vývojového clusteru se systémem. Podívejte se [– Příručka Začínáme](service-fabric-get-started.md) informace o nastavení vašeho místního prostředí.
> 
> 

![Zobrazení diagnostických událostí v sadě Visual Studio](media/service-fabric-reliable-services-quick-start/hello-stateful-Output.png)

## <a name="next-steps"></a>Další postup
[Ladění aplikace Service Fabric v sadě Visual Studio](service-fabric-debugging-your-application.md)

[Začínáme: Služby Service Fabric webového rozhraní API s vlastním hostováním OWIN](service-fabric-reliable-services-communication-webapi.md)

[Další informace o Reliable Collections](service-fabric-reliable-services-reliable-collections.md)

[Nasazení aplikace](service-fabric-deploy-remove-applications.md)

[Upgrade aplikací](service-fabric-application-upgrade.md)

[Referenční informace pro vývojáře pro službu Reliable Services](https://msdn.microsoft.com/library/azure/dn706529.aspx)

