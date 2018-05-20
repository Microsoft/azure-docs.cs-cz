---
title: Správce prostředků clusteru Service Fabric - zásady umístění | Microsoft Docs
description: Přehled další umístění zásad a pravidel pro služby Service Fabric
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: 5c2d19c6-dd40-4c4b-abd3-5c5ec0abed38
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: bdf10f1d9199500d4f5bda3c5a05a3d67ff65541
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="placement-policies-for-service-fabric-services"></a>Umístění zásady pro služby service fabric
Umístění zásady jsou další pravidla, které lze použít k řízení umístění služby v některých scénářích konkrétní, méně běžné. Mezi příklady tyto scénáře patří:

- Cluster Service Fabric zahrnuje geografické vzdálenosti, například několik datových center v místě nebo mezi oblastmi Azure
- Vaše prostředí zahrnuje více oblastí řízení geopolitické nebo právních nebo některých jiných případu, kdy máte zásady potřebujete vynutit hranice
- Výkon nebo latenci rozhodnutí z důvodu dlouhé vzdálenosti nebo použití odkazů pomalejší nebo méně spolehlivé síťové komunikace
- Je třeba zachovat určité úlohy společně umístěná jako nejlepší úsilí, s ostatními úlohami, nebo v blízkosti zákazníkům

Většinu těchto požadavků zarovnané s fyzické rozložení clusteru, vyjádřené domén selhání clusteru. 

Zásady rozšířené umístění, které pomůžou adresy jsou tyto scénáře:

1. Neplatný domén
2. Požadované domén
3. Upřednostňovaných domén
4. Zakazuje se okolních repliky

Většina následující ovládacích prvků se daly konfigurovat prostřednictvím vlastnosti uzlu a omezení umístění, ale některé jsou složitější. Chcete-li věcí jednodušší, správce prostředků clusteru Service Fabric poskytuje tyto zásady další umístění. Konfigurace zásad umístění na základě služby za názvem instance. Se dá se taky aktualizovat dynamicky.

## <a name="specifying-invalid-domains"></a>Neplatné zadání domén
**InvalidDomain** umístění zásady umožňuje určit, že je neplatný pro konkrétní službu konkrétní domény selhání. Tato zásada zajišťuje, že konkrétní službu nikdy běží v určité oblasti, například důvodů geopolitické nebo podnikové zásady. Přes samostatné zásady je možné zadat více domén neplatný.

<center>
![Příklad domény je neplatný][Image1]
</center>

Kód:

```csharp
ServicePlacementInvalidDomainPolicyDescription invalidDomain = new ServicePlacementInvalidDomainPolicyDescription();
invalidDomain.DomainName = "fd:/DCEast"; //regulations prohibit this workload here
serviceDescription.PlacementPolicies.Add(invalidDomain);
```

Prostředí PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("InvalidDomain,fd:/DCEast”)
```
## <a name="specifying-required-domains"></a>Určení požadované domén
Zásady požadovanou doménu umístění vyžaduje, aby služba pouze v zadané doméně nenachází. Lze zadat více domén požadované přes samostatné zásady.

<center>
![Příklad požadovanou doménu][Image2]
</center>

Kód:

```csharp
ServicePlacementRequiredDomainPolicyDescription requiredDomain = new ServicePlacementRequiredDomainPolicyDescription();
requiredDomain.DomainName = "fd:/DC01/RK03/BL2";
serviceDescription.PlacementPolicies.Add(requiredDomain);
```

Prostředí PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomain,fd:/DC01/RK03/BL2")
```

## <a name="specifying-a-preferred-domain-for-the-primary-replicas-of-a-stateful-service"></a>Určení upřednostňované domény pro primární repliky stavové služby
Upřednostňovaný primární doménu Určuje domény selhání umístit primární v. Primární skončilo v této doméně při všechno, co je v pořádku. Pokud doméně nebo primární repliky selže nebo vypne, primární přesune do jiného umístění, nejlépe ve stejné doméně. Není-li toto nové umístění v upřednostňované doméně, správce prostředků clusteru přesouvá ji zpět do upřednostňovaných domény co nejdříve. Přirozeně toto nastavení má smysl jenom pro stavové služby. Tato zásada je velmi užitečné v clusterech, které jsou rozloženy oblastí Azure nebo několik datových center ale máte služby, které dávají přednost umístění v určitých umístění. Zachovat základní barvy blízko uživatelů nebo jiné služby pomáhá poskytovat nižší latenci, hlavně pro čtení, které jsou zpracovávány základní barvy ve výchozím nastavení.

<center>
![Upřednostňovaný primární domény a převzetí služeb při selhání][Image3]
</center>

```csharp
ServicePlacementPreferPrimaryDomainPolicyDescription primaryDomain = new ServicePlacementPreferPrimaryDomainPolicyDescription();
primaryDomain.DomainName = "fd:/EastUS/";
serviceDescription.PlacementPolicies.Add(invalidDomain);
```

Prostředí PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("PreferredPrimaryDomain,fd:/EastUS")
```

## <a name="requiring-replica-distribution-and-disallowing-packing"></a>Vyžadování distribučních repliky a zakazuje okolních
Repliky jsou _normálně_ distribuované napříč doménami selhání a upgradu, až bude cluster v pořádku. Existují však případy, kdy může se stát víc než jednu repliku pro daný oddíl dočasně sbalené do jedné domény. Předpokládejme například, že aby cluster má devět uzly v tři domén selhání, fd: / 0, fd: / 1 a fd: / 2. Dále předpokládejme, že vaše služba má tři repliky. Řekněme, že uzly, které byly používány pro tyto repliky v fd: / 1 a fd: / 2 byl vypnut. Obvykle správce prostředků clusteru přejete jiné uzly v těchto stejné domény selhání. V takovém případě Řekněme kvůli problémům s kapacitu, že žádný z jiných uzlů v těchto doménách nebyly platné. Pokud správce prostředků clusteru sestavení nahrazení pro tyto repliky, by mělo zvolte uzlů v fd: / 0. To však _,_ vytvoří situaci, kdy porušení omezení domény selhání. Balení zvyšuje repliky může pravděpodobnost, že nastavení celou repliku přejděte nebo ztratí. 

> [!NOTE]
> Další informace o omezení a omezení priority obecně platí, podívejte se na [v tomto tématu](service-fabric-cluster-resource-manager-management-integration.md#constraint-priorities).
>

Pokud někdy vidíte zprávu stavu, jako "`The Load Balancer has detected a Constraint Violation for this Replica:fabric:/<some service name> Secondary Partition <some partition ID> is violating the Constraint: FaultDomain`", pak jste stisknutí tlačítka tato podmínka nebo něco podobného jako ho. Obvykle pouze jednu nebo dvě repliky jsou společně zabaleny dočasně. Tak dlouho, dokud je méně než kvorum repliky v dané doméně, jste bezpečné. Okolních je taková situace vzácná, ale může se stát, a obvykle jsou tyto situace přechodný vzhledem k tomu, že uzly se vraťte. Pokud uzly zůstat dolů a správce prostředků clusteru je potřeba vytvořit náhrady, obvykle existuje jiné uzly v doménách selhání ideální.

Některé úlohy by raději vždy s cílovým počtem replik, i když jsou zabaleny do menšího počtu domén. Tyto úlohy jsou ale proti selhání celkový počet souběžných trvalé domény a obvykle můžete obnovit místní stavu. Další úlohy by starší než riziko správnost nebo ke ztrátě dat. místo trvat výpadek. Většina produkční úlohy spustit s víc než tři repliky, víc než třemi domén selhání a mnoho platný uzlů na domény selhání. Z tohoto důvodu se výchozí chování umožňuje okolních domény ve výchozím nastavení. Výchozí chování umožňuje normální vyrovnávání a převzetí služeb při selhání pro zpracování těchto výjimečných případech i v případě, že to znamená okolních dočasné domény.

Pokud chcete zakázat tyto obaly pro danou úlohu, můžete zadat `RequireDomainDistribution` zásady ve službě. Pokud je tato zásada nastavená, zajišťuje správce prostředků clusteru spustí žádné dvě repliky ze stejného oddílu ve stejné doméně selhání nebo upgradu.

Kód:

```csharp
ServicePlacementRequireDomainDistributionPolicyDescription distributeDomain = new ServicePlacementRequireDomainDistributionPolicyDescription();
serviceDescription.PlacementPolicies.Add(distributeDomain);
```

Prostředí PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomainDistribution")
```

Teď by bylo možné použít tyto konfigurace pro služby v clusteru, který nebyl předané geograficky? Vám může, ale není skvělé důvod příliš. Pokud je vyžadují scénáře je nutno konfigurace vyžaduje, neplatný a upřednostňované domény. Nemá smysl žádné pokusí vynutit daného zatížení spustit v jednom stojanu nebo raději některé segment místním clusteru oproti jinému. Různé hardwarové konfigurace by měl být rozloženy domén selhání a zpracovávají pomocí normální umístění omezení a vlastnosti uzlu.

## <a name="next-steps"></a>Další postup
- Další informace o konfiguraci služby [Další informace o konfiguraci služby](service-fabric-cluster-resource-manager-configure-services.md)

[Image1]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-invalid-placement-domain.png
[Image2]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-required-placement-domain.png
[Image3]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-preferred-primary-domain.png
