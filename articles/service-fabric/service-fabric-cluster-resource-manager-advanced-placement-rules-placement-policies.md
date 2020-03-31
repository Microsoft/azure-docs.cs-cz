---
title: Správce prostředků clusteru Service Fabric – zásady umístění
description: Přehled dalších zásad umístění a pravidel pro služby Service Fabric Services
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 9aea157d03f344e07a81f0588d3e0127f17ca75d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75834419"
---
# <a name="placement-policies-for-service-fabric-services"></a>Zásady umístění pro služby fabric
Zásady umístění jsou další pravidla, která lze použít k řízení umístění služby v některých konkrétních, méně běžných scénářích. Některé příklady těchto scénářů jsou:

- Cluster Service Fabric pokrývá geografické vzdálenosti, jako je například více místních datových center nebo napříč oblastmi Azure.
- Vaše prostředí zahrnuje více oblastí geopolitické nebo právní kontroly nebo v jiném případě, kdy máte hranice zásad, které je třeba vynutit.
- Vzhledem k velkým vzdálenostem nebo pomalejšímu nebo méně spolehlivému síťovému propojení mů e to aspekty výkonu komunikace nebo latence
- Je třeba, aby některé úlohy co by bylo nejlepší úsilí, a to buď s jinými úlohami, nebo v blízkosti zákazníků.

Většina těchto požadavků je v souladu s fyzickým rozložením clusteru, reprezentovaného jako domény selhání clusteru. 

Pokročilé zásady umístění, které pomáhají řešit tyto scénáře, jsou:

1. Neplatné domény
2. Požadované domény
3. Upřednostňované domény
4. Nepovolit balení replik

Většina následujících ovládacích prvků může být nakonfigurována prostřednictvím vlastností uzlu a omezení umístění, ale některé jsou složitější. Aby to bylo jednodušší, Správce prostředků clusteru Service Fabric poskytuje tyto další zásady umístění. Zásady umístění jsou konfigurovány na základě instance služby podle pojmenovaného. Mohou být také dynamicky aktualizovány.

## <a name="specifying-invalid-domains"></a>Určení neplatných domén
Zásady umístění **InvalidDomain** umožňují určit, že určitá doména selhání je pro určitou službu neplatná. Tato zásada zajišťuje, že určitá služba nikdy neběží v určité oblasti, například z důvodů geopolitické nebo podnikové politiky. Pomocí samostatných zásad může být zadáno více neplatných domén.

<center>

![Neplatný příklad domény][Image1]
</center>

Kód:

```csharp
ServicePlacementInvalidDomainPolicyDescription invalidDomain = new ServicePlacementInvalidDomainPolicyDescription();
invalidDomain.DomainName = "fd:/DCEast"; //regulations prohibit this workload here
serviceDescription.PlacementPolicies.Add(invalidDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("InvalidDomain,fd:/DCEast”)
```
## <a name="specifying-required-domains"></a>Určení požadovaných domén
Požadované zásady umístění domény vyžadují, aby služba byla k dispozici pouze v zadané doméně. Pomocí samostatných zásad lze zadat více požadovaných domén.

<center>

![Příklad požadované domény][Image2]
</center>

Kód:

```csharp
ServicePlacementRequiredDomainPolicyDescription requiredDomain = new ServicePlacementRequiredDomainPolicyDescription();
requiredDomain.DomainName = "fd:/DC01/RK03/BL2";
serviceDescription.PlacementPolicies.Add(requiredDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomain,fd:/DC01/RK03/BL2")
```

## <a name="specifying-a-preferred-domain-for-the-primary-replicas-of-a-stateful-service"></a>Určení upřednostňované domény pro primární repliky stavové služby
Upřednostňovaná primární doména určuje doménu selhání, do které má být primární. Primární skončí v této doméně, když je vše v pořádku. Pokud se doména nebo primární replika nezdaří nebo vypne, primární přesune do jiného umístění, v ideálním případě ve stejné doméně. Pokud toto nové umístění není v upřednostňované doméně, Správce prostředků clusteru přesune zpět do upřednostňované domény co nejdříve. Samozřejmě toto nastavení má smysl pouze pro stavové služby. Tato zásada je nejužitečnější v clusterech, které jsou rozloženy napříč oblastmi Azure nebo více datových center, ale mají služby, které upřednostňují umístění v určitém umístění. Udržování primárek v blízkosti jejich uživatelů nebo jiných služeb pomáhá poskytovat nižší latenci, zejména pro čtení, které jsou ve výchozím nastavení zpracovány primárky.

<center>

![Upřednostňované primární domény a převzetí služeb při selhání][Image3]
</center>

```csharp
ServicePlacementPreferPrimaryDomainPolicyDescription primaryDomain = new ServicePlacementPreferPrimaryDomainPolicyDescription();
primaryDomain.DomainName = "fd:/EastUS/";
serviceDescription.PlacementPolicies.Add(primaryDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("PreferredPrimaryDomain,fd:/EastUS")
```

## <a name="requiring-replica-distribution-and-disallowing-packing"></a>Vyžadování distribuce replik a nepovolení balení
Repliky jsou _obvykle_ distribuovány mezi doménami selhání a upgradu, když je cluster v pořádku. Existují však případy, kdy více než jedna replika pro daný oddíl může skončit dočasně zabalena do jedné domény. Řekněme například, že cluster má devět uzlů ve třech doménách selhání, fd:/0, fd:/1 a fd:/2. Řekněme také, že vaše služba má tři repliky. Řekněme, že uzly, které byly používány pro tyto repliky v fd:/1 a fd:/2 šel dolů. Správce prostředků clusteru by normálně upřednostňoval jiné uzly ve stejných doménách selhání. V tomto případě řekněme z důvodu problémů s kapacitou žádný z ostatních uzlů v těchto doménách nebyly platné. Pokud Správce prostředků clusteru vytvoří náhrady za tyto repliky, bude muset zvolit uzly v fd:/0. _To_ však vytvoří situaci, kdy je porušeno omezení domény selhání. Balení replik zvyšuje pravděpodobnost, že celá sada replik může přejít dolů nebo být ztraceny. 

> [!NOTE]
> Další informace o omezeních a prioritách omezení obecně naleznete v [tomto tématu](service-fabric-cluster-resource-manager-management-integration.md#constraint-priorities).
>

Pokud jste někdy viděli zdravotní zprávu,`The Load Balancer has detected a Constraint Violation for this Replica:fabric:/<some service name> Secondary Partition <some partition ID> is violating the Constraint: FaultDomain`jako je " ", pak jste narazili na tento stav nebo něco podobného. Obvykle pouze jedna nebo dvě repliky jsou zabaleny dohromady dočasně. Pokud je v dané doméně méně než kvorum replik, jste v bezpečí. Balení je vzácné, ale může se to stát a obvykle jsou tyto situace přechodné, protože uzly se vrátí. Pokud uzly zůstanou nepřízni a Správce prostředků clusteru potřebuje vytvořit náhrady, obvykle jsou v ideálních doménách selhání k dispozici další uzly.

Některé úlohy by raději vždy s cílový počet replik, i v případě, že jsou zabaleny do méně domén. Tyto úlohy sázejí proti celkovému souběžnému selhání trvalé domény a obvykle mohou obnovit místní stav. Jiné úlohy by raději vzít prostoje dříve než riziko správnost nebo ztráta dat. Většina produkčních úloh běží s více než třemi replikami, více než třemi doménami selhání a mnoha platnými uzly na doménu selhání. Z tohoto důvodu výchozí chování umožňuje balení domény ve výchozím nastavení. Výchozí chování umožňuje normální vyrovnávání a převzetí služeb při selhání pro zpracování těchto extrémních případů, i když to znamená dočasné balení domény.

Pokud chcete zakázat takové balení pro dané zatížení, můžete zadat `RequireDomainDistribution` zásady ve službě. Pokud je tato zásada nastavena, Správce prostředků clusteru zajistí, že nebudou spuštěny žádné dvě repliky ze stejného oddílu ve stejné doméně selhání nebo upgradu.

Kód:

```csharp
ServicePlacementRequireDomainDistributionPolicyDescription distributeDomain = new ServicePlacementRequireDomainDistributionPolicyDescription();
serviceDescription.PlacementPolicies.Add(distributeDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomainDistribution")
```

Bylo by nyní možné použít tyto konfigurace pro služby v clusteru, který nebyl geograficky rozložen? Mohl bys, ale taky není dobrý důvod. Požadované, neplatné a upřednostňované konfigurace domény je třeba se vyhnout, pokud scénáře vyžadují. Nemá smysl snažit se vynutit spuštění dané úlohy v jednom racku nebo upřednostňovat některé segmenty místního clusteru před jiným. Různé konfigurace hardwaru by měly být rozloženy mezi domény selhání a zpracovány pomocí omezení normálního umístění a vlastností uzlů.

## <a name="next-steps"></a>Další kroky
- Další informace o konfiguraci služeb [naleznete v informacích o konfiguraci služeb](service-fabric-cluster-resource-manager-configure-services.md)

[Image1]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-invalid-placement-domain.png
[Image2]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-required-placement-domain.png
[Image3]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-preferred-primary-domain.png
