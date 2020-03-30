---
title: Monitorování stavu v service fabric
description: Úvod do modelu monitorování stavu Azure Service Fabric, který poskytuje monitorování clusteru a jeho aplikací a služeb.
author: oanapl
ms.topic: conceptual
ms.date: 2/28/2018
ms.author: oanapl
ms.openlocfilehash: 473aa2b9a74193a857390cd3e29b2b559b6084d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282416"
---
# <a name="introduction-to-service-fabric-health-monitoring"></a>Úvod do monitorování stavu Service Fabric
Azure Service Fabric zavádí model stavu, který poskytuje bohaté, flexibilní a rozšiřitelné hodnocení stavu a vykazování. Model umožňuje téměř v reálném čase sledování stavu clusteru a služeb spuštěných v něm. Můžete snadno získat informace o zdraví a opravit potenciální problémy dříve, než kaskády a způsobit masivní výpadky. V typickém modelu služby odesílají sestavy na základě místních zobrazení a tyto informace jsou agregovány tak, aby poskytovaly celkové zobrazení na úrovni clusteru.

Součásti Service Fabric používají tento model bohatého stavu k vykazení aktuálního stavu. Stejný mechanismus můžete použít k hlášení stavu z vašich aplikací. Pokud investujete do vysoce kvalitních sestav stavu, které zachycují vaše vlastní podmínky, můžete mnohem snadněji zjistit a opravit problémy spuštěné aplikace.

> [!NOTE]
> Spustili jsme subsystém stavu, který řeší potřebu monitorovaných upgradů. Service Fabric poskytuje monitorované upgrady aplikací a clusterů, které zajišťují plnou dostupnost, žádné prostoje a minimální až žádný zásah uživatele. K dosažení těchto cílů upgrade kontroluje stav na základě nakonfigurovaných zásad upgradu. Upgrade může pokračovat pouze v případě, že stav respektuje požadované prahové hodnoty. V opačném případě je upgrade buď automaticky vrácena zpět nebo pozastavena, aby správci měli možnost problémy vyřešit. Další informace o inovacích aplikací naleznete v [tomto článku](service-fabric-application-upgrade.md).
> 
> 

## <a name="health-store"></a>Obchod se zdravím
Úložiště stavu uchovává informace o entitách souvisejících se stavem v clusteru pro snadné načítání a vyhodnocení. Je implementována jako service fabric trvalé stavové služby k zajištění vysoké dostupnosti a škálovatelnosti. Úložiště stavu je součástí **prostředků fabric:/Systémové** aplikace a je k dispozici, když je cluster v provozu.

## <a name="health-entities-and-hierarchy"></a>Entity stavu a hierarchie
Entity stavu jsou uspořádány v logické hierarchii, která zachycuje interakce a závislosti mezi různými entitami. Úložiště stavu automaticky vytváří entity stavu a hierarchii na základě sestav přijatých z komponent Service Fabric.

Entity stavu zrcadlí entity Service Fabric. (Například **entita aplikace stavu** odpovídá instanci aplikace nasazené v clusteru, zatímco **entita uzlu stavu** odpovídá uzlu clusteru Service Fabric.) Hierarchie stavu zachycuje interakce systémových entit a je základem pro pokročilé hodnocení stavu. O klíčových konceptech service fabric se můžete dozvědět v [technickém přehledu service fabric](service-fabric-technical-overview.md). Další informace o aplikaci naleznete v tématu [Service Fabric aplikační model](service-fabric-application-model.md).

Entity stavu a hierarchie umožňují clusteru a aplikacím efektivně vykazovat, ladit a monitorovat. Model stavu poskytuje přesné, *podrobné* znázornění stavu mnoha pohyblivých částí v clusteru.

![Entity stavu.][1]
Entity stavu uspořádané v hierarchii založené na vztazích nadřazený-podřízený.

[1]: ./media/service-fabric-health-introduction/servicefabric-health-hierarchy.png

Subjekty stavu jsou:

* **Cluster**. Představuje stav clusteru Service Fabric. Sestavy stavu clusteru popisují podmínky, které ovlivňují celý cluster. Tyto podmínky ovlivňují více entit v clusteru nebo samotném clusteru. Na základě tohoto stavu, reportér nemůže zúžit problém na jednu nebo více nezdravých dětí. Příklady zahrnují mozek clusteru rozdělení z důvodu rozdělení sítě nebo komunikační problémy.
* **Uzel**. Představuje stav uzlu Service Fabric. Sestavy stavu uzlu popisují podmínky, které ovlivňují funkčnost uzlu. Obvykle ovlivňují všechny nasazené entity, které jsou na něm spuštěny. Mezi příklady patří uzel nedostatek místa na disku (nebo jiné vlastnosti celého počítače, jako je například paměť, připojení) a když uzel je mimo. Entita uzlu je identifikována názvem uzlu (řetězec).
* **Aplikace**. Představuje stav instance aplikace spuštěné v clusteru. Zprávy o stavu aplikace popisují podmínky, které ovlivňují celkový stav aplikace. Nelze je zúžit na jednotlivé děti (služby nebo nasazené aplikace). Příklady zahrnují end-to-end interakce mezi různými službami v aplikaci. Entita aplikace je identifikována názvem aplikace (URI).
* **Služba**. Představuje stav služby spuštěné v clusteru. Zprávy o stavu služby popisují podmínky, které ovlivňují celkový stav služby. Reportér nemůže zúžit problém na nefunkční oddíl nebo repliku. Mezi příklady patří konfigurace služby (například port nebo externí sdílená složka), která způsobuje problémy pro všechny oddíly. Entita služby je identifikována názvem služby (URI).
* **Oddíl**. Představuje stav oddílu služby. Sestavy stavu oddílu popisují podmínky, které ovlivňují celou sadu replik. Mezi příklady patří, když je počet replik pod počtem cílů a kdy je oddíl ve ztrátě kvora. Entita oddílu je identifikována ID oddílu (GUID).
* **Replika**. Představuje stav repliky stavové služby nebo instance bezstavové služby. Replika je nejmenší jednotka, která watchdogs a systémové součásti mohou vykazovat pro aplikaci. Pro stavové služby příklady zahrnují primární repliku, která nemůže replikovat operace na sekundární sekundární a pomalou replikaci. Bezstavová instance může také hlásit, když je nedostatek prostředků nebo má problémy s připojením. Entita repliky je identifikována ID oddílu (GUID) a ID repliky nebo instance (dlouhé).
* **Nasazená aplikace**. Představuje stav *aplikace spuštěné na uzlu*. Sestavy stavu nasazených aplikací popisují podmínky specifické pro aplikaci v uzlu, které nelze zúžit na balíčky služeb nasazené ve stejném uzlu. Příklady zahrnují chyby, kdy balíček aplikace nelze stáhnout na tomto uzlu a problémy s nastavením objektů zabezpečení aplikace v uzlu. Nasazená aplikace je identifikována názvem aplikace (URI) a názvem uzlu (řetězec).
* **DeployedServicePackage**. Představuje stav balíčku služby spuštěného na uzlu v clusteru. Popisuje podmínky specifické pro balíček služeb, které nemají vliv na ostatní balíčky služeb ve stejném uzlu pro stejnou aplikaci. Příklady zahrnují balíček kódu v balíčku služby, který nelze spustit a konfigurační balíček, který nelze číst. Nasazený balíček služby je identifikován názvem aplikace (URI), názvem uzlu (řetězec), názvem manifestu služby (řetězec) a ID aktivace balíčku služby (řetězec).

Členitost modelu stavu usnadňuje detekci a opravu problémů. Například pokud služba neodpovídá, je možné hlásit, že instance aplikace není v pořádku. Vykazování na této úrovni však není ideální, protože problém nemusí mít vliv na všechny služby v rámci této aplikace. Sestava by měla být použita pro službu není v pořádku nebo na konkrétní podřízený oddíl, pokud více informací odkazuje na tento oddíl. Data se automaticky zobrazí v hierarchii a oddíl není v pořádku je viditelný na úrovni služeb a aplikací. Tato agregace pomáhá rychleji určit a vyřešit hlavní příčinu problému.

Hierarchie stavu se skládá z nadřazených a podřízených vztahů. Cluster se skládá z uzlů a aplikací. Aplikace mají služby a nasazené aplikace. Nasazené aplikace nasadily balíčky služeb. Služby mají oddíly a každý oddíl má jednu nebo více replik. Existuje zvláštní vztah mezi uzly a nasazenými entitami. Uzel není v pořádku, jak je hlášen jeho součástí systému autority, služba Správce převzetí služeb při selhání, ovlivňuje nasazené aplikace, balíčky služeb a repliky nasazené na něm.

Hierarchie stavu představuje nejnovější stav systému na základě nejnovějších zpráv o stavu, což jsou informace téměř v reálném čase.
Interní a externí watchdogs můžete sestavy na stejné entity na základě logiky specifické pro aplikaci nebo vlastní monitorované podmínky. Uživatelské sestavy koexistují se systémovými sestavami.

Naplánujte si investice do toho, jak nahlásit stav a reagovat na něj během návrhu velké cloudové služby. Tato počáteční investice usnadňuje ladění, monitorování a provoz služby.

## <a name="health-states"></a>Stavy zdraví
Service Fabric používá tři stavy k popisu, zda je entita v pořádku nebo ne: OK, upozornění a chyba. Všechny sestavy odeslané do úložiště stavu musí určit jeden z těchto stavů. Výsledek hodnocení stavu je jedním z těchto stavů.

Možné [zdravotní stavy](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthstate) jsou:

* **Ok**. Entita je v pořádku. Nejsou hlášeny žádné známé problémy o něm nebo jeho podřízených (pokud je to možné).
* **Upozornění**. Entita má některé problémy, ale stále může fungovat správně. Například dochází ke zpoždění, ale zatím nezpůsobují žádné funkční problémy. V některých případech se může varovný stav opravit bez vnějšího zásahu. V těchto případech zprávy o zdraví zvyšují povědomí a poskytují přehled o tom, co se děje. V ostatních případech může dojít k degradaci varovného stavu na závažný problém bez zásahu uživatele.
* **Chyba**. Entita není v pořádku. Je třeba přijmout opatření k opravě stavu entity, protože nemůže správně fungovat.
* **Neznámé**. Entita neexistuje v úložišti stavu. Tento výsledek lze získat z distribuovaných dotazů, které slučují výsledky z více součástí. Například dotaz seznamu polí get přejde do **služby FailoverManager**, **ClusterManager**a **HealthManager**; dotaz get seznam aplikací přejde do **clustermanageru** a **healthmanageru**. Tyto dotazy sloučit výsledky z více součástí systému. Pokud jiná systémová součást vrátí entitu, která není přítomna v úložišti stavu, sloučený výsledek má neznámý stav. Entita není v úložišti, protože sestavy stavu ještě nebyly zpracovány nebo entita byla po odstranění vyčištěna.

## <a name="health-policies"></a>Zdravotní politiky
Úložiště stavu používá zásady stavu k určení, zda je entita v pořádku na základě svých sestav a jejích podřízených.

> [!NOTE]
> Zásady stavu lze zadat v manifestu clusteru (pro vyhodnocení stavu clusteru a uzlu) nebo v manifestu aplikace (pro vyhodnocení aplikace a všechny jeho podřízené). Žádosti o vyhodnocení stavu může také předat vlastní zásady hodnocení stavu, které se používají pouze pro toto hodnocení.
> 
> 

Ve výchozím nastavení Service Fabric používá přísná pravidla (vše musí být v pořádku) pro nadřazený podřízený hierarchický vztah. Pokud i jeden z dětí má jednu událost není v pořádku, nadřazený je považován za není v pořádku.

### <a name="cluster-health-policy"></a>Zásady stavu clusteru
[Zásady stavu clusteru](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy) se používají k vyhodnocení stavu clusteru a stavu uzlu. Zásadu lze definovat v manifestu clusteru. Pokud není k dispozici, použije se výchozí zásada (nulová tolerovaná selhání).
Zásady stavu clusteru obsahují:

* [Zvažte WarningAsError](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.considerwarningaserror). Určuje, zda se má sestavám stavu upozornění považovat za chyby během hodnocení stavu. Výchozí hodnota: false.
* [MaxPercentUnhealthyApplications](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.maxpercentunhealthyapplications). Určuje maximální tolerované procento aplikací, které mohou být nefunkční, než je cluster považován za chybný.
* [MaxPercentUnhealthyNodes](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.maxpercentunhealthynodes). Určuje maximální tolerované procento uzlů, které mohou být nefunkční, než je cluster považován za chybný. Ve velkých clusterech jsou některé uzly vždy mimo nebo mimo pro opravy, takže toto procento by mělo být nakonfigurováno tak, aby to tolerovalo.
* [ApplicationTypeHealthPolicyMap](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.applicationtypehealthpolicymap). Mapování zásad stavu typu aplikace lze použít při hodnocení stavu clusteru k popisu speciálních typů aplikací. Ve výchozím nastavení jsou všechny aplikace vloženy do fondu a vyhodnoceny pomocí aplikace MaxPercentUnhealthyApplications. Pokud některé typy aplikací by měly být zpracovány odlišně, mohou být převzaty z globálního fondu. Místo toho jsou vyhodnoceny proti procentům přidruženým k názvu typu aplikace v mapě. Například v clusteru existují tisíce aplikací různých typů a několik instancí aplikace řízení zvláštního typu aplikace. Řídicí aplikace by nikdy neměly být chybné. Můžete zadat globální MaxPercentUnhealthyApplications na 20 % tolerovat některé chyby, ale pro typ aplikace "ControlApplicationType" nastavit MaxPercentUnhealthyApplications na 0. Tímto způsobem, pokud některé z mnoha aplikací nejsou v pořádku, ale pod globální procento není v pořádku, clusteru by být vyhodnocena upozornění. Stav upozornění nemá vliv na upgrade clusteru nebo jiné monitorování aktivované stavem chyb. Ale i jedna řídicí aplikace v chybě by clusteru není v pořádku, který aktivuje vrátit zpět nebo pozastaví upgrade clusteru, v závislosti na konfiguraci upgradu.
  Pro typy aplikací definované v mapě jsou všechny instance aplikace převzaty z globálního fondu aplikací. Jsou vyhodnocovány na základě celkového počtu aplikací typu aplikace pomocí konkrétní MaxPercentUnhealthyApplications z mapy. Všechny ostatní aplikace zůstávají v globálním fondu a jsou vyhodnocovány s MaxPercentUnhealthyApplications.

Následující příklad je výňatek z manifestu clusteru. Chcete-li definovat položky v mapě typu aplikace, předponu název parametru s "ApplicationTypeMaxPercentUnhealthyApplications-", následovaný názvem typu aplikace.

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
[Zásady stavu aplikace](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthpolicy) popisuje, jak se provádí hodnocení událostí a agregace podřízených stavů pro aplikace a jejich podřízené aplikace. Může být definovánv manifestu aplikace **ApplicationManifest.xml**v balíčku aplikace. Pokud nejsou zadány žádné zásady, Service Fabric předpokládá, že entita není v pořádku, pokud má zprávu o stavu nebo podřízený ve stavu upozornění nebo chyby.
Konfigurovatelné zásady jsou:

* [Zvažte WarningAsError](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.considerwarningaserror). Určuje, zda se má sestavám stavu upozornění považovat za chyby během hodnocení stavu. Výchozí hodnota: false.
* [MaxPercentUnhealthyDeployedApplications](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthpolicy.maxpercentunhealthydeployedapplications). Určuje maximální tolerované procento nasazených aplikací, které mohou být nefunkční, než je aplikace považována za chybnou. Toto procento se vypočítá vydělením počtu nefunkčních nasazených aplikací počtem uzlů, na které jsou aplikace aktuálně nasazeny v clusteru. Výpočtem zaokrouhluje nahoru tolerovat jednu poruchu na malý počet uzlů. Výchozí procento: nula.
* [DefaultServiceTypeHealthPolicy](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthpolicy.defaultservicetypehealthpolicy). Určuje výchozí zásadu stavu typu služby, která nahradí výchozí zásady stavu pro všechny typy služeb v aplikaci.
* [ServiceTypeHealthPolicyMap](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthpolicy.servicetypehealthpolicymap). Obsahuje mapu zásad stavu služby podle typu služby. Tyto zásady nahrazují výchozí zásady stavu typu služby pro každý zadaný typ služby. Například pokud aplikace má bezstavový typ služby brány a stavového typu služby motoru, můžete nakonfigurovat zásady stavu pro jejich vyhodnocení odlišně. Když zadáte zásady pro typ služby, můžete získat podrobnější kontrolu stavu služby.

### <a name="service-type-health-policy"></a>Zásady stavu typu služby
[Zásady stavu typu služby](https://docs.microsoft.com/dotnet/api/system.fabric.health.servicetypehealthpolicy) určují, jak vyhodnotit a agregovat služby a podřízené služby. Zásady obsahují:

* [MaxPercentUnhealthyPartitionsPerService](https://docs.microsoft.com/dotnet/api/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthypartitionsperservice). Určuje maximální tolerované procento oddílů není v pořádku před službou je považován za není v pořádku. Výchozí procento: nula.
* [MaxPercentUnhealthyReplicasPerPartition](https://docs.microsoft.com/dotnet/api/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthyreplicasperpartition). Určuje maximální tolerované procento replik není v pořádku před oddíl je považován za není v pořádku. Výchozí procento: nula.
* [MaxPercentUnhealthyServices](https://docs.microsoft.com/dotnet/api/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthyservices). Určuje maximální tolerované procento nefunkčních služeb před tím, než je aplikace považována za nefunkční. Výchozí procento: nula.

Následující příklad je výňatek z manifestu aplikace:

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

## <a name="health-evaluation"></a>Hodnocení zdravotního stavu
Uživatelé a automatizované služby mohou kdykoli vyhodnotit stav pro libovolnou entitu. K vyhodnocení stavu entity úložiště stavu agreguje všechny sestavy stavu entity a vyhodnotí všechny její podřízené položky (pokud je to možné). Algoritmus agregace stavu používá zásady stavu, které určují, jak vyhodnotit sestavy stavu a jak agregovat stav podřízené (pokud je to možné).

### <a name="health-report-aggregation"></a>Agregace sestavy stavu
Jedna entita může mít více zpráv o stavu odeslaných různými reportéry (součásti systému nebo sledovacími zařízeními) na různé vlastnosti. Agregace používá přidružené zásady stavu, zejména ConsiderWarningAsError člen aplikace nebo zásady stavu clusteru. ConsiderWarningAsError určuje, jak vyhodnotit upozornění.

Agregovaný stav je spuštěn *nejhorší* zprávy o stavu na entitě. Pokud existuje alespoň jedna zpráva o stavu chyb, agregovaný stav je chyba.

![Agregace sestavy stavu se zprávou o chybě.][2]

Entita stavu, která má jednu nebo více zpráv o stavu chyb, je vyhodnocena jako chyba. Totéž platí pro zprávu o stavu vypršela, bez ohledu na jeho stav stavu.

[2]: ./media/service-fabric-health-introduction/servicefabric-health-report-eval-error.png

Pokud neexistují žádné zprávy o chybách a jedno nebo více upozornění, agregovaný stav je buď upozornění nebo chyba, v závislosti na příznaku zásad considerWarningAsError.

![Agregace sestavy stavu s varovnou zprávou a ConsiderWarningAsError false.][3]

Agregace sestavy stavu s varovnou sestavou a ConsiderWarningAsError nastavena na false (výchozí).

[3]: ./media/service-fabric-health-introduction/servicefabric-health-report-eval-warning.png

### <a name="child-health-aggregation"></a>Agregace zdraví dětí
Agregovaný stav entity odráží stav podřízeného stavu (pokud je to možné). Algoritmus pro agregaci stavů podřízených používá zásady stavu platné na základě typu entity.

![Podřízené entity agregace stavu.][4]

Agregace podřízených na základě zásad stavu.

[4]: ./media/service-fabric-health-introduction/servicefabric-health-hierarchy-eval.png

Poté, co úložiště stavu vyhodnotila všechny podřízené děti, agreguje jejich stavy na základě nakonfigurovanémaximální procento nezdravých dětí. Toto procento je převzato ze zásady založené na entitě a podřízeném typu.

* Pokud všechny podřízené mají ok stavy, podřízené agregované stav je ok.
* Pokud děti mají ok a upozornění stavy, podřízené agregované stav je upozornění.
* Pokud existují podřízené objekty s chybovými stavy, které nerespektují maximální povolené procento nezdravých dětí, agregovaný nadřazený stav je chyba.
* Pokud podřízené objekty s chybovými stavy respektují maximální povolené procento nezdravých dětí, je upozornění agregovaný nadřazený stav.

## <a name="health-reporting"></a>Zprávy o stavu
Systémové součásti, aplikace System Fabric a interní/externí sledovací zařízení mohou vykazovat entity Service Fabric. Reportéři provádějí *místní* rozhodnutí o stavu sledovaných subjektů na základě podmínek, které monitorují. Nemusí se dívat na žádné globální stav nebo agregovaná data. Požadované chování je mít jednoduché reportéry, a ne složité organismy, které je třeba se podívat na mnoho věcí, aby vyvodit, jaké informace poslat.

Chcete-li odeslat data o stavu do úložiště stavu, musí reportér identifikovat postiženou entitu a vytvořit sestavu stavu. Chcete-li odeslat sestavu, použijte [fabricclient.HealthClient.ReportHealth](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.reporthealth) API, `Partition` `CodePackageActivationContext` sestavy rozhraní API stavu vystavené na objekty nebo, Rutiny prostředí PowerShell nebo REST.

### <a name="health-reports"></a>Zdravotní zprávy
[Sestavy stavu](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthreport) pro každou entitu v clusteru obsahují následující informace:

* **SourceId**. Řetězec, který jednoznačně identifikuje reportéra události stavu.
* **Identifikátor entity**. Identifikuje entitu, ve které je sestava použita. Liší se v závislosti na [typu entity](service-fabric-health-introduction.md#health-entities-and-hierarchy):
  
  * Clusteru. Žádné.
  * Uzel. Název uzlu (řetězec).
  * Aplikace. Název aplikace (URI). Představuje název instance aplikace nasazené v clusteru.
  * Služby. Název služby (URI). Představuje název instance služby nasazené v clusteru.
  * Oddíl. ID oddílu (GUID). Představuje jedinečný identifikátor oddílu.
  * Replika. ID repliky stavové služby nebo ID instance bezstavové služby (INT64).
  * Nasazená aplikace. Název aplikace (URI) a název uzlu (řetězec).
  * DeployedServicePackage. Název aplikace (URI), název uzlu (řetězec) a název manifestu služby (řetězec).
* **Vlastnost**. *Řetězec* (není pevný výčet), který umožňuje zařazování události stavu pro konkrétní vlastnost entity. Například reportér A může hlásit stav vlastnosti Node01 "Storage" a reportér B může hlásit stav vlastnosti Připojení uzlu01. V úložišti stavu jsou tyto sestavy považovány za samostatné události stavu pro entitu Node01.
* **Popis**. Řetězec, který umožňuje reportérovi poskytnout podrobné informace o události stavu. **SourceId**, **Property**a **HealthState** by mělplně popsat sestavu. Popis přidá informace o sestavě čitelné pro člověka. Text usnadňuje správcům a uživatelům pochopit sestavu stavu.
* **HealthState**. [Výčet,](service-fabric-health-introduction.md#health-states) který popisuje stav sestavy. Přijaté hodnoty jsou OK, Upozornění a Chyba.
* **TimeToLive**. Časový rozsah, který označuje, jak dlouho je platná sestava stavu. Ve spojení s **RemoveWhenExpired**umožňuje úložiště stavu vědět, jak vyhodnotit události, jejichž platnost vypršela. Ve výchozím nastavení je hodnota nekonečná a sestava je platná navždy.
* **RemoveWhenExpired**. Logická. Pokud je nastavena na hodnotu true, sestava stavu vypršela automaticky odebrána z úložiště stavu a sestava nemá vliv na hodnocení stavu entity. Používá se v případě, že je sestava platná pouze po určitou dobu a zapisovatelní k němu nemusí explicitně vyklidit. Používá se také k odstranění sestav z úložiště stavu (například sledovací zařízení se změní a zastaví odesílání sestav s předchozím zdrojem a vlastností). Může odeslat zprávu se stručným TimeToLive spolu s RemoveWhenExpired vymazat všechny předchozí stav z úložiště stavu. Pokud je hodnota nastavena na false, vypršela platnost sestavy je považována za chybu v hodnocení stavu. Falešná hodnota signalizuje úložiště stavu, které by měl zdroj pravidelně hlásit na tuto vlastnost. Pokud ne, tak musí být něco v nepořádku s hlídacím psem. Stav sledovacího zařízení je zachycen a považuje událost za chybu.
* **SequenceNumber**. Kladné celé číslo, které musí být stále rostoucí, představuje pořadí sestav. Úložiště stavu jej používá ke zjištění zastaralých sestav, které jsou přijaty pozdě z důvodu zpoždění sítě nebo jiných problémů. Sestava je odmítnuta, pokud je pořadové číslo menší nebo rovno naposledy použitému číslu pro stejnou entitu, zdroj a vlastnost. Pokud není zadán, pořadové číslo je generováno automaticky. Je nutné umístit pořadové číslo pouze při vykazování přechodů stavu. V takovém případě musí zdroj zapamatovat, které sestavy odeslal, a uchovávat informace pro obnovení při převzetí služeb při selhání.

Tyto čtyři části informací – SourceId, identifikátor entity, Vlastnost a HealthState – jsou vyžadovány pro každou sestavu stavu. Řetězec SourceID nesmí začínat předponou**System.**", která je vyhrazena pro systémové sestavy. Pro stejnou entitu existuje pouze jedna sestava pro stejný zdroj a vlastnost. Více sestav pro stejný zdroj a vlastnost přepsat navzájem, buď na straně klienta stavu (pokud jsou dávkové) nebo na straně obchodu se stavem. Nahrazení je založeno na pořadových číslech; novější sestavy (s vyššími pořadovými čísly) nahrazují starší sestavy.

### <a name="health-events"></a>Zdravotní události
Interně úložiště stavu udržuje [události stavu](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthevent), které obsahují všechny informace ze sestav a další metadata. Metadata zahrnují čas, kdy byla sestava předána klientovi stavu, a čas, kdy byla změněna na straně serveru. Události stavu jsou [vráceny dotazy stavu](service-fabric-view-entities-aggregated-health.md#health-queries).

Přidaná metadata obsahují:

* **SourceUtcTimestamp**. Čas, kdy byla zpráva předána klientovi stavu (Koordinovaný světový čas).
* **LastModifiedUtcTimestamp**. Čas poslední změny sestavy na straně serveru (Koordinovaný světový čas).
* **Platnost isexpired**. Příznak označující, zda vypršela platnost sestavy při spuštění dotazu úložištěm stavu. Platnost události může být ukončena pouze v případě, že je nepravdivá. V opačném případě událost není vrácena dotazem a je odebrána z úložiště.
* **LastOkTransitionat**, **lastWarningTransitionat**, **lasterrortransitionat**. Čas posledního pro přechody OK/upozornění/chyby. Tato pole poskytují historii přechodů stavu pro událost.

Pole přechodu stavu lze použít pro chytřejší výstrahy nebo "historické" informace o událostech stavu. Umožňují scénáře, jako jsou:

* Upozornit, pokud vlastnost byla na upozornění/chyba pro více než X minut. Kontrola stavu po určitou dobu zabraňuje upozornění mj. Například výstraha, pokud stav byl upozornění pro více než pět minut lze přeložit do (HealthState == Upozornění a nyní - LastWarningTransitionTime > 5 minut).
* Upozorněte pouze na podmínky, které se změnily v posledních X minutách. Pokud byla sestava již chybována před určeným časem, může být ignorována, protože již byla signalizována dříve.
* Pokud vlastnost přepíná mezi upozorněním a chybou, zjistěte, jak dlouho je v nepořádku (to znamená, že není OK). Například výstraha, pokud vlastnost nebyla v pořádku více než pět minut lze přeložit do (HealthState != Ok a nyní - LastOkTransitionTime > 5 minut).

## <a name="example-report-and-evaluate-application-health"></a>Příklad: Sestavte a vyhodnoťte stav aplikace
Následující příklad odešle zprávu o stavu prostřednictvím prostředí PowerShell na **prostředkůch aplikace:/WordCount** ze zdroje **MyWatchdog**. Sestava stavu obsahuje informace o vlastnosti stavu "dostupnost" ve stavu chyby, s nekonečné TimeToLive. Pak se dotazuje stavu aplikace, která vrátí agregované chyby stavu a hlášené události stavu v seznamu událostí stavu.

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
Model stavu umožňuje cloudové služby a základní platformu Service Fabric škálovat, protože monitorování a stanovení stavu jsou distribuovány mezi různé monitory v rámci clusteru.
Jiné systémy mají jednu centralizovanou službu na úrovni clusteru, která analyzuje všechny *potenciálně* užitečné informace vyzařované službami. Tento přístup brání jejich škálovatelnosti. Také jim neumožňuje shromažďovat konkrétní informace, které by jim pomohly identifikovat problémy a potenciální problémy co nejblíže hlavní příčině.

Model stavu se používá hojně pro monitorování a diagnostiku, pro vyhodnocení stavu clusteru a aplikace a pro sledované upgrady. Jiné služby používají data o stavu k provádění automatických oprav, vytváření historie stavu clusteru a vydávání výstrah za určitých podmínek.

## <a name="next-steps"></a>Další kroky
[Zobrazit sestavy stavu service fabric](service-fabric-view-entities-aggregated-health.md)

[Použití sestav stavu systému pro řešení potíží](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Jak nahlásit a zkontrolovat stav služby](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Přidání vlastních sestav stavu service fabric](service-fabric-report-health.md)

[Místní monitorování a diagnostika služeb](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Upgrade aplikace Service Fabric](service-fabric-application-upgrade.md)

