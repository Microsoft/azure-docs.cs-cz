---
title: Reliable Actors ve službě Service Fabric | Dokumentace Microsoftu
description: Popisuje, jak jsou rozloženy do vrstev v Reliable Services v Reliable Actors a pomocí funkcí platformy Service Fabric.
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
ms.openlocfilehash: 82818df267fc08bf200178fda2c8d621bdd832ca
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2019
ms.locfileid: "55753159"
---
# <a name="how-reliable-actors-use-the-service-fabric-platform"></a>Jak objekty Reliable Actors využívají platformu Service Fabric
Tento článek vysvětluje, jak fungují Reliable Actors na platformě Azure Service Fabric. Spustit v rozhraní framework, která je hostována v implementaci spolehlivé stavové služby Reliable Actors volá se, *služba objektu actor*. Služba objektu actor obsahuje všechny komponenty potřebné ke správě životního cyklu a odesílání vašeho actors zpráva:

* Modulu Runtime Actor životního cyklu uvolňování paměti spravuje a vynucuje přístup s jedním vláknem.
* Naslouchací proces vzdálené komunikace služby objektu actor přijímá volání vzdáleného přístupu pro objekty actor a odesílá je do dispečera směrovat do instance odpovídající objektu actor.
* Poskytovatel stavu objektu Actor zabalí zprostředkovatelů stavu (například zprostředkovatele stavu Reliable Collections) a poskytuje adaptér pro správu stavu objektu actor.

Tyto součásti společně tvoří rozhraní Reliable Actors.

## <a name="service-layering"></a>Služba vrstvení
Protože samotnou službu objektu actor je spolehlivá služba, všechny [aplikační model](service-fabric-application-model.md), životní cyklus, [balení](service-fabric-package-apps.md), [nasazení](service-fabric-deploy-remove-applications.md), upgrade a škálování koncepty spolehlivé Služby použít stejným způsobem jako do služby objektu actor.

![Vrstvení služby objektu actor][1]

Předchozí diagram znázorňuje vztah mezi Service Fabric aplikační architektury a uživatelského kódu. Modré elementy reprezentují rozhraní framework aplikace Reliable Services, orange představuje Reliable Actors rozhraní framework a zelená představuje uživatelský kód.

V Reliable Services, službě dědí `StatefulService` třídy. Tato třída je odvozen z `StatefulServiceBase` (nebo `StatelessService` pro bezstavové služby). V Reliable Actors použijte službu objektu actor. Služba objektu actor se na různé implementace `StatefulServiceBase` třídu, která implementuje vzor objektu actor, ve kterém se spouští vaše objektů actor. Protože je právě implementace samotnou službu objektu actor `StatefulServiceBase`, můžete napsat vlastní služby, která je odvozena z `ActorService` a implementujte funkce na úrovni služby, stejně jako byste to udělali při dědění `StatefulService`, například:

* Zálohování a obnovení služby.
* Sdílené funkce pro všechny objekty actor, například jističe.
* Vzdálené volání procedur na samotnou službu objektu actor a na každé jednotlivé objektu actor.

Další informace najdete v tématu [implementace funkce na úrovni služby v rámci služby objektu actor](service-fabric-reliable-actors-using.md).

## <a name="application-model"></a>Aplikační model
Služby objektů actor jsou spolehlivé služby, takže aplikační model je stejný. Ale nástroje pro sestavení rozhraní objektu actor vygenerovat některé soubory modelu aplikace za vás.

### <a name="service-manifest"></a>Manifest služby
Nástroje pro sestavení rozhraní objektu actor automaticky generovat obsah souborů ServiceManifest.xml služba objektu actor. Tento soubor obsahuje:

* Typ služby objektu actor. Název typu je generován a základě název projektu objektu actor. Na základě trvalost atributu na objekt actor, HasPersistedState není zároveň nastavený příznak odpovídajícím způsobem.
* Balíček kódu.
* Balíček config.
* Prostředky a koncových bodů.

### <a name="application-manifest"></a>Manifest aplikace
Nástroje pro sestavení rozhraní objektu actor automaticky vytvořit výchozí definice služby pro vaši službu objektu actor. Nástroje sestavení naplnit výchozí vlastnosti služby:

* Počet sady replik je určen atribut trvalost na objekt actor. Pokaždé, když trvalost atribut na objekt actor se změní, počet sady replik ve výchozí definici služby se resetuje odpovídajícím způsobem.
* Schéma oddílu a rozsah nastavené jednotné Int64 s plný rozsah klíčů Int64.

## <a name="service-fabric-partition-concepts-for-actors"></a>Koncepty oddílů Service Fabric actors
Služby objektu actor se dělené stavové služby. Každý oddíl službu actor service obsahuje sadu objektů actor. Oddíly služby jsou automaticky rozloženy na více uzlů v Service Fabric. Instance objektu actor se distribuují ve výsledku.

![Objekt actor vytváření oddílů a rozdělení][5]

Reliable Services lze vytvořit pomocí různých oddílů schémata a rozsahů klíče oddílů. Služba objektu actor pomocí Int64 schéma rozdělení oddílů s plný rozsah klíčů Int64 mapuje actors na oddíly.

### <a name="actor-id"></a>ID objektu actor
Každý objekt actor, který se vytvoří ve službě má jedinečné ID přidružené k jeho reprezentována `ActorId` třídy. `ActorId` neprůhledná hodnota ID, který slouží pro rovnoměrné actors napříč oddíly služby vygenerováním náhodné ID je:

```csharp
ActorProxy.Create<IMyActor>(ActorId.CreateRandom());
```
```Java
ActorProxyBase.create<MyActor>(MyActor.class, ActorId.newId());
```


Každý `ActorId` se počítají hašovací hodnoty pro typ Int64. To je důvod, proč služba objektu actor musí používat schéma dělení Int64 s plný rozsah klíčů Int64. Vlastní ID hodnoty však lze použít pro `ActorID`, včetně GUID/UUID, řetězce a Int64s.

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

Při používání identifikátorů GUID/UUID a řetězce, jsou hodnoty mají hodnotu hash pro typ Int64. Ale když explicitně zadáváte Int64 do `ActorId`, Int64 namapuje přímo do oddílu bez další algoritmu hash. Můžete použít tuto techniku k oddíl, který actors jsou umístěny do ovládacího prvku.


## <a name="next-steps"></a>Další postup
* [Správa stavu objektu actor](service-fabric-reliable-actors-state-management.md)
* [Životní cyklus a uvolňování paměti kolekce objektu actor](service-fabric-reliable-actors-lifecycle.md)
* [Referenční dokumentace rozhraní API objektů actor](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.actors?redirectedfrom=MSDN&view=azure-dotnet)
* [Vzorový kód .NET](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Vzorový kód Java](http://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png
