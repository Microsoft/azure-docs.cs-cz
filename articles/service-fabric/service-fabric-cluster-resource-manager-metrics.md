---
title: Správa Azure mikroslužbu zatížení pomocí metriky | Microsoft Docs
description: Další informace o tom, jak konfigurovat a používat ke správě služby spotřeby prostředků metriky v Service Fabric.
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: 0d622ea6-a7c7-4bef-886b-06e6b85a97fb
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 26dffa7e57da2ef383f078c7c5cbb7b9664923ee
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="managing-resource-consumption-and-load-in-service-fabric-with-metrics"></a>Správa spotřeby prostředků a zatížení v Service Fabric pomocí metrik
*Metriky* jsou prostředky, které vaše služby péče o a které jsou k dispozici na uzlech v clusteru. Metrika je všechno, co chcete spravovat pro zlepšení nebo sledovat výkon vašich služeb. Například je může sledovat využití paměti vědět, pokud je přetížena služby. Použití jiné je zjistit, jestli může služba přesunout jinde kde paměti je že menší omezené, aby bylo možné získat lepší výkon.

Příklady metriky jsou věci jako využití paměti, disku a procesoru. Tyto metriky jsou fyzické metriky, prostředky, které odpovídají fyzické prostředky na uzlu, který je třeba spravovat. Metriky může být také (a často jsou) logické metriky. Logické metriky věci jako "MyWorkQueueDepth" nebo "MessagesToProcess" nebo "TotalRecords". Logické metriky jsou definované aplikací a nepřímo odpovídají některé spotřeby fyzických prostředků. Vzhledem k tomu může být obtížné měr a sestava spotřeby fyzické prostředky na základě služby jsou běžné logické metriky. Složitost měření a vytváření sestav vlastní fyzické metriky je také proč Service Fabric nabízí několik výchozích metrik.

## <a name="default-metrics"></a>Výchozích metrik
Řekněme, že chcete začít pracovat, zápisu a nasazení služby. V tomto okamžiku nevíte fyzické nebo logické prostředky spotřebuje. Není to! Správce prostředků clusteru Service Fabric používá několik výchozích metrik, pokud nejsou zadány žádné jiné metriky. Jsou to tyto:

  - PrimaryCount - počet primární repliky na uzlu 
  - ReplicaCount - počet celkový stavová repliky na uzlu
  - Počet - počet všech objektů služby (bezstavové a stavové) na uzlu

| Metrika | Bezstavové Instance zatížení | Stavová zatížení sekundární | Stavová zatížení primární |
| --- | --- | --- | --- |
| PrimaryCount |0 |0 |1 |
| ReplicaCount |0 |1 |1 |
| Počet |1 |1 |1 |

Pro základní úlohy výchozích metrik zadejte dostatečnou distribuční práce v clusteru. V následujícím příkladu se podíváme, co se stane, když jsme vytvořit dvě služby a spoléhají na výchozích metrik pro vyrovnávání. Repliku cíl nastavit velikost tří první službě je stavové služby s tři oddíly. Druhý služba je bezstavové služby s jedním oddílem a počet instancí tří.

Tady je můžete získat:

<center>
![Cluster rozložení s výchozím nastavení metriky][Image1]
</center>

Některé věci Všimněte si:
  - Primární repliky pro stavové služby, které jsou rozmístěny v několika uzly
  - Repliky pro stejného oddílu jsou na různých uzlech
  - Celkový počet základní barvy a sekundárních databází se distribuuje v clusteru
  - Celkový počet objektů služby, které jsou rovnoměrně přiděleny na každém uzlu

Dobré!

Výchozích metrik fungovat skvěle jako spuštění. Ale výchozích metrik bude pouze provádění můžete dosavadní práce. Příklad: co je pravděpodobnost, že jste vytváření oddílů scheme vybráno výsledky perfektně i využití tím všechny oddíly? Co je pravděpodobné, že je zatížení pro danou službu, konstantní v čase, nebo dokonce stejně napříč více oddílů nyní?

Můžete spustit s právě výchozích metrik. Obvykle to však znamená, že využití vašeho clusteru je menší a více nerovnoměrné, než byste chtěli. Je to proto, výchozích metrik nejsou adaptivní a předpokládá, že všechno, co je ekvivalentní. Například primární, který je zaneprázdněn a ten, který není obou přispívat metriky PrimaryCount "1". V nejhorším případě pomocí výchozích metrik může také způsobit overscheduled uzly vést k problémům s výkonem. Pokud vás zajímá maximální využití vašeho clusteru a vyhnout problémům s výkonem, budete muset použít vlastní metriky a vytváření sestav dynamického zatížení.

## <a name="custom-metrics"></a>Vlastní metriky
Metriky jsou nakonfigurované na základě jednotlivých s názvem service instancí při vytváření služby.

Všechny metrika se některé vlastnosti, které popisují ho: název, váhu a výchozí zatížení.

* Název metriky: Název metriky. Název metriky je jedinečný identifikátor pro metriku v rámci clusteru z hlediska správce prostředků.
* Váha: Váha metriky definuje, jak důležité tato metrika je relativní vzhledem k jiné metriky pro tuto službu.
* Výchozí zatížení: Výchozí zatížení je reprezentována odlišně v závislosti na tom, zda je služba bezstavové nebo stateful.
  * Pro bezstavové služby jednotlivé metriky má jednu vlastnost s názvem DefaultLoad
  * Pro stavové služby můžete definovat:
    * PrimaryDefaultLoad: Výchozí velikost tato metrika tato služba využívá Pokud je primární
    * SecondaryDefaultLoad: Výchozí velikost tato metrika tato služba využívá případě, že je sekundární

> [!NOTE]
> Pokud definujete vlastní metriky a vy chcete _také_ použití výchozích metrik, budete muset _explicitně_ přidání výchozích metrik zpět a zadejte váhu a hodnoty pro ně. Je to proto, že je nutné definovat vztah mezi výchozích metrik a vaše vlastní metriky. Například možná se zajímáte o ConnectionCount nebo WorkQueueDepth víc než primární distribuce. Ve výchozím nastavení je váha metriky PrimaryCount horní, tak chcete snížit na střední při přidání dalších metrik Ujistěte se, že se mají přednost před.
>

### <a name="defining-metrics-for-your-service---an-example"></a>Definice metrik služby – příklad
Může se stát, že chcete následující konfiguraci:

  - Sestavy služby metriky s názvem "ConnectionCount"
  - Také můžete chtít použít výchozích metrik 
  - Jste provádí některé měření a vědět, že normálně primární repliky této služby zabírají 20 jednotek "ConnectionCount"
  - Sekundární databáze použijte 5 jednotky "ConnectionCount"
  - Víte, že "ConnectionCount" je nejdůležitější metriky z hlediska Správa výkonu této konkrétní služby
  - Stále chcete vyrovnáváním primární repliky. Vyrovnávání primární repliky obecně je vhodné bez ohledu na to, co. To pomáhá zabránit ztrátě některé domény uzlu nebo selhání většiny primární repliky společně s jeho, které mají vliv. 
  - Jinak jsou výchozích metrik

Tady je kód, který by zápis k vytvoření služby pomocí této konfigurace metriky:

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

Prostředí PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("ConnectionCount,High,20,5”,"PrimaryCount,Medium,1,0”,"ReplicaCount,Low,1,1”,"Count,Low,1,1”)
```

> [!NOTE]
> Příklady uvedené nahoře a zbytek tohoto dokumentu popisují správu metriky na základě za-s názvem service. Je také možné definovat metriky pro vaše služby ve službě _typ_ úroveň. To se provádí zadáním v manifestech vaší služby. Definování typu metriky na úrovni se nedoporučuje z několika důvodů. Prvním důvodem je, že metriky názvy jsou často konkrétní prostředí. Pokud je na místě závazné kontrakt, nemůže být jisti, že metrika "Jader" v jednom prostředí není v jiných "MiliCores" nebo "Jader". Pokud vaše metriky jsou definovány v manifestu je třeba vytvořit nové manifesty za prostředí. To obvykle vede k velkému množství různých manifesty s pouze drobné rozdíly, které může vést k potížím správy.  
>
> Metriky zatížení jsou běžně přiřadit na základě jednotlivých s názvem service instancí. Řekněme například, že vytvoříte jednu instanci služby pro CustomerA, kdo plány ji použít pouze lehkým. Také se stát, že vytvoříte jiné pro CustomerB, který má větší zatížení. V takovém případě by pravděpodobně chcete upravit výchozí zatížení pro tyto služby. Pokud máte metriky a zatížením definované prostřednictvím manifesty a vy chcete pro podporu tohoto scénáře, vyžaduje pro každého zákazníka jiné aplikace a typů služeb. Hodnot fronty definovaných v okamžiku vytvoření služby přepíšou nastavení definované v manifestu, pomocí které lze nastavit konkrétní výchozí hodnoty. Ale učinit způsobí, že hodnoty deklarované v manifestech k neshodují s hodnotami služba bude spuštěna ve skutečnosti s. To může vést k nejasnostem. 
>

Jako připomenutí: Pokud chcete použít výchozí nastavení, nemusíte touch kolekci metriky na všech nebo provádět žádné zvláštní při vytváření služby. Výchozích metrik získat použít automaticky, když jsou definovány žádné jiné. 

Nyní se Vraťme se prostřednictvím každé z těchto nastavení podrobněji a mluvit o chování, které má vliv.

## <a name="load"></a>Načtení
Celý bod definice metrik je představují některé zatížení. *Zatížení* je kolik danou metriku obsazením některé instance služby nebo repliky v daném uzlu. Zatížení se dá nakonfigurovat téměř libovolném okamžiku. Příklad:

  - Zatížení se dá definovat při vytváření služby. To se označuje jako _výchozí zatížení_.
  - Informace o metrikách, včetně výchozí zatížení pro služby můžete aktualizovat po vytvoření služby. To se označuje jako _aktualizace služby_. 
  - Načítání v daném oddílu můžete resetovat na výchozí hodnoty pro tuto službu. To se označuje jako _obnovení zatížení oddílu_.
  - Zatížení mohou být zaznamenány na za objekt individuálních dynamicky za běhu. To se označuje jako _reporting zatížení_. 
  
Všechny tyto strategie lze použít v rámci stejné služby přes celé jeho životnosti. 

## <a name="default-load"></a>Výchozí zatížení
*Výchozí zatížení* je kolik metriky využívá každý objekt služby (bezstavové instance nebo stavová repliky) této služby. Správce prostředků clusteru používá toto číslo pro zatížení objektu služby, dokud neobdrží jiné informace, jako je například sestavy dynamického zatížení. Pro jednodušší služby je výchozí zatížení definici statické. Výchozí zatížení nikdy se aktualizuje a používá se po dobu jeho existence služby. Výchozí načte funguje dobře pro jednoduché plánování scénáře, kde určité množství prostředků jsou vyhrazené pro různé zatížení a neměňte kapacity.

> [!NOTE]
> Další informace o řízení kapacity a definování kapacity pro uzly v clusteru, najdete v tématu [v tomto článku](service-fabric-cluster-resource-manager-cluster-description.md#capacity).
> 

Správce prostředků clusteru umožňuje stavové služby zadat jiného výchozího zatížení pro jejich základní barvy a sekundární repliky. Bezstavové služby můžete zadat jenom jednu hodnotu, která se vztahuje na všechny instance. Pro stavové služby, výchozí zatížení pro primární a sekundární repliky se obvykle liší, vzhledem k tomu, že repliky proveďte různé druhy práce v každé role. Například základní barvy obvykle sloužit čtení a zápisu a zpracovat většinu výpočetní zatížení, když sekundární databáze nepodporují. Obvykle je vyšší než výchozí zatížení pro sekundární repliky výchozí zatížení pro primární repliku. Reálná čísla by měla závisí na svých vlastních hodnot.

## <a name="dynamic-load"></a>Dynamické načtení
Řekněme, že jste byl spuštěn služby nějakou dobu. S některá monitorování jste si všimli který:

1. Některé oddíly nebo instance dané služby spotřebují více prostředků než jiné
2. Některé služby mají zatížení, který se liší v čase.

Je velké množství věcí, které by mohly způsobit tyto typy kolísání zátěže. Například různé služby nebo oddíly jsou přidruženy různých zákazníků, jejichž jiné požadavky. Zatížení může také změnit, protože se liší množství práce, kterou služba nemá v průběhu dne. Bez ohledu na důvod, proč se obvykle neexistuje, můžete použít pro výchozí číslo jeden. To platí hlavně pokud chcete získat většina využití mimo cluster. Libovolná hodnota, kterou vyberete pro výchozí zatížení je nesprávný určitou dobu. Nesprávný výchozí načte výsledek v clusteru správce prostředků nad nebo pod přidělování prostředků. V důsledku toho máte uzlů, které jsou nad nebo pod využité to i v případě, že správce prostředků clusteru předpokládá, že jsou rovnoměrně clusteru. Výchozí zatížení jsou stále vhodné, protože poskytují některé informace pro počáteční umístění, ale nejsou kompletní scénáře pro skutečné úlohy. Přesně zachytit změny požadavků na prostředky, Cluster Resource Manager umožňuje každý objekt služby aktualizovat vlastní zatížení za běhu. Tomu se říká reporting dynamického zatížení.

Dynamické načtení sestav povolit replik nebo instancí upravit jejich přidělení/hlášené zatížení metrik průběhu své životnosti. Replika služby nebo instanci, která se uloží málo používaná data a není to veškerou práci, by obvykle sestavy, aby používalo nízkou množství danou metriku. Zaneprázdněný replik nebo instancí by hlásit, že používají více.

Vytváření sestav zatížení za replik nebo instancí umožňuje správci prostředků clusteru reorganizace objekty jednotlivé služby v clusteru. Reorganizace služby pomáhá zajistit, že se získají z prostředků, které vyžadují. Zaneprázdněný služby efektivně get "uvolnit" prostředky z dalších replik nebo instancí, které jsou aktuálně studené nebo méně pracuje.

V rámci spolehlivé služby kód, který sestavu zatížení dynamicky vypadá takto:

Kód:

```csharp
this.Partition.ReportLoad(new List<LoadMetric> { new LoadMetric("CurrentConnectionCount", 1234), new LoadMetric("metric1", 42) });
```

Služba může hlásit na žádném z metriky definované pro něj v okamžiku vytvoření. Pokud zatížení sestavy služby pro metriku, který není nakonfigurovaný na použití, Service Fabric ignoruje této sestavy. Pokud existují další metriky hlášené ve stejnou dobu, které jsou platné, jsou přijaty tyto sestavy. Kódu služby můžete měřit a sestavy všechny metriky věděl, že může postupy, a operátory můžete zadat konfiguraci metriky používat bez nutnosti změny kódu služby. 

### <a name="updating-a-services-metric-configuration"></a>Aktualizuje se konfigurace metriky služby
Seznam metriky spojené s touto službou a vlastnosti tyto metriky lze dynamicky aktualizovat při provozu služby. To umožňuje experimentování a flexibilitu. Některé příklady Pokud to je užitečné, jsou:

  - zakázání metrika s buggy zprávy pro konkrétní službu
  - Změna konfigurace váhu metriky na základě požadovaného chování
  - povolení nové metrika až po kód již byl nasazen a ověřit pomocí jiných mechanismů
  - Změna výchozí zatížení pro službu na základě pozorovaných chování a spotřeba

Hlavní rozhraní API pro změnu metriky konfigurace jsou `FabricClient.ServiceManagementClient.UpdateServiceAsync` v jazyce C# a `Update-ServiceFabricService` v prostředí PowerShell. Ať informace, které zadáte pomocí těchto rozhraní API nahradí stávající informace o metrikách pro službu okamžitě. 

## <a name="mixing-default-load-values-and-dynamic-load-reports"></a>Kombinování výchozí hodnoty zatížení a dynamické načtení sestav
Výchozí zatížení a dynamické zatížení lze použít pro stejnou službu. Když služba využívá výchozí zatížení a dynamické načtení sestavy, výchozí zatížení slouží jako odhad dokud dynamické sestavy, na které se objeví. Výchozí zatížení je vhodný, protože nabízí správce prostředků clusteru něco na práci s. Výchozí zatížení umožňuje správci prostředků clusteru umístit objekty služby ve funkčním umístěních, při jejich vytváření. Pokud je zadaný žádný výchozí informace o zatížení, je efektivně náhodný umístění služeb. Při načítání sestavy příchodu později počáteční náhodných umístění je často nesprávný a správce prostředků clusteru musí přesunout služby.

Umožňuje trvat naše předchozí příklad a zjistěte, co se stane, když přidáme některé vlastní metriky a vytváření sestav dynamického zatížení. V tomto příkladu používáme jako metriku příklad "MemoryInMb".

> [!NOTE]
> Paměť je jedním z systému metriky, které můžete Service Fabric [prostředek řídí](service-fabric-resource-governance.md), a reporting sami je obvykle obtížná. Nemáte Očekáváme, že ve skutečnosti nahlásit na spotřebu paměti; Paměť se používá sem jako pomůcku při získání informací o možnosti Správce prostředků clusteru.
>

Pojďme předpokládá, že jsme původně vytvořili stavové služby pomocí následujícího příkazu:

Prostředí PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("MemoryInMb,High,21,11”,"PrimaryCount,Medium,1,0”,"ReplicaCount,Low,1,1”,"Count,Low,1,1”)
```

Připomínáme je tato syntaxe (MetricName, MetricWeight, PrimaryDefaultLoad, SecondaryDefaultLoad").

Podívejme se, co jeden rozložení možné clusteru může vypadat podobně jako:

<center>
![Cluster rovnováha s výchozí i vlastní metriky][Image2]
</center>

Některé věci, které jsou vhodné poznamenat:

* Každý sekundární repliky v rámci oddílu může mít vlastní zatížení
* Celkové vypadat vyrovnáváním metriky. O paměť, poměr mezi maximální a minimální zatížení je 1,75 (uzel s největším zatížení je N3, je nejmenší N2 a 28/16 = 1,75).

Existují některé věci, které musíme vysvětlují:

* Co je určit, zda byla poměr 1,75 přiměřené nebo ne? Jak správce prostředků clusteru, že pokud to je výhodné dostatečně, nebo pokud je další práci udělat?
* Pokud vyrovnávání dojít?
* Co to znamená, že byl "Základní" váha paměti?

## <a name="metric-weights"></a>Metriky váhu
Sledování stejné metriky mezi různé služby je důležité. Tuto globální zobrazení je co umožňuje správci prostředků clusteru sledovat spotřebu v clusteru, vyvážit spotřeba mezi uzly a zajistit uzly si projít kapacity. Služby však mohou mít různá zobrazení na význam stejné metriky. V clusteru s mnoha metriky a velké množství služeb, navíc perfektně vyrovnáváním řešení neexistuje pro všechny metriky. Jak správci prostředků clusteru pracovat s těmito situacemi?

Metriky váhu povolit správce prostředků clusteru k rozhodování o způsobu vyrovnávat clusteru při volaná úplně bez chyby. Metriky váhu také povolit správce prostředků clusteru k určitým službám vyrovnávání jinak. Metriky lze vytvořit čtyři úrovně různých váhy: nula, nízké, střední a vysokou. Metrika s váhou nula přispívá nic při určování, zda jsou vyváženy věcí, nebo ne. Jejím zatížení však stále přispívat k řízení kapacity. Metriky s nulovou tloušťkou jsou stále užitečné a často používají jako součást chování služby a sledování výkonu. [Tento článek](service-fabric-diagnostics-event-generation-infra.md) poskytuje další informace o použití metriky pro monitorování a Diagnostika vašich služeb. 

Skutečné dopad jiné metriky váhu v clusteru je, že správce prostředků clusteru generuje různá řešení. Metriky váhu řekněte správci prostředků clusteru, že určité metriky jsou důležitější než jiné. Pokud neexistuje ideální řešení správce prostředků clusteru můžete přednost řešení, které vyvážit vyšší lepší vyvážené metriky. Pokud služba rootu předpokládá, že konkrétní metrika je důležitý, že jejich použití tohoto Metrika může najít imbalanced. To umožňuje získat rovnoměrné rozdělení některé metriku, je potřeba ho jiné služby.

Podívejme se na příklad některých sestav zatížení a jak budou různí metrika provede výsledky v různých přidělení v clusteru. V tomto příkladu vidíte, že přepnutí relativní váhu metriky způsobí, že správce prostředků clusteru vytvořit různé uspořádání služeb.

<center>
![Příklad váha metriky a její vliv na řešení vyrovnávání][Image3]
</center>

V tomto příkladu jsou čtyři různé služby, všechny sestavy různé hodnoty pro dva různé metriky, MetricA a MetricB. V jednom případě definovat všechny služby MetricA je nejdůležitějším (váhy = vysoce) a MetricB jako důležitý (váhy = nízká). V důsledku toho vidíte, že správce prostředků clusteru tak, aby MetricA jsou rovnoměrně lépe než MetricB umístí služeb. "Lépe vyrovnáváním" znamená, že MetricA má nižší má nižší směrodatnou odchylku než MetricB. V druhém případě jsme obrátíte metriky váhu. V důsledku toho prohození správce prostředků clusteru služby A a B přijít s přidělení, kde je MetricB lépe než MetricA vyrovnáváním.

> [!NOTE]
> Metriky váhu určit, jak by měla vyvážit správce prostředků clusteru, ale když vyrovnávání došlo k neočekávané chybě. Další informace o vyrovnávání, podívejte se na [v tomto článku](service-fabric-cluster-resource-manager-balancing.md)
>

### <a name="global-metric-weights"></a>Globální metriky váhu
Řekněme, že ServiceA definuje MetricA jako vážené vysokou a váhu MetricA ServiceB nastaví nízkou nebo nula. Co je skutečný váhy, který končí získávání použít?

Existuje více váhu sledovaných pro každý metriku. První váhy je definováno pro metriku při vytváření služby. Další váhu je globální váhy, které se počítá automaticky. Správce prostředků clusteru používá obě tyto váhy při vyhodnocování řešení. Vezme v úvahu oba váhu je důležité. To umožňuje Správce prostředků clusteru, aby vyvážit každé služby podle vlastní priority a také se ujistěte, že je správně přidělen clusteru jako celek.

Co by mohlo dojít, pokud správce prostředků clusteru nebyl vědět o globální a místní vyrovnávání? Dobře je snadné vytvořit řešení globálně vyrovnáváním, ale důsledkem toho rovnováze nízký prostředků pro jednotlivé služby. V následujícím příkladu umožňuje vyhledat služba nakonfigurovaná pomocí právě výchozích metrik a najdete, co se stane, když je považován za pouze globální vyrovnávání:

<center>
![Dopad globální pouze řešení][Image4]
</center>

V horní příkladu podle pouze globální vyrovnávání je skutečně vyvážit clusteru jako celek. Všechny uzly mají stejný počet základní barvy a stejné číslo celkový repliky. Ale pokud se podíváte na konkrétní dopad toto přidělení není proto vhodné: ztrátě libovolného uzlu ovlivňuje určité zatížení nepřiměřeně, protože trvá se všechny jeho základní barvy. Například pokud první uzel selže tři základní barvy pro tři různé oddíly kruh služby by všechny ztratí. Naopak služby trojúhelníček a šestiúhelník mít jejich oddíly ztratit repliku. To způsobí, že bez přerušení, než bylo nutné obnovit dolů repliky.

V příkladu dolní správce prostředků clusteru byla distribuována repliky podle globální a za službou Vyrovnávání. Při výpočtu skóre řešení poskytuje nejvíc váhu globální řešení a část (Konfigurovat) pro jednotlivé služby. Globální vyrovnávání pro metriku je vypočítáváno na průměr metriky váhu z jednotlivých služeb. Každá služba jsou rovnoměrně podle vlastní definované metriky váhu. Tím se zajistí, že služby jsou vyváženy v rámci sami podle vlastních potřeb. Výsledkem je Pokud stejné prvního uzlu selže selhání je distribuován pro všechny oddíly všech služeb. Dopad na každý je stejný.

## <a name="next-steps"></a>Další postup
- Další informace o konfiguraci služby [Další informace o konfiguraci služby](service-fabric-cluster-resource-manager-configure-services.md)(service-fabric-cluster-resource-manager-configure-services.md)
- Definování defragmentace metriky je jeden způsob, jak konsolidovat zatížení na uzlech místo rozšíří ho. Naučte se konfigurovat defragmentace, najdete v tématu [v tomto článku](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
- Chcete-li zjistit, o tom, jak správce prostředků clusteru spravuje a vyrovnává zatížení v clusteru, podívejte se na článek na [Vyrovnávání zatížení](service-fabric-cluster-resource-manager-balancing.md)
- Začít od začátku a [získejte Úvod do Service Fabric clusteru správce prostředků](service-fabric-cluster-resource-manager-introduction.md)
- Náklady na přesunutí je jeden ze způsobů signalizace správce prostředků clusteru, že jsou dražší než jiné přesunout určité služby. Další informace o náklady na přesunutí, najdete v tématu [v tomto článku](service-fabric-cluster-resource-manager-movement-cost.md)

[Image1]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-cluster-layout-with-default-metrics.png
[Image2]:./media/service-fabric-cluster-resource-manager-metrics/Service-Fabric-Resource-Manager-Dynamic-Load-Reports.png
[Image3]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-metric-weights-impact.png
[Image4]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-global-vs-local-balancing.png
