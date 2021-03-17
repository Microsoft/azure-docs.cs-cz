---
title: Integrace správy Správce prostředků clusteru
description: Přehled integračních bodů mezi Správce prostředků clusteru a správou Service Fabric.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: ae80ac5833e90164fc4ff92010fd1830ae932cd2
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2020
ms.locfileid: "92174035"
---
# <a name="cluster-resource-manager-integration-with-service-fabric-cluster-management"></a>Integrace správce prostředků clusteru s Service Fabric správu clusterů
Cluster Service Fabric Správce prostředků nereaguje na inovace v Service Fabric, ale je součástí této služby. První způsob, jakým cluster Správce prostředků pomáhá se správou, je sledovat požadovaný stav clusteru a služby uvnitř něj. Cluster Správce prostředků odesílá zprávy o stavu, když nedokáže cluster vložit do požadované konfigurace. Pokud například není dostatečná kapacita, cluster Správce prostředků odesílá upozornění na stav a chyby, které signalizují problém. Další integrací se musí udělat s tím, jak upgrade funguje. Cluster Správce prostředků během upgradu mírně mění jeho chování.  

## <a name="health-integration"></a>Integrace stavu
Cluster Správce prostředků stále sleduje pravidla, která jste definovali pro umístění služeb. Sleduje také zbývající kapacitu pro každou metriku v uzlech a v clusteru a v clusteru jako celek. Pokud zařízení nesplňuje tato pravidla, nebo pokud není dostatečná kapacita, budou generována upozornění na stav a chyby. Například pokud uzel překročí kapacitu a Správce prostředků clusteru se pokusí tuto situaci opravit přesunutím služeb. Pokud to nedokáže opravit situaci, vygeneruje upozornění na stav označující, který uzel je nad kapacitou, a pro které metriky.

Dalším příkladem upozornění na stav Správce prostředků je porušení omezení umístění. Pokud jste například definovali omezení umístění (například `“NodeColor == Blue”` ) a správce prostředků zjistí porušení tohoto omezení, vygeneruje upozornění na stav. To platí pro vlastní omezení a výchozí omezení (například pro doménu selhání a omezení domény upgradu).

Tady je příklad jedné takové sestavy o stavu. V tomto případě je sestava stavu určena pro jeden z oddílů systémové služby. Zpráva o stavu znamená, že repliky tohoto oddílu jsou dočasně zabaleny do příliš málo domén upgradu.

```posh
PS C:\Users\User > Get-ServiceFabricPartitionHealth -PartitionId '00000000-0000-0000-0000-000000000001'


PartitionId           : 00000000-0000-0000-0000-000000000001
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.PLB', Property='ReplicaConstraintViolation_UpgradeDomain', HealthState='Warning', ConsiderWarningAsError=false.

ReplicaHealthStates   :
                        ReplicaId             : 130766528804733380
                        AggregatedHealthState : Ok

                        ReplicaId             : 130766528804577821
                        AggregatedHealthState : Ok

                        ReplicaId             : 130766528854889931
                        AggregatedHealthState : Ok

                        ReplicaId             : 130766528804577822
                        AggregatedHealthState : Ok

                        ReplicaId             : 130837073190680024
                        AggregatedHealthState : Ok

HealthEvents          :
                        SourceId              : System.PLB
                        Property              : ReplicaConstraintViolation_UpgradeDomain
                        HealthState           : Warning
                        SequenceNumber        : 130837100116930204
                        SentAt                : 8/10/2015 7:53:31 PM
                        ReceivedAt            : 8/10/2015 7:53:33 PM
                        TTL                   : 00:01:05
                        Description           : The Load Balancer has detected a Constraint Violation for this Replica: fabric:/System/FailoverManagerService Secondary Partition 00000000-0000-0000-0000-000000000001 is
                        violating the Constraint: UpgradeDomain Details: UpgradeDomain ID -- 4, Replica on NodeName -- Node.8 Currently Upgrading -- false Distribution Policy -- Packing
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Ok->Warning = 8/10/2015 7:13:02 PM, LastError = 1/1/0001 12:00:00 AM
```

Tady je tato zpráva o stavu, kterou nám řekne:

1. Všechny samotné repliky jsou v pořádku: každá má `AggregatedHealthState : Ok`
2. V tuto chvíli je porušené omezení distribuce domény upgradu. To znamená, že konkrétní upgradovací doména má více replik z tohoto oddílu, než by měl.
3. Který uzel obsahuje repliku, která způsobuje narušení. V tomto případě je to uzel s *uzlem Name. 8*
4. Zda aktuálně probíhá upgrade pro tento oddíl (aktuálně probíhá upgrade--false)
5. Zásady distribuce pro tuto službu: "zásady distribuce--balení". Řídí se `RequireDomainDistribution` [zásadami umístění](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md#requiring-replica-distribution-and-disallowing-packing). *Balení* znamená, že v tomto případě DomainDistribution _nebylo vyžadováno,_ takže víme, že pro tuto službu nebyla zadána zásada umístění. 
6. Když se nastala sestava-8/10/2015 7:13:02 ODP.

Takové informace, jako jsou tyto výzvy, upozorňují na to, že se něco pokazilo a že se také používá ke zjištění a zastavení chybných upgradů. V takovém případě chceme zjistit, proč Správce prostředků musela zabalit repliky do upgradovací domény. Balení je obvykle přechodný, protože uzly v jiných doménách upgradu byly mimo provoz, například.

Řekněme, že Správce prostředků clusteru se snaží umístit některé služby, ale nejsou k dispozici žádná řešení, která by fungovala. Pokud se služby nedají umístit, obvykle se jedná o jeden z následujících důvodů:

1. Některá přechodná podmínka znemožnila, aby tato instance služby nebo replika nebyla správně umístěna.
2. Požadavky na umístění služby jsou nevyhovující.

V těchto případech se Správce prostředků sestavy o stavu z clusteru, které vám pomůžou zjistit, proč službu nejde umístit. Tento proces se nazývá sekvence eliminace omezení. Během této doby provede systém prochází nakonfigurovanými omezeními, která ovlivňují službu, a zaznamenává, co eliminují. Tímto způsobem, kdy není možné umístit služby, můžete zjistit, které uzly byly eliminovány a proč.

## <a name="constraint-types"></a>Typy omezení
Pojďme se o každé z různých omezení v těchto sestavách stavů spojit. V případě, že repliky nejdou umístit, zobrazí se zprávy o stavu související s těmito omezeními.

* **ReplicaExclusionStatic** a **ReplicaExclusionDynamic**: Tato omezení označují, že řešení bylo odmítnuto, protože dva objekty služby ze stejného oddílu by musely být umístěny do stejného uzlu. Tato možnost není povolená, protože v takovém případě by selhání tohoto uzlu mělo vliv na oddíl. ReplicaExclusionStatic a ReplicaExclusionDynamic jsou skoro stejné pravidlo a rozdíly nezáleží na tom. Pokud vidíte sekvenci eliminace omezení obsahující omezení ReplicaExclusionStatic nebo ReplicaExclusionDynamic, cluster Správce prostředků považuje za to, že nejsou k dispozici dostatek uzlů. To vyžaduje zbývající řešení pro použití těchto neplatných míst, která nejsou povolena. Ostatní omezení v sekvenci obvykle říkají, proč se uzly odstraňují na prvním místě.
* **PlacementConstraint**: Pokud se zobrazí tato zpráva, znamená to, že jsme některé uzly vyloučili, protože neodpovídaly omezením umístění služby. V rámci této zprávy sledujeme aktuálně konfigurovaná omezení umístění. To je normální, pokud máte definováno omezení umístění. Pokud je ale omezení umístění nesprávně příčinou příliš velkého počtu uzlů, které by bylo možné odstranit, jedná se o to, jak si všimnete.
* **NodeCapacity**: Toto omezení znamená, že správce prostředků clusteru nemohly umístit repliky na označené uzly, protože by se daly předávat do kapacity.
* **Spřažení**: Toto omezení znamená, že se nám nepovedlo umístit repliku na ovlivněné uzly, protože by to způsobilo porušení omezení spřažení. Další informace o spřažení [najdete v tomto článku](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md) .
* **FaultDomain** a **UpgradeDomain**: Toto omezení eliminuje počet uzlů, pokud umístění repliky na uvedených uzlech způsobí balení v konkrétní chybě nebo upgradovací doméně. K dispozici je několik příkladů, které se týkají tohoto omezení, a to v tématu [omezení týkající se selhání a upgradu domény a výsledné chování](service-fabric-cluster-resource-manager-cluster-description.md) .
* **PreferredLocation**: Toto omezení byste normálně neměli vidět z řešení, protože se ve výchozím nastavení spouští jako optimalizace. Omezení upřednostňované umístění je také k dispozici během upgradů. Během upgradu se používá k přesunu služeb zpátky do umístění, kde byly při zahájení upgradu.

## <a name="blocklisting-nodes"></a>Uzly Blocklisting
Další zpráva o stavu, kterou cluster Správce prostředků, je v případě, že se uzly blocklisted. Blocklisting můžete představit jako dočasné omezení, které je automaticky použito pro vás. Uzly získají blocklisted, když při spouštění instancí tohoto typu služby dojde k opakovaným chybám. Uzly se blocklisted podle typu jednotlivých služeb. Uzel může být blocklisted pro jeden typ služby, ale ne pro jiný. 

Uvidíte, že Blocklisting se během vývoje často setká: některá chyba způsobí, že hostitel služby selže při spuštění, Service Fabric se pokusí vytvořit hostitele služby několikrát a dojde k selhání. Po několika pokusech uzel získá blocklisted a cluster Správce prostředků se pokusí službu vytvořit jinde. Pokud se toto selhání chová na více uzlech, je možné, že všechny platné uzly v clusteru jsou zablokované. Blocklisting může také odebrat tolik uzlů, které nestačí k úspěšnému spuštění služby, aby splňovaly požadované škálování. Obvykle se zobrazí další chyby nebo upozornění z Správce prostředků clusteru, což znamená, že se služba nachází pod požadovaným počtem replik nebo instancí a také se zprávami o stavu, které označují, co selhání vedlo k Blocklisting na prvním místě.

Blocklisting není trvalá podmínka. Po několika minutách se uzel odebere z seznamu blokovaných a Service Fabric může znovu aktivovat služby v tomto uzlu. Pokud se služby nadále nezdaří, uzel bude znovu blocklisted pro daný typ služby. 

### <a name="constraint-priorities"></a>Priority omezení

> [!WARNING]
> Změna priorit omezení se nedoporučuje a ve vašem clusteru může mít významný negativní dopad. Níže uvedené informace jsou k dispozici pro referenci na výchozí priority omezení a jejich chování. 
>

U všech těchto omezení jste si možná mysleli, že v systému jsou omezení domén selhání nejdůležitější. Aby bylo zajištěno, že nedošlo k porušení omezení domény selhání, je ochotno porušovat jiná omezení. "

Omezení lze konfigurovat s různými úrovněmi priority. Jsou to:

   - "pevná" (0)
   - "měkké" (1)
   - "optimalizace" (2)
   - "off" (-1). 
   
Většina omezení je ve výchozím nastavení nakonfigurována jako pevná omezení.

Změna priority omezení je neobvyklá. Byly zjištěny časy, kdy se priority omezení potřebují ke změně, obvykle k řešení nějaké jiné chyby nebo chování, které ovlivnilo prostředí. Obecně platí, že flexibilita infrastruktury priority omezení se velmi dobře osvědčila, ale není nutná často. Ve většině případů je vše na jejich výchozích prioritách. 

Prioritní úrovně neznamenají, že dané omezení _bude_ narušeno, ani že bude vždy splněné. Priority omezení definují pořadí, v jakém se vynutila omezení. Priority definují kompromisy, kdy není možné vyhovět všem omezením. Obvykle je možné vyhovět všechna omezení, pokud v prostředí nebudete dělat něco jiného. Některé příklady scénářů, které vedou k porušení omezení, jsou konfliktní omezení nebo velké počty souběžných selhání.

V pokročilých situacích můžete změnit priority omezení. Řekněme například, že jste chtěli zajistit, aby spřažení bylo vždy porušeno, pokud je to nutné pro řešení problémů s kapacitou uzlů. K tomu byste mohli nastavit prioritu omezení spřažení na "měkké" (1) a ponechat omezení kapacity nastavené na hodnotu "pevná" (0).

Výchozí hodnoty priorit pro různá omezení jsou uvedeny v následující konfiguraci:

ClusterManifest.xml

```xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="PlacementConstraintPriority" Value="0" />
            <Parameter Name="CapacityConstraintPriority" Value="0" />
            <Parameter Name="AffinityConstraintPriority" Value="0" />
            <Parameter Name="FaultDomainConstraintPriority" Value="0" />
            <Parameter Name="UpgradeDomainConstraintPriority" Value="1" />
            <Parameter Name="PreferredLocationConstraintPriority" Value="2" />
        </Section>
```

prostřednictvím ClusterConfig.jsv pro samostatná nasazení nebo Template.jsv případě hostovaných clusterů Azure:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "PlacementConstraintPriority",
          "value": "0"
      },
      {
          "name": "CapacityConstraintPriority",
          "value": "0"
      },
      {
          "name": "AffinityConstraintPriority",
          "value": "0"
      },
      {
          "name": "FaultDomainConstraintPriority",
          "value": "0"
      },
      {
          "name": "UpgradeDomainConstraintPriority",
          "value": "1"
      },
      {
          "name": "PreferredLocationConstraintPriority",
          "value": "2"
      }
    ]
  }
]
```

## <a name="fault-domain-and-upgrade-domain-constraints"></a>Omezení domény selhání a domény upgradu
Cluster Správce prostředků chce zajistit, aby se služby rozšířily mezi selhání a upgradovací domény. IT modeluje toto omezení v modulu Správce prostředků clusteru. Další informace o tom, jak se používají a jejich konkrétní chování, najdete v článku o [konfiguraci clusteru](service-fabric-cluster-resource-manager-cluster-description.md#fault-and-upgrade-domain-constraints-and-resulting-behavior).

Cluster Správce prostředků může potřebovat zabalit několik replik do upgradovací domény, aby bylo možné řešit upgrady, selhání nebo jiná porušení omezení. Dobalení do domén pro selhání nebo k upgradu obvykle probíhá pouze v případě, že dojde k několika selháním nebo jiným výpadkům systému, který brání správnému umístění. Pokud chcete zabránit balení i během těchto situací, můžete využít `RequireDomainDistribution` [zásadu umístění](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md#requiring-replica-distribution-and-disallowing-packing). Všimněte si, že to může mít vliv na dostupnost služby a spolehlivost jako vedlejší efekt, takže je pečlivě zvažte.

Pokud je prostředí správně nakonfigurované, všechna omezení jsou plně respektována i během upgradu. Klíčovým aspektem je, že Správce prostředků clusteru se na vaše omezení díváte. Když zjistí, že je toto porušení okamžitě, ohlásí ho a pokusí se problém vyřešit.

## <a name="the-preferred-location-constraint"></a>Upřednostňované omezení umístění
Omezení PreferredLocation je trochu odlišné, protože má dvě různá použití. Jedno použití tohoto omezení je během upgradu aplikace. Cluster Správce prostředků automaticky spravuje toto omezení během upgradu. Slouží k zajištění, že po dokončení upgradu se repliky vrátí do jejich počátečních umístění. Další použití omezení PreferredLocation je pro [ `PreferredPrimaryDomain` zásadu umístění](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md). Obě tyto hodnoty jsou optimalizace, takže omezení PreferredLocation je ve výchozím nastavení jediným omezením nastaveným na "optimalizace".

## <a name="upgrades"></a>Programů
Cluster Správce prostředků také pomáhá při upgradování aplikací a clusterů, během kterých má dvě úlohy:

* Zajistěte, aby nedošlo k ohrožení pravidel clusteru.
* Zkuste přispět k bezproblémovému upgradu.

### <a name="keep-enforcing-the-rules"></a>Udržování vynucování pravidel
Hlavní věcí, o které je potřeba vědět, je to, že pravidla – striktní omezení, jako jsou omezení umístění a kapacity, se při upgradech pořád vynutila. Omezení umístění zajistí, aby vaše úlohy běžely jenom tam, kde jsou povolené, i během upgradů. Když jsou služby vysoce omezené, můžou upgrady trvat delší dobu. Když je služba nebo její uzel v provozu pro aktualizaci, může to mít několik možností, jak to může projít.

### <a name="smart-replacements"></a>Inteligentní náhrady
Když se spustí upgrade, Správce prostředků pořizuje snímek aktuálního uspořádání clusteru. Vzhledem k tomu, že se každá upgradovací doména dokončí, pokusí se vrátit služby, které byly v této doméně upgradu, na původní uspořádání. Tímto způsobem existují u služby během upgradu nanejvýš dvě přechody. Dojde k jednomu přesunu z ovlivněného uzlu a jeden přesun zpátky. Aktualizace clusteru nebo služby na to, jak probíhala před upgradem, také zajistí, že upgrade nebude mít vliv na rozložení clusteru. 

### <a name="reduced-churn"></a>Omezené změny
Další věcí, ke které dojde během upgradu, je to, že cluster Správce prostředků vypne vyrovnávání. Prevence vyrovnávání brání zbytečné reakce samotného upgradu, jako je třeba přesunutí služeb do uzlů, které se pro upgrade vyprázdní. Pokud se jedná o upgrade clusteru, celý cluster se během upgradu vyrovnává. Kontroly omezení zůstávají aktivní, pouze pohyb na základě proaktivního vyrovnávání metrik je zakázán.

### <a name="buffered-capacity--upgrade"></a>& upgrade kapacity vyrovnávací paměti
Obecně chcete, aby se upgrade dokončil i v případě, že je cluster omezený nebo blízko úplný. Správa kapacity clusteru je ještě důležitější během inovací než obvykle. V závislosti na počtu domén upgradu se musí migrovat 5 až 20 procent kapacity, protože upgrade prochází clusterem. Tato práce musí jít někam. Tady je užitečné vyhodnotit [kapacitu ve vyrovnávací paměti](service-fabric-cluster-resource-manager-cluster-description.md#node-buffer-and-overbooking-capacity) . Při normálním provozu se respektuje kapacita ve vyrovnávací paměti. Cluster Správce prostředků může v případě potřeby doplňovat uzly až do celkové kapacity (což spotřebovává vyrovnávací paměť).

## <a name="next-steps"></a>Další kroky
* Začněte od začátku a [Získejte Úvod do clusteru Service Fabric správce prostředků](service-fabric-cluster-resource-manager-introduction.md)
