---
title: 'Vytvoření první aplikace Service Fabric v jazyce C #'
description: Úvod k vytvoření aplikace Microsoft Azure Service Fabric se stavovou a stavovou službou.
ms.topic: conceptual
ms.date: 07/10/2019
ms.custom: sfrev, devx-track-csharp
ms.openlocfilehash: 45341c98a40cbcabfa8b96f2016f02f1755fe2b3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791523"
---
# <a name="get-started-with-reliable-services"></a>Začínáme s Reliable Services

> [!div class="op_single_selector"]
> * [C# v systému Windows](service-fabric-reliable-services-quick-start.md)
> * [Java v Linuxu](service-fabric-reliable-services-quick-start-java.md)

Aplikace Azure Service Fabric obsahuje jednu nebo více služeb, které spouštějí váš kód. V této příručce se dozvíte, jak vytvořit bezstavové a stavové Service Fabric aplikace pomocí [Reliable Services](service-fabric-reliable-services-introduction.md).  

## <a name="basic-concepts"></a>Základní koncepty

Abyste mohli začít s Reliable Services, stačí pochopit jenom několik základních konceptů:

* **Typ služby**: Toto je vaše implementace služby. Je definována třídou, kterou napíšete, která rozšiřuje `StatelessService` a jakýkoli jiný kód nebo závislosti, společně s názvem a číslem verze.
* **Instance pojmenované služby**: Pokud chcete službu spustit, vytvoříte pojmenované instance typu služby, podobně jako při vytváření instancí objektů typu třídy. Instance služby má název ve formě identifikátoru URI s použitím "Fabric:/". schéma, jako je například Fabric:/MyApp/Mojesluzba.
* **Hostitel služby**: pojmenované instance služby, které vytvoříte, musí běžet v hostitelském procesu. Hostitel služby je jenom proces, ve kterém se můžou spouštět instance služby.
* **Registrace služby**: registrace přináší všechno dohromady. Typ služby musí být zaregistrován s modulem runtime Service Fabric v hostiteli služby, aby mohl Service Fabric vytvářet instance pro spuštění.  

## <a name="create-a-stateless-service"></a>Vytvoření bezstavové služby

Bezstavová služba je typ služby, která je aktuálně normou v cloudových aplikacích. Je považována za bezstavovou, protože samotná služba neobsahuje data, která je třeba spolehlivě ukládat nebo mít vysokou dostupnost. Pokud dojde k výpadku instance nestavové služby, dojde ke ztrátě všech vnitřních stavů. V tomto typu služby musí být stav uložený v externím úložišti, jako jsou například tabulky Azure nebo SQL Database, aby byl vysoce dostupný a spolehlivý.

Spusťte Visual Studio 2017 nebo Visual Studio 2019 jako správce a vytvořte nový projekt Service Fabric aplikace s názvem *HelloWorld*:

![Pomocí dialogového okna Nový projekt můžete vytvořit novou Service Fabric aplikaci.](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject.png)

Pak vytvořte projekt nestavové služby pomocí **.NET Core 2,0** s názvem *HelloWorldStateless*:

![V druhém dialogovém okně vytvořte projekt služby bez stavu.](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject2.png)

Vaše řešení teď obsahuje dva projekty:

* *HelloWorld*. Toto je projekt *aplikace* , který obsahuje vaše *služby*. Obsahuje také manifest aplikace, který popisuje aplikaci, a také několik skriptů prostředí PowerShell, které vám pomůžou nasadit aplikaci.
* *HelloWorldStateless*. Toto je projekt služby. Obsahuje nestavovou implementaci služby.

## <a name="implement-the-service"></a>Implementace služby

V projektu služby otevřete soubor **HelloWorldStateless. cs** . V Service Fabric může služba spustit libovolnou obchodní logiku. Rozhraní API služby poskytuje dva vstupní body pro váš kód:

* Otevřená metoda vstupního bodu s názvem *RunAsync*, kde můžete začít spouštět jakékoli úlohy, včetně dlouhotrvajících výpočetních úloh.

```csharp
protected override async Task RunAsync(CancellationToken cancellationToken)
{
    ...
}
```

* Vstupní bod komunikace, ve kterém můžete připojit svůj komunikační zásobník dle výběru, například ASP.NET Core. Tady můžete začít přijímat žádosti od uživatelů a dalších služeb.

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    ...
}
```

V tomto kurzu se zaměříme na `RunAsync()` metodu vstupního bodu. Tady můžete hned začít s kódem.
Šablona projektu obsahuje ukázkovou implementaci `RunAsync()` , která zvýší počet kumulovaných hodnot.

> [!NOTE]
> Podrobnosti o tom, jak pracovat s komunikačním zásobníkem, najdete v tématu [komunikace služby s ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md) .

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

Platforma volá tuto metodu, když je umístěna instance služby a je připravena k provedení. Pro bezstavovou službu, která jednoduše znamená, že je otevřená instance služby. Token zrušení se poskytuje ke koordinaci, když je potřeba uzavřít instanci služby. V Service Fabric může tento cyklus otevření nebo ukončení instance služby probíhat mnohokrát po celou dobu životnosti služby. K tomu může dojít z různých důvodů, včetně:

* Systém přesune vaše instance služby pro vyrovnávání prostředků.
* Ve vašem kódu dojde k chybám.
* Aplikace nebo systém se upgraduje.
* Dojde k výpadku základního hardwaru.

Tato orchestrace je spravovaná systémem, aby byla vaše služba vysoce dostupná a správně vyvážená.

`RunAsync()` nemělo by se blokovat synchronně. Vaše implementace RunAsync by měla vrátit úlohu nebo očekávat jakékoli dlouhotrvající nebo blokující operace, aby bylo možné pokračovat v běhu. Poznámka ve `while(true)` smyčce v předchozím příkladu se používá vrácení úlohy `await Task.Delay()` . Pokud vaše úloha musí blokovat synchronně, měli byste naplánovat novou úlohu `Task.Run()` v rámci vaší `RunAsync` implementace.

Zrušení úloh je úsilí v družstvu, které provádí poskytnutý token zrušení. Systém bude čekat na ukončení úlohy (po úspěšném dokončení, zrušení nebo chybě), než se přesune. Je důležité přijmout token zrušení, dokončit práci a skončit `RunAsync()` co nejrychleji, když systém požaduje zrušení.

V tomto příkladu služby bez stavu je počet uložený v místní proměnné. Vzhledem k tomu, že se jedná o bezstavovou službu, hodnota, která je uložena, je určena pouze pro aktuální životní cyklus své instance služby. Při přesunu nebo restartu služby dojde ke ztrátě hodnoty.

## <a name="create-a-stateful-service"></a>Vytvoření stavové služby

Service Fabric zavádí nový druh služby, která je stavová. Stavová služba může udržovat stav spolehlivě v rámci samotné služby, společně umístěná pomocí kódu, který je používá. Stav je vysoce dostupný pomocí Service Fabric bez nutnosti zachovat stav do externího úložiště.

Chcete-li převést hodnotu čítače ze stavu bez stavů na vysokou dostupnost a trvalé, i když se služba přesune nebo restartuje, budete potřebovat stavovou službu.

Ve stejné aplikaci *HelloWorld* můžete přidat novou službu tak, že kliknete pravým tlačítkem na odkazy na služby v projektu aplikace a vyberete **Add-> New Service Fabric Service**.

![Přidání služby do aplikace Service Fabric](media/service-fabric-reliable-services-quick-start/hello-stateful-NewService.png)

Vyberte **.NET Core 2,0-> stavová služba** a pojmenujte ji *HelloWorldStateful*. Klikněte na **OK**.

![Pomocí dialogového okna Nový projekt můžete vytvořit novou stavovou službu Service Fabric.](media/service-fabric-reliable-services-quick-start/hello-stateful-NewProject.png)

Vaše aplikace by teď měla mít dvě služby: bezstavovou službu *HelloWorldStateless* a stavovou službu *HelloWorldStateful*.

Stavová služba má stejné vstupní body jako Bezstavová služba. Hlavním rozdílem je dostupnost *zprostředkovatele stavu* , který může spolehlivě ukládat stav. Service Fabric obsahuje implementaci poskytovatele stavu nazvanou [Reliable Collections](service-fabric-reliable-services-reliable-collections.md), která umožňuje vytvářet replikované datové struktury prostřednictvím Správce spolehlivého stavu. Stavová služba Reliable využívá tohoto poskytovatele stavu ve výchozím nastavení.

Otevřete **HelloWorldStateful. cs** v *HelloWorldStateful*, který obsahuje následující RunAsync metodu:

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

`RunAsync()` funguje podobně jako stavová a Bezstavová služba. Ve stavové službě ale platforma před spuštěním provede další práci vaším jménem `RunAsync()` . Tato práce může zahrnovat jistotu, že je správce spolehlivých stavů a spolehlivé kolekce připravený k použití.

### <a name="reliable-collections-and-the-reliable-state-manager"></a>Spolehlivé kolekce a správce spolehlivého stavu

```csharp
var myDictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");
```

[IReliableDictionary](/dotnet/api/microsoft.servicefabric.data.collections.ireliabledictionary-2#microsoft_servicefabric_data_collections_ireliabledictionary_2) je slovníková implementace, kterou můžete použít k spolehlivému ukládání stavu ve službě. Pomocí Service Fabric a spolehlivých kolekcí můžete přímo ukládat data do vaší služby, aniž by bylo nutné externí trvalé úložiště. Spolehlivé kolekce zajistí vysokou dostupnost vašich dat. Service Fabric toho dosahuje vytvořením a správou více *replik* vaší služby za vás. Poskytuje také rozhraní API, které abstrakce zjednodušuje správu těchto replik a jejich přechodů na stav.

Spolehlivé kolekce můžou ukládat jakýkoli typ .NET, včetně vašich vlastních typů, s několika upozorněními:

* Service Fabric zajistí, aby byl stav vysoce dostupný při *replikaci* do všech uzlů, a spolehlivé kolekce ukládají vaše data na místní disk v každé replice. To znamená, že všechno, co je uloženo ve spolehlivých kolekcích, musí být *serializovatelný*. Ve výchozím nastavení používají spolehlivé kolekce [kontrakt DataContract](/dotnet/api/system.runtime.serialization.datacontractattribute) pro serializaci, takže je důležité zajistit, aby byly vaše typy [podporovány serializátorem kontraktu dat](/dotnet/framework/wcf/feature-details/types-supported-by-the-data-contract-serializer) při použití výchozího serializátoru.
* Objekty jsou při potvrzení transakcí u spolehlivých kolekcí replikovány pro zajištění vysoké dostupnosti. Objekty uložené ve spolehlivých kolekcích jsou v rámci služby uchovávány v místní paměti. To znamená, že máte místní odkaz na objekt.
  
   Je důležité, abyste nemuseli provádět místní instance těchto objektů bez provedení operace aktualizace pro spolehlivou kolekci v transakci. Důvodem je to, že změny místních instancí objektů nebudou replikovány automaticky. Objekt je nutné znovu vložit zpět do slovníku nebo použít jednu z metod *aktualizace* ve slovníku.

Správce Reliable State spravuje spolehlivé kolekce za vás. Správce spolehlivého stavu můžete jednoduše požádat o spolehlivé shromažďování dat podle názvu kdykoli a na jakémkoli místě ve vaší službě. Správce Reliable State zajišťuje, že získáte odkaz zpátky. Nedoporučujeme ukládat odkazy na spolehlivé instance kolekcí v proměnných členů třídy nebo vlastnostech. Aby se zajistilo, že odkaz bude v životním cyklu služby neustále nastavený na instanci, musí být podniknuta zvláštní péče. Reliable State Manager zpracovává tuto práci za vás a je optimalizovaná pro opakované návštěvy.

### <a name="transactional-and-asynchronous-operations"></a>Transakční a asynchronní operace

```csharp
using (ITransaction tx = this.StateManager.CreateTransaction())
{
    var result = await myDictionary.TryGetValueAsync(tx, "Counter-1");

    await myDictionary.AddOrUpdateAsync(tx, "Counter-1", 0, (k, v) => ++v);

    await tx.CommitAsync();
}
```

Spolehlivé kolekce obsahují mnohé ze stejných operací, které `System.Collections.Generic` dělají jejich `System.Collections.Concurrent` protějšky, s výjimkou jazyka LINQ (Language Integrated Query). Operace na spolehlivých kolekcích jsou asynchronní. Důvodem je to, že operace zápisu s spolehlivými kolekcemi provádějí vstupně-výstupní operace pro replikaci a uchovávání dat na disk.

Spolehlivé operace shromažďování dat jsou *transakční*, takže můžete udržovat stav konzistentní napříč několika spolehlivými kolekcemi a operacemi. Můžete například vyřadit pracovní položku ze spolehlivé fronty, provést na ní operaci a výsledek uložit ve spolehlivém slovníku, který je v rámci jedné transakce. Tato možnost se považuje za atomickou operaci a zaručuje, že celá operace bude úspěšná nebo se vrátí celá operace. Pokud dojde k chybě po vyřazení položky z fronty, ale před uložením výsledku, je celá transakce vrácena zpět a položka zůstane ve frontě ke zpracování.

## <a name="run-the-application"></a>Spuštění aplikace
Nyní se vrátíme do aplikace *HelloWorld* . Nyní můžete vytvářet a nasazovat vaše služby. Po stisknutí klávesy **F5** bude vaše aplikace sestavena a nasazena do místního clusteru.

Po spuštění služeb můžete v okně **diagnostické události** zobrazit generované události trasování událostí pro Windows (ETW). Všimněte si, že zobrazené události jsou ze stavové služby a stavové služby v aplikaci. Datový proud můžete pozastavit kliknutím na tlačítko **pozastavit** . Můžete si prohlédnout podrobnosti zprávy rozbalením této zprávy.

> [!NOTE]
> Před spuštěním aplikace se ujistěte, že máte spuštěný místní vývojový cluster. Informace o nastavení místního prostředí najdete v [příručce Začínáme](service-fabric-get-started.md) .
> 
> 

![Zobrazení diagnostických událostí v aplikaci Visual Studio](media/service-fabric-reliable-services-quick-start/hello-stateful-Output.png)

## <a name="next-steps"></a>Další kroky
[Ladění aplikace Service Fabric v aplikaci Visual Studio](service-fabric-debugging-your-application.md)

[Začínáme: Service Fabric služby webového rozhraní API pomocí samoobslužného hostování OWIN](./service-fabric-reliable-services-communication-aspnetcore.md)

[Další informace o spolehlivých kolekcích](service-fabric-reliable-services-reliable-collections.md)

[Nasazení aplikace](service-fabric-deploy-remove-applications.md)

[Upgrade aplikací](service-fabric-application-upgrade.md)

[Referenční příručka pro vývojáře pro Reliable Services](/previous-versions/azure/dn706529(v=azure.100))
