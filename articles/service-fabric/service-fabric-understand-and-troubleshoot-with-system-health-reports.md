---
title: Řešení potíží se systémovými stavovými sestavami
description: Popisuje sestavy stavu odeslané součástmi Azure Service Fabric a jejich využití pro řešení problémů clusteru nebo aplikací.
author: oanapl
ms.topic: conceptual
ms.date: 2/28/2018
ms.author: oanapl
ms.openlocfilehash: a76ae803b1283ce50d2f4e259943ce5ffcf0274c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282013"
---
# <a name="use-system-health-reports-to-troubleshoot"></a>Řešení problémů pomocí sestav o stavu systému
Součásti Azure Service Fabric poskytují sestavy stavu systému na všechny entity v clusteru ihned po vybalení. [Úložiště stavu](service-fabric-health-introduction.md#health-store) vytvoří a odstraní entity založené na systémových sestavách. Také je organizuje v hierarchii, která zachycuje interakce entit.

> [!NOTE]
> Chcete-li porozumět konceptům souvisejícím se stavem, přečtěte si další informace o [modelu stavu Service Fabric](service-fabric-health-introduction.md).
> 
> 

Sestavy stavu systému poskytují přehled o funkcích clusteru a aplikace a problémy s příznakem. U aplikací a služeb sestavy stavu systému ověřují, že entity jsou implementovány a chovají se správně z hlediska service fabric. Sestavy neposkytují žádné monitorování stavu obchodní logiky služby nebo zjišťování procesů, které nereagují. Uživatelské služby mohou obohatit data o stav informacemi specifickými pro jejich logiku.

> [!NOTE]
> Sestavy stavu odeslané uživateli watchdogs jsou viditelné pouze *po* vytvoření entity systémové součásti. Při odstranění entity úložiště stavu automaticky odstraní všechny sestavy stavu s ní spojené. Totéž platí při vytvoření nové instance entity. Příkladem je, když je vytvořena nová stavová instance repliky trvalé služby. Všechny sestavy přidružené ke staré instanci jsou odstraněny a vyčištěny z úložiště.
> 
> 

Sestavy systémových součástí jsou identifikovány zdrojem, který začíná "**System.**" „com.microsoft.intune.mam“. Watchdogs nelze použít stejnou předponu pro své zdroje, jako sestavy s neplatnými parametry jsou odmítnuty.

Podívejme se na některé systémové sestavy, abychom pochopili, co je spouští, a zjistili, jak opravit potenciální problémy, které představují.

> [!NOTE]
> Service Fabric pokračuje v přidávání sestav o podmínkách zájmu, které zlepšují přehled o tom, co se děje v clusteru a v aplikacích. Stávající sestavy lze vylepšit o další podrobnosti, které vám pomohou problém vyřešit rychleji.
> 
> 

## <a name="cluster-system-health-reports"></a>Sestavy stavu systému clusteru
Entita stavu clusteru se vytvoří automaticky v úložišti stavu. Pokud vše funguje správně, nemá systémovou zprávu.

### <a name="neighborhood-loss"></a>Ztráta okolí
**System.Federation** hlásí chybu, když zjistí ztrátu okolí. Sestava pochází z jednotlivých uzlů a ID uzlu je zahrnuto v názvu vlastnosti. Pokud dojde ke ztrátě jedné čtvrti v celém kroužku Service Fabric, můžete obvykle očekávat dvě události, které představují obě strany sestavy mezery. Pokud se ztratí více čtvrtí, existuje více událostí.

Sestava určuje časový limit globálního zapůjčení jako time-to-live (TTL). Sestava je nesnášena každou polovinu trvání TTL tak dlouho, dokud podmínka zůstává aktivní. Událost je automaticky odebrána po vypršení její platnosti. Chování odebrání při vypršení platnosti zajišťuje, že sestava je správně vyčištěna z úložiště stavu, a to i v případě, že uzel hlášení je mimo.

* **SourceId**: System.Federation
* **Vlastnost**: Začíná **sousedství** a obsahuje informace o uzlu.
* **Další kroky**: Zjistěte, proč je sousedství ztraceno. Zkontrolujte například komunikaci mezi uzly clusteru.

### <a name="rebuild"></a>Opětovné sestavení (Rebuild)

Služba Správce převzetí služeb při selhání (FM) spravuje informace o uzlech clusteru. Když FM ztratí svá data a přejde do ztráty dat, nemůže zaručit, že má nejaktuálnější informace o uzlech clusteru. V tomto případě systém prochází znovu sestavit a System.FM shromažďuje data ze všech uzlů v clusteru za účelem opětovného sestavení stavu. Někdy se kvůli problémům se sítí nebo uzly může obnovit nebo zastavit. Totéž se může stát u služby Master správce převzetí služeb při selhání (FMM). FMM je bezstavová systémová služba, která sleduje, kde jsou všechny FM v clusteru. Primární FMM je vždy uzel s ID nejblíže 0. Pokud se tento uzel přepne, je spuštěno opětovné sestavení.
Pokud dojde k jedné z předchozích podmínek, **System.FM** nebo **System.FMM** ji označí prostřednictvím zprávy o chybě. Opětovné sestavení může být zablokované v jedné ze dvou fází:

* **Čekání na vysílání**: FM/FMM čeká na odpověď na zprávu všesměrového vysílání z ostatních uzlů.

  * **Další kroky**: Zjistěte, zda došlo k problému se síťovým připojením mezi uzly.
* **Čekání na uzly**: FM/FMM již obdrželo odpověď na vysílání z jiných uzlů a čeká na odpověď z konkrétních uzlů. Sestava stavu uvádí uzly, pro které FM/FMM čeká na odpověď.
   * **Další kroky**: Prozkoumejte síťové připojení mezi FM/FMM a uvedenými uzly. Prozkoumejte každý uvedený uzel pro další možné problémy.

* **SourceID**: System.FM nebo System.FMM
* **Vlastnost:** Znovu sestavit.
* **Další kroky**: Prozkoumejte síťové připojení mezi uzly a také stav všech konkrétních uzlů, které jsou uvedeny v popisu sestavy stavu.

### <a name="seed-node-status"></a>Stav osiva
**System.FM** hlásí upozornění na úrovni clusteru, pokud některé uzly nejsou v pořádku. Semenné uzly jsou uzly, které udržují dostupnost základního clusteru. Tyto uzly pomáhají zajistit, že cluster zůstane až navázání matné adresy s jinými uzly a slouží jako tiebreakers během určitých druhů selhání sítě. Pokud většina uzly osiva jsou dole v clusteru a nejsou přeneseny zpět, cluster automaticky vypne. 

Osiva uzel není v pořádku, pokud jeho stav uzlu je dolů, odebrána nebo neznámý.
Varovná sestava pro stav osiva uzlu zobrazí všechny uzly není v pořádku s podrobnými informacemi.

* **SourceID**: System.FM
* **Vlastnost**: SeedNodeStatus
* **Další kroky**: Pokud se toto upozornění zobrazí v clusteru, postupujte podle následujících pokynů k opravě: Pro clusteru běží Service Fabric verze 6.5 nebo vyšší: Pro service fabric clusteru v Azure, po uplynutí název uzlu se zobrazí, Service Fabric se pokusí změnit na uzel bez osiva automaticky. Chcete-li k tomu dojít, ujistěte se, že počet uzlů bez semen v typu primárního uzlu je větší nebo roven počtu uzlů down seed. V případě potřeby přidejte další uzly do typu primárního uzlu, abyste toho dosáhli.
V závislosti na stavu clusteru může trvat nějakou dobu, než problém vyřešíte. Jakmile je to hotovo, varovná zpráva je automaticky vymazána.

Pro service fabric samostatný cluster, chcete-li vymazat varovnou zprávu, všechny uzly osiva musí být v pořádku. V závislosti na tom, proč jsou uzly osiva nefunkční, je třeba provést různé akce: pokud je uzel osiva dolů, uživatelé musí tento uzel semen y vyvolat; Pokud je uzel osiva odebrán nebo neznámý, musí být tento uzel osiva [odebrán z clusteru](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-windows-server-add-remove-nodes).
Zpráva upozornění je automaticky vymazána, když všechny uzly osiva se stanou v pořádku.

Pro clusteru spuštěná verze Service Fabric starší než 6.5: V tomto případě je nutné vymazat varovnou zprávu ručně. **Uživatelé by se měli ujistit, že všechny uzly osiva se stanou zdravými před vymazáním sestavy**: pokud je uzel osiva dolů, uživatelé musí tento uzel osiva přenést nahoru;pokud je uzel osiva odebrán nebo neznámý, tento uzel osiva musí být odebrán z clusteru.
Poté, co se všechny uzly osiva stanou zdravými, použijte následující příkaz z prostředí Powershell k [vymazání varovné zprávy](https://docs.microsoft.com/powershell/module/servicefabric/send-servicefabricclusterhealthreport):

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
**System.FabricNode** hlásí upozornění, když certifikáty používané uzlem jsou blízko vypršení platnosti. Na každý uzel jsou k dispozici tři certifikáty: **Certificate_cluster**, **Certificate_server**a **Certificate_default_client**. Pokud je vypršení platnosti alespoň dva týdny pryč, stav sestavy je v pořádku. Pokud je vypršení platnosti do dvou týdnů, typ sestavy je upozornění. TTL těchto událostí je nekonečná a jsou odebrány při uzel opustí clusteru.

* **SourceId**: System.FabricNode
* **Vlastnost**: Začíná **certifikátem** a obsahuje další informace o typu certifikátu.
* **Další kroky**: Aktualizace certifikátů, pokud jsou blízko vypršení platnosti.

### <a name="load-capacity-violation"></a>Narušení nosnosti
Service Fabric Balancer hlásí upozornění, když zjistí porušení kapacity uzlu.

* **SourceId**: System.PLB
* **Vlastnost**: Začíná **kapacitou**.
* **Další kroky**: Zkontrolujte zadaný metriky a zobrazit aktuální kapacitu na uzlu.

### <a name="node-capacity-mismatch-for-resource-governance-metrics"></a>Neshoda kapacity uzlů pro metriky zásad správného řízení prostředků
System.Hosting hlásí upozornění, pokud jsou definované kapacity uzlů v manifestu clusteru větší než skutečné kapacity uzlů pro metriky zásad správného řízení prostředků (paměť ová jádra a jádra procesoru). Sestava stavu se zobrazí při prvním balíčku služby, který používá registry [zásad správného řízení prostředků](service-fabric-resource-governance.md) na určeném uzlu.

* **SourceId**: System.Hosting
* **Vlastnost**: **ResourceGovernance**.
* **Další kroky**: Tento problém může být problém, protože řídící balíčky služeb nejsou vynuceny podle očekávání a [zásadsprávné řízení prostředků](service-fabric-resource-governance.md) nefunguje správně. Aktualizujte manifest clusteru se správnými kapacitami uzlů pro tyto metriky nebo je nezadávejte a nechte Service Fabric automaticky rozpoznat dostupné prostředky.

## <a name="application-system-health-reports"></a>Sestavy stavu aplikačního systému
System.CM, která představuje službu Správce clusteru, je autorita, která spravuje informace o aplikaci.

### <a name="state"></a>Stav
System.CM se stavů jako OK, pokud byla aplikace vytvořena nebo aktualizována. Informuje úložiště stavu při odstranění aplikace tak, aby ji lze odebrat z úložiště.

* **SourceId**: System.CM
* **Nemovitosti**: Stát.
* **Další kroky**: Pokud byla aplikace vytvořena nebo aktualizována, měla by obsahovat sestavu stavu Správce clusteru. V opačném případě zkontrolujte stav aplikace vydáním dotazu. Použijte například rutinu prostředí PowerShell **Get-ServiceFabricApplication -ApplicationName** *applicationName*.

Následující příklad ukazuje událost stavu v aplikaci **fabric:/WordCount:**

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

## <a name="service-system-health-reports"></a>Sestavy stavu systému služeb
System.FM, která představuje službu Správce převzetí služeb při selhání, je autorita, která spravuje informace o službách.

### <a name="state"></a>Stav
System.FM se hlásí jako OK, když byla služba vytvořena. Odstraní entitu z úložiště stavu při odstranění služby.

* **SourceId**: System.FM
* **Nemovitosti**: Stát.

Následující příklad ukazuje událost stavu na **struktuře služby:/WordCount/WordCountWebService**:

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
**System.PLB** hlásí chybu, když zjistí, že aktualizace služby je korelována s jinou službou, která vytvoří řetězec spřažení. Sestava je vymazána, když dojde k úspěšné aktualizaci.

* **SourceId**: System.PLB
* **Vlastnost**: **ServiceDescription**.
* **Další kroky**: Zkontrolujte korelované popisy služeb.

## <a name="partition-system-health-reports"></a>Sestavy stavu systému oddílů
System.FM, která představuje službu Správce převzetí služeb při selhání, je autorita, která spravuje informace o oddílech služby.

### <a name="state"></a>Stav
System.FM se hlásí jako OK, pokud byl oddíl vytvořen a je v pořádku. Odstraní entitu z úložiště stavu při odstranění oddílu.

Pokud je oddíl nižší než minimální počet replik, hlásí chybu. Pokud oddíl není nižší než minimální počet replik, ale je pod počtem cílových replik, hlásí upozornění. Pokud je oddíl ve ztrátě kvora, System.FM hlásí chybu.

Mezi další významné události patří upozornění, když změna konfigurace trvá déle, než bylo očekáváno, a pokud sestavení trvá déle, než bylo očekáváno. Očekávané časy pro sestavení a rekonfigurace jsou konfigurovatelné na základě scénářů služby. Například pokud služba má terabajt stavu, jako je například Azure SQL Database, sestavení trvá déle než pro službu s malým množstvím stavu.

* **SourceId**: System.FM
* **Nemovitosti**: Stát.
* **Další kroky**: Pokud stav není v pořádku, je možné, že některé repliky nebyly vytvořeny, otevřeny nebo povýšeny na primární nebo sekundární správně. 

Pokud popis popisuje ztrátu kvora, pak zkoumání podrobné sestavy stavu pro repliky, které jsou dolů a jejich uvedení zpět nahoru pomáhá převést oddíl zpět do režimu online.

Pokud popis popisuje oddíl uvízl v [rekonfiguraci](service-fabric-concepts-reconfiguration.md), pak sestava stavu na primární repliky poskytuje další informace.

Pro jiné System.FM zprávy o stavu by sestavy repliky nebo oddíl nebo služby z jiných součástí systému. 

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

Následující příklad ukazuje stav oddílu, který je pod počtem replik cíle. Dalším krokem je získat popis oddílu, který ukazuje, jak je nakonfigurován: **MinReplicaSetSize** je tři a **TargetReplicaSetSize** je sedm. Pak získat počet uzlů v clusteru, což je v tomto případě pět. Takže v tomto případě nelze umístit dvě repliky, protože cílový počet replik je vyšší než počet uzlů k dispozici.

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

Následující příklad ukazuje stav oddílu, který je zaseknutý v rekonfiguraci z důvodu, že uživatel nerespektuje token zrušení v **runasync** metoda. Zkoumání sestavy stavu jakékoli repliky označené jako primární (P) může pomoci přejít k podrobnostem dále do problému.

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
Tato sestava stavu zobrazuje stav replik oddílu, který prochází rekonfigurací: 

```
  P/S Ready Node1 131482789658160654
  S/P Ready Node2 131482789688598467
  S/S Ready Node3 131482789688598468
```

Pro každou repliku obsahuje sestava stavu:
- Předchozí konfigurační role
- Aktuální konfigurační role
- [Stav repliky](service-fabric-concepts-replica-lifecycle.md)
- Uzel, na kterém je replika spuštěna
- ID repliky

V případě, jako je příklad, je zapotřebí dalšího šetření. Prozkoumejte stav každé jednotlivé repliky počínaje `Primary` repliky označené jako a `Secondary` (131482789658160654 a 131482789688598467) v předchozím příkladu.

### <a name="replica-constraint-violation"></a>Porušení omezení repliky
**System.PLB** hlásí upozornění, pokud zjistí porušení omezení repliky a nelze umístit všechny repliky oddílů. Podrobnosti sestavy ukazují, která omezení a vlastnosti brání umístění repliky.

* **SourceId**: System.PLB
* **Vlastnost**: Začíná **ReplicaConstraintViolation**.

## <a name="replica-system-health-reports"></a>Sestavy stavu systému repliky
**System.RA**, který představuje součást agenta pro změnu konfigurace, je autoritou pro stav repliky.

### <a name="state"></a>Stav
System.RA hlásí ok po vytvoření repliky.

* **SourceId**: System.RA
* **Nemovitosti**: Stát.

Následující příklad ukazuje zdravou repliku:

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

### <a name="replicaopenstatus-replicaclosestatus-replicachangerolestatus"></a>Stav replicaopenstatus, stav uzavření repliky, stav ReplicaChangeRoleStatus
Tato vlastnost se používá k označení upozornění nebo selhání při pokusu o otevření repliky, zavření repliky nebo přechodu repliky z jedné role do druhé. Další informace naleznete v [tématu Životní cyklus repliky](service-fabric-concepts-replica-lifecycle.md). Selhání může být výjimky vyvolána z volání rozhraní API nebo selhání procesu hostitele služby během této doby. Pro selhání z důvodu volání rozhraní API z kódu Jazyka C#, Service Fabric přidá výjimku a trasování zásobníku do sestavy stavu.

Tato upozornění stavu jsou vyvolány po opakování akce místně několikrát (v závislosti na zásadách). Service Fabric opakuje akce až do maximální prahové hodnoty. Po dosažení maximální prahové hodnoty se může pokusit jednat o nápravu situace. Tento pokus může způsobit, že tato upozornění získat vymazány, protože se vzdává na akci v tomto uzlu. Například pokud se replika nedaří otevřít na uzlu, Service Fabric vyvolá upozornění stavu. Pokud se replika nadále nedaří otevřít, Service Fabric provádí vlastní opravy. Tato akce může zahrnovat pokus o stejnou operaci na jiném uzlu. Tento pokus způsobí, že upozornění aktivována pro tuto repliku, které mají být vymazány. 

* **SourceId**: System.RA
* **Vlastnost:** **ReplicaOpenStatus**, **ReplicaCloseStatus**a **ReplicaChangeChangeStatus**.
* **Další kroky**: Prozkoumejte kód služby nebo výpisy stavu systému a zjistěte, proč se operace nedaří.

Následující příklad ukazuje stav repliky, která `TargetInvocationException` je vyvolání z jeho otevřené metody. Popis obsahuje bod **selhání, IStatefulServiceReplica.Open**, typ výjimky **TargetInvocationException**a trasování zásobníku.

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

Následující příklad ukazuje repliku, která se během zavření neustále zhroutí:

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
Tato vlastnost se používá k označení, kdy replika provádějící [změnu konfigurace](service-fabric-concepts-reconfiguration.md) zjistí, že změna konfigurace je zastavena nebo zablokovaná. Tato sestava stavu může být v replice, jejíž aktuální role je primární, s výjimkou případů odkládací primární rekonfigurace, kde může být na replice, která je právě degradován z primární na aktivní sekundární.

Rekonfigurace může být přilepená z jednoho z následujících důvodů:

- Akce na místní replice, stejná replika jako ta, která provádí změnu konfigurace, není dokončena. V takovém případě může zkoumání zpráv o stavu této repliky z jiných součástí System.RAP nebo System.RE poskytnout další informace.

- Akce není dokončení na vzdálené repliky. Repliky, pro které jsou čekající akce jsou uvedeny v sestavě stavu. Další šetření by mělo být provedeno na zprávy o stavu pro tyto vzdálené repliky. Může také být problémy s komunikací mezi tímto uzlem a vzdálený uzel.

Ve výjimečných případech může být změna konfigurace zablokovaná z důvodu komunikace nebo jiných problémů mezi tímto uzlem a službou Správce převzetí služeb při selhání.

* **SourceId**: System.RA
* **Vlastnost**: Změna konfigurace.
* **Další kroky**: Prozkoumejte místní nebo vzdálené repliky v závislosti na popisu sestavy stavu.

Následující příklad ukazuje zprávu o stavu, kde je změna konfigurace zablokovaná v místní replice. V této ukázce je kvůli službě, která nerespektuje token zrušení.

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

Následující příklad ukazuje zprávu o stavu, kde je změna konfigurace zablokovaná a čeká na odpověď ze dvou vzdálených replik. V tomto příkladu jsou tři repliky v oddílu, včetně aktuální primární. 

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

Tato sestava stavu ukazuje, že změna konfigurace je přilepená čekání na odpověď ze dvou replik: 

```
    P/I Down 40 131483956244554282
    S/S Down 20 131483956274972403
```

Pro každou repliku jsou uvedeny následující informace:
- Předchozí konfigurační role
- Aktuální konfigurační role
- [Stav repliky](service-fabric-concepts-replica-lifecycle.md)
- ID uzlu
- ID repliky

Chcete-li odblokovat změnu konfigurace:
- Dolů **down** repliky by měly být vychovány. 
- **Repliky vsestavení** by měl dokončit sestavení a přechod na připraven.

### <a name="slow-service-api-call"></a>Pomalé volání rozhraní API služby
**System.RAP** a **System.Replicator** hlásí upozornění, pokud volání kódu služby uživatele trvá déle než nakonfigurovaný čas. Po dokončení volání je upozornění vymazáno.

* **SourceId**: System.RAP nebo System.Replicator
* **Vlastnost**: Název pomalé rozhraní API. Popis obsahuje další podrobnosti o době, kdy rozhraní API čeká na vyřízení.
* **Další kroky**: Zjistěte, proč volání trvá déle, než bylo očekáváno.

Následující příklad ukazuje událost stavu ze system.RAP pro spolehlivou službu, která není ctít zrušení token u **RunAsync**:

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

Vlastnost a text označují, které rozhraní API se zaseklo. Další kroky, které je třeba provést pro různá zablokovaná api, se liší. Jakékoli rozhraní API na *IStatefulServiceReplica* nebo *IStatelessServiceInstance* je obvykle chyba v kódu služby. Následující část popisuje, jak se tyto přeložit do [modelu spolehlivé služby](service-fabric-reliable-services-lifecycle.md):

- **IStatefulServiceReplica.Open**: Toto upozornění `CreateServiceInstanceListeners`označuje, že volání `ICommunicationListener.OpenAsync` `OnOpenAsync` , , nebo pokud je přepsáno, je zablokované.

- **IStatefulServiceReplica.Close** a **IStatefulServiceReplica.Abort**: Nejběžnější případ je služba, která nerespektuje token zrušení předaný do `RunAsync`. Může se také `ICommunicationListener.CloseAsync`nalepit , `OnCloseAsync` nebo pokud je přepsáno.

- **IStatefulServiceReplica.ChangeRole(S)** a **IStatefulServiceReplica.ChangeRole(N)**: Nejběžnější případ je služba, která nerespektuje token zrušení předaný do `RunAsync`. V tomto scénáři nejlepším řešením je restartování repliky.

- **IStatefulServiceReplica.ChangeRole(P)**: Nejběžnější případ je, že služba nevrátila úlohu z `RunAsync`.

Ostatní volání rozhraní API, které můžete uvíznout jsou na rozhraní **IReplicator.** Například:

- **IReplicator.CatchupReplicaSet**: Toto upozornění označuje jednu ze dvou věcí. Repliky nejsou dostatečné. Chcete-li zjistit, zda se jedná o tento případ, podívejte se na stav replik y repliky v oddílu nebo System.FM sestavy stavu pro zablokovanou změnu konfigurace. Nebo repliky neuznávají operace. Rutina prostředí PowerShell `Get-ServiceFabricDeployedReplicaDetail` lze určit průběh všech replik. Problém spočívá v `LastAppliedReplicationSequenceNumber` replikách, jejichž hodnota `CommittedSequenceNumber` je za primární hodnotu.

- **IReplicator.BuildReplica(\<Vzdálené ReplicaId>)**: Toto upozornění označuje problém v procesu sestavení. Další informace naleznete v [tématu Životní cyklus repliky](service-fabric-concepts-replica-lifecycle.md). Může to být způsobeno chybnou konfigurací adresy replikátoru. Další informace naleznete [v tématu Konfigurace stavových spolehlivých služeb](service-fabric-reliable-services-configuration.md) a Určení prostředků v [manifestu služby](service-fabric-service-manifest-resources.md). Může to být také problém na vzdáleném uzlu.

### <a name="replicator-system-health-reports"></a>Sestavy stavu systému replikátorů
**Replikační fronta je plná:**
**System.Replicator** hlásí upozornění, když je fronta replikace plná. Na primární replikace fronty obvykle stane plný, protože jeden nebo více sekundárních replik jsou pomalé potvrdit operace. Na sekundární, k tomu obvykle dochází, když služba je pomalé použití operací. Upozornění je vymazána, pokud fronta již není plná.

* **SourceId**: System.Replicator
* **Vlastnost:** **PrimaryReplicationQueueStatus** nebo **SecondaryReplicationQueueStatus**, v závislosti na roli repliky.
* **Další kroky**: Pokud se sestava nachází na primární, zkontrolujte připojení mezi uzly v clusteru. Pokud jsou všechna připojení v pořádku, může existovat alespoň jeden pomalý sekundární s vysokou latencí disku použít operace. Pokud se sestava nachází na sekundární, zkontrolujte nejprve využití disku a výkon na uzlu. Potom zkontrolujte odchozí připojení z pomalého uzlu k primárnímu.

**RemoteReplicatorConnectionStatus:**
**System.Replicator** na primární replice hlásí upozornění, když připojení k sekundárnímu (vzdálenému) replikátoru není v pořádku. Adresa vzdáleného replikátora je zobrazena ve zprávě sestavy, což usnadňuje zjištění, zda byla předána nesprávná konfigurace nebo zda existují problémy se sítí mezi replikátory.

* **SourceId**: System.Replicator
* **Vlastnost:** **RemoteReplicatorConnectionStatus**.
* **Další kroky**: Zkontrolujte chybovou zprávu a zkontrolujte, zda je adresa vzdáleného replikátoru správně nakonfigurována. Například pokud vzdálený replikátor je otevřen s "localhost" naslouchací adresu, není dosažitelný zvenčí. Pokud adresa vypadá správně, zkontrolujte připojení mezi primárním uzlem a vzdálenou adresou, abyste našli případné problémy se sítí.

### <a name="replication-queue-full"></a>Replikační fronta je plná.
**System.Replicator** hlásí upozornění, když je replikační fronta plná. Na primární replikace fronty obvykle stane plný, protože jeden nebo více sekundárních replik jsou pomalé potvrdit operace. Na sekundární, k tomu obvykle dochází, když služba je pomalé použití operací. Upozornění je vymazána, pokud fronta již není plná.

* **SourceId**: System.Replicator
* **Vlastnost:** **PrimaryReplicationQueueStatus** nebo **SecondaryReplicationQueueStatus**, v závislosti na roli repliky.

### <a name="slow-naming-operations"></a>Operace pojmenování pomalého
**System.NamingService** hlásí stav na jeho primární repliky při pojmenování operace trvá déle, než přijatelné. Příklady operací pojmenování jsou [CreateServiceAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync) nebo [DeleteServiceAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient.deleteserviceasync). Další metody lze nalézt v části FabricClient. Například je lze nalézt v rámci [metod správy služeb](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient) nebo metod správy [vlastností](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.propertymanagementclient).

> [!NOTE]
> Služba Pojmenování překládá názvy služeb do umístění v clusteru. Uživatelé ji mohou použít ke správě názvů služeb a vlastností. Je to služba Service Fabric rozdělena trvalé služby. Jeden z oddílů představuje *vlastníka autority*, který obsahuje metadata o všech názvech a službách Service Fabric. Názvy Service Fabric jsou mapovány na různé oddíly s názvem *Name Owner* oddíly, takže služba je rozšiřitelná. Přečtěte si další informace o [službě Pojmenování](service-fabric-architecture.md).
> 
> 

Pokud operace pojmenování trvá déle, než bylo očekáváno, je operace označena varovnou zprávou o primární replice oddílu služby Pojmenování, který operaci obsluhuje. Pokud je operace úspěšně dokončena, upozornění je vymazána. Pokud operace dokončí s chybou, sestava stavu obsahuje podrobnosti o chybě.

* **SourceId**: System.NamingService
* **Vlastnost**: Spustí se předponou**Duration_** a identifikuje pomalou operaci a název Service Fabric, na který je operace použita. Například pokud vytvořit službu na název **fabric:/MyApp/MyService** trvá příliš dlouho, vlastnost je **Duration_AOCreateService.fabric:/MyApp/MyService**. "AO" odkazuje na roli pojmenování oddílu pro tento název a operaci.
* **Další kroky**: Zkontrolujte, proč se operace Pojmenování nezdaří. Každá operace může mít různé příčiny. Například odstranění služby může být zablokovat. Služba může být zablokovaná, protože hostitel aplikace stále dochází k selhání v uzlu z důvodu chyby uživatele v kódu služby.

Následující příklad ukazuje operaci vytvořit službu. Operace trvala déle než nakonfigurovaná doba trvání. "AO" opakuje a odesílá práci do "NE." "NE" dokončilo poslední operaci s TIMEOUT. V tomto případě je stejná replika primární pro role "AO" a "NE".

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

## <a name="deployedapplication-system-health-reports"></a>Sestavy stavu nasazeného systému aplikace
**System.Hosting** je autorita na nasazených entitách.

### <a name="activation"></a>Aktivace
System.Hosting hlásí jako OK, když byla aplikace úspěšně aktivována na uzlu. V opačném případě hlásí chybu.

* **SourceId**: System.Hosting
* **Vlastnost**: **Aktivace**včetně verze pro zavedení.
* **Další kroky**: Pokud aplikace není v pořádku, zjistěte, proč se aktivace nezdařila.

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
System.Hosting hlásí chybu, pokud se stahování balíčku aplikace nezdaří.

* **SourceId**: System.Hosting
* **Vlastnost**: **Stáhnout**, včetně verze pro zavedení.
* **Další kroky**: Zjistěte, proč se stahování v uzlu nezdařilo.

## <a name="deployedservicepackage-system-health-reports"></a>Sestavy stavu systému DeployedServicePackage
**System.Hosting** je autorita na nasazených entitách.

### <a name="service-package-activation"></a>Aktivace balíčku služeb
System.Hosting hlásí jako OK, pokud je aktivace balíčku služeb v uzlu úspěšná. V opačném případě hlásí chybu.

* **SourceId**: System.Hosting
* **Vlastnost**: Aktivace.
* **Další kroky**: Zjistěte, proč se aktivace nezdařila.

### <a name="code-package-activation"></a>Aktivace balíčku kódu
System.Hosting hlásí jako OK pro každý balíček kódu, pokud je aktivace úspěšná. Pokud se aktivace nezdaří, hlásí upozornění jako nakonfigurované. Pokud **codepackage** neaktivuje nebo ukončí s chybou větší než nakonfigurovaná **CodePackageHealthErrorThreshold**, hostování hlásí chybu. Pokud balíček služby obsahuje více balíčků kódu, je pro každý z nich vygenerována aktivační zpráva.

* **SourceId**: System.Hosting
* **Vlastnost**: Používá předponu **CodePackageActivation** a obsahuje název balíčku kódu a vstupníbod jako *CodePackageActivation:CodePackageName:SetupEntryPoint/EntryPoint*. Například **CodePackageActivation:Code:SetupEntryPoint**.

### <a name="service-type-registration"></a>Registrace typu služby
System.Hosting hlásí jako OK, pokud typ služby byl úspěšně zaregistrován. Hlásí chybu, pokud registrace nebyla provedena včas, nakonfigurované pomocí **ServiceTypeRegistrationTimeout**. Pokud je za běhu uzavřen, typ služby je unregistered z uzlu a hostování hlásí upozornění.

* **SourceId**: System.Hosting
* **Vlastnost**: Používá předponu **ServiceTypeRegistration** a obsahuje název typu služby. Například **ServiceTypeRegistration:FileStoreServiceType**.

Následující příklad ukazuje balíček nasazených služeb v pořádku:

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
System.Hosting hlásí chybu, pokud se stahování balíčku služeb nezdaří.

* **SourceId**: System.Hosting
* **Vlastnost**: **Stáhnout**, včetně verze pro zavedení.
* **Další kroky**: Zjistěte, proč se stahování v uzlu nezdařilo.

### <a name="upgrade-validation"></a>Ověření upgradu
System.Hosting hlásí chybu, pokud se ověření během upgradu nezdaří nebo pokud se upgrade nezdaří v uzlu.

* **SourceId**: System.Hosting
* **Vlastnost**: Používá předponu **FabricUpgradeValidation** a obsahuje verzi upgradu.
* **Popis**: Odkazuje na došlo k chybě.

### <a name="undefined-node-capacity-for-resource-governance-metrics"></a>Nedefinovaná kapacita uzlu pro metriky zásad správného řízení prostředků
System.Hosting hlásí upozornění, pokud nejsou v manifestu clusteru definovány kapacity uzlů a konfigurace pro automatickou detekci je vypnutá. Service Fabric vyvolá upozornění na stav vždy, když balíček služby, který používá registry [zásad správného řízení prostředků](service-fabric-resource-governance.md) na zadaném uzlu.

* **SourceId**: System.Hosting
* **Vlastnost**: **ResourceGovernance**.
* **Další kroky**: Upřednostňovaným způsobem, jak tento problém překonat, je změna manifestu clusteru a povolení automatického zjišťování dostupných prostředků. Dalším způsobem je aktualizace manifestu clusteru s správně zadanými kapacitami uzlů pro tyto metriky.

## <a name="next-steps"></a>Další kroky
* [Zobrazit sestavy stavu service fabric](service-fabric-view-entities-aggregated-health.md)

* [Jak nahlásit a zkontrolovat stav služby](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

* [Místní monitorování a diagnostika služeb](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

* [Upgrade aplikace Service Fabric](service-fabric-application-upgrade.md)

