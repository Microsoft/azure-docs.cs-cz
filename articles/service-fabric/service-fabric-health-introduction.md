---
title: Sledování stavu v Service Fabric | Microsoft Docs
description: Úvod do Azure Service Fabric stav monitorování model, který poskytuje sledování clusteru a příslušné aplikace a služby.
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
ms.openlocfilehash: fc0bb56e85c2a9cf7a458b0f6d97887d392ee65f
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37114312"
---
# <a name="introduction-to-service-fabric-health-monitoring"></a>Úvod do monitorování stavu Service Fabric
Azure Service Fabric zavádí stavu model, který poskytuje bohatý, flexibilní a rozšiřitelný stavu vyhodnocení a vytváření sestav. Model umožňuje téměř v reálném čase monitorování stavu clusteru a služby spuštěné v ní. Můžete snadno získat informace o stavu a opravte potenciální problémy předtím, než v kaskádě a způsobit masivní výpadků. V typické modelu služby odesílat sestavy založené na jejich místní zobrazení, a že informace se shromažďují zajistit celkovou clusteru úrovni zobrazení.

Komponenty Service Fabric pomocí tohoto bohaté stavu modelu sestavy jejich aktuálního stavu. Můžete použít stejný mechanismus stav sestavy z aplikace. Pokud jste investovat do stavu vysoce kvalitní reporting shromažďuje vaše vlastní podmínky, můžete zjistit a opravit problémy mnohem snadněji pro běžící aplikaci.

V následujícím videu Microsoft Virtual Academy také popisuje model stavu Service Fabric a jak se používají: <center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tevZw56yC_1906218965">
<img src="./media/service-fabric-health-introduction/HealthIntroVid.png" WIDTH="360" HEIGHT="244">
</a></center>

> [!NOTE]
> Spuštění subsystém stavu vyřešit potřebu monitorovaných upgrady. Service Fabric nabízí monitorovaných aplikací a cluster upgrady, které zajistit úplnou dostupnost, nedojde k žádnému výpadku a minimální na žádný zásah uživatele. K dosažení těchto cílů, zkontroluje upgradu stavu, v závislosti na nakonfigurovaných zásadách upgradu. Upgrade můžete pokračovat jenom v případě, že stav respektuje požadované prahové hodnoty. Jinak upgrade buď automaticky vrácena nebo pozastavení a poskytuje správcům možnost opravit problémy. Další informace o upgradu aplikace naleznete v tématu [v tomto článku](service-fabric-application-upgrade.md).
> 
> 

## <a name="health-store"></a>Úložiště stavu
Úložiště stavu zajišťuje zdravotních informací o entitách clusteru pro snadné načítání a vyhodnocení. Jsou implementované jako Service Fabric trvalé stavové služby zajistit vysokou dostupnost a škálovatelnost. Úložiště stavu je součástí **fabric: / systému** aplikace a je k dispozici, pokud je cluster v provozu a spuštěná.

## <a name="health-entities-and-hierarchy"></a>Stav entity a hierarchie
Stav entity, které jsou uspořádány do logických hierarchie, která zaznamená interakce a závislosti mezi různými entitami. Úložiště stavu automaticky vytvoří entity stavu a hierarchie, které jsou založené na zprávách přijaté z komponenty Service Fabric.

Stav entity zrcadlení entity Service Fabric. (Například **stavu aplikace entity** odpovídá instance aplikace nasazené v clusteru, při **stavu uzlu entity** odpovídá uzlem clusteru Service Fabric.) Hierarchie stavu zaznamená interakce systémových entit a je základem pro vyhodnocení pokročilé stavu. Informace o klíčové koncepty Service Fabric najdete [Service Fabric technický přehled](service-fabric-technical-overview.md). Další informace o aplikaci, najdete v části [model aplikace Service Fabric](service-fabric-application-model.md).

Stav entity a hierarchii povolit cluster a aplikace se efektivně hlášené, ladit a sledovat. Health model poskytuje přesný, *granulární* reprezentace stavu mnoho přesunutí součásti v clusteru.

![Stav entity.][1]
Entity stavu uspořádány v hierarchii podle vztahů nadřazenosti a podřízenosti.

[1]: ./media/service-fabric-health-introduction/servicefabric-health-hierarchy.png

Stav entity, které jsou:

* **Cluster**. Představuje stav clusteru Service Fabric. Sestavy stavu clusteru popisují podmínky, které ovlivňují celý cluster. Tyto podmínky ovlivňují více entit v clusteru nebo samotného clusteru. Podle stavu, nelze zpravodaje upřesněte problém na jeden nebo více není v pořádku, děti. Mezi příklady patří mozku rozdělení kvůli problémům se sítí rozdělení do oddílů nebo komunikaci clusteru.
* **Uzel**. Reprezentuje stav uzlu Service Fabric. Sestavy stavu uzlu popisují podmínky, které ovlivňují funkci uzlu. Obvykle mají vliv na všechny nasazené entity v rámci něj spuštěna. Mezi příklady patří uzlu nedostatek místa na disku (nebo jiných vlastností celého systému, jako je například paměť, připojení) a po uzlu. Entity uzlu je identifikována název uzlu (string).
* **Aplikace**. Reprezentuje stav instance aplikace spuštěná v clusteru. Sestavy o stavu aplikace popisují podmínky, které ovlivňují celkový stav aplikace. Nemůže být zúžit jednotlivých podřízených objektů (služby nebo aplikace nasazené). Mezi příklady patří začátku do konce interakce mezi různými službami v aplikaci. Je aplikace identifikován podle názvu aplikace (URI).
* **Služba**. Reprezentuje stav služby spuštěné v clusteru. Sestavy o stavu služby popisují podmínky, které ovlivňují celkový stav služby. Zpravodaje, která nelze zúžit problém na není v pořádku, oddíl nebo repliky. Mezi příklady patří konfigurace služby (například port nebo externí sdílené složky), která způsobuje problémy pro všechny oddíly. Entita služby je identifikována názvu služby (URI).
* **Oddíl**. Reprezentuje stav oddílu služby. Sestavy stavu oddíl popisuje podmínky, které ovlivňují celé sadě replik. Mezi příklady patří, kdy počet replik nedosahuje počtu cílových a oddíl je ve ztrátě kvora. Je oddíl identifikován oddílem ID (GUID).
* **Repliky**. Představuje stav repliky stavové služby nebo instance bezstavové služby. Replika je nejmenší jednotka, ve které watchdogs a součástí systému můžete sestavy pro aplikaci. Pro stavové služby příklady primární repliky, která nelze replikovat operations do sekundární databáze a pomalé replikace. Navíc bezstavové instance může hlásit, pokud je dostatek prostředků nebo má problémy s připojením. Entita repliky je identifikován oddílu ID (GUID) a ID replik nebo instancí (long).
* **DeployedApplication**. Představuje stav *aplikace běží na uzlu s*. Sestavy o stavu nasazení aplikace popisují podmínky, které jsou specifické pro aplikaci na uzlu, který nemůže být zúžit balíčky služeb, které jsou nasazené na stejném uzlu. Mezi příklady patří chyby, pokud v tomto uzlu nelze stáhnout balíček aplikace a problémy nastavení aplikace objekty zabezpečení na uzlu. Nasazené aplikace je identifikován název aplikace (URI) a název uzlu (string).
* **DeployedServicePackage**. Reprezentuje stav balíčku service běží na uzlu v clusteru. Popisuje podmínky specifické pro balíček služby, které nemají vliv na ostatní služby balíčky na stejném uzlu pro stejnou aplikaci. Mezi příklady patří balíček kódu v balíčku služby, který nelze spustit a konfigurační balíček, který nelze číst. Balíček nasazené služby je určen název aplikace (URI), název uzlu (string), manifestu název služby (string) a ID aktivace balíčku služby (string).

Členitost health model umožňuje snadno zjistit a opravit problémy. Například pokud služba nereaguje, je vhodná pro hlášení, že je instance aplikace na není v pořádku. Vytváření sestav v, úroveň není ideální, ale protože nemusí být problém ovlivňuje všechny služby v rámci této aplikace. Sestava bude použito ke službě není v pořádku nebo konkrétní podřízeného oddílu, pokud informace odkazuje na tento oddíl. Data automaticky povrchy prostřednictvím hierarchie a není v pořádku oddílu je dostupná na úrovni služby a aplikace. Tato agregace pomůže přesně určit a vyřešte příčinu problému rychleji.

Hierarchie stavu se skládá z vztahů nadřazenosti a podřízenosti. Cluster se skládá z uzlů a aplikace. Aplikací mít služeb a nasazené aplikace. Nasazených aplikací mít nasazené balíčky služeb. Služby mají oddíly a každý oddíl má jednu nebo více replik. Je speciální vztah mezi uzly a nasazené entity. Není v pořádku uzlu vykazované součást systému autority, službu Failover Manager ovlivňuje nasazené aplikace, balíčky služeb a repliky na něm nasazené.

Hierarchie stavu představuje nejnovější stav systému, na základě na nejnovější sestav stavu, což je téměř v reálném čase.
Vnitřní a vnější watchdogs může hlásit na stejných entit na základě logiky specifické pro aplikaci nebo vlastní monitorovaných podmínky. Uživatel sestavy souběžnou existenci s sestav systému.

Naplánujte investovat do sestavy a reakce na stav během návrhu velké cloudové služby. Tento počáteční investiční usnadňuje službu k ladění, monitorování a činnosti.

## <a name="health-states"></a>Stavy
Service Fabric používá tři stavy popsat, zda entita je nebo není v pořádku: OK, upozornění a chyb. Všechny sestavy odeslané k úložišti stavu musíte zadat jednu z těchto stavů. Výsledek vyhodnocení stavu je jedním z těchto stavů.

Možné [stavů](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthstate) jsou:

* **OK**. Entita je v pořádku. Neexistují žádné známé problémy, které jsou ohlášeny ho nebo jejích podřízených objektech (v případě potřeby).
* **Upozornění**. Entita, která má některé problémy, ale mohou i nadále fungovat správně. Například je, ale nezpůsobí ještě všechny funkční problémy. V některých případech může opravit podmínky upozornění bez zásahu externí sám sebe. V těchto případech sestav stavu zvýšení povědomí a poskytují přehled o co se děje. V jiných případech může snížit podmínky upozornění do k závažnému problému bez zásahu uživatele.
* **Error** (Chyba). Entita není v pořádku. Chcete-li vyřešit stav entity, by měla provedena akce, protože nemůže správně fungovat.
* **Neznámý**. Entita neexistuje v health store. Tento výsledek je možné získat z distribuované dotazy, které sloučení výsledky z několika součástí. Například dotaz get uzel přejde k **FailoverManager**, **ClusterManager**, a **HealthManager**; získat aplikaci dotaz přejde na  **ClusterManager** a **HealthManager**. Tyto dotazy sloučení výsledky z několika komponent systému. Pokud součást jiného systému vrací entity, která se nenachází v health store, výsledného má neznámý stav. Entity není v úložišti, protože stav sestavy nebyly dosud zpracovány nebo entita byla vyčištěna po jeho odstranění.

## <a name="health-policies"></a>Zásady stavu
Úložiště stavu platí zásady stavu k určení, zda entita je v pořádku, na základě jeho sestavy a její podřízené položky.

> [!NOTE]
> Zásady stavu mohou být zadané v manifestu clusteru (pro vyhodnocení stavu clusteru a uzel) nebo v manifestu aplikace (pro vyhodnocení aplikace a všech jeho podřízených položek). Žádosti o vyhodnocení stavu můžete předat také v vlastní stavu vyhodnocení zásady, které se používají pouze pro vyhodnocení.
> 
> 

Ve výchozím nastavení Service Fabric používá striktní pravidla (vše, co musí být v pořádku) pro nadřazený podřízený hierarchický vztah. Pokud ani jeden z podřízených má jednu událost není v pořádku, považuje za nadřazený není v pořádku.

### <a name="cluster-health-policy"></a>Zásady stavu clusteru
[Clusteru zásady stavu](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy) se používá k vyhodnocení stavu clusteru a stavů uzlu. Zásady lze definovat v manifestu clusteru. Pokud není přítomen, použije se výchozí zásady (nula. povolená počet selhání).
Zásady stavu clusteru obsahuje:

* [ConsiderWarningAsError](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.considerwarningaserror). Určuje, zda považovat upozornění stavu sestav jako chyby během vyhodnocení stavu. Výchozí: false.
* [MaxPercentUnhealthyApplications](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.maxpercentunhealthyapplications). Určuje maximální procento. povolená aplikací, které může být není v pořádku, než clusteru považován za chybu.
* [MaxPercentUnhealthyNodes](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.maxpercentunhealthynodes). Určuje maximální procento. povolená uzlů, které může být není v pořádku, než clusteru považován za chybu. Ve velkých clusterech některé uzly jsou vždy mimo provoz nebo se pro opravy, takže toto procento by měl být nakonfigurovaný pro tolerovat možnost, která.
* [ApplicationTypeHealthPolicyMap](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.applicationtypehealthpolicymap). Mapy zásady stavu typ aplikace lze během vyhodnocení stavu clusteru popisují typy speciální aplikací. Ve výchozím nastavení jsou všechny aplikace umístit do fondu a vyhodnotit s MaxPercentUnhealthyApplications. Pokud některé typy aplikací by měly zpracovávat odděleně, může být přijata mimo globální fond. Místo toho se vyhodnocují proti procenta přidružené k jejich název typu aplikace v mapě. Například v clusteru s podporou existují tisíce různých typů aplikací a několik instancí aplikace ovládacího prvku typu speciální aplikace. Řízení aplikace by měly být nikdy chyby. Můžete určit globální MaxPercentUnhealthyApplications na 20 % a tolerovat některé chyby, ale pro typ aplikace, které "ControlApplicationType" nastavena MaxPercentUnhealthyApplications na hodnotu 0. Tímto způsobem, pokud jsou některé z mnoha aplikací není v pořádku, ale pod globální procento není v pořádku, cluster bude vyhodnocena jako upozornění. Stav varování nemá negativní vliv na upgrade clusteru nebo jiných monitorování aktivovány chybového stavu. Ale aplikace i jeden ovládací prvek v chybě by clusteru není v pořádku, která aktivuje vrácení nebo pozastaví upgrade clusteru, v závislosti na konfiguraci upgradu.
  Pro aplikace typů front definovaných v mapě všechny instance aplikace se vyjímají z globální fond aplikací. Jsou vyhodnocovány na základě celkového počtu aplikací typu aplikace, pomocí konkrétní MaxPercentUnhealthyApplications z mapování. Všechny ostatní aplikace zůstanou globálního fondu a vyhodnocují se MaxPercentUnhealthyApplications.

Následující příklad je výňatek ze manifestu clusteru. K definování položky v mapě typu aplikace, zadejte před název parametru "ApplicationTypeMaxPercentUnhealthyApplications-", za nímž následuje název typu aplikace.

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
[Zásady stavu aplikace](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthpolicy) popisuje, jak se provádí vyhodnocení události a podřízené stavy agregace pro aplikace a jejich podřízené položky. V manifestu aplikace, lze definovat **ApplicationManifest.xml**, v balíčku aplikace. Pokud nejsou zadány žádné zásady, Service Fabric předpokládá, že je entita není v pořádku, pokud má sestavy stavu, nebo podřízenou položku ve stavu upozornění nebo chyby.
Konfigurovat zásady, jsou:

* [ConsiderWarningAsError](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.considerwarningaserror). Určuje, zda považovat upozornění stavu sestav jako chyby během vyhodnocení stavu. Výchozí: false.
* [MaxPercentUnhealthyDeployedApplications](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthpolicy.maxpercentunhealthydeployedapplications). Určuje maximální procento. povolená nasazené aplikace, které se dají není v pořádku, než aplikace je považována za chybu. Se počítá vydělením počtu není v pořádku nasazené aplikace přes počet uzlů, které aplikace jsou aktuálně nasazená v v clusteru. Výpočet zaokrouhlí na tolerovat jeden selhání na malém počtu uzlů. Výchozí procento: nula.
* [DefaultServiceTypeHealthPolicy](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthpolicy.defaultservicetypehealthpolicy). Určuje výchozí služby typu zásad stavu, která nahradí výchozí zásady stavu pro všechny typy služeb v aplikaci.
* [ServiceTypeHealthPolicyMap](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthpolicy.servicetypehealthpolicymap). Obsahuje mapování zásad stavu service podle typu služby. Tyto zásady nahradí výchozí zásady stavu typ služby pro každý typ zadaná služba. Pokud aplikace má typ služby bezstavové brány a service type stavový stroj, můžete je třeba nakonfigurovat zásady stavu pro jejich hodnocení jinak. Když zadáte zásady, za typ služby, můžete získat podrobnější řízení stavu služby.

### <a name="service-type-health-policy"></a>Zásady stavu typ služby
[Služby typu zásady stavu](https://docs.microsoft.com/dotnet/api/system.fabric.health.servicetypehealthpolicy) Určuje, jak k vyhodnocení a agregovat služeb a podřízené položky služby. Zásady obsahuje:

* [MaxPercentUnhealthyPartitionsPerService](https://docs.microsoft.com/dotnet/api/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthypartitionsperservice). Určuje maximální procento. povolená oddílů není v pořádku, než služba není v pořádku. Výchozí procento: nula.
* [MaxPercentUnhealthyReplicasPerPartition](https://docs.microsoft.com/dotnet/api/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthyreplicasperpartition). Určuje maximální procento. povolená repliky není v pořádku, než oddíl není v pořádku. Výchozí procento: nula.
* [MaxPercentUnhealthyServices](https://docs.microsoft.com/dotnet/api/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthyservices). Určuje maximální procento. povolená služby není v pořádku, než aplikace není v pořádku. Výchozí procento: nula.

Následující příklad je výňatek ze manifest aplikace:

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

## <a name="health-evaluation"></a>Vyhodnocení stavu
Uživatele a automatizované služby můžete kdykoli vyhodnocení stavu pro všechny entity. K vyhodnocení stavu entity, agregace úložiště stavu všechny stavy hlásí u entity a vyhodnocuje všechny její podřízené položky (v případě potřeby). Algoritmus agregace stavu používá zásady stavu, které určují, jak vyhodnotit sestavy o stavu a jak k agregaci podřízené stavů (v případě potřeby).

### <a name="health-report-aggregation"></a>Agregace sestav stavu
Jedna entita může mít několik sestav stavu poslal jiný reporters (součásti systému nebo watchdogs) na jiné vlastnosti. Agregace používá zásady přidružené stavu, zejména ConsiderWarningAsError členem zásady stavu aplikace nebo clusteru. ConsiderWarningAsError Určuje, jak vyhodnotit upozornění.

Agregovaný stav je aktivován *nejhorší* sestavy stavu u entity. Pokud je sestava stavu alespoň jednu chybu, agregovaný stav je chyba.

![Agregace sestavy stavu s zprávu o chybách.][2]

Stav entity, který má jeden nebo více sestav stavu chyby je vyhodnocena jako chyba. Totéž platí pro zprávu o stavu s ukončenou platností, bez ohledu na jeho stav.

[2]: ./media/service-fabric-health-introduction/servicefabric-health-report-eval-error.png

Pokud nejsou žádné zprávy o chybách a jedno či více varování, je agregovaný stav upozornění nebo chyby, v závislosti na příznak ConsiderWarningAsError zásad.

![Agregace sestavy stavu se sestava upozornění a ConsiderWarningAsError false.][3]

Agregace sestavy stavu se sestava upozornění a ConsiderWarningAsError nastavena na hodnotu false (výchozí).

[3]: ./media/service-fabric-health-introduction/servicefabric-health-report-eval-warning.png

### <a name="child-health-aggregation"></a>Podřízená položka stavu
Agregovaný stav entity odráží stav podřízených (v případě potřeby). Algoritmus pro souhrn stavů podřízené používá stavu zásady použitelné na základě typu entity.

![Podřízená položka stavu entity.][4]

Podřízená položka na základě zásad stavu.

[4]: ./media/service-fabric-health-introduction/servicefabric-health-hierarchy-eval.png

Po vyhodnotit všechny podřízené objekty úložiště zdravotní agreguje jejich stavů podle nakonfigurované maximální procento není v pořádku, děti. Toto procento je převzat ze zásad na základě typu entity a podřízené.

* Pokud všechny podřízené objekty mají OK stavy, stav podřízených agregovat je OK.
* Pokud podřízené objekty mají OK a upozornění stavy stav podřízených agregovat je upozornění.
* Pokud existují podřízené objekty s chybové stavy, které neodpovídají maximální povolené procento není v pořádku, děti, agregovaný stav je chyba.
* Pokud podřízených objektů se stavy chyba respektují maximální povolené procento není v pořádku, děti, agregovaný stav je upozornění.

## <a name="health-reporting"></a>Vytváření sestav stavu
Součásti systému, aplikace systému prostředků infrastruktury a interní/externí watchdogs může hlásit proti Service Fabric entity. Ujistěte se, reporters *místní* stanovení stavu monitorovaných entit, na základě podmínek, že monitorování. Nemusí se podívat na všechny globální stav nebo agregovaná data. Toto chování žádoucí je jednoduchý reporters a ne s komplexními organismy, které je potřeba, podívejte se na celou řadu věcí odvodit informacích k odeslání.

Zpravodaje, která pokud chcete odeslat data o stavu k úložišti stavů, musí identifikovat ovlivněné entity a vytvoření sestavy stavu. Chcete-li odeslat zprávu o, použijte [FabricClient.HealthClient.ReportHealth](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.reporthealth) rozhraní API, sestava stavu rozhraní API zveřejněné na `Partition` nebo `CodePackageActivationContext` objekty, rutiny prostředí PowerShell nebo REST.

### <a name="health-reports"></a>Sestavy o stavu
[Sestav stavu](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthreport) pro každou z entit v clusteru obsahovat tyto informace:

* **SourceId**. Řetězec, který jednoznačně identifikuje ohlašování stavu události.
* **Identifikátor entity**. Identifikuje entity, kde je použito sestavy. Liší se i na základě [typ entity](service-fabric-health-introduction.md#health-entities-and-hierarchy):
  
  * Cluster. Žádné.
  * uzel. Název uzlu (string).
  * Aplikace. Název aplikace (URI). Představuje název instance aplikace nasazené v clusteru.
  * Služba. Název služby (URI). Představuje název instance služby, který je nasazen v clusteru.
  * Oddíl. Oddíl ID (GUID). Představuje jedinečný identifikátor oddílu.
  * Replika. ID repliky stavové služby nebo ID instance bezstavové služby (INT64).
  * DeployedApplication. Název aplikace (URI) a název uzlu (string).
  * DeployedServicePackage. Název aplikace (URI), název uzlu (string) a service manifest název (string).
* **Vlastnost**. A *řetězec* (ne na pevnou výčet), která umožňuje zpravodaje zařadit do kategorií událost stavu pro určité vlastnosti entity. Například můžete ohlašování A hlášení stavu Node01 vlastnost "Úložiště" a ohlašování B mohou zasílat zprávy o stavu Node01 vlastnost "Připojení". V health store tyto sestavy jsou považovány za události samostatné stavu pro entitu Node01.
* **Popis**. Řetězec, který umožňuje ohlašování poskytnout podrobné informace o stavu události. **SourceId**, **vlastnost**, a **HealthState** by měl plně popisují sestavy. Popis přidá čitelná pro člověka informace o sestavě. Text usnadňuje správcům a uživatelům pochopit sestava stavu.
* **Stav HealthState**. [Výčtu](service-fabric-health-introduction.md#health-states) stav sestavy, který popisuje. Možné hodnoty jsou OK, upozornění a chyby.
* **TimeToLive**. Časový interval, která určuje, jak dlouho sestava stavu je platný. Kombinaci s **RemoveWhenExpired**, umožňuje úložiště zdravotní vědět, jak vyhodnotit vypršela platnost události. Ve výchozím nastavení hodnota je nekonečno a sestava je stále platný.
* **RemoveWhenExpired**. Logická hodnota. Pokud je nastaven na hodnotu true, sestava vypršela platnost stavu je automaticky odstraněna z health store a sestavy nebude mít vliv vyhodnocení stavu entity. Použít, když sestava je platný pro zadaný časový úsek pouze, a zpravodaje, která není nutné explicitně vyčistit. Používá se také k odstranění z health store sestav (například sledovací zařízení se změní a zastaví zasílání sestav se předchozí zdrojem a vlastnosti). Může poslat zprávu s krátkou TimeToLive společně s RemoveWhenExpired zrušte všechny předchozí stav z health store. Pokud je hodnota nastavena na hodnotu false, kterým vyprší platnost sestavy je považovat za chybu na vyhodnocení stavu. Hodnota false signály k úložišti stavu, který zdroj by měl pravidelně zprávu o tuto vlastnost. Pokud tomu tak není, pak musí existovat problém se sledovací zařízení. Sledovací zařízení stavu zachycenou s událostí za chybu.
* **SequenceNumber**. Kladné celé číslo, které musí být stále rostoucí, představuje pořadí sestavy. Slouží podle stavu úložiště ke zjištění zastaralé sestavy, které byly přijaty opožděně z důvodu zpoždění sítě nebo jiných problémů. Sestava je zamítnut, když pořadové číslo je že menší než nebo rovna nejvíc nedávno aplikovat číslo pro stejnou entitu, zdroje a vlastnost. Pokud není zadaný, vygeneruje se automaticky pořadové číslo. Je nutné uvést do pořadové číslo jenom v případě, že zprávy o přechodů mezi stavy. V takovém případě musí zdroj mějte na paměti, které sestavy je odeslána a uchovávat informace pro obnovení na převzetí služeb při selhání.

Tyto čtyři části informací – SourceId, identifikátor entity, vlastnosti a elementu HealthState – jsou vyžadovány pro každý sestava stavu. SourceId řetězec nesmí začínat předponu "**systému.**", který je vyhrazený pro sestavy systému. Pro stejnou entitu je pouze jednu sestavu pro stejný zdroj a vlastnost. Několik sestav pro stejný zdroj a vlastnost přepíší navzájem, buď na straně klienta stavu (pokud jsou se dávkově) nebo na stav ukládání straně. Pokud chcete nahrazení podle pořadových čísel; novější sestavy (s vyšším pořadová čísla) nahraďte starší sestavy.

### <a name="health-events"></a>Stav události
Interně úložiště zdravotní udržuje [události stavu](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthevent), které obsahují všechny informace ze sestav a dalších metadat. Metadata zahrnují době sestavy nebyl uveden do stavu klienta a čas, kdy se změnil na straně serveru. Události stavu se vrátí pomocí [dotazů na stav](service-fabric-view-entities-aggregated-health.md#health-queries).

Přidání metadat obsahuje:

* **SourceUtcTimestamp**. Čas sestavy byl zadán do stavu klienta (Coordinated Universal Time).
* **LastModifiedUtcTimestamp**. Čas, kdy sestavy, které bylo naposledy změněno na straně serveru (Coordinated Universal Time).
* **IsExpired**. Příznak označující, zda sestava byla při dotaz byl provedený úložiště zdravotní vypršelo. Událost můžete platnost pouze v případě RemoveWhenExpired je false. Událost, jinak hodnota není vrácených dotazem a bude odebrán z úložiště.
* **LastOkTransitionAt**, **LastWarningTransitionAt**, **LastErrorTransitionAt**. Čas poslední přechody OK, upozornění nebo chyby. Tato pole poskytnout historii stavu přechodů mezi stavy pro událost.

Pole přechod stavu lze chytřejší výstrahy nebo informace o stavu "historických". Umožňují scénáře, jako:

* Upozornit, když vlastnost byla v upozornění nebo chyby pro více než X minut. Kontrola podmínky pro určitou dobu zabraňuje výstrahy na dočasné stavy. Například můžete výstrahu, pokud stav má byl upozornění pro více než pět minut přeložit na (stav HealthState == upozornění a nyní - LastWarningTransitionTime > 5 minut).
* Výstrahy pouze na podmínky, které se změnily v posledních X minut. Pokud sestavu již byla v chybě než určený čas, může být ignorována, protože byl již signalizovala dříve.
* Pokud vlastnost je při přepínání mezi upozornění a chyb, zjistěte, jak dlouho byla není v pořádku (to znamená, není OK). Například můžete výstrahu, pokud vlastnost nebyla v pořádku po dobu více než pět minut přeložit na (stav HealthState! = Ok a teď - LastOkTransitionTime > 5 minut).

## <a name="example-report-and-evaluate-application-health"></a>Příklad: Vytvoření sestavy a vyhodnocování stavu aplikace
Následující příklad odešle zprávu o stavu pomocí prostředí PowerShell na aplikaci **fabric: / WordCount** ze zdroje **MyWatchdog**. Sestava stavu obsahuje informace o stavu vlastnost "dostupnosti" v chybového stavu, s neomezenou TimeToLive. Následně dotazuje stavu aplikací, která vrátí agregovat stavu stav chyby a události hlášené stavu v seznamu události stavu.

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

## <a name="health-model-usage"></a>Použití stavu modelu
Health model umožňuje cloudové služby a základní platformy Service Fabric chcete použít škálování, protože stanovení stavu a monitorování jsou rozděleny do různých monitorování v rámci clusteru.
Jiné systémy musí mít jedinou, centralizovanou služby na úrovni clusteru, která analyzuje všechny *potenciálně* užitečné informace, které služby. Tento přístup omezuje jejich škálovatelnost. Je také nedovoluje shromažďovat konkrétní informace usnadňují identifikaci problémů a s potenciálními problémy jako blízko příčiny nejblíže.

Health model se používá výraznou pro monitorování a diagnostiku pro vyhodnocení stavu cluster a aplikace a monitorovaných upgrady. Jiné služby stavu data použít k provedení automatické opravy, sestavení historie stavu clusteru a vydání výstrahy na určité podmínky.

## <a name="next-steps"></a>Další postup
[Zobrazit sestavy stavu Service Fabric](service-fabric-view-entities-aggregated-health.md)

[Pomocí sestav o stavu systému pro řešení potíží](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Postup vytvoření sestavy a zkontrolujte stav služby](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Přidat vlastní sestavy stavu Service Fabric](service-fabric-report-health.md)

[Monitorování a Diagnostika služby místně](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Upgrade aplikace Service Fabric](service-fabric-application-upgrade.md)

