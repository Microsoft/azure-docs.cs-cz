---
title: Implementace funkce v Azure Service Fabric aktéři | Microsoft Docs
description: Popisuje, jak psát vlastní objektu actor služba, která implementuje funkce na úrovni služby, které byste při dědění StatefulService stejným způsobem jako.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: amanbha
ms.assetid: 45839a7f-0536-46f1-ae2b-8ba3556407fb
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/19/2018
ms.author: vturecek
ms.openlocfilehash: 41548c3395fa0c8f56e62cfcfb7338a2d53f040f
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34212887"
---
# <a name="implementing-service-level-features-in-your-actor-service"></a>Implementace funkce na úrovni služby ve službě objektu actor
Jak je popsáno v [služby rozvrstvení](service-fabric-reliable-actors-platform.md#service-layering), samotné služby objektu actor je spolehlivá služba.  Můžete napsat vlastní službu, která je odvozena z `ActorService` a implementovat funkce úrovně služeb stejným způsobem jako při dědění StatefulService, jako například:

- Služba zálohování a obnovení.
- Sdílené funkce pro všechny účastníky, například jistič.
- Vzdálená volání procedur v samotné služby objektu actor a v každé jednotlivé objektu actor.

## <a name="using-the-actor-service"></a>Pomocí služby objektu actor
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

## <a name="actor-service-methods"></a>Metody služby objektu actor
Implementuje služby objektu Actor `IActorService` (C#) nebo `ActorService` (Java), který naopak implementuje `IService` (C#) nebo `Service` (Java). Toto je rozhraní používá vzdálenou komunikaci spolehlivé služby, které umožňuje vzdálené volání procedur u metod služby. Obsahuje úrovně služby metod, které je možné volat vzdáleně přes vzdálené komunikace služby a umožňují [výčet](service-fabric-reliable-actors-enumerate.md) a [odstranit](service-fabric-reliable-actors-delete-actors.md) aktéři.


## <a name="custom-actor-service"></a>Služba vlastní objektu actor
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

## <a name="implementing-actor-backup-and-restore"></a>Implementace objektu actor zálohování a obnovení
Vlastní objektu actor služby můžou zpřístupnit metody zálohování dat objektu actor využívat výhod naslouchacího procesu vzdálené komunikace, která je již v `ActorService`.  Příklad, naleznete v části [zálohování a obnovení aktéři](service-fabric-reliable-actors-backup-and-restore.md).

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
