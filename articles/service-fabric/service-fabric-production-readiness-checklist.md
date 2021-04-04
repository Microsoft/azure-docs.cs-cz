---
title: Kontrolní seznam připravenosti na produkci pro Azure Service Fabric
description: Připravte svou Service Fabricovou aplikaci a produkční clustery podle osvědčených postupů.
ms.topic: conceptual
ms.date: 6/05/2019
ms.openlocfilehash: a03df40a8ce213c5de9ed7017d47713c4de3449d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "87835863"
---
# <a name="production-readiness-checklist"></a>Kontrolní seznam připravenosti k produkci

Je vaše aplikace a cluster připravený přijmout provozní provoz? Spuštění a testování vaší aplikace a clusteru nemusí nutně znamenat, že je připraveno přejít do produkčního prostředí. Zajistěte plynulé zprovoznění aplikace a clusteru tím, že projdete následující kontrolní seznam. Důrazně doporučujeme, abyste všechny tyto položky kontrolovali. Zjevně můžete zvolit použití alternativních řešení pro konkrétní řádkovou položku (například vlastní diagnostické architektury).


## <a name="prerequisites-for-production"></a>Předpoklady pro produkční prostředí
1. Osvědčené postupy pro Azure Service Fabric: [Návrh aplikace](./service-fabric-best-practices-applications.md), [zabezpečení](./service-fabric-best-practices-security.md), [sítě](./service-fabric-best-practices-networking.md), [plánování a škálování kapacity](./service-fabric-best-practices-capacity-scaling.md), [Infrastruktura jako kód](./service-fabric-best-practices-infrastructure-as-code.md)a [monitorování a diagnostika](./service-fabric-best-practices-monitoring.md). 
1. [Nakonfigurujte nastavení FabricTransport](./service-fabric-reliable-actors-fabrictransportsettings.md) , pokud používáte model programování Reliable Actors a potřebujete zabezpečenou komunikaci mezi službami.
1. Pro clustery s více než 20 jádry nebo 10 uzly Vytvořte vyhrazený typ primárního uzlu pro systémové služby. Přidejte [omezení umístění](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md) pro rezervaci typu primárního uzlu pro systémové služby.
1. Pro typ primárního uzlu použijte SKU D2v2 nebo vyšší. Doporučuje se vybrat SKU, která má minimálně 50 GB kapacity pevného disku.
1. Provozní clustery musí být [zabezpečené](service-fabric-cluster-security.md). Příklad nastavení zabezpečeného clusteru najdete v této [šabloně clusteru](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/7-VM-Windows-3-NodeTypes-Secure-NSG). Používejte běžné názvy certifikátů a nepoužívejte certifikáty podepsané svým vlastníkem.
1. Přidejte [omezení prostředků na kontejnery a služby](service-fabric-resource-governance.md), aby nespotřeboval více než 75% prostředků uzlu. 
1. Pochopení a nastavení [úrovně trvanlivosti](service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster). Pro typy uzlů spouštějících stavová zatížení se doporučují stříbrná nebo vyšší úroveň odolnosti.
1. Pochopení a výběr [úrovně spolehlivosti](service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster) typu uzlu. Doporučuje se spolehlivost stříbra nebo vyšší.
1. Zátěžový a škálovatelný test úloh vám umožní identifikovat [požadavky na kapacitu](service-fabric-cluster-capacity.md) clusteru. 
1. Vaše služby a aplikace jsou monitorovány a protokoly aplikací se generují a ukládají s výstrahami. Podívejte se například [na téma Přidání protokolování do aplikace Service Fabric](service-fabric-how-to-diagnostics-log.md) a [monitorování kontejnerů pomocí protokolů Azure monitor](service-fabric-diagnostics-oms-containers.md).
1. Cluster se monitoruje pomocí upozorňování (například pomocí [protokolů Azure monitor](service-fabric-diagnostics-event-analysis-oms.md)). 
1. Základní infrastruktura sady škálování virtuálního počítače se monitoruje pomocí upozorňování (například pomocí [protokolů Azure monitor](service-fabric-diagnostics-oms-agent.md).
1. Cluster má vždycky [primární a sekundární certifikát](service-fabric-cluster-security-update-certs-azure.md) (takže se nebudete zablokovat).
1. Udržujte samostatné clustery pro vývoj, přípravu a produkci. 
1. [Upgrady aplikací](service-fabric-application-upgrade.md) a [upgrady clusterů](service-fabric-tutorial-upgrade-cluster.md) se nejprve testují ve vývojových a pracovních clusterech. 
1. Vypněte automatické upgrady v produkčních clusterech a zapněte je pro vývojové a přípravné clustery (vrácení zpět podle potřeby). 
1. Navažte cíl bodu obnovení (RPO) pro vaši službu a nastavte [proces zotavení po havárii](service-fabric-disaster-recovery.md) a otestujte ho.
1. Naplánujte ruční nebo programové [škálování](service-fabric-cluster-scaling.md) clusteru.
1. Naplánujte [opravy](service-fabric-patch-orchestration-application.md) vašich uzlů clusteru. 
1. Vytvořte kanál CI/CD, abyste mohli průběžně testovat nejnovější změny. Například pomocí [Azure DevOps](service-fabric-tutorial-deploy-app-with-cicd-vsts.md) nebo [Jenkinse](/azure/developer/jenkins/deploy-to-service-fabric-cluster)
1. Otestujte vývoj & přípravných clusterů pod zátěží pomocí [služby analýzy chyb](service-fabric-testability-overview.md) a [nachaos](service-fabric-controlled-chaos.md)řízených. 
1. Naplánujte [škálování](service-fabric-concepts-scalability.md) aplikací. 


Pokud používáte Service Fabric Reliable Services nebo Reliable Actors programovací model, je nutné zkontrolovat následující položky:
1. Upgradujte aplikace během místního vývoje, abyste zkontrolovali, jestli kód služby dodržuje token zrušení v `RunAsync` metodě a zavřeli vlastní naslouchací procesy komunikace.
1. Vyhněte se [běžným nástrah](service-fabric-work-with-reliable-collections.md) při používání spolehlivých kolekcí.
1. Sledujte čítače výkonu paměti .NET CLR při spuštění zátěžových testů a kontrolujte vysoké míry uvolňování paměti nebo nárůstu haldy.
1. Udržujte offline zálohování [Reliable Services a Reliable Actors](service-fabric-reliable-services-backup-restore.md) a otestujte proces obnovení.
1. Váš primární počet instancí virtuálních počítačů NodeType by se měl ideálně rovnat minimální úrovni spolehlivosti clusterů; podmínky, které jsou vhodné pro překročení minima úrovně: dočasně při vertikálním škálování primárních jednotek SKU sady škálování virtuálních počítačů.

## <a name="optional-best-practices"></a>Volitelné osvědčené postupy

I když se výše uvedené seznamy dostanou do produkčního prostředí, měly by se vzít v úvahu taky tyto položky:
1. Připojte se k [modelu Service Fabric stavu](service-fabric-health-introduction.md) pro rozšíření integrovaného vyhodnocení stavu a vytváření sestav.
1. Nasaďte vlastní sledovací zařízení, které monitoruje [zatížení](service-fabric-cluster-resource-manager-metrics.md) vaší aplikace a sestav pro [vyrovnávání prostředků](service-fabric-cluster-resource-manager-balancing.md). 


## <a name="next-steps"></a>Další kroky
* [Nasazení Service Fabric clusteru se systémem Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* [Nasazení clusteru Service Fabric Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
* Informace o [životním cyklu aplikace](service-fabric-application-lifecycle.md) Service Fabric.