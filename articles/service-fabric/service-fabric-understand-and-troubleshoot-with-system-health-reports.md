---
title: Řešení potíží se systémovými stavovými sestavami | Dokumentace Microsoftu
description: Popisuje sestav o stavu odeslané komponenty Azure Service Fabric a jejich využití pro řešení problémů s aplikací nebo řešení potíží clusteru
services: service-fabric
documentationcenter: .net
author: oanapl
manager: chackdan
editor: ''
ms.assetid: 52574ea7-eb37-47e0-a20a-101539177625
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2018
ms.author: oanapl
ms.openlocfilehash: b190db401b8ae31582ea31cf59d30f20baccf8c7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67060360"
---
# <a name="use-system-health-reports-to-troubleshoot"></a>Řešení problémů pomocí sestav o stavu systému
Komponenty služby Azure Service Fabric vám na všechny entity v clusteru předem připravené sestav stravu systému. [Health store](service-fabric-health-introduction.md#health-store) vytvoří a odstraní entity, které jsou založeny na sestavách systému. Je také uspořádány v hierarchii, který explicitně zaznamenává interakce entity.

> [!NOTE]
> Koncepce související se stavem, další informace najdete v [modelu stavu Service Fabric](service-fabric-health-introduction.md).
> 
> 

Systémovými stavovými sestavami poskytují přehled o clusteru a funkčnost aplikace a příznak problémy. Pro aplikace a služby systémovými stavovými sestavami ověřte, že entity jsou implementovány a chovají správně z hlediska Service Fabric. Sestavy neposkytují žádné sledování stavu obchodní logiku, služby nebo detekce procesy, které nereagují. Uživatel služby obohatit tak data stavu informace specifické pro jejich logiku.

> [!NOTE]
> Sestav o stavu odeslané watchdogs uživatel vidí jenom *po* součásti systému vytvoření entity. Při odstranění entity úložišti health automaticky odstraní všechny sestav stavu s ním spojená. Totéž platí, když je vytvořena nová instance entity. Příkladem je, když je vytvořena nová instance repliky stavové trvalé služby. Všechny sestavy přidružené k původní instanci se odstraní a vymaže se z úložiště.
> 
> 

Součásti systému sestavy, jsou identifikované zdroj, který začíná "**systému.** " Předpona. Watchdogs nemůžou používat stejnou předponu jejich zdroje jako odmítají sestavy s neplatnými parametry.

Podívejme se na některé sestavy systému pochopit, co je spustí a zjistěte, jak opravte potenciální problémy, které reprezentují.

> [!NOTE]
> Service Fabric se nadále přidání sestav na podmínkách zájmu, které zlepšují přehled o co se děje v clusteru a aplikace. Existující sestavy dá vylepšit s dalšími podrobnostmi, které vám pomohou vyřešit problém rychleji.
> 
> 

## <a name="cluster-system-health-reports"></a>Cluster systémovými stavovými sestavami
V health store se automaticky vytvoření entity stavu clusteru. Pokud všechno bude fungovat správně, nemá sestavu system.

### <a name="neighborhood-loss"></a>Detekovaná sousední ztráta
**System.Federation** hlásí chybu, když zjistí detekovaná sousední ztráta. Sestava je z jednotlivých uzlů a ID uzlu je součástí názvu vlastnosti. Pokud jeden okolí dojde ke ztrátě celý prstenec Service Fabric, obvykle můžete očekávat dvě události, které představují obou stranách sestavy mezery. Pokud další sousedství jsou ztraceny, existují další události.

Sestava udává časový limit globální zapůjčení jako time to live (TTL). Sestava se zopakuje každý poloviční dobu trvání TTL tak dlouho, dokud podmínka zůstává aktivní. Událost je automaticky odstraněno, jakmile vyprší jeho platnost. Vypršela platnost odebrat v případě zaručuje, že sestava je vymaže se z health store správně, i v případě, že uzel vytváření sestav je mimo provoz.

* **SourceId**: System.Federation
* **Vlastnost**: Začíná **detekovaná sousední** a obsahuje informace o uzlu.
* **Další kroky**: Zjistěte, proč je ztraceno okolí. Například zkontrolujte komunikaci mezi uzly clusteru.

### <a name="rebuild"></a>Opětovné sestavení

Služba Správce převzetí služeb při selhání (FM) spravuje informace o uzlech clusteru. Při převzetí služeb při selhání ztratí svoje data a přejde do ztrátu dat, nezaručuje, že na něm uvedeny nejaktuálnější informace o uzlech clusteru. V tomto případě systém prochází opětovné sestavení a System.FM – shromažďuje data ze všech uzlů v clusteru, aby bylo možné znovu sestavte svůj stav. V některých případech z důvodu sítě nebo problémů uzlu, opětovné sestavení můžete získat zablokuje nebo zastaven a proces. Stejné může dojít ke službě Master Správce převzetí služeb při selhání (FMM). FMM je Bezstavová systémová služba, která sleduje kde jsou všechny FMs v clusteru. Uzel s ID nejblíže 0 je vždy FMM primární. Získá ztracené tento uzel se aktivuje opětovné sestavení.
Pokud jeden z předchozích podmínek dojde, **System.FM –** nebo **System.FMM** příznaky prostřednictvím zprávu o chybách. Opětovné sestavení se mohly zaseknout v jednom ze dvou fází:

* **Čeká se na vysílání**: FM/FMM čeká na odpověď vysílání všesměrné zprávy z jiných uzlů.

  * **Další kroky**: Zjistěte, zda je problém se síťovým připojením mezi uzly.
* **Čekání na uzly**: FM/FMM již přijali vysílanou odpověď z jiných uzlů a čeká na odpověď od konkrétním uzlům. Sestava stavu zobrazuje seznam uzlů, pro které FM/FMM je čekání na odpověď.
   * **Další kroky**: Zjistěte síťové připojení mezi FM/FMM a uvedené uzly. Prozkoumání jednotlivých uvedených uzel pro další informace o možných problémech.

* **SourceID**: System.FM – nebo System.FMM
* **Vlastnost**: Znovu sestavte.
* **Další kroky**: Zjistěte síťové připojení mezi uzly a také stav žádné konkrétní uzlů, které jsou uvedeny v popisu sestavy stavu.

### <a name="seed-node-status"></a>Počáteční stav uzlu
**System.FM –** sestavy varováním na úrovni clusteru, pokud jsou některé uzly počáteční hodnoty není v pořádku. Počáteční uzly jsou uzly, které zachovat dostupnost základního clusteru. Tyto uzly pomáhají zajistit, že cluster zůstane až vytvořením zapůjčení s ostatními uzly a slouží jako tiebreakers během určité druhy chyb v síti. Pokud nejsou opět nepřipojí většinou počáteční uzly jsou vypnuté v clusteru, cluster automaticky ukončí. 

Počáteční uzel je v pořádku, pokud jeho stav uzlu je mimo provoz, odebráno nebo neznámý.
Sestava upozornění pro počáteční uzel stav se zobrazí seznam všech není v pořádku, počáteční uzly s podrobnými informacemi.

* **SourceID**: System.FM –
* **Vlastnost**: SeedNodeStatus
* **Další kroky**: Pokud se toto upozornění se zobrazí v clusteru, postupujte podle následujících pokynů to opravit: U clusteru se systémem Service Fabric verze 6.5 nebo vyšší: Pro cluster Service Fabric v Azure po počáteční uzel ocitne mimo provoz, Service Fabric se pokusí automaticky ho změnit na uzlu – počáteční hodnota. Aby to chcete udělat, ujistěte se, zkontrolujte počet uzlů – počáteční hodnota v primární typ uzlu je větší nebo rovna počtu dolů počáteční uzly. V případě potřeby přidejte více uzlů do primární typ uzlu dosáhnout.
V závislosti na stavu clusteru může trvat nějakou dobu, pokud chcete problém vyřešit. Až to uděláte, jsou automaticky vymazány sestavy upozornění.

Samostatný cluster Service Fabric zrušte sestavy upozornění počáteční uzly muset se obnoví dobrý stav. V závislosti na tom, proč počáteční uzly jsou v pořádku, třeba provést různé akce: Pokud je uzel počáteční hodnoty dolů, uživatelé potřebují, zobrazíte takový uzel počáteční hodnoty; Pokud je uzel počáteční hodnoty odebrané nebo neznámá, tento uzel počáteční hodnoty [potřeba odebrat z clusteru](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-windows-server-add-remove-nodes).
Sestava upozornění jsou automaticky vymazány, až budou všechny počáteční uzly v dobrém stavu.

Pro cluster Service Fabric verze starší než verze 6.5: V takovém případě upozornění sestavy je potřeba ručně vymazat. **Uživatelé by měl Ujistěte se, že před vymazáním sestavy se obnoví dobrý stav počáteční uzly**: Pokud je počátečním uzlem je vypnutý, uživatelé potřebují zobrazíte takový uzel počáteční hodnoty; Pokud je uzel počáteční hodnoty odebrané nebo neznámý, takový uzel počáteční hodnoty musí být odstraněno z clusteru.
Po počáteční uzly se obnoví dobrý stav, použijte následující příkaz z Powershellu [vymazat sestavy upozornění](https://docs.microsoft.com/powershell/module/servicefabric/send-servicefabricclusterhealthreport):

```powershell
PS C:\> Send-ServiceFabricClusterHealthReport -SourceId "System.FM" -HealthProperty "SeedNodeStatus" -HealthState OK

## Node system health reports
System.FM, which represents the Failover Manager service, is the authority that manages information about cluster nodes. Each node should have one report from System.FM showing its state. The node entities are removed when the node state is removed. For more information, see [RemoveNodeStateAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.clustermanagementclient.removenodestateasync).

### Node up/down
System.FM reports as OK when the node joins the ring (it's up and running). It reports an error when the node departs the ring (it's down, either for upgrading or simply because it has failed). The health hierarchy built by the health store acts on deployed entities in correlation with System.FM node reports. It considers the node a virtual parent of all deployed entities. The deployed entities on that node are exposed through queries if the node is reported as up by System.FM, with the same instance as the instance associated with the entities. When System.FM reports that the node is down or restarted, as a new instance, the health store automatically cleans up the deployed entities that can exist only on the down node or on the previous instance of the node.

* **SourceId**: System.FM
* **Property**: State.
* **Next steps**: If the node is down for an upgrade, it should come back up after it's been upgraded. In this case, the health state should switch back to OK. If the node doesn't come back or it fails, the problem needs more investigation.

The following example shows the System.FM event with a health state of OK for node up:

```powershell
PS C:\> Get-ServiceFabricNodeHealth  _Node_0

NodeName              : _Node_0
AggregatedHealthState : Ok
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 8
                        SentAt                : 7/14/2017 4:54:51 PM
                        ReceivedAt            : 7/14/2017 4:55:14 PM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
```


### <a name="certificate-expiration"></a>Vypršení platnosti certifikátu
**System.FabricNode** sestavy upozornění, když certifikáty používané uzel blíží vypršení platnosti. Existují tři certifikáty podle počtu uzlů: **Certificate_cluster**, **Certificate_server**, a **Certificate_default_client**. Po vypršení platnosti alespoň dva týdny, je sestava Stav v pořádku. Po vypršení platnosti je během dvou týdnů, typ sestavy je upozornění. Hodnota TTL z těchto událostí je nekonečné, jsou odebírají a Jestliže uzel opustí clusteru.

* **SourceId**: System.FabricNode
* **Vlastnost**: Začíná **certifikát** a obsahuje další informace o typu certifikátu.
* **Další kroky**: Aktualizujte certifikáty, pokud se blíží vypršení platnosti.

### <a name="load-capacity-violation"></a>Načíst porušení kapacity
Vyrovnávání zatížení Service Fabric oznámí upozornění při zjištění porušení kapacity uzlu.

* **SourceId**: System.PLB
* **Vlastnost**: Začíná **kapacity**.
* **Další kroky**: Zkontrolujte zadané metriky a zobrazit aktuální kapacita na uzlu.

### <a name="node-capacity-mismatch-for-resource-governance-metrics"></a>Neshoda kapacity uzlů pro metriky zásad správného řízení prostředků
System.Hosting sestav, upozornění, pokud definované kapacity uzlů v manifestu clusteru jsou větší než skutečné kapacity uzlů pro metriky zásad správného řízení prostředků (paměti a jader procesoru). Sestava stavu se zobrazí, když balíček první služba, která používá [zásady správného řízení prostředků](service-fabric-resource-governance.md) zaregistruje pro zadaný uzel.

* **SourceId**: System.Hosting
* **Vlastnost**: **ResourceGovernance**.
* **Další kroky**: Tento problém může být problém, protože řízení služby balíčky nejsou vynucená podle očekávání a [zásady správného řízení prostředků](service-fabric-resource-governance.md) nefunguje správně. Aktualizujte manifest clusteru kapacity správný uzel pro tyto metriky, nebo nemáte zadejte je a nechat Service Fabric automaticky zjišťovat prostředky k dispozici.

## <a name="application-system-health-reports"></a>Aplikace systémovými stavovými sestavami
System.CM, který představuje službu, kterou Správce clusteru, je, že úřad, který spravuje informace o aplikaci.

### <a name="state"></a>Stav
System.CM hlásí jako OK při vytvoření nebo aktualizace aplikace. Při odstranění aplikace tak, aby bylo možné odebrat z úložiště, informuje o stavu úložiště.

* **SourceId**: System.CM
* **Vlastnost**: Stav.
* **Další kroky**: Pokud aplikace byly vytvořeny nebo aktualizovány, měl by obsahovat sestava stavu Správce clusteru. Zadání dotazu a v opačném případě zkontrolujte stav aplikace. Například použijte rutinu prostředí PowerShell **Get ServiceFabricApplication - ApplicationName** *applicationName*.

Následující příklad ukazuje na události stavu **fabric: / WordCount** aplikace:

```powershell
PS C:\> Get-ServiceFabricApplicationHealth fabric:/WordCount -ServicesFilter None -DeployedApplicationsFilter None -ExcludeHealthStatistics

ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Ok
ServiceHealthStates             : None
DeployedApplicationHealthStates : None
HealthEvents                    : 
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 282
                                  SentAt                : 7/13/2017 5:57:05 PM
                                  ReceivedAt            : 7/14/2017 4:55:10 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Error->Ok = 7/13/2017 5:57:05 PM, LastWarning = 1/1/0001 12:00:00 AM
```

## <a name="service-system-health-reports"></a>Služba systémovými stavovými sestavami
System.FM –, který představuje službu, kterou správce převzetí služeb při selhání, je, že úřad, který spravuje informace o službách.

### <a name="state"></a>Stav
System.FM – hlásí jako OK po vytvoření služby. Odstraní entitu z health store při odstraňování služby.

* **SourceId**: System.FM –
* **Vlastnost**: Stav.

Následující příklad ukazuje události stavu služby **fabric: / WordCount/WordCountWebService**:

```powershell
PS C:\> Get-ServiceFabricServiceHealth fabric:/WordCount/WordCountWebService -ExcludeHealthStatistics


ServiceName           : fabric:/WordCount/WordCountWebService
AggregatedHealthState : Ok
PartitionHealthStates : 
                        PartitionId           : 8bbcd03a-3a53-47ec-a5f1-9b77f73c53b2
                        AggregatedHealthState : Ok
                        
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 14
                        SentAt                : 7/13/2017 5:57:05 PM
                        ReceivedAt            : 7/14/2017 4:55:10 PM
                        TTL                   : Infinite
                        Description           : Service has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="service-correlation-error"></a>Chyba služby korelace
**System.PLB** hlásí chybu, když zjistí, že aktualizace služby jsou korelována se jiné službě, která vytváří řetěz spřažení. Sestava se po úspěšné aktualizaci se stane, vymaže.

* **SourceId**: System.PLB
* **Vlastnost**: **Popis ServiceDescription**.
* **Další kroky**: Projděte si popisy korelační služby.

## <a name="partition-system-health-reports"></a>Oddíl systémovými stavovými sestavami
System.FM –, který představuje službu, kterou správce převzetí služeb při selhání, je, že úřad, který spravuje informace o oddílech služby.

### <a name="state"></a>Stav
System.FM – hlásí jako OK při vytvoření oddílu a je v pořádku. Odstraní entitu z health store při odstranění oddílu.

Pokud oddíl nižší než minimální replik, hlásí chybu. Pokud oddíl není pod minimální replik, ale jeho nedosahuje počtu cílových replik, sestavy upozornění. Pokud oddíl je ve ztrátě kvora, System.FM – nahlásí chybu.

Další významné události zahrnovat upozornění rekonfiguraci trvá déle, než se očekávalo, a při sestavování trvá déle, než se očekávalo. Očekávané doby pro sestavení a změny konfigurace se dají konfigurovat podle scénářů služby. Například pokud má terabajt stavu, jako je Azure SQL Database, služba sestavení trvá déle, než pro službu s menším objemem stavu.

* **SourceId**: System.FM –
* **Vlastnost**: Stav.
* **Další kroky**: Pokud stav není v pořádku, je možné, že některé repliky nebyly vytvořené, otevřené nebo povýšen na primární nebo sekundární správně. 

Pokud se popis popisuje ztráty kvora, pak zkoumání stavu podrobné sestavy pro repliky, které jsou vypnuté a jejich uvedení zálohování pomáhá vám oddílu zpět do režimu online.

Pokud se popis popisuje oddíl zablokované ve [Rekonfigurace](service-fabric-concepts-reconfiguration.md), pak sestava stavu na primární replice poskytuje další informace.

Pro jiné System.FM – sestav o stavu bude zprávy o repliky nebo oddíl nebo služby z dalších komponent systému. 

Následující příklady popisují některé z těchto sestav. 

Následující příklad ukazuje oddíl v pořádku:

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountWebService | Get-ServiceFabricPartitionHealth -ExcludeHealthStatistics -ReplicasFilter None

PartitionId           : 8bbcd03a-3a53-47ec-a5f1-9b77f73c53b2
AggregatedHealthState : Ok
ReplicaHealthStates   : None
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 70
                        SentAt                : 7/13/2017 5:57:05 PM
                        ReceivedAt            : 7/14/2017 4:55:10 PM
                        TTL                   : Infinite
                        Description           : Partition is healthy.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
```

Následující příklad ukazuje stav oddíl nedosahuje počtu cílových replik. Dalším krokem je získání popisu oddílu, který ukazuje, jak je nakonfigurovaný: **MinReplicaSetSize** je třetí a **TargetReplicaSetSize** je sedm. Potom Získejte počet uzlů v clusteru, který v tomto případě je pět. Ano v tomto případě dvě repliky nelze umístit, protože cílový počet replik je vyšší než počet dostupných uzlů.

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricPartitionHealth -ReplicasFilter None -ExcludeHealthStatistics


PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.
                        
ReplicaHealthStates   : None
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 123
                        SentAt                : 7/14/2017 4:55:39 PM
                        ReceivedAt            : 7/14/2017 4:55:44 PM
                        TTL                   : Infinite
                        Description           : Partition is below target replica or instance count.
                        fabric:/WordCount/WordCountService 7 2 af2e3e44-a8f8-45ac-9f31-4093eb897600
                          N/S Ready _Node_2 131444422260002646
                          N/S Ready _Node_4 131444422293113678
                          N/S Ready _Node_3 131444422293113679
                          N/S Ready _Node_1 131444422293118720
                          N/P Ready _Node_0 131444422293118721
                          (Showing 5 out of 5 replicas. Total available replicas: 5)
                        
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 7/14/2017 4:55:44 PM, LastOk = 1/1/0001 12:00:00 AM
                        
                        SourceId              : System.PLB
                        Property              : ServiceReplicaUnplacedHealth_Secondary_af2e3e44-a8f8-45ac-9f31-4093eb897600
                        HealthState           : Warning
                        SequenceNumber        : 131445250939703027
                        SentAt                : 7/14/2017 4:58:13 PM
                        ReceivedAt            : 7/14/2017 4:58:14 PM
                        TTL                   : 00:01:05
                        Description           : The Load Balancer was unable to find a placement for one or more of the Service's Replicas:
                        Secondary replica could not be placed due to the following constraints and properties:  
                        TargetReplicaSetSize: 7
                        Placement Constraint: N/A
                        Parent Service: N/A
                        
                        Constraint Elimination Sequence:
                        Existing Secondary Replicas eliminated 4 possible node(s) for placement -- 1/5 node(s) remain.
                        Existing Primary Replica eliminated 1 possible node(s) for placement -- 0/5 node(s) remain.
                        
                        Nodes Eliminated By Constraints:
                        
                        Existing Secondary Replicas -- Nodes with Partition's Existing Secondary Replicas/Instances:
                        --
                        FaultDomain:fd:/4 NodeName:_Node_4 NodeType:NodeType4 UpgradeDomain:4 UpgradeDomain: ud:/4 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/3 NodeName:_Node_3 NodeType:NodeType3 UpgradeDomain:3 UpgradeDomain: ud:/3 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/2 NodeName:_Node_2 NodeType:NodeType2 UpgradeDomain:2 UpgradeDomain: ud:/2 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/1 NodeName:_Node_1 NodeType:NodeType1 UpgradeDomain:1 UpgradeDomain: ud:/1 Deactivation Intent/Status: None/None
                        
                        Existing Primary Replica -- Nodes with Partition's Existing Primary Replica or Secondary Replicas:
                        --
                        FaultDomain:fd:/0 NodeName:_Node_0 NodeType:NodeType0 UpgradeDomain:0 UpgradeDomain: ud:/0 Deactivation Intent/Status: None/None
                        
                        
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 7/14/2017 4:56:14 PM, LastOk = 1/1/0001 12:00:00 AM

PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | select MinReplicaSetSize,TargetReplicaSetSize

MinReplicaSetSize TargetReplicaSetSize
----------------- --------------------
                2                    7                        

PS C:\> @(Get-ServiceFabricNode).Count
5
```

Následující příklad ukazuje stav oddílu, který se zasekla v kvůli uživatele není dodržením zrušení rekonfiguraci token **RunAsync** metody. Zkoumání sestava stavu jakákoli replika označena jako primární (P) může pomoct přejít k další nižší na problém.

```powershell
PS C:\utilities\ServiceFabricExplorer\ClientPackage\lib> Get-ServiceFabricPartitionHealth 0e40fd81-284d-4be4-a665-13bc5a6607ec -ExcludeHealthStatistics 


PartitionId           : 0e40fd81-284d-4be4-a665-13bc5a6607ec
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', 
                        ConsiderWarningAsError=false.
                                               
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 7
                        SentAt                : 8/27/2017 3:43:09 AM
                        ReceivedAt            : 8/27/2017 3:43:32 AM
                        TTL                   : Infinite
                        Description           : Partition reconfiguration is taking longer than expected.
                        fabric:/app/test1 3 1 0e40fd81-284d-4be4-a665-13bc5a6607ec
                          P/S Ready Node1 131482789658160654
                          S/P Ready Node2 131482789688598467
                          S/S Ready Node3 131482789688598468
                          (Showing 3 out of 3 replicas. Total available replicas: 3)                        
                        
                        For more information see: https://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Ok->Warning = 8/27/2017 3:43:32 AM, LastError = 1/1/0001 12:00:00 AM
```
Tato sestava stavu zobrazuje stav repliky probíhá Rekonfigurace oddílu: 

```
  P/S Ready Node1 131482789658160654
  S/P Ready Node2 131482789688598467
  S/S Ready Node3 131482789688598468
```

Pro každou repliku obsahuje sestava stavu:
- Předchozí konfigurace role
- Aktuální konfigurace role
- [Stav repliky](service-fabric-concepts-replica-lifecycle.md)
- Uzel, na kterém běží repliky
- ID repliky

V případě stejně jako u příkladu další šetření je potřeba. Prozkoumat stav každé jednotlivé repliky počínaje repliky označené jako `Primary` a `Secondary` (131482789658160654 a 131482789688598467) v předchozím příkladu.

### <a name="replica-constraint-violation"></a>Porušení omezení repliky
**System.PLB** zprávy upozornění, pokud zjistí porušení omezení repliky a nelze umístit všechny repliky oddílů. Podrobnosti sestavy zobrazit které omezení a zabránit vlastnosti umístění repliky.

* **SourceId**: System.PLB
* **Vlastnost**: Začíná **ReplicaConstraintViolation**.

## <a name="replica-system-health-reports"></a>Repliky systémovými stavovými sestavami
**System.RA**, která představuje součást agent Rekonfigurace je autorita pro stav replika.

### <a name="state"></a>Stav
System.RA sestavy OK po vytvoření repliky.

* **SourceId**: System.RA
* **Vlastnost**: Stav.

Následující příklad ukazuje repliku v pořádku:

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricReplica | where {$_.ReplicaRole -eq "Primary"} | Get-ServiceFabricReplicaHealth

PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
ReplicaId             : 131444422293118721
AggregatedHealthState : Ok
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131445248920273536
                        SentAt                : 7/14/2017 4:54:52 PM
                        ReceivedAt            : 7/14/2017 4:55:13 PM
                        TTL                   : Infinite
                        Description           : Replica has been created._Node_0
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/14/2017 4:55:13 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="replicaopenstatus-replicaclosestatus-replicachangerolestatus"></a>ReplicaOpenStatus, ReplicaCloseStatus, ReplicaChangeRoleStatus
Tato vlastnost se používá k označení objevily chyby nebo upozornění při pokusu o otevření repliky, zavření repliky nebo přechod na jiný repliku z jedné role. Další informace najdete v tématu [životní cyklus replik](service-fabric-concepts-replica-lifecycle.md). Chyby může být výjimky vyvolané z volání rozhraní API nebo selhání hostitelského procesu služby během této doby. Pro selhání kvůli rozhraní API volá C# kódu, Service Fabric přidá do sestavy stavu trasování zásobníku a výjimka.

Tato upozornění stavu jsou vyvolány po opakovaném pokusu akce místně některé počet pokusů (v závislosti na zásadách). Service Fabric znovu pokusí o akce až po dosažení maximálního povoleného počtu. Po dosažení maximálního povoleného počtu dosažení, může pokusit tak, aby fungoval k napravení situace. Tento pokus může způsobit, že tato upozornění na získat zrušeno, protože umožňuje na akce na tomto uzlu. Například pokud replika se nedaří otevřít na uzlu, Service Fabric vyvolá upozornění stavu. Pokud replika neotevře, Service Fabric funguje pro automatickou opravu. Tato akce může zahrnovat pokusu o stejnou operaci v jiném uzlu. Tento pokus způsobí upozornění aktivována pro tuto repliku vymazání. 

* **SourceId**: System.RA
* **Vlastnost**: **ReplicaOpenStatus**, **ReplicaCloseStatus**, a **ReplicaChangeRoleStatus**.
* **Další kroky**: Prošetření kódu služby nebo výpisy Pokud chcete určit, proč se selháním operace.

Následující příklad ukazuje stav repliky, která vyvolává `TargetInvocationException` z jeho metodu open. Popis obsahuje bodem selhání, **IStatefulServiceReplica.Open**, typ výjimky **typu TargetInvocationException**a trasování zásobníku.

```powershell
PS C:\> Get-ServiceFabricReplicaHealth -PartitionId 337cf1df-6cab-4825-99a9-7595090c0b1b -ReplicaOrInstanceId 131483509874784794


PartitionId           : 337cf1df-6cab-4825-99a9-7595090c0b1b
ReplicaId             : 131483509874784794
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='ReplicaOpenStatus', HealthState='Warning', 
                        ConsiderWarningAsError=false.
                        
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : ReplicaOpenStatus
                        HealthState           : Warning
                        SequenceNumber        : 131483510001453159
                        SentAt                : 8/27/2017 11:43:20 PM
                        ReceivedAt            : 8/27/2017 11:43:21 PM
                        TTL                   : Infinite
                        Description           : Replica had multiple failures during open on _Node_0 API call: IStatefulServiceReplica.Open(); Error = System.Reflection.TargetInvocationException (-2146232828)
Exception has been thrown by the target of an invocation.
   at Microsoft.ServiceFabric.Replicator.RecoveryManager.d__31.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Replicator.LoggingReplicator.d__137.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Replicator.DynamicStateManager.d__109.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Replicator.TransactionalReplicator.d__79.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Replicator.StatefulServiceReplica.d__21.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Services.Runtime.StatefulServiceReplicaAdapter.d__0.MoveNext()

    For more information see: https://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/27/2017 11:43:21 PM, LastOk = 1/1/0001 12:00:00 AM                        
```

Následující příklad ukazuje, která neustále selhává během zavírání repliky:

```powershell
C:>Get-ServiceFabricReplicaHealth -PartitionId dcafb6b7-9446-425c-8b90-b3fdf3859e64 -ReplicaOrInstanceId 131483565548493142


PartitionId           : dcafb6b7-9446-425c-8b90-b3fdf3859e64
ReplicaId             : 131483565548493142
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='ReplicaCloseStatus', HealthState='Warning', 
                        ConsiderWarningAsError=false.
                        
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : ReplicaCloseStatus
                        HealthState           : Warning
                        SequenceNumber        : 131483565611258984
                        SentAt                : 8/28/2017 1:16:01 AM
                        ReceivedAt            : 8/28/2017 1:16:03 AM
                        TTL                   : Infinite
                        Description           : Replica had multiple failures during close on _Node_1. The application 
                        host has crashed.
                        
                        For more information see: https://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/28/2017 1:16:03 AM, LastOk = 1/1/0001 12:00:00 AM
```

### <a name="reconfiguration"></a>Rekonfigurace
Tato vlastnost se používá k označení repliky provádění [Rekonfigurace](service-fabric-concepts-reconfiguration.md) zjistí, že rekonfiguraci je zastaven a proces nebo jsou zablokované. Tato sestava stavu může být aktuální role je primární, s výjimkou případů primární Rekonfigurace odkládacího souboru, kde může být v replice, která je právě snížena z primárního do aktivní sekundární repliky.

Rekonfiguraci může být zablokované pro jednu z následujících důvodů:

- Akce na místní replika, replika stejné jako ta, která provádí změny konfigurace, se nedokončuje. V takovém případě zkoumání sestav o stavu této repliky z jiných komponent, System.RAP nebo System.RE, může poskytnout další informace.

- Akce se nedokončuje na vzdálené repliky. Repliky, které čekají na vyřízení akce jsou uvedeny v sestavě o stavu. Na sestav o stavu pro tyto vzdálené repliky by měl provést další šetření. Také může být problémy s komunikací mezi tento uzel a vzdálené uzlu.

Ve výjimečných případech může být rekonfiguraci zablokované kvůli komunikaci nebo jiné problémy mezi tento uzel a služby Správce převzetí služeb při selhání.

* **SourceId**: System.RA
* **Vlastnost**: Změny konfigurace.
* **Další kroky**: Zjistěte, místní nebo vzdálené repliky v závislosti na popis sestavy stavu.

Následující příklad ukazuje sestava stavu, ve kterém Rekonfigurace se zablokovala místní repliky. V této ukázce to není z důvodu služba dodržením token zrušení.

```powershell
PS C:\> Get-ServiceFabricReplicaHealth -PartitionId 9a0cedee-464c-4603-abbc-1cf57c4454f3 -ReplicaOrInstanceId 131483600074836703


PartitionId           : 9a0cedee-464c-4603-abbc-1cf57c4454f3
ReplicaId             : 131483600074836703
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='Reconfiguration', HealthState='Warning', 
                        ConsiderWarningAsError=false.
                        
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : Reconfiguration
                        HealthState           : Warning
                        SequenceNumber        : 131483600309264482
                        SentAt                : 8/28/2017 2:13:50 AM
                        ReceivedAt            : 8/28/2017 2:13:57 AM
                        TTL                   : Infinite
                        Description           : Reconfiguration is stuck. Waiting for response from the local replica
                        
                        For more information see: https://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/28/2017 2:13:57 AM, LastOk = 1/1/0001 12:00:00 AM
```

Následující příklad ukazuje stavu sestavy tam, kde Rekonfigurace se zablokovala, čekající na odpověď z dvě vzdálené repliky. V tomto příkladu existují tři repliky v oddílu, včetně aktuální primární. 

```Powershell
PS C:\> Get-ServiceFabricReplicaHealth -PartitionId  579d50c6-d670-4d25-af70-d706e4bc19a2 -ReplicaOrInstanceId 131483956274977415


PartitionId           : 579d50c6-d670-4d25-af70-d706e4bc19a2
ReplicaId             : 131483956274977415
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='Reconfiguration', HealthState='Warning', ConsiderWarningAsError=false.
                        
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : Reconfiguration
                        HealthState           : Warning
                        SequenceNumber        : 131483960376212469
                        SentAt                : 8/28/2017 12:13:57 PM
                        ReceivedAt            : 8/28/2017 12:14:07 PM
                        TTL                   : Infinite
                        Description           : Reconfiguration is stuck. Waiting for response from 2 replicas
                        
                        Pending Replicas: 
                        P/I Down 40 131483956244554282
                        S/S Down 20 131483956274972403
                        
                        For more information see: https://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/28/2017 12:07:37 PM, LastOk = 1/1/0001 12:00:00 AM
```

Tato sestava stavu zobrazuje, že Rekonfigurace se zablokovala čekání na odpověď z dvě repliky: 

```
    P/I Down 40 131483956244554282
    S/S Down 20 131483956274972403
```

Pro každou repliku je uveden následující informace:
- Předchozí konfigurace role
- Aktuální konfigurace role
- [Stav repliky](service-fabric-concepts-replica-lifecycle.md)
- ID uzlu
- ID repliky

Odblokování změna konfigurace:
- **Dolů** by měl být aktivují repliky. 
- **Inbuild** repliky by měla dokončení sestavení a přechod na hodnotu Připraveno.

### <a name="slow-service-api-call"></a>Pomalá volání rozhraní API služby
**System.RAP** a **System.Replicator** hlásit upozornění v případě, že trvá déle než nastavená doba volání do kódu služby uživatele. Upozornění je vymazán po dokončení volání.

* **SourceId**: System.RAP nebo System.Replicator
* **Vlastnost**: Název rozhraní API pro pomalé. Popis obsahuje podrobnosti o čas, kdy se čeká na rozhraní API.
* **Další kroky**: Zjistěte, proč trvá déle, než bylo očekáváno volání.

Následující příklad ukazuje událost stavu z System.RAP spolehlivé služby, který není dodržením zrušení tokenu v **RunAsync**:

```powershell
PS C:\> Get-ServiceFabricReplicaHealth -PartitionId 5f6060fb-096f-45e4-8c3d-c26444d8dd10 -ReplicaOrInstanceId 131483966141404693


PartitionId           : 5f6060fb-096f-45e4-8c3d-c26444d8dd10
ReplicaId             : 131483966141404693
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='Reconfiguration', HealthState='Warning', ConsiderWarningAsError=false.
                        
HealthEvents          :                         
                        SourceId              : System.RAP
                        Property              : IStatefulServiceReplica.ChangeRole(S)Duration
                        HealthState           : Warning
                        SequenceNumber        : 131483966663476570
                        SentAt                : 8/28/2017 12:24:26 PM
                        ReceivedAt            : 8/28/2017 12:24:56 PM
                        TTL                   : Infinite
                        Description           : The api IStatefulServiceReplica.ChangeRole(S) on _Node_1 is stuck. Start Time (UTC): 2017-08-28 12:23:56.347.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/28/2017 12:24:56 PM, LastOk = 1/1/0001 12:00:00 AM
                        
```

Vlastnost a text označují, které rozhraní API je teď zablokované. Další kroky pro různé zablokované rozhraní API se liší. Jakékoliv rozhraní API na *IStatefulServiceReplica* nebo *IStatelessServiceInstance* je obvykle chybu v kódu služby. Následující část popisuje, jak tyto přeložit na [modelu Reliable Services](service-fabric-reliable-services-lifecycle.md):

- **IStatefulServiceReplica.Open**: Toto upozornění signalizuje, že volání `CreateServiceInstanceListeners`, `ICommunicationListener.OpenAsync`, nebo pokud přepsána, `OnOpenAsync` se zablokovala.

- **IStatefulServiceReplica.Close** a **IStatefulServiceReplica.Abort**: Nejběžnější případ je služba není dodržením token rušení, který byl poskytnut `RunAsync`. Může být také, která `ICommunicationListener.CloseAsync`, nebo pokud přepsána, `OnCloseAsync` se zablokovala.

- **IStatefulServiceReplica.ChangeRole (S)** a **IStatefulServiceReplica.ChangeRole(N)** : Nejběžnější případ je služba není dodržením token rušení, který byl poskytnut `RunAsync`. V tomto scénáři nejlepším řešením je restartovat repliky.

- **IStatefulServiceReplica.ChangeRole(P)** : Nejběžnější případ je, že nebyla služba vrátila úlohu z `RunAsync`.

Další volání rozhraní API, které může způsobit zablokování a zobrazení jsou na **IReplicator** rozhraní. Příklad:

- **IReplicator.CatchupReplicaSet**: Toto upozornění znamená jeden ze dvou kroků. Nejsou dostatečná repliky. Pokud chcete zobrazit, pokud se jedná o tento případ, podívejte se na stav repliky replik v oddílu nebo System.FM – sestava stavu pro zablokované Rekonfigurace. Nebo můžete repliky nejsou potvrdil operace. Rutiny Powershellu `Get-ServiceFabricDeployedReplicaDetail` slouží k určení rozsahu postupu všechny repliky. Potíže nezpůsobuje repliky, jejichž `LastAppliedReplicationSequenceNumber` hodnotu za primární `CommittedSequenceNumber` hodnotu.

- **IReplicator.BuildReplica(\<Remote ReplicaId>)** : Toto upozornění signalizuje problém v procesu sestavení. Další informace najdete v tématu [životní cyklus replik](service-fabric-concepts-replica-lifecycle.md). Může být z důvodu chybné konfigurace adresy replikátor. Další informace najdete v tématu [konfigurace stavovém modelu Reliable Services](service-fabric-reliable-services-configuration.md) a [určení prostředků v manifestu služby](service-fabric-service-manifest-resources.md). Také může být problém ve vzdáleném uzlu.

### <a name="replicator-system-health-reports"></a>Replikátor systémovými stavovými sestavami
**Replikační fronta je plná:** 
**System.Replicator** hlásí upozornění, pokud fronta replikací je plná. Na primárním, replikace fronta obvykle zaplní protože jeden nebo více sekundárních replik jsou pomalá potvrzení operace. Na sekundárním to obvykle se stane, když je pomalé provádět operace služby. Upozornění se vymaže při fronty již není úplná.

* **SourceId**: System.Replicator
* **Vlastnost**: **PrimaryReplicationQueueStatus** nebo **SecondaryReplicationQueueStatus**, v závislosti na role repliky.
* **Další kroky**: Pokud je sestava na primární, zkontrolujte připojení mezi uzly v clusteru. Pokud všechna připojení jsou v pořádku, může být alespoň jeden pomalé sekundárního objektu s latence vysokou disku provádět operace. Pokud je sestava na sekundárním, proveďte nejprve kontrolu využití disku a výkonu na uzlu. Zkontrolujte odchozí připojení z pomalého uzlu na primární.

**RemoteReplicatorConnectionStatus:** 
**System.Replicator** na primární replice sestavy varování při připojení k sekundární (vzdálené) replikátoru není v pořádku. Adresa vzdáleného replikátoru je zobrazena ve zprávě sestavy, díky tomu je snazší zjistit, jestli byla předána chybná konfigurace, nebo pokud existují problémy se sítí mezi replikátorů.

* **SourceId**: System.Replicator
* **Vlastnost**: **RemoteReplicatorConnectionStatus**.
* **Další kroky**: Najdete v chybové zprávě a ujistěte se, že je správně nakonfigurovaná adresa vzdáleného replikátoru. Například pokud vzdáleného replikátoru je otevřen s vlastností listenurimode nastavenou na adresu "localhost", není dostupný z vnějšku. Pokud adresu vypadá správně, zkontrolujte připojení mezi primárním uzlu a Vzdálená adresa najít všechny potenciální problémy se sítí.

### <a name="replication-queue-full"></a>Úplné ve frontě replikací
**System.Replicator** hlásí upozornění, pokud fronta replikací je plná. Na primárním, replikace fronta obvykle zaplní protože jeden nebo více sekundárních replik jsou pomalá potvrzení operace. Na sekundárním to obvykle se stane, když je pomalé provádět operace služby. Upozornění se vymaže při fronty již není úplná.

* **SourceId**: System.Replicator
* **Vlastnost**: **PrimaryReplicationQueueStatus** nebo **SecondaryReplicationQueueStatus**, v závislosti na role repliky.

### <a name="slow-naming-operations"></a>Pomalá operace pojmenování
**System.NamingService** hlásit stav na její primární replikou, při pojmenování operace trvá déle než přijatelné. Příklady operací s názvy jsou [CreateServiceAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync) nebo [DeleteServiceAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient.deleteserviceasync). Další metody najdete v části FabricClient. Například můžete najít v části [metody správy služeb](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient) nebo [metody správy vlastnost](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.propertymanagementclient).

> [!NOTE]
> Služba pojmenování přeloží názvy služeb do umístění v clusteru. Uživatelé můžou použít ho ke správě služby názvy a vlastnosti. Jedná se o službu Service Fabric jako trvalý rozdělit na oddíly. Představuje jeden z oddílů *Authority Owner*, který obsahuje metadata o všech názvů Service Fabric a služeb. Service Fabric názvy jsou namapovány na různé oddíly, volá *vlastníka* oddíly, abyste službu je rozšiřitelné. Další informace najdete [pojmenování service](service-fabric-architecture.md).
> 
> 

Při pojmenování operace trvá déle, než se očekávalo, je označen jako operace se sestavou upozornění na primární replice služby oddílu zásady vytváření názvů, který poskytuje operaci. Pokud se operace úspěšně dokončí, se vymaže upozornění. Pokud operace skončí s chybou, sestava stavu zahrnuje podrobnosti o chybě.

* **SourceId**: System.NamingService
* **Vlastnost**: Začíná předponou "**Duration_** " a identifikuje pomalá operace a název Service Fabric, na kterém se používá operace. Například pokud vytvoříte službu v názvu **fabric: / MyApp/Moje_služba** trvá příliš dlouho, je vlastnost **Duration_AOCreateService.fabric:/MyApp/MyService**. "AO" odkazuje na roli z oddílu zásady vytváření názvů pro tento název a operaci.
* **Další kroky**: Zjistěte, proč operace pojmenování se nezdaří. Každá operace může mít jiný hlavní příčiny. Například se mohly zaseknout službu odstranit. Služba se mohly zaseknout, protože pořád padá hostitele aplikace na uzlu kvůli chybě uživatele v kódu služby.

Následující příklad ukazuje operaci služby vytvořit. Operace trvalo déle než nakonfigurovaná doba trvání. "AO" počet opakování a odešle práce "Ne" "Ne" dokončit poslední operaci s časovým LIMITEM. V takovém případě je stejné repliky primární žádosti "o" a "Žádná" rolí.

```powershell
PartitionId           : 00000000-0000-0000-0000-000000001000
ReplicaId             : 131064359253133577
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.NamingService', Property='Duration_AOCreateService.fabric:/MyApp/MyService', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131064359308715535
                        SentAt                : 4/29/2016 8:38:50 PM
                        ReceivedAt            : 4/29/2016 8:39:08 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 4/29/2016 8:39:08 PM, LastWarning = 1/1/0001 12:00:00 AM

                        SourceId              : System.NamingService
                        Property              : Duration_AOCreateService.fabric:/MyApp/MyService
                        HealthState           : Warning
                        SequenceNumber        : 131064359526778775
                        SentAt                : 4/29/2016 8:39:12 PM
                        ReceivedAt            : 4/29/2016 8:39:38 PM
                        TTL                   : 00:05:00
                        Description           : The AOCreateService started at 2016-04-29 20:39:08.677 is taking longer than 30.000.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 4/29/2016 8:39:38 PM, LastOk = 1/1/0001 12:00:00 AM

                        SourceId              : System.NamingService
                        Property              : Duration_NOCreateService.fabric:/MyApp/MyService
                        HealthState           : Warning
                        SequenceNumber        : 131064360657607311
                        SentAt                : 4/29/2016 8:41:05 PM
                        ReceivedAt            : 4/29/2016 8:41:08 PM
                        TTL                   : 00:00:15
                        Description           : The NOCreateService started at 2016-04-29 20:39:08.689 completed with FABRIC_E_TIMEOUT in more than 30.000.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 4/29/2016 8:39:38 PM, LastOk = 1/1/0001 12:00:00 AM
```

## <a name="deployedapplication-system-health-reports"></a>DeployedApplication systémovými stavovými sestavami
**System.Hosting** autoritou na nasazeném entity.

### <a name="activation"></a>Aktivace
System.Hosting hlásí jako OK při aplikace se úspěšně aktivoval na uzlu. V opačném případě nahlásí chybu.

* **SourceId**: System.Hosting
* **Vlastnost**: **Aktivace**, včetně uvedení verze.
* **Další kroky**: Pokud aplikace není v pořádku, prozkoumejte, proč aktivace se nezdařila.

Následující příklad ukazuje úspěšné aktivaci:

```powershell
PS C:\> Get-ServiceFabricDeployedApplicationHealth -NodeName _Node_1 -ApplicationName fabric:/WordCount -ExcludeHealthStatistics

ApplicationName                    : fabric:/WordCount
NodeName                           : _Node_1
AggregatedHealthState              : Ok
DeployedServicePackageHealthStates : 
                                     ServiceManifestName   : WordCountServicePkg
                                     ServicePackageActivationId : 
                                     NodeName              : _Node_1
                                     AggregatedHealthState : Ok
                                     
HealthEvents                       : 
                                     SourceId              : System.Hosting
                                     Property              : Activation
                                     HealthState           : Ok
                                     SequenceNumber        : 131445249083836329
                                     SentAt                : 7/14/2017 4:55:08 PM
                                     ReceivedAt            : 7/14/2017 4:55:14 PM
                                     TTL                   : Infinite
                                     Description           : The application was activated successfully.
                                     RemoveWhenExpired     : False
                                     IsExpired             : False
                                     Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="download"></a>Ke stažení
System.Hosting hlásí chybu, pokud stahování balíčku aplikace selže.

* **SourceId**: System.Hosting
* **Vlastnost**: **Stáhněte si**, včetně uvedení verze.
* **Další kroky**: Zjistěte, proč se nepodařilo položky stáhnout na uzlu.

## <a name="deployedservicepackage-system-health-reports"></a>DeployedServicePackage systémovými stavovými sestavami
**System.Hosting** autoritou na nasazeném entity.

### <a name="service-package-activation"></a>Balíček pro aktivaci služeb
System.Hosting jako OK hlásí, zda aktivace balíček služby v uzlu úspěšné. V opačném případě nahlásí chybu.

* **SourceId**: System.Hosting
* **Vlastnost**: Aktivace.
* **Další kroky**: Zjistěte, proč aktivace se nezdařila.

### <a name="code-package-activation"></a>Aktivaci balíčku kódu
System.Hosting hlásí jako OK pro každý balíček kódu pokud je aktivace úspěšná. Pokud se aktivace nepodaří, sestavy upozornění podle konfigurace. Pokud **CodePackage** nepodaří aktivovat nebo ukončí s chybou větší než nakonfigurované **CodePackageHealthErrorThreshold**, který je hostitelem hlásí chybu. Pokud balíček služby obsahuje více balíčků kódu, se pro každé z nich vygeneruje zprávu o aktivaci.

* **SourceId**: System.Hosting
* **Vlastnost**: Předpona, která používá **CodePackageActivation** a obsahuje název balíček kódu a vstupního bodu jako *CodePackageActivation:CodePackageName:SetupEntryPoint / EntryPoint*. Například **CodePackageActivation:Code:SetupEntryPoint**.

### <a name="service-type-registration"></a>Typ registrace služby
System.Hosting hlásí jako OK, pokud typ služby byl úspěšně zaregistrován. Nahlásí chybu pokud registrace nebyla provedena v čase, je nakonfigurován pomocí **ServiceTypeRegistrationTimeout**. Pokud modul runtime je zavřená, typ služby se registrace z uzlu a hostování sestavy upozornění.

* **SourceId**: System.Hosting
* **Vlastnost**: Předpona, která používá **ServiceTypeRegistration** a obsahuje název typu služby. Například **ServiceTypeRegistration:FileStoreServiceType**.

Následující příklad ukazuje v pořádku nasazený balíček služby:

```powershell
PS C:\> Get-ServiceFabricDeployedServicePackageHealth -NodeName _Node_1 -ApplicationName fabric:/WordCount -ServiceManifestName WordCountServicePkg


ApplicationName            : fabric:/WordCount
ServiceManifestName        : WordCountServicePkg
ServicePackageActivationId : 
NodeName                   : _Node_1
AggregatedHealthState      : Ok
HealthEvents               : 
                             SourceId              : System.Hosting
                             Property              : Activation
                             HealthState           : Ok
                             SequenceNumber        : 131445249084026346
                             SentAt                : 7/14/2017 4:55:08 PM
                             ReceivedAt            : 7/14/2017 4:55:14 PM
                             TTL                   : Infinite
                             Description           : The ServicePackage was activated successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
                             
                             SourceId              : System.Hosting
                             Property              : CodePackageActivation:Code:EntryPoint
                             HealthState           : Ok
                             SequenceNumber        : 131445249084306362
                             SentAt                : 7/14/2017 4:55:08 PM
                             ReceivedAt            : 7/14/2017 4:55:14 PM
                             TTL                   : Infinite
                             Description           : The CodePackage was activated successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
                             
                             SourceId              : System.Hosting
                             Property              : ServiceTypeRegistration:WordCountServiceType
                             HealthState           : Ok
                             SequenceNumber        : 131445249088096842
                             SentAt                : 7/14/2017 4:55:08 PM
                             ReceivedAt            : 7/14/2017 4:55:14 PM
                             TTL                   : Infinite
                             Description           : The ServiceType was registered successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="download"></a>Ke stažení
System.Hosting hlásí chybu, pokud služba stahování balíčku selže.

* **SourceId**: System.Hosting
* **Vlastnost**: **Stáhněte si**, včetně uvedení verze.
* **Další kroky**: Zjistěte, proč se nepodařilo položky stáhnout na uzlu.

### <a name="upgrade-validation"></a>Ověření upgradu
System.Hosting hlásí chybu, pokud selže ověření během upgradu, nebo pokud upgrade selže na uzlu.

* **SourceId**: System.Hosting
* **Vlastnost**: Předpona, která používá **FabricUpgradeValidation** a obsahuje upgradované verze.
* **Popis**: Odkazuje na došlo k chybě.

### <a name="undefined-node-capacity-for-resource-governance-metrics"></a>Kapacita nedefinovaného uzlu pro metriky zásad správného řízení prostředků
Pokud v manifestu clusteru nejsou definované kapacity uzlů a konfiguraci pro automatické zjišťování je vypnutý, nahlásí System.Hosting upozornění. Service Fabric vyvolá upozornění stavu pokaždé, když se balíček služby, která používá [zásady správného řízení prostředků](service-fabric-resource-governance.md) zaregistruje pro zadaný uzel.

* **SourceId**: System.Hosting
* **Vlastnost**: **ResourceGovernance**.
* **Další kroky**: Preferovaný způsob, jak vyřešit tento problém je změnit v manifestu clusteru povolit automatické zjišťování dostupných prostředků. Jiný způsob, jak je aktualizovat v manifestu clusteru správně zadaný uzel kapacity pro tyto metriky.

## <a name="next-steps"></a>Další postup
* [Zobrazení sestav health Service Fabric](service-fabric-view-entities-aggregated-health.md)

* [Způsob hlášení a kontrola stavu služby](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

* [Monitorování a Diagnostika služeb místně](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

* [Upgrade aplikace Service Fabric](service-fabric-application-upgrade.md)

