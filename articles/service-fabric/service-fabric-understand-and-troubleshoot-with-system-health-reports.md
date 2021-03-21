---
title: Řešení potíží se systémovými stavovými sestavami
description: Popisuje sestavy stavu odesílané součástmi Azure Service Fabric a jejich využití při řešení problémů s clustery nebo aplikacemi.
author: georgewallace
ms.topic: conceptual
ms.date: 2/28/2018
ms.author: gwallace
ms.openlocfilehash: 8e60ac5065c2f9543a641daf4f62299c00c61fc8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96000653"
---
# <a name="use-system-health-reports-to-troubleshoot"></a>Řešení problémů pomocí sestav o stavu systému
Komponenty služby Azure Service Fabric poskytují zprávy o stavu systému pro všechny entity v clusteru přímo ze seznamu. [Health Store](service-fabric-health-introduction.md#health-store) vytvoří a odstraní entity založené na sestavách systému. Uspořádává je také v hierarchii, která zachycuje interakce entit.

> [!NOTE]
> Pro pochopení konceptů týkajících se stavu si přečtěte další informace o [Service Fabric modelu stavu](service-fabric-health-introduction.md).
> 
> 

Sestavy o stavu systému poskytují přehled o funkcích clusterů a aplikací a označují problémy s příznakem. Pro aplikace a služby se v sestavách stavu systému ověřují, jestli jsou entity implementované a že se chovají správně z Service Fabric perspektivy. Sestavy neposkytují žádné monitorování stavu obchodní logiky služby nebo zjišťování procesů, které nereagují. Služby uživatelů mohou rozšířit údaje o stavu o informace, které jsou specifické pro jejich logiku.

> [!NOTE]
> Sestavy stavu odesílané pomocí sledovacích zařízení uživatelů jsou viditelné pouze *poté, co* komponenty systému vytvářejí entitu. Když se entita odstraní, Health Store automaticky odstraní všechny zprávy o stavu, které jsou k němu přidružené. Totéž platí při vytvoření nové instance entity. Příkladem je, že se vytvoří nová stavová trvalá instance repliky služby. Všechny sestavy přidružené k staré instanci se odstraní a vyčistí ze Storu.
> 
> 

Sestavy systémových součástí jsou označeny zdrojem, který začíná na "**System".** „com.microsoft.intune.mam“. Sledovací zařízení nemůžou používat stejnou předponu pro své zdroje, protože sestavy s neplatnými parametry se odmítnou.

Podívejme se na některé systémové sestavy, abyste porozuměli tomu, co je spouští, a zjistili si, jak opravit potenciální problémy, které představují.

> [!NOTE]
> Service Fabric nadále přidávají sestavy za podmínky zájmu, které zlepšují přehled o tom, co se děje v clusteru a aplikacích. Stávající sestavy můžete rozšířit o další podrobnosti, které vám pomohou rychleji řešit potíže.
> 
> 

## <a name="cluster-system-health-reports"></a>Sestavy stavu systému clusteru
Entita stav clusteru se vytvoří automaticky v Health Store. Pokud vše funguje správně, nemá k dispozici systémovou zprávu.

### <a name="neighborhood-loss"></a>Okolní ztráta
Při zjištění výpadku okolí hlásí **System. Federation** zprávu o chybě. Sestava je z jednotlivých uzlů a ID uzlu je obsaženo v názvu vlastnosti. Pokud dojde ke ztrátě jednoho souseda v rámci celého Service Fabricového prstence, můžete obvykle očekávat dvě události, které reprezentují obě strany sestavy mezery. Pokud dojde ke ztrátě více okolních okolních dat, bude k dispozici více událostí.

Sestava určuje časový limit globálního zapůjčení jako hodnota TTL (Time to Live). Sestava je znovu odeslána každou polovinu doby TTL trvání, pokud tato podmínka zůstane aktivní. Událost se po vypršení platnosti automaticky odebere. Chování funkce odebrat po vypršení platnosti zajišťuje, aby se sestava vyčistila z Health Store správně, i když je uzel sestav mimo provoz.

* **SourceId**: System. Federation
* **Vlastnost**: začíná na **okolí** a obsahuje informace o uzlu.
* **Další kroky**: Zjistěte, proč došlo ke ztrátě okolního okolí. Například ověřte komunikaci mezi uzly clusteru.

### <a name="rebuild"></a>Opětovné sestavení (Rebuild)

Služba Správce převzetí služeb při selhání (FM) spravuje informace o uzlech clusteru. Když FM ztratí svá data a přejde do ztráty dat, nemůže zaručit, že obsahuje nejaktuálnější informace o uzlech clusteru. V tomto případě systém provede opakované sestavení a System.FM shromáždí data ze všech uzlů v clusteru, aby bylo možné znovu sestavit svůj stav. V důsledku potíží se sítí nebo uzlem se někdy může opětovné sestavení podržet nebo zablokovat. Totéž může nastat u služby Správce převzetí služeb při selhání Master (FMM). FMM je Bezstavová systémová služba, která sleduje, kde jsou všechny FMs v clusteru. Primární FMM je vždy uzel s ID nejbližším 0. Pokud je tento uzel vyřazen, je aktivováno opětovné sestavení.
V případě, že dojde k jednomu z předchozích podmínek, **System.FM** nebo **System. FMM** ho označí pomocí zprávy o chybách. Opětovné sestavení může být zablokované v jedné ze dvou fází:

* **Čekání na vysílání**: FM/FMM čeká na odpověď ve zprávě všesměrového vysílání od ostatních uzlů.

  * **Další kroky**: Prozkoumejte, jestli došlo k potížím se síťovým připojením mezi uzly.
* **Čekání na uzly**: FM/FMM již obdržela odpověď všesměrového vysílání z jiných uzlů a čeká na odpověď z konkrétních uzlů. Sestava o stavu obsahuje seznam uzlů, pro které FMMy FM/čekají na odpověď.
   * **Další kroky**: Prozkoumejte síťové připojení mezi FM/FMM a uvedenými uzly. Prozkoumejte každý uvedený uzel pro další možné problémy.

* **SourceId**: System.FM nebo System. FMM
* **Vlastnost**: znovu sestavit.
* **Další kroky**: Prozkoumejte síťové připojení mezi uzly a také stav všech konkrétních uzlů, které jsou uvedeny v popisu sestavy o stavu.

### <a name="seed-node-status"></a>Stav počátečního uzlu
**System.FM** oznamuje upozornění na úrovni clusteru, pokud některé počáteční uzly nejsou v pořádku. Počáteční uzly jsou uzly, které udržují dostupnost podkladového clusteru. Tyto uzly pomůžou zajistit, že cluster zůstane v platnosti, a to tak, že se zapůjčí s ostatními uzly a během určitých druhů selhání sítě zaručí jako tiebreakers. Pokud se většina počátečních uzlů v clusteru nenachází a nevrátí se zpět, cluster se automaticky vypne. 

Uzel počáteční hodnoty není v pořádku, pokud je stav uzlu mimo provoz, odebrán nebo neznámý.
Zpráva s upozorněním na stav uzlu počáteční hodnoty zobrazí seznam všech nestavových počátečních uzlů s podrobnými informacemi.

* **SourceId**: System.FM
* **Vlastnost**: SeedNodeStatus
* **Další kroky**: Pokud se toto upozornění v clusteru zobrazí, postupujte podle pokynů níže, abyste ho opravili: u clusteru se systémem Service Fabric verze 6,5 nebo vyšší: pro Service Fabric clusteru v Azure, když se uzel počáteční hodnoty ukončí, Service Fabric se ho automaticky změní na nepočáteční uzel. Aby k tomu docházelo, ujistěte se, že počet nepočátečních uzlů v primárním uzlu je větší nebo roven počtu počátečních uzlů. V případě potřeby přidejte k primárnímu typu uzlu další uzly, abyste to dosáhli.
V závislosti na stavu clusteru může řešení problému nějakou dobu trvat. Až to uděláte, zpráva upozornění se automaticky vymaže.

Pro Service Fabric samostatný cluster, aby bylo možné vymazat zprávu o upozornění, musí být všechny počáteční uzly v dobrém stavu. V závislosti na tom, proč jsou uzly osazení v chybném stavu, je nutné provést různé akce: Pokud je uzel počáteční hodnoty mimo provoz, uživatelé musí tento počáteční uzel přenést. Pokud je uzel počáteční hodnoty odebraný nebo neznámý, je nutné tento uzel počátečního [rozhraní odebrat z clusteru](./service-fabric-cluster-windows-server-add-remove-nodes.md).
Zpráva o upozornění se automaticky vymaže, jakmile budou všechny počáteční uzly v dobrém stavu.

Pro cluster se systémem Service Fabric verze starší než 6,5: v takovém případě je nutné zprávu upozornění vymazat ručně. **Před vymazáním sestavy by se uživatelé měli ujistit, že všechny počáteční uzly budou v pořádku**. Pokud je uzel počáteční hodnoty mimo provoz, uživatelé musí tento počáteční uzel přenést. Pokud je počáteční uzel odebraný nebo neznámý, musí být tento počáteční uzel odebraný z clusteru.
Až budou všechny počáteční uzly v dobrém stavu, použijte následující příkaz z PowerShellu k [vymazání zprávy upozornění](/powershell/module/servicefabric/send-servicefabricclusterhealthreport):

```powershell
PS C:\> Send-ServiceFabricClusterHealthReport -SourceId "System.FM" -HealthProperty "SeedNodeStatus" -HealthState OK

## Node system health reports
System.FM, which represents the Failover Manager service, is the authority that manages information about cluster nodes. Each node should have one report from System.FM showing its state. The node entities are removed when the node state is removed. For more information, see [RemoveNodeStateAsync](/dotnet/api/system.fabric.fabricclient.clustermanagementclient.removenodestateasync).

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
**System. FabricNode** oznamuje upozornění, když se certifikáty používané uzlem blíží k vypršení platnosti. Existují tři certifikáty na uzel: **Certificate_cluster**, **Certificate_server** a **Certificate_default_client**. Pokud je doba vypršení alespoň dvou týdnů pryč, stav sestavy je OK. V případě vypršení platnosti do dvou týdnů je typ sestavy upozornění. Hodnota TTL těchto událostí je nekonečná a jsou odebrána, když uzel opustí cluster.

* **SourceId**: System. FabricNode
* **Vlastnost**: začíná s **certifikátem** a obsahuje další informace o typu certifikátu.
* **Další kroky**: aktualizace certifikátů, pokud se blíží vypršení platnosti.

### <a name="load-capacity-violation"></a>Narušení kapacity zatížení
Service Fabric Load Balancer ohlásí upozornění, když zjistí porušení kapacity uzlu.

* **SourceId**: System. PLB
* **Vlastnost**: začíná na **kapacitě**.
* **Další kroky**: Zkontrolujte zadané metriky a zobrazte aktuální kapacitu uzlu.

### <a name="node-capacity-mismatch-for-resource-governance-metrics"></a>Neshoda kapacity uzlů pro metriky zásad správného řízení prostředků
System. hosting hlásí upozornění, pokud jsou definované kapacity uzlů v manifestu clusteru větší než skutečné kapacity uzlů pro metriky zásad správného řízení prostředků (paměti a jádra procesoru). Po prvním balíčku služby, který používá Registry [zásad správného řízení prostředků](service-fabric-resource-governance.md) v zadaném uzlu, se zobrazí zpráva o stavu.

* **SourceId**: System. hosting
* **Vlastnost**: **ResourceGovernance**.
* **Další kroky**: Tento problém může být problém, protože řízení balíčků služby se neuplatňuje podle očekávání a zásady [správného řízení prostředků](service-fabric-resource-governance.md) nefungují správně. Aktualizujte manifest clusteru o správnou kapacitu uzlů pro tyto metriky, nebo je nezadávejte a nechte Service Fabric automaticky zjišťovat dostupné prostředky.

## <a name="application-system-health-reports"></a>Sestavy o stavu systému aplikace
System.CM, která představuje službu Správce clusterů, je autoritou, která spravuje informace o aplikaci.

### <a name="state"></a>Stav
System.CM sestavy jako OK, když byla aplikace vytvořena nebo aktualizována. Informuje Health Store při odstranění aplikace, aby ji bylo možné odebrat z úložiště.

* **SourceId**: System.cm
* **Vlastnost**: State
* **Další kroky**: Pokud se aplikace vytvořila nebo aktualizovala, měla by obsahovat sestavu o stavu Správce clusterů. V opačném případě Ověřte stav aplikace vyvoláním dotazu. Použijte například rutinu PowerShellu **Get-ServiceFabricApplication-ApplicationName** *ApplicationName*.

Následující příklad ukazuje událost stavu v aplikaci **Fabric:/WORDCOUNT** :

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

## <a name="service-system-health-reports"></a>Sestavy stavu systému služby
System.FM, která představuje službu Správce převzetí služeb při selhání, je autoritou, která spravuje informace o službách.

### <a name="state"></a>Stav
System.FM sestavy jako OK po vytvoření služby. Odstraní entitu z Health Store při odstranění služby.

* **SourceId**: System.FM
* **Vlastnost**: State

Následující příklad ukazuje událost stavu v Service **Fabric:/WORDCOUNT/WordCountWebService**:

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

### <a name="service-correlation-error"></a>Chyba korelace služby
**System. PLB** hlásí chybu, když zjistí, že aktualizace služby je korelace s jinou službou, která vytvoří řetěz spřažení. Sestava je vymazána, když dojde k úspěšné aktualizaci.

* **SourceId**: System. PLB
* **Vlastnost**: **ServiceDescription**.
* **Další kroky**: Podívejte se na popis korelační služby.

## <a name="partition-system-health-reports"></a>Vytváření oddílů sestav stavu systému
System.FM, která představuje službu Správce převzetí služeb při selhání, je autoritou, která spravuje informace o oddílech služeb.

### <a name="state"></a>Stav
System.FM sestavy jako OK, když byl oddíl vytvořen a je v pořádku. Odstraní entitu z Health Store, když se oddíl odstraní.

Pokud je oddíl pod minimálním počtem replik, ohlásí chybu. Pokud oddíl není pod minimálním počtem replik, ale je pod počtem cílových replik, ohlásí upozornění. Pokud je oddíl ve ztrátě kvora, System.FM hlásí chybu.

Další významné události obsahují upozornění, pokud přetrvá překonfigurování déle, než se čekalo, a když Build trvá déle, než se čekalo. Očekávané časy pro sestavení a opětovnou konfiguraci jsou konfigurovatelné v závislosti na scénářích služby. Pokud má například služba stav terabajt, například Azure SQL Database, sestavení trvá déle než u služby s malým množstvím stavu.

* **SourceId**: System.FM
* **Vlastnost**: State
* **Další kroky**: Pokud stav není OK, je možné, že některé repliky nebyly vytvořeny, otevřeny nebo povýšeny na primární nebo sekundární. 

Pokud popis popisuje ztrátu kvora, potom se podíváme na podrobnou zprávu o stavu pro repliky, které jsou vypnuté a jejich zálohováním pomůže převést oddíl zpátky do online režimu.

Pokud popis popisuje oddíl zablokované v rámci změny [Konfigurace](service-fabric-concepts-reconfiguration.md), zobrazí se další informace v sestavě o stavu primární repliky.

U jiných sestav o stavu System.FM by existovaly sestavy replik nebo oddílu nebo služby z dalších komponent systému. 

V následujících příkladech jsou popsány některé z těchto sestav. 

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

Následující příklad ukazuje stav oddílu, který je pod počtem cílových replik. Dalším krokem je získání popisu oddílu, který ukazuje, jak je nakonfigurovaný: **MinReplicaSetSize** je tři a **TargetReplicaSetSize** je sedm. Pak Získejte počet uzlů v clusteru, což je v tomto případě pět. V tomto případě proto nelze umístit dvě repliky, protože cílový počet replik je vyšší, než je počet dostupných uzlů.

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

Následující příklad ukazuje stav oddílu, který se zablokuje v rekonfiguraci, protože uživatel nedodržuje token zrušení v metodě **RunAsync** . Zkoumání sestavy o stavu jakékoli repliky označené jako primární (P) může přispět k dalšímu procházení tohoto problému.

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
Tato sestava stavu zobrazuje stav replik oddílu, který přesměruje do konfigurace: 

```
  P/S Ready Node1 131482789658160654
  S/P Ready Node2 131482789688598467
  S/S Ready Node3 131482789688598468
```

Sestava stavu pro každou repliku obsahuje:
- Předchozí role konfigurace
- Aktuální konfigurační role
- [Stav repliky](service-fabric-concepts-replica-lifecycle.md)
- Uzel, na kterém je replika spuštěná
- ID repliky

V případě příkladu je potřeba další šetření. Prozkoumejte stav každé jednotlivé repliky počínaje replikami označenými jako `Primary` a `Secondary` (131482789658160654 a 131482789688598467) v předchozím příkladu.

### <a name="replica-constraint-violation"></a>Porušení omezení repliky
**System. PLB** oznamuje upozornění, pokud zjistí porušení omezení repliky a nemůže umístit všechny repliky oddílů. Podrobnosti sestavy obsahují informace o tom, která omezení a vlastnosti brání umístění repliky.

* **SourceId**: System. PLB
* **Vlastnost**: začíná na **ReplicaConstraintViolation**.

## <a name="replica-system-health-reports"></a>Sestavy stavu systému repliky
**System. ra**, který představuje součást agenta rekonfigurace, je autoritou pro stav repliky.

### <a name="state"></a>Stav
Sestavy System. RA po vytvoření repliky jsou v pořádku.

* **SourceId**: System. ra
* **Vlastnost**: State

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
Tato vlastnost slouží k označení varování nebo selhání při pokusu o otevření repliky, zavření repliky nebo přechodu repliky z jedné role do druhé. Další informace najdete v tématu [životní cyklus repliky](service-fabric-concepts-replica-lifecycle.md). Chyby mohou být výjimky vyvolané z volání rozhraní API nebo havárií procesu hostitele služby během této doby. V případě selhání z důvodu volání rozhraní API z kódu jazyka C# Service Fabric přidá výjimku a trasování zásobníku do sestavy stavu.

Tato upozornění na stav jsou aktivována po výskytu akce místně v určitém počtu (v závislosti na zásadách). Service Fabric opakuje akci až do maximální prahové hodnoty. Po dosažení maximální prahové hodnoty se může pokusit jednat, aby se situace opravila. Tento pokus může způsobit, že se tato upozornění vymažou, protože zastavuje akci v tomto uzlu. Například pokud se replika nedaří otevřít na uzlu, Service Fabric vyvolá upozornění na stav. Pokud se replika i nadále nedaří otevřít, Service Fabric funguje k samočinné opravě. Tato akce může zahrnovat pokus o stejnou operaci na jiném uzlu. Tento pokus způsobí vymazání upozornění pro tuto repliku. 

* **SourceId**: System. ra
* **Vlastnost**: **ReplicaOpenStatus**, **ReplicaCloseStatus** a **ReplicaChangeRoleStatus**.
* **Další kroky**: Prozkoumejte kód služby nebo výpisy stavu systému, abyste mohli zjistit, proč se operace nezdařila.

Následující příklad ukazuje stav repliky, která je aktivována `TargetInvocationException` z její otevřené metody. Popis obsahuje bod selhání, **IStatefulServiceReplica. Open**, typ výjimky **TargetInvocationException –** a trasování zásobníku.

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

Následující příklad ukazuje replikovanou repliku, která během zavírání stále dochází k chybě:

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
Tato vlastnost slouží k označení, že replika provádějící [rekonfiguraci](service-fabric-concepts-reconfiguration.md) zjistí, že se rekonfigurace zablokovala nebo zablokuje. Tato sestava o stavu může být na replice, jejíž aktuální role je primární, s výjimkou případů swap Primary rekonfigurace, kde se může nacházet v replice, která se snižuje z primární na aktivní sekundární.

Rekonfigurace může být zablokovaná z jednoho z následujících důvodů:

- Akce na místní replice, stejná replika jako ta, která provádí rekonfiguraci, není dokončená. V takovém případě mohou další informace prozkoumáním sestav o stavu této repliky z jiných komponent, System. RAP nebo System.RE.

- Akce není na vzdálené replice dokončená. Repliky, jejichž akce čekají na vyřízení, jsou uvedeny v sestavě stavu. Pro tyto vzdálené repliky by se měly provádět další šetření v sestavách o stavu. Mezi tímto uzlem a vzdáleným uzlem můžou být taky problémy s komunikací.

Ve výjimečných případech může být rekonfigurace zablokovaná kvůli komunikaci nebo jiným problémům mezi tímto uzlem a službou Správce převzetí služeb při selhání.

* **SourceId**: System. ra
* **Vlastnost**: rekonfigurace.
* **Další kroky**: Prozkoumejte místní nebo vzdálené repliky v závislosti na popisu sestavy o stavu.

Následující příklad ukazuje zprávu o stavu, ve které je rekonfigurace zablokovaná v místní replice. V této ukázce je důvodem, že služba nedodržuje token zrušení.

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

Následující příklad ukazuje zprávu o stavu, ve které je rekonfigurace zablokovaná při čekání na odpověď ze dvou vzdálených replik. V tomto příkladu jsou v oddílu tři repliky, včetně aktuální primární. 

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

Tato sestava stavu ukazuje, že rekonfigurace zablokovala čekání na odpověď ze dvou replik: 

```
    P/I Down 40 131483956244554282
    S/S Down 20 131483956274972403
```

Pro každou repliku jsou uvedené následující informace:
- Předchozí role konfigurace
- Aktuální konfigurační role
- [Stav repliky](service-fabric-concepts-replica-lifecycle.md)
- ID uzlu
- ID repliky

Odblokování rekonfigurace:
- **Repliky** by měly být zavedeny. 
- Repliky **inbuildu** by měly dokončit sestavení a přechod do stavu připraveno.

### <a name="slow-service-api-call"></a>Pomalé volání rozhraní API služby
**System. rap** a **System. Replicator** hlásí upozornění, pokud volání kódu uživatelské služby trvá déle než nakonfigurovaný čas. Upozornění je po dokončení volání vymazáno.

* **SourceId**: System. rap nebo System. Replicator
* **Vlastnost**: název pomalého rozhraní API. Popis obsahuje další podrobnosti o době, kdy rozhraní API čeká na vyřízení.
* **Další kroky**: Zjistěte, proč volání trvá déle, než bylo očekáváno.

Následující příklad ukazuje událost stavu ze System. RAP pro spolehlivou službu, která nedodržuje token zrušení v **RunAsync**:

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

Vlastnost a text indikují, které rozhraní API bylo zablokováno. Další kroky, které je potřeba provést pro různá zablokovaná rozhraní API, se liší. Jakékoli rozhraní API na *IStatefulServiceReplica* nebo *IStatelessServiceInstance* je obvykle chyba v kódu služby. Následující část popisuje, jak se tyto překlady na [model Reliable Services](service-fabric-reliable-services-lifecycle.md):

- **IStatefulServiceReplica. Open**: Toto upozornění indikuje, že volání `CreateServiceInstanceListeners` , `ICommunicationListener.OpenAsync` nebo pokud je přepsáno, `OnOpenAsync` je zablokované.

- **IStatefulServiceReplica. Close** a **IStatefulServiceReplica. Abort**: Nejběžnější případ je služba, která nedodržuje token zrušení předaný do `RunAsync` . Může to také být, že `ICommunicationListener.CloseAsync` Pokud je přepsáno, `OnCloseAsync` bude zablokováno.

- **IStatefulServiceReplica. ChangeRole** a **IStatefulServiceReplica. ChangeRole (N)**: nejběžnějším případem je služba, která nedodržuje token zrušení předané do `RunAsync` . V tomto scénáři je nejlepším řešením restartování repliky.

- **IStatefulServiceReplica. ChangeRole (P)**: Nejčastějším případem je, že Služba nevrátila úlohu z `RunAsync` .

Další volání rozhraní API, která můžou zablokovat, jsou v rozhraní **IReplicator** . Například:

- **IReplicator. CatchupReplicaSet**: Toto upozornění indikuje jednu ze dvou věcí. Neexistují žádné nedostatečné repliky. Pokud se chcete podívat, jestli se jedná o tento případ, podívejte se na stav repliky v oddílu nebo v sestavě stavu System.FM pro zablokované překonfigurování. Nebo repliky nepotvrzující operace. Pomocí rutiny PowerShellu se `Get-ServiceFabricDeployedReplicaDetail` dá určit průběh všech replik. Problém se nachází v replikách, jejichž `LastAppliedReplicationSequenceNumber` hodnota je za hodnotou primární `CommittedSequenceNumber` .

- **IReplicator. BuildReplica ( \<Remote ReplicaId> )**: Toto upozornění indikuje problém v procesu sestavení. Další informace najdete v tématu [životní cyklus repliky](service-fabric-concepts-replica-lifecycle.md). Příčinou může být nepřesná konfigurace adresy replikátoru. Další informace najdete v tématech [Konfigurace stavového Reliable Services](service-fabric-reliable-services-configuration.md) a [určení prostředků v manifestu služby](service-fabric-service-manifest-resources.md). Může se také jednat o problém na vzdáleném uzlu.

### <a name="replicator-system-health-reports"></a>Replikace sestav stavu systému
**Fronta replikace je plná:** 
 **System. Replicator** hlásí upozornění, když je fronta replikace plná. V primárním případě se fronta replikace obvykle zaplní, protože jedna nebo více sekundárních replik je pomalé k potvrzení operací. V sekundárním případě k tomu obvykle dochází, když je služba pomalé pro použití operací. Upozornění je vymazáno, pokud již není fronta zaplněna.

* **SourceId**: System. Replicator
* **Vlastnost**: **PrimaryReplicationQueueStatus** nebo **SecondaryReplicationQueueStatus**, v závislosti na roli repliky.
* **Další kroky**: Pokud je sestava na primárním uzlu, ověřte připojení mezi uzly v clusteru. Pokud jsou všechna připojení v pořádku, může při použití operací dojít k nejméně jednomu pomalému sekundárnímu zpomalení s vysokou latencí disku. Pokud je sestava na sekundárním počítači, nejprve na uzlu ověřte využití disku a výkon. Pak zkontrolujte odchozí připojení z pomalého uzlu k primárnímu.

**RemoteReplicatorConnectionStatus:** 
 **System. Replicator** v primární replice hlásí upozornění v případě, že připojení k sekundárnímu (vzdálenému) replikátoru není v pořádku. Adresa vzdáleného replikátoru je zobrazena ve zprávě sestavy, což usnadňuje detekci, zda byla předána Chybná konfigurace, nebo pokud dojde k problémům se sítí mezi replikami.

* **SourceId**: System. Replicator
* **Vlastnost**: **RemoteReplicatorConnectionStatus**.
* **Další kroky**: Zkontrolujte chybovou zprávu a ujistěte se, že je správně nakonfigurovaná adresa vzdáleného replikátoru. Pokud je například vzdálený Replikátor otevřený pomocí adresy "localhost", není dosažitelný z vnějšku. Pokud adresa vypadá správně, ověřte připojení mezi primárním uzlem a vzdálenou adresou a najděte případné problémy se sítí.

### <a name="replication-queue-full"></a>Fronta replikace je plná.
**System. Replicator** hlásí upozornění, když je fronta replikace plná. V primárním případě se fronta replikace obvykle zaplní, protože jedna nebo více sekundárních replik je pomalé k potvrzení operací. V sekundárním případě k tomu obvykle dochází, když je služba pomalé pro použití operací. Upozornění je vymazáno, pokud již není fronta zaplněna.

* **SourceId**: System. Replicator
* **Vlastnost**: **PrimaryReplicationQueueStatus** nebo **SecondaryReplicationQueueStatus**, v závislosti na roli repliky.

### <a name="slow-naming-operations"></a>Pomalé operace pojmenování
**System. NamingService** hlásí stav primární repliky, když operace pojmenování trvá déle, než je přijatelné. Příklady operací pojmenování jsou [CreateServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync) nebo [DeleteServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.deleteserviceasync). Další metody najdete v části FabricClient. Můžete je například najít v části [metody správy služeb](/dotnet/api/system.fabric.fabricclient.servicemanagementclient) nebo [metody správy vlastností](/dotnet/api/system.fabric.fabricclient.propertymanagementclient).

> [!NOTE]
> Služba pojmenování překládá názvy služeb na umístění v clusteru. Uživatelé je můžou použít ke správě názvů a vlastností služby. Jedná se o Service Fabric s dělenou trvalou službou. Jeden z oddílů představuje *vlastníka autority*, který obsahuje metadata o všech Service Fabric názvech a službách. Názvy Service Fabric jsou namapovány na jiné oddíly označované jako oddíly *vlastníka názvu* , takže služba je rozšiřitelná. Přečtěte si další informace o [službě pojmenování](service-fabric-architecture.md).
> 
> 

Pokud operace pojmenování trvá déle, než se očekávalo, operace se označí pomocí sestavy upozornění na primární replice oddílu názvového serveru, který slouží k operaci. Pokud se operace úspěšně dokončí, upozornění se vymaže. Pokud se operace dokončí s chybou, zpráva o stavu obsahuje podrobnosti o chybě.

* **SourceId**: System. NamingService
* **Vlastnost**: začíná předponou "**Duration_**" a identifikuje pomalou operaci a název Service Fabric, na kterém je operace použita. Například pokud služba Create Service v názvu **Fabric:/MyApp/mojesluzba** trvá příliš dlouho, vlastnost je **Duration_AOCreateService. Fabric:/MyApp/mojesluzba**. "AO" odkazuje na roli oddílu názvů pro tento název a operaci.
* **Další kroky**: Zkontrolujte, proč se operace pojmenování nezdařila. Každá operace může mít různé hlavní příčiny. Například služba odstranění může být zablokovaná. Služba může být zablokovaná, protože hostitel aplikace udržuje v uzlu chybu kvůli chybě uživatele v kódu služby.

Následující příklad ukazuje operaci vytvoření služby. Operace trvala déle, než je nastavená doba trvání. "AO" opakuje a odesílá práci do "ne." Při poslední operaci s ČASOVÝm LIMITem se nedokončila žádná akce NO. V tomto případě je stejná replika primární pro role "AO" i "NO".

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

## <a name="deployedapplication-system-health-reports"></a>Sestavy o stavu systému DeployedApplication
**System. hosting** je autoritou u nasazených entit.

### <a name="activation"></a>Aktivace
Pokud se aplikace úspěšně aktivovala v uzlu, sestavy System. Hosting se nastavují jako OK. V opačném případě hlásí chybu.

* **SourceId**: System. hosting
* **Vlastnost**: **Aktivace**, včetně verze uvedení.
* **Další kroky**: Pokud je aplikace poškozená, zjistěte, proč se aktivace nezdařila.

Následující příklad ukazuje úspěšnou aktivaci:

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

### <a name="download"></a>Stáhnout
Při neúspěšném stažení balíčku aplikace hlásí System. Hosting.

* **SourceId**: System. hosting
* **Vlastnost**: **Stáhnout**, včetně verze uvedení.
* **Další kroky**: Zjistěte, proč se stahování na uzlu nezdařilo.

## <a name="deployedservicepackage-system-health-reports"></a>Sestavy o stavu systému DeployedServicePackage
**System. hosting** je autoritou u nasazených entit.

### <a name="service-package-activation"></a>Aktivace balíčku služby
Pokud je aktivace balíčku služby na uzlu úspěšná, sestavy System. hosting jsou v pořádku. V opačném případě hlásí chybu.

* **SourceId**: System. hosting
* **Vlastnost**: aktivace.
* **Další kroky**: Zjistěte, proč se aktivace nezdařila.

### <a name="code-package-activation"></a>Aktivace balíčku kódu
Pokud je aktivace úspěšná, jsou sestavy System. hosting pro každý balíček kódu v pořádku. Pokud se aktivace nezdařila, oznamuje, že je nakonfigurované upozornění. Pokud se **CodePackage** nepovede aktivovat nebo ukončí s chybou větší než nakonfigurovaná **CodePackageHealthErrorThreshold**, hostování hlásí chybu. Pokud balíček služby obsahuje více balíčků kódu, je pro každou z nich vygenerována aktivační zpráva.

* **SourceId**: System. hosting
* **Vlastnost**: používá předponu **CodePackageActivation** a obsahuje název balíčku kódu a vstupní bod jako *CodePackageActivation: CodePackageName: SetupEntryPoint/EntryPoint*. Například **CodePackageActivation: code: SetupEntryPoint**.

### <a name="service-type-registration"></a>Registrace typu služby
Pokud byl typ služby úspěšně zaregistrován, budou sestavy System. hosting uloženy jako OK. Hlásí chybu, pokud se registrace neudělala v čase, jak je nakonfigurované pomocí **ServiceTypeRegistrationTimeout**. Je-li modul runtime zavřen, je typ služby z uzlu zaregistrován a hostování oznamuje upozornění.

* **SourceId**: System. hosting
* **Vlastnost**: používá předponu **ServiceTypeRegistration** a obsahuje název typu služby. Například **ServiceTypeRegistration: FileStoreServiceType**.

Následující příklad ukazuje dobrý nasazený balíček služby:

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

### <a name="download"></a>Stáhnout
Pokud se stažení balíčku služby nepovede, hlásí System. hosting zprávu o chybě.

* **SourceId**: System. hosting
* **Vlastnost**: **Stáhnout**, včetně verze uvedení.
* **Další kroky**: Zjistěte, proč se stahování na uzlu nezdařilo.

### <a name="upgrade-validation"></a>Ověření upgradu
Pokud se ověření během upgradu nepovede nebo pokud se upgrade na uzlu nepovede, zastavuje se chyba System. Hosting.

* **SourceId**: System. hosting
* **Vlastnost**: používá předponu **FabricUpgradeValidation** a obsahuje verzi upgradu.
* **Popis**: odkazuje na zjištěnou chybu.

### <a name="undefined-node-capacity-for-resource-governance-metrics"></a>Nedefinovaná kapacita uzlu pro metriky zásad správného řízení prostředků
Když se v manifestu clusteru nedefinují kapacity uzlů a konfigurace pro automatické zjišťování je vypnutá, zobrazí se zpráva System. Hosting. Service Fabric vyvolá upozornění na stav vždy, když balíček služby, který používá Registry [zásad správného řízení prostředků](service-fabric-resource-governance.md) v zadaném uzlu.

* **SourceId**: System. hosting
* **Vlastnost**: **ResourceGovernance**.
* **Další kroky**: upřednostňovaný způsob, jak tento problém vyřešit, je změnit manifest clusteru tak, aby povoloval automatické zjišťování dostupných prostředků. Dalším způsobem je aktualizovat manifest clusteru s správně zadanými kapacitami uzlů pro tyto metriky.

## <a name="next-steps"></a>Další kroky
* [Zobrazit Service Fabric sestavy o stavu](service-fabric-view-entities-aggregated-health.md)

* [Postup hlášení a kontroly stavu služby](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

* [Místní monitorování a diagnostika služeb](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

* [Upgrade aplikace Service Fabric](service-fabric-application-upgrade.md)
