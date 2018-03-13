---
title: Reliable Actors v Service Fabric | Microsoft Docs
description: "Popisuje, jak jsou vrstvu na spolehlivé služby Reliable Actors a pomocí funkcí platformy Service Fabric."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: amanbha
ms.assetid: 45839a7f-0536-46f1-ae2b-8ba3556407fb
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 3/9/2018
ms.author: vturecek
ms.openlocfilehash: ee248cb656eeb54e259ff1adf45080a207b5a866
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2018
---
# <a name="how-reliable-actors-use-the-service-fabric-platform"></a>Jak objekty Reliable Actors využívají platformu Service Fabric
Tento článek vysvětluje, jak fungují Reliable Actors na platformě Azure Service Fabric. Spustit v rozhraní, které je hostován v implementaci stavové spolehlivé služby Reliable Actors názvem *služby objektu actor*. Služby objektu actor obsahuje všechny komponenty potřebné ke správě životního cyklu a odeslání pro vaše aktéři zpráva:

* Modul Runtime objektu Actor spravuje životní cyklus, uvolňování paměti a vynucuje jednovláknové přístup.
* Naslouchací proces vzdálené komunikace služby objektu actor přijímá volání aktéři vzdáleného přístupu a je odešle do dispečera směrovat na příslušné objektu actor instanci.
* Zprostředkovatel stavu objektu Actor zabalí zprostředkovatelů stavu (např. zprostředkovatele stavu spolehlivé kolekce) a poskytuje adaptér pro správu stavu objektu actor.

Tyto součásti společně tvoří spolehlivé objektu Actor framework.

## <a name="service-layering"></a>Služba vrstvení
Protože samotné služby objektu actor je spolehlivá služba, všechny [aplikačního modelu](service-fabric-application-model.md), životního cyklu, [balení](service-fabric-package-apps.md), [nasazení](service-fabric-deploy-remove-applications.md), upgrade a škálování koncepty spolehlivé služby použít stejným způsobem jako do služby objektu actor.

![Rozvrstvení služby objektu actor][1]

Předchozí diagram znázorňuje vztah mezi architektury aplikace Service Fabric a uživatelského kódu. Modré elementy reprezentují rozhraní spolehlivé služby, oranžová představuje rozhraní objektu Actor spolehlivé a zelená představuje uživatelského kódu.

V spolehlivé služby, služby dědí `StatefulService` třídy. Tato třída je sám odvozené od `StatefulServiceBase` (nebo `StatelessService` pro bezstavové služby). V Reliable Actors pomocí služby objektu actor. Je na různé implementace služby objektu actor `StatefulServiceBase` třídu, která implementuje vzor objektu actor, kde vaše aktéři spustit. Protože je právě implementací samotné služby objektu actor `StatefulServiceBase`, můžete napsat vlastní službu, která je odvozena z `ActorService` a implementovat funkce úrovně služeb stejným způsobem jako při dědění `StatefulService`, jako například:

* Služba zálohování a obnovení.
* Sdílené funkce pro všechny účastníky, například jistič.
* Vzdálená volání procedur v samotné služby objektu actor a v každé jednotlivé objektu actor.

### <a name="using-the-actor-service"></a>Pomocí služby objektu actor
Instance objektu actor mít přístup ke službě objektu actor, ve kterém běží. Prostřednictvím služby objektu actor instancí objektu actor prostřednictvím kódu programu získat kontext služby. Kontext služby má ID oddílu, název služby, název aplikace a další informace specifické pro platformu Service Fabric:

```csharp
Task MyActorMethod()
{
    Guid partitionId = this.ActorService.Context.PartitionId;
    string serviceTypeName = this.ActorService.Context.ServiceTypeName;
    Uri serviceInstanceName = this.ActorService.Context.ServiceName;
    string applicationInstanceName = this.ActorService.Context.CodePackageActivationContext.ApplicationName;
}
```
```Java
CompletableFuture<?> MyActorMethod()
{
    UUID partitionId = this.getActorService().getServiceContext().getPartitionId();
    String serviceTypeName = this.getActorService().getServiceContext().getServiceTypeName();
    URI serviceInstanceName = this.getActorService().getServiceContext().getServiceName();
    String applicationInstanceName = this.getActorService().getServiceContext().getCodePackageActivationContext().getApplicationName();
}
```


Podobně jako všechny spolehlivé služby musí být zaregistrován služby objektu actor s typem služby v modulu runtime Service Fabric. Služby objektu actor ke spuštění vaše instance objektu actor musí být typu vašeho objektu actor také zaregistrován u služby objektu actor. Pro objekty actor tuto práci provádí metoda registrace `ActorRuntime`. V nejjednodušším případě zaregistrujete na typ vašeho objektu actor a služby objektu actor s výchozím nastavením se implicitně použije:

```csharp
static class Program
{
    private static void Main()
    {
        ActorRuntime.RegisterActorAsync<MyActor>().GetAwaiter().GetResult();

        Thread.Sleep(Timeout.Infinite);
    }
}
```

Alternativně můžete lambda poskytované registrace metoda služby objektu actor vytvořit sami. Můžete pak nakonfigurovat služby objektu actor a explicitně vytvořit vaše objektu actor instance, kde můžete vložit závislosti na vaší objektu actor prostřednictvím jeho konstruktoru:

```csharp
static class Program
{
    private static void Main()
    {
        ActorRuntime.RegisterActorAsync<MyActor>(
            (context, actorType) => new ActorService(context, actorType, () => new MyActor()))
            .GetAwaiter().GetResult();

        Thread.Sleep(Timeout.Infinite);
    }
}
```
```Java
static class Program
{
    private static void Main()
    {
      ActorRuntime.registerActorAsync(
              MyActor.class,
              (context, actorTypeInfo) -> new FabricActorService(context, actorTypeInfo),
              timeout);

        Thread.sleep(Long.MAX_VALUE);
    }
}
```

### <a name="actor-service-methods"></a>Metody služby objektu actor
Implementuje služby objektu Actor `IActorService` (C#) nebo `ActorService` (Java), který naopak implementuje `IService` (C#) nebo `Service` (Java). Toto je rozhraní používá vzdálenou komunikaci spolehlivé služby, které umožňuje vzdálené volání procedur u metod služby. Obsahuje metody úrovni služby, které je možné volat vzdáleně přes vzdálenou komunikaci služby.

#### <a name="enumerating-actors"></a>Vytváření výčtu actors
Služby objektu actor umožňuje klientovi výčet metadata o aktéři, která je hostitelem služby. Protože služby objektu actor oddílů stavové služby, výčet se provádí na oddíl. Protože každý oddíl může obsahovat mnoho aktéři, výčtu se vrátí jako sadu stránkových výsledků. Na stránkách jsou smyčce přes, dokud se číst všechny stránky. Následující příklad ukazuje, jak vytvořit seznam všech active aktéři v jednom oddílu služby objektu actor:

```csharp
IActorService actorServiceProxy = ActorServiceProxy.Create(
    new Uri("fabric:/MyApp/MyService"), partitionKey);

ContinuationToken continuationToken = null;
List<ActorInformation> activeActors = new List<ActorInformation>();

do
{
    PagedResult<ActorInformation> page = await actorServiceProxy.GetActorsAsync(continuationToken, cancellationToken);

    activeActors.AddRange(page.Items.Where(x => x.IsActive));

    continuationToken = page.ContinuationToken;
}
while (continuationToken != null);
```

```Java
ActorService actorServiceProxy = ActorServiceProxy.create(
    new URI("fabric:/MyApp/MyService"), partitionKey);

ContinuationToken continuationToken = null;
List<ActorInformation> activeActors = new ArrayList<ActorInformation>();

do
{
    PagedResult<ActorInformation> page = actorServiceProxy.getActorsAsync(continuationToken);

    while(ActorInformation x: page.getItems())
    {
         if(x.isActive()){
              activeActors.add(x);
         }
    }

    continuationToken = page.getContinuationToken();
}
while (continuationToken != null);
```

#### <a name="deleting-actors"></a>Odstranění actors
Služby objektu actor také poskytuje funkce pro odstranění aktéři:

```csharp
ActorId actorToDelete = new ActorId(id);

IActorService myActorServiceProxy = ActorServiceProxy.Create(
    new Uri("fabric:/MyApp/MyService"), actorToDelete);

await myActorServiceProxy.DeleteActorAsync(actorToDelete, cancellationToken)
```
```Java
ActorId actorToDelete = new ActorId(id);

ActorService myActorServiceProxy = ActorServiceProxy.create(
    new URI("fabric:/MyApp/MyService"), actorToDelete);

myActorServiceProxy.deleteActorAsync(actorToDelete);
```

Další informace o odstranění aktéři a jejich stavu najdete v tématu [objektu actor životního cyklu dokumentaci](service-fabric-reliable-actors-lifecycle.md).

### <a name="custom-actor-service"></a>Služba vlastní objektu actor
Pomocí lambda registrace objektu actor můžete zaregistrovat vlastní vlastní objektu actor služba, která je odvozena z `ActorService` (C#) a `FabricActorService` (Java). V rámci této služby objektu actor vlastní můžete implementovat vlastní úroveň služby funkce napsáním třídu služby, který dědí `ActorService` (C#) nebo `FabricActorService` (Java). Služby objektu actor vlastní dědí všechny funkcionalita modulu runtime objektu actor z `ActorService` (C#) nebo `FabricActorService` (Java) a lze použít k implementaci vlastních metod služby.

```csharp
class MyActorService : ActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, Func<ActorBase> newActor)
        : base(context, typeInfo, newActor)
    { }
}
```
```Java
class MyActorService extends FabricActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, BiFunction<FabricActorService, ActorId, ActorBase> newActor)
    {
         super(context, typeInfo, newActor);
    }
}
```

```csharp
static class Program
{
    private static void Main()
    {
        ActorRuntime.RegisterActorAsync<MyActor>(
            (context, actorType) => new MyActorService(context, actorType, () => new MyActor()))
            .GetAwaiter().GetResult();

        Thread.Sleep(Timeout.Infinite);
    }
}
```
```Java
public class Program
{
    public static void main(String[] args)
    {
        ActorRuntime.registerActorAsync(
                MyActor.class,
                (context, actorTypeInfo) -> new FabricActorService(context, actorTypeInfo),
                timeout);
        Thread.sleep(Long.MAX_VALUE);
    }
}
```

#### <a name="implementing-actor-backup-and-restore"></a>Implementace objektu actor zálohování a obnovení
 V následujícím příkladu služby objektu actor vlastní zpřístupní metodu zálohování dat objektu actor využívat výhod naslouchacího procesu vzdálené komunikace, která je již v `ActorService`:

```csharp
public interface IMyActorService : IService
{
    Task BackupActorsAsync();
}

class MyActorService : ActorService, IMyActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, Func<ActorBase> newActor)
        : base(context, typeInfo, newActor)
    { }

    public Task BackupActorsAsync()
    {
        return this.BackupAsync(new BackupDescription(PerformBackupAsync));
    }

    private async Task<bool> PerformBackupAsync(BackupInfo backupInfo, CancellationToken cancellationToken)
    {
        try
        {
           // store the contents of backupInfo.Directory
           return true;
        }
        finally
        {
           Directory.Delete(backupInfo.Directory, recursive: true);
        }
    }
}
```
```Java
public interface MyActorService extends Service
{
    CompletableFuture<?> backupActorsAsync();
}

class MyActorServiceImpl extends ActorService implements MyActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, Func<FabricActorService, ActorId, ActorBase> newActor)
    {
       super(context, typeInfo, newActor);
    }

    public CompletableFuture backupActorsAsync()
    {
        return this.backupAsync(new BackupDescription((backupInfo, cancellationToken) -> performBackupAsync(backupInfo, cancellationToken)));
    }

    private CompletableFuture<Boolean> performBackupAsync(BackupInfo backupInfo, CancellationToken cancellationToken)
    {
        try
        {
           // store the contents of backupInfo.Directory
           return true;
        }
        finally
        {
           deleteDirectory(backupInfo.Directory)
        }
    }

    void deleteDirectory(File file) {
        File[] contents = file.listFiles();
        if (contents != null) {
            for (File f : contents) {
               deleteDirectory(f);
             }
        }
        file.delete();
    }
}
```


V tomto příkladu `IMyActorService` je Vzdálená komunikace kontrakt, který implementuje `IService` (C#) a `Service` (Java) a pak je implementováno modulem `MyActorService`. Přidáním této smlouvy vzdálenou komunikaci, metody na `IMyActorService` jsou nyní k dispozici také ke klientovi vytvořením proxy vzdálenou komunikaci prostřednictvím `ActorServiceProxy`:

```csharp
IMyActorService myActorServiceProxy = ActorServiceProxy.Create<IMyActorService>(
    new Uri("fabric:/MyApp/MyService"), ActorId.CreateRandom());

await myActorServiceProxy.BackupActorsAsync();
```
```Java
MyActorService myActorServiceProxy = ActorServiceProxy.create(MyActorService.class,
    new URI("fabric:/MyApp/MyService"), actorId);

myActorServiceProxy.backupActorsAsync();
```

## <a name="application-model"></a>Aplikační model
Služby objektu actor jsou spolehlivé služby, takže aplikačního modelu je stejný. Však nástroje sestavení objektu actor framework generovat některé soubory modelu aplikace za vás.

### <a name="service-manifest"></a>Manifest služby
Nástroje sestavení objektu actor framework automaticky generovat obsah souboru ServiceManifest.xml služby objektu actor. Tento soubor obsahuje:

* Typ služby objektu actor. Název typu je vytvořen na základě názvu objektu actor projektu. Založená na atributu trvalost na vaše objektu actor, je HasPersistedState také nastavený příznak odpovídajícím způsobem.
* Balíček kódu.
* Konfigurační balíček.
* Koncové body a prostředky.

### <a name="application-manifest"></a>Manifest aplikace
Nástroje sestavení objektu actor framework automaticky vytvoří definici výchozí služby pro služby objektu actor. Nástroje pro sestavení naplnit výchozí vlastnosti služby:

* Počet sady replik je určen podle atribut trvalost na vaše objektu actor. Pokaždé, když trvalost atribut na vaše objektu actor mění, počet sady replik v definici výchozí služby se resetuje odpovídajícím způsobem.
* Schéma oddílu a rozsah jsou nastaveny na Uniform Int64 s plný rozsah klíče Int64.

## <a name="service-fabric-partition-concepts-for-actors"></a>Koncepty oddílu Service Fabric actors
Služby objektu actor jsou oddílů stavové služby. Každý oddíl služby objektu actor obsahuje sadu aktéři. Oddílů služby je automaticky distribuovaná přes více uzlů v Service Fabric. Instance objektu actor jsou distribuovány v důsledku.

![Objekt actor vytváření oddílů a distribuci][5]

Spolehlivé služby můžete vytvořit s schémata jiný oddíl a oddíl rozsahy klíčů. Služby objektu actor používá schéma rozdělení oddílů Int64 s plný rozsah klíče Int64 k mapování aktéři na oddíly.

### <a name="actor-id"></a>ID objektu actor
Každý objekt actor, který se vytvoří ve službě má jedinečné ID přidružený reprezentována `ActorId` třídy. `ActorId` je neprůhledné hodnoty ID, které je možné pro rovnoměrné aktéři mezi oddílů služby tak, že generuje náhodné ID:

```csharp
ActorProxy.Create<IMyActor>(ActorId.CreateRandom());
```
```Java
ActorProxyBase.create<MyActor>(MyActor.class, ActorId.newId());
```


Každý `ActorId` se rozdělí na datovém typu Int64. Z tohoto důvodu služby objektu actor musí používat schéma rozdělení oddílů Int64 s plný rozsah klíče Int64. Však můžete použít vlastní hodnoty ID pro `ActorID`, včetně GUID/UUID, řetězce a Int64s.

```csharp
ActorProxy.Create<IMyActor>(new ActorId(Guid.NewGuid()));
ActorProxy.Create<IMyActor>(new ActorId("myActorId"));
ActorProxy.Create<IMyActor>(new ActorId(1234));
```
```Java
ActorProxyBase.create(MyActor.class, new ActorId(UUID.randomUUID()));
ActorProxyBase.create(MyActor.class, new ActorId("myActorId"));
ActorProxyBase.create(MyActor.class, new ActorId(1234));
```

Pokud používáte GUID/UUID a řetězce, hodnoty rozdělí na datovém typu Int64. Ale když explicitně zadáváte Int64 pro `ActorId`, Int64 se mapování přímo na oddíl bez další algoritmu hash. Tento postup na ovládací prvek oddíl, který aktéři jsou umístěny v můžete použít.

## <a name="actor-using-remoting-v2-stack"></a>Použití zásobníku V2 vzdálenou komunikaci objektu actor
2,8 balíčkem nuget uživatelé teď můžou používat vzdálenou komunikaci V2 zásobníku, která je další původce a poskytuje funkce, například vlastní serializace. Vzdálená komunikace V2 není zpětně kompatibilní s existující vzdálenou komunikaci zásobníku (voláme teď ho jako Vzdálená komunikace V1 zásobníku).

Následující změny vyžadovaných pro použití zásobníku V2 vzdálenou komunikaci.
 1. Přidejte následující atribut sestavení v objektu Actor rozhraní.
   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListener = RemotingListener.V2Listener,RemotingClient = RemotingClient.V2Client)]
   ```

 2. Sestavení a upgradovat ActorService a objektu Actor klienta projekty k použití zásobníku V2.

### <a name="actor-service-upgrade-to-remoting-v2-stack-without-impacting-service-availability"></a>Objektu actor služby upgradovat na vzdálenou komunikaci V2 zásobníku bez dopadu na dostupnost služeb.
Tato změna bude upgrade kroku 2. Jak je uvedeno, postupujte podle kroků ve stejném pořadí.

1.  Přidejte následující atribut sestavení v objektu Actor rozhraní. Tento atribut se spustí dva naslouchací procesy pro ActorService V1 (existující) a V2 naslouchací proces. Upgrade ActorService v této změně.

  ```csharp
  [assembly:FabricTransportActorRemotingProvider(RemotingListener = RemotingListener.CompatListener,RemotingClient = RemotingClient.V2Client)]
  ```

2. Upgrade ActorClients po dokončení upgradu výše.
Tento krok zajistí, že Proxy objektu Actor používá zásobník V2 vzdálenou komunikaci.

3. Tento krok je volitelný. Změňte atribut výše, čímž odeberete naslouchací proces V1.

    ```csharp
    [assembly:FabricTransportActorRemotingProvider(RemotingListener = RemotingListener.V2Listener,RemotingClient = RemotingClient.V2Client)]
    ```

## <a name="next-steps"></a>Další postup
* [Řízení stavu objektu actor](service-fabric-reliable-actors-state-management.md)
* [Kolekce paměti a životního cyklu objektu actor](service-fabric-reliable-actors-lifecycle.md)
* [Referenční dokumentace rozhraní API actors](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [Ukázkový kód rozhraní .NET](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Ukázkový kód Java](http://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png
