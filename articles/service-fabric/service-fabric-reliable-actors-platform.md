---
title: Reliable Actors v Service Fabric
description: V této části najdete popis způsobu vrstvení Reliable Actors Reliable Services a použití funkcí platformy Service Fabric.
ms.topic: conceptual
ms.date: 3/9/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: e204b47b7fa9f528341507c315c926159d524e13
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98789578"
---
# <a name="how-reliable-actors-use-the-service-fabric-platform"></a>Jak Reliable Actors používat platformu Service Fabric
Tento článek vysvětluje, jak Reliable Actors pracovat na platformě Azure Service Fabric. Reliable Actors běžet v rámci architektury hostované v implementaci stavové spolehlivé služby nazývané *služby objektu actor*. Služba objektu actor obsahuje všechny součásti potřebné ke správě životního cyklu a odeslání zpráv pro vaše objekty actor:

* Modul runtime objektu actor spravuje životní cyklus, uvolňování paměti a vynutil přístup s jedním vláknem.
* Naslouchací proces vzdálené služby objektu actor přijímá volání objektů actor vzdáleného přístupu a odesílá je do dispečera ke směrování na příslušnou instanci objektu actor.
* Poskytovatel stavu objektu actor obtéká zprostředkovatele stavu (například zprostředkovatele stavu spolehlivých kolekcí) a poskytuje adaptér pro správu stavu objektu actor.

Tyto komponenty dohromady tvoří architekturu Reliable actor.

## <a name="service-layering"></a>Vrstvení služeb
Vzhledem k tomu, že samotná služba actor je spolehlivou službou, je nutné použít pro služby objektu actor stejný způsob, jakým je [model aplikace](service-fabric-application-model.md), životní cyklus, [balení](service-fabric-package-apps.md), [nasazení](service-fabric-deploy-remove-applications.md), upgrade a škálování konceptu Reliable Services stejný.

![Vrstvení služby objektu actor][1]

Předchozí diagram znázorňuje vztah mezi Service Fabric aplikačními architekturami a uživatelským kódem. Modré prvky představují Reliable Services aplikační Framework, oranžová představuje architekturu Reliable actor a zelená představuje uživatelský kód.

V Reliable Services vaše služba dědí `StatefulService` třídu. Tato třída je sama o sobě odvozena z `StatefulServiceBase` (nebo `StatelessService` pro bezstavové služby). V Reliable Actors používáte službu objektu actor. Služba objektu actor je odlišná implementace `StatefulServiceBase` třídy, která implementuje vzorek objektu actor, ve kterém se vaše objekty actor spouštějí. Vzhledem k tomu, že samotné služby objektu actor jsou pouze implementací `StatefulServiceBase` , můžete napsat vlastní službu, která je odvozena z `ActorService` a implementovat funkce na úrovni služby stejným způsobem jako při dědění `StatefulService` , jako například:

* Zálohování a obnovení služby.
* Sdílená funkce všech objektů Actor, například přerušení okruhů.
* Vzdálená volání procedur přímo na samotné službě actor a na každý jednotlivec actor.

Další informace najdete v tématu [implementace funkcí na úrovni služby ve službě objektu actor](service-fabric-reliable-actors-using.md).

## <a name="application-model"></a>Aplikační model
Služby objektu actor jsou Reliable Services, takže model aplikace je stejný. Nástroje pro sestavení rozhraní actor však generují některé soubory modelu aplikace za vás.

### <a name="service-manifest"></a>Manifest služby
Nástroje pro sestavování rozhraní actor automaticky generují obsah souboru ServiceManifest.xml služby objektu actor. Tento soubor obsahuje:

* Typ služby objektu actor Název typu se generuje na základě názvu projektu vašeho objektu actor. V závislosti na atributu trvalosti v objektu actor je příznak HasPersistedState také nastaven odpovídajícím způsobem.
* Balíček kódu.
* Konfigurační balíček.
* Prostředky a koncové body.

### <a name="application-manifest"></a>Manifest aplikace
Nástroje pro sestavování rozhraní actor automaticky vytvoří výchozí definici služby pro vaši službu objektu actor. Nástroje sestavení naplní výchozí vlastnosti služby:

* Počet sad replik je určen atributem trvalosti objektu actor. Pokaždé, když se změní atribut trvalosti objektu actor, obnoví se odpovídající počet sad replik v definici výchozí služby.
* Schéma oddílu a rozsah jsou nastaveny na hodnotu Uniform Int64 s plným rozsahem klíčů Int64.

## <a name="service-fabric-partition-concepts-for-actors"></a>Koncepty Service Fabric oddílů pro objekty actor
Služby objektu actor jsou rozdělené stavové služby. Každý oddíl služby objektu actor obsahuje sadu objektů actor. Oddíly služby jsou automaticky distribuovány na více uzlů v Service Fabric. Instance objektu actor jsou distribuovány jako výsledek.

![Dělení a distribuce objektu actor][5]

Reliable Services lze vytvořit s různými schématy oddílů a rozsahy klíčů oddílů. Služba actor používá schéma dělení Int64 s plným rozsahem klíčů Int64 pro mapování objektů actor na oddíly.

### <a name="actor-id"></a>ID objektu actor
Každý objekt actor, který je vytvořen ve službě, má přiřazen jedinečný identifikátor, který je reprezentován `ActorId` třídou. `ActorId` je neprůhledná hodnota ID, která se dá použít pro jednotnou distribuci aktérů napříč oddíly služby vygenerováním náhodných ID:

```csharp
ActorProxy.Create<IMyActor>(ActorId.CreateRandom());
```
```Java
ActorProxyBase.create<MyActor>(MyActor.class, ActorId.newId());
```


Každé `ActorId` je hodnota hash na hodnotu Int64. Důvodem je, že služba objektu actor musí používat schéma dělení na instance Int64 s plným rozsahem klíčů Int64. Vlastní hodnoty ID však lze použít pro `ActorID` , včetně identifikátorů GUID/UUID, řetězců a Int64.

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

Pokud používáte identifikátory GUID/UUID a řetězce, hodnoty jsou pro hodnotu Int64 hash. Pokud ale výslovně zadáte hodnotu typu Int64 k objektu `ActorId` , bude hodnota Int64 namapována přímo na oddíl bez dalšího hashování. Tento postup můžete použít k určení oddílu, ve kterém jsou objekty actor umístěny.


## <a name="next-steps"></a>Další kroky
* [Správa stavu objektu actor](service-fabric-reliable-actors-state-management.md)
* [Životní cyklus objektu actor a uvolňování paměti](service-fabric-reliable-actors-lifecycle.md)
* [Referenční dokumentace k rozhraní actor API](/dotnet/api/microsoft.servicefabric.actors)
* [Vzorový kód .NET](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Vzorový kód Java](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png
