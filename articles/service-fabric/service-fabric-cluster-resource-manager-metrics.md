---
title: Správa načítání aplikací Azure Service Fabric pomocí metrik
description: Přečtěte si, jak nakonfigurovat a používat metriky v Service Fabric ke správě spotřeby prostředků služby.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: ea21502cdab35b261e20af7f23b7b522f77c6667
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75452001"
---
# <a name="managing-resource-consumption-and-load-in-service-fabric-with-metrics"></a>Správa spotřeby prostředků a zatížení v Service Fabric s metrikami
*Metriky* jsou prostředky, které vaše služby péče a které jsou poskytovány uzly v clusteru. Metrika je vše, co chcete spravovat za účelem zlepšení nebo sledování výkonu vašich služeb. Můžete například sledovat spotřebu paměti, abyste věděli, zda je vaše služba přetížena. Dalším použitím je zjistit, zda služba může přesunout jinde, kde je paměť méně omezená, aby se získal lepší výkon.

Věci jako paměť, disk a využití procesoru jsou příklady metrik. Tyto metriky jsou fyzické metriky, prostředky, které odpovídají fyzické prostředky na uzlu, které je třeba spravovat. Metriky mohou být také (a běžně jsou) logické metriky. Logické metriky jsou věci jako "MyWorkQueueDepth" nebo "MessagesToProcess" nebo "TotalRecords". Logické metriky jsou definovány aplikací a nepřímo odpovídají některé fyzické spotřeby prostředků. Logické metriky jsou běžné, protože může být obtížné měřit a vykazovat spotřebu fyzických zdrojů na základě služby. Složitost měření a vykazování vlastní fyzické metriky je také důvod, proč Service Fabric poskytuje některé výchozí metriky.

## <a name="default-metrics"></a>Výchozí metriky
Řekněme, že chcete začít psát a nasazovat službu. V tomto okamžiku nevíte, jaké fyzické nebo logické prostředky spotřebovává. To je v pořádku! Správce prostředků clusteru Service Fabric používá některé výchozí metriky, pokud nejsou zadány žádné jiné metriky. Jsou to tyto:

  - PrimaryCount - počet primárních replik v uzlu 
  - ReplicaCount - počet celkových stavových replik v uzlu
  - Počet - počet všech objektů služby (bezstavové a stavové) na uzlu

| Metrika | Zatížení instance bez stavů | Stavové sekundární zatížení | Stavové primární zatížení | Hmotnost |
| --- | --- | --- | --- | --- |
| PrimaryCount |0 |0 |1 |Vysoká |
| ReplicaCount |0 |1 |1 |Střednědobé používání |
| Počet |1 |1 |1 |Nízká |


Pro základní úlohy výchozí metriky poskytují slušné rozdělení práce v clusteru. V následujícím příkladu se podíváme, co se stane, když vytvoříme dvě služby a spoléháme na výchozí metriky pro vyrovnávání. První služba je stavová služba se třemi oddíly a cílovou sadou replik velikosti tři. Druhá služba je bezstavová služba s jedním oddílem a počet instancí tři.

Získáme:

<center>

![Rozložení clusteru s výchozími metrikami][Image1]
</center>

Všimněte si:
  - Primární repliky pro stavovou službu jsou distribuovány mezi několika uzly
  - Repliky pro stejný oddíl jsou na různých uzlech
  - Celkový počet primárek a sekundárních je distribuován v clusteru
  - Celkový počet objektů služby jsou rovnoměrně přiděleny na každém uzlu

Dobré!

Výchozí metriky fungují skvěle jako začátek. Výchozí metriky vás však přenesou pouze do posudku. Například: Jaká je pravděpodobnost, že schéma dělení, které jste vybrali, má za následek dokonalé rovnoměrné využití všemi oddíly? Jaká je šance, že zatížení pro danou službu je konstantní v průběhu času, nebo dokonce stejně napříč více oddíly právě teď?

Můžete spustit pouze s výchozími metrikami. To však obvykle znamená, že využití clusteru je nižší a nerovnoměrnější, než byste chtěli. Důvodem je, že výchozí metriky nejsou adaptivní a předpokládají, že vše je ekvivalentní. Například Primární, který je zaneprázdněn a ten, který není oba přispívají "1" na PrimaryCount metriky. V nejhorším případě pomocí pouze výchozí metriky může také způsobit, že přeplánované uzly za následek problémy s výkonem. Pokud máte zájem získat co nejvíce z vašeho clusteru a vyhnout se problémům s výkonem, musíte použít vlastní metriky a dynamické vykazování zatížení.

## <a name="custom-metrics"></a>Vlastní metriky
Metriky jsou konfigurovány na základě pojmenovaná instance služby při vytváření služby.

Každá metrika má některé vlastnosti, které ji popisují: název, hmotnost a výchozí zatížení.

* Název metriky: Název metriky. Název metriky je jedinečný identifikátor pro metriku v rámci clusteru z pohledu Správce prostředků.
* Hmotnost: Metrika hmotnost definuje, jak důležitá je tato metrika vzhledem k ostatním metrikám pro tuto službu.
* Výchozí zatížení: Výchozí zatížení je reprezentováno odlišně v závislosti na tom, zda je služba bezstavová nebo stavová.
  * Pro bezstavové služby má každá metrika jednu vlastnost s názvem DefaultLoad
  * Pro stavové služby definujete:
    * PrimaryDefaultLoad: Výchozí částka této metriky, kterou tato služba spotřebovává, když je primární
    * SecondaryDefaultLoad: Výchozí částka této metriky, kterou tato služba spotřebovává, pokud je sekundární

> [!NOTE]
> Pokud definujete vlastní metriky a chcete _také_ použít výchozí metriky, musíte _explicitně_ přidat výchozí metriky zpět a definovat váhy a hodnoty pro ně. Je to proto, že je nutné definovat vztah mezi výchozí metriky a vlastní metriky. Například možná vám záleží ConnectionCount nebo WorkQueueDepth více než primární distribuce. Ve výchozím nastavení je hmotnost metriky PrimaryCount vysoká, takže ji chcete snížit na střední, když přidáte další metriky, abyste zajistili, že mají přednost.
>

### <a name="defining-metrics-for-your-service---an-example"></a>Definování metrik pro vaši službu – příklad
Řekněme, že chcete následující konfiguraci:

  - Vaše služba hlásí metriku s názvem ConnectionCount.
  - Chcete také použít výchozí metriky 
  - Provedli jste některá měření a víte, že primární replika této služby obvykle zabírá 20 jednotek "ConnectionCount"
  - Sekundární použití 5 jednotek "ConnectionCount"
  - Víte, že "ConnectionCount" je nejdůležitější metrika z hlediska správy výkonu této konkrétní služby
  - Stále chcete primární repliky vyvážené. Vyvažování primární repliky je obecně dobrý nápad bez ohledu na to, co. To pomáhá zabránit ztrátě některých uzlů nebo domény selhání ovlivnění většiny primárních replik spolu s ním. 
  - V opačném případě jsou výchozí metriky v pořádku

Tady je kód, který byste zapisovali, abyste vytvořili službu s tou metrickou konfigurací:

Kód:

```csharp
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
StatefulServiceLoadMetricDescription connectionMetric = new StatefulServiceLoadMetricDescription();
connectionMetric.Name = "ConnectionCount";
connectionMetric.PrimaryDefaultLoad = 20;
connectionMetric.SecondaryDefaultLoad = 5;
connectionMetric.Weight = ServiceLoadMetricWeight.High;

StatefulServiceLoadMetricDescription primaryCountMetric = new StatefulServiceLoadMetricDescription();
primaryCountMetric.Name = "PrimaryCount";
primaryCountMetric.PrimaryDefaultLoad = 1;
primaryCountMetric.SecondaryDefaultLoad = 0;
primaryCountMetric.Weight = ServiceLoadMetricWeight.Medium;

StatefulServiceLoadMetricDescription replicaCountMetric = new StatefulServiceLoadMetricDescription();
replicaCountMetric.Name = "ReplicaCount";
replicaCountMetric.PrimaryDefaultLoad = 1;
replicaCountMetric.SecondaryDefaultLoad = 1;
replicaCountMetric.Weight = ServiceLoadMetricWeight.Low;

StatefulServiceLoadMetricDescription totalCountMetric = new StatefulServiceLoadMetricDescription();
totalCountMetric.Name = "Count";
totalCountMetric.PrimaryDefaultLoad = 1;
totalCountMetric.SecondaryDefaultLoad = 1;
totalCountMetric.Weight = ServiceLoadMetricWeight.Low;

serviceDescription.Metrics.Add(connectionMetric);
serviceDescription.Metrics.Add(primaryCountMetric);
serviceDescription.Metrics.Add(replicaCountMetric);
serviceDescription.Metrics.Add(totalCountMetric);

await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("ConnectionCount,High,20,5”,"PrimaryCount,Medium,1,0”,"ReplicaCount,Low,1,1”,"Count,Low,1,1”)
```

> [!NOTE]
> Výše uvedené příklady a zbytek tohoto dokumentu popisují správu metrik na základě pojmenovanou službu. Je také možné definovat metriky pro vaše služby na úrovni _typu_ služby. Toho lze dosáhnout zadáním je ve vašich manifestech služby. Definování metriky úrovně typu se nedoporučuje z několika důvodů. Prvním důvodem je, že názvy metrik jsou často specifické pro prostředí. Pokud neexistuje pevná smlouva na místě, nemůžete si být jisti, že metrika "Jádra" v jednom prostředí není "MiliCores" nebo "CoReS" v jiných. Pokud jsou metriky definovány v manifestu, musíte vytvořit nové manifesty pro jedno prostředí. To obvykle vede k šíření různých manifestů pouze s malými rozdíly, což může vést k problémům s řízením.  
>
> Metrika zatížení jsou běžně přiřazovány na základě pojmenovaná instance služby. Řekněme například, že vytvoříte jednu instanci služby pro CustomerA, která ji plánuje používat pouze lehce. Řekněme také, že vytvoříte další pro CustomerB, který má větší pracovní vytížení. V takovém případě byste pravděpodobně chtěli vyladit výchozí zatížení pro tyto služby. Pokud máte metriky a zatížení definované prostřednictvím manifestů a chcete podporovat tento scénář, vyžaduje různé typy aplikací a služeb pro každého zákazníka. Hodnoty definované v době vytvoření služby přepíší hodnoty definované v manifestu, takže je můžete použít k nastavení konkrétních výchozích hodnot. Však způsobuje hodnoty deklarované v manifestech neodpovídá těm, se kterými služba skutečně běží. To může vést ke zmatku. 
>

Připomínáme: Pokud chcete použít pouze výchozí metriky, nemusíte se při vytváření služby dotýkat kolekce metrik vůbec ani dělat nic zvláštního. Výchozí metriky se použijí automaticky, když nejsou definovány žádné jiné. 

Nyní si projdeme každé z těchto nastavení podrobněji a promluvme si o chování, které ovlivňuje.

## <a name="load"></a>Načtení
Celý bod definování metriky je představují některé zatížení. *Zatížení* je, kolik dané metriky spotřebovává některé instance služby nebo repliky na daném uzlu. Zatížení lze konfigurovat téměř v každém bodě. Například:

  - Zatížení lze definovat při vytvoření služby. Tose nazývá _výchozí zatížení_.
  - Informace o metrikách, včetně výchozího zatížení, pro službu lze aktualizovat po vytvoření služby. Tato aktualizace se nazývá _aktualizace služby_. 
  - Zatížení pro daný oddíl lze obnovit na výchozí hodnoty pro tuto službu. Tomu se říká _obnovení zatížení oddílu_.
  - Zatížení lze vykazovat na základě objektu služby dynamicky za běhu. Tose nazývá _zatížení vykazování_. 
  
Všechny tyto strategie lze použít v rámci stejné služby po celou dobu její životnosti. 

## <a name="default-load"></a>Výchozí zatížení
*Výchozí zatížení* je, kolik z metriky každý objekt služby (bezstavová instance nebo stavové repliky) této služby spotřebovává. Správce prostředků clusteru používá toto číslo pro zatížení objektu služby, dokud neobdrží další informace, například zprávu o dynamickém zatížení. Pro jednodušší služby je výchozí zatížení statickou definicí. Výchozí zatížení se nikdy neaktualizuje a používá se po dobu životnosti služby. Výchozí zatížení funguje skvěle pro jednoduché scénáře plánování kapacity, kde určité množství prostředků jsou vyhrazeny pro různé úlohy a nemění.

> [!NOTE]
> Další informace o správě kapacity a definování kapacit pro uzly v clusteru naleznete v [tomto článku](service-fabric-cluster-resource-manager-cluster-description.md#capacity).
> 

Správce prostředků clusteru umožňuje stavové služby určit jiné výchozí zatížení pro jejich primárky a sekundární. Bezstavové služby můžete zadat pouze jednu hodnotu, která platí pro všechny instance. Pro stavové služby se výchozí zatížení primárních a sekundárních replik obvykle liší, protože repliky provádějí různé druhy práce v každé roli. Například Primárky obvykle slouží čtení i zápisy a zpracovávat většinu výpočetní zátěže, zatímco sekundární ne. Výchozí zatížení primární repliky je obvykle vyšší než výchozí zatížení pro sekundární repliky. Reálná čísla by měla záviset na vašich vlastních měřeních.

## <a name="dynamic-load"></a>Dynamické zatížení
Řekněme, že už nějakou dobu řídíš své služby. S některými sledování, jste si všimli, že:

1. Některé oddíly nebo instance dané služby spotřebovávají více prostředků než jiné
2. Některé služby mají zatížení, které se v průběhu času mění.

Existuje spousta věcí, které by mohly způsobit tyto typy kolísání zatížení. Například různé služby nebo oddíly jsou spojeny s různými zákazníky s různými požadavky. Zatížení může také změnit, protože množství práce služby se liší v průběhu dne. Bez ohledu na důvod obvykle neexistuje žádné jediné číslo, které můžete použít pro výchozí. To platí zejména, pokud chcete získat co nejvíce využití z clusteru. Jakákoli hodnota, kterou vyberete pro výchozí zatížení, je někdy chybná. Nesprávné výchozí zatížení má za následek správce prostředků clusteru přes nebo pod přidělování prostředků. V důsledku toho máte uzly, které jsou nad nebo pod využívány, i když správce prostředků clusteru si myslí, že cluster je vyvážený. Výchozí zatížení jsou stále dobré, protože poskytují některé informace pro počáteční umístění, ale nejsou úplným příběhem pro skutečné úlohy. Chcete-li přesně zachytit měnící se požadavky na prostředky, Správce prostředků clusteru umožňuje každému objektu služby aktualizovat vlastní zatížení za běhu. To se nazývá dynamické vykazování zatížení.

Dynamické sestavy zatížení umožňují replikám nebo instancím upravit jejich přidělení/hlášené zatížení metrik po celou dobu jejich životnosti. Replika služby nebo instance, která byla studená a neprovádí žádnou práci, by obvykle hlásila, že používá nízké množství dané metriky. Zaneprázdněná replika nebo instance by hlásila, že používají více.

Vytváření sestav zatížení na repliku nebo instanci umožňuje Správci prostředků clusteru reorganizovat jednotlivé objekty služby v clusteru. Reorganizace služeb pomáhá zajistit, že získají potřebné prostředky. Zaneprázdněné služby efektivně získat "kultivovat" prostředky z jiných replik nebo instancí, které jsou aktuálně studené nebo dělat méně práce.

V rámci spolehlivé služby, kód sestavy zatížení dynamicky vypadá takto:

Kód:

```csharp
this.Partition.ReportLoad(new List<LoadMetric> { new LoadMetric("CurrentConnectionCount", 1234), new LoadMetric("metric1", 42) });
```

Služba může vykazovat všechny metriky definované pro ni v době vytvoření. Pokud služba hlásí zatížení pro metriku, která není nakonfigurována pro použití, Service Fabric ignoruje tuto sestavu. Pokud jsou současně hlášeny další metriky, které jsou platné, jsou tyto přehledy přijaty. Kód služby můžete měřit a hlásit všechny metriky, které ví, jak a operátory můžete zadat konfiguraci metriky použít bez nutnosti měnit kód služby. 

### <a name="updating-a-services-metric-configuration"></a>Aktualizace konfigurace metriky služby
Seznam metrik spojených se službou a vlastnosti těchto metrik lze aktualizovat dynamicky, zatímco služba je aktivní. To umožňuje experimentování a flexibilitu. Některé příklady, kdy je to užitečné, jsou:

  - zakázání metriky s přehledem buggy pro konkrétní službu
  - změna konfigurace hmotností metrik na základě požadovaného chování
  - povolení nové metriky až poté, co byl kód již nasazen a ověřen prostřednictvím jiných mechanismů
  - změna výchozího zatížení služby na základě pozorovaného chování a spotřeby

Hlavní api pro změnu `FabricClient.ServiceManagementClient.UpdateServiceAsync` konfigurace metriky `Update-ServiceFabricService` jsou v C# a v PowerShellu. Jakékoli informace, které zadáte pomocí těchto api nahradí existující informace metriky pro službu okamžitě. 

## <a name="mixing-default-load-values-and-dynamic-load-reports"></a>Míchání výchozích hodnot zatížení a dynamických sestav zatížení
Pro stejnou službu lze použít výchozí zatížení a dynamická zatížení. Pokud služba využívá výchozí zatížení i dynamické sestavy zatížení, výchozí zatížení slouží jako odhad, dokud se nezobrazí dynamické sestavy. Výchozí zatížení je dobré, protože poskytuje Správci prostředků clusteru něco, s čím může pracovat. Výchozí zatížení umožňuje Správci prostředků clusteru umístit objekty služby na dobrá místa při jejich vytvoření. Pokud nejsou k dispozici žádné výchozí informace o zatížení, umístění služeb je efektivně náhodné. Když sestavy zatížení dorazí později počáteční náhodné umístění je často nesprávné a Správce prostředků clusteru má přesunout služby.

Vezměme si náš předchozí příklad a uvidíme, co se stane, když přidáme některé vlastní metriky a dynamické vykazování zatížení. V tomto příkladu používáme "MemoryInMb" jako ukázkovou metriku.

> [!NOTE]
> Paměť je jednou ze systémových metrik, které service fabric může [řídit prostředky](service-fabric-resource-governance.md)a hlášení sami je obvykle obtížné. Ve skutečnosti neočekáváme, že budete podávat zprávy o spotřebě paměti; Paměť se zde používá jako pomůcka pro učení o možnostech Správce prostředků clusteru.
>

Předpokládejme, že jsme původně vytvořili stavovou službu s následujícím příkazem:

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("MemoryInMb,High,21,11”,"PrimaryCount,Medium,1,0”,"ReplicaCount,Low,1,1”,"Count,Low,1,1”)
```

Připomínáme, že tato syntaxe je ("MetricName, MetricWeight, PrimaryDefaultLoad, SecondaryDefaultLoad").

Podívejme se, jak by mohlo vypadat jedno možné rozložení clusteru:

<center>

![Vyrovnaný cluster s výchozí i vlastní metriky][Image2]
</center>

Některé věci, které stojí za zmínku:

* Sekundární repliky v rámci oddílu může mít každý své vlastní zatížení
* Celkově metriky vypadají vyváženě. Pro paměť je poměr mezi maximálním a minimálním zatížením 1,75 (uzel s největším zatížením je N3, nejmenší je N2 a 28/16 = 1,75).

Je tu několik věcí, které musíme ještě vysvětlit:

* Co určilo, zda byl poměr 1,75 přiměřený, nebo ne? Jak Správce prostředků clusteru pozná, zda je to dost dobré nebo co je třeba udělat víc práce?
* Kdy dochází k vyvažování?
* Co to znamená, že paměť byla vážena "High"?

## <a name="metric-weights"></a>Metrická závaží
Sledování stejných metrik v různých službách je důležité. Toto globální zobrazení umožňuje Správci prostředků clusteru sledovat spotřebu v clusteru, vyvážit spotřebu mezi uzly a zajistit, aby uzly nepřekročily kapacitu. Služby však mohou mít různá zobrazení, pokud jde o důležitost stejné metriky. Také v clusteru s mnoha metrikami a spoustou služeb nemusí pro všechny metriky existovat dokonale vyvážená řešení. Jak má Správce prostředků clusteru tyto situace řešit?

Metrika váhy umožňují Správce prostředků clusteru rozhodnout, jak vyvážit clusteru, když neexistuje dokonalá odpověď. Metrika váhy také umožňují Správce prostředků clusteru vyvážit konkrétní služby odlišně. Metriky mohou mít čtyři různé úrovně hmotnosti: Nula, Nízká, Střední a Vysoká. Metrika s váhou nula nepřispívá při zvažování, zda jsou věci vyvážené nebo ne. Jeho zatížení však stále přispívá k řízení kapacity. Metriky s nulovou hmotností jsou stále užitečné a jsou často používány jako součást chování služby a sledování výkonu. [Tento článek](service-fabric-diagnostics-event-generation-infra.md) obsahuje další informace o použití metrik pro monitorování a diagnostiku vašich služeb. 

Skutečný dopad různých metrik váhy v clusteru je, že Správce prostředků clusteru generuje různá řešení. Metriky váhy informujte Správce prostředků clusteru, že některé metriky jsou důležitější než jiné. Pokud neexistuje dokonalé řešení, správce prostředků clusteru může upřednostňovat řešení, která lépe vyvažují vyšší vážené metriky. Pokud si služba myslí, že určitá metrika není důležitá, může zjistit, že jejich použití této metriky je nevyvážené. To umožňuje jiné službě získat rovnoměrné rozdělení některé metriky, která je pro něj důležitá.

Podívejme se na příklad některých sestav zatížení a jak různé metriky váhy má za následek různé přidělení v clusteru. V tomto příkladu vidíme, že přepnutí relativní hmotnost metriky způsobí, že Správce prostředků clusteru vytvořit různá uspořádání služeb.

<center>

![Příklad metrické hmotnosti a jeho dopad na řešení vyvažování][Image3]
</center>

V tomto příkladu existují čtyři různé služby, všechny vykazování různé hodnoty pro dvě různé metriky, MetricA a MetricB. V jednom případě všechny služby definovat MetricA je nejdůležitější (Hmotnost = Vysoká) a MetricB jako nedůležité (Hmotnost = Nízká). V důsledku toho vidíme, že Správce prostředků clusteru umístí služby tak, aby MetricA je lépe vyvážená než MetricB. "Lépe vyvážené" znamená, že MetricA má nižší má nižší směrodatnou odchylku než MetricB. Ve druhém případě obrátíme metrickou hmotnost. V důsledku toho Správce prostředků clusteru swapy služby A a B přijít s přidělením, kde MetricB je lépe vyvážené než MetricA.

> [!NOTE]
> Metrika váhy určit, jak správce prostředků clusteru by měl zůstatek, ale ne, když by mělo dojít k vyrovnávání. Další informace o vyvažování naleznete v [tomto článku](service-fabric-cluster-resource-manager-balancing.md)
>

### <a name="global-metric-weights"></a>Globální metrická vahovky
Řekněme, že ServiceA definuje MetricA jako vysokou váhu a ServiceB nastaví váhu pro MetricA na nízkou nebo nulovou. Jaká je skutečná váha, která nakonec zvykne?

Existuje více závaží, které jsou sledovány pro každou metriku. První váha je ta, která je definována pro metriku při vytvoření služby. Druhá váha je globální hmotnost, která se vypočítá automaticky. Správce prostředků clusteru používá obě tyto váhy při vyhodnocování řešení. Vezmeme-li v úvahu obě váhy, je důležité. To umožňuje Správci prostředků clusteru vyvážit každou službu podle vlastních priorit a také zajistit, aby byl cluster jako celek přidělen správně.

Co by se stalo, kdyby správce prostředků clusteru nezáleželo na globální i místní rovnováze? No, je to snadné vytvořit řešení, která jsou globálně vyvážené, ale které mají za následek špatnou rovnováhu zdrojů pro jednotlivé služby. V následujícím příkladu se podívejme na službu nakonfigurovanou pouze s výchozími metrikami a podívejte se, co se stane, když se vezme v úvahu pouze globální zůstatek:

<center>

![Dopad globálního řešení][Image4]
</center>

V nejvyšším příkladu založeném pouze na globální rovnováze je cluster jako celek skutečně vyvážený. Všechny uzly mají stejný počet primárek a stejný počet celkových replik. Pokud se však podíváte na skutečný dopad této alokace, není to tak dobré: ztráta libovolného uzlu nepřiměřeně ovlivňuje určitou pracovní zátěž, protože vynese všechny své primárky. Například pokud první uzel selže tři primárky pro tři různé oddíly circle služby by všechny být ztraceny. Naopak triangle a hexagon služby mají své oddíly ztratí repliku. To nezpůsobí žádné narušení, než nutnost obnovit dolů repliky.

V dolním příkladu správce prostředků clusteru distribuuje repliky na základě globálního i provozního zůstatku na základě služby. Při výpočtu skóre řešení dává většinu váhy globálnímu řešení a (konfigurovatelnou) část pro jednotlivé služby. Globální zůstatek pro metriku se vypočítá na základě průměru metriky váhy z každé služby. Každá služba je vyvážená podle vlastních definovaných metrických vahou. Tím je zajištěno, že služby jsou v sobě vyváženy podle svých vlastních potřeb. V důsledku toho pokud stejný první uzel selže selhání selhání je distribuován napříč všechny oddíly všech služeb. Dopad na každého z nich je stejný.

## <a name="next-steps"></a>Další kroky
- Další informace o konfiguraci služeb [naleznete v informacích o konfiguraci služeb](service-fabric-cluster-resource-manager-configure-services.md)(service-fabric-cluster-resource-manager-configure-services.md)
- Definování defragmentace metriky je jedním ze způsobů, jak konsolidovat zatížení uzlů namísto jeho rozprostření. Informace o konfiguraci defragmentace naleznete v [tomto článku](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
- Informace o tom, jak Správce prostředků clusteru spravuje a vyrovnává zatížení clusteru, naleznete v článku o [vyrovnávání zatížení](service-fabric-cluster-resource-manager-balancing.md)
- Začněte od začátku a [získejte úvod do Správce prostředků clusteru Service Fabric](service-fabric-cluster-resource-manager-introduction.md)
- Náklady na přesun je jedním ze způsobů, jak správci prostředků clusteru signalizovat, že přesun některých služeb je nákladnější než jiné. Další informace o nákladech na přesun naleznete v [tomto článku](service-fabric-cluster-resource-manager-movement-cost.md)

[Image1]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-cluster-layout-with-default-metrics.png
[Image2]:./media/service-fabric-cluster-resource-manager-metrics/Service-Fabric-Resource-Manager-Dynamic-Load-Reports.png
[Image3]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-metric-weights-impact.png
[Image4]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-global-vs-local-balancing.png
