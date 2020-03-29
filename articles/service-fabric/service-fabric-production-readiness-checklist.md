---
title: Kontrolní seznam připravenosti infrastruktury azure service fabric
description: Připravte si aplikaci Service Fabric a produkční prostředí clusteru podle doporučených postupů.
ms.topic: conceptual
ms.date: 6/05/2019
ms.openlocfilehash: 90d600b01aa870f7b3a58e70ef32e774e7107524
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75376796"
---
# <a name="production-readiness-checklist"></a>Kontrolní seznam připravenosti k produkci

Je vaše aplikace a cluster připraven k převzetí produkčního provozu? Spuštění a testování aplikace a clusteru nemusí nutně znamenat, že je připraven a přejde do produkčního prostředí. Udržujte aplikaci a cluster běží hladce procházením následující kontrolní seznam. Důrazně doporučujeme, aby všechny tyto položky byly odškrtnuty. Je zřejmé, že můžete použít alternativní řešení pro konkrétní řádkovou položku (například vlastní diagnostické rámce).


## <a name="prerequisites-for-production"></a>Předpoklady pro výrobu
1. Osvědčené postupy Azure Service Fabric: [Návrh aplikace](./service-fabric-best-practices-applications.md), [zabezpečení](./service-fabric-best-practices-security.md), [sítě](./service-fabric-best-practices-networking.md), [plánování kapacity a škálování](./service-fabric-best-practices-capacity-scaling.md), [infrastruktura jako kód](./service-fabric-best-practices-infrastructure-as-code.md)a monitorování a [diagnostika](./service-fabric-best-practices-monitoring.md). 
1. Implementujte konfiguraci zabezpečení Reliable Actors, pokud používáte programovací model Actors
1. Pro clustery s více než 20 jádry nebo 10 uzly vytvořte vyhrazený typ primárního uzlu pro systémové služby. Přidáním [omezení umístění vyhradíte](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md) typ primárního uzlu pro systémové služby.
1. Pro primární typ uzlu použijte skladovou položku D2v2 nebo vyšší. Doporučujeme vybrat skladovou položku s kapacitou pevného disku alespoň 50 GB.
1. Produkční clustery musí být [zabezpečené](service-fabric-cluster-security.md). Příklad nastavení zabezpečeného clusteru naleznete v této [šabloně clusteru](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/7-VM-Windows-3-NodeTypes-Secure-NSG). Používejte běžné názvy certifikátů a vyhněte se použití certifikátů podepsaných svým držitelem.
1. Přidejte [omezení prostředků na kontejnery a služby](service-fabric-resource-governance.md), aby nespotřebovávaly více než 75 % prostředků uzlu. 
1. Pochopte a nastavte [úroveň trvanlivosti](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster). Stříbrná nebo vyšší úroveň odolnosti se doporučuje pro typy uzlů se stavové úlohy. Typ primárního uzlu by měl mít úroveň odolnosti nastavenou na stříbrnou nebo vyšší.
1. Pochopit a vybrat [úroveň spolehlivosti](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) typu uzlu. Doporučuje se stříbrná nebo vyšší spolehlivost.
1. Načtení a škálování otestujte své úlohy k identifikaci [požadavků na kapacitu](service-fabric-cluster-capacity.md) pro váš cluster. 
1. Vaše služby a aplikace jsou monitorovány a protokoly aplikací jsou generovány a uloženy s výstrahami. Například najdete v [tématu přidání protokolování do aplikace Service Fabric](service-fabric-how-to-diagnostics-log.md) a monitorování [kontejnerů s protokoly Azure Monitor](service-fabric-diagnostics-oms-containers.md).
1. Cluster je monitorován pomocí výstrah (například [protokoly Azure Monitoru).](service-fabric-diagnostics-event-analysis-oms.md) 
1. Základní infrastruktura škálovací sady virtuálních strojů se monitoruje pomocí výstrah (například [protokoly Azure Monitoru](service-fabric-diagnostics-oms-agent.md).
1. Cluster má [primární a sekundární certifikáty](service-fabric-cluster-security-update-certs-azure.md) vždy (takže se nedostanete uzamčeni).
1. Udržujte samostatné clustery pro vývoj, přípravu a výrobu. 
1. [Upgrady aplikací](service-fabric-application-upgrade.md) a [upgrady clusteru](service-fabric-tutorial-upgrade-cluster.md) jsou nejprve testovány ve vývojových a přípravných clusterech. 
1. Vypněte automatické upgrady v produkčních clusterech a zapněte je pro vývoj a pracovní clustery (vrácení zpět podle potřeby). 
1. Vytvořte cíl bodu obnovení (RPO) pro vaši službu a nastavte [proces zotavení po havárii](service-fabric-disaster-recovery.md) a vyzkoušejte ho.
1. [Naplánujte škálování clusteru](service-fabric-cluster-scaling.md) ručně nebo programově.
1. Naplánujte [opravy](service-fabric-patch-orchestration-application.md) uzlů clusteru. 
1. Vytvořte kanál CI/CD tak, aby vaše nejnovější změny byly průběžně testovány. Například pomocí [Azure DevOps](service-fabric-tutorial-deploy-app-with-cicd-vsts.md) nebo [Jenkins](service-fabric-cicd-your-linux-applications-with-jenkins.md)
1. Otestujte vývoj & pracovní clustery pod zatížením pomocí [služby Fault Analysis Service](service-fabric-testability-overview.md) a vyvolat řízený [chaos](service-fabric-controlled-chaos.md). 
1. Plán pro [škálování](service-fabric-concepts-scalability.md) aplikací. 


Pokud používáte service fabric spolehlivé služby nebo spolehlivé actors programovací model, je třeba odškrtnout následující položky:
1. Upgrade aplikací během místního vývoje zkontrolovat, že váš kód `RunAsync` služby je ctít zrušení token v metodě a zavření vlastní naslouchací procesy komunikace.
1. Vyhněte se [běžným nástrahám](service-fabric-work-with-reliable-collections.md) při používání spolehlivých kolekcí.
1. Monitorujte čítače výkonu paměti .NET CLR při spuštění zátěžových testů a zkontrolujte vysokou míru růstu haldy nebo haldy runaway.
1. Udržujte offline zálohování [spolehlivých služeb a spolehlivých aktérů](service-fabric-reliable-services-backup-restore.md) a otestujte proces obnovení.
1. Počet instancí primárního typu nodetype virtuálního počítače by se v ideálním případě měl rovnat minimu pro úroveň spolehlivosti clusterů. podmínky, pokud je to vhodné k překročení minimální úrovně zahrnuje: dočasně při vertikálníškálování primární chod typy virtuálních počítačů škálovací sady skladových položk.

## <a name="optional-best-practices"></a>Volitelné osvědčené postupy

Zatímco výše uvedené seznamy jsou předpokladem pro vstup do výroby, je třeba vzít v úvahu také následující položky:
1. Připojte se k [modelu stavu Service Fabric](service-fabric-health-introduction.md) pro rozšíření integrované hodnocení stavu a vykazování.
1. Nasaďte vlastní sledovací zařízení, které monitoruje vaši aplikaci a [načte](service-fabric-cluster-resource-manager-metrics.md) se pro [vyrovnávání prostředků](service-fabric-cluster-resource-manager-balancing.md). 


## <a name="next-steps"></a>Další kroky
* [Nasazení clusteru Windows fabric služby](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* [Nasazení clusteru Service Fabric Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
* Informace o [životním cyklu aplikace](service-fabric-application-lifecycle.md) Service Fabric.
