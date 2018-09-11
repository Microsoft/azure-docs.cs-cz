---
title: Azure Service Fabric produkční připravenosti kontrolní seznam | Dokumentace Microsoftu
description: Příprava produkčního prostředí clusteru a aplikace Service Fabric pomocí osvědčených postupů.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 7/10/2018
ms.author: subramar
ms.openlocfilehash: 6ebe83794f23ed3cf637f668d2810b9ba7521084
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2018
ms.locfileid: "44295866"
---
# <a name="production-readiness-checklist"></a>Kontrolní seznam připravenosti k produkci

Je vaše aplikace a clusteru, jste připraveni udělat produkční provoz? Spuštění a testování vaší aplikace i cluster ale neznamená nutně, že jste připravení přejít do produkčního prostředí. Udržujte aplikace a clusteru se systémem hladce prostřednictvím následujícího kontrolního seznamu. Důrazně doporučujeme tyto položky mají být zkontrolovány. Samozřejmě můžete použít alternativní řešení pro konkrétní položky řádku (například vlastní Diagnostika rozhraní).


## <a name="pre-requisites-for-production"></a>Předpoklady pro produkční prostředí

1. Pro clustery s více než 20 jader nebo 10 uzlů vytvoření vyhrazených uzlů primárního typu pro systémové služby. Přidat [omezení umístění](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md) rezervovat primární typ uzlu systémových služeb. 
2. Použijte D2v2 nebo vyšší skladová položka pro primární typ uzlu. Doporučujeme vybrat skladovou Položku s kapacitou disku alespoň 50 GB.
2. Produkčních clusterů musí být [zabezpečené](service-fabric-cluster-security.md). Příklad vytvoření zabezpečeného clusteru, najdete v tomto [šablony clusteru](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/7-VM-Windows-3-NodeTypes-Secure-NSG). Používat běžné názvy pro certifikáty a vyhněte se použití vlastní podepsané certifikáty.
4. Přidat [omezení prostředků pro kontejnery a služby](service-fabric-resource-governance.md), takže není více než 75 % uzel prostředky spotřebují. 
5. Pochopte a nastavte [úroveň odolnosti](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster). Úroveň odolnosti stříbrné nebo vyšší se doporučuje pro typy uzlů spuštěná stavová zatížení. Typ primárního uzlu by měl mít úroveň odolnosti nastavena na Silver nebo vyšší.
6. Pochopení a vybrat [úroveň spolehlivosti](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) typu uzlu. Doporučuje se spolehlivost stříbrné nebo vyšší.
7. Zatížení a škálování otestovat své úlohy k identifikaci [požadavky na kapacitu](service-fabric-cluster-capacity.md) pro váš cluster. 
8. Služby a aplikace jsou monitorovány a protokoly aplikací se vygeneruje a uložená, s výstrahy. Viz například [přidání protokolování do aplikace Service Fabric](service-fabric-how-to-diagnostics-log.md) a [monitorování kontejnerů pomocí Log Analytics](service-fabric-diagnostics-oms-containers.md).
9. Cluster se monitoruje s výstrahy (třeba index Mei [OMS](service-fabric-diagnostics-event-analysis-oms.md)). 
10. Základní infrastruktura sady škálování virtuálního počítače je monitorovat pomocí upozornění (třeba index Mei [Log Analytics](service-fabric-diagnostics-oms-agent.md).
11. Cluster má [primárního a sekundárního certifikátu](service-fabric-cluster-security-update-certs-azure.md) vždy (takže nezůstanete zamknutí).
12. Udržujte samostatné clustery pro vývoj, přípravném nebo produkčním prostředí. 
13. [Upgrady aplikací](service-fabric-application-upgrade.md) a [clusteru upgrady](service-fabric-tutorial-upgrade-cluster.md) jsou testovány ve vývoji a první pracovní clustery. 
14. Vypnout automatické upgrady v produkčních clusterech a zapnout ho pro vývoj a testování clusterů (vrácení zpět v případě potřeby). 
15. Vytvořit cíl bodu obnovení (RPO) pro vaši službu a nastavit [procesu pro zotavení po havárii](service-fabric-disaster-recovery.md) a otestování.
16. Plánování [škálování](service-fabric-cluster-scaling.md) cluster ručně nebo prostřednictvím kódu programu.
17. Plánování [opravy](service-fabric-patch-orchestration-application.md) uzly clusteru. 
18. Vytvoření kanálu CI/CD tak, aby nejnovější změny průběžně testován. Například použití [Azure DevOps](service-fabric-tutorial-deploy-app-with-cicd-vsts.md) nebo [Jenkinse](service-fabric-cicd-your-linux-applications-with-jenkins.md)
19. Vývoj a pracovní zatížení s clustery testů [Fault Analysis Service](service-fabric-testability-overview.md) a vyvolávat řízené [chaos](service-fabric-controlled-chaos.md). 
20. Plánování [škálování](service-fabric-concepts-scalability.md) vašich aplikací. 


Pokud používáte programovacího modelu Service Fabric Reliable Services a Reliable Actors, třeba zkontrolovat následující položky:
21. Upgrade aplikací během vývoje. Zkontrolujte, že je váš kód služby dodržením token zrušení v místní `RunAsync` metoda a zavírání vlastní komunikační naslouchacích procesů.
22. Vyhněte se [běžné nástrahy](service-fabric-work-with-reliable-collections.md) při použití Reliable Collections.
23. Monitorování výkonu paměti .NET CLR čítačů při spuštění zátěžových testů a vyhledejte vysoký objem uvolňování paměti nebo vyčerpává dlouho běžící haldy růstu.
24. Zálohování offline udržovat [Reliable Services a Reliable Actors](service-fabric-reliable-services-backup-restore.md) a testování procesu obnovení. 


## <a name="optional-best-practices"></a>Volitelné postupy

Výše uvedené seznamy jsou požadavky na přechod do produkčního prostředí, byste také zvážit následující položky:
25. Zapojte [modelu stavu Service Fabric](service-fabric-health-introduction.md) pro rozšíření integrovaných stav vyhodnocení a vytváření sestav.
26. Nasazení vlastní sledovacího zařízení, která monitoruje vaše aplikace a sestavy [načíst](service-fabric-cluster-resource-manager-metrics.md) pro [vyrovnávání prostředků](service-fabric-cluster-resource-manager-balancing.md). 


## <a name="next-steps"></a>Další postup
* [Nasazení clusteru Service Fabric Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* [Nasazení clusteru Service Fabric s Linuxem](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
* Informace o [životním cyklu aplikace](service-fabric-application-lifecycle.md) Service Fabric.
