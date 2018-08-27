---
title: Service Fabric Cluster Resource Manager – integrace správy | Dokumentace Microsoftu
description: Přehled bodů integrace mezi Cluster Resource Manager a Správa prostředků infrastruktury.
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: 956cd0b8-b6e3-4436-a224-8766320e8cd7
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 7a1bab75521730f7e80e5b86112bbb0aed129f88
ms.sourcegitcommit: ebb460ed4f1331feb56052ea84509c2d5e9bd65c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/24/2018
ms.locfileid: "42917870"
---
# <a name="cluster-resource-manager-integration-with-service-fabric-cluster-management"></a>Cluster resource manager integrace s správy clusteru Service Fabric
Service Fabric Cluster Resource Manageru není jednotka upgradu v Service Fabric, ale je zahrnuta. První způsob, který Cluster Resource Manager pomáhá se správou se sledováním požadovaný stav clusteru a služby dovnitř. Cluster Resource Manager odešle sestav o stavu pokud ho nelze vložit do požadovanou konfiguraci clusteru. Například pokud není k dispozici dostatečná kapacita Cluster Resource Manager odešle upozornění stavu a chyb, které označují problém. Další část integrace souvisí se fungování upgradu. Cluster Resource Manager mírně změní jeho chování během upgradu.  

## <a name="health-integration"></a>Integrace stavu
Cluster Resource Manager neustále sleduje pravidla, která jste definovali pro uvedení vašich služeb. Také sleduje zbývající kapacity pro jednotlivé metriky na uzlech v clusteru a v clusteru jako celek. Pokud ho nelze splnit pravidla nebo pokud není dostatečná kapacita, jsou emitovány stav varování a chyby. Například, pokud uzel je překročena kapacita a Cluster Resource Manager se pokusí opravit situace přesunutím služby. Pokud se nedá napravit situaci vydává stav varování, který uzel je překročena kapacita a pro jaké metriky.

Další příklad upozornění stavu Resource Manageru je porušení omezení umístění. Například, pokud jste definovali omezení umístění (například `“NodeColor == Blue”`) a Resource Manager rozpozná porušení tohoto omezení, vydá upozornění stavu. To platí pro vlastní omezení a výchozí omezení (např. doména selhání a upgradu domény omezení).

Tady je příklad jednoho tyto sestavy stavu. Sestava stavu v tomto případě je pro jeden z oddílů systémové služby. Zpráva stavu znamená, že repliky oddílu jsou dočasně zkomprimována do moc malý počet domén upgradu.

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

Zde je, co tato zpráva stavu nám oznamuje je:

1. Všechny repliky samotných jsou v pořádku: každá má AggregatedHealthState: Ok
2. Omezení distribuce upgradu domény je aktuálně porušen. To znamená, že má víc replik oddílu, než by měl v určité doméně upgradu.
3. Který uzel obsahuje repliku způsobuje narušení. V tomto případě je uzel s názvem "Node.8"
4. Určuje, zda upgrade se právě děje pro tento oddíl ("aktuálně upgrade--false")
5. Zásady distribuce pro tuto službu: "Distribuci zásad – balení". To se vztahují `RequireDomainDistribution` [zásady umístění](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md#requiring-replica-distribution-and-disallowing-packing). "Komprimace" znamená, že v tomto případě DomainDistribution byla _není_ vyžaduje, abychom měli jistotu, že zásady umístění nebyl zadaný pro tuto službu. 
6. Když se stalo sestavy – 8/10/2015 19:13:02: 00

Informace, jako je tato mocniny výstrahy, které se aktivují v produkčním prostředí s oznámením, něco se pokazilo a slouží také ke zjišťování a zastavit chybný upgrady. V tomto případě jsme byste chtěli zobrazit, pokud jsme můžete zjistit, proč museli pack repliky do domény upgradu Resource Manageru. Obvykle balení je přechodná, protože uzly v upgradu domény nebyly, třeba.

Řekněme, že Cluster Resource Manager se pokouší vložit některé služby, ale nejsou k dispozici žádné řešení, která fungují. Pokud služby nelze umístit, je to obvykle pro jednu z následujících důvodů:

1. Některé přechodné podmínky znemožnil správně umístit tato instance služby nebo repliky
2. Požadavky na umístění služby jsou unsatisfiable.

V těchto případech sestav o stavu z Cluster Resource Manager vám pomohou určit Proč nelze umístit služby. Tomuto procesu říkáme pořadí odstranění omezení. Během, systém vás provede nakonfigurované omezení týkající se služby a záznamy se vyhnete. Tímto způsobem při služby nemohou být umístěny, zobrazí se uzly, na kterých byly odstraněny a proč.

## <a name="constraint-types"></a>Typy omezení
Povězme si o všech jiná omezení v těchto sestav o stavu. Zobrazí se stav zprávy související s těmito omezeními během repliky nemůže být umístěn.

* **ReplicaExclusionStatic** a **ReplicaExclusionDynamic**: těmto omezením znamená, že řešení byl odmítnut, protože dva objekty služby ze stejného oddílu by mají být umístěny na stejném uzlu. To není povoleno, protože pak selhání uzlu příliš postihlo daného oddílu. ReplicaExclusionStatic a ReplicaExclusionDynamic jsou téměř stejné pravidlo a nejsou ve skutečnosti důležité rozdíly. Pokud dochází k sekvenci odstranění omezení obsahující ReplicaExclusionStatic nebo ReplicaExclusionDynamic omezení, Cluster Resource Manager domnívá, že nejsou k dispozici dostatek uzlů. To vyžaduje zbývající řešení použít tyto neplatné umístění, které nejsou povoleny. Další omezení v sekvenci se obvykle Řekněte nám, proč uzly se zanikne brány na prvním místě.
* **PlacementConstraint**: Pokud se zobrazí tato zpráva, znamená to, že jsme odstranili některé uzly vzhledem k tomu, že neodpovídají omezení umístění služby. Jsme trasování si omezení umístění aktuálně nakonfigurovaný jako součást této zprávy. To je normální, pokud máte definované omezení umístění. Nicméně pokud omezení umístění je moc velký počet uzlů se nesprávně příčinou to je, jak by si.
* **NodeCapacity**: Toto omezení znamená, že Cluster Resource Manager nemohl umístit repliky na uvedené uzly, protože, které vložili překročena kapacita.
* **Spřažení**: Toto omezení znamená, že jsme nemohl umístit repliky na ovlivněné uzly protože by způsobil porušení omezení spřažení. Další informace o spřažení je v [v tomto článku](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
* **FaultDomain** a **UpgradeDomain**: Toto omezení eliminuje uzly, pokud umístění repliky na uvedené uzly by způsobilo balení v konkrétní chyba nebo upgradovací doméně. Několik příkladů diskuze o tato omezení jsou uvedeny v tématu na [chybových nebo upgradovacích omezení domény a výsledné chování](service-fabric-cluster-resource-manager-cluster-description.md)
* **PreferredLocation**: byste neměli vidět normálně toto omezení odebrání uzlů z řešení, protože poběží brána jako optimalizace ve výchozím nastavení. Omezení upřednostňované umístění je také k dispozici během upgradu. Během upgradu se používá k přesunutí služeb zpět do kdy byly při spuštění upgradu.

## <a name="blocklisting-nodes"></a>Blocklisting uzly
Další zpráva stavu sestavy Cluster Resource Manageru je při blocklisted jsou uzly. Blocklisting si lze představit jako dočasné omezení, které se automaticky použije za vás. Uzly získáte blocklisted opakovaných neúspěšných dojde při spuštění instance daného typu služby. Uzly jsou blocklisted na základě typu na službu. Uzlem může být blocklisted jedna služba typu, ale jiné ne. 

Zobrazí se vám blocklisting rozjíždí často během vývoje: Některé chyby způsobí, že hostitel vaší služby k chybě při spuštění. Service Fabric se pokusí vytvořit hostitele služby několikrát a vyskytuje opakovaně selhání. Po několika pokusech se uzel získá blocklisted a Cluster Resource Manager se pokusí vytvořit službu jinde. Pokud tohoto selhání udržuje aktivit ve více uzlů, je možné, že všechny platné uzly v clusteru skončí blokované. Blocklisting můžete také odebrat tolik, že není dostatek můžete úspěšně spustit službu pro splnění požadovaného škálování uzlů. Obvykle uvidíte další chyby nebo upozornění z označující Cluster Resource Manageru, které služba je nižší než požadované repliky nebo počet instancí, stejně jako zprávy stavu označující, co je selhání, které vznikají blocklisting v prvním umístíte.

Blocklisting není trvalé podmínce. Po několika minutách je uzel odebrán z seznamu blokovaných a Service Fabric může znovu aktivovat služby v tomto uzlu. Pokud služby nezdaří, uzel opět blocklisted pro daný typ služby. 

### <a name="constraint-priorities"></a>Priority omezení

> [!WARNING]
> Změna priority omezení se nedoporučuje a může mít významný nepříznivý vliv na váš cluster. Níže uvedené informace slouží pro odkaz na výchozí priority omezení a jejich chování. 
>

Se všemi těchto omezení vám může mít byla uvažujete "Dobrý den – myslím, že omezení domény selhání jsou nejdůležitější v systému. Aby se zajistilo, že není došlo k porušení omezení domény selhání, chci se jiná omezení porušují."

Omezení je nakonfigurovat pomocí různých úrovní priority. Jsou to:

   - "pevné" (0)
   - "soft" (1)
   - "optimalizace" (2)
   - "off" (-1). 
   
Většinu omezení jsou ve výchozím nastavení nakonfigurované jako pevný omezení.

Změna priority omezení neobvyklé. Byly doby kde priority omezení potřeba změnit, obvykle Chcete-li nějaké chyby nebo chování, které to mělo vliv na prostředí. Obecně flexibilitu infrastruktury priority omezení pracoval velmi dobře, ale není potřeba často. Ve většině případů je všechno, co umístěno na jejich výchozí priority. 

Úrovně priority neznamenají, který dané omezení _bude_ porušení, ani se vždy splněna. Priority omezení definovat pořadí, ve kterém jsou vynucena omezení. Priority definujte nevýhody po není možné vyhovět všem omezením. Všechna omezení obvykle je možné splnit, pokud je něco jinak se děje v prostředí. Příklady scénářů, které může vést k narušení omezení jsou konfliktní omezení, nebo velkého počtu souběžných selhání.

V situacích, rozšířené můžete změnit prioritu omezení. Řekněme například, že jste chtěli Ujistěte se, že spřažení by vždy být došlo k porušení když je potřeba řešit potíže s kapacitou uzlu. Za tím účelem můžete nastavit prioritu omezení spřažení "soft" (1) a nechte omezení kapacity nastavte na "pevné" (0).

Výchozí hodnoty priority pro jiná omezení jsou zadány v následující konfiguraci:

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

prostřednictvím ClusterConfig.json pro samostatné nasazení nebo Template.json pro Azure hostované clustery:

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

## <a name="fault-domain-and-upgrade-domain-constraints"></a>Omezení domény selhání domény a upgrade
Cluster Resource Manageru se chce ponechat služby rozložené mezi chybových nebo upgradovacích doménách. Modely to jako omezení v Cluster Resource Manageru stroji. Další informace o tom, jak se používají a jejich konkrétní chování, najdete v článku na [konfigurace clusteru](service-fabric-cluster-resource-manager-cluster-description.md#fault-and-upgrade-domain-constraints-and-resulting-behavior).

Cluster Resource Manager, může být potřeba pack několik replik do logických sítí, aby bylo možné řešit upgrady, chyby nebo jiné porušení omezení. Balení do domén selhání nebo upgradu obvykle dochází pouze v případě, že existuje několik chyb nebo jiných změn v systému brání správné umístění. Pokud chcete zabránit balení i během těchto situacích, můžete využít `RequireDomainDistribution` [zásady umístění](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md#requiring-replica-distribution-and-disallowing-packing). Všimněte si, že to může mít vliv na dostupnost služeb a spolehlivost jako vedlejší účinek, proto pečlivě zvažte.

Pokud je prostředí správně nakonfigurováno, všechna omezení jsou úplně dodrženy, dokonce i během upgradu. Nejdůležitější je, že Cluster Resource Manager je sledují se omezení. Jakmile rozpozná porušení okamžitě ohlásí ji a pokusí se problém opravte.

## <a name="the-preferred-location-constraint"></a>Omezení upřednostňované umístění
PreferredLocation omezení se mírně liší, protože má dvě různá použití. Jedno použití tohoto omezení je během upgradu aplikace. Cluster Resource Manager automaticky spravuje toto omezení během upgradu. Používá se k Ujistěte se, že až se dokončí, že repliky se vrátit k jejich počáteční umístění upgrady. Použití omezení PreferredLocation je [ `PreferredPrimaryDomain` zásady umístění](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md). Obě tyto optimalizace jsou, a proto PreferredLocation omezení je jenom jedno omezení, které jsou ve výchozím nastavení "Optimalizace".

## <a name="upgrades"></a>Upgrady
Cluster Resource Manager pomáhá také během aplikací a upgrady clusteru, během kterých má dvě úlohy:

* Ujistěte se, že nebudou ohrožena pravidla clusteru
* Při pokusu o upgrade napomohou nápovědy

### <a name="keep-enforcing-the-rules"></a>Zachovat vynucení pravidel
Hlavní věc, kterou je potřeba vědět je, že pravidla – striktní omezení jako omezení umístění a kapacity – se stále uplatňují během upgradu. Omezení umístění Ujistěte se, že vaše úlohy pouze spustili, kde můžou, dokonce i během upgradu. Když je vysoce vymezené služby, upgrade může trvat déle. Když služba nebo na uzel, který je spuštěn na se načtou aktualizace může být několik možností, kde můžete přejít.

### <a name="smart-replacements"></a>Inteligentní nahrazení
Když se upgrade spustí, správce prostředků pořídí snímek aktuální uspořádání clusteru. Každá doména upgradu dokončení, pokusí se vrátit služby, které byly v této doméně upgradu na jejich původní uspořádání. Tímto způsobem existují maximálně dvě přechody pro službu během upgradu. Existuje jeden přesunout mimo uzel došlo k chybě a jeden přechod. Vrátí do původního stavu před upgradem clusteru nebo služby také zajišťuje, že upgrade nebude mít vliv na rozložení clusteru. 

### <a name="reduced-churn"></a>Snížení četnosti změn
Další věc, ke které dochází během upgradu je, že Cluster Resource Manager vypne vyrovnávání. Brání vyrovnávání zabrání zbytečné reakcí s palcem k upgradu, samostatně, jako přesunutí služeb do uzlů, které byly vyprázdněna pro upgrade. Je-li upgrade dotyčný upgradu clusteru, není během upgradu balanced celý cluster. Kontroly omezení zůstávají aktivní, pouze pohyb podle proaktivní vyrovnávání metriky je zakázán.

### <a name="buffered-capacity--upgrade"></a>Kapacita ve vyrovnávací paměti a Upgrade
Obecně byste měli dokončení i v případě, že je omezen cluster nebo blízko úplné. Správa kapacity clusteru je ještě důležitější během upgradu než obvykle. V závislosti na počet upgradovacích domén mezi 5 a 20 procent kapacity se musí migrovat upgradu prochází clusteru. Tato práce musí projít někde. Tady pojem [ukládány do vyrovnávací paměti kapacity](service-fabric-cluster-resource-manager-cluster-description.md#buffered-capacity) je užitečné. Kapacita ve vyrovnávací paměti je dodržena během normálního provozu. Cluster Resource Manager může vyplnit uzly až po jejich celkové kapacity (využívání vyrovnávací paměti) během upgradu v případě potřeby.

## <a name="next-steps"></a>Další postup
* Začít od začátku a [Úvod do Service Fabric Cluster Resource Manager](service-fabric-cluster-resource-manager-introduction.md)
