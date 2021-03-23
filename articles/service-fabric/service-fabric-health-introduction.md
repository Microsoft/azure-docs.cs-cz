---
title: Monitorování stavu v Service Fabric
description: Úvod do modelu sledování stavu služby Azure Service Fabric, který poskytuje monitorování clusteru a jeho aplikací a služeb.
author: georgewallace
ms.topic: conceptual
ms.date: 2/28/2018
ms.author: gwallace
ms.openlocfilehash: 6c96651fa48acc2f88658148c7e60be2f3fa09da
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2021
ms.locfileid: "104800155"
---
# <a name="introduction-to-service-fabric-health-monitoring"></a>Úvod do monitorování stavu Service Fabric
Azure Service Fabric zavádí model stavu, který poskytuje bohatě, flexibilní a rozšiřitelné vyhodnocení stavu a vytváření sestav. Model umožňuje monitorovat stav clusteru a služby, které jsou v něm spuštěné, do téměř v reálném čase. Můžete snadno získat informace o stavu a opravit případné problémy, které se budou zastarat, a způsobit obrovské výpadky. V typickém modelu odesílají služby sestavy na základě místních zobrazení a tyto informace jsou agregované tak, aby poskytovaly celkové zobrazení na úrovni clusteru.

Service Fabric komponenty používají tento bohatý model stavu k hlášení jejich aktuálního stavu. Stejný mechanismus můžete použít k hlášení stavu z vašich aplikací. Pokud investujete do vysoce kvalitních sestav o stavu, které zachycují vaše vlastní podmínky, můžete zjistit a opravit problémy pro vaši spuštěnou aplikaci mnohem snadněji.

> [!NOTE]
> Provedli jsme spuštění subsystému stavu, aby se vyřešila nutnost sledovaných upgradů. Service Fabric poskytuje monitorované aplikace a upgrady clusterů, které zajišťují plnou dostupnost, bez výpadků a minimální zásahy uživatele. Pro dosažení těchto cílů upgrade kontroluje stav na základě konfigurovaných zásad upgradu. Upgrade může pokračovat pouze v případě, že se stav doohledí na požadovaný práh. V opačném případě se upgrade automaticky vrátí nebo pozastaví, aby správcům poskytoval možnost tyto problémy vyřešit. Další informace o upgradech aplikací najdete v [tomto článku](service-fabric-application-upgrade.md).
> 
> 

## <a name="health-store"></a>Úložiště stavu
Health Store uchovává informace týkající se entit v clusteru, aby bylo snadné ho načíst a vyhodnotit. Implementuje se jako Service Fabric trvalá stavová služba, aby se zajistila vysoká dostupnost a škálovatelnost. Health Store je součástí aplikace **Fabric:/System** a je k dispozici, když je cluster spuštěný a spuštěný.

## <a name="health-entities-and-hierarchy"></a>Entity a hierarchie stavu
Entity Health jsou uspořádány do logické hierarchie, která zachycuje interakce a závislosti mezi různými entitami. Health Store automaticky sestaví entity stavu a hierarchii na základě sestav přijatých z komponent Service Fabric.

Entity stavu zrcadlí Service Fabric entit. (Například **entita aplikace Health** se shoduje s instancí aplikace nasazenou v clusteru, zatímco **entita uzlu stavu** odpovídá Service Fabricmu uzlu clusteru.) Hierarchie stavu zachycuje interakce systémových entit a je základem pro pokročilé hodnocení stavu. Informace o klíčových Service Fabric konceptech najdete v článku [Service Fabric Technical Overview](service-fabric-technical-overview.md). Další informace o aplikaci naleznete v tématu [Service Fabric Application model](service-fabric-application-model.md).

Entity a hierarchie stavu umožňují, aby cluster a aplikace byly efektivně hlášeny, laděny a monitorovány. Model stavu poskytuje přesné a *podrobné* znázornění stavu mnoha přesunů v clusteru.

![Entity stavu.][1]
Entity stavu uspořádané v hierarchii na základě vztahů nadřazenosti a podřízenosti.

[1]: ./media/service-fabric-health-introduction/servicefabric-health-hierarchy.png

Jsou to tyto entity:

* **Cluster**. Představuje stav clusteru Service Fabric. Sestavy o stavu clusteru popisují podmínky ovlivňující celý cluster. Tyto podmínky ovlivňují více entit v clusteru nebo samotném clusteru. V závislosti na této podmínce nemůže zpravodaj zúžit problém na jeden nebo více nezdravých podřízených objektů. Mezi příklady patří mozek z rozdělení clusteru z důvodu problémů s vytvářením oddílů sítě nebo komunikace.
* **Uzel**. Představuje stav uzlu Service Fabric. Sestavy o stavu uzlu popisují podmínky, které mají vliv na funkčnost uzlu. Obvykle mají vliv na všechny nasazené entity, které jsou v něm spuštěné. Mezi příklady patří uzel nedostatek místa na disku (nebo jiné vlastnosti v rámci počítače, například paměť, připojení) a uzel mimo provoz. Entita uzlu je identifikována názvem uzlu (řetězec).
* **Aplikace**. Představuje stav instance aplikace spuštěné v clusteru. Sestavy o stavu aplikace popisují podmínky ovlivňující celkový stav aplikace. Nemůžou být zúžené dolů na jednotlivé podřízené položky (služby nebo nasazené aplikace). Mezi příklady patří koncová interakce mezi různými službami v aplikaci. Entita aplikace je identifikována názvem aplikace (URI).
* **Služba**. Představuje stav služby spuštěné v clusteru. Sestavy služby Service Health popisují podmínky ovlivňující celkový stav služby. Zpravodaj nemůže zúžit problém na oddíl nebo repliku, která není v pořádku. Příkladem může být konfigurace služby (například port nebo externí sdílení souborů), která způsobuje problémy pro všechny oddíly. Entita služby je identifikována názvem služby (URI).
* **Oddíl**. Představuje stav oddílu služby. Sestavy o stavu oddílů popisují podmínky, které mají vliv na celou sadu replik. Mezi příklady patří i v případě, že počet replik je pod počtem cílů a když je oddíl ve ztrátě kvora. Entita oddílu je identifikovaná IDENTIFIKÁTORem oddílu (GUID).
* **Replika**. Představuje stav repliky stavové služby nebo instance bezstavové služby. Replika je nejmenší jednotka, na kterou se můžou v rámci aplikace vykazovat sledovací a systémové komponenty. V případě stavových služeb jsou mezi příklady primární replika, která nemůže replikovat operace do sekundárních a pomalých replikací. Bezstavová instance se také může hlásit, když má dostatek prostředků nebo dochází k problémům s připojením. Entita repliky je identifikovaná IDENTIFIKÁTORem oddílu (GUID) a replikou nebo ID instance (Long).
* **DeployedApplication**. Představuje stav *aplikace spuštěné na uzlu*. Nasazené sestavy o stavu aplikace popisují podmínky specifické pro aplikaci v uzlu, které nelze zúžit na balíčky služby nasazené na stejném uzlu. Příklady zahrnují chyby, když se balíček aplikace nedá stáhnout v tomto uzlu a problémy s nastavením objektů zabezpečení aplikace na uzlu. Nasazená aplikace je identifikována názvem aplikace (URI) a názvem uzlu (String).
* **DeployedServicePackage**. Představuje stav balíčku služby spuštěného na uzlu v clusteru. Popisuje podmínky specifické pro balíček služby, který nemá vliv na ostatní balíčky služby na stejném uzlu pro stejnou aplikaci. Příklady zahrnují balíček kódu v balíčku služby, který nelze spustit, a konfigurační balíček, který nelze číst. Nasazený balíček služby je identifikovaný názvem aplikace (URI), názvem uzlu (řetězcem), názvem manifestu služby (řetězcem) a ID aktivace balíčku služby (řetězec).

Členitost modelu stavu umožňuje snadno rozpoznat a opravit problémy. Pokud například služba neodpovídá, je vhodné ohlásit, že instance aplikace není v pořádku. Vytváření sestav na této úrovni není ideální, ale vzhledem k tomu, že problém nemusí mít vliv na všechny služby v této aplikaci. Tato sestava by se měla použít na službu, která není v pořádku, nebo na konkrétní podřízený oddíl, pokud se na tento oddíl odkazuje více informací. Data se automaticky provedou přes hierarchii a na úrovni služby a aplikace jsou viditelné oddíly, které nejsou v pořádku. Tato agregace pomáhá určit a vyřešit hlavní příčinu problému rychleji.

Hierarchie stavů se skládá z vztahů nadřazenosti a podřízenosti. Cluster se skládá z uzlů a aplikací. Aplikace mají služby a nasazené aplikace. Nasazené aplikace mají nasazené balíčky služeb. Služba má oddíly a každý oddíl má jednu nebo více replik. Mezi uzly a nasazenými entitami je zvláštní vztah. Uzel není v pořádku, jak oznamuje součást systému jeho autority, služba Správce převzetí služeb při selhání, má vliv na nasazené aplikace, balíčky služeb a repliky, které jsou na ní nasazené.

Hierarchie stavů představuje nejnovější stav systému založený na nejnovějších sestavách stavu, což je téměř informace v reálném čase.
Interní a externí sledovací zařízení mohou hlásit stejné entity na základě logiky specifické pro aplikaci nebo pro vlastní monitorované podmínky. Sestavy uživatelů existují společně se systémovými sestavami.

Naplánujte investici, jak ohlásit a reagovat na stav při návrhu velké cloudové služby. Tato přední investice usnadňuje ladění, sledování a provoz služby.

## <a name="health-states"></a>Stav
Service Fabric používá tři stavové stavy k popisu, jestli je entita v pořádku, nebo ne: OK, varování a chyba. Všechny sestavy odeslané do Health Store musí určovat jeden z těchto stavů. Výsledkem vyhodnocení stavu je jeden z těchto stavů.

Možné [stavy](/dotnet/api/system.fabric.health.healthstate) :

* **OK**. Entita je v pořádku. Neexistují žádné známé problémy, které jsou na něm hlášeny (Pokud je to možné).
* **Upozornění**: Entita obsahuje nějaké problémy, ale přesto může fungovat správně. Existují například prodlevy, ale ještě nezpůsobují žádné funkční problémy. V některých případech se může podmínka upozornění opravit bez vnějšího zásahu. V těchto případech sestavy o stavu zvyšují povědomí a poskytují přehled o tom, co se prochází. V jiných případech může stav varování snížit závažnost problému bez zásahu uživatele.
* **Chyba**. Entita není v pořádku. Měla by být provedena akce pro opravu stavu entity, protože nemůže správně fungovat.
* **Neznámé**: Entita v Health Store neexistuje. Tento výsledek lze získat z distribuovaných dotazů, které sloučí výsledky z více součástí. Například dotaz získat seznam uzlů přejde na **FailoverManager**, **ClusterManager** a **HealthManager**; získat dotaz na seznam aplikací přejde na **ClusterManager** a **HealthManager**. Tyto dotazy sloučí výsledky z více systémových součástí. Pokud jiná systémová součást vrátí entitu, která není přítomna v Health Store, má sloučený výsledek neznámý stav. Entita není v úložišti, protože sestavy o stavu ještě nejsou zpracované nebo se entita vyčistila po jejím odstranění.

## <a name="health-policies"></a>Zásady stavu
Health Store používá zásady stavu k určení, jestli je entita v pořádku, na základě jejich sestav a jejích podřízených položek.

> [!NOTE]
> Zásady stavu lze zadat v manifestu clusteru (pro vyhodnocení stavu clusteru a uzlu) nebo v manifestu aplikace (pro vyhodnocení aplikace a všechny její podřízené položky). Žádosti o vyhodnocení stavu můžou taky předat vlastní zásady hodnocení stavu, které se používají jenom pro toto vyhodnocení.
> 
> 

Ve výchozím nastavení Service Fabric používá striktní pravidla (vše musí být v pořádku) pro hierarchický vztah nadřazený-podřízený. Pokud dokonce jedna z podřízených prvků obsahuje jednu událost, která není v pořádku, považuje se nadřazená položka za není v pořádku.

### <a name="cluster-health-policy"></a>Zásady stavu clusteru
[Zásady stavu clusteru](/dotnet/api/system.fabric.health.clusterhealthpolicy) se používají k vyhodnocení stavu clusteru a stavu uzlu. Zásady je možné definovat v manifestu clusteru. Pokud není k dispozici, je použita výchozí zásada (počet nedovolených selhání).

Zásada stavu clusteru obsahuje:

* [ConsiderWarningAsError](/dotnet/api/system.fabric.health.clusterhealthpolicy.considerwarningaserror). Určuje, jestli se mají při hodnocení stavu považovat zprávy o stavu s varováním za chyby. Výchozí hodnota: false.
* [MaxPercentUnhealthyApplications](/dotnet/api/system.fabric.health.clusterhealthpolicy.maxpercentunhealthyapplications). Určuje maximální povolený procentuální podíl aplikací, které můžou být chybné, než se cluster bude považovat za chybu.
* [MaxPercentUnhealthyNodes](/dotnet/api/system.fabric.health.clusterhealthpolicy.maxpercentunhealthynodes). Určuje maximální povolený procentuální podíl uzlů, které mohou být v nesprávném stavu, než se cluster bude považovat za chybu. Ve velkých clusterech jsou některé uzly u oprav vždycky mimo provoz, takže toto procento by mělo být nakonfigurované tak, aby to bylo tolerováno.
* [ApplicationTypeHealthPolicyMap](/dotnet/api/system.fabric.health.clusterhealthpolicy.applicationtypehealthpolicymap). Mapování zásad stavu aplikace lze použít při vyhodnocení stavu clusteru k popisu speciálních typů aplikací. Ve výchozím nastavení jsou všechny aplikace vloženy do fondu a vyhodnocovány pomocí MaxPercentUnhealthyApplications. Pokud se některé typy aplikací mají zpracovávat odlišně, můžou se vycházet z globálního fondu. Místo toho jsou vyhodnocovány proti procentům přidruženým k názvu typu aplikace na mapě. Například v clusteru existují tisíce aplikací různých typů a několik instancí aplikace pro řízení speciálního typu aplikace. Řídicí aplikace by nikdy neměly být v chybě. Můžete zadat globální MaxPercentUnhealthyApplications na 20% pro tolerovatcí některé chyby, ale pro typ aplikace "ControlApplicationType" nastaví MaxPercentUnhealthyApplications na 0. Tímto způsobem platí, že pokud některé z mnoha aplikací nejsou v pořádku, ale pod globálním procentem není v pořádku, cluster se vyhodnotí jako varování. Stav upozornění nemá vliv na upgrade clusteru ani na jiné monitorování aktivované chybovým stavem. I když ale jedna aplikace ovládacího prvku v chybě způsobí, že cluster není v pořádku, což aktivuje odvolání nebo pozastaví upgrade clusteru v závislosti na konfiguraci upgradu.
  Pro typy aplikací definované v mapě se všechny instance aplikace vyberou z globálního fondu aplikací. Jsou vyhodnocovány na základě celkového počtu aplikací typu aplikace pomocí konkrétního MaxPercentUnhealthyApplications z mapy. Všechny zbývající aplikace zůstanou v globálním fondu a vyhodnocují se pomocí MaxPercentUnhealthyApplications.

  Následující příklad je výňatek z manifestu clusteru. Chcete-li definovat položky v mapě typu aplikace, zadejte předponu názvu parametru pomocí "ApplicationTypeMaxPercentUnhealthyApplications-" následovaný názvem typu aplikace.

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

* [NodeTypeHealthPolicyMap](/dotnet/api/system.fabric.health.clusterhealthpolicy.nodetypehealthpolicymap). Mapování zásad stavu uzlu lze použít při vyhodnocení stavu clusteru k popisu speciálních typů uzlů. Typy uzlů jsou vyhodnocovány proti procentům přidruženým k názvu typu uzlu v mapě. Nastavení této hodnoty nemá žádný vliv na Globální fond uzlů používaných pro `MaxPercentUnhealthyNodes` . Cluster má například stovky uzlů různých typů a několik typů uzlů, které hostují důležitou práci. Žádný uzel v tomto typu by neměl být mimo provoz. Můžete zadat globální `MaxPercentUnhealthyNodes` až 20% pro tolerování některých selhání pro všechny uzly, ale pro typ uzlu nastavte na `SpecialNodeType` `MaxPercentUnhealthyNodes` 0. Tímto způsobem platí, že pokud některý z mnoha uzlů není v pořádku, ale pod globálním procentem není v pořádku, cluster se vyhodnotí jako ve stavu varování. Stav upozornění nebude mít vliv na upgrade clusteru ani na jiné monitorování aktivované chybovým stavem. Ale i jeden uzel typu `SpecialNodeType` v chybovém stavu způsobí, že cluster není v pořádku a může vrátit zpět nebo pozastavit upgrade clusteru v závislosti na konfiguraci upgradu. Naopak když nastavíte globální `MaxPercentUnhealthyNodes` omezení na 0 a nastavíte `SpecialNodeType` maximální procento uzlů, které nejsou v pořádku, na 100 s jedním uzlem typu `SpecialNodeType` v chybovém stavu, bude cluster stále v chybovém stavu, protože globální omezení je v tomto případě přísnější. 

  Následující příklad je výňatek z manifestu clusteru. Chcete-li definovat položky v mapě typu uzlu, zadejte předponu názvu parametru pomocí "NodeTypeMaxPercentUnhealthyNodes-" a za ním název typu uzlu.

  ```xml
  <FabricSettings>
    <Section Name="HealthManager/ClusterHealthPolicy">
      <Parameter Name="ConsiderWarningAsError" Value="False" />
      <Parameter Name="MaxPercentUnhealthyApplications" Value="20" />
      <Parameter Name="MaxPercentUnhealthyNodes" Value="20" />
      <Parameter Name="NodeTypeMaxPercentUnhealthyNodes-SpecialNodeType" Value="0" />
    </Section>
  </FabricSettings>
  ```

### <a name="application-health-policy"></a>Zásada stavu aplikace
[Zásady stavu aplikace](/dotnet/api/system.fabric.health.applicationhealthpolicy) popisují, jak se provádí vyhodnocení agregací událostí a podřízených stavů pro aplikace a jejich podřízené objekty. Může být definován v manifestu aplikace, **ApplicationManifest.xml** v balíčku aplikace. Pokud nejsou zadány žádné zásady, Service Fabric předpokládá, že entita není v pořádku, pokud má zprávu o stavu nebo podřízenou položku ve stavu upozornění nebo chyba.
Konfigurovatelné zásady jsou:

* [ConsiderWarningAsError](/dotnet/api/system.fabric.health.clusterhealthpolicy.considerwarningaserror). Určuje, jestli se mají při hodnocení stavu považovat zprávy o stavu s varováním za chyby. Výchozí hodnota: false.
* [MaxPercentUnhealthyDeployedApplications](/dotnet/api/system.fabric.health.applicationhealthpolicy.maxpercentunhealthydeployedapplications). Určuje maximální povolený procentuální podíl nasazených aplikací, které mohou být poškozeny, než je aplikace považována za chybu. Toto procento se počítá vydělením počtu chybně nasazených aplikací nad počtem uzlů, na kterých jsou aktuálně nasazené aplikace v clusteru. Výpočet se zaokrouhlí na jednu neúspěch na malých číslech uzlů. Výchozí procento: nula.
* [DefaultServiceTypeHealthPolicy](/dotnet/api/system.fabric.health.applicationhealthpolicy.defaultservicetypehealthpolicy). Určuje výchozí zásadu stavu pro typ služby, která nahrazuje výchozí zásady stavu pro všechny typy služeb v aplikaci.
* [ServiceTypeHealthPolicyMap](/dotnet/api/system.fabric.health.applicationhealthpolicy.servicetypehealthpolicymap). Poskytuje mapu zásad stavu služby na typ služby. Tyto zásady nahrazují výchozí zásady stavu typu služby pro každý zadaný typ služby. Pokud má aplikace například typ služby brány bez stavu a typ služby stavového stroje, můžete nakonfigurovat zásady stavu pro jejich vyzkoušení jinak. Když zadáte zásadu pro typ služby, můžete získat podrobnější kontrolu nad stavem služby.

### <a name="service-type-health-policy"></a>Zásada stavu pro typ služby
[Zásada stavu typu služby](/dotnet/api/system.fabric.health.servicetypehealthpolicy) určuje, jak se mají vyhodnocovat a agregovat služby a podřízené položky služeb. Zásada obsahuje:

* [MaxPercentUnhealthyPartitionsPerService](/dotnet/api/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthypartitionsperservice). Určuje maximální povolený procentní podíl nestavových oddílů, než se služba považuje za poškozenou. Výchozí procento: nula.
* [MaxPercentUnhealthyReplicasPerPartition](/dotnet/api/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthyreplicasperpartition). Určuje maximální tolerovánou procentuální hodnotu replik, které nejsou v pořádku, než se oddíl považuje za špatný. Výchozí procento: nula.
* [MaxPercentUnhealthyServices](/dotnet/api/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthyservices). Určuje maximální povolený procentuální podíl bezstavových služeb předtím, než se aplikace považuje za poškozenou. Výchozí procento: nula.

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

## <a name="health-evaluation"></a>Vyhodnocení stavu
Uživatelé a automatizované služby mohou kdykoli vyhodnotit stav jakékoli entity. Aby bylo možné vyhodnotit stav entity, Health Store agreguje všechny sestavy o stavu v entitě a vyhodnotí všechny její podřízené položky (Pokud je k dispozici). Algoritmus agregace stavu používá zásady stavu, které určují, jak vyhodnotit sestavy o stavu a jak agregovat podřízené stavy (Pokud je k dispozici).

### <a name="health-report-aggregation"></a>Agregace sestavy stavu
Jedna entita může mít několik sestav o stavu odesílaných různými sestavami (součásti systému nebo sledovacích zařízení) v různých vlastnostech. Agregace používá přidružené zásady stavu, zejména ConsiderWarningAsError člena zásad stavu aplikace nebo clusteru. ConsiderWarningAsError určuje, jak vyhodnotit upozornění.

Agregovaný stav je aktivovaný v *nejhorších* sestavách o stavu entity. Pokud existuje alespoň jedna zpráva o stavu chyby, agregovaný stav je chyba.

![Agregace sestavy stavu s chybovou zprávou.][2]

Entita o stavu s jednou nebo více zprávami o stavu chyby je vyhodnocena jako chyba. Totéž platí pro sestavu stavu s vypršenou platností, bez ohledu na její stav.

[2]: ./media/service-fabric-health-introduction/servicefabric-health-report-eval-error.png

Pokud neexistují žádné zprávy o chybách a jedno nebo více upozornění, je agregovaný stav v závislosti na příznaku zásady ConsiderWarningAsError buď varování, nebo chyba.

![Agregace sestavy stavu se sestavou upozornění a ConsiderWarningAsError false][3]

Agregace sestavy stavu se sestavou upozornění a ConsiderWarningAsError nastavenou na hodnotu false (výchozí).

[3]: ./media/service-fabric-health-introduction/servicefabric-health-report-eval-warning.png

### <a name="child-health-aggregation"></a>Agregace podřízeného stavu
Agregovaný stav entity odráží podřízené stavy (Pokud je k dispozici). Algoritmus pro agregaci podřízených stavů používá příslušné zásady stavu založené na typu entity.

![Agregace stavu podřízených entit][4]

Agregace podřízenosti na základě zásad stavu.

[4]: ./media/service-fabric-health-introduction/servicefabric-health-hierarchy-eval.png

Jakmile Health Store vyhodnotí všechny podřízené položky, agreguje jejich stavy na základě nakonfigurovaného maximálního procenta nestavových podřízených objektů. Toto procento se převezme ze zásad založených na typu entity a podřízeného typu.

* Pokud všechny podřízené položky mají stav OK, je podřízený agregovaný stav stavu OK.
* Pokud mají děti stav OK i upozornění, je podřízený agregovaný stav stavu upozornění.
* Pokud existují podřízené položky s chybovými stavy, které nerespektují maximální povolené procento nestavových podřízených objektů, agregovaný nadřazený stav je chyba.
* Pokud podřízené položky s chybovými stavy respektují maximální povolené procento špatných podřízených objektů, je agregovaný nadřazený stav upozornění.

## <a name="health-reporting"></a>Vytváření sestav o stavu
Komponenty systému, aplikace systémových prostředků infrastruktury a interní/externí sledovací zařízení mohou hlásit Service Fabric entit. Sestavy provádějí *místní* určení stavu monitorovaných entit na základě podmínek, které monitorují. Nemusí se pohlížet na žádná globální stav ani agregovaná data. Požadovaným chováním je jednoduché sestavování a ne komplexní organismy, které potřebují prohledat informace, které se mají odeslat, a získat tak mnoho věcí.

Aby bylo možné odesílat údaje o stavu Health Store, musí zpravodaj identifikovat ovlivněnou entitu a vytvořit sestavu o stavu. K odeslání sestavy použijte rozhraní API [FabricClient. HealthClient. ReportHealth](/dotnet/api/system.fabric.fabricclient.healthclient.reporthealth) , vystavení rozhraní API pro stav vystavená na `Partition` `CodePackageActivationContext` objektech nebo, rutinách PowerShellu nebo REST.

### <a name="health-reports"></a>Sestavy stavu
[Sestavy o stavu](/dotnet/api/system.fabric.health.healthreport) pro každou entitu v clusteru obsahují následující informace:

* **SourceId**. Řetězec, který jednoznačně identifikuje zpravodaj události stavu.
* **Identifikátor entity** Určuje entitu, ve které se sestava aplikuje. Liší se v závislosti na [typu entity](service-fabric-health-introduction.md#health-entities-and-hierarchy):
  
  * Služby. Žádné
  * Uzlu. Název uzlu (řetězec).
  * Použití. Název aplikace (URI). Představuje název instance aplikace nasazené v clusteru.
  * Službám. Název služby (URI). Představuje název instance služby nasazené v clusteru.
  * Rozdělován. IDENTIFIKÁTOR oddílu (GUID). Představuje jedinečný identifikátor oddílu.
  * Změněno. ID repliky stavové služby nebo ID instance služby bez stavu (INT64).
  * DeployedApplication. Název aplikace (URI) a název uzlu (řetězec).
  * DeployedServicePackage. Název aplikace (URI), název uzlu (řetězec) a název manifestu služby (řetězec).
* **Vlastnost**. *Řetězec* (nejedná se o pevný výčet), který umožňuje zpravodaji kategorizovat událost stavu pro konkrétní vlastnost entity. Například zpravodaj A může hlásit stav vlastnosti "úložiště" Node01 a zpravodaj B může ohlásit stav vlastnosti "připojení" Node01. V Health Store se tyto sestavy považují za samostatné události stavu pro entitu Node01.
* **Popis:** Řetězec, který umožňuje zpravodaji poskytovat podrobné informace o události stavu. **SourceId**, **Property** a **elementu** stav by měly plně popsat sestavu. Popis přidá do sestavy informace o tom, které se dají přečíst lidmi. Tento text usnadňuje správcům a uživatelům pochopení sestavy o stavu.
* Stav **elementu**. [Výčet](service-fabric-health-introduction.md#health-states) , který popisuje stav sestavy. Přijaté hodnoty jsou OK, varování a chyba.
* **TimeToLive**. Interval TimeSpan, který určuje, jak dlouho je sestava stavu platná. Společně s **RemoveWhenExpired** umožňuje Health Store zjistit, jak vyhodnotit události, jejichž platnost vypršela. Ve výchozím nastavení je hodnota nekonečno a sestava je platná trvale.
* **RemoveWhenExpired**. Logická hodnota. Pokud je nastavená hodnota true, zpráva o stavu s vypršenou platností se automaticky odebere z Health Store a sestava nebude mít vliv na hodnocení stavu entity. Používá se, když je sestava platná jenom po zadanou dobu, a zpravodaj je nepotřebuje explicitně vymazat. Používá se také k odstranění sestav z Health Store (například sledovací zařízení se změnilo a zastaví odesílání sestav s předchozím zdrojem a vlastností). Může odeslat sestavu se stručným TimeToLive společně s RemoveWhenExpired, aby se vymazal jakýkoli předchozí stav z Health Store. Pokud je hodnota nastavená na false, zpráva o vypršení platnosti se považuje za chybu při vyhodnocování stavu. Hodnota false signalizuje Health Store, že zdroj by měl pravidelně vykazovat tuto vlastnost. Pokud tomu tak není, musí být u sledovacího zařízení něco špatné. Stav sledovacího zařízení se zachycuje s ohledem na událost jako chybu.
* **SequenceNumber**. Kladné celé číslo, které je třeba stále zvyšovat, představuje pořadí sestav. Používá ho Health Store ke zjišťování zastaralých sestav, které se přijímají pozdě kvůli zpožděním sítě nebo jiným problémům. Sestava je odmítnuta, pokud je pořadové číslo menší nebo rovno naposledy použitému číslu pro stejnou entitu, zdroj a vlastnost. Pokud není zadaný, pořadové číslo se vygeneruje automaticky. Je nutné vložit do pořadového čísla pouze při vytváření sestav o přechodech stavu. V takové situaci musí zdroj zapamatovat si, které zprávy poslaly, a uchovávat informace pro obnovení při převzetí služeb při selhání.

Tyto čtyři informace – SourceId, identifikátor entity, vlastnost a stav podstavu jsou požadovány pro každou sestavu stavu. Řetězec SourceId nesmí začínat předponou "**System.**", která je vyhrazena pro systémové sestavy. Pro stejnou entitu je pro stejný zdroj a vlastnost k dispozici pouze jedna sestava. Několik sestav pro stejný zdroj a vlastnost jsou navzájem popsány buď na straně klienta stavu (pokud jsou v dávce), nebo na straně Health Store. Náhrada je založena na pořadových číslech; novější sestavy (s vyššími čísly pořadových čísel) nahrazují starší sestavy.

### <a name="health-events"></a>Události stavu
Interně Health Store udržuje [události stavu](/dotnet/api/system.fabric.health.healthevent), které obsahují všechny informace ze sestav a další metadata. Metadata obsahují čas, kdy byla sestava předána klientovi stavu a čas jejich změny na straně serveru. [Dotazy](service-fabric-view-entities-aggregated-health.md#health-queries)na stav jsou vraceny událostmi stavu.

Přidaná metadata obsahují:

* **SourceUtcTimestamp**. Čas, kdy byla sestava udělena klientovi stavu (koordinovaný světový čas).
* **LastModifiedUtcTimestamp**. Čas poslední změny sestavy na straně serveru (koordinovaný světový čas).
* **Platnost vypršela**. Příznak, který označuje, jestli vypršela platnost sestavy, když Health Store dotaz spustil. Událost může mít vypršení platnosti, pokud RemoveWhenExpired je false. V opačném případě událost není vrácena dotazem a je odebrána z úložiště.
* **LastOkTransitionAt**, **LastWarningTransitionAt**, **LastErrorTransitionAt**. Čas poslední přechody OK/upozornění/chyby Tato pole poskytují historii přechodů stavu pro událost.

Pole přechodu stavu lze použít pro informace o událostech s inteligentními oznámeními nebo historické události stavu. Umožňují scénáře, jako například:

* Upozorní, když došlo k upozornění nebo chybě na více než X minut. Kontrola podmínky v časovém intervalu brání upozornění na dočasné podmínky. Například výstraha v případě, že je stav upozornění na více než pět minut, lze přeložit do (stav = = upozornění a nyní-LastWarningTransitionTime > 5 minut).
* Výstraha pouze pro podmínky, které se změnily za posledních X minut. Pokud se již sestava stala chybou před určenou časem, může být ignorována, protože již byla signalizována dříve.
* Pokud je vlastnost přepínání mezi upozorněním a chybou, určete, jak dlouho není v pořádku (tj. ne OK). Například výstraha v případě, že vlastnost není v pořádku na více než pět minut, může být převedena na (Stavový! = ok a nyní-LastOkTransitionTime > 5 minut).

## <a name="example-report-and-evaluate-application-health"></a>Příklad: sestava a vyhodnocení stavu aplikace
Následující příklad pošle zprávu o stavu prostřednictvím PowerShellu v Application **Fabric:/WORDCOUNT** ze zdrojového **MyWatchdog**. Zpráva o stavu obsahuje informace o vlastnostech stavu "dostupnost" v chybovém stavu s nekonečné TimeToLive. Pak se dotazuje na stav aplikace, který vrátí souhrnné chyby stavu a hlášené události stavu v seznamu událostí stavu.

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
Model stavu umožňuje cloudové služby a základní Service Fabricou platformu pro škálování, protože monitorování a určování stavu jsou distribuované mezi různými monitory v rámci clusteru.
Jiné systémy mají jedinou centralizovanou službu na úrovni clusteru, která analyzuje všechny *potenciálně* užitečné informace vydávané službami. Tento přístup brání jejich škálovatelnosti. Zároveň jim neumožňuje shromažďovat konkrétní informace, které vám pomůžou identifikovat problémy a potenciální problémy co nejblíže původní příčině.

Model stavu se silně používá pro monitorování a diagnostiku, pro vyhodnocení stavu clusteru a aplikací a pro monitorované upgrady. Jiné služby používají data o stavu k provádění automatických oprav, sestavení historie stavu clusteru a vydávání výstrah pro určité podmínky.

## <a name="next-steps"></a>Další kroky
[Zobrazit Service Fabric sestavy o stavu](service-fabric-view-entities-aggregated-health.md)

[Použití sestav stavu systému pro řešení potíží](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Postup hlášení a kontroly stavu služby](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Přidat vlastní sestavy o stavu Service Fabric](service-fabric-report-health.md)

[Místní monitorování a diagnostika služeb](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Upgrade aplikace Service Fabric](service-fabric-application-upgrade.md)
