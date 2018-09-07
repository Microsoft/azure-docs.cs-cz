---
title: Správa zatížení aplikace Azure Service Fabric pomocí metrik | Dokumentace Microsoftu
description: Další informace o tom, jak konfigurovat a spravovat využívání prostředků služeb pomocí metrik v Service Fabric.
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
ms.openlocfilehash: 7a7d3ad59d743287e5fe13c52c6c6a1a115d53f3
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44053308"
---
# <a name="managing-resource-consumption-and-load-in-service-fabric-with-metrics"></a>Správa spotřeby prostředků a zatížení v Service Fabric s metrikami
*Metriky* jsou prostředky, které vaše služby péče o a které jsou k dispozici uzly v clusteru. Metriky je všechno, co potřebujete ke správě za účelem zlepšení nebo sledovat výkon vašich služeb. Například může sledovat využití paměti vědět, pokud je vaše služba přetížená. Další možností použití je zjistit, zda služba přesunout jinde kde paměti je že menší, aby bylo možné dosáhnout lepšího výkonu omezené.

Příkladem metriky jsou věci, jako je využití paměti, disku a využití procesoru. Tyto metriky jsou fyzické metriky, prostředky, které odpovídají fyzické prostředky v uzlu, který je potřeba spravovat. Metriky lze také (a jsou běžně) logické metriky. Logické metriky jsou věci, jako je "MyWorkQueueDepth" nebo "MessagesToProcess" nebo "TotalRecords". Logické metriky jsou definované aplikací a nepřímo odpovídají některé využití fyzické prostředky. Logické metriky jsou běžné, protože může být obtížné měr a sestava spotřeby z fyzických prostředků na základě služby. Složitost měření a oznamování fyzické metriky je také proč Service Fabric nabízí některé výchozí metriky.

## <a name="default-metrics"></a>Výchozí metriky
Řekněme, že chcete začít pracovat, psaní a nasazování služby. V tuto chvíli nevíte jaký fyzické nebo logické prostředky, které používá. To je v pořádku! Service Fabric Cluster Resource Manager používá některé metriky výchozí, pokud jsou zadány žádné metriky. Jsou to tyto:

  - PrimaryCount - počet primární repliky na uzlu 
  - ReplicaCount - počet celkový počet replik stavových na uzlu
  - Počet – počet všech objektů služby (bezstavových a stavových) na uzel

| Metrika | Instance Bezstavová zatížení | Stavová zatížení sekundární | Stavová zatížení primární | Hmotnost |
| --- | --- | --- | --- | --- |
| PrimaryCount |0 |0 |1 |Vysoký |
| ReplicaCount |0 |1 |1 |Střednědobé používání |
| Počet |1 |1 |1 |Nízká |


Pro základní úlohy poskytují metriky výchozí vrazíme rozdělení práce v clusteru. V následujícím příkladu se podíváme, co se stane, když vytvoříme dvě služby a Spolehněte se na výchozí metriky pro službu Vyrovnávání. První služby je stavovou službu s tři oddíly a repliky cílového nastavení velikosti tři. Druhá služba je bezstavové služby s jedním oddílem a počet instancí tří.

Zde je, co získáte:

<center>
![Rozložení clusteru s výchozí metriky][Image1]
</center>

Poznamenat několik věcí:
  - Primární repliky pro stavové služby, které jsou distribuovány napříč několika uzly
  - Repliky pro stejného oddílu jsou na různých uzlech
  - Celkový počet primárek a sekundární databáze je distribuován v clusteru
  - Celkový počet objektů služeb se přidělují rovnoměrně na každém uzlu

Dobré!

Metriky výchozí skvěle fungovat jako spuštění. Ale metriky výchozí bude vykonávat pouze jste zatím. Příklad: co je pravděpodobnost, že jste rozdělení schéma prodlouží výsledků nemusíte zajistit dokonalou i využití všech oddílů? Co je pravděpodobné, že zatížení pro danou službu je konstantní v čase, nebo dokonce stejně napříč několika oddíly teď?

Můžete spustit s pouze výchozí metriky. Obvykle to ale znamená, že vaše využití clusteru je nižší a více nerovnoměrné, než byste chtěli. Je to proto, že výchozí metriky nejsou adaptivní a předpokládá, že všechno, co je ekvivalentní. Například primární, který je zaneprázdněný a, který není obou přispívat "1" do PrimaryCount metriky. V nejhorším případě používání pouze výchozí metriky můžete také přinést overscheduled uzly, což vede k problémům s výkonem. Pokud vás zajímá maximum vašeho clusteru a jak se vyhnout problémům s výkonem, budete muset použít vlastní metriky a generování sestav dynamického zatížení.

## <a name="custom-metrics"></a>Vlastní metriky
Metriky jsou nakonfigurované na základě jednotlivé s názvem service-instance při vytváření služby.

Jakékoliv metriky, má některé vlastnosti, které popisují ho: název, váhu a výchozí zatížení.

* Název metriky: Název metriky. Název metriky je jedinečný identifikátor pro metriku v rámci clusteru z pohledu správce prostředků.
* Váha: Váha metriky definuje, jak důležité tato metrika je relativní vůči jiné metriky pro tuto službu.
* Načítání výchozí: Výchozí zatížení je reprezentován odlišně v závislosti na tom, zda služba je Bezstavová nebo stavová.
  * Pro bezstavové služby jednotlivé metriky má jednu vlastnost s názvem DefaultLoad
  * Pro stavové služby, které definujete:
    * PrimaryDefaultLoad: Výchozí velikost tuto metriku tato služba využívá až bude primární
    * SecondaryDefaultLoad: Výchozí velikost tuto metriku tato služba využívá při je sekundární

> [!NOTE]
> Pokud definujete vlastní metriky a chcete _také_ použít výchozí nastavení, budete muset _explicitně_ přidat výchozí metriky zpět a definujte váhy a hodnoty pro ně. Je to proto, že je nutné definovat vztah mezi metriky výchozí a vlastní metriky. Například možná vás zajímá ConnectionCount nebo WorkQueueDepth více než primární distribuce. Ve výchozím nastavení je váha metriky PrimaryCount vysoká, tak budete chtít snížit na médium, když přidáte další metriky Ujistěte se, že se mají přednost.
>

### <a name="defining-metrics-for-your-service---an-example"></a>Definice metriky pro vaši službu – příklad
Řekněme, že má následující konfiguraci:

  - Vaše služba sestavy metriku s názvem "ConnectionCount"
  - Chcete také použít výchozí metriky 
  - Jste provedli některé měření a vědět, že obvykle primární repliky této služby trvá až 20 jednotek "ConnectionCount"
  - Sekundární databáze použít 5 jednotek "ConnectionCount"
  - Víte, že "ConnectionCount" je nejdůležitější metriky z hlediska správy výkonu této konkrétní služby
  - Chcete i s vyrovnáváním primární repliky. Vyrovnávání primární repliky je obecně vhodné, bez ohledu na to, co. To pomáhá zabránit ztrátě některé domény uzlu nebo chyby z vliv na většinu primární repliky společně. 
  - V opačném případě se dají výchozí metriky

Tady je kód, měli byste napsat k vytvoření služby pomocí této konfigurace metriky:

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
> Výše uvedených příkladech a zbývající část tohoto dokumentu popsat správu metriky na základě za názvem – služba. Je také možné definovat metriky pro vaše služby na službu _typ_ úroveň. Toho dosahuje tak, že zadáte v manifestech vaší služby. Definování typu metriky na úrovni se nedoporučuje z několika důvodů. Prvním důvodem je, že názvy metrik často specifických pro prostředí. Pokud není pevně smlouvy na místě, nemůže být jisti, že metrika "Jader" v jednom prostředí není v jiných "MiliCores" nebo "Jader". Pokud vaše metriky jsou definovány v manifestu je potřeba vytvořit nové manifesty pro každé prostředí. To obvykle vede k šíření různých manifesty s jen drobné rozdíly, což může vést k potížím správy.  
>
> Metrika zatížení jsou běžně přiřadit na základě jednotlivé s názvem service-instance. Řekněme například, že vytvoříte jednu instanci služby pro CustomerA, kdo má v plánu používat jenom lehce. Také Řekněme, že vytvoříte jiný pro CustomerB, který má větší zatížení. V takovém případě by pravděpodobně chcete upravit výchozí zatížení těchto služeb. Pokud máte metriky a zatížení prostřednictvím manifestů a chcete využít tento scénář, vyžaduje jinou aplikací a typů služeb pro každého zákazníka. Přepsat hodnoty definované v okamžiku vytvoření služby jsou definované v manifestu, který může použít k nastavení výchozí hodnoty pro konkrétní. Ale způsobem, který způsobí, že hodnoty deklarované v manifestech se nemá shodovat tyto služby ve skutečnosti se spustí s. To může vést k nejasnostem. 
>

Připomínáme: Pokud chcete použít výchozí nastavení, nemusíte vůbec dotknout shromažďování metrik nebo dělat nic zvláštního při vytváření služby. Metriky výchozí získat automaticky použity, když jsou definovány žádné jiné. 

Teď se podívejme každé z těchto nastavení podrobněji a mluvit o chování, které má vliv.

## <a name="load"></a>Načtení
Smyslem definice metriky je pro reprezentaci nějaké zatížení. *Zatížení* je, jak velká část danou metriku využívá některé instance služby nebo repliky v daném uzlu. Zatížení se dá nakonfigurovat v téměř libovolném bodě. Příklad:

  - Zatížení lze definovat při vytváření služby. Tento postup se nazývá _výchozí zatížení_.
  - Po vytvoření služby včetně výchozí zatížení pro služby může aktualizovat informace o metrikách. Tento postup se nazývá _aktualizace služby_. 
  - Zatížení pro daný oddíl můžete resetovat na výchozí hodnoty pro tuto službu. Tento postup se nazývá _resetování zatížení oddílu_.
  - Zatížení mohou být zaznamenány na základě služby objekt dynamicky za běhu. Tento postup se nazývá _reporting zatížení_. 
  
Všechny tyto strategií je možné v rámci stejné služby za dobu života. 

## <a name="default-load"></a>Načíst výchozí
*Výchozí zatížení* je jak velká část metrika využívá každý objekt služby (instance bezstavové nebo stavové repliky) této služby. Cluster Resource Manager používá toto číslo pro zatížení objekt služby, až do obdržení Další informace, jako je například sestav dynamického zatížení. Pro jednodušší služby je výchozí zatížení statické definice. Výchozí zatížení se nikdy neaktualizuje a používá se po dobu životnosti služby. Výchozí načte skvěle funguje pro plánování situacích, kdy určitá množství prostředků jsou vyhrazené pro různé úlohy a neměňte jednoduché kapacity.

> [!NOTE]
> Další informace o správě kapacity a definování kapacity pro uzly v clusteru, najdete v tématu [v tomto článku](service-fabric-cluster-resource-manager-cluster-description.md#capacity).
> 

Cluster Resource Manager umožňuje stavové služby můžete zadat jiné výchozí zatížení pro jejich Primárek a sekundární databáze. Bezstavové služby můžete zadat jenom jednu hodnotu, která se vztahuje na všechny instance. Pro stavové služby, výchozí zatížení pro primární a sekundární repliky se obvykle liší od repliky proveďte různé druhy práce v každé role. Například Primárek obvykle slouží operací čtení a zápisu a zpracovat většinu výpočetní zátěž, zatímco sekundární databáze nepodporují. Obvykle je vyšší než výchozí zatížení pro sekundární repliky výchozí zatížení pro primární repliku. Reálná čísla by měl záviset na vlastní měření.

## <a name="dynamic-load"></a>Dynamického zatížení
Řekněme, že běží služby nějakou dobu. Pomocí některé monitorování, jsme si všimli, že:

1. Některé oddíly nebo instance dané služby spotřebovávat více prostředků než ostatní
2. Některé služby mají zatížení, která se mění v čase.

Existuje mnoho věcí, které by mohly způsobit tyto druhy kolísání zatížení. Například různé služby nebo oddíly jsou spojeny s různými zákazníky s různými požadavky. Zatížení může také změnit, protože se liší množství práce, kterou služba nebude v průběhu dne. Bez ohledu na důvod obvykle neexistuje žádné jedno číslo, které můžete použít pro výchozí hodnotu. To platí zejména pokud chcete získat většinu využití z clusteru. Libovolná hodnota, kterou vyberete pro výchozí zatížení je nesprávné určitou dobu. Načte nesprávné výchozí výsledek v Cluster Resource Manager nad nebo pod přidělování prostředků. V důsledku toho máte uzly, které jsou nad nebo pod využít i v případě, že Cluster Resource Manager předpokládá, že jsou rovnoměrně clusteru. Načte výchozí jsou pořád dobré, protože poskytují nějaké informace pro počáteční umístění, ale nejsou kompletní scénář pro skutečné úlohy. K zachycení přesně se měnící požadavky na prostředky, Cluster Resource Manager umožňuje každého objektu služby se aktualizovat vlastní zatížení za běhu. Tomu se říká, generování sestav dynamického zatížení.

Sestav dynamického zatížení umožňují replik nebo instancí upravit jejich přidělení nebo hlášené zatížení metrik průběhu své životnosti. Repliku služby nebo instanci, která byla studené a pokud to veškerou práci by obvykle zprávu, že díky nízkým množství danou metriku. Zaneprázdněný replik nebo instancí zasílat zprávy, které využívají více.

Vytváření sestav zatížení na jednu replik nebo instancí umožňuje Cluster Resource Manageru pro změnu uspořádání objektů jednotlivých služeb v clusteru. Reorganizace služby pomáhá zajistit, že dostanou prostředky, které vyžadují. Zaneprázdněný služby získat efektivní "uvolnit" prostředky z jiných replik nebo instancí, které jsou v tuto chvíli studeného nebo provádějící méně práce.

V rámci Reliable Services kód pro načtení sestavy dynamicky vypadá takto:

Kód:

```csharp
this.Partition.ReportLoad(new List<LoadMetric> { new LoadMetric("CurrentConnectionCount", 1234), new LoadMetric("metric1", 42) });
```

Služba může podávat všechny metriky pro ni definována v okamžiku vytvoření. Pokud zatížení služby sestav metriky, který není konfigurován pro použití, Service Fabric ignoruje tuto sestavu. Pokud jsou hlášeny současně jiné metriky, které jsou platné, tyto zprávy jsou přijímány. Kód služby můžete měřit a nahlásit všechny metriky ví jak, a operátory lze zadat konfiguraci metrik použití bez nutnosti změny kódu služby. 

### <a name="updating-a-services-metric-configuration"></a>Aktualizuje se konfigurace metriky služby
Seznam metrik související se službou a vlastnosti tyto metriky můžete dynamicky aktualizovat, pokud služba je v provozu. To umožňuje experimentování a flexibilitu. Některé příklady kdy to je užitečné, jsou:

  - zakázání metriku s nevycházeli sestavy pro konkrétní službu
  - Opětovná konfigurace váhy metriky na základě požadované chování
  - povolení nové metriky až po kód již byl nasazen a ověřit prostřednictvím jiné mechanismy
  - Změna výchozí zatížení služby na základě vypozorovaného chování a využití

Hlavní rozhraní API pro změnu konfigurace metriky jsou `FabricClient.ServiceManagementClient.UpdateServiceAsync` v jazyce C# a `Update-ServiceFabricService` v prostředí PowerShell. Libovolné informace, které zadáte pomocí těchto rozhraní API nahradí stávající informace o metrikách pro službu okamžitě. 

## <a name="mixing-default-load-values-and-dynamic-load-reports"></a>Smíšené používání výchozích hodnot zatížení a sestav dynamického zatížení
Výchozí zatížení a dynamické zatížení lze pro stejnou službu. Když služba využívá výchozí zatížení a sestav dynamického zatížení, výchozí zatížení slouží jako odhad dokud zobrazení dynamické sestavy. Výchozí zatížení je dobré, protože Cluster Resource Manager nabízí něco pro práci s. Umožňuje načíst výchozí Cluster Resource Manager mají být umístěny objekty služby vhodné umístění při jejich vytváření. Pokud je k dispozici žádné informace o načtení výchozí, umístění služeb je efektivně náhodné. Při načítání sestavy obdržení později počáteční náhodných umístění je často nesprávné a má Cluster Resource Manageru pro přesun služeb.

Můžeme využít naše předchozí příklad a zjistěte, co se stane, když jsme některé vlastní metriky a generování sestav dynamického zatížení. V tomto příkladu použijeme jako příklad metriku "MemoryInMb".

> [!NOTE]
> Paměť je jedním z systémové metriky, která se dá Service Fabric [řízení prostředků](service-fabric-resource-governance.md), a vykazování sami je obvykle obtížné. Neočekáváme skutečně zprávy o využití paměti; Paměť je používána jako pomocný při seznamování s možností Cluster Resource Manager.
>

Pojďme předpokládají, že jsme původně vytvořili stavové služby pomocí následujícího příkazu:

Prostředí PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("MemoryInMb,High,21,11”,"PrimaryCount,Medium,1,0”,"ReplicaCount,Low,1,1”,"Count,Low,1,1”)
```

Připomínáme Tato syntaxe je ("MetricName MetricWeight, PrimaryDefaultLoad, SecondaryDefaultLoad").

Podívejme se, jaké jeden rozložení možné clusteru může vypadat:

<center>
![Rovnováha clusteru s výchozí a vlastní metriky][Image2]
</center>

Některé věci, které stojí za zmínku:

* Každý sekundární repliky v rámci oddílu může mít vlastní zatížení
* Celkově vypadat vyvážené metriky. O paměť, je poměr mezi maximální a minimální zatížení 1,75 (N3, je uzel s největší zatížení je nejmenší N2 a 28/16 = 1,75).

Existuje několik věcí, které musíme popisují:

* Co určuje, zda byla poměr 1,75 přiměřené nebo ne? Jak Cluster Resource Manageru znáte Pokud je to dostatečné, nebo pokud existuje více práce udělat?
* Pokud k tomu vyrovnávání dochází?
* Co znamená, že byl "Vysoká" váha paměti?

## <a name="metric-weights"></a>Metriky váhy
Sledování stejné metriky napříč různými službami je důležité. Globální zobrazení je co umožňuje Cluster Resource Manager ke sledování využití v clusteru, vyrovnání využití napříč uzly a ujistěte se, že uzly nenavazují přes kapacity. Služby, ale mohou mít různá zobrazení význam stejné metriky. Také v clusteru s mnoha metrik a velké množství služeb, nemusíte zajistit dokonalou vyvážené řešení neexistuje pro všechny metriky. Jak Cluster Resource Manager pracovat s těmito situacemi?

Metriky váhy povolit Cluster Resource Manageru k rozhodování o způsobu vyvážit clusteru, pokud neexistuje žádný ideální odpovědí. Metriky váhy také povolit Cluster Resource Manageru pro určité služby odlišně. Metriky můžete mít čtyři různé váhy úrovně: nula, nízká, střední a vysokou. Metrika s váhou nula přispívá nic, když zvažujete, zda věci vyvažují nebo ne. Ale jeho zatížení stále přispívají ke správě kapacity. Metriky s nulovou váhou jsou stále užitečné a často slouží jako součást služby chování a sledování výkonu. [Tento článek](service-fabric-diagnostics-event-generation-infra.md) poskytuje další informace o použití metrik pro monitorování a Diagnostika služeb. 

Skutečný dopad různé metriky váhy v clusteru je, že Cluster Resource Manageru generuje různá řešení. Metriky váhy řekněte Cluster Resource Manager, že některé metriky jsou důležitější než jiné. Když neexistuje ideální řešení Cluster Resource Manageru můžete preferovat řešení, která vyvážit vyšší vážený metriky lepší. Pokud služba předpokládá, že není důležitý, že jejich použití tuto metriku může najít imbalanced konkrétní metriky. To umožňuje další služby k získání rovnoměrnou distribuci přenosu některé metriky, které jsou důležité k němu.

Pojďme se podívat na příklad některých sestav zátěžových a jak různé metriky oceňuje výsledky v různých přidělení v clusteru. V tomto příkladu vidíme, že přepínání relativní váha metriky způsobí, že Cluster Resource Manageru k vytvoření různých uspořádání služeb.

<center>
![Příklad váha metriky a její vliv na řešení vyrovnávání][Image3]
</center>

V tomto příkladu existují čtyři různé služby, všechny sestavy různé hodnoty pro dvě různé metriky MetricA a MetricB. Všechny služby v jednom případě definovat MetricA je ten nejdůležitější (váha = vysoká) a MetricB jako důležitý (váha = nízká). V důsledku toho vidíme, že Cluster Resource Manager tak, aby MetricA lépe s vyrovnáváním než MetricB umístí služeb. "Lépe vyvážené" znamená, že MetricA má nižší má nižší než MetricB směrodatnou odchylku. V druhém případě můžeme obrátit metriky váhy. Cluster Resource Manageru v důsledku toho Zamění služby A a B přijít s přidělení, kde je MetricB lépe než MetricA vyvážené.

> [!NOTE]
> Metriky váhy určit, jak by měl vyvážit Cluster Resource Manager, ale při vyrovnávání nemělo stát. Další informace o vyrovnávání, podívejte se na [v tomto článku](service-fabric-cluster-resource-manager-balancing.md)
>

### <a name="global-metric-weights"></a>Globální váhy metriky
Řekněme, že ServiceA definuje MetricA jako váha vysokou a ServiceB nastaví váha MetricA nízké nebo nula. Co je skutečná váhu, která zakončí zvyknout?

Existuje více váhy, které jsou sledovány pro každý metriku. První váha je definováno pro metriku při vytvoření služby. Další váha je globální váhy, které je vypočítán automaticky. Cluster Resource Manager pomocí obou těchto typů důležitosti při vyhodnocování řešení. S ohledem obou váhy je důležité. To umožňuje Cluster Resource Manageru k vyrovnání jednotlivé služby podle vlastní priority a také se ujistěte, že je správně přidělen clusteru jako celek.

Co by mohlo dojít, pokud Cluster Resource Manager nebyl záleží globální a místní zůstatek? Dobře je snadné vytvořit řešení, která se globálně vyvažují, ale který má za následek nízký prostředků zůstatku pro jednotlivé služby. V následujícím příkladu Pojďme podívejte se na službu nakonfigurovanou jenom výchozí metriky a naleznete v tématu co se stane, když se považuje za pouze globální vyrovnávání:

<center>
![Dopad globální pouze řešení][Image4]
</center>

V horním příkladu pouze podle globální vyrovnávání jsou skutečně rovnoměrně clusteru jako celek. Všechny uzly mají stejný počet primárek a stejné číslo celkový počet replik. Ale pokud se podíváte na skutečný dopad toto přidělení není tak dobré: ztráty libovolný uzel ovlivňuje konkrétní pracovní postup nepřiměřeně, protože má všechny jeho primárek. Například pokud první uzel selže tři primárek pro tři různé oddíly služby kruh by všechny ztratí. Naopak trojúhelník a šestiúhelník služby mají jejich oddíly dojít ke ztrátě replik. To způsobí, že žádné přerušení, než by bylo nutné obnovit dolů repliky.

V příkladu dole distribuoval Cluster Resource Manager repliky podle globální a za službou Vyrovnávání. Při výpočtu skóre řešení poskytuje většinu váhy globální řešení a (Konfigurovat) část do jednotlivých služeb. Globální vyrovnávání pro metriky se počítají podle průměr metriky váhu z každé služby. Každá služba je vyrovnává podle nastavených vlastní definované metriky váhy. Tím se zajistí, že služby vyvažují v rámci samotné podle vlastních potřeb. V důsledku toho v případě selhání selhání stejné prvního uzlu se distribuuje mezi všechny oddíly všechny služby. Dopad na jednotlivé je stejný.

## <a name="next-steps"></a>Další postup
- Další informace o konfiguraci služby [Další informace o konfiguraci služby](service-fabric-cluster-resource-manager-configure-services.md)(service-fabric-cluster-resource-manager-configure-services.md)
- Definování defragmentaci metrik je jeden způsob, jak konsolidovat zatížení na uzlech, místo aby rozložil ho navýšení kapacity. Další informace o konfiguraci defragmentace, najdete v tématu [v tomto článku](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
- Přečtěte si o tom, jak Cluster Resource Manager spravuje a vyrovnává zatížení v clusteru, přečtěte si článek na [Vyrovnávání zatížení](service-fabric-cluster-resource-manager-balancing.md)
- Začít od začátku a [Úvod do Service Fabric Cluster Resource Manager](service-fabric-cluster-resource-manager-introduction.md)
- Náklady na přesunutí je jeden způsob, jak signalizace pro Cluster Resource Manager, že určité služby jsou dražší než jiné přesunout. Další informace o náklady na přesunutí, najdete v tématu [v tomto článku](service-fabric-cluster-resource-manager-movement-cost.md)

[Image1]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-cluster-layout-with-default-metrics.png
[Image2]:./media/service-fabric-cluster-resource-manager-metrics/Service-Fabric-Resource-Manager-Dynamic-Load-Reports.png
[Image3]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-metric-weights-impact.png
[Image4]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-global-vs-local-balancing.png
