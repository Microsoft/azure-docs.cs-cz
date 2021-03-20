---
title: Správa zatížení aplikace v Azure Service Fabric pomocí metrik
description: Přečtěte si, jak nakonfigurovat a používat metriky v Service Fabric ke správě spotřeby prostředků služby.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.custom: devx-track-csharp
ms.openlocfilehash: 2a7dedea2937c9cafb4216da3628aa1360ad6993
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92173003"
---
# <a name="managing-resource-consumption-and-load-in-service-fabric-with-metrics"></a>Správa spotřeby prostředků a načítání v Service Fabric se metrikami
*Metriky* jsou prostředky, o kterých se vaše služby týkají a které jsou poskytovány uzly v clusteru. Metrika je cokoli, co chcete spravovat, aby bylo možné zlepšit nebo sledovat výkon služeb. Můžete například sledovat spotřebu paměti a zjistit, jestli je vaše služba přetížená. Dalším použitím je zjistit, zda se může služba přesunout jinam, kde je paměť méně omezená, aby bylo možné dosáhnout vyššího výkonu.

Příklady metrik se týkají například paměti, disku a využití procesoru. Tyto metriky představují fyzické metriky, prostředky, které odpovídají fyzickým prostředkům v uzlu, který je třeba spravovat. Metriky mohou být také logickými metrikami (a obvykle). Logické metriky jsou například "MyWorkQueueDepth" nebo "MessagesToProcess" nebo "TotalRecords". Logické metriky jsou definované aplikací a nepřímo odpovídají některým fyzickým spotřebám prostředků. Logické metriky jsou běžné, protože může být obtížné změřit a ohlásit spotřebu fyzických prostředků na základě jednotlivých služeb. Složitost měření a vytváření sestav vašich vlastních fyzických metrik také znamená, proč Service Fabric poskytuje některé výchozí metriky.

## <a name="default-metrics"></a>Výchozí metriky
Řekněme, že chcete začít s psaním a nasazením služby. V tuto chvíli nevíte, jaké fyzické nebo logické prostředky spotřebovává. To je dobré! Cluster Service Fabric Správce prostředků používá některé výchozí metriky, pokud nejsou zadány žádné jiné metriky. Jsou to tyto:

  - PrimaryCount – počet primárních replik na uzlu 
  - ReplicaCount – počet celkových stavových replik na uzlu
  - Count – počet všech objektů služby (bezstavových a stavových) na uzlu

| Metric | Bezstavová zátěžová instance | Stavové sekundární zatížení | Stavové primární zatížení | Hmotnost |
| --- | --- | --- | --- | --- |
| PrimaryCount |0 |0 |1 |Vysoká |
| ReplicaCount |0 |1 |1 |Střední |
| Počet |1 |1 |1 |Nízká |


Výchozí metriky pro základní úlohy poskytují dát distribuci práce v clusteru. V následujícím příkladu se podívejme na to, co se stane, když vytvoříme dvě služby a spoléhá se na výchozí metriky pro vyrovnávání. První služba je stavová služba se třemi oddíly a cílovou sadou repliky o velikosti tři. Druhá služba je Bezstavová služba s jedním oddílem a počtem instancí tři.

Získáme:

<center>

![Rozložení clusteru s výchozími metrikami][Image1]
</center>

Všimněte si:
  - Primární repliky stavové služby jsou distribuované napříč několika uzly.
  - Repliky pro stejný oddíl jsou v různých uzlech.
  - Celkový počet primárních a sekundárních primárních serverů se distribuuje v clusteru.
  - Celkový počet objektů služby je rovnoměrně přidělen na každý uzel.

Dobré!

Výchozí metriky fungují skvěle jako na začátku. Výchozí metriky ale budou zatím přenášet. Například: jaká je pravděpodobnost, že schéma dělení, které jste vybrali, má za následek naprostou i použití všemi oddíly? Jaká je pravděpodobnost, že zatížení dané služby je v průběhu času konstantní, nebo dokonce jenom stejné v několika oddílech hned?

Mohli byste spustit pouze s výchozími metrikami. To ale obvykle znamená, že využití clusteru je menší a více než vám to vyhovuje. Důvodem je to, že výchozí metriky nejsou adaptivní a předpokládají se, že vše je ekvivalentní. Například primární primární, který je zaneprázdněný a jeden, který do metriky PrimaryCount přispěje "1". V nejhorším případě můžou použití jenom výchozích metrik způsobit také naplánované uzly, které mají za následek problémy s výkonem. Pokud vás zajímá, jak využít váš cluster a vyhnout se problémům s výkonem, je nutné použít vlastní metriky a generování sestav dynamického načtení.

## <a name="custom-metrics"></a>Vlastní metriky
Při vytváření služby jsou metriky nakonfigurovány na základě instance podle názvu služby.

Každá metrika má některé vlastnosti, které ji popisují: název, váhu a výchozí zatížení.

* Název metriky: název metriky. Název metriky je jedinečný identifikátor pro metriku v rámci clusteru z hlediska Správce prostředků.
* Váha: váha metriky definuje, jak je tato metrika relativní vzhledem k ostatním metrikám této služby.
* Výchozí zatížení: výchozí zatížení je znázorněno různě v závislosti na tom, zda je služba Bezstavová nebo stavová.
  * U bezstavových služeb má každá metrika jedinou vlastnost s názvem DefaultLoad.
  * Pro stavové služby, které definujete, postupujte takto:
    * PrimaryDefaultLoad: výchozí hodnota této metriky, kterou tato služba využívá, když je primární
    * SecondaryDefaultLoad: výchozí hodnota této metriky, kterou tato služba využívá, když je sekundární

> [!NOTE]
> Pokud definujete vlastní metriky a chcete _také_ použít výchozí metriky, musíte _explicitně_ přidat výchozí metriky a definovat váhy a hodnoty pro ně. Je to proto, že je nutné definovat relaci mezi výchozími metrikami a vlastními metrikami. Například můžete se starat o ConnectionCount nebo WorkQueueDepth více než k primární distribuci. Ve výchozím nastavení je váha metriky PrimaryCount vysoká, takže ji chcete snížit na střední, když přidáte další metriky, abyste zajistili jejich přednost.
>

### <a name="defining-metrics-for-your-service---an-example"></a>Definování metrik pro službu – příklad
Řekněme, že chcete následující konfiguraci:

  - Vaše služba hlásí metriku s názvem "ConnectionCount".
  - Chcete také použít výchozí metriky 
  - Provedli jste některá měření a věděli, že primární replika této služby zabere 20 jednotek "ConnectionCount".
  - Sekundární jednotky používají 5 jednotek "ConnectionCount"
  - Víte, že "ConnectionCount" je nejdůležitější metrikou v souvislosti s řízením výkonu této konkrétní služby.
  - Pořád chcete vyrovnávat primární repliky. Vyvážení primárních replik je obecně dobrý nápad bez ohledu na to, co. To pomáhá zabránit ztrátě některých uzlů nebo domén selhání proti ovlivnění většiny primárních replik spolu s ní. 
  - V opačném případě jsou výchozí metriky přesné.

Zde je kód, který byste napsali pro vytvoření služby s touto metrikou konfigurace:

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

Prostředí

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("ConnectionCount,High,20,5”,"PrimaryCount,Medium,1,0”,"ReplicaCount,Low,1,1”,"Count,Low,1,1”)
```

> [!NOTE]
> Výše uvedené příklady a zbytek tohoto dokumentu popisují správu metrik na základě jednotlivých pojmenovaných služeb. Je také možné definovat metriky pro vaše služby na úrovni _typu_ služby. To lze provést zadáním v manifestech služby. Definování metrik na úrovni typu se z několika důvodů nedoporučuje. Prvním důvodem je to, že názvy metrik jsou často specifické pro konkrétní prostředí. Pokud není k dispozici žádný pracovní kontrakt, nemusíte mít jistotu, že metrika "jádra" v jednom prostředí není "MiliCores" nebo "CoRes" v jiných prostředích. Pokud jsou vaše metriky definované v manifestu, potřebujete vytvořit nové manifesty pro každé prostředí. To obvykle vede k rozmnožení různých manifestů s pouze drobnými rozdíly, což může vést k potížím se správou.  
>
> Zatížení metriky se obvykle přiřazují na základě instance podle názvu služby. Řekněme například, že vytvoříte jednu instanci služby pro zákazníka, která plánuje použití jenom lehce. Řekněme také, že vytvoříte další pro CustomerB, který má větší zatížení. V takovém případě byste pravděpodobně chtěli pro tyto služby vylepšit výchozí zatížení. Pokud máte metriky a načítáte je definována prostřednictvím manifestů a chcete tento scénář podporovat, vyžaduje pro každého zákazníka různé typy aplikací a služeb. Hodnoty definované při vytváření služby popíší ty, které jsou definované v manifestu, takže je můžete použít k nastavení specifických výchozích hodnot. Nicméně to způsobí, že hodnoty deklarované v manifestech se neshodují s hodnotami, se kterými služba skutečně běží. To může vést k nejasnostem. 
>

Jako připomenutí: Pokud chcete používat jenom výchozí metriky, nemusíte se při vytváření služby nic dotýkat s kolekcí metrik. Výchozí metriky se automaticky použijí, když žádné jiné nejsou definované. 

Teď projdeme každé z těchto nastavení podrobněji a podíváme se na chování, které ovlivňuje.

## <a name="load"></a>Načítání
Celý bod definující metriky představuje určité zatížení. *Zatížení* je způsob, jakým je velká část dané metriky spotřebovaná určitou instancí služby nebo replikou na daném uzlu. Zatížení je možné nakonfigurovat prakticky v jakémkoli bodě. Například:

  - Zatížení lze definovat při vytvoření služby. Tento typ konfigurace zatížení se nazývá _výchozí zatížení_.
  - Informace o metrikách, včetně výchozích zatížení, lze pro službu aktualizovat po vytvoření služby. Tato aktualizace metriky se provádí _aktualizací služby_.
  - Načtení pro daný oddíl lze obnovit na výchozí hodnoty pro danou službu. Tato aktualizace metriky se nazývá _resetování zatížení oddílu_.
  - Zatížení je možné nahlásit na základě objektu na jednotlivé služby, a to dynamicky během doby běhu. Tato aktualizace metriky se nazývá _načítání sestav_.
  - Zatížení pro repliky nebo instance oddílu lze také aktualizovat pomocí hodnot zatížení vytváření sestav prostřednictvím volání rozhraní API prostředků infrastruktury. Tato aktualizace metriky se označuje jako _načítání sestav pro oddíl_.

Všechny tyto strategie lze použít ve stejné službě v průběhu své životnosti.

## <a name="default-load"></a>Výchozí zatížení
*Výchozí zatížení* je množství metriky jednotlivých objektů služby (Bezstavová instance nebo stavová replika) této služby. Cluster Správce prostředků používá toto číslo pro zatížení objektu služby, dokud neobdrží jiné informace, jako je například sestava dynamického načtení. Pro jednodušší služby je výchozí zatížení Statická definice. Výchozí zatížení se nikdy neaktualizovalo a používá se pro dobu života služby. Výchozí zátěž funguje skvěle pro jednoduché scénáře plánování kapacity, kdy určité množství prostředků je vyhrazeno pro různé úlohy a nemění se.

> [!NOTE]
> Další informace o správě kapacity a definování kapacit pro uzly ve vašem clusteru najdete v [tomto článku](service-fabric-cluster-resource-manager-cluster-description.md#capacity).
> 

Cluster Správce prostředků umožňuje stavovým službám zadat jiné výchozí zatížení pro primární a sekundární. Bezstavové služby můžou určovat jenom jednu hodnotu, která se vztahuje na všechny instance. U stavových služeb se výchozí zatížení primárních a sekundárních replik obvykle liší od toho, že repliky v jednotlivých rolích dělají různé druhy práce. Základní například obvykle slouží ke čtení i zápisu a k zpracování většiny výpočetního zatížení, zatímco sekundární není. Výchozí zatížení primární repliky je obvykle vyšší než výchozí zatížení sekundárních replik. Reálné hodnoty by měly záviset na vlastních měřeních.

## <a name="dynamic-load"></a>Dynamické načtení
Řekněme, že v průběhu chvilky běželi vaši službu. U některých monitorování jste si všimli, že:

1. Některé oddíly nebo instance dané služby spotřebují více prostředků než jiné.
2. Některé služby mají zatížení, které se v průběhu času mění.

Existují spousty věcí, které by mohly způsobovat tyto typy výkyvů zatížení. Například různé služby nebo oddíly jsou přidruženy k různým zákazníkům s různými požadavky. Zátěž se může také změnit, protože množství práce, které se služba v průběhu dne liší. Bez ohledu na důvod není obvykle k dispozici žádné jednotné číslo, které můžete použít pro výchozí nastavení. To platí hlavně v případě, že chcete získat nejvíc využití z clusteru. Všechny hodnoty, které vybíráte pro výchozí zatížení, jsou v některých časových intervalech chybné. Nesprávná výchozí načtení způsobí, že se cluster Správce prostředků buď přes, nebo pod přidělením prostředků. V důsledku toho máte uzly, které se překročí nebo jsou využívány, i když cluster Správce prostředků považuje cluster za vyvážený. Výchozí zatížení jsou pořád dobrá, protože poskytují nějaké informace pro počáteční umístění, ale nejedná se o kompletní příběh pro reálné úlohy. Aby bylo možné přesně zachytit změny požadavků na prostředky, cluster Správce prostředků umožňuje, aby každý objekt služby aktualizoval vlastní zatížení během běhu. Tato funkce se nazývá generování sestav dynamického načtení.

Sestavy dynamického načtení umožňují replikám nebo instancím upravit jejich přidělení a nahlášené zatížení metriky během své životnosti. Replika nebo instance služby, která byla studena a nikoli, by obvykle hlásila, že používala nízké množství dané metriky. Zaneprázdněná replika nebo instance by nahlásila, že používá více.

Zatížení sestav na základě repliky nebo instance umožňuje, aby cluster Správce prostředků reorganizovat jednotlivé objekty služby v clusteru. Změna uspořádání služeb pomáhá zajistit, že získají prostředky, které vyžadují. Zaneprázdněné služby se efektivně dostanou k "získání" prostředků z jiných replik nebo instancí, které jsou v současné době studené nebo méně fungují.

V rámci Reliable Services kód pro nahlášení dynamického načítání vypadá takto:

Kód:

```csharp
this.Partition.ReportLoad(new List<LoadMetric> { new LoadMetric("CurrentConnectionCount", 1234), new LoadMetric("metric1", 42) });
```

Služba může vykazovat jakoukoli metriku, která je pro ni definována v okamžiku vytvoření. Pokud služba hlásí metriku, že není nakonfigurovaná k použití, Service Fabric tuto sestavu ignoruje. Pokud jsou hlášeny jiné metriky v platném okamžiku, jsou tyto zprávy přijaty. Kód služby může měřit a hlásit všechny metriky, které ví, jak a můžou určit konfiguraci metriky, která se má použít, aniž byste museli měnit kód služby. 

## <a name="reporting-load-for-a-partition"></a>Načítání sestav pro oddíl
Předchozí část popisuje, jak se repliky služby nebo instance načítají sami. Existuje další možnost, jak dynamicky nahlásit zatížení pomocí FabricClient. Při načítání sestav pro oddíl můžete hlásit více oddílů najednou.

Tyto sestavy budou použity stejným způsobem jako sestavy načtení, které pocházejí z replik nebo samotných instancí. Hlášené hodnoty budou platné, dokud nebudou hlášeny nové hodnoty načtení, buď replikou, nebo instancí, nebo zasláním nové hodnoty zatížení pro oddíl.

V tomto rozhraní API existuje několik způsobů, jak v clusteru aktualizovat zátěž:

  - Oddíl stavové služby může aktualizovat své primární replikační zatížení.
  - Bezstavové a stavové služby mohou aktualizovat zatížení všech sekundárních replik nebo instancí.
  - Bezstavové a stavové služby mohou aktualizovat zatížení konkrétní repliky nebo instance v uzlu.

Také je možné kombinovat jakékoli aktualizace na oddíl ve stejnou dobu.

Aktualizace zatížení pro více oddílů je možné provést s jedním voláním rozhraní API. v takovém případě bude výstup obsahovat odpověď na oddíl. V případě, že aktualizace oddílu případu není z jakéhokoli důvodu úspěšná, aktualizace pro tento oddíl se přeskočí a bude k dispozici odpovídající kód chyby pro cílový oddíl:

  - PartitionNotFound – zadané ID oddílu neexistuje.
  - ReconfigurationPending – probíhá změna konfigurace oddílu.
  - InvalidForStatelessServices – byl proveden pokus o změnu zatížení primární repliky pro oddíl patřící do bezstavové služby.
  - ReplicaDoesNotExist-v zadaném uzlu neexistuje sekundární replika nebo instance.
  - InvalidOperation – může nastat ve dvou případech: aktualizace zatížení oddílu, který patří do systémové aplikace nebo aktualizace předpokládaného zatížení, není povolená.

Pokud se vrátí některé z těchto chyb, můžete aktualizovat vstup pro určitý oddíl a zopakovat aktualizaci pro konkrétní oddíl.

Kód:

```csharp
Guid partitionId = Guid.Parse("53df3d7f-5471-403b-b736-bde6ad584f42");
string metricName0 = "CustomMetricName0";
List<MetricLoadDescription> newPrimaryReplicaLoads = new List<MetricLoadDescription>()
{
    new MetricLoadDescription(metricName0, 100)
};

string nodeName0 = "NodeName0";
List<MetricLoadDescription> newSpecificSecondaryReplicaLoads = new List<MetricLoadDescription>()
{
    new MetricLoadDescription(metricName0, 200)
};

OperationResult<UpdatePartitionLoadResultList> updatePartitionLoadResults =
    await this.FabricClient.UpdatePartitionLoadAsync(
        new UpdatePartitionLoadQueryDescription
        {
            PartitionMetricLoadDescriptionList = new List<PartitionMetricLoadDescription>()
            {
                new PartitionMetricLoadDescription(
                    partitionId,
                    newPrimaryReplicaLoads,
                    new List<MetricLoadDescription>(),
                    new List<ReplicaMetricLoadDescription>()
                    {
                        new ReplicaMetricLoadDescription(nodeName0, newSpecificSecondaryReplicaLoads)
                    })
            }
        },
        this.Timeout,
        cancellationToken);
```

V tomto příkladu provedete aktualizaci posledního nahlášeného zatížení oddílu _53df3d7f-5471-403b-B736-bde6ad584f42_. Zatížení primární repliky pro metriku _CustomMetricName0_ se aktualizuje hodnotou 100. Současně se zatížení pro stejnou metriku pro konkrétní sekundární repliku, která se nachází na uzlu _NodeName0_, aktualizuje s hodnotou 200.

### <a name="updating-a-services-metric-configuration"></a>Aktualizuje se konfigurace metriky služby.
Seznam metrik přidružených ke službě a vlastnosti těchto metrik se dají dynamicky aktualizovat, pokud je služba živá. To umožňuje experimenty a flexibilitu. Některé příklady, kdy je to užitečné:

  - Zakázání metriky pomocí sestavy ladění pro určitou službu
  - Změna konfigurace vah metrik na základě požadovaného chování
  - povolení nové metriky až poté, co byl kód již nasazen a ověřen prostřednictvím jiných mechanismů
  - Změna výchozího zatížení služby na základě pozorovaného chování a spotřeby

Hlavní rozhraní API pro změnu konfigurace metriky jsou `FabricClient.ServiceManagementClient.UpdateServiceAsync` v jazyce C# a `Update-ServiceFabricService` v prostředí PowerShell. Jakékoli informace, které zadáte pomocí těchto rozhraní API, nahradí okamžitě existující informace metriky pro službu. 

## <a name="mixing-default-load-values-and-dynamic-load-reports"></a>Kombinování výchozích hodnot zatížení a sestav dynamického načtení
Pro stejnou službu lze použít výchozí zatížení a dynamické načtení. Když služba využívá výchozí zatížení a dynamické sestavy zatížení, slouží jako odhad výchozí zatížení, dokud se nezobrazují dynamické sestavy. Výchozí zatížení je dobré, protože zajišťuje, aby cluster Správce prostředků něco. Výchozí zatížení umožňuje, aby cluster Správce prostředků umístit objekty služby do dobrých umístění při jejich vytvoření. Pokud nejsou k dispozici žádné výchozí informace o načtení, umístění služeb je efektivně náhodné. Když budou načteny sestavy později, počáteční náhodné umístění je často chybné a cluster Správce prostředků musí přesunout služby.

Pojďme si vyzkoušet náš předchozí příklad a podívat se, co se stane, když přidáme některé vlastní metriky a generování sestav dynamického načtení. V tomto příkladu používáme jako ukázkovou metriku "MemoryInMb".

> [!NOTE]
> Paměť je jednou ze systémových metrik, které Service Fabric mohou [řídit prostředky](service-fabric-resource-governance.md)a jejich ruční vytváření sestav je obvykle obtížné. Ve skutečnosti neočekáváme, že budete hlásit spotřebu paměti; Zde se používá paměť jako pomůcka pro učení o možnostech Správce prostředků clusteru.
>

Řekněme, že jsme původně vytvořili stavovou službu pomocí následujícího příkazu:

Prostředí

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("MemoryInMb,High,21,11”,"PrimaryCount,Medium,1,0”,"ReplicaCount,Low,1,1”,"Count,Low,1,1”)
```

Tato syntaxe je jako připomenutí ("metric, MetricWeight, PrimaryDefaultLoad, SecondaryDefaultLoad").

Pojďme se podívat, jak může vypadat možné rozložení clusteru:

<center>

![Cluster s vyrovnáváním s výchozí i vlastní metrikou][Image2]
</center>

Zde jsou některé věci, které se zaznamenaly:

* Sekundární repliky v rámci oddílu můžou mít své vlastní zatížení.
* Celkově metriky vypadají jako vyrovnané. Pro paměť je poměr mezi maximální a minimální zátěží 1,75 (uzel s největším zatížením je N3, nejméně je N2 a 28/16 = 1,75).

K dispozici jsou některé věci, které ještě potřebujeme vysvětlit:

* Jak bylo zjištěno, zda byl poměr 1,75 rozumný nebo ne? Jak cluster Správce prostředků ví, jestli je dostatečně dobrý, nebo pokud je to tak více práce?
* Kdy dojde k vyvážení?
* Co to znamená, že paměť byla vážená "vysoká"?

## <a name="metric-weights"></a>Váhy metriky
Sledování stejných metrik v různých službách je důležité. Toto globální zobrazení umožňuje Správce prostředků clusteru sledovat spotřebu v clusteru, vyrovnávat spotřebu napříč uzly a zajistit, aby uzly nepřešly do kapacity. Nicméně služby mohou mít různá zobrazení, která mají význam stejné metriky. Také v clusteru s mnoha metrikami a velkým množstvím služeb nemusí pro všechny metriky existovat dokonale vyvážená řešení. Jak by měl cluster Správce prostředků zvládnout tyto situace?

Váhy metriky umožňují, aby se cluster Správce prostředků rozhodl, jak vyrovnávat cluster, pokud neexistuje žádná dokonalý odpověď. Váhy metriky také umožňují, aby cluster Správce prostředků různým způsobem vyrovnávat konkrétní služby. Metriky můžou mít čtyři různé úrovně důležitosti: 0, nízká, střední a vysoká. Metrika s váhou nula nepřispívá k tomu, když zvažujete, jestli jsou věci vyvážené. Jeho zatížení ale pořád přispívá k řízení kapacity. Metriky s nulovou váhou jsou stále užitečné a často se používají jako součást chování služby a sledování výkonu. V [tomto článku](service-fabric-diagnostics-event-generation-infra.md) najdete další informace o použití metrik pro monitorování a diagnostiku vašich služeb. 

Skutečný dopad různých vah v clusteru je, že cluster Správce prostředků generuje různá řešení. Váhy metriky oznamují clusteru Správce prostředků, že některé metriky jsou důležitější než jiné. Pokud není k dispozici žádné dokonalé řešení, může cluster Správce prostředků preferovat řešení, která lépe vyrovnávají vyšší vážené metriky. Pokud se služba domnívá, že určitá metrika je neimportovaná, může najít jejich použití v nevyrovnaném měřítku. To umožňuje, aby jiná služba získala ještě rozmístění některé metriky, které jsou pro ni důležité.

Pojďme se podívat na příklad některých sestav zatížení a na to, jak různé váhy metriky mají za následek různá přidělení v clusteru. V tomto příkladu vidíte, že přepínání relativní váhy metrik způsobí, že cluster Správce prostředků vytvoří různé mechanismy služeb.

<center>

![Příklad váhy metriky a jeho dopad na vyvážení řešení][Image3]
</center>

V tomto příkladu jsou k dispozici čtyři různé služby, které vytvářejí sestavy různých hodnot pro dvě různé metriky, metriky a MetricB. V jednom případě jsou všechny služby, které definuje metrika, nejdůležitější (váha = vysoká) a MetricB jako neimportované (váha = nízká). V důsledku toho uvidí, že cluster Správce prostředků umístí služby, aby byla metrika lépe vyvážená, než MetricB. "Lépe vyvážené" znamená, že metrika má nižší směrodatnou odchylku než MetricB. V druhém případě vrátíme váhy metriky. V důsledku toho cluster Správce prostředků prohodí služby A a B, aby se přizpůsobilo přidělení, kde je MetricB lépe vyvážené, než je metrika.

> [!NOTE]
> Váhy metriky určují, jak se má cluster Správce prostředků vyrovnávat, ale ne v případě, kdy by mělo dojít k vyvážení Další informace o vyrovnávání zatížení najdete v [tomto článku](service-fabric-cluster-resource-manager-balancing.md) .
>

### <a name="global-metric-weights"></a>Hmotnosti globálních metrik
Řekněme, že služba Service definuje metriku jako vysokou váhu a ServiceB nastaví váhu metriky na nízká nebo nula. Jaká je skutečná váha, která končí použití?

Pro každou metriku je sledováno více vah. První tloušťka je ta, která je definována pro metriku při vytvoření služby. Druhá váha je globální váha, která je automaticky vypočítána. Správce prostředků clusteru používá tyto váhy při bodování řešení. Je důležité brát v úvahu oba váhy. To umožňuje, aby cluster Správce prostředků vyrovnávat každou službu podle svých priorit a také zajistil, že se cluster jako celek přidělí správně.

Co se stane, když se cluster Správce prostředků nestará o globální i místní zůstatek? Dobře je snadné vytvořit řešení, která jsou globálně vyvážená, ale výsledkem je špatné vyvážení prostředků pro jednotlivé služby. V následujícím příkladu se podíváme na službu nakonfigurovanou pouze s výchozími metrikami a zjistíte, co se stane, když se bude brát pouze globální zůstatek:

<center>

![Dopad jenom na globální řešení][Image4]
</center>

V horním příkladu, který je založený jenom na globálním zůstatku, cluster jako celek je skutečně vyvážený. Všechny uzly mají stejný počet primárních prvků a stejné číslo jako celkový počet replik. Nicméně pokud se podíváte na skutečný dopad tohoto přidělení, není tak dobrý: ztráta jakéhokoli uzlu má dopad na určitou úlohu, protože zabírá všechny primární údaje. Například pokud první uzel neprojde tři primárními verzemi pro tři různé oddíly služby Circle, ztratí se všechny tři. Naopak, trojúhelník a šestiúhelníkové služby mají své oddíly ke ztrátě repliky. To nezpůsobuje žádné přerušení, kromě nutnosti obnovení repliky.

V dolním příkladu cluster Správce prostředků distribuuje repliky založené na globálním zůstatku i na úrovni služby. Při výpočtu skóre řešení poskytuje většina váhy globálnímu řešení a (konfigurovatelná) část k jednotlivým službám. Globální zůstatek pro metriku se vypočítá na základě průměru vah metriky od jednotlivých služeb. Každá služba je vyvážená podle vlastní definované váhy metriky. Tím se zajistí, že se služby rovnoměrně vyrovnávají podle svých potřeb. V důsledku toho dojde při selhání jednoho prvního uzlu k distribuci selhání napříč všemi oddíly všech služeb. Dopad na každý je stejný.

## <a name="next-steps"></a>Další kroky
- Další informace o konfiguraci služeb najdete v informacích [o konfiguraci služeb](service-fabric-cluster-resource-manager-configure-services.md)(Service-Fabric-cluster-Resource-Manager-Configure-Services.MD).
- Definování metrik defragmentace je jedním ze způsobů, jak konsolidovat zatížení uzlů místo jejich rozprostření. Informace o tom, jak nakonfigurovat defragmentaci, najdete v [tomto článku](service-fabric-cluster-resource-manager-defragmentation-metrics.md) .
- Informace o tom, jak cluster Správce prostředků spravuje a vyrovnává zatížení v clusteru, najdete v článku o [Vyrovnávání zatížení](service-fabric-cluster-resource-manager-balancing.md) .
- Začněte od začátku a [Získejte Úvod do clusteru Service Fabric správce prostředků](service-fabric-cluster-resource-manager-introduction.md)
- Náklady na pohyb jsou jedním ze způsobů signalizace clusteru Správce prostředků, že některé služby jsou dražší než ostatní. Další informace o nákladech na přesun najdete v [tomto článku](service-fabric-cluster-resource-manager-movement-cost.md) .

[Image1]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-cluster-layout-with-default-metrics.png
[Image2]:./media/service-fabric-cluster-resource-manager-metrics/Service-Fabric-Resource-Manager-Dynamic-Load-Reports.png
[Image3]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-metric-weights-impact.png
[Image4]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-global-vs-local-balancing.png
