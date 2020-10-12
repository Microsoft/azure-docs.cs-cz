---
title: Implementace funkcí v Azure Service Fabric Actors
description: Popisuje, jak napsat vlastní službu objektu actor, která implementuje funkce na úrovni služby stejným způsobem jako při dědění StatefulService.
author: vturecek
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: vturecek
ms.custom: devx-track-csharp
ms.openlocfilehash: 4eb8dd98909459feab72f61e46834e60c5b07ebd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89007753"
---
# <a name="implement-service-level-features-in-your-actor-service"></a>Implementace funkcí na úrovni služby ve službě objektu actor

Jak je popsáno v tématu [vrstvení služby](service-fabric-reliable-actors-platform.md#service-layering), služba actor sama o sobě představuje spolehlivou službu. Můžete napsat vlastní službu, která je odvozena z `ActorService` . Funkce na úrovni služby můžete implementovat také stejným způsobem jako při dědění stavové služby, jako je například:

- Zálohování a obnovení služby.
- Sdílená funkce všech objektů Actor, například přerušení okruhů.
- Vzdálená volání procedur přímo na samotné službě actor a na každý jednotlivec actor.

## <a name="use-the-actor-service"></a>Použití služby objektu actor

Instance objektu actor mají přístup ke službě objektu actor, ve které jsou spuštěny. Prostřednictvím služby objektu actor mohou instance objektu actor programově získat kontext služby. Kontext služby má ID oddílu, název služby, název aplikace a další informace, které jsou specifické pro platformu Azure Service Fabric.

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

Stejně jako všechny Reliable Services musí být služba objektu actor registrována s typem služby v modulu Service Fabric runtime. Aby služba actor mohla spouštět instance objektu actor, musí být váš typ objektu actor také zaregistrován ve službě objektu actor. Pro objekty actor tuto práci provádí metoda registrace `ActorRuntime`. V nejjednodušším případě můžete zaregistrovat typ objektu actor a službu objektu actor pak použije výchozí nastavení.

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

Alternativně můžete použít výraz lambda poskytnutý registrační metodou k vytvoření služby objektu actor sami. Pak můžete nakonfigurovat službu objektu actor a explicitně vytvořit instance objektu actor. Můžete vložit závislosti do objektu actor prostřednictvím jeho konstruktoru.

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

Služba actor implementuje `IActorService` (c#) nebo `ActorService` (Java), která zase implementuje `IService` (c#) nebo `Service` (Java). Toto rozhraní používá Reliable Services vzdálené komunikace, což umožňuje vzdálené volání procedur na metodách služby. Obsahuje metody úrovně služeb, které se dají vzdáleně volat prostřednictvím vzdálené komunikace služby. Můžete ji použít k zobrazení [výčtu](service-fabric-reliable-actors-enumerate.md) a [odstraňování](service-fabric-reliable-actors-delete-actors.md) objektů actor.


## <a name="custom-actor-service"></a>Vlastní služba objektu actor

Pomocí výrazu lambda pro registraci objektu actor můžete zaregistrovat vlastní službu objektu actor, která je odvozena z `ActorService` (C#) a `FabricActorService` (Java). Pak můžete implementovat vlastní funkce na úrovni služby napsáním třídy služby, která dědí `ActorService` (C#) nebo `FabricActorService` (Java). Vlastní služba objektu actor dědí všechny běhové funkce objektu actor z `ActorService` (C#) nebo `FabricActorService` (Java). Dá se použít k implementaci vlastních metod služby.

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

Vlastní služba objektu actor může vystavit metodu pro zálohování dat objektu actor tím, že využívá naslouchací proces vzdálené komunikace, který je již přítomen v nástroji `ActorService` . Příklad najdete v tématu [Backup and Restore Actors](service-fabric-reliable-actors-backup-and-restore.md).

## <a name="actor-that-uses-a-remoting-v2-interface-compatible-stack"></a>Objekt actor, který používá zásobník vzdálené komunikace v2 (rozhraní kompatibilní s rozhraním)

Zásobník vzdálené komunikace v2 (rozhraní kompatibilní se systémem, který se označuje jako V2_1) obsahuje všechny funkce sady vzdálené komunikace v2. Jeho rozhraní je kompatibilní se zásobníkem vzdálené komunikace V1, ale není zpětně kompatibilní s v2 a v1. Pokud chcete upgradovat z verze V1 na V2_1 bez vlivu na dostupnost služby, postupujte podle kroků v následující části.

Pro použití V2_1ho zásobníku pro vzdálenou komunikaci se vyžadují tyto změny:

1. Přidejte následující atribut sestavení na rozhraní objektu actor.
  
   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1,RemotingClientVersion = RemotingClientVersion.V2_1)]
   ```

2. Sestavení a upgrade služby objektu actor a klientské projekty objektu actor pro zahájení používání zásobníku v2.

### <a name="actor-service-upgrade-to-remoting-v2-interface-compatible-stack-without-affecting-service-availability"></a>Upgrade služby objektu actor na zásobník vzdálené komunikace v2 (rozhraní kompatibilní s rozhraním) bez ovlivnění dostupnosti služby

Tato změna je dvoustupňový upgrade. Postupujte podle kroků v této sekvenci.

1. Přidejte následující atribut sestavení na rozhraní objektu actor. Tento atribut spustí dva naslouchací procesy pro službu objektu actor, V1 (existující) a naslouchací proces V2_1. Při této změně upgradujte službu objektu actor.

   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V1|RemotingListenerVersion.V2_1,RemotingClientVersion = RemotingClientVersion.V2_1)]
   ```

2. Po dokončení předchozího upgradu upgradujte klienty objektu actor.
   Tento krok zajistí, že proxy server actor používá V2_1 zásobník pro vzdálenou komunikaci.

3. Tento krok je volitelný. Změňte předchozí atribut pro odebrání naslouchacího procesu v1.

    ```csharp
    [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1,RemotingClientVersion = RemotingClientVersion.V2_1)]
    ```

## <a name="actor-that-uses-the-remoting-v2-stack"></a>Objekt actor, který používá zásobník vzdálené komunikace v2

S balíčkem verze 2,8 NuGet můžou uživatelé teď používat zásobník vzdálené komunikace v2, který je vydaný lépe a poskytuje funkce, jako je vlastní serializace. Vzdálená komunikace v2 není zpětně kompatibilní s existujícím zásobníkem vzdálené komunikace (nyní se označuje jako zásobník vzdálené komunikace V1).

Pro použití zásobníku vzdálené komunikace v2 jsou vyžadovány následující změny.

1. Přidejte následující atribut sestavení na rozhraní objektu actor.

   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2,RemotingClientVersion = RemotingClientVersion.V2)]
   ```

2. Sestavte a upgradujte projekty objektu actor a klienta actor pro zahájení používání zásobníku v2.

### <a name="upgrade-the-actor-service-to-the-remoting-v2-stack-without-affecting-service-availability"></a>Upgrade služby objektu actor na sadu vzdálené komunikace v2, aniž by to ovlivnilo dostupnost služby

Tato změna je dvoustupňový upgrade. Postupujte podle kroků v této sekvenci.

1. Přidejte následující atribut sestavení na rozhraní objektu actor. Tento atribut spustí dva naslouchací procesy pro službu objektu actor, V1 (existující) a naslouchací proces v2. Při této změně upgradujte službu objektu actor.

   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V1|RemotingListenerVersion.V2,RemotingClientVersion = RemotingClientVersion.V2)]
   ```

2. Po dokončení předchozího upgradu upgradujte klienty objektu actor.
   Tento krok zajistí, že proxy server objektu actor používá zásobník vzdálené komunikace v2.

3. Tento krok je volitelný. Změňte předchozí atribut pro odebrání naslouchacího procesu v1.

    ```csharp
    [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2,RemotingClientVersion = RemotingClientVersion.V2)]
    ```

## <a name="next-steps"></a>Další kroky

* [Správa stavu objektu actor](service-fabric-reliable-actors-state-management.md)
* [Životní cyklus objektu actor a uvolňování paměti](service-fabric-reliable-actors-lifecycle.md)
* [Referenční dokumentace k rozhraní actor API](/previous-versions/azure/dn971626(v=azure.100))
* [Vzorový kód .NET](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Vzorový kód Java](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png
