---
title: Spolehlivé herce na service fabric
description: Popisuje, jak jsou spolehlivé objekty actor vrstvy na spolehlivé služby a používat funkce platformy Service Fabric.
author: vturecek
ms.topic: conceptual
ms.date: 3/9/2018
ms.author: vturecek
ms.openlocfilehash: 92c717fa2c82dd147acd3c28333e37ccf8dd2e89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282299"
---
# <a name="how-reliable-actors-use-the-service-fabric-platform"></a>Jak spolehliví aktéři používají platformu Service Fabric
Tento článek vysvětluje, jak spolehlivé objekty actor pracovat na platformě Azure Service Fabric. Spolehlivé objekty actor spustit v rámci, který je hostován v implementaci stavové spolehlivé služby s názvem *služby objektu actor*. Služba objektu actor obsahuje všechny součásti potřebné ke správě životního cyklu a odesílání zpráv pro objekty actor:

* Objekt actor runtime spravuje životní cyklus, uvolňování paměti a vynucuje přístup s jedním vláknem.
* Naslouchací proces vzdálené komunikace objektu actor přijímá volání vzdáleného přístupu k objektu actor a odešle je dispečerovi k směrování do příslušné instance objektu actor.
* Zprostředkovatel stavu objektu actor zabalí zprostředkovatele stavu (například zprostředkovatele stavu spolehlivé kolekce) a poskytuje adaptér pro správu stavu objektu actor.

Tyto součásti společně tvoří rámec reliable actor.

## <a name="service-layering"></a>Vrstvení služby
Vzhledem k tomu, že samotná služba objektu actor je spolehlivá služba, všechny [koncepty objektu actor](service-fabric-application-model.md), všechny koncepty aplikace , životní cyklus, [balení](service-fabric-package-apps.md), [nasazení](service-fabric-deploy-remove-applications.md), upgrade a škálování spolehlivé služby platí stejným způsobem pro služby objektu actor.

![Vrstvení služby objektu actor][1]

Předchozí diagram znázorňuje vztah mezi rozhraními aplikace Service Fabric a uživatelským kódem. Modré prvky představují architekturu aplikace spolehlivé služby, oranžová představuje rozhraní reliable actor a zelená představuje uživatelský kód.

Ve spolehlivých službách vaše `StatefulService` služba dědí třídu. Tato třída je sama `StatefulServiceBase` odvozena od (nebo `StatelessService` pro služby bez stav). V spolehlivé herci, můžete použít službu actor. Služba actor je různé implementace `StatefulServiceBase` třídy, která implementuje objekt actor vzor, kde jsou spuštěny objekty actor. Vzhledem k tomu, že `StatefulServiceBase`samotná služba objektu actor je `ActorService` pouze implementace aplikace , můžete napsat vlastní `StatefulService`službu, která je odvozena od funkcí na úrovni služby a implementovat je stejným způsobem jako při dědění , například:

* Zálohování a obnovení služby.
* Sdílené funkce pro všechny aktéry, například jistič.
* Vzdálená procedura volá samotnou službu objektu actor a na jednotlivé ho sli.

Další informace naleznete [v tématu Implementace funkcí na úrovni služby ve službě objektu actor](service-fabric-reliable-actors-using.md).

## <a name="application-model"></a>Aplikační model
Actor služby jsou spolehlivé služby, takže model aplikace je stejný. Však nástroje sestavení rozhraní actor generovat některé soubory modelu aplikace pro vás.

### <a name="service-manifest"></a>Manifest služby
Nástroje pro vytváření architektury objektu actor automaticky generují obsah souboru ServiceManifest.xml služby actor služby. Tento soubor obsahuje:

* Typ služby objektu actor. Název typu je generován na základě názvu projektu herce. Na základě atributu trvalosti na objektu actor haspersistedState příznak je také nastaven odpovídajícím způsobem.
* Balíček kódu.
* Config balíček.
* Prostředky a koncové body.

### <a name="application-manifest"></a>Manifest aplikace
Nástroje sestavení architektury objektu actor automaticky vytvořit výchozí definici služby pro službu objektu actor. Nástroje sestavení naplní výchozí vlastnosti služby:

* Počet sad replik je určen atributem trvalosti na objektu actor. Pokaždé, když se změní atribut trvalosti na objektu actor, počet sad replik ve výchozí definici služby se odpovídajícím způsobem vynuluje.
* Schéma oddílu a rozsah jsou nastaveny na uniformu Int64 s plným rozsahem klíčů Int64.

## <a name="service-fabric-partition-concepts-for-actors"></a>Koncepty oddílů Service Fabric pro objekty actor
Služby objektu actor jsou rozdělené stavové služby. Každý oddíl služby objektu actor obsahuje sadu objekty actor. Oddíly služby jsou automaticky distribuovány přes více uzlů v Service Fabric. V důsledku toho jsou distribuovány instance objektu actor.

![Dělení a distribuce objektu actor][5]

Spolehlivé služby lze vytvořit s různými schématy oddílů a rozsahy klíčů oddílů. Služba objektu actor používá schéma dělení Int64 s úplným rozsahem klíčů Int64 k mapování objektů actor na oddíly.

### <a name="actor-id"></a>Herec ID
Každý objekt actor, který je vytvořen ve službě má jedinečné `ActorId` ID s ním spojené, reprezentované třídy. `ActorId`je neprůhledná hodnota ID, kterou lze použít pro rovnoměrné rozložení aktérů mezi oddíly služby generováním náhodných ID:

```csharp
ActorProxy.Create<IMyActor>(ActorId.CreateRandom());
```
```Java
ActorProxyBase.create<MyActor>(MyActor.class, ActorId.newId());
```


Každý `ActorId` je zašepován na Int64. To je důvod, proč služba objektu actor musí používat schéma dělení Int64 s úplným rozsahem klíčů Int64. Vlastní hodnoty ID však lze `ActorID`použít pro , včetně identifikátorů GUID/UUID, řetězců a Int64s.

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

Při použití identifikátorů GUID/UUID a řetězců jsou hodnoty zapisovány do int64. Však pokud jste explicitně poskytuje Int64 na `ActorId`, Int64 bude mapovat přímo do oddílu bez dalšího hašování. Tuto techniku můžete použít k řízení, které oddíly jsou umístěny v.


## <a name="next-steps"></a>Další kroky
* [Správa stavu actoru](service-fabric-reliable-actors-state-management.md)
* [Životní cyklus objektu actor a uvolňování paměti](service-fabric-reliable-actors-lifecycle.md)
* [Referenční dokumentace rozhraní API actors](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.actors?redirectedfrom=MSDN&view=azure-dotnet)
* [Ukázkový kód rozhraní .NET](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Ukázkový kód Javy](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png
