---
title: Implementuje funkce v Azure Service Fabric actors | Dokumentace Microsoftu
description: Popisuje, jak psát vlastní služba objektu actor, který implementuje funkce na úrovni služby stejným způsobem jako při dědění StatefulService.
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
ms.openlocfilehash: 89161f3dad68c4b208f4badc548e2057c7ed58c1
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2018
ms.locfileid: "44022045"
---
# <a name="implement-service-level-features-in-your-actor-service"></a>Implementace funkce na úrovni služby v rámci služby objektu actor

Jak je popsáno v [služby vrstvení](service-fabric-reliable-actors-platform.md#service-layering), samotnou službu objektu actor je spolehlivé služby. Můžete napsat vlastní služby, která je odvozena z `ActorService`. Můžete také implementovat funkce na úrovni služby stejným způsobem jako při dědění stavové služby, jako například:

- Zálohování a obnovení služby.
- Sdílené funkce pro všechny objekty actor, například jističe.
- Vzdálené volání procedur na samotnou službu objektu actor a na každé jednotlivé objektu actor.

## <a name="use-the-actor-service"></a>Použít službu objektu actor

Instance objektu actor mají přístup ke službě objektu actor, ve kterém jsou spuštěné. Prostřednictvím služby objektu actor instance objektu actor prostřednictvím kódu programu můžete získat kontext služby. Kontext služby má ID oddílu, název služby, název aplikace a další informace specifické pro platformu Azure Service Fabric.

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

Stejně jako Reliable Services musí být služba objektu actor zaregistrovaný s typem služby v modulu runtime Service Fabric. Pro službu objektu actor pro spuštění vaší instance objektu actor typ actor musí být zaregistrovaný také služba objektu actor. Pro objekty actor tuto práci provádí metoda registrace `ActorRuntime`. V nejjednodušším případě zaregistrujete typ actor a služba objektu actor, použije výchozí nastavení.

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

Alternativně můžete použít výraz lambda poskytuje způsob registrace k sestavení kompletních služba objektu actor, sami. Pak můžete nakonfigurovat službu objektu actor a explicitní tvorbu vaše instance objektu actor. Závislosti lze vložit do objekt actor pomocí jeho konstruktoru.

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

Implementuje služba objektu actor `IActorService` (C#) nebo `ActorService` (Java), která zase implementuje `IService` (C#) nebo `Service` (Java). Toto rozhraní se používá ve vzdálené komunikace modelu Reliable Services, která umožňuje vzdálené volání procedur na metody služby. Obsahuje metody úrovně služeb, které lze volat vzdáleně přes vzdálenou komunikaci služby. Můžete použít k [výčet](service-fabric-reliable-actors-enumerate.md) a [odstranit](service-fabric-reliable-actors-delete-actors.md) objektů actor.


## <a name="custom-actor-service"></a>Vlastní objekt actor service

Pomocí výrazu lambda registrace objektu actor, můžete zaregistrovat vlastní vlastní objekt actor služby, která je odvozena z `ActorService` (C#) a `FabricActorService` (Java). Potom můžete implementovat vlastní funkce úrovně služeb zápisem, která dědí třídu služby `ActorService` (C#) nebo `FabricActorService` (Java). Služba objektu actor vlastní dědí všechny funkce modulu runtime actor z `ActorService` (C#) nebo `FabricActorService` (Java). Slouží k implementaci vlastních metod služby.

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

## <a name="implement-actor-backup-and-restore"></a>Implementace objektu actor zálohování a obnovení

Vlastní objekt actor service můžete vystavit metodu k zálohování dat objektu actor s využitím naslouchací proces vzdálené komunikace, která je již v `ActorService`. Příklad najdete v tématu [zálohování a obnovení objektů actor](service-fabric-reliable-actors-backup-and-restore.md).

## <a name="actor-that-uses-a-remoting-v2-interface-compatible-stack"></a>Objekt actor, který používá vzdálenou komunikaci V2 zásobníku (kompatibilní s rozhraní)

Vzdálená komunikace V2 (rozhraní kompatibilní, označované jako V2_1) zásobník obsahuje všechny funkce zásobníku vzdálené komunikace V2. Rozhraní je kompatibilní se zásobníkem V1 vzdálenou komunikaci, ale není zpětně kompatibilní s V2 a V1. Chcete-li upgradovat z V1 na V2_1 s žádný vliv na dostupnost služeb, postupujte podle kroků v další části.

Následující změny, musí používat vzdálenou komunikaci V2_1 zásobníku:

 1. Přidejte následující atribut sestavení rozhraní objektu actor.
  
   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1,RemotingClientVersion = RemotingClientVersion.V2_1)]
   ```

 2. Sestavení a upgrade služby objektu actor a klientské projekty actor samy chcete začít používat V2 zásobníku.

### <a name="actor-service-upgrade-to-remoting-v2-interface-compatible-stack-without-affecting-service-availability"></a>Upgrade služby objektu actor pro vzdálenou komunikaci V2 (rozhraní kompatibilní) zásobníku bez ovlivnění dostupnosti služby

Tato změna se dvoustupňové upgrade. Postupujte podle kroků v tomto pořadí.

1. Přidejte následující atribut sestavení rozhraní objektu actor. Tento atribut spustí dva naslouchací procesy pro službu objektu actor V1 (existující) a V2_1 naslouchacího procesu. Upgradujte službu objektu actor díky této změně.

  ```csharp
  [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V1|RemotingListenerVersion.V2_1,RemotingClientVersion = RemotingClientVersion.V2_1)]
  ```

2. Upgradujte klienty objektu actor, po dokončení předchozí upgrade.
Tento krok zajistí, že objekt actor proxy používá vzdálené komunikace V2_1 zásobníku.

3. Tento krok je volitelný. Změňte atribut předchozí k odebrání naslouchacího procesu V1.

    ```csharp
    [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1,RemotingClientVersion = RemotingClientVersion.V2_1)]
    ```

## <a name="actor-that-uses-the-remoting-v2-stack"></a>Objekt actor, který používá vzdálenou komunikaci V2 zásobníku

Pomocí balíčku NuGet verze 2.8 uživatelé teď můžou používat V2 zásobníku vzdálené komunikace, která vrací lepší výsledky a poskytuje funkce, jako jsou vlastní serializace. Vzdálená komunikace V2 není zpětně kompatibilní s existující vzdálenou komunikaci zásobníku (nyní nazývá Vzdálená komunikace zásobníku V1).

Následující změny jsou nutné k použití vzdálené komunikace V2 zásobníku.

 1. Přidejte následující atribut sestavení rozhraní objektu actor.

   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2,RemotingClientVersion = RemotingClientVersion.V2)]
   ```

 2. Sestavení a upgrade služby objektu actor a klientské projekty actor samy chcete začít používat V2 zásobníku.

### <a name="upgrade-the-actor-service-to-the-remoting-v2-stack-without-affecting-service-availability"></a>Upgradovat službu objektu actor se zásobníkem vzdálené komunikace V2 bez ovlivnění dostupnosti služby

Tato změna se dvoustupňové upgrade. Postupujte podle kroků v tomto pořadí.

1. Přidejte následující atribut sestavení rozhraní objektu actor. Tento atribut spustí dva naslouchací procesy pro službu objektu actor V1 (existující) a naslouchacího procesu V2. Upgradujte službu objektu actor díky této změně.

  ```csharp
  [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V1|RemotingListenerVersion.V2,RemotingClientVersion = RemotingClientVersion.V2)]
  ```

2. Upgradujte klienty objektu actor, po dokončení předchozí upgrade.
Tento krok zajistí, že proxy server objektu actor používá vzdálené komunikace V2 zásobníku.

3. Tento krok je volitelný. Změňte atribut předchozí k odebrání naslouchacího procesu V1.

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
