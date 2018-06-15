---
title: Správce prostředků clusteru Service Fabric - integraci správy | Microsoft Docs
description: Přehled integrace body mezi správce prostředků clusteru a správu prostředků infrastruktury služby.
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
ms.openlocfilehash: 3f93ca94d5aa3e95637a53a4c8fe3d9d264dd58c
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34208280"
---
# <a name="cluster-resource-manager-integration-with-service-fabric-cluster-management"></a>Integrace manager prostředek clusteru s Správa clusteru Service Fabric
Správce prostředků clusteru Service Fabric není disk upgradů Service Fabric, ale je zahrnuta. První způsob, jakým clusteru Resource Manager pomůže se správou je sledování požadovaný stav clusteru a služby je uvnitř. Správce prostředků clusteru rozešle sestav stavu, když ji nemůžete uvést do požadovanou konfiguraci clusteru. Například pokud není dostatečná kapacita správce prostředků clusteru rozešle stavu upozornění a chyby naznačující problém. Další část integrace souvisí se fungování upgradu. Správce prostředků clusteru mění své chování mírně během upgradu.  

## <a name="health-integration"></a>Integrace stavu
Správce prostředků clusteru neustále sleduje pravidla, která jste definovali k umístění vaší služby. Je také sleduje zbývající kapacity pro jednotlivé metriky na uzlech a v clusteru a v clusteru jako celek. Pokud ji nemůže splnit tato pravidla nebo pokud není dostatečná kapacita, jsou vydávány stav varování a chyby. Například pokud uzel je nad kapacitu a správce prostředků clusteru se pokusí opravit situaci přesunutím služby. Pokud nemůže ji napravit situaci vysílá stavu varování, uzel, který je přes kapacity a pro které metriky.

Dalším příkladem upozornění stavu Správce prostředků je porušení omezení umístění. Například, pokud jste definovali omezení umístění (například `“NodeColor == Blue”`) a Resource Manager zjistí porušení omezení, se vydá upozornění stavu. To platí pro vlastní omezení a výchozí omezení (např. omezení domény selhání a upgradu domény).

Tady je příklad jeden takové zprávy stavu. V takovém případě je sestava stavu pro jeden z oddílů systému služby. Zpráva stav označuje, že repliky tohoto oddílu jsou dočasně zabalit do příliš málo upgradu domén.

```posh
PS C:\Users\User > Get-WindowsFabricPartitionHealth -PartitionId '00000000-0000-0000-0000-000000000001'


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

Tady je co tato zpráva stavu nám oznamuje je:

1. Všechny repliky sami jsou v pořádku: každá z nich má AggregatedHealthState: Ok
2. Je aktuálně probíhá došlo k porušení omezení distribuční upgradu domény. To znamená, že konkrétní upgradu domény má další repliky z tohoto oddílu, než by měl.
3. Uzel obsahuje repliku, která způsobila porušení zásady. V takovém případě je uzel s názvem "Node.8"
4. Jestli upgradu se právě děje pro toto oddílu ("aktuálně upgrade--false")
5. Zásady distribuce pro tuto službu: "Distribuční zásady – okolních". Toto se řídí `RequireDomainDistribution` [umístění zásady](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md#requiring-replica-distribution-and-disallowing-packing). "Balení" znamená, že v tomto případě DomainDistribution byla _není_ potřeby, abychom věděli, že umístění zásady nebyl zadán pro tuto službu. 
6. Když se stalo sestavu - 8/10/2015 19:13:02: 00

Údaje, jako tento zajišťuje výstrahy, které aktivují v produkčním prostředí, který vám oznamuje něco nepovede a slouží ke zjišťování a zastaví chybný upgrady. V takovém případě by chceme zobrazit, pokud jsme můžete zjistit, proč museli pack repliky do domény upgradu Resource Manager. Obvykle balení je přechodný, protože uzlů v jiných doménách upgradu byly dolů, např.

Řekněme, že se pokouší o umístit některé služby Správce prostředků clusteru, ale nejsou k dispozici žádné řešení, které pracují. Při služby nelze umístit, je obvykle pro jednu z následujících důvodů:

1. Některé dočasný stav má nebylo možné správně umístit této instance služby nebo repliky
2. Požadavky na umístění služby jsou unsatisfiable.

V těchto případech sestavy o stavu ze Správce prostředků clusteru můžete zjistit, proč nelze umístit služby. Tento proces říkáme pořadí odstranění omezení. Během, provede systému nakonfigurované omezení týkající se služby a záznamy se vyhnete. Tímto způsobem, pokud nejsou služby moci umístit, uvidíte uzlů, které byly odstraněny a proč.

## <a name="constraint-types"></a>Omezení typů
Budeme mluvit o každé z různých omezení v těchto sestavách stavu. Zobrazí se stav zprávy týkající se těchto omezení, pokud nemůže být umístěn repliky.

* **ReplicaExclusionStatic** a **ReplicaExclusionDynamic**: těchto omezení označuje, že řešení byla odmítnuta, protože dva objekty služby ze stejného oddílu by musel být umístěny na stejném uzlu. Toto není povolená, protože pak selhání tohoto uzlu by zbytečně ovlivnit oddílu. ReplicaExclusionStatic a ReplicaExclusionDynamic jsou téměř stejného pravidla a rozdíly nejsou opravdu důležité. Pokud se zobrazuje sekvenci odstranění omezení obsahující buď ReplicaExclusionStatic nebo ReplicaExclusionDynamic omezení, správce prostředků clusteru se domnívá, že není dostatek uzlů. To vyžaduje zbývající řešení použít tyto neplatné umísťováním, které nejsou povoleny. Jiná omezení v pořadí bude obvykle Řekněte nám, proč se právě uzly na prvním místě vyloučí.
* **PlacementConstraint**: Pokud se zobrazí tato zpráva, znamená to, jsme vyloučit některé uzly, protože neodpovídají omezení umístění služby. Jsme trasování se omezení umístění aktuálně nakonfigurovaný jako součást této zprávy. To je normální, pokud máte definované omezení umístění. Ale pokud omezení umístění je nesprávně by příliš mnoho uzly eliminace Toto je, jak by si všimnete.
* **NodeCapacity**: Toto omezení znamená, že clusteru Resource Manager nemohl umístit repliky na uvedené uzly, protože který by put přes kapacity.
* **Spřažení**: Toto omezení označuje, že jsme nemohl umístit repliky na ovlivněných uzly vzhledem k tomu, že by to způsobilo porušení omezení spřažení. Další informace o spřažení je v [v tomto článku](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
* **FaultDomain** a **UpgradeDomain**: Toto omezení eliminuje uzly, pokud umístění repliky na uvedené uzly by způsobilo balení v konkrétní chyby nebo doméně pro upgrade. Několik příkladů hovoříte o toto omezení jsou uvedeny v tomto tématu na [selhání a upgradu omezení domény a výsledné chování](service-fabric-cluster-resource-manager-cluster-description.md)
* **PreferredLocation**: byste neměli vidět normálně toto omezení, protože je ve výchozím nastavení spustí jako optimalizace odstranění uzlů z řešení. Omezení upřednostňované umístění je také dostupný při upgradu. Během upgradu se používá pro přesun služby zpět do které byly při spuštění upgradu.

## <a name="blocklisting-nodes"></a>Blocklisting uzly
Další zprávu stavu sestavy správce prostředků clusteru je, když jsou blocklisted uzly. Blocklisting si můžete představit jako dočasné omezení, která je automaticky použita pro vás. Uzly získat blocklisted opakovaných neúspěšných dojde při spuštění instance tohoto typu služby. Uzly jsou blocklisted na základě typu za služby. Uzlem může být blocklisted pro typ jedna služba, ale ne jiné. 

Zobrazí se nové často během vývoje blocklisting: Některé chyby způsobí, že váš hostitel služby došlo k chybě při spuštění. Service Fabric se pokusí vytvořit hostitele služby několikrát, a vyskytuje opakovaně selhání. Po několika pokusech uzlu získá blocklisted a správce prostředků clusteru se pokusí vytvořit službu jinde. Pokud ve více uzlech udržuje nedošlo k této chybě, je možné všechny platné uzly v clusteru end až zablokuje. Blocklisting rovněž můžete odebrat mnoho uzly, že není dostatek úspěšně spustit službu ke splnění požadovaných měřítka. Obvykle uvidíte další chyby nebo upozornění od uvádí správce prostředků clusteru, která služba je nižší než požadovaný repliky nebo počet instancí, jak jako stavu zprávy, která určuje, co je selhání, které vznikají blocklisting v prvním místní.

Blocklisting není trvalé podmínce. Po několika minutách bude uzel odebrán z blocklist a Service Fabric může znovu aktivujte služeb v tomto uzlu. Pokud služby dále nezdaří, je uzel blocklisted pro daný typ služby znovu. 

### <a name="constraint-priorities"></a>Omezení priority

> [!WARNING]
> Změna omezení priority se nedoporučuje a může mít významný negativní vliv na clusteru. Níže uvedené informace slouží pro odkaz na výchozí omezení priority a jejich chování. 
>

Se všemi těchto omezení vám může mít byla myslím "Hey – myslím, že jsou omezení domény selhání je třeba mít v systému. Aby se zajistilo, že není došlo k porušení omezení domény selhání, chci se porušování jiná omezení."

Omezení lze konfigurovat různých úrovní priority. Jsou to:

   - "pevné" (0)
   - "soft" (1)
   - "optimalizace" (2)
   - "off" (-1). 
   
Většina omezení jsou nakonfigurovány jako pevný omezení ve výchozím nastavení.

Změna prioritu omezení neobvyklé. Byly časy kde omezení priority třeba chcete změnit, obvykle obejít některé chyb nebo chování, které vliv prostředí. Obecně velmi dobře fungovala pružnost infrastruktury s prioritou omezení, ale nebyla často potřeba. Ve většině případů, vše, co je umístěn na jejich výchozí priority. 

Úrovně priority nemáte znamenají, že dané omezení _bude_ být došlo k porušení, ani se vždy splnit. Omezení priority definovat pořadí, ve kterém jsou vynucena omezení. Priority definovat nevýhody v případě, že je nelze vyhovět všechna omezení. Všechna omezení obvykle může obsloužit, pokud má něco jinak se děje v daném prostředí. Příklady scénářů, které povede k porušení omezení jsou konfliktní omezení, nebo velkého počtu souběžných selhání.

V situacích, rozšířené můžete změnit prioritu omezení. Řekněme například, že chcete zajistit spřažení by porušila vždy v případě potřeby řešit problémy kapacita uzlu. Jak toho docílit, můžete nastavit prioritu spřažení omezení a "soft" (1) a nechte omezení kapacity nastavena na "pevný" (0).

Výchozí hodnoty priority pro různé omezení jsou zadány v následující konfigurace:

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

pomocí souboru ClusterConfig.json pro samostatné nasazení nebo Template.json pro Azure hostované clustery:

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
Správce prostředků clusteru chce ponechat služby šíření mezi doménách selhání a upgradu. Modely to jako omezení uvnitř modul Správce prostředků clusteru. Další informace o tom, jak se používají a jejich konkrétní chování, najdete v článku na [konfigurace clusteru](service-fabric-cluster-resource-manager-cluster-description.md#fault-and-upgrade-domain-constraints-and-resulting-behavior).

Správce prostředků clusteru možná muset pack několik repliky do upgradu domény, aby bylo možné čelit upgrady, chyby nebo další narušení omezení. Balení do domén selhání nebo upgradu obvykle dochází jenom v případě, že existuje několik selhání nebo dalších změn v systému, které brání správné umístění. Pokud chcete, aby se zabránilo okolních i v těchto situacích, můžete využít `RequireDomainDistribution` [umístění zásady](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md#requiring-replica-distribution-and-disallowing-packing). Všimněte si, že to může mít vliv na dostupnost služeb a spolehlivost jako vedlejším účinkem proto pečlivě zvažte.

Pokud prostředí je správně nakonfigurována, všechna omezení jsou dodržení i během upgradu. Klíče věc je, že správce prostředků clusteru sleduje pro vaše omezení. Když zjistí narušení okamžitě sestavy ji a pokusí se tento problém opravili.

## <a name="the-preferred-location-constraint"></a>Omezení upřednostňované umístění
Omezení PreferredLocation je trochu liší, protože má dva různé používá. Jedno použití tohoto omezení je během upgradu aplikace. Správce prostředků clusteru automaticky spravuje toto omezení při upgradu. Použije se zajistit, aby po dokončení, že repliky vrátit k jejich počáteční umístění upgrady. Použití omezení PreferredLocation je pro [ `PreferredPrimaryDomain` umístění zásady](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md). Obě tyto optimalizace a proto je omezení PreferredLocation pouze omezení ve výchozím nastavení má "Optimalizace".

## <a name="upgrades"></a>Upgrady
Správce prostředků clusteru také pomáhá při aplikace a upgrady clusteru, za které má dvě úlohy:

* Ujistěte se, že nejsou ohrožená pravidla clusteru
* Pokuste se upgrade bezproblémové pomoci

### <a name="keep-enforcing-the-rules"></a>Zachovat vynucování pravidel
Hlavní věc zajímat je, že se pravidla – striktní omezení jako omezení umístění a kapacity - stále vynucují během upgradu. Omezení umístění zkontrolujte, zda úlohy pouze spustili, kde mohou, i během upgradu. Když jsou vysoce omezené služby, upgrade může trvat déle. Pokud služba nebo na uzel, který je spuštěn na se snížila pro aktualizaci může být několik možností pro kde můžete přejít.

### <a name="smart-replacements"></a>Inteligentní nahrazení
Když se spustí se upgrade, správce prostředků pořídí snímek aktuální uspořádání clusteru. Protože každá doména upgradu se dokončí, pokusí se vrátí služby, které byly v této doméně upgradu do jejich původního uspořádání. Tímto způsobem existují maximálně dvě přechody pro službu během upgradu. Neexistuje jeden Přesun na příslušném uzlu a jeden přesuňte zpět. Vrátí do původního stavu před upgradem clusteru nebo služby zajišťuje, že upgrade nebude mít vliv na rozložení clusteru. 

### <a name="reduced-churn"></a>Snížené změn
Jiné z věcí, které se stane při upgradech je, že správce prostředků clusteru vypne vyrovnávání. Brání vyrovnávání zabraňuje zbytečným reakce upgrade samostatně, například přesun služby do uzlů, které byly vyprázdněny pro upgrade. Pokud dotyčném upgrade je upgrade clusteru, není během upgradu vyvážit celý cluster. Omezení kontroly zůstane aktivní, pouze přesun podle proaktivní vyrovnávání metriky je zakázána.

### <a name="buffered-capacity--upgrade"></a>Ve vyrovnávací paměti kapacity & upgradu
Obecně je vhodné na dokončení i v případě, že je omezené clusteru nebo blízko úplné upgradu. Správa kapacitu clusteru je důležitější-při upgradech než obvykle. V závislosti na počet domén upgradu mezi 5 a 20 procent kapacity je potřeba migrovat po upgradu prostřednictvím clusteru. Které pracují se má přejít někde. To je, kdy představu o [uložená do vyrovnávací paměti kapacity](service-fabric-cluster-resource-manager-cluster-description.md#buffered-capacity) je užitečné. Kapacita ve vyrovnávací paměti je dodržena při běžném provozu. Správce prostředků clusteru můžou vyplní uzly až jejich celkové kapacity (využívání vyrovnávací paměti) během upgradu v případě potřeby.

## <a name="next-steps"></a>Další postup
* Začít od začátku a [získejte Úvod do Service Fabric clusteru správce prostředků](service-fabric-cluster-resource-manager-introduction.md)
