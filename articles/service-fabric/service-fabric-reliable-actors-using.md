---
title: Implementace funkcí v objektech Azure Service Fabric
description: Popisuje, jak napsat vlastní službu actor, která implementuje funkce na úrovni služby stejným způsobem jako při zdědění StatefulService.
author: vturecek
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: vturecek
ms.openlocfilehash: 55ee4c7498dcda3060d4e4221711793b80132bdf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502283"
---
# <a name="implement-service-level-features-in-your-actor-service"></a>Implementace funkcí na úrovni služby ve službě actor

Jak je popsáno v [vrstvení služby](service-fabric-reliable-actors-platform.md#service-layering), samotná služba objektu actor je spolehlivá služba. Můžete napsat vlastní službu, `ActorService`která je odvozena od aplikace . Můžete také implementovat funkce na úrovni služby stejným způsobem, jako když zdědíte stavovou službu, například:

- Zálohování a obnovení služby.
- Sdílené funkce pro všechny aktéry, například jistič.
- Vzdálená procedura volá samotnou službu objektu actor a na jednotlivé ho sli.

## <a name="use-the-actor-service"></a>Použití služby objektu actor

Instance objektu actor mají přístup ke službě objektu actor, ve kterém jsou spuštěny. Prostřednictvím služby objektu actor instance objektu actor můžete programově získat kontext služby. Kontext služby má ID oddílu, název služby, název aplikace a další informace specifické pro platformu Azure Service Fabric.

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

Stejně jako všechny spolehlivé služby musí být služba objektu actor registrována s typem služby v runtime Service Fabric. Pro službu objektu actor spustit instance objektu actor, typ objektu actor také musí být registrovány služby objektu actor. Pro objekty actor tuto práci provádí metoda registrace `ActorRuntime`. V nejjednodušším případě můžete zaregistrovat typ objektu actor a služba objektu actor pak použije výchozí nastavení.

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

Alternativně můžete použít lambda poskytované metodou registrace k vytvoření služby objektu actor sami. Potom můžete nakonfigurovat službu objektu actor a explicitně vytvořit instance objektu actor. Můžete vložit závislosti objektu actor prostřednictvím jeho konstruktoru.

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

## <a name="actor-service-methods"></a>Metody služby actor

Služba objektu `IActorService` actor implementuje `ActorService` (C#) nebo `IService` (Java), které `Service` zase implementuje (C#) nebo (Java). Toto rozhraní používá vzdálené komunikace služby spolehlivé služby, což umožňuje vzdálené volání procedur na metody služby. Obsahuje metody na úrovni služby, které lze volat vzdáleně prostřednictvím vzdálené komunikace služby. Můžete ji použít k [vytvoření výčtu](service-fabric-reliable-actors-enumerate.md) a [odstranění](service-fabric-reliable-actors-delete-actors.md) objekty actor.


## <a name="custom-actor-service"></a>Vlastní služba objektu actor

Pomocí lambda registrace objektu actor můžete zaregistrovat vlastní službu objektu actor, která je odvozena z `ActorService` (C#) a `FabricActorService` (Java). Potom můžete implementovat vlastní funkce na úrovni služeb zápisem `ActorService` třídy služby, která dědí (C#) nebo `FabricActorService` (Java). Vlastní služba objektu actor dědí všechny `ActorService` funkce objektu `FabricActorService` actor runtime z (C#) nebo (Java). Lze jej použít k implementaci vlastních metod služby.

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

## <a name="implement-actor-backup-and-restore"></a>Implementace zálohování a obnovení objektu actor

Vlastní služba objektu actor může vystavit metodu zálohovat data objektu actor `ActorService`s využitím vzdálené komunikace naslouchací proces již v . Například viz [Zálohování a obnovení objekty actor](../synapse-analytics/sql-data-warehouse/backup-and-restore.md).

## <a name="actor-that-uses-a-remoting-v2-interface-compatible-stack"></a>Objekt actor, který používá zásobník vzdálené komunikace V2 (kompatibilní s rozhraním)

Vzdálené komunikace V2 (rozhraní kompatibilní, známý jako V2_1) zásobníku má všechny funkce zásobníku vzdálené komunikace V2. Jeho rozhraní je kompatibilní s remoting V1 zásobníku, ale není zpětně kompatibilní s V2 a V1. Chcete-li upgradovat z V1 na V2_1 bez ohledu na dostupnost služeb, postupujte podle kroků v další části.

Následující změny jsou nutné použít zásobníku vzdálené komunikace V2_1:

1. Přidejte následující atribut sestavení na rozhraní objektu actor.
  
   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1,RemotingClientVersion = RemotingClientVersion.V2_1)]
   ```

2. Sestavení a upgrade objektu actor a objekt actor klienta projekty začít používat zásobníku V2.

### <a name="actor-service-upgrade-to-remoting-v2-interface-compatible-stack-without-affecting-service-availability"></a>Upgrade služby Actor na zásobník vzdálené komunikace V2 (kompatibilní s rozhraním) bez ovlivnění dostupnosti služby

Tato změna je dvoustupňový upgrade. Postupujte podle kroků v tomto pořadí.

1. Přidejte následující atribut sestavení na rozhraní objektu actor. Tento atribut spustí dva naslouchací procesy pro službu objektu actor, V1 (existující) a V2_1 naslouchací proces. Upgradujte službu objektu actor pomocí této změny.

   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V1|RemotingListenerVersion.V2_1,RemotingClientVersion = RemotingClientVersion.V2_1)]
   ```

2. Po dokončení předchozího upgradu inovujte klienty objektu actor.
   Tento krok zajišťuje, že proxy objekt actor používá vzdálené komunikace V2_1 zásobníku.

3. Tento krok je volitelný. Změňte předchozí atribut odebrat naslouchací proces V1.

    ```csharp
    [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1,RemotingClientVersion = RemotingClientVersion.V2_1)]
    ```

## <a name="actor-that-uses-the-remoting-v2-stack"></a>Objekt actor, který používá zásobník vsazení do jiné komunikace V2

S balíčkem 2.8 NuGet, uživatelé nyní mohou používat vzdálené komunikace V2 zásobníku, který funguje lépe a poskytuje funkce, jako je vlastní serializace. Vzdálené komunikace V2 není zpětně kompatibilní s existující vzdálené komunikace zásobníku (nyní nazývá v1 vzdálené komunikace zásobníku).

Následující změny jsou nutné použít vzdálené komunikace V2 zásobníku.

1. Přidejte následující atribut sestavení na rozhraní objektu actor.

   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2,RemotingClientVersion = RemotingClientVersion.V2)]
   ```

2. Sestavení a upgrade projektů služby objektu actor a objektu actor klienta začít používat zásobníku V2.

### <a name="upgrade-the-actor-service-to-the-remoting-v2-stack-without-affecting-service-availability"></a>Upgrade služby objektu actor do zásobníku vzdálené komunikace V2 bez ovlivnění dostupnosti služby

Tato změna je dvoustupňový upgrade. Postupujte podle kroků v tomto pořadí.

1. Přidejte následující atribut sestavení na rozhraní objektu actor. Tento atribut spustí dva naslouchací procesy pro službu objektu actor, V1 (existující) a naslouchací proces V2. Upgradujte službu objektu actor pomocí této změny.

   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V1|RemotingListenerVersion.V2,RemotingClientVersion = RemotingClientVersion.V2)]
   ```

2. Po dokončení předchozího upgradu inovujte klienty objektu actor.
   Tento krok zajišťuje, že proxy objekt u actor používá vzdálené komunikace V2 zásobníku.

3. Tento krok je volitelný. Změňte předchozí atribut odebrat naslouchací proces V1.

    ```csharp
    [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2,RemotingClientVersion = RemotingClientVersion.V2)]
    ```

## <a name="next-steps"></a>Další kroky

* [Správa stavu actoru](service-fabric-reliable-actors-state-management.md)
* [Životní cyklus objektu actor a uvolňování paměti](service-fabric-reliable-actors-lifecycle.md)
* [Referenční dokumentace rozhraní API actors](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [Ukázkový kód rozhraní .NET](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Ukázkový kód Javy](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png
