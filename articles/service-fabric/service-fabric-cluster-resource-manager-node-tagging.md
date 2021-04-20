---
title: Značky dynamického uzlu Azure Service Fabric
description: Azure Service Fabric umožňuje dynamicky přidávat a odebírat značky uzlů.
author: yu-supersonic
ms.topic: conceptual
ms.date: 04/05/2021
ms.author: branim
ms.custom: devx-track-csharp
ms.openlocfilehash: 712e6422060619e5567a74d6335320eff9ed8e66
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741639"
---
# <a name="introduction-to-dynamic-node-tags"></a>Úvod do značek dynamických uzlů
Značky uzlů umožňují dynamicky přidávat a odebírat značky z uzlů, aby bylo možné ovlivnit umístění služeb. Označování uzlů je velmi flexibilní a umožňuje změny v umístění služby bez upgradování aplikací nebo clusterů. Značky lze v uzlech kdykoli přidat nebo odebrat a služby mohou určit požadavky na určité značky při jejich vytvoření. Služba může také mít své požadavky na značku dynamicky aktualizované, když běží.

Označování uzlů se podobá [omezením umístění](service-fabric-cluster-resource-manager-configure-services.md) a obvykle se používá k řízení toho, na kterých uzlech služba běží. Každou službu Service Fabric je možné nakonfigurovat tak, aby vyžadovala umístění nebo zachování značky.

Označování uzlů je podporované pro všechny Service Fabric typy hostovaných služeb (Reliable Services, spustitelné soubory hosta a kontejnery). Chcete-li použít označování uzlů, musíte spustit verzi 8,0 nebo vyšší Service Fabric modulu runtime.

Zbývající část tohoto článku popisuje způsoby, jak povolit nebo zakázat označování uzlů a obsahuje příklady použití této funkce.


## <a name="describing-dynamic-node-tags"></a>Popis značek dynamických uzlů
Služby mohou určovat značky, které vyžadují. Existují dva typy značek:
* **Tagy vyžadované pro umístění** popisují sadu značek, které se vyžadují jenom pro umístění služby. Po umístění repliky lze tyto značky odebrat bez přerušení služby. Pokud se některá z těchto značek odebere z uzlu, replika služby zůstane funkční a Service Fabric službu neodebere.

* Značky, které jsou **nutné ke spuštění** , popisují sadu značek, které jsou požadovány pro umístění i chod služby. Pokud se odebere kterákoli z požadovaných značek, Service Fabric přesune službu na jiný uzel, který má tyto značky zadány.

Příklad: vyžadováno pro označování značek umístění lze využít při použití určitého druhu služby pro zařazování kontejnerů a potřebujete, aby byla služba pro váš kontejner umístěna, a jakmile bude kontejner aktivován, již není nutné aktivovat, a můžete také odebrat značku, která je k ní přidružená, ale kontejner by měl zůstat spuštěný.
Vyžaduje se ke spuštění značek, pokud máte fakturační službu, která je užitečná pro společně umístěného s uživatelem orientovanými službami. Pokud služba fakturace v uzlu selhává, odeberete značku, která je k ní přidružená, a uživatelskou službu přesunete do jiného uzlu, který má fakturační službu a její značku, která je k dispozici.

Značku nebo sadu značek lze přidat, aktualizovat nebo odebrat z jednoho uzlu pomocí mechanismů standardního Service Fabric rozhraní, jako jsou rozhraní API jazyka C#, rozhraní REST API nebo příkazy prostředí PowerShell.

> [!NOTE]
> Service Fabric neudržuje distribuce UD/FD při použití značek uzlů. Spravujte značky uzlů vhodně, takže nebudete mít k dispozici porušení FD/UD z důvodu špatné distribuce značek napříč doménami.

## <a name="enabling-dynamic-node-tags"></a>Povolení značek dynamických uzlů
Aby tato funkce fungovala, budete muset povolit NodeTaggingEnabled config v oddílu PlacementAndLoadBalancing manifestu clusteru buď pomocí XML, nebo JSON:

``` xml
<Section Name="PlacementAndLoadBalancing">
     <Parameter Name="NodeTaggingEnabled" Value="true" />
</Section>
```

prostřednictvím ClusterConfig.jsv pro samostatná nasazení nebo Template.jsv případě hostovaných clusterů Azure:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": " NodeTaggingEnabled ",
          "value": "true"
      }
    ]
  }
]
```

## <a name="setting-dynamic-node-tags"></a>Nastavení značek dynamických uzlů

### <a name="using-powershell"></a>Pomocí prostředí PowerShell

Přidávání značek uzlů do uzlu:

```posh
Add-ServiceFabricNodeTags -NodeName "DB.1" -NodeTags @("SampleTag1", "SampleTag2")
```
Tento příkaz přidá značky "SampleTag1" a "SampleTag2" v Node DB. 1.

Odebrat značky uzlů z uzlu:

```posh
Remove-ServiceFabricNodeTags -NodeName "DB.1" -NodeTags @("SampleTag1", "SampleTag2")
```
Tento příkaz odstraní značky "SampleTag1" a "SampleTag2" v Node DB. 1.

### <a name="using-c-apis"></a>Použití rozhraní API jazyka C#

Přidávání značek uzlů do uzlu:

```csharp
FabricClient fabricClient = new FabricClient();
List<string> nodeTagsList = new List<string>();
nodeTagsList.Add("SampleTag1");
nodeTagsList.Add("SampleTag2");
await fabricClient.ClusterManager.AddNodeTagsAsync("DB.1", nodeTagsList);
```

Odebrat značky uzlů z uzlu:

```csharp
FabricClient fabricClient = new FabricClient();
List<string> nodeTagsList = new List<string>();
nodeTagsList.Add("SampleTag1");
nodeTagsList.Add("SampleTag2");
await fabricClient.ClusterManager.RemoveNodeTagsAsync("DB.1", nodeTagsList);
```

## <a name="setting-required-tags-for-services"></a>Nastavení požadovaných značek pro služby

### <a name="using-powershell"></a>Pomocí prostředí PowerShell

Vytváří se nová služba:

```posh
New-ServiceFabricService -ApplicationName fabric:/HelloWorld -ServiceName fabric:/HelloWorld/svc1 -ServiceTypeName HelloWorldStateful -Stateful -PartitionSchemeSingleton -TargetReplicaSetSize 5 -MinReplicaSetSize 3 -TagsRequiredToRun @("SampleTag1") - TagsRequiredToPlace @("SampleTag2")
```
Tento příkaz vytvoří službu, která vyžaduje, aby se na uzlu nacházelo "SampleTag2", aby se služba umístila do umístění a "SampleTag1", která bude k dispozici, aby mohla služba v tomto uzlu dál běžet.

Aktualizuje se stávající služba:

```posh
Update-ServiceFabricService -Stateful -ServiceName fabric:/myapp/test -TagsRequiredToRun @("SampleTag1") -TagsRequiredToPlace @("SampleTag2")
```
Tento příkaz aktualizuje službu, která vyžaduje, aby se v uzlu nacházela položka SampleTag2, aby mohla být služba umístěna, a k dispozici je "SampleTag1", aby mohla služba v tomto uzlu dále běžet.

### <a name="using-c-apis"></a>Použití rozhraní API jazyka C#

Vytváří se nová služba:

```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
//set up the rest of the ServiceDescription
ServiceTags serviceTags = new ServiceTags();
serviceTags.TagsRequiredToPlace.Add("SampleTag1");
serviceTags.TagsRequiredToRun.Add("SampleTag2");
serviceDescription.ServiceTags = serviceTags;
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

Aktualizuje se stávající služba:

```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceUpdateDescription serviceUpdate = new StatefulServiceUpdateDescription();
ServiceTags serviceTags = new ServiceTags();
serviceTags.TagsRequiredToPlace.Add("SampleTag1");
serviceTags.TagsRequiredToRun.Add("SampleTag2");
serviceUpdate.ServiceTags = serviceTags;
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/AppName/ServiceName"), serviceUpdate);
```

Všechny tyto příkazy platí stejně jako bezstavové služby.

## <a name="next-steps"></a>Další kroky
Další informace o [omezení umístění](service-fabric-cluster-resource-manager-configure-services.md)
