---
title: Implementace funkce v Azure Service Fabric actors | Dokumentace Microsoftu
description: Popisuje, jak psát vlastní služba objektu actor, který implementuje stejným způsobem jako při dědění StatefulService funkce na úrovni služby.
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
ms.openlocfilehash: 6aff9e9599d31942f994f3cb4e5e9219f33dc7e1
ms.sourcegitcommit: 30221e77dd199ffe0f2e86f6e762df5a32cdbe5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2018
ms.locfileid: "39205516"
---
# <a name="implementing-service-level-features-in-your-actor-service"></a>Implementace funkce na úrovni služby v rámci služby objektu actor
Jak je popsáno v [služby vrstvení](service-fabric-reliable-actors-platform.md#service-layering), samotnou službu objektu actor je spolehlivé služby.  Můžete napsat vlastní služby, která je odvozena z `ActorService` a implementujte funkce na úrovni služby stejným způsobem jako při dědění StatefulService, jako například:

- Zálohování a obnovení služby.
- Sdílené funkce pro všechny objekty actor, například jističe.
- Vzdálené volání procedur na samotnou službu objektu actor a na každé jednotlivé objektu actor.

## <a name="using-the-actor-service"></a>Pomocí služby objektu actor
Instance objektu actor mají přístup ke službě objektu actor, ve kterém jsou spuštěné. Prostřednictvím služby objektu actor instance objektu actor prostřednictvím kódu programu můžete získat kontext služby. Kontext služby má ID oddílu, název služby, název aplikace a další informace specifické pro platformu Service Fabric:

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

Stejně jako Reliable Services musí být služba objektu actor zaregistrovaný s typem služby v modulu runtime Service Fabric. Pro službu objektu actor pro spuštění vaší instance objektu actor musí být typ actor zaregistrovaná taky v služba objektu actor. Pro objekty actor tuto práci provádí metoda registrace `ActorRuntime`. V nejjednodušším případě zaregistrujete na typ actor a služba objektu actor s výchozím nastavením se implicitně použije:

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

Alternativně můžete použít výraz lambda poskytuje způsob registrace k sestavení kompletních služba objektu actor, sami. Můžete pak nakonfigurovat služba objektu actor a explicitní tvorbu vaší instance objektu actor, kde můžete vložit závislosti pro objekt actor pomocí jeho konstruktoru:

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
Implementuje služba objektu Actor `IActorService` (C#) nebo `ActorService` (Java), která zase implementuje `IService` (C#) nebo `Service` (Java). Toto je rozhraní, který používá vzdálenou komunikaci modelu Reliable Services, která umožňuje vzdálené volání procedur na metody služby. Obsahuje metody úrovně služeb, které lze volat vzdáleně přes vzdálené komunikace služby a umožní vám [výčet](service-fabric-reliable-actors-enumerate.md) a [odstranit](service-fabric-reliable-actors-delete-actors.md) objektů actor.


## <a name="custom-actor-service"></a>Vlastní objekt actor service
Pomocí výrazu lambda registrace objektu actor, můžete zaregistrovat vlastní vlastní objekt actor služby, která je odvozena z `ActorService` (C#) a `FabricActorService` (Java). V této službě vlastní objekt actor, můžete implementovat vlastní funkce úrovně služeb zápisem, která dědí třídu služby `ActorService` (C#) nebo `FabricActorService` (Java). Služba objektu actor vlastní dědí všechny funkce modulu runtime actor z `ActorService` (C#) nebo `FabricActorService` (Java) a je možné implementovat vlastní metody služby.

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
Vlastní objekt actor service můžete vystavit metodu k zálohování dat objektu actor s využitím naslouchací proces vzdálené komunikace, která je již v `ActorService`.  Příklad najdete v tématu [zálohování a obnovení objektů actor](service-fabric-reliable-actors-backup-and-restore.md).

## <a name="actor-using-remoting-v2interfacecompatible-stack"></a>Použití vzdálené komunikace V2(InterfaceCompatible) zásobníku objektu actor
Vzdálená komunikace V2 zásobníku (InterfaceCompatible neboli V2_1) má všechny funkce vzdálené komunikace V2 zásobníku kromě je rozhraní kompatibilní zásobníku do zásobníku vzdálené komunikace V1, ale není zpětně kompatibilní s V2 a V1. Aby bylo možné provést upgrade z V1 na V2_1 bez dopadu na dostupnost služeb, postupujte podle následujících [článku](#actor-service-upgrade-to-remoting-v2interfacecompatible-stack-without-impacting-service-availability).

Následující změny jsou nutné k použití vzdálené komunikace V2_1 zásobníku.
 1. Přidejte následující atribut sestavení rozhraní objektu Actor.
   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1,RemotingClientVersion = RemotingClientVersion.V2_1)]
   ```

 2. Projekty sestavení a upgradovat ActorService objektu Actor klienta a pokud chcete začít používat V2 zásobníku.

#### <a name="actor-service-upgrade-to-remoting-v2interfacecompatible-stack-without-impacting-service-availability"></a>Objektu actor Service Upgrade na zásobník V2(InterfaceCompatible) vzdálené komunikace bez dopadu na dostupnost služeb.
Tato změna bude upgrade kroku 2. Jak je uvedeno, postupujte podle kroků ve stejném pořadí.

1.  Přidejte následující atribut sestavení rozhraní objektu Actor. Tento atribut se spustí dva naslouchací procesy pro ActorService V1 (existující) a V2_1 naslouchacího procesu. Upgrade ActorService díky této změně.

  ```csharp
  [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V1|RemotingListenerVersion.V2_1,RemotingClientVersion = RemotingClientVersion.V2_1)]
  ```

2. Upgrade ActorClients po dokončení upgradu výše.
Tento krok zajistí, že objekt Actor Proxy používá vzdálené komunikace V2_1 zásobníku.

3. Tento krok je volitelný. Změňte atribut výše k odebrání naslouchacího procesu V1.

    ```csharp
    [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1,RemotingClientVersion = RemotingClientVersion.V2_1)]
    ```

## <a name="actor-using-remoting-v2-stack"></a>Použití vzdálené komunikace V2 zásobníku objektu actor
2.8 součástí balíčku nuget uživatelé teď můžou používat vzdálenou komunikaci V2 zásobník. Díky tomu je výkonnější a poskytuje funkce, jako jsou vlastní serializace. Vzdálená komunikace V2 není zpětně kompatibilní s existující vzdálenou komunikaci zásobníku (voláme teď ho jako vzdálené komunikace V1 stack).

Následující změny jsou nutné k použití vzdálené komunikace V2 zásobníku.
 1. Přidejte následující atribut sestavení rozhraní objektu Actor.
   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2,RemotingClientVersion = RemotingClientVersion.V2)]
   ```

 2. Projekty sestavení a upgradovat ActorService objektu Actor klienta a pokud chcete začít používat V2 zásobníku.

#### <a name="actor-service-upgrade-to-remoting-v2-stack-without-impacting-service-availability"></a>Objektu actor Service Upgrade na zásobník V2 vzdálené komunikace bez dopadu na dostupnost služeb.
Tato změna bude upgrade kroku 2. Jak je uvedeno, postupujte podle kroků ve stejném pořadí.

1.  Přidejte následující atribut sestavení rozhraní objektu Actor. Tento atribut se spustí dva naslouchací procesy pro ActorService V1 (existující) a naslouchacího procesu V2. Upgrade ActorService díky této změně.

  ```csharp
  [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V1|RemotingListenerVersion.V2,RemotingClientVersion = RemotingClientVersion.V2)]
  ```

2. Upgrade ActorClients po dokončení upgradu výše.
Tento krok zajistí, že objekt Actor Proxy používá vzdálené komunikace V2 zásobníku.

3. Tento krok je volitelný. Změňte atribut výše k odebrání naslouchacího procesu V1.

    ```csharp
    [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2,RemotingClientVersion = RemotingClientVersion.V2)]
    ```

## <a name="next-steps"></a>Další postup
* [Správa stavu objektu actor](service-fabric-reliable-actors-state-management.md)
* [Životní cyklus a uvolňování paměti kolekce objektu actor](service-fabric-reliable-actors-lifecycle.md)
* [Referenční dokumentace rozhraní API objektů actor](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [Vzorový kód .NET](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Vzorový kód Java](http://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png
