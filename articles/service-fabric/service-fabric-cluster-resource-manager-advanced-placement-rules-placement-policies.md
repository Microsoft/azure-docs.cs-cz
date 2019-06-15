---
title: Service Fabric Cluster Resource Manager – zásady umístění | Dokumentace Microsoftu
description: Přehled další umístění zásad a pravidel pro služby Service Fabric
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: ''
ms.assetid: 5c2d19c6-dd40-4c4b-abd3-5c5ec0abed38
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: d5aea441f15cbf7a2a444439c06cd5f74a559d3f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60386422"
---
# <a name="placement-policies-for-service-fabric-services"></a>Zásady umístění služeb service fabric
Zásady umístění jsou další pravidla, které lze použít k řízení umístění služby v některé konkrétní, méně běžné scénáře. Mezi příklady tyto scénáře patří:

- Cluster Service Fabric zahrnuje geografické vzdálenosti, jako je například více místních datových centrech nebo mezi oblastmi Azure
- Vaše prostředí zahrnuje několik oblastí geopolitických nebo právní ovládacího prvku nebo některé ostatní případy, kdy máte zásady budete muset vynutit hranice
- Některé aspekty výkonu nebo latence komunikace z důvodu dlouhé vzdálenosti nebo použití nižší nebo méně spolehlivé síťová propojení
- Je potřeba nechat určité úlohy společně umístěná v podobě nezaručené, buď s ostatními úlohami nebo v blízkosti zákazníkům

Většina tyto požadavky odpovídaly fyzické rozložení clusteru, vyjádřené domén selhání clusteru. 

Zásady rozšířené umístění, které řešení jsou tyto scénáře:

1. Neplatný domén
2. Požadované domén
3. Upřednostňované domén
4. Zákaz repliky balení

Většina následující ovládací prvky může být konfigurováno prostřednictvím vlastnosti uzlu a omezení umístění, ale některé jsou složitější. Aby to bylo jednodušší, Service Fabric Cluster Resource Manager poskytuje tyto zásady další umístění. Zásady umístění se konfigurují na základě služby za názvem instance. Dá se taky aktualizovat dynamicky.

## <a name="specifying-invalid-domains"></a>Neplatné zadání domén
**InvalidDomain** umístění zásad umožňuje zadat, že konkrétní doména selhání je neplatný pro konkrétní službu. Tato zásada zajistí, že konkrétní službu nikdy nespustí v konkrétní oblasti, například kvůli geopolitických nebo firemní zásady. Přes samostatné zásady je možné zadat více domén neplatný.

<center>

![Příklad domény je neplatný][Image1]
</center>

Kód:

```csharp
ServicePlacementInvalidDomainPolicyDescription invalidDomain = new ServicePlacementInvalidDomainPolicyDescription();
invalidDomain.DomainName = "fd:/DCEast"; //regulations prohibit this workload here
serviceDescription.PlacementPolicies.Add(invalidDomain);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("InvalidDomain,fd:/DCEast”)
```
## <a name="specifying-required-domains"></a>Určení požadovaných domén
Zásady umístění vyžaduje domény vyžaduje, že služba je k dispozici pouze v zadané doméně. Přes samostatné zásady je možné zadat několik požadovaných domén.

<center>

![Příklad požadovanou doménu][Image2]
</center>

Kód:

```csharp
ServicePlacementRequiredDomainPolicyDescription requiredDomain = new ServicePlacementRequiredDomainPolicyDescription();
requiredDomain.DomainName = "fd:/DC01/RK03/BL2";
serviceDescription.PlacementPolicies.Add(requiredDomain);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomain,fd:/DC01/RK03/BL2")
```

## <a name="specifying-a-preferred-domain-for-the-primary-replicas-of-a-stateful-service"></a>Určení upřednostňovaného domény pro primární repliky stavové služby
Primární doména upřednostňované určuje doména selhání umístit primární v. Primární skončilo v této doméně při všechno, co je v pořádku. Pokud domény nebo je primární replika selže nebo vypne, primární přesune do jiného umístění, v ideálním případě ve stejné doméně. Není-li tohoto nového umístění v doméně, upřednostňovanou, Cluster Resource Manager přesouvá ji zpět do upřednostňované doménu co nejdříve. Toto nastavení je přirozeně pouze dává smysl pro stavové služby. Tato zásada je nejužitečnější v clusterech, které jsou rozložené mezi oblastmi Azure nebo několik datových center, ale na nich služby, které dáváte přednost umístění na určité místo. Udržování Primárek blízko uživatelům nebo jiné služby, pomáhá zajistit nižší latenci, zejména pro čtení, které jsou zpracovány Primárek ve výchozím nastavení.

<center>

![Upřednostňované primární domény a převzetí služeb při selhání][Image3]
</center>

```csharp
ServicePlacementPreferPrimaryDomainPolicyDescription primaryDomain = new ServicePlacementPreferPrimaryDomainPolicyDescription();
primaryDomain.DomainName = "fd:/EastUS/";
serviceDescription.PlacementPolicies.Add(primaryDomain);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("PreferredPrimaryDomain,fd:/EastUS")
```

## <a name="requiring-replica-distribution-and-disallowing-packing"></a>Vyžadování distribučního repliky a Probíhá balení
Repliky jsou _obvykle_ distribuovány napříč chybových nebo upgradovacích doménách v případě, že cluster je v pořádku. Existují však případy, ve kterém je více než jednu repliku pro daný oddíl uvíznout dočasně komprimovat komprimovaný objekt do jedné domény. Například Řekněme, že má cluster devíti uzlů v tři domény selhání, fd: / 0, fd: / 1 a fd: / 2. Také Předpokládejme, že vaše služba má tři repliky. Řekněme, že uzly, které byly používány pro tyto repliky ve fd: / 1 a fd: / 2 byl vypnut. Cluster Resource Manageru obvykle raději jiných uzlů v těchto stejných doménách selhání. V takovém případě Řekněme kvůli potíže s kapacitou, že žádný z jiných uzlů v těchto doménách nebyly platné. Pokud Cluster Resource Manager vytvoří nahrazení pro tyto repliky, ho bude potřeba, abyste si uzly ve fd: / 0. Nicméně to _, který_ vytvoří situace, kde je porušena omezení doména selhání. Balení zvýšení replik může pravděpodobnost, že celý replik vypne nebo ztratí. 

> [!NOTE]
> Další informace o omezení a omezení priority obecně platí, projděte si [v tomto tématu](service-fabric-cluster-resource-manager-management-integration.md#constraint-priorities).
>

Pokud jste někdy viděli stavu zprávy, jako "`The Load Balancer has detected a Constraint Violation for this Replica:fabric:/<some service name> Secondary Partition <some partition ID> is violating the Constraint: FaultDomain`", pak jste přístupů k tomuto stavu nebo něco líbilo. Obvykle pouze jednu nebo dvě repliky jsou zkomprimována společně dočasně. Tak dlouho, dokud jsou kratší než kvorum replik v dané doméně jste bezpečné. Balení je taková situace vzácná, ale může stát, a těchto situacích jsou obvykle přechodné, neboť uzly se vraťte. Uzly zůstat dolů a Cluster Resource Manager je potřeba vytvořit nahrazení, obvykle existuje další uzly v doménách selhání ideální.

Některé úlohy přejete vždy s cílovým počtem replik, i když jsou zkomprimována do menšího počtu domén. Tyto úlohy jsou sází na celkový počet souběžných trvalých domény selhání a obvykle můžete obnovit místní stavu. Další úlohy by starší než riziko správnosti nebo ke ztrátě dat. spíše trvat výpadek. Většinu provozních úloh spustit s víc než tři repliky, více než tři domény selhání a mnoha uzly platnou doménu selhání. Z tohoto důvodu výchozí chování umožňuje komprimaci domény ve výchozím nastavení. Výchozí chování umožňuje normální vyrovnávání a převzetí služeb při selhání pro zpracování těchto extrémních případech i v případě, že to znamená, že komprimaci dočasných domény.

Pokud chcete zakázat tyto balení pro konkrétní úlohu, můžete zadat `RequireDomainDistribution` zásady ve službě. Pokud je nastavena tato zásada, Cluster Resource Manager zajišťuje že spustit žádné dvě repliky ze stejného oddílu ve stejné doméně selhání nebo upgradu.

Kód:

```csharp
ServicePlacementRequireDomainDistributionPolicyDescription distributeDomain = new ServicePlacementRequireDomainDistributionPolicyDescription();
serviceDescription.PlacementPolicies.Add(distributeDomain);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomainDistribution")
```

Teď by bylo možné použít tyto konfigurace pro služby v clusteru, který nebyl geograficky rozložených? Vám může, ale není k dispozici skvělý důvod, proč příliš. Konfigurace domén povinné, neplatné a upřednostňovaný mělo by se vyhnout, pokud scénáře, které je vyžadují. Nemá smysl jakékoli pokusí vynutit konkrétní úlohu pro spuštění v jedné stojanu nebo upřednostňovat některé segmentu místní cluster před jiným. Jiné hardwarové konfigurace by měl rozložit napříč doménami selhání a zpracování prostřednictvím vlastnosti uzlu a omezení normální umístění.

## <a name="next-steps"></a>Další postup
- Další informace o konfiguraci služby [informace o konfiguraci služby](service-fabric-cluster-resource-manager-configure-services.md)

[Image1]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-invalid-placement-domain.png
[Image2]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-required-placement-domain.png
[Image3]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-preferred-primary-domain.png
