---
title: Seznam událostí pro Azure Service Fabric
description: Úplný seznam událostí poskytovaných službou Azure Service Fabric, které vám pomůžou monitorovat clustery.
ms.topic: reference
ms.date: 2/25/2019
ms.openlocfilehash: a1ba21aaac831abb07544944420f2b77e1753955
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2021
ms.locfileid: "106279778"
---
# <a name="list-of-service-fabric-events"></a>Seznam událostí Service Fabric 

Service Fabric zveřejňuje primární sadu událostí clusteru, aby informovala o stavu clusteru jako [Service Fabricch událostí](service-fabric-diagnostics-events.md). Jsou založené na akcích, které provádí Service Fabric na vašich uzlech a na rozhodnutích o clusteru nebo správě provedených vlastníkem nebo operátorem clusteru. K těmto událostem může být umožněno konfigurace několika způsobů, včetně konfigurace [Azure monitor protokolů s clusterem](service-fabric-diagnostics-oms-setup.md)nebo dotazování na [eventstoru](service-fabric-diagnostics-eventstore.md). V počítačích s Windows se tyto události dodají do protokolu událostí, takže v Prohlížeč událostí uvidíte události Service Fabric. 

Tady jsou některé charakteristiky těchto událostí.
* Každá událost je svázána se specifickou entitou v clusteru, např. aplikace, služba, uzel, replika.
* Každá událost obsahuje sadu společných polí: EventInstanceId, EventName a Category.
* Každá událost obsahuje pole, která propojují událost zpátky k entitě, ke které je přidružena. Například událost ApplicationCreated by měla pole, která identifikují název vytvořené aplikace.
* Události jsou strukturované takovým způsobem, že je lze spotřebovat v různých nástrojích, aby bylo možné provádět další analýzu. Kromě toho jsou relevantní podrobnosti události definovány jako samostatné vlastnosti na rozdíl od dlouhého řetězce. 
* Události jsou zapisovány různými subsystémy v Service Fabric jsou označeny zdrojem (úlohou) níže. Další informace jsou k dispozici v těchto podsystémech [Service Fabric architektury](service-fabric-architecture.md) a [Service Fabric technický přehled](service-fabric-technical-overview.md).

Tady je seznam těchto událostí Service Fabric uspořádaných podle entity.

## <a name="cluster-events"></a>Události clusteru

**Události upgradu clusteru**

Další podrobnosti o upgradech clusteru najdete [tady](service-fabric-cluster-upgrade-windows-server.md).

| ID události | Name | Kategorie | Popis |Zdroj (úloha) | Level | 
| --- | --- | --- | --- | --- | --- | 
| 29627 | ClusterUpgradeStarted | Upgrade | Upgrade clusteru se spustil. | CM | Informační |
| 29628 | ClusterUpgradeCompleted | Upgrade | Upgrade clusteru byl dokončen. | CM | Informační | 
| 29629 | ClusterUpgradeRollbackStarted | Upgrade | Upgrade clusteru se začal vrátit zpátky.  | CM | Upozornění | 
| 29630 | ClusterUpgradeRollbackCompleted | Upgrade | Upgrade clusteru se dokončil zpátky. | CM | Upozornění | 
| 29631 | ClusterUpgradeDomainCompleted | Upgrade | Upgrade domény byl dokončen během upgradu clusteru. | CM | Informační | 

**Události umístění**
| ID události | Name | Kategorie | Popis |Zdroj (úloha) | Level |
| --- | --- | ---| --- | --- | --- |
| 17616 | Rozhodnutí |StateTransition | U operace umístění bylo naplánováno určení umístění nových replik. | APLIKACE | Informační |


## <a name="node-events"></a>Události uzlu

**Události životního cyklu uzlu** 

| ID události | Name | Kategorie | Popis |Zdroj (úloha) | Level |
| --- | --- | ---| --- | --- | --- | 
| 18602 | NodeDeactivateCompleted | StateTransition | Deaktivace uzlu se dokončila. | FM | Informační | 
| 18603 | NodeUp | StateTransition | Cluster zjistil, že se spustil uzel. | FM | Informační | 
| 18604 | NodeDown | StateTransition | Cluster zjistil vypnutí uzlu. Během restartování uzlu se zobrazí událost NodeDown následovaná událostí NodeUp. |  FM | Chyba | 
| 18605 | NodeAddedToCluster | StateTransition |  Do clusteru se přidal nový uzel a Service Fabric můžou do tohoto uzlu nasadit aplikace. | FM | Informační | 
| 18606 | NodeRemovedFromCluster | StateTransition |  Uzel byl odebrán z clusteru. Service Fabric už nebude nasazovat aplikace do tohoto uzlu. | FM | Informační | 
| 18607 | NodeDeactivateStarted | StateTransition |  Bylo zahájeno deaktivace uzlu. | FM | Informační | 
| 25621 | NodeOpenSucceeded | StateTransition |  Uzel byl úspěšně spuštěn. | FabricNode | Informační | 
| 25622 | NodeOpenFailed | StateTransition |  Uzel se nepovedlo spustit a připojit k okruhu. | FabricNode | Chyba | 
| 25624 | NodeClosed | StateTransition |  Uzel se úspěšně vypnul. | FabricNode | Informační | 
| 25626 | NodeAborted | StateTransition |  Uzel se nekorektně vypnul. | FabricNode | Chyba | 

**Události uzlu protokolovacího nástroje KTL** 

| ID události | Name | Kategorie | Popis |Zdroj (úloha) | Level |
| --- | --- | --- | --- | --- | --- | 
| 50187 | SharedLogWriteThrottled | Zdravotnictví | Zápisy do sdíleného protokolu jsou omezené. | KtlLoggerNode | Informační | 
| 50188 | SharedLogWriteUnthrottled | Zdravotnictví | Zápisy do sdíleného protokolu nejsou omezené. | KtlLoggerNode | Informační | 

## <a name="application-events"></a>Události aplikace

**Události životního cyklu aplikace**

| ID události | Name | Kategorie | Popis |Zdroj (úloha) | Level | 
| --- | --- | --- | --- | --- | --- | 
| 29620 | ApplicationCreated | Nejčastější | Byla vytvořena nová aplikace. | CM | Informační | 
| 29625 | ApplicationDeleted | Nejčastější | Existující aplikace byla odstraněna. | CM | Informační | 
| 23083 | ApplicationProcessExited | Nejčastější | Proces v rámci aplikace se ukončil. | Hostování | Informační | 

**Události upgradu aplikace**

Další podrobnosti o upgradech aplikací najdete [tady](service-fabric-application-upgrade.md).

| ID události | Name | Kategorie | Popis |Zdroj (úloha) | Level | 
| --- | --- | ---| --- | --- | --- | 
| 29621 | ApplicationUpgradeStarted | Upgrade | Byl spuštěn upgrade aplikace. | CM | Informační | 
| 29622 | ApplicationUpgradeCompleted | Upgrade | Upgrade aplikace byl dokončen. | CM | Informační | 
| 29623 | ApplicationUpgradeRollbackStarted | Upgrade | Upgrade aplikace se začal odvolat. |CM | Upozornění | 
| 29624 | ApplicationUpgradeRollbackCompleted | Upgrade | Byl dokončen návrat do upgradu aplikace | CM | Upozornění | 
| 29626 | ApplicationUpgradeDomainCompleted | Upgrade | Upgrade domény byl dokončen během upgradu aplikace. | CM | Informační | 

## <a name="service-events"></a>Události služby

**Události životního cyklu služby**

| ID události | Name | Kategorie | Popis |Zdroj (úloha) | Level | 
| --- | --- | ---| --- | --- | --- |
| 18657 | ServiceCreated | Nejčastější | Vytvořila se nová služba. | FM | Informační | 
| 18658 | ServiceDeleted | Nejčastější | Existující služba se odstranila. | FM | Informační | 

## <a name="partition-events"></a>Rozdělit události na oddíly

**Přesunout události oddílu**

| ID události | Name | Kategorie | Popis |Zdroj (úloha) | Level | 
| --- | --- | ---| --- | --- | --- |
| 18940 | PartitionReconfigured | Nejčastější | Konfigurace oddílu se dokončila. | RA | Informační | 

## <a name="replica-events"></a>Události repliky

**Události životního cyklu repliky**

| ID události | Name | Kategorie | Popis |Zdroj (úloha) | Level |
| --- | --- | ---| --- | --- | --- |
| 61701 | ReliableDictionaryOpened | Nejčastější | Byl otevřen spolehlivý slovník. | DistributedDictionary | Informační |
| 61702 | ReliableDictionaryClosed | Nejčastější | Spolehlivý slovník se zavřel. | DistributedDictionary | Informační |
| 61703 | ReliableDictionaryCheckpointRecovered | Nejčastější | Spolehlivý slovník obnovil svůj kontrolní bod. | DistributedDictionary | Informační |
| 61704 | ReliableDictionaryCheckpointFilesSent | Nejčastější | Replika odeslala soubory kontrolních bodů spolehlivého slovníku. | DistributedDictionary | Informační |
| 61705 | ReliableDictionaryCheckpointFilesReceived | Nejčastější | Replika přijala soubory kontrolních bodů spolehlivého slovníku. | DistributedDictionary | Informační |
| 61963 | ReliableQueueOpened | Nejčastější | Spolehlivá fronta byla otevřena. | DistributedQueue | Informační |
| 61964 | ReliableQueueClosed | Nejčastější | Spolehlivá fronta byla zavřena. | DistributedQueue | Informační |
| 61965 | ReliableQueueCheckpointRecovered | Nejčastější | Spolehlivá fronta obnovila svůj kontrolní bod. | DistributedQueue | Informační |
| 61966 | ReliableQueueCheckpointFilesSent | Nejčastější | Replika odeslala soubory kontrolních bodů spolehlivé fronty. | DistributedQueue | Informační |
| 63647 | ReliableQueueCheckpointFilesReceived | Nejčastější | Replika přijala soubory kontrolních bodů spolehlivé fronty. | DistributedQueue | Informační |
| 63648 | ReliableConcurrentQueueOpened | Nejčastější | Spolehlivá souběžná fronta byla otevřena. | ReliableConcurrentQueue | Informační |
| 63649 | ReliableConcurrentQueueClosed | Nejčastější | Spolehlivá souběžná fronta byla zavřena. | ReliableConcurrentQueue | Informační |
| 63650 | ReliableConcurrentQueueCheckpointRecovered | Nejčastější | Spolehlivá souběžná fronta obnovila svůj kontrolní bod. | ReliableConcurrentQueue | Informační |
| 61687 | TStoreError | Selhání | Spolehlivá kolekce přijala neočekávanou chybu. | TStore | Chyba |
| 63831 | PrimaryFullCopyInitiated | Nejčastější | Primární replika iniciovala úplnou kopii. | TReplicator | Informační |
| 63832 | PrimaryPartialCopyInitiated | Nejčastější | Primární replika iniciovala částečnou kopii. | TReplicator | Informační |
| 16831 | BuildIdleReplicaStarted | Nejčastější | Primární replika zahájila sestavování nečinné repliky. | Replikace | Informační |
| 16832 | BuildIdleReplicaCompleted | Nejčastější | U primární repliky se dokončilo vytváření nečinné repliky. | Replikace | Informační |
| 16833 | BuildIdleReplicaFailed | Nejčastější | Primární replika selhala při vytváření nečinné repliky. | Replikace | Upozornění |
| 16834 | PrimaryReplicationQueueFull | Zdravotnictví | Fronta replikace primární repliky je plná. | Replikace | Upozornění |
| 16835 | PrimaryReplicationQueueWarning | Zdravotnictví | Fronta replikace primární repliky je blízko úplného. | Replikace | Upozornění |
| 16836 | PrimaryReplicationQueueWarningMitigated | Zdravotnictví | Fronta replikace primární repliky je v pořádku. | Replikace | Informační |
| 16837 | SecondaryReplicationQueueFull | Zdravotnictví | Fronta replikace sekundární repliky je plná. | Replikace | Upozornění |
| 16838 | SecondaryReplicationQueueWarning | Zdravotnictví | Fronta replikace sekundární repliky je blízko úplného. | Replikace | Upozornění |
| 16839 | SecondaryReplicationQueueWarningMitigated | Zdravotnictví | Fronta replikace sekundární repliky je v pořádku. | Replikace | Informační |
| 16840 | PrimaryFaultedSlowSecondary | Zdravotnictví | U primární repliky došlo k chybě pomalé sekundární repliky. | Replikace | Upozornění |
| 16841 | ReplicatorFaulted | Zdravotnictví | V replice došlo k chybě. | Replikace | Upozornění |

## <a name="container-events"></a>Události kontejneru

**Události životního cyklu kontejneru** 

| ID události | Název | Description |Zdroj (úloha) | Level | Verze |
| --- | --- | ---| --- | --- | --- |
| 23074 | ContainerActivated | Kontejner se spustil. | Hostování | Informační | 1 |
| 23075 | ContainerDeactivated | Kontejner se zastavil. | Hostování | Informační | 1 |
| 23082 | ContainerExited | Kontejner se ukončil – Podívejte se na příznak UnexpectedTermination. | Hostování | Informační | 1 |

## <a name="health-reports"></a>Sestavy stavu

[Model Service Fabric Health](service-fabric-health-introduction.md) poskytuje bohatě, flexibilní a rozšiřitelné vyhodnocení stavu a vytváření sestav. Počínaje Service Fabric verze 6,2 se data o stavu zapisují jako události platformy, aby poskytovaly historické záznamy o stavu. Chcete-li udržet objem událostí stavu nízká, zapište pouze následující události Service Fabric:

* Všechny `Error` `Warning` sestavy nebo sestavy stavu
* `Ok` sestavy stavu během přechodů
* Po `Error` `Warning` vypršení platnosti události nebo stavu. Dá se použít k určení, jak dlouho není entita v pořádku.

**Události sestav stavu clusteru**

| ID události | Název | Description |Zdroj (úloha) | Level | Verze |
| --- | --- | --- | --- | --- | --- |
| 54428 | ClusterNewHealthReport | K dispozici je nová Sestava stavu clusteru. | HM | Informační | 1 |
| 54437 | ClusterHealthReportExpired | Platnost existující sestavy stavu clusteru vypršela. | HM | Informační | 1 |

**Události sestav stavu uzlu**

| ID události | Název | Description |Zdroj (úloha) | Level | Verze |
| --- | --- | ---| --- | --- | --- |
| 54423 | NodeNewHealthReport | Je k dispozici nová Sestava stavu uzlu. | HM | Informační | 1 |
| 54432 | NodeHealthReportExpired | Platnost existující sestavy stavu uzlu vypršela. | HM | Informační | 1 |

**Události sestavy stavu aplikace**

| ID události | Název | Description |Zdroj (úloha) | Level | Verze |
| --- | --- | ---| --- | --- | --- |
| 54425 | ApplicationNewHealthReport | Byla vytvořena nová sestava o stavu aplikace. Toto je pro nenasazené aplikace. | HM | Informační | 1 |
| 54426 | DeployedApplicationNewHealthReport | Vytvořila se nová Sestava stavu nasazené aplikace. | HM | Informační | 1 |
| 54427 | DeployedServicePackageNewHealthReport | Byla vytvořena nová nasazená Sestava služby Service Health. | HM | Informační | 1 |
| 54434 | ApplicationHealthReportExpired | Platnost existující sestavy stavu aplikace vypršela. | HM | Informační | 1 |
| 54435 | DeployedApplicationHealthReportExpired | Platnost existující nasazené sestavy o stavu aplikace vypršela. | HM | Informační | 1 |
| 54436 | DeployedServicePackageHealthReportExpired | Platnost existující nasazené sestavy služby Service Health vypršela. | HM | Informační | 1 |

**Události sestav stavu služby**

| ID události | Název | Description |Zdroj (úloha) | Level | Verze |
| --- | --- | ---| --- | --- | --- |
| 54424 | ServiceNewHealthReport | Byla vytvořena nová Sestava služby Service Health. | HM | Informační | 1 |
| 54433 | ServiceHealthReportExpired | Platnost existující sestavy služby Service Health vypršela. | HM | Informační | 1 |

**Události sestav stavu oddílu**

| ID události | Název | Description |Zdroj (úloha) | Level | Verze |
| --- | --- | ---| --- | --- | --- |
| 54422 | PartitionNewHealthReport | Vytvořila se nová Sestava stavu oddílů. | HM | Informační | 1 |
| 54431 | PartitionHealthReportExpired | Platnost existující sestavy stavu oddílu vypršela. | HM | Informační | 1 |

**Události sestav stavu repliky**

| ID události | Název | Description |Zdroj (úloha) | Level | Verze |
| --- | --- | ---| --- | --- | --- |
| 54429 | StatefulReplicaNewHealthReport | Byla vytvořena stavová zpráva o stavu repliky. | HM | Informační | 1 |
| 54430 | StatelessInstanceNewHealthReport | Byla vytvořena nová stavová zpráva o stavu instance. | HM | Informační | 1 |
| 54438 | StatefulReplicaHealthReportExpired | Platnost stávající stavové repliky stavu vypršela. | HM | Informační | 1 |
| 54439 | StatelessInstanceHealthReportExpired | Platnost stávající stavové sestavy stavu bez stavů vypršela. | HM | Informační | 1 |

## <a name="chaos-testing-events"></a>Události testování chaos 

**Události relace chaos**

| ID události | Název | Description |Zdroj (úloha) | Level | Verze |
| --- | --- | ---| --- | --- | --- |
| 50021 | ChaosStarted | Spustila se relace testování chaos. | Testovatelnosti | Informační | 1 |
| 50023 | ChaosStopped | Relace testování chaos se zastavila. | Testovatelnosti | Informační | 1 |

**Události uzlu chaos**

| ID události | Název | Description |Zdroj (úloha) | Level | Verze |
| --- | --- | ---| --- | --- | --- |
| 50033 | ChaosNodeRestartScheduled | Uzel se naplánoval restart jako součást relace testování chaos. | Testovatelnosti | Informační | 1 |
| 50087 | ChaosNodeRestartCompleted | Restart uzlu byl dokončen v rámci relace testování chaos | Testovatelnosti | Informační | 1 |

**Události aplikace chaos**

| ID události | Název | Description |Zdroj (úloha) | Level | Verze |
| --- | --- | ---| --- | --- | --- |
| 50053 | ChaosCodePackageRestartScheduled | Během relace testování chaos se naplánoval restart balíčku kódu. | Testovatelnosti | Informační | 1 |
| 50101 | ChaosCodePackageRestartCompleted | Během relace testování chaos se dokončil restart balíčku kódu. | Testovatelnosti | Informační | 1 |

**Události oddílů chaos**

| ID události | Název | Description |Zdroj (úloha) | Level | Verze |
| --- | --- | ---| --- | --- | --- |
| 50069 | ChaosPartitionPrimaryMoveScheduled | Primární oddíl se naplánoval na přesun v rámci relace chaos testování. | Testovatelnosti | Informační | 1 |
| 50077 | ChaosPartitionSecondaryMoveScheduled | Sekundární oddíl má naplánovaný přesun v rámci relace chaos testování. | Testovatelnosti | Informační | 1 |
| 65003 | PartitionPrimaryMoveAnalysis | K dispozici je hlubší analýza primárního oddílu přesunutí. | Testovatelnosti | Informační | 1 |

**Události repliky chaos**

| ID události | Název | Description |Zdroj (úloha) | Level | Verze |
| --- | --- | ---| --- | --- | --- |
| 50047 | ChaosReplicaRestartScheduled | Restart repliky byl naplánován jako součást relace testování chaos. | Testovatelnosti | Informační | 1 |
| 50051 | ChaosReplicaRemovalScheduled | Odebrání repliky bylo naplánováno jako součást relace testování chaos. | Testovatelnosti | Informační | 1 |
| 50093 | ChaosReplicaRemovalCompleted | Odebrání repliky bylo dokončeno jako součást relace testování chaos. | Testovatelnosti | Informační | 1 |

## <a name="other-events"></a>Další události

**Události korelace**

| ID události | Název | Description |Zdroj (úloha) | Level | Verze |
| --- | --- | ---| --- | --- | --- |
| 65011 | CorrelationOperational | Zjistila se korelace. | Testovatelnosti | Informační | 1 |

## <a name="events-prior-to-version-62"></a>Události před verzí 6,2

Zde je vyčerpávající seznam událostí poskytovaných Service Fabric před verzí 6,2.

| ID události | Name | Zdroj (úloha) | Level |
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

## <a name="next-steps"></a>Další kroky

* Získejte přehled [diagnostiky v Service Fabric](service-fabric-diagnostics-overview.md)
* Další informace o Eventstoru najdete v článku [Přehled nástroje Service Fabric eventstoru](service-fabric-diagnostics-eventstore.md) .
* Úprava konfigurace [Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md) pro shromažďování dalších protokolů
* [Nastavení Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) pro zobrazení protokolů provozních kanálů
