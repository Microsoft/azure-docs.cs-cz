---
title: Reliable Actors v Service Fabric | Microsoft Docs
description: Popisuje, jak jsou vrstvu na spolehlivé služby Reliable Actors a pomocí funkcí platformy Service Fabric.
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
ms.date: 3/9/2018
ms.author: vturecek
ms.openlocfilehash: b2369f9468c54f10d01203841b6d7ba44b7ba2de
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
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

> [!NOTE]
> Stavové služby nejsou aktuálně podporované v jazyce Java nebo Linux.

Další informace najdete v tématu [implementace funkce na úrovni služby ve službě objektu actor](service-fabric-reliable-actors-using.md).

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
