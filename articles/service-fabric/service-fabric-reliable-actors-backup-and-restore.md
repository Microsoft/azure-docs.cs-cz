---
title: Zálohování a obnovení objektů Azure Service Fabric
description: Zjistěte, jak implementovat zálohování a obnovení v objektech actor Azure Service Fabric.
author: vturecek
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: vturecek
ms.openlocfilehash: 41ba3f9c7d362756b800005d0c140c23dd96caa6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75370455"
---
# <a name="implement-reliable-actors-backup-and-restore"></a>Implementujte zálohování a obnovení spolehlivých herců

> [!NOTE]
> Společnost Microsoft doporučuje používat [pravidelné zálohování a obnovení](service-fabric-backuprestoreservice-quickstart-azurecluster.md) pro konfiguraci zálohování dat spolehlivé stavové služby a spolehlivé objekty actor. 
> 

V následujícím příkladu vlastní služba objektu actor zpřístupňuje metodu pro zálohování dat objektu actor využitím vzdáleného naslouchací proces již v `ActorService`:

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

V tomto `IMyActorService` příkladu je smlouva vzdálené `IService` komunikace, která `Service` implementuje (C#) `MyActorService`a (Java) a je pak implementována . Přidáním této smlouvy vzdálené komunikace `IMyActorService` jsou nyní metody k dispozici také klientovi `ActorServiceProxy`vytvořením proxy vzdálené komunikace prostřednictvím :

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

Další informace o spolehlivých aktérech naleznete v následujících článcích:
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
