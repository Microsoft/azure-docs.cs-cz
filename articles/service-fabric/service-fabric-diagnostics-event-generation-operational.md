---
title: Seznam událostí služby Azure Service Fabric | Microsoft Docs
description: Úplný seznam událostí, které poskytuje Azure Service Fabric ke sledování clusterů.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/25/2018
ms.author: dekapur
ms.openlocfilehash: d397dcb1ecdc25dbd66ab3d6f2f010bc29f87c6c
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2018
---
# <a name="list-of-service-fabric-events"></a>Seznam událostí Service Fabric 

Service Fabric zveřejňuje sadu clusteru události o stavu vašeho clusteru jako primární [události služby Fabric](service-fabric-diagnostics-events.md). Tyto jsou založené na akci, kterou provádí Service Fabric na uzly a clusteru nebo správu rozhodnutí clusteru vlastníka nebo operátor. Tyto události je přístupná pomocí dotazu [EventStore](service-fabric-diagnostics-eventstore.md) v clusteru, nebo prostřednictvím provozní kanálu. Na počítače s Windows provozní kanál je také připojených k protokolu událostí – aby se zobrazily události služby prostředků infrastruktury v prohlížeči událostí. 

>[!NOTE]
>Seznam událostí Service Fabric pro clustery v verze < 6.2 naleznete v následující části. 

Tady je seznam všech událostí v platformě, seřazené podle typ entity, která jsou mapovány na k dispozici.

## <a name="cluster-events"></a>Události clusteru

**Události upgrade clusteru**

| ID události | Název | Zdroj (úloha) | Úroveň | Verze |
| --- | --- | --- | --- | --- |
| 29627 | ClusterUpgradeStartOperational | CM | Informační | 1 |
| 29628 | ClusterUpgradeCompleteOperational | CM | Informační | 1 |
| 29629 | ClusterUpgradeRollbackStartOperational | CM | Informační | 1 |
| 29630 | ClusterUpgradeRollbackCompleteOperational | CM | Informační | 1 |
| 29631 | ClusterUpgradeDomainCompleteOperational | CM | Informační | 1 |

**Události sestavy stavu clusteru**

| ID události | Název | Zdroj (úloha) | Úroveň | Verze |
| --- | --- | --- | --- | --- |
| 54428 | ProcessClusterReportOperational | HM | Informační | 1 |
| 54437 | ExpiredClusterEventOperational | HM | Informační | 1 |

**Události služby Chaos**

| ID události | Název | Zdroj (úloha) | Úroveň | Verze |
| --- | --- | --- | --- | --- |
| 50021 | ChaosStartedEvent | Možnosti testování | Informační | 1 |
| 50023 | ChaosStoppedEvent | Možnosti testování | Informační | 1 |

## <a name="node-events"></a>Uzel události

**Události životního cyklu uzlu** 

| ID události | Název | Zdroj (úloha) | Úroveň | Verze |
| --- | --- | --- | --- | --- |
| 18602 | DeactivateNodeCompletedOperational | FM | Informační | 1 |
| 18603 | NodeUpOperational | FM | Informační | 1 |
| 18604 | NodeDownOperational | FM | Informační | 1 |
| 18605 | NodeAddedOperational | FM | Informační | 1 |
| 18606 | NodeRemovedOperational | FM | Informační | 1 |
| 18607 | DeactivateNodeStartOperational | FM | Informační | 1 |
| 25620 | NodeOpening | FabricNode | Informační | 1 |
| 25621 | NodeOpenedSuccess | FabricNode | Informační | 1 |
| 25622 | NodeOpenedFailed | FabricNode | Informační | 1 |
| 25623 | NodeClosing | FabricNode | Informační | 1 |
| 25624 | NodeClosed | FabricNode | Informační | 1 |
| 25625 | NodeAborting | FabricNode | Informační | 1 |
| 25626 | NodeAborted | FabricNode | Informační | 1 |

**Události sestavy stavu uzlu**

| ID události | Název | Zdroj (úloha) | Úroveň | Verze |
| --- | --- | --- | --- | --- |
| 54423 | ProcessNodeReportOperational | HM | Informační | 1 |
| 54432 | ExpiredNodeEventOperational | HM | Informační | 1 |

**Chaos uzlu události**

| ID události | Název | Zdroj (úloha) | Úroveň | Verze |
| --- | --- | --- | --- | --- |
| 50033 | ChaosRestartNodeFaultScheduledEvent | Možnosti testování | Informační | 1 |
| 50087 | ChaosRestartNodeFaultCompletedEvent | Možnosti testování | Informační | 1 |

## <a name="application-events"></a>Události aplikace

**Události životního cyklu aplikací**

| ID události | Název | Zdroj (úloha) | Úroveň | Verze |
| --- | --- | --- | --- | --- |
| 29620 | ApplicationCreatedOperational | CM | Informační | 1 |
| 29625 | ApplicationDeletedOperational | CM | Informační | 1 |
| 23083 | ProcessExitedOperational | Hostování | Informační | 1 |

**Události upgradu aplikace**

| ID události | Název | Zdroj (úloha) | Úroveň | Verze |
| --- | --- | --- | --- | --- |
| 29621 | ApplicationUpgradeStartOperational | CM | Informační | 1 |
| 29622 | ApplicationUpgradeCompleteOperational | CM | Informační | 1 |
| 29623 | ApplicationUpgradeRollbackStartOperational | CM | Informační | 1 |
| 29624 | ApplicationUpgradeRollbackCompleteOperational | CM | Informační | 1 |
| 29626 | ApplicationUpgradeDomainCompleteOperational | CM | Informační | 1 |

**Události sestavy stavu aplikace**

| ID události | Název | Zdroj (úloha) | Úroveň | Verze |
| --- | --- | --- | --- | --- |
| 54425 | ProcessApplicationReportOperational | HM | Informační | 1 |
| 54426 | ProcessDeployedApplicationReportOperational | HM | Informační | 1 |
| 54427 | ProcessDeployedServicePackageReportOperational | HM | Informační | 1 |
| 54434 | ExpiredApplicationEventOperational | HM | Informační | 1 |
| 54435 | ExpiredDeployedApplicationEventOperational | HM | Informační | 1 |
| 54436 | ExpiredDeployedServicePackageEventOperational | HM | Informační | 1 |

**Události Chaos aplikace**

| ID události | Název | Zdroj (úloha) | Úroveň | Verze |
| --- | --- | --- | --- | --- |
| 50053 | ChaosRestartCodePackageFaultScheduledEvent | Možnosti testování | Informační | 1 |
| 50101 | ChaosRestartCodePackageFaultCompletedEvent | Možnosti testování | Informační | 1 |

## <a name="service-events"></a>Události služby

**Události služby životního cyklu**

| ID události | Název | Zdroj (úloha) | Úroveň | Verze |
| --- | --- | --- | --- | --- |
| 18602 | ServiceCreatedOperational | FM | Informační | 1 |
| 18658 | ServiceDeletedOperational | FM | Informační | 1 |

**Události sestav stavu služby**

| ID události | Název | Zdroj (úloha) | Úroveň | Verze |
| --- | --- | --- | --- | --- |
| 54424 | ProcessServiceReportOperational | HM | Informační | 1 |
| 54433 | ExpiredServiceEventOperational | HM | Informační | 1 |

## <a name="partition-events"></a>Oddíl události

**Oddíl Přesunutí události**

| ID události | Název | Zdroj (úloha) | Úroveň | Verze |
| --- | --- | --- | --- | --- |
| 18940 | ReconfigurationCompleted | RA | Informační | 1 |

**Události sestavy stavu oddílu**

| ID události | Název | Zdroj (úloha) | Úroveň | Verze |
| --- | --- | --- | --- | --- |
| 54422 | ProcessPartitionReportOperational | HM | Informační | 1 |
| 54431 | ExpiredPartitionEventOperational | HM | Informační | 1 |

**Chaos oddílu události**

| ID události | Název | Zdroj (úloha) | Úroveň | Verze |
| --- | --- | --- | --- | --- |
| 50069 | ChaosMovePrimaryFaultScheduledEvent | Možnosti testování | Informační | 1 |
| 50077 | ChaosMoveSecondaryFaultScheduledEvent | Možnosti testování | Informační | 1 |

**Oddíl analýzy událostí**

| ID události | Název | Zdroj (úloha) | Úroveň | Verze |
| --- | --- | --- | --- | --- |
| 65003 | PrimaryMoveAnalysisEvent | Možnosti testování | Informační | 1 |

## <a name="replica-events"></a>Repliky události

**Události sestavy stavu repliky**

| ID události | Název | Zdroj (úloha) | Úroveň | Verze |
| --- | --- | --- | --- | --- |
| 54429 | ProcessStatefulReplicaReportOperational | HM | Informační | 1 |
| 54430 | ProcessStatelessInstanceReportOperational | HM | Informační | 1 |
| 54438 | ExpiredStatefulReplicaEventOperational | HM | Informační | 1 |
| 54439 | ExpiredStatelessInstanceEventOperational | HM | Informační | 1 |

**Chaos repliky události**

| ID události | Název | Zdroj (úloha) | Úroveň | Verze |
| --- | --- | --- | --- | --- |
| 50047 | ChaosRestartReplicaFaultScheduledEvent | Možnosti testování | Informační | 1 |
| 50051 | ChaosRemoveReplicaFaultScheduledEvent | Možnosti testování | Informační | 1 |
| 50093 | ChaosRemoveReplicaFaultCompletedEvent | Možnosti testování | Informační | 1 |

## <a name="container-events"></a>Události kontejneru

**Události životního cyklu kontejneru** 

| ID události | Název | Zdroj (úloha) | Úroveň | Verze |
| --- | --- | --- | --- | --- |
| 23074 | ContainerActivatedOperational | Hostování | Informační | 1 |
| 23075 | ContainerDeactivatedOperational | Hostování | Informační | 1 |
| 23082 | ContainerExitedOperational | Hostování | Informační | 1 |

## <a name="other-events"></a>Další události

**Události modulu Correlation**

| ID události | Název | Zdroj (úloha) | Úroveň | Verze |
| --- | --- | --- | --- | --- |
| 65011 | CorrelationOperational | Možnosti testování | Informační | 1 |

## <a name="events-prior-to-version-62"></a>Události starší než verze 6.2

Tady je úplný seznam událostí poskytované Service Fabric starší než verze 6.2.

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
| 18566 | ServiceCreated | FM | Informační |
| 18567 | ServiceDeleted | FM | Informační |

## <a name="next-steps"></a>Další postup

* Další informace o celkové [generování událostí na úrovni platformy](service-fabric-diagnostics-event-generation-infra.md) v Service Fabric
* Úprava vaše [Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md) konfigurace shromažďovat další protokoly
* [Nastavení služby Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) zobrazíte vaše provozní kanálu protokoly
