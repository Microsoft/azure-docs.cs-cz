---
title: Správce prostředků clusteru – integrace správy
description: Přehled integračních bodů mezi Správcem prostředků clusteru a service fabric managementem.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 50751c7d23797a597dc5e2d209c1e3eecf6f7a40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258743"
---
# <a name="cluster-resource-manager-integration-with-service-fabric-cluster-management"></a>Integrace správce prostředků clusteru se správou clusteru Service Fabric
Správce prostředků clusteru Service Fabric nepodporuje upgrady v Service Fabric, ale je zapojen. Prvním způsobem, který Správce prostředků clusteru pomáhá se správou, je sledování požadovaného stavu clusteru a služeb uvnitř clusteru. Správce prostředků clusteru odesílá sestavy stavu, když nemůže umístit cluster do požadované konfigurace. Pokud například není dostatečná kapacita, správce prostředků clusteru odešle upozornění na stav a chyby označující problém. Další část integrace má co do činění s tím, jak upgrady fungují. Správce prostředků clusteru mírně změní své chování během inovací.  

## <a name="health-integration"></a>Integrace zdraví
Správce prostředků clusteru neustále sleduje pravidla, která jste definovali pro umístění služeb. Také sleduje zbývající kapacitu pro každou metriku na uzlech a v clusteru a v clusteru jako celku. Pokud nemůže splňovat tato pravidla nebo pokud je nedostatečná kapacita, jsou vydávána zdravotní upozornění a chyby. Například pokud uzel je přes kapacitu a Správce prostředků clusteru se pokusí opravit situaci přesunutím služeb. Pokud nelze opravit situaci vydává upozornění stavu označující, který uzel je nad kapacitu a pro které metriky.

Dalším příkladem upozornění správce prostředků je porušení omezení umístění. Pokud jste například definovali omezení umístění `“NodeColor == Blue”`(například) a Správce prostředků zjistí porušení tohoto omezení, vydává upozornění na stav. To platí pro vlastní omezení a výchozí omezení (jako omezení domény selhání a upgradu domény).

Zde je příklad jedné takové zdravotní zprávy. V tomto případě je sestava stavu pro jeden z oddílů systémové služby. Zpráva o stavu označuje, že repliky tohoto oddílu jsou dočasně zabaleny do příliš málo domén upgradu.

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

Zde je to, co to to zdravotní zpráva nám říká, je:

1. Všechny repliky samy o sobě jsou v pořádku: Každý má AggregatedHealthState: Ok
2. Omezení distribuce domény upgradu je aktuálně porušeno. To znamená, že konkrétní upgrade domény má více replik z tohoto oddílu, než by měl.
3. Uzel, který obsahuje repliku způsobující porušení. V tomto případě je to uzel s názvem "Uzel.8"
4. Zda pro tento oddíl aktuálně probíhá upgrade ("V současné době inovace -- false")
5. Zásady distribuce pro tuto službu: "Zásady distribuce -- Balení". To se řídí `RequireDomainDistribution` zásadami [umístění](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md#requiring-replica-distribution-and-disallowing-packing). "Balení" označuje, že v tomto případě DomainDistribution _nebylo_ požadováno, takže víme, že zásady umístění nebyla zadána pro tuto službu. 
6. Kdy se zpráva stala - 8/10/2015 19:13:02

Informace, jako je tento pravomoci upozornění, že oheň ve výrobě, abyste věděli, že se něco pokazilo a je také používán k detekci a zastavení špatné upgrady. V takovém případě bychom chtěli zjistit, zda můžeme zjistit, proč Správce prostředků musel zabalit repliky do domény upgradu. Obvykle balení je přechodné, protože uzly v jiných doménách upgradu byly například mimo.

Řekněme, že Správce prostředků clusteru se pokouší umístit některé služby, ale neexistují žádná řešení, která fungují. Pokud služby nelze umístit, je to obvykle z jednoho z následujících důvodů:

1. Některé přechodné podmínky znemožnily správně umístit tuto instanci služby nebo repliku.
2. Požadavky na umístění služby jsou neupřesnitelné.

V těchto případech vám sestavy stavu ze Správce prostředků clusteru pomohou určit, proč službu nelze umístit. Nazýváme tento proces sekvenci eliminace omezení. Během něj systém prochází nakonfigurovaná omezení ovlivňující službu a zaznamenává, co eliminují. Tímto způsobem, když služby nelze umístit, můžete vidět, které uzly byly odstraněny a proč.

## <a name="constraint-types"></a>Typy omezení
Promluvme si o jednotlivých omezeních v těchto zdravotních zprávách. Zobrazí se zprávy o stavu související s těmito omezeními, když repliky nelze umístit.

* **ReplicaExclusionStatic** a **ReplicaExclusionDynamic**: Tato omezení označují, že řešení bylo odmítnuto, protože dva objekty služby ze stejného oddílu by musely být umístěny na stejném uzlu. To není povoleno, protože pak selhání tohoto uzlu by příliš ovlivnit tento oddíl. ReplicaExclusionStatic a ReplicaExclusionDynamic jsou téměř stejné pravidlo a rozdíly nezáleží. Pokud se zobrazuje sekvence eliminace omezení obsahující omezení ReplicaExclusionStatic nebo ReplicaExclusionDynamic, správce prostředků clusteru si myslí, že není dostatek uzlů. To vyžaduje zbývající řešení pro použití těchto neplatných umístění, která jsou zakázána. Ostatní omezení v pořadí obvykle nám říkají, proč jsou uzly eliminovány na prvním místě.
* **PlacementConstraint:** Pokud se zobrazí tato zpráva, znamená to, že jsme odstranili některé uzly, protože neodpovídaly omezení umístění služby. Jako součást této zprávy vysledujeme aktuálně nakonfigurovaná omezení umístění. To je normální, pokud máte definované omezení umístění. Pokud však omezení umístění nesprávně způsobuje, že je třeba odstranit příliš mnoho uzlů, tak to zjistíte.
* **NodeCapacity**: Toto omezení znamená, že Správce prostředků clusteru nemohl umístit repliky na uvedené uzly, protože by je převyšovalo kapacitou.
* **Spřažení**: Toto omezení označuje, že jsme nemohli umístit repliku na ovlivněné uzly, protože by to způsobilo porušení omezení spřažení. Další informace o afinitě jsou v [tomto článku](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
* **FaultDomain** a **UpgradeDomain**: Toto omezení eliminuje uzly, pokud umístění repliky na uvedené uzly by způsobilo balení v určité doméně selhání nebo upgradu. V tématu omezení domény o selhání [a upgradu a výsledném chování](service-fabric-cluster-resource-manager-cluster-description.md) je uvedeno několik příkladů, které popisují toto omezení.
* **PreferredLocation:** Obvykle by se nemělo zobrazit toto omezení odebrání uzlů z řešení, protože je ve výchozím nastavení spuštěno jako optimalizace. Omezení umístění upřednostňované je také k dispozici během inovací. Během upgradu se používá k přesunutí služeb zpět na místo, kde byly při spuštění upgradu.

## <a name="blocklisting-nodes"></a>Uzly blocklistingu
Další zpráva o stavu, kterou hlásí Správce prostředků clusteru, je, když jsou uzly zaškrtávaly. Blocklisting si můžete myslet jako dočasné omezení, které se automaticky použije pro vás. Uzly získat blocklisted při výskytu opakovaných chyb při spouštění instancí tohoto typu služby. Uzly jsou na základě typu služby zařazovány do bloku. Uzel může být blokován pro jeden typ služby, ale ne jiný. 

Uvidíte blocklisting kick v často během vývoje: některé chyby způsobí, že hostitel vaší služby k selhání při spuštění. Service Fabric se pokusí vytvořit hostitele služby několikrát a selhání stále dochází. Po několika pokusech uzel získá blocklisted a Správce prostředků clusteru se pokusí vytvořit službu jinde. Pokud k této chybě dochází stále ve více uzlech, je možné, že všechny platné uzly v clusteru skončí blokovány. Blocklisting můžete také odebrat tolik uzlů, že nestačí může úspěšně spustit službu ke splnění požadovaného měřítku. Obvykle se zobrazí další chyby nebo upozornění ze Správce prostředků clusteru označující, že služba je pod požadovanou replikou nebo počtem instancí, a také zprávy o stavu označující, co je selhání, které vede k blocklistingu v prvním Místo.

Blocklisting není trvalá podmínka. Po několika minutách uzel je odebrán z seznamu blokování a Service Fabric může aktivovat služby na tomto uzlu znovu. Pokud služby nadále selhat, uzel je blocklisted pro tento typ služby znovu. 

### <a name="constraint-priorities"></a>Priority omezení

> [!WARNING]
> Změna priorit omezení se nedoporučuje a může mít významné nepříznivé účinky na clusteru. Níže uvedené informace jsou uvedeny pro odkaz na výchozí priority omezení a jejich chování. 
>

Se všemi těmito omezeními, můžete si myslel: "Hej - myslím, že omezení domény poruchy jsou nejdůležitější věc v mém systému. Aby nebylo zajištěno, že omezení domény selhání není porušeno, jsem ochoten porušit další omezení."

Omezení lze konfigurovat s různými úrovněmi priority. Jsou to:

   - "tvrdý" (0)
   - "měkké" (1)
   - "optimalizace" (2)
   - "vypnuto" (-1). 
   
Většina omezení je ve výchozím nastavení nakonfigurována jako tvrdá omezení.

Změna priority omezení je neobvyklá. Tam byly časy, kdy omezení priority potřebné ke změně, obvykle obejít některé jiné chyby nebo chování, které mělo vliv na životní prostředí. Obecně flexibilita prioritní infrastruktury omezení funguje velmi dobře, ale není to často potřeba. Většinou vše sedí na své výchozí priority. 

Úrovně priority neznamenají, že dané omezení _bude_ porušeno, ani že bude vždy splněno. Priority omezení definují pořadí, ve kterém jsou vynucována omezení. Priority definují kompromisy, když není možné splnit všechna omezení. Obvykle všechna omezení mohou být splněny, pokud se v prostředí děje něco jiného. Některé příklady scénářů, které povedou k porušení omezení jsou konfliktní omezení nebo velký počet souběžných selhání.

V pokročilých situacích můžete změnit priority omezení. Řekněme například, že jste chtěli zajistit, že spřažení bude vždy porušena v případě potřeby k vyřešení problémů s kapacitou uzlu. Chcete-li toho dosáhnout, můžete nastavit prioritu omezení spřažení na "soft" (1) a ponechat omezení kapacity nastaveno na "hard" (0).

Výchozí hodnoty priority pro různá omezení jsou určeny v následujícím konfiguračním příkazu:

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

prostřednictvím souboru ClusterConfig.json pro samostatná nasazení nebo Template.json pro hostované clustery Azure:

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

## <a name="fault-domain-and-upgrade-domain-constraints"></a>Omezení domény selhání a upgradu
Správce prostředků clusteru chce zachovat služby rozprostřené mezi doménami selhání a upgradu. Modeluje to jako omezení uvnitř modulu Správce prostředků clusteru. Další informace o tom, jak jsou používány a jejich konkrétní chování, podívejte se na článek o [konfiguraci clusteru](service-fabric-cluster-resource-manager-cluster-description.md#fault-and-upgrade-domain-constraints-and-resulting-behavior).

Správce prostředků clusteru může potřebovat zabalit několik replik do domény upgradu, aby bylo možné řešit upgrady, selhání nebo jiné porušení omezení. Balení do domény selhání nebo upgrade obvykle dochází pouze v případě, že existuje několik selhání nebo jiné změny v systému brání správné umístění. Pokud chcete zabránit balení i v těchto situacích, můžete využít `RequireDomainDistribution` podmínky pro [umístění](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md#requiring-replica-distribution-and-disallowing-packing). Všimněte si, že to může ovlivnit dostupnost a spolehlivost služby jako vedlejší účinek, proto to pečlivě zvažte.

Pokud je prostředí nakonfigurováno správně, všechna omezení jsou plně respektována, a to i během upgradů. Klíčové je, že Správce prostředků clusteru je pozor na vaše omezení. Když zjistí porušení, okamžitě jej nahlásí a pokusí se problém opravit.

## <a name="the-preferred-location-constraint"></a>Omezení umístění upřednostňované
PreferredLocation omezení je trochu jiný, protože má dvě různá použití. Jedno použití tohoto omezení je během upgradů aplikace. Správce prostředků clusteru automaticky spravuje toto omezení během inovací. Používá se k zajištění, že po dokončení upgradů se repliky vrátí do počátečních umístění. Další použití omezení PreferredLocation je pro [ `PreferredPrimaryDomain` zásady umístění](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md). Oba tyto jsou optimalizace a proto PreferredLocation omezení je pouze omezení nastavena na "Optimalizace" ve výchozím nastavení.

## <a name="upgrades"></a>Upgrady
Správce prostředků clusteru také pomáhá při upgradu aplikací a clusteru, během kterého má dvě úlohy:

* zajistit, aby pravidla clusteru nebyla ohrožena
* se snaží pomoci upgrade hladce

### <a name="keep-enforcing-the-rules"></a>Pokračujte v prosazování pravidel
Hlavní věc, kterou je třeba si uvědomit, je, že pravidla - přísná omezení, jako jsou omezení umístění a kapacity - jsou stále vynucovány během upgradů. Omezení umístění zajišťují, že vaše úlohy běží pouze tam, kde jsou povoleny, a to i během upgradů. Pokud jsou služby vysoce omezené, upgrady mohou trvat déle. Když je služba nebo uzel, na kterém běží, spuštěna pro aktualizaci, může existovat několik možností, kam může jít.

### <a name="smart-replacements"></a>Inteligentní náhrady
Při spuštění upgradu správce prostředků pořídí snímek aktuální uspořádání clusteru. Po dokončení každé domény upgradu se pokusí vrátit služby, které byly v této doméně upgradu, do původního uspořádání. Tímto způsobem existují maximálně dva přechody pro službu během upgradu. Jeden pohyb z postiženého uzlu je a jeden se přesune zpět. Vrácení clusteru nebo služby, jak to bylo před upgradem také zajišťuje upgrade nemá vliv na rozložení clusteru. 

### <a name="reduced-churn"></a>Snížené konve
Další věc, která se stane během upgradů je, že Správce prostředků clusteru vypne vyrovnávání. Zabránění vyvažování zabraňuje zbytečné reakce na upgrade sám, jako je přesunutí služeb do uzlů, které byly vyprázdněny pro upgrade. Pokud je dotyčný upgrade upgrade clusteru, pak celý cluster není vyvážena během upgradu. Kontroly omezení zůstávají aktivní, je zakázán pouze pohyb na základě proaktivního vyvažování metrik.

### <a name="buffered-capacity--upgrade"></a>Upgrade kapacity ve vyrovnávací paměti &
Obecně platí, že chcete, aby upgrade dokončit i v případě, že cluster je omezen nebo blízko k úplné. Správa kapacity clusteru je ještě důležitější během upgradů než obvykle. V závislosti na počtu upgradovacích domén musí být při procházení clusteru migrováno 5 až 20 procent kapacity. Ta práce musí někam jít. To je místo, kde je pojem [vyrovnávací paměti kapacity](service-fabric-cluster-resource-manager-cluster-description.md#buffered-capacity) je užitečné. Kapacita ve vyrovnávací paměti je respektována při běžném provozu. Správce prostředků clusteru může v případě potřeby zaplnit uzly až do celkové kapacity (spotřebovávající vyrovnávací paměť) během inovací.

## <a name="next-steps"></a>Další kroky
* Začněte od začátku a [získejte úvod do Správce prostředků clusteru Service Fabric](service-fabric-cluster-resource-manager-introduction.md)
