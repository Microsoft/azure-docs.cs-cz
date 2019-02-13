---
title: Sledování stavu v Service Fabric | Dokumentace Microsoftu
description: Úvod do Azure Service Fabric stav monitorování modelu, který poskytuje monitorování clusteru a jeho aplikací a služeb.
services: service-fabric
documentationcenter: .net
author: oanapl
manager: timlt
editor: ''
ms.assetid: 1d979210-b1eb-4022-be24-799fd9d8e003
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2018
ms.author: oanapl
ms.openlocfilehash: 87081398e844f1e2b085a7e12c2b7aafce330ec9
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56193760"
---
# <a name="introduction-to-service-fabric-health-monitoring"></a>Úvod do monitorování stavu Service Fabric
Azure Service Fabric představuje stav modelu, který poskytuje bohaté možnosti, flexibilní a rozšiřitelné stav vyhodnocení a vytváření sestav. Model umožňuje téměř v reálném čase, monitorování stavu clusteru a služeb spuštěných v ní. Můžete snadno získat informace o stavu a opravte potenciální problémy před jejich nepostupovala dále a způsobit, že masivní výpadků. V typické modelu služby Odeslat sestavy vycházející ze svých místních zobrazeních a, informace se shromažďují poskytnout celkový cluster úrovně zobrazení.

Komponenty Service Fabric pomocí tohoto modelu bohaté stavu hlásit jejich aktuální stav. Můžete použít stejný mechanismus stav sestavy z vašich aplikací. Pokud Investujete do sestav stavu vysoce kvalitní, který zachytí vlastní podmínky můžete zjišťovat a mnohem snadněji řešení problémů pro spuštěné aplikaci.

> [!NOTE]
> Začali jsme stavu subsystému potřebu pro monitorované upgrady. Service Fabric nabízí monitorované upgrady aplikací a clusteru, které zajistí úplné dostupnost, bez výpadků a minimální bez zásahu uživatele. K dosažení těchto cílů, upgradu kontroluje stav na základě nakonfigurovaných zásad upgradu. Upgradu můžete pokračovat pouze v případě, že stav respektuje požadované prahové hodnoty. V opačném případě upgrade je buď automaticky vrátit zpět nebo pozastavena a poskytuje správcům možnost opravit problémy. Další informace o upgradech aplikací najdete v tématu [v tomto článku](service-fabric-application-upgrade.md).
> 
> 

## <a name="health-store"></a>Úložiště stavu
Úložiště stavu uchovává zdravotních informací o entitách v clusteru pro snadné načítání a vyhodnocení. Je implementován jako Service Fabric trvalé stavové služby k zajištění vysoké dostupnosti a škálovatelnosti. Úložišti health je součástí **fabric: / System** aplikace a je dostupné v případě, že cluster běží a spuštěna.

## <a name="health-entities-and-hierarchy"></a>Stav entity a hierarchie
Stav entity, které jsou uspořádány do logických hierarchie, která zaznamenává interakce a závislosti mezi různými entitami. Health store se automaticky vytvoří stavu entity a hierarchie, které jsou založeny na sestavách přijatých z komponenty Service Fabric.

Stav entity, které zrcadlí entitami služby Service Fabric. (Například **stavu aplikace entity** odpovídá instance aplikace nasazené v clusteru, zatímco **stavu uzlu entity** odpovídá uzlu clusteru Service Fabric.) Hierarchie stavu zaznamenává interakce systémových entit a slouží jako základ pro vyhodnocování pokročilé stavu. Informace o klíčových konceptech Service Fabric [Service Fabric – technický přehled](service-fabric-technical-overview.md). Další informace o aplikaci najdete v tématu [aplikačním modelem Service Fabric](service-fabric-application-model.md).

Stav entity a hierarchie povolit cluster a aplikace účinně hlásí, ladit a monitorovat. Health model poskytuje přesný, *detailní* vyjádření stavu mnoha přesunutí částí v clusteru.

![Stav entity.][1]
Stav entity, uspořádány v hierarchii podle vztahů nadřazenosti a podřízenosti.

[1]: ./media/service-fabric-health-introduction/servicefabric-health-hierarchy.png

Stav entity, které jsou:

* **Cluster**. Reprezentuje stav clusteru Service Fabric. Sestavy stavu clusteru popisují podmínky, které ovlivňují celý cluster. Tyto podmínky ovlivnit více entit v clusteru nebo samotného clusteru. Na základě podmínky, osoby podávající hlášení nelze zúžit problém na jeden nebo více podřízených není v pořádku. Mezi příklady patří brain clusteru rozdělení kvůli problémům se sítí rozdělení do oddílů nebo komunikace.
* **Uzel**. Reprezentuje stav uzlu Service Fabricu. Sestavy stavu uzlu popisují podmínky, které ovlivňují funkčnost uzlu. Obvykle ovlivňují všechny nasazené entity v něm spuštěný. Mezi příklady patří uzel volné místo na disku (nebo jiné vlastnosti celého systému, jako je paměť, připojení) a pokud je uzel dolů. Uzlu entity je určený podle názvu uzlu (řetězec).
* **Aplikace**. Reprezentuje stav instance aplikace spuštěné v clusteru. Sestav o stavu aplikace popisují podmínky, které mají vliv celkový stav aplikace. Nelze se zúží a jednotlivých podřízených (služby nebo nasazených aplikací). Mezi příklady patří na začátku do konce interakci mezi různými službami v aplikaci. Aplikace entity je určený podle názvu aplikace (URI).
* **Služba**. Reprezentuje stav služby spuštěné v clusteru. Sestav o stavu služby popisují podmínky, které mají vliv celkový stav služby. Problém není v pořádku, oddílu nebo repliku nelze zúžit osoby podávající hlášení. Mezi příklady patří service konfiguraci (například port nebo externí sdílené složky), která způsobuje problémy pro všechny oddíly. Entity služby service je určený podle názvu služby (URI).
* **Oddíl**. Reprezentuje stav oddílu služby. Sestavy stavu oddílu popisují podmínky, které ovlivňují celé sadě replik. Mezi příklady patří, když počet replik nedosahuje počtu cílových a když oddíl je ve ztrátě kvora. Oddíl entita je identifikován ID (GUID) oddílu.
* **Repliky**. Reprezentuje stav repliky stavové služby nebo instance bezstavové služby. Replika je nejmenší jednotka watchdogs a součásti systému může podávat pro aplikaci. Mezi příklady patří pro stavové služby primární repliky, který nelze operace replikace do sekundární databáze a pomalá replikace. Bezstavové instance může hlásit také, když běží nemá dostatek prostředků nebo má problémy s připojením. Entita repliky je identifikován ID (GUID) oddílu a ID replik nebo instancí (dlouhé).
* **DeployedApplication**. Představuje stav *aplikace spuštěný v uzlu*. Sestav o stavu nasazených aplikací popisují podmínky, které jsou specifické pro aplikace na uzlu, který nemůže být zúží a balíčky služeb, které jsou nasazené na stejném uzlu. Mezi příklady patří chyby, když balíček aplikace se nedá stáhnout v tomto uzlu a problémy s nastavením objekty zabezpečení aplikace na uzlu. Název aplikace (URI) a název uzlu (řetězec) je identifikován nasazené aplikace.
* **DeployedServicePackage**. Reprezentuje stav balíčku služby běží na uzlu v clusteru. Popisuje podmínky specifické pro balíček služby, které nemají vliv na ostatní balíčky service ve stejném uzlu pro stejnou aplikaci. Mezi příklady patří balíček kódu v balíčku služby, který nelze spustit a konfigurační balíček, který nelze číst. Balíček nasazenou službu je identifikován název aplikace (URI), název uzlu (řetězec), název manifestu služby (řetězec) a ID aktivace balíček služby (řetězec).

Členitost modelu stavu umožňuje snadno zjistit a opravit problémy. Například pokud služba nereaguje, se dá oznamuje, že instance aplikace není v pořádku. Vytváření sestav na, že úroveň není ideální, ale protože tento problém pravděpodobně ovlivňovat všechny služby v rámci této aplikace. Sestava bude použito ke službě není v pořádku nebo na konkrétní podřízeného oddílu, je-li další informace o odkazuje na tento oddíl. Data automaticky povrchy prostřednictvím hierarchie a není v pořádku oddílu jsou dostupná na úrovních služby a aplikace. Tato agregace pomáhá identifikovat a vyřešit hlavní příčinu problému rychleji.

Stav hierarchie se skládá z vztahů nadřazenosti a podřízenosti. Cluster se skládá z uzlů a aplikací. Aplikace služeb a nasazené aplikace. Nasazené aplikace mít nasazené balíčky služeb. Služby mají oddílů a každý oddíl má jeden nebo více replikami. Není speciální vztah mezi uzly a nasazené entity. Poškozený uzel podle součásti systému autority, službu Správce převzetí služeb při selhání má vliv na nasazené aplikace, balíčky služeb a repliky na něm nasazené.

Hierarchie stavu představuje nejnovější stav systém založený na nejnovější zprávy o stavu, což je téměř v reálném čase.
Interní a externí watchdogs vykazovat stejné entity na základě specifické pro aplikaci logiky nebo vlastní monitorovaných podmínky. Sestavy uživatelů existovat současně s sestavy systému.

Naplánujte investovat do sestavy a reakce na stav během návrhu velké cloudovou službu. Tento počáteční investici usnadňuje služba ladění, monitorování a obsluha.

## <a name="health-states"></a>Stavy
Service Fabric pomocí tří stavů popisující, zda entita je nebo není v pořádku: OK, upozornění a chyb. Každé zprávy odeslané do storu stavu musíte zadat jednu z těchto stavů. Výsledek vyhodnocení stavu je jedním z těchto stavů.

Možné [stavů](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthstate) jsou:

* **OK**. Entita je v pořádku. Nejsou žádné známé problémy v jeho nebo její podřízené jednotce (Pokud se používá).
* **Upozornění**. Entita obsahuje nějaké problémy, ale mohou i nadále fungovat správně. Například je ale nezpůsobí ještě případných funkčních problémů. V některých případech může opravit podmínky upozornění bez zásahu externí samotný. V těchto případech sestav o stavu zvýšíte povědomí a poskytují přehled o co se děje. V jiných případech může snížit podmínky upozornění do vážnému problému bez zásahu uživatele.
* **Error** (Chyba). Entita je v pořádku. Chcete-li vyřešit stav entity, potřeba provést akci, protože nemůže správně fungovat.
* **Neznámý**. Entita neexistuje v health store. Tento výsledek je možné získat z distribuovaných dotazů, které sloučit výsledky z několika součástí. Například seznamu dotazu get uzel přejde na **FailoverManager**, **ClusterManager**, a **HealthManager**; získání aplikace seznamu dotazu přejde na  **ClusterManager** a **HealthManager**. Tyto dotazy sloučit výsledky z více komponent systému. Pokud jiné součásti systému vrací entity, který není přítomen v úložišti stavů, výsledek sloučení má neznámý stav. Entita není v úložišti, protože sestav o stavu nebyly dosud zpracovány nebo entity vyčištění po jejím odstranění.

## <a name="health-policies"></a>Zásady stavu
Úložiště stavu platí zásady stavu k určení, zda entita je v pořádku na základě jeho sestavy a jejích potomků.

> [!NOTE]
> Stav zásad se dá nastavit v manifestu clusteru (pro vyhodnocení stavu clusteru a uzlů) nebo manifestu aplikace (pro hodnocení aplikace a všech jeho podřízených prvků). Žádostí o stav vyhodnocení můžete předat vlastní stav vyhodnocení zásady, které se používají pouze pro vyhodnocení.
> 
> 

Ve výchozím nastavení Service Fabric použije striktní pravidla (vše, co musí být v pořádku) pro hierarchický vztah nadřízenosti a podřízenosti. Pokud ani jeden z podřízených má jednu událost není v pořádku, nadřazené považoval za poškozený.

### <a name="cluster-health-policy"></a>Zásady stavu clusteru
[Clusteru zásady stavu](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy) se používá k vyhodnocení stavu clusteru a uzel stavů. Zásady lze definovat v manifestu clusteru. Pokud není k dispozici, se používá výchozí zásady (nula přípustný selhání).
Zásady stavu clusteru obsahuje:

* [ConsiderWarningAsError](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.considerwarningaserror). Určuje, zda zacházet se stav upozornění sestavy jako chyby během vyhodnocování stavu. Výchozí: false.
* [MaxPercentUnhealthyApplications](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.maxpercentunhealthyapplications). Určuje maximální přípustný procento aplikací, které může být není v pořádku, než clusteru se považuje za chybu.
* [MaxPercentUnhealthyNodes](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.maxpercentunhealthynodes). Určuje maximální přípustný procento uzlů, které může být není v pořádku, než clusteru se považuje za chybu. Ve velkých clusterech některé uzly jsou vždy dolů nebo navýšení kapacity pro opravy, tak tolerovat, který by měl být nakonfigurovaný toto procento.
* [ApplicationTypeHealthPolicyMap](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.applicationtypehealthpolicymap). Mapa aplikace typ stavu zásady je možné během vyhodnocení stavu clusteru popisující typy speciální aplikací. Ve výchozím nastavení jsou všechny aplikace umístit do fondu a vyhodnotit s MaxPercentUnhealthyApplications. Pokud některé typy aplikací by měl zpracovávat odděleně, můžou být přijata z globálního fondu. Místo toho se vyhodnocují před procenta spojené s jejich název typu aplikace v objektu map. Například v clusteru existují nepřeberným množstvím aplikací z různých typů a několik instancí aplikace ovládací prvek typu zvláštní aplikace. Ovládací prvek aplikace by nikdy neměly být chybu. Můžete určit globální MaxPercentUnhealthyApplications 20 % tolerovat několika chybám, ale pro aplikace typu "ControlApplicationType" MaxPercentUnhealthyApplications nastavena na hodnotu 0. Tímto způsobem, pokud některé z mnoha aplikací nejsou v pořádku, ale pod globální procentní hodnotu není v pořádku, clusteru vyhodnocována na upozornění. Stav upozornění nemá vliv na upgrade clusteru nebo jiných monitorování aktivované chybového stavu. Ale ještě jeden ovládací prvek aplikace chybu s žádným clusteru není v pořádku, která aktivuje vrácení zpět nebo pozastaví upgradu clusteru, v závislosti na konfiguraci upgradu.
  Pro aplikace typy definované v objektu map všechny instance aplikace pocházejí z globálního fondu aplikací. Jsou vyhodnocovány na základě celkového počtu aplikací typu aplikace, pomocí konkrétní MaxPercentUnhealthyApplications z mapy. Všechny ostatní aplikace zůstanou v globální fond a vyhodnocují se MaxPercentUnhealthyApplications.

Následující příklad je výpisem z manifestu clusteru. K definování položky na mapě typ aplikace, zadejte před název parametru "ApplicationTypeMaxPercentUnhealthyApplications-", za nímž následuje název typu aplikace.

```xml
<FabricSettings>
  <Section Name="HealthManager/ClusterHealthPolicy">
    <Parameter Name="ConsiderWarningAsError" Value="False" />
    <Parameter Name="MaxPercentUnhealthyApplications" Value="20" />
    <Parameter Name="MaxPercentUnhealthyNodes" Value="20" />
    <Parameter Name="ApplicationTypeMaxPercentUnhealthyApplications-ControlApplicationType" Value="0" />
  </Section>
</FabricSettings>
```

### <a name="application-health-policy"></a>Zásady stavu aplikace
[Zásady stavu aplikace](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthpolicy) popisuje, jak se provádí hodnocení událostí a stavů podřízené agregace pro aplikace a jejich podřízené prvky. Lze ji definovat v manifestu aplikace **ApplicationManifest.xml**, v balíčku aplikace. Pokud nejsou zadány žádné zásady, Service Fabric předpokládá, že entita je v pořádku, pokud má sestava stavu, nebo podřízený atribut ve stavu upozornění nebo chyby.
Konfigurovatelné zásady jsou:

* [ConsiderWarningAsError](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.considerwarningaserror). Určuje, zda zacházet se stav upozornění sestavy jako chyby během vyhodnocování stavu. Výchozí: false.
* [MaxPercentUnhealthyDeployedApplications](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthpolicy.maxpercentunhealthydeployedapplications). Určuje maximální přípustný procento nasazených aplikací, které může být není v pořádku, než aplikace se považuje za chybu. Toto procento se vypočítá jako podíl počtu poškozené nasazené aplikace prostřednictvím z počtu uzlů, které aplikace jsou aktuálně nasazené v clusteru. Výpočet zaokrouhlí tento údaj tolerovat selhání jednoho na malý počet uzlů. Výchozí procentuální: nula.
* [DefaultServiceTypeHealthPolicy](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthpolicy.defaultservicetypehealthpolicy). Určuje výchozí služby typu zásad stavu, která nahradí výchozí zásady stavu pro všechny typy služeb v aplikaci.
* [ServiceTypeHealthPolicyMap](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthpolicy.servicetypehealthpolicymap). Obsahuje mapování zásady stavu služby pro konkrétní typ služby. Tyto zásady nahradit zásady výchozí typ služby stavu pro každý typ zadaná služba. Například pokud aplikace má typ služby bezstavové brány a typ služby stavové stroje, můžete nakonfigurovat zásady stavu pro vyhodnocují jinak. Pokud určíte zásady na typ služby, můžete získat podrobnější kontrolu stavu služby.

### <a name="service-type-health-policy"></a>Zásady stavu typ služby
[Služby typu zásady stavu](https://docs.microsoft.com/dotnet/api/system.fabric.health.servicetypehealthpolicy) Určuje, jak vyhodnotit a agregovat služeb a podřízených prvků služeb. Zásady obsahují:

* [MaxPercentUnhealthyPartitionsPerService](https://docs.microsoft.com/dotnet/api/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthypartitionsperservice). Určuje maximální přípustný procento poškozené oddíly, než se služba se považuje za není v pořádku. Výchozí procentuální: nula.
* [MaxPercentUnhealthyReplicasPerPartition](https://docs.microsoft.com/dotnet/api/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthyreplicasperpartition). Určuje maximální přípustný procento poškozené repliky, než se oddíl se považoval za poškozený. Výchozí procentuální: nula.
* [MaxPercentUnhealthyServices](https://docs.microsoft.com/dotnet/api/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthyservices). Určuje maximální přípustný procento služby není v pořádku, než se aplikace se považoval za poškozený. Výchozí procentuální: nula.

Následující příklad je výpisem z manifestu aplikace:

```xml
    <Policies>
        <HealthPolicy ConsiderWarningAsError="true" MaxPercentUnhealthyDeployedApplications="20">
            <DefaultServiceTypeHealthPolicy
                   MaxPercentUnhealthyServices="0"
                   MaxPercentUnhealthyPartitionsPerService="10"
                   MaxPercentUnhealthyReplicasPerPartition="0"/>
            <ServiceTypeHealthPolicy ServiceTypeName="FrontEndServiceType"
                   MaxPercentUnhealthyServices="0"
                   MaxPercentUnhealthyPartitionsPerService="20"
                   MaxPercentUnhealthyReplicasPerPartition="0"/>
            <ServiceTypeHealthPolicy ServiceTypeName="BackEndServiceType"
                   MaxPercentUnhealthyServices="20"
                   MaxPercentUnhealthyPartitionsPerService="0"
                   MaxPercentUnhealthyReplicasPerPartition="0">
            </ServiceTypeHealthPolicy>
        </HealthPolicy>
    </Policies>
```

## <a name="health-evaluation"></a>Vyhodnocování stavu
Uživatele a automatizované služby můžete vyhodnotit stav pro každou entitu v každém okamžiku. K vyhodnocení stavu entity, agregace úložiště stavu všechny stavy hlásí v entitě a vyhodnotí všechny jeho podřízené objekty (Pokud se používá). Algoritmus agregace stavu používá zásady stavu, které určují, jak vyhodnotit sestav o stavu a jak se spojují podřízené stavů (Pokud se používá).

### <a name="health-report-aggregation"></a>Agregace sestav stavu
Jedna entita může mít několik sestav o stavu odeslané jinou reporters (součásti systému nebo watchdogs) na jiné vlastnosti. Agregace využívá zásady přidruženého stavu, zejména ConsiderWarningAsError členem zásady stavu aplikace nebo clusteru. ConsiderWarningAsError Určuje, jak vyhodnotit upozornění.

Agregovaný stav je spuštěno *nejhorší* sestavy stavu v entitě. Pokud je sestava stavu alespoň jednu chybu, agregovaný stav je chyba.

![Agregace sestav stavu s zprávu o chybách.][2]

Stav entity, která má jeden nebo více zpráv o chybách stav se vyhodnotí jako chyba. Totéž platí pro zprávu o stavu s ukončenou platností, bez ohledu na jeho stav.

[2]: ./media/service-fabric-health-introduction/servicefabric-health-report-eval-error.png

Pokud neexistují žádné zprávy o chybách a minimálně jedno upozornění, agregovaný stav je upozornění nebo chyby, v závislosti na příznak ConsiderWarningAsError zásad.

![Agregace sestav stavu s zprávu upozornění a ConsiderWarningAsError false.][3]

Agregace sestav stavu s zprávu upozornění a ConsiderWarningAsError nastavena na hodnotu false (výchozí).

[3]: ./media/service-fabric-health-introduction/servicefabric-health-report-eval-warning.png

### <a name="child-health-aggregation"></a>Podřízený stav agregace
Agregovaný stav entity odráží stav podřízených (Pokud se používá). Algoritmus pro souhrn stavů podřízené používá stavu zásad platných na základě typu entity.

![Podřízené entity stavu agregace.][4]

Podřízená položka na základě zásad stavu.

[4]: ./media/service-fabric-health-introduction/servicefabric-health-hierarchy-eval.png

Po vyhodnocení všechny podřízené objekty úložiště stavu agreguje jejich stavů podle nakonfigurované maximální procento není v pořádku, podřízené položky. Určuje toto procento je převzata z zásad na základě typu entity a podřízené.

* Pokud všechny podřízené objekty mají OK stavy, stav podřízených agregovat je v pořádku.
* Pokud mají OK a upozornění státy, stav podřízených agregovat je upozornění.
* Pokud jsou podřízené objekty s chybové stavy licencí, které maximální povolené procento podřízené položky není v pořádku, nerespektují, je stav agregovaných nadřazené chybu.
* Pokud podřízených položek s chybové stavy licencí dodržovat maximální povolené procento podřízené položky není v pořádku, stav agregovaných nadřazené je upozornění.

## <a name="health-reporting"></a>Vytváření sestav stavu
Součásti systému, aplikace Fabric systému a interní/externí watchdogs může hlásit s entitami služby Service Fabric. Ujistěte se, reporters *místní* stanovení stavu monitorovaných entit na základě zadaných podmínek se monitorování. Není potřeba podívejte se na všechny globální stav nebo agregovaná data. Požadované chování je jednoduchý reporters a není komplexní organismy, které je potřeba se podívat na mnoho věcí k odvození informací k odeslání.

K odesílání dat o stavu k úložišti stavů, reportérka musí identifikovat ovlivněné entity a vytvoření sestavy stavu. Chcete-li odeslat sestavy, použijte [FabricClient.HealthClient.ReportHealth](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.reporthealth) rozhraní API, sestava stavu rozhraní API je zveřejněné na `Partition` nebo `CodePackageActivationContext` objekty, rutin prostředí PowerShell nebo REST.

### <a name="health-reports"></a>Sestav stavu
[Sestav o stavu](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthreport) pro všechny entity v clusteru obsahovat tyto informace:

* **SourceId**. Řetězec, který jednoznačně identifikuje reportérka událost stavu.
* **Identifikátor entity**. Identifikují entitu, ve kterém se použije sestavy. Se liší v závislosti [typ entity](service-fabric-health-introduction.md#health-entities-and-hierarchy):
  
  * Cluster. Žádné.
  * Uzel. Název uzlu (řetězec).
  * aplikace. Název aplikace (URI). Představuje název instance aplikace nasazené v clusteru.
  * Služba. Název služby (URI). Představuje název instance služby nasazené v clusteru.
  * Oddíl. ID oddílu (GUID). Představuje jedinečný identifikátor oddílu.
  * Replika. ID instance bezstavovou službu (INT64) nebo ID repliky stavové služby.
  * DeployedApplication. Název aplikace (URI) a název uzlu (řetězec).
  * DeployedServicePackage. Název aplikace (URI), název uzlu (řetězec) a service manifest název (řetězec).
* **Vlastnost**. A *řetězec* (ne pevné výčet), která umožňuje osoby podávající hlášení do kategorií událost stavu pro určité vlastnosti entity. Například můžete reportérka A hlášení stavu Node01 vlastnost "Úložiště" a B reportérka můžete hlášení stavu Node01 vlastnost "Připojení". V health store tyto sestavy jsou považovány za události samostatné stavu entity Node01.
* **Popis**. Řetězec, který umožňuje reportérka, aby poskytoval podrobné informace o stavu události. **SourceId**, **vlastnost**, a **stavu HealthState** by měl plně popisují sestavy. Popis přidá lidsky čitelném informace o sestavě. Text usnadňuje správcům a uživatelům pochopit sestava stavu.
* **Stav HealthState**. [Výčet](service-fabric-health-introduction.md#health-states) , která popisuje stav sestavy. Přípustné hodnoty jsou OK, upozornění a chyby.
* **TimeToLive**. Interval timespan, která určuje, jak dlouho sestava stavu je platný. S velkou provázaností **RemoveWhenExpired**, umožňuje health store vědět, jak vyhodnotit prošlé události. Ve výchozím nastavení hodnota je nekonečno a sestava je stále platný.
* **RemoveWhenExpired**. Logická hodnota. Pokud je nastavený na hodnotu true, sestav stavu s ukončenou platností se automaticky odebere z health store a sestavy nebude mít vliv vyhodnocování stavu entity. Používá, když se sestava je platný pro určité zadané době pouze a není nutné explicitně smažte osoby podávající hlášení. Slouží také k odstranění z health store sestav (například sledovacího zařízení se změní a zastaví odesílání sestav s předchozí zdroj a vlastnost). Může odeslat sestavy s krátkou TimeToLive spolu s RemoveWhenExpired vymazat všechny předchozí stav z health store. Pokud je hodnota nastavena na hodnotu false, je považován za chybu při hodnocení stavu vypršela platnost sestavy. Hodnota false signály, které mají stav úložiště, které pravidelně hlásit zdroj pro tuto vlastnost. Pokud ne, pak musí existovat něco špatného sledovacího zařízení. Stav od sledovacího zařízení je zachycen zvážení události za chybu.
* **SequenceNumber**. Kladné celé číslo, které musí být stále se zvětšujícím, představuje pořadí zpráv. Používá se v úložišti stavů k zjišťování zastaralých sestavy, které byly přijaty opožděně kvůli zpoždění v síti nebo jiných problémů. Sestavy byl odmítnut, pokud pořadové číslo je že menší nebo rovna maximální číslo pro stejnou entitu, zdroj a vlastnost nedávno zavedlo. Pokud se nezadá, číslo sekvence se vygeneruje automaticky. Je nutné umístit do pořadovým číslem pouze v případě, že vytváření sestav o přechodů mezi stavy. V takovém případě zdroj musí pamatovat které sestavy je odeslat a uchovávat informace pro obnovení na převzetí služeb při selhání.

Tyto čtyři úryvky informací – ID zdroje, identifikátor entity, vlastnosti a elementu HealthState – jsou požadovány pro každé sestavě o stavu. ID zdroje řetězce nesmí začínat předponou "**systému.**", který je vyhrazený pro sestavy systému. Pro stejnou entitu je pouze jednu sestavu pro stejný zdroj a vlastnost. Více sestav pro stejný zdroj a vlastnosti navzájem přepisují, stavu na straně klienta (pokud jsou dávkové,) nebo na stav uložení na straně. Nahrazení je založen na sekvenci čísel. novější sestavy (přičemž vyšší čísla pořadí) nahraďte starších sestav.

### <a name="health-events"></a>Události stavu
Interně, health store udržuje [události týkající se stavu](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthevent), které obsahují všechny informace ze sestav a další metadata. Metadata obsahují čas, kdy byl zadán sestavy stavu klienta a čas, kdy se změnil na straně serveru. Události stavu jsou vráceny pomocí [dotazů na stav](service-fabric-view-entities-aggregated-health.md#health-queries).

Přidání metadat obsahuje:

* **SourceUtcTimestamp**. Čas sestavy byl zadán pro stav klienta (Coordinated Universal Time).
* **LastModifiedUtcTimestamp**. Čas poslední změny sestavě na straně serveru (Coordinated Universal Time).
* **IsExpired**. Příznak označující, zda sestavy platnost vypršela, když byl dotaz spuštěn v úložišti stavů. Událost můžete platnost vypršela, pouze v případě, že RemoveWhenExpired má hodnotu false. V opačném případě události není vrácené dotazem a odebrat z úložiště.
* **LastOkTransitionAt**, **LastWarningTransitionAt**, **LastErrorTransitionAt**. Čas posledního OK a upozornění/Chyba přechodů. Tato pole poskytují historie stavu přechodů mezi stavy pro událost.

Pole přechod stavu je možné chytřejší výstrahy nebo informace o událostech "historických" stavu. Umožňují scénáře, jako:

* Pošle upozornění, když vlastnost bylo v chybě nebo upozornění pro více než X minut. Kontrola stavu pro určitou dobu se vyhnete upozornění na dočasné situace. Například lze přeložit výstrahu, pokud stav má byla upozornění pro víc než pět minut do (stav HealthState == upozornění a LastWarningTransitionTime nyní - > 5 minut).
* Upozornění jen na podmínkách, které byly změněny za posledních X minut. Pokud už sestavy než určený čas na chyby, může být ignorována, protože byl již signalizována dříve.
* Pokud vlastnost je při přepínání mezi upozornění a chyby, určete, jak dlouho to není v pořádku po (tedy ne OK). Například lze přeložit výstrahu, pokud vlastnost nebyla v pořádku po dobu více než pět minut do (stav HealthState! = Ok a nyní - LastOkTransitionTime > 5 minut).

## <a name="example-report-and-evaluate-application-health"></a>Příklad: Sestavy a vyhodnocování stavu aplikace
Následující příklad odešle zprávu o stavu prostředí PowerShell na aplikaci **fabric: / WordCount** ze zdroje **MyWatchdog**. Sestava stavu obsahuje informace o stavu vlastnosti "dostupnosti" v chybového stavu, s nekonečnou TimeToLive. Poté dotazuje stavu aplikace, která vrátí souhrn stavu chyby stavu a událostí ohlášené stavu v seznamu událostí stavu.

```powershell
PS C:\> Send-ServiceFabricApplicationHealthReport –ApplicationName fabric:/WordCount –SourceId "MyWatchdog" –HealthProperty "Availability" –HealthState Error

PS C:\> Get-ServiceFabricApplicationHealth fabric:/WordCount -ExcludeHealthStatistics


ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Error
UnhealthyEvaluations            :
                                  Error event: SourceId='MyWatchdog', Property='Availability'.

ServiceHealthStates             :
                                  ServiceName           : fabric:/WordCount/WordCountService
                                  AggregatedHealthState : Error

                                  ServiceName           : fabric:/WordCount/WordCountWebService
                                  AggregatedHealthState : Ok

DeployedApplicationHealthStates :
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_0
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_2
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_3
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_4
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_1
                                  AggregatedHealthState : Ok

HealthEvents                    :
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 360
                                  SentAt                : 3/22/2016 7:56:53 PM
                                  ReceivedAt            : 3/22/2016 7:56:53 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Error->Ok = 3/22/2016 7:56:53 PM, LastWarning = 1/1/0001 12:00:00 AM

                                  SourceId              : MyWatchdog
                                  Property              : Availability
                                  HealthState           : Error
                                  SequenceNumber        : 131032204762818013
                                  SentAt                : 3/23/2016 3:27:56 PM
                                  ReceivedAt            : 3/23/2016 3:27:56 PM
                                  TTL                   : Infinite
                                  Description           :
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Ok->Error = 3/23/2016 3:27:56 PM, LastWarning = 1/1/0001 12:00:00 AM
```

## <a name="health-model-usage"></a>Využití modelu stavu
Health model umožňuje cloudové služby a základní platformy Service Fabric škálovat, protože monitorování a stavu jsou rozděleny do různých monitorech v rámci clusteru.
Jiných systémů mají jednu, centralizované služby na úrovni clusteru, který analyzuje všechna *potenciálně* užitečné informace, protože ho vygeneroval služby. Tento přístup omezuje jejich škálovatelnost. To není jim také povolit shromažďování konkrétních informací vám pomůže identifikovat problémy a potenciálních problémech blízko hlavní příčinu, jako je to možné.

Health model se používá silně pro monitorování a diagnostiku, za vaše rozhodnutí vyzkoušet stav clusteru a aplikací a pro monitorované upgrady. Jiné služby stavu data použít k provádění automatických oprav, historie stavu clusteru, ale taky popustit vydat upozornění za určitých podmínek.

## <a name="next-steps"></a>Další postup
[Zobrazení sestav health Service Fabric](service-fabric-view-entities-aggregated-health.md)

[Použití sestav stavu systému pro řešení potíží](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Způsob hlášení a kontrola stavu služby](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Přidání vlastních stavových sestav Service Fabric](service-fabric-report-health.md)

[Monitorování a Diagnostika služeb místně](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Upgrade aplikace Service Fabric](service-fabric-application-upgrade.md)

