---
title: Seznam událostí služby Azure Service Fabric | Dokumentace Microsoftu
description: Úplný seznam událostí, které poskytuje Azure Service Fabric, aby se usnadnilo monitorování clusterů.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: reference
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/25/2018
ms.author: dekapur
ms.openlocfilehash: da4a779bca806fe6aa392db96eafc6c20f8ddcf6
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2018
ms.locfileid: "47182157"
---
# <a name="list-of-service-fabric-events"></a>Seznam událostí Service Fabric 

Service Fabric poskytuje sadu události clusteru pro informování o stavu vašeho clusteru jako primární [události Service Fabric](service-fabric-diagnostics-events.md). Jsou založeny na rozhodnutích týkajících se správy od vlastníka nebo operátor clusteru nebo akce provádět na svých uzlech i cluster Service Fabric. Tyto události můžete přistupovat pomocí dotazu [Eventstoru](service-fabric-diagnostics-eventstore.md) ve vašem clusteru, nebo prostřednictvím provozní kanál. Na počítačích s Windows provozní kanál je také připojili do protokolu událostí – abyste si mohli zobrazit události Service Fabric v prohlížeči událostí. 

>[!NOTE]
>Seznam událostí Service Fabric pro clustery v verze < 6.2 najdete v následující části. 

Tady je k dispozici na platformě, seřazené podle entity, které jsou mapovány na seznam všech událostí.

## <a name="cluster-events"></a>Události clusteru

**Události upgrade clusteru**

| ID události | Název | Popis |Zdroj (úloha) | Úroveň | Verze |
| --- | --- | --- | --- | --- | --- |
| 29627 | ClusterUpgradeStarted | Bylo zahájeno upgradu clusteru | CM | Informační | 1 |
| 29628 | ClusterUpgradeCompleted | Dokončení upgradu clusteru| CM | Informační | 1 |
| 29629 | ClusterUpgradeRollbackStarted | Upgrade clusteru začal vrácení zpět | CM | Informační | 1 |
| 29630 | ClusterUpgradeRollbackCompleted | Upgrade clusteru dokončení vrácení zpět | CM | Informační | 1 |
| 29631 | ClusterUpgradeDomainCompleted | Dokončení upgradu domény během upgradu clusteru | CM | Informační | 1 |

## <a name="node-events"></a>Uzel události

**Události životního cyklu uzlu** 

| ID události | Název | Popis |Zdroj (úloha) | Úroveň | Verze |
| --- | --- | ---| --- | --- | --- |
| 18602 | NodeDeactivateCompleted | Deaktivace uzlu byla dokončena. | PŘEVZETÍ SLUŽEB PŘI SELHÁNÍ | Informační | 1 |
| 18603 | NodeUp | Cluster zjistil, že uzel má spuštění | PŘEVZETÍ SLUŽEB PŘI SELHÁNÍ | Informační | 1 |
| 18604 | NodeDown | Cluster zjistil, že uzel byl vypnut. |  PŘEVZETÍ SLUŽEB PŘI SELHÁNÍ | Informační | 1 |
| 18605 | NodeAddedToCluster | Byl přidán nový uzel do clusteru | PŘEVZETÍ SLUŽEB PŘI SELHÁNÍ | Informační | 1 |
| 18606 | NodeRemovedFromCluster | Odebral uzel z clusteru | PŘEVZETÍ SLUŽEB PŘI SELHÁNÍ | Informační | 1 |
| 18607 | NodeDeactivateStarted | Bylo zahájeno deaktivace uzlu | PŘEVZETÍ SLUŽEB PŘI SELHÁNÍ | Informační | 1 |
| 25620 | NodeOpening | Uzel se spouští. První fáze životního cyklu uzlu | FabricNode | Informační | 1 |
| 25621 | NodeOpenSucceeded | Uzel byl úspěšně spuštěn | FabricNode | Informační | 1 |
| 25622 | NodeOpenFailed | Uzel se nepovedlo spustit | FabricNode | Informační | 1 |
| 25623 | NodeClosing | Uzel se vypíná. Začátek závěrečná fáze životního cyklu uzlu | FabricNode | Informační | 1 |
| 25624 | NodeClosed | Uzel úspěšně vypnuta | FabricNode | Informační | 1 |
| 25625 | NodeAborting | Spouští se uzel ungracefully vypnutí | FabricNode | Informační | 1 |
| 25626 | NodeAborted | Uzel má ungracefully vypnout | FabricNode | Informační | 1 |

## <a name="application-events"></a>Události aplikace

**Události životního cyklu aplikace**

| ID události | Název | Popis |Zdroj (úloha) | Úroveň | Verze |
| --- | --- | ---| --- | --- | --- |
| 29620 | ApplicationCreated | Vytvoření nové aplikace | CM | Informační | 1 |
| 29625 | ApplicationDeleted | Existující aplikace byla odstraněna. | CM | Informační | 1 |
| 23083 | ApplicationProcessExited | Došlo k ukončení procesu v rámci aplikace | Hostování | Informační | 1 |

**Události upgradu aplikace**

| ID události | Název | Popis |Zdroj (úloha) | Úroveň | Verze |
| --- | --- | ---| --- | --- | --- |
| 29621 | ApplicationUpgradeStarted | Upgrade aplikace byla spuštěna. | CM | Informační | 1 |
| 29622 | ApplicationUpgradeCompleted | Upgrade aplikace byla dokončena. | CM | Informační | 1 |
| 29623 | ApplicationUpgradeRollbackStarted | Upgrade aplikace spustila na vrácení zpět |CM | Informační | 1 |
| 29624 | ApplicationUpgradeRollbackCompleted | Upgrade aplikace dokončení vrácení zpět | CM | Informační | 1 |
| 29626 | ApplicationUpgradeDomainCompleted | Dokončení upgradu domény během upgradu aplikace | CM | Informační | 1 |

## <a name="service-events"></a>Události služby

**Události životního cyklu služeb**

| ID události | Název | Popis |Zdroj (úloha) | Úroveň | Verze |
| --- | --- | ---| --- | --- | --- |
| 18657 | ServiceCreated | Byla vytvořena nová služba | PŘEVZETÍ SLUŽEB PŘI SELHÁNÍ | Informační | 1 |
| 18658 | ServiceDeleted | Existující služba se odstranila. | PŘEVZETÍ SLUŽEB PŘI SELHÁNÍ | Informační | 1 |

## <a name="partition-events"></a>Oddíl události

**Události pohybu oddílu**

| ID události | Název | Popis |Zdroj (úloha) | Úroveň | Verze |
| --- | --- | ---| --- | --- | --- |
| 18940 | PartitionReconfigured | Rekonfigurace oddílu byla dokončena. | VZDÁLENÁ POMOC | Informační | 1 |

## <a name="container-events"></a>Události kontejneru

**Události životního cyklu kontejneru** 

| ID události | Název | Popis |Zdroj (úloha) | Úroveň | Verze |
| --- | --- | ---| --- | --- | --- |
| 23074 | ContainerActivated | Bylo zahájeno kontejneru | Hostování | Informační | 1 |
| 23075 | ContainerDeactivated | Kontejner se zastavila. | Hostování | Informační | 1 |
| 23082 | ContainerExited | Kontejner se ukončil - zkontrolujte soubor UnexpectedTermination příznak | Hostování | Informační | 1 |

## <a name="health-reports"></a>Sestav stavu

**Události sestavy stavu clusteru**

| ID události | Název | Popis |Zdroj (úloha) | Úroveň | Verze |
| --- | --- | --- | --- | --- | --- |
| 54428 | ClusterNewHealthReport | Je k dispozici nová sestava stavu clusteru | HM | Informační | 1 |
| 54437 | ClusterHealthReportExpired | Vypršela platnost existující sestavy stavu clusteru | HM | Informační | 1 |

**Události sestavy stavu uzlu**

| ID události | Název | Popis |Zdroj (úloha) | Úroveň | Verze |
| --- | --- | ---| --- | --- | --- |
| 54423 | NodeNewHealthReport | Je k dispozici nová sestava stavu uzlu | HM | Informační | 1 |
| 54432 | NodeHealthReportExpired | Vypršela platnost existující sestavy stavu uzlu | HM | Informační | 1 |

**Události sestavy stavu aplikace**

| ID události | Název | Popis |Zdroj (úloha) | Úroveň | Verze |
| --- | --- | ---| --- | --- | --- |
| 54425 | ApplicationNewHealthReport | Byla vytvořena nová sestava stavu aplikace. Jde o nenasazené aplikace. | HM | Informační | 1 |
| 54426 | DeployedApplicationNewHealthReport | Vytvoření nové sestavy stavu nasazení aplikace | HM | Informační | 1 |
| 54427 | DeployedServicePackageNewHealthReport | Vytvoření nové sestavy nasazené služby stavu | HM | Informační | 1 |
| 54434 | ApplicationHealthReportExpired | Vypršela platnost existující sestavy stavu aplikace | HM | Informační | 1 |
| 54435 | DeployedApplicationHealthReportExpired | Vypršela platnost existující sestavy stavu nasazení aplikace | HM | Informační | 1 |
| 54436 | DeployedServicePackageHealthReportExpired | Vypršela platnost existující sestavy nasazené služby stavu | HM | Informační | 1 |

**Sestava události služby Service health**

| ID události | Název | Popis |Zdroj (úloha) | Úroveň | Verze |
| --- | --- | ---| --- | --- | --- |
| 54424 | ServiceNewHealthReport | Nová sestava health service byla vytvořena. | HM | Informační | 1 |
| 54433 | ServiceHealthReportExpired | Vypršela platnost existující sestavy stavu služby | HM | Informační | 1 |

**Události sestavy stav oddílu**

| ID události | Název | Popis |Zdroj (úloha) | Úroveň | Verze |
| --- | --- | ---| --- | --- | --- |
| 54422 | PartitionNewHealthReport | Vytvoření nové sestavy stav oddílu | HM | Informační | 1 |
| 54431 | PartitionHealthReportExpired | Existující sestavy stav oddílu vypršela platnost. | HM | Informační | 1 |

**Události sestavy stavu repliky**

| ID události | Název | Popis |Zdroj (úloha) | Úroveň | Verze |
| --- | --- | ---| --- | --- | --- |
| 54429 | StatefulReplicaNewHealthReport | Sestava stavu stavové replika byla vytvořena. | HM | Informační | 1 |
| 54430 | StatelessInstanceNewHealthReport | Nová sestava stavu bezstavové instance byla vytvořena. | HM | Informační | 1 |
| 54438 | StatefulReplicaHealthReportExpired | Existující sestavy stavu stavové repliky vypršela platnost. | HM | Informační | 1 |
| 54439 | StatelessInstanceHealthReportExpired | Existující sestavy stavu bezstavové instance vypršela platnost. | HM | Informační | 1 |

## <a name="chaos-testing-events"></a>Testování události chaosu 

**Chaos relace události**

| ID události | Název | Popis |Zdroj (úloha) | Úroveň | Verze |
| --- | --- | ---| --- | --- | --- |
| 50021 | ChaosStarted | Bylo zahájeno Chaos testovací relaci | Testovatelnosti | Informační | 1 |
| 50023 | ChaosStopped | Chaos testování relace byla zastavena. | Testovatelnosti | Informační | 1 |

**Chaos uzel události**

| ID události | Název | Popis |Zdroj (úloha) | Úroveň | Verze |
| --- | --- | ---| --- | --- | --- |
| 50033 | ChaosNodeRestartScheduled | Uzel je naplánováno restartuje jako součást Chaos testovací relaci | Testovatelnosti | Informační | 1 |
| 50087 | ChaosNodeRestartCompleted | Uzel dokončení se restartuje jako součást Chaos testovací relaci | Testovatelnosti | Informační | 1 |

**Události aplikace chaosu**

| ID události | Název | Popis |Zdroj (úloha) | Úroveň | Verze |
| --- | --- | ---| --- | --- | --- |
| 50053 | ChaosCodePackageRestartScheduled | Restartování balíček kódu je naplánovaná během Chaos testovací relaci | Testovatelnosti | Informační | 1 |
| 50101 | ChaosCodePackageRestartCompleted | Restartování balíček kódu byla dokončena během Chaos testovací relaci | Testovatelnosti | Informační | 1 |

**Chaos oddílu události**

| ID události | Název | Popis |Zdroj (úloha) | Úroveň | Verze |
| --- | --- | ---| --- | --- | --- |
| 50069 | ChaosPartitionPrimaryMoveScheduled | Primární oddíl je naplánováno přesunout jako součást Chaos testovací relaci | Testovatelnosti | Informační | 1 |
| 50077 | ChaosPartitionSecondaryMoveScheduled | Sekundární oddílu je naplánováno přesunout jako součást Chaos testovací relaci | Testovatelnosti | Informační | 1 |
| 65003 | PartitionPrimaryMoveAnalysis | Hlubší analýza přesunutí primární oddíl je k dispozici | Testovatelnosti | Informační | 1 |

**Události repliky chaosu**

| ID události | Název | Popis |Zdroj (úloha) | Úroveň | Verze |
| --- | --- | ---| --- | --- | --- |
| 50047 | ChaosReplicaRestartScheduled | Restartování replika byla plánována jako součást Chaos testovací relaci | Testovatelnosti | Informační | 1 |
| 50051 | ChaosReplicaRemovalScheduled | Jako součást Chaos testovací relaci bylo naplánováno odstranění repliky | Testovatelnosti | Informační | 1 |
| 50093 | ChaosReplicaRemovalCompleted | Odstranění repliky byla dokončena jako součást Chaos testovací relaci | Testovatelnosti | Informační | 1 |

## <a name="other-events"></a>Další události

**Události modulu Correlation**

| ID události | Název | Popis |Zdroj (úloha) | Úroveň | Verze |
| --- | --- | ---| --- | --- | --- |
| 65011 | CorrelationOperational | Byla korelaci detacted | Testovatelnosti | Informační | 1 |

## <a name="events-prior-to-version-62"></a>Události starší než verze 6.2

Tady je úplný seznam událostí Service Fabric poskytuje starší než verze 6.2.

| ID události | Název | Zdroj (úloha) | Úroveň |
| --- | --- | --- | --- |
| 25620 | NodeOpening | FabricNode | Informační |
| 25621 | NodeOpenedSuccess | FabricNode | Informační |
| 25622 | NodeOpenedFailed | FabricNode | Informační |
| 25623 | NodeClosing | FabricNode | Informační |
| 25624 | NodeClosed | FabricNode | Informační |
| 25625 | NodeAborting | FabricNode | Informační |
| 25626 | NodeAborted | FabricNode | Informační |
| 29627 | ClusterUpgradeStart | CM | Informační |
| 29628 | ClusterUpgradeComplete | CM | Informační |
| 29629 | ClusterUpgradeRollback | CM | Informační |
| 29630 | ClusterUpgradeRollbackComplete | CM | Informační |
| 29631 | ClusterUpgradeDomainComplete | CM | Informační |
| 23074 | ContainerActivated | Hostování | Informační |
| 23075 | ContainerDeactivated | Hostování | Informační |
| 29620 | ApplicationCreated | CM | Informační |
| 29621 | ApplicationUpgradeStart | CM | Informační |
| 29622 | ApplicationUpgradeComplete | CM | Informační |
| 29623 | ApplicationUpgradeRollback | CM | Informační |
| 29624 | ApplicationUpgradeRollbackComplete | CM | Informační |
| 29625 | ApplicationDeleted | CM | Informační |
| 29626 | ApplicationUpgradeDomainComplete | CM | Informační |
| 18566 | ServiceCreated | PŘEVZETÍ SLUŽEB PŘI SELHÁNÍ | Informační |
| 18567 | ServiceDeleted | PŘEVZETÍ SLUŽEB PŘI SELHÁNÍ | Informační |

## <a name="next-steps"></a>Další postup

* Další informace o celkové [generování událostí na úrovni platformy](service-fabric-diagnostics-event-generation-infra.md) v Service Fabric
* Úprava vaše [Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md) konfigurace shromažďování protokolů více
* [Nastavení Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) zobrazíte vaší provozní kanál protokoly
