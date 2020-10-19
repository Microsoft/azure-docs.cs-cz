---
title: Zásady správného řízení prostředků pro kontejnery a služby
description: Azure Service Fabric umožňuje zadat požadavky a omezení prostředků pro služby spuštěné jako procesy nebo kontejnery.
ms.topic: conceptual
ms.date: 8/9/2017
ms.openlocfilehash: 889fce77c1a3a743e9805ec482a9c87b9bf8da65
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2020
ms.locfileid: "92172860"
---
# <a name="resource-governance"></a>Zásady správného řízení prostředků

Pokud spouštíte více služeb na stejném uzlu nebo clusteru, může se stát, že jedna služba bude spotřebovávat více prostředků, omezují se v procesu další služby. Tento problém se označuje jako problém s "vysokou úrovní souseda". Azure Service Fabric umožňuje vývojářům řídit toto chování zadáním požadavků a omezení pro službu, aby se omezilo využití prostředků.

> Než budete pokračovat v tomto článku, doporučujeme vám seznámit se s [Service Fabric aplikačním modelem][application-model-link] a [modelem Service Fabric hostování][hosting-model-link].
>

## <a name="resource-governance-metrics"></a>Metriky zásad správného řízení prostředků

Zásady správného řízení prostředků jsou podporované v Service Fabric v souladu s [balíčkem služby][application-model-link]. Prostředky, které jsou přiřazeny k balíčku služby, mohou být dále rozděleny mezi balíčky kódu. Service Fabric podporuje zásady správného řízení procesoru a paměti na jeden balíček služby se dvěma integrovanými [metrikami](service-fabric-cluster-resource-manager-metrics.md):

* *CPU* (název metriky `servicefabric:/_CpuCores` ): logický jádro, které je k dispozici na hostitelském počítači. Všechny jádra ve všech uzlech jsou vážená na stejnou.

* *Paměť* (název metriky `servicefabric:/_MemoryInMB` ): paměť je vyjádřena v megabajtech a je namapována na fyzickou paměť, která je k dispozici v počítači.

Pro tyto dvě metriky [cluster správce prostředků (CRM)][cluster-resource-manager-description-link] sleduje celkovou kapacitu clusteru, zatížení každého uzlu v clusteru a zbývající prostředky v clusteru. Tyto dvě metriky jsou ekvivalentní libovolné jiné uživateli nebo vlastní metriky. U těchto funkcí se dají použít všechny existující funkce:

* Cluster může být [vyvážený](service-fabric-cluster-resource-manager-balancing.md) podle těchto dvou metrik (výchozí chování).
* Cluster se dá [defragmentovat](service-fabric-cluster-resource-manager-defragmentation-metrics.md) podle těchto dvou metrik.
* Při [popisu clusteru][cluster-resource-manager-description-link]je možné nastavit kapacitu vyrovnávací paměti pro tyto dvě metriky.

> [!NOTE]
> [Generování sestav dynamického načtení](service-fabric-cluster-resource-manager-metrics.md) není pro tyto metriky podporováno. zatížení těchto metrik je definováno při vytvoření.

## <a name="resource-governance-mechanism"></a>Mechanismus řízení prostředků

Počínaje verzí 7,2 Service Fabric runtime podporuje určení požadavků a omezení pro prostředky procesoru a paměti.

> [!NOTE]
> Verze Service Fabric runtime starší než 7,2 podporují pouze model, který jedinou hodnotou slouží jak jako **požadavek** , **tak pro určitý** prostředek (procesor nebo paměť). Tento dokument popisuje specifikace **RequestsOnly** .

* *Požadavky:* Hodnoty požadavků na procesor a paměť reprezentují zatížení používané [clusterem Správce prostředků (CRM)][cluster-resource-manager-description-link] pro `servicefabric:/_CpuCores` `servicefabric:/_MemoryInMB` metriky a. Jinými slovy CRM zvažuje spotřebu prostředků služby, která se má rovnat hodnotám požadavků, a při rozhodování o umístění používá tyto hodnoty.

* *Omezení:* Hodnoty limitu procesoru a paměti reprezentují skutečné limity prostředků, které se uplatní při aktivaci procesu nebo kontejneru na uzlu.

Service Fabric umožňuje **RequestsOnly, LimitsOnly** a **RequestsAndLimits** specifikace procesoru a paměti.
* Při použití specifikace RequestsOnly Service Fabric také používá hodnoty požadavků jako omezení.
* Při použití specifikace LimitsOnly Service Fabric považuje hodnoty žádosti za 0.
* Při použití specifikace RequestsAndLimits musí být mezní hodnoty větší než nebo rovny hodnotám žádosti.

Pro lepší pochopení mechanismu řízení prostředků se podívejme na ukázkový scénář umístění se specifikací **RequestsOnly** pro prostředek procesoru (mechanismus řízení paměti je ekvivalentní). Vezměte v úvahu uzel se dvěma jádry procesoru a dvěma balíčky služeb, které se na něj umístí. První balíček služby, který se má umístit, se skládá jenom z jednoho balíčku kódu kontejneru a určuje jenom požadavek jednoho jádra procesoru. Druhý balíček služby, který se má umístit, se skládá jenom z jednoho balíčku kódu založeného na procesu a zároveň určuje jenom požadavek jednoho jádra procesoru. Vzhledem k tomu, že oba balíčky služby mají specifikaci RequestsOnly, jejich mezní hodnoty jsou nastaveny na jejich hodnoty požadavků.

1. Nejprve se v uzlu umístí balíček služby na bázi kontejneru požadující jeden procesor. Modul runtime aktivuje kontejner a nastaví limit procesoru na jeden Core. Kontejner nebude moci používat více než jedno jádro.

2. V dalším kroku se na uzel umístí balíček služby na základě procesu požadujícího jeden PROCESORový jader. Modul runtime aktivuje proces služby a nastaví jeho limit pro procesor na jednu jádro.

V tomto okamžiku je součet požadavků roven kapacitě uzlu. CRM nebude v tomto uzlu umísťovat žádné další kontejnery ani procesy služeb s požadavky na procesor. V uzlu je proces a kontejner spuštěný s jedním jádrem a nesoupeří se mezi sebou pro procesor.

Pojďme teď přejít na náš příklad se specifikací **RequestsAndLimits** . Tentokrát balíček služby založený na kontejneru určí požadavek na jádro procesoru a omezení dvou jader procesoru. Balíček služby na základě procesu určuje jak požadavek, tak i limit jednoho jádra procesoru.
  1. Nejprve se balíček služby na bázi kontejneru umístí na uzel. Modul runtime aktivuje kontejner a nastaví limit procesoru na dvě jádra. Kontejner nebude moci používat více než dvě jádra.
  2. V dalším kroku se balíček služby na základě procesu umístí na uzel. Modul runtime aktivuje proces služby a nastaví jeho limit pro procesor na jednu jádro.

  V tomto okamžiku se součet požadavků na procesor balíčků služeb, které jsou umístěné na uzlu, rovná kapacitě procesoru uzlu. CRM nebude v tomto uzlu umísťovat žádné další kontejnery ani procesy služeb s požadavky na procesor. V uzlu ale součet omezení (dvě jádra pro kontejner a jeden jádro pro proces) překračuje kapacitu dvou jader. Pokud kontejner a proces se současně rozchází, existuje možnost kolizí prostředků procesoru. Takové spory budou spravovaných podkladovým operačním systémem pro danou platformu. V tomto příkladu může kontejner zvýšit kapacitu až na dvě jádra procesoru, což vede k tomu, že požadavek na procesor se nezaručuje.

> [!NOTE]
> Jak je znázorněno v předchozím příkladu, hodnoty požadavků pro procesor a paměť **nevedou k rezervaci prostředků na uzlu**. Tyto hodnoty reprezentují spotřebu prostředků, kterou cluster Správce prostředků při rozhodování o umístění. Hodnoty limitu reprezentují skutečné limity prostředků použité při aktivaci procesu nebo kontejneru na uzlu.


Existuje několik situací, kdy může dojít ke sporům pro procesor. V takových situacích se může stát, že proces a kontejner z našeho příkladu nastanou problém s okolním sousedem:

* *Kombinování řízených a neřízených služeb a kontejnerů*: Pokud uživatel vytvoří službu bez zadaného zásad správného řízení prostředků, modul runtime ji uvidí, že nespotřebovává žádné prostředky, a může je umístit na uzel v našem příkladu. V tomto případě tento nový proces efektivně spotřebovává určitý procesor na úkor služeb, které jsou již spuštěny na uzlu. Existují dvě řešení tohoto problému. Buď nemíchejte řízení a neřídící služby ve stejném clusteru, nebo použijte [omezení umístění](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md) , aby tyto dva typy služeb neukončily stejnou sadu uzlů.

* *Když je na uzlu spuštěný jiný proces, mimo Service Fabric (například pro službu operačního systému)*: v této situaci se proces mimo Service Fabric taky zamýšlí pro procesor s existujícími službami. Řešením tohoto problému je správné nastavení kapacity uzlů pro účet pro režijní náklady na operační systém, jak je znázorněno v další části.

* *Pokud požadavky nejsou rovnocenné omezením*: jak je popsáno v předchozím příkladu RequestsAndLimits, požadavky nevedou k rezervaci prostředků na uzlu. Když je služba s omezením větší než požadavky umístěná na uzlu, může spotřebovávat prostředky (pokud jsou dostupné) až do omezení IT. V takových případech nemusí být další služby na uzlu schopné spotřebovávat prostředky až do jejich hodnot požadavků.

## <a name="cluster-setup-for-enabling-resource-governance"></a>Nastavení clusteru pro povolení zásad správného řízení prostředků

Když se uzel spustí a připojí ke clusteru, Service Fabric zjistí dostupné množství paměti a dostupný počet jader a pak nastaví kapacity uzlů pro tyto dva prostředky.

Pro opuštění prostoru vyrovnávací paměti pro operační systém a pro jiné procesy, které mohou být spuštěny na uzlu, Service Fabric používá pouze 80% dostupných prostředků na uzlu. Toto procento je konfigurovatelné a v manifestu clusteru je možné ho změnit.

Tady je příklad, jak dát Service Fabric k použití 50% dostupného procesoru a 70% dostupné paměti:

```xml
<Section Name="PlacementAndLoadBalancing">
    <!-- 0.0 means 0%, and 1.0 means 100%-->
    <Parameter Name="CpuPercentageNodeCapacity" Value="0.5" />
    <Parameter Name="MemoryPercentageNodeCapacity" Value="0.7" />
</Section>
```

Pro většinu zákazníků a scénářů se doporučuje automatické zjišťování kapacit uzlů pro procesor a paměť, což je doporučená konfigurace (automatické zjišťování je ve výchozím nastavení zapnuté). Pokud ale potřebujete úplnou ruční instalaci kapacit uzlů, můžete je nakonfigurovat podle typu uzlu pomocí mechanismu pro popis uzlů v clusteru. Tady je příklad, jak nastavit typ uzlu se čtyřmi jádry a 2 GB paměti:

```xml
    <NodeType Name="MyNodeType">
      <Capacities>
        <Capacity Name="servicefabric:/_CpuCores" Value="4"/>
        <Capacity Name="servicefabric:/_MemoryInMB" Value="2048"/>
      </Capacities>
    </NodeType>
```

Když je povolená Automatická detekce dostupných prostředků a kapacity uzlů se v manifestu clusteru definují ručně, Service Fabric zkontroluje, jestli má uzel dostatek prostředků pro podporu kapacity, kterou uživatel definoval:

* Pokud jsou kapacity uzlů, které jsou definovány v manifestu, menší nebo rovny dostupným prostředkům v uzlu, Service Fabric používá kapacity, které jsou uvedeny v manifestu.

* Pokud jsou kapacity uzlů, které jsou definovány v manifestu, větší než dostupné prostředky, Service Fabric používá dostupné prostředky jako kapacity uzlů.

Automatické zjišťování dostupných prostředků může být vypnuto, pokud není vyžadováno. Pokud ho chcete vypnout, změňte následující nastavení:

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="AutoDetectAvailableResources" Value="false" />
</Section>
```

Pro zajištění optimálního výkonu byste měli v manifestu clusteru zapnout taky následující nastavení:

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="PreventTransientOvercommit" Value="true" />
    <Parameter Name="AllowConstraintCheckFixesDuringApplicationUpgrade" Value="true" />
</Section>
```

> [!IMPORTANT]
> Počínaje Service Fabric verze 7,0 jsme aktualizovali pravidlo, jak se počítají kapacity prostředků uzlu v případech, kdy uživatel ručně poskytuje hodnoty pro kapacitu prostředků uzlu. Pojďme vzít v úvahu následující scénář:
>
> * V uzlu je celkem 10 jader procesoru.
> * Hodnota SF je nakonfigurovaná tak, aby používala 80% celkových prostředků pro služby uživatelů (výchozí nastavení), což ponechá vyrovnávací paměť 20% pro ostatní služby běžící na uzlu (včetně systémových služeb Service Fabric).
> * Uživatel rozhodne ručně přepsat kapacitu prostředků uzlu pro metriku jader procesoru a nastaví ji na 5 jader.
>
> Změnili jsme pravidlo způsobu výpočtu dostupné kapacity pro Service Fabric uživatelských služeb následujícím způsobem:
>
> * Před Service Fabric 7,0 bude dostupná kapacita pro uživatelské služby vypočítaná na **5 jader** (vyrovnávací paměť kapacity 20% se ignoruje).
> * Počínaje Service Fabric 7,0 budou dostupné kapacity pro uživatelské služby vypočítány na **4 jádra** (vyrovnávací paměť kapacity 20% se Neignoruje).

## <a name="specify-resource-governance"></a>Zadat zásady správného řízení prostředků

Požadavky a omezení zásad správného řízení prostředků jsou uvedeny v manifestu aplikace (oddíl ServiceManifestImport). Podívejme se na několik příkladů:

**Příklad 1: specifikace RequestsOnly**
```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='https://www.w3.org/2001/XMLSchema-instance'>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName='ServicePackageA' ServiceManifestVersion='v1'/>
    <Policies>
      <ServicePackageResourceGovernancePolicy CpuCores="1"/>
      <ResourceGovernancePolicy CodePackageRef="CodeA1" CpuShares="512" MemoryInMB="1024" />
      <ResourceGovernancePolicy CodePackageRef="CodeA2" CpuShares="256" MemoryInMB="1024" />
    </Policies>
  </ServiceManifestImport>
```

V tomto příkladu se `CpuCores` atribut používá k určení žádosti o 1 jádro procesoru pro **ServicePackageA**. Vzhledem k tomu, že není zadaný limit CPU ( `CpuCoresLimit` atribut), Service Fabric pro balíček služby používá taky zadanou hodnotu požadavku 1 jádro.

**ServicePackageA** se umístí jenom na uzel, kde zbývající kapacita procesoru po odečtení **součtu požadavků na procesor všech balíčků služeb umístěných v tomto uzlu** je větší nebo rovna 1 jádru. V uzlu bude balíček služby omezený na jedno jádro. Balíček služby obsahuje dva balíčky kódu (**CodeA1** a **CodeA2**) a oba určují `CpuShares` atribut. Podíl CpuShares 512:256 se používá k výpočtu limitů procesoru pro jednotlivé balíčky kódu. CodeA1 se tedy omezí na dvě třetiny jádra a CodeA2 se omezí na jednu třetinu jader. Pokud CpuShares nejsou zadány pro všechny balíčky kódu, Service Fabric rozdělí limit procesoru rovnoměrně mezi ně.

Zatímco CpuShares zadaná pro balíčky kódu představuje relativní podíl celkového limitu procesoru balíčku služby, hodnoty paměti pro balíčky kódu jsou zadány v absolutních výrazech. V tomto příkladu se `MemoryInMB` atribut používá k určení požadavků na paměť 1024 MB pro CodeA1 i CodeA2. Vzhledem k tomu, že není zadán limit paměti ( `MemoryInMBLimit` atribut), Service Fabric používá také zadané hodnoty požadavků jako limity pro balíčky kódu. Požadavek (a omezení) paměti pro balíček služby se počítá jako součet hodnot požadavků na paměť (a limitu) svých balíčků kódu daného prvku. V případě **ServicePackageA**je tedy požadavek a limit paměti počítán jako 2048 MB.

**ServicePackageA** se umístí jenom na uzel, kde zbývající kapacita paměti po odečtení **součtu požadavků na paměť u všech balíčků služeb, které jsou umístěné na tomto uzlu,** je větší nebo rovna 2048 MB. V uzlu budou oba balíčky kódu omezeny na 1024 MB paměti. Balíčky kódu (kontejnery nebo procesy) nebudou moci přidělovat více paměti, než je toto omezení, a pokus o to by vyplynule z důvodu nedostatku paměti.

**Příklad 2: specifikace LimitsOnly**
```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='https://www.w3.org/2001/XMLSchema-instance'>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName='ServicePackageA' ServiceManifestVersion='v1'/>
    <Policies>
      <ServicePackageResourceGovernancePolicy CpuCoresLimit="1"/>
      <ResourceGovernancePolicy CodePackageRef="CodeA1" CpuShares="512" MemoryInMBLimit="1024" />
      <ResourceGovernancePolicy CodePackageRef="CodeA2" CpuShares="256" MemoryInMBLimit="1024" />
    </Policies>
  </ServiceManifestImport>
```
Tento příklad používá `CpuCoresLimit` a `MemoryInMBLimit` atributy, které jsou k dispozici pouze v SF verze 7,2 a novější. Atribut CpuCoresLimit se používá k určení limitu CPU 1 jádro pro **ServicePackageA**. Vzhledem `CpuCores` k tomu, že není zadán požadavek na procesor (atribut), je považován za hodnotu 0. `MemoryInMBLimit` atribut slouží k určení limitů paměti 1024 MB pro CodeA1 a CodeA2 a protože `MemoryInMB` nejsou zadány požadavky (atribut), jsou považovány za 0. Požadavek na paměť a omezení pro **ServicePackageA** se tak počítají jako 0 a 2048. Vzhledem k tomu, že požadavky na procesor a paměť pro **ServicePackageA** jsou 0, nepředstavuje žádné zatížení pro CRM, aby bylo možné zvážit umístění `servicefabric:/_CpuCores` a `servicefabric:/_MemoryInMB` metriky a. Z hlediska zásad správného řízení prostředků proto můžete **ServicePackageA** umístit na libovolný uzel **bez ohledu na zbývající kapacitu**. Podobně jako v příkladu 1 se CodeA1 bude omezit na dvě třetiny základního a 1024 MB paměti a CodeA2 se omezí na jednu třetinu základního a 1024 MB paměti.

**Příklad 3: specifikace RequestsAndLimits**
```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='https://www.w3.org/2001/XMLSchema-instance'>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName='ServicePackageA' ServiceManifestVersion='v1'/>
    <Policies>
      <ServicePackageResourceGovernancePolicy CpuCores="1" CpuCoresLimit="2"/>
      <ResourceGovernancePolicy CodePackageRef="CodeA1" CpuShares="512" MemoryInMB="1024" MemoryInMBLimit="3072" />
      <ResourceGovernancePolicy CodePackageRef="CodeA2" CpuShares="256" MemoryInMB="2048" MemoryInMBLimit="4096" />
    </Policies>
  </ServiceManifestImport>
```
V tomto příkladu se sestavuje na prvních dvou příkladech určení požadavků i omezení pro procesor a paměť. **ServicePackageA** má v uvedeném pořadí požadavky na procesor a paměť 1 jádro a 3072 (1024 + 2048) MB. Dá se umístit jenom na uzel, který má aspoň 1 jádro (a 3072 MB) kapacity, po odečtení součtu všech požadavků na procesor (a paměti) všech balíčků služeb, které jsou umístěné na uzlu z celkové kapacity procesoru (a paměti) uzlu. V uzlu bude CodeA1 omezen na dvě třetiny 2 jádra a 3072 MB paměti, zatímco CodeA2 bude omezen na jednu třetinu 2 jader a 4096 MB paměti.

### <a name="using-application-parameters"></a>Použití parametrů aplikace

Při zadávání nastavení zásad správného řízení prostředků je možné použít [parametry aplikace](service-fabric-manage-multiple-environment-app-configuration.md) ke správě více konfigurací aplikace. Následující příklad ukazuje použití parametrů aplikace:

```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='https://www.w3.org/2001/XMLSchema-instance'>

  <Parameters>
    <Parameter Name="CpuCores" DefaultValue="4" />
    <Parameter Name="CpuSharesA" DefaultValue="512" />
    <Parameter Name="CpuSharesB" DefaultValue="512" />
    <Parameter Name="MemoryA" DefaultValue="2048" />
    <Parameter Name="MemoryB" DefaultValue="2048" />
  </Parameters>

  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName='ServicePackageA' ServiceManifestVersion='v1'/>
    <Policies>
      <ServicePackageResourceGovernancePolicy CpuCores="[CpuCores]"/>
      <ResourceGovernancePolicy CodePackageRef="CodeA1" CpuShares="[CpuSharesA]" MemoryInMB="[MemoryA]" />
      <ResourceGovernancePolicy CodePackageRef="CodeA2" CpuShares="[CpuSharesB]" MemoryInMB="[MemoryB]" />
    </Policies>
  </ServiceManifestImport>
```

V tomto příkladu jsou pro produkční prostředí nastavené výchozí hodnoty parametrů, kde každý balíček služby by získal 4 jádra a 2 GB paměti. Je možné změnit výchozí hodnoty pomocí souborů parametrů aplikace. V tomto příkladu lze použít jeden soubor parametrů pro místní testování aplikace, kde by bylo méně prostředků než v produkčním prostředí:

```xml
<!-- ApplicationParameters\Local.xml -->

<Application Name="fabric:/TestApplication1" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="CpuCores" DefaultValue="2" />
    <Parameter Name="CpuSharesA" DefaultValue="512" />
    <Parameter Name="CpuSharesB" DefaultValue="512" />
    <Parameter Name="MemoryA" DefaultValue="1024" />
    <Parameter Name="MemoryB" DefaultValue="1024" />
  </Parameters>
</Application>
```

> [!IMPORTANT]
> Zadání zásad správného řízení prostředků s parametry aplikace je k dispozici počínaje verzí Service Fabric 6,1.<br>
>
> Pokud se k určení zásad správného řízení prostředků používají parametry aplikace, Service Fabric nejde downgradovat na verzi starší než verze 6,1.

## <a name="enforcing-the-resource-limits-for-user-services"></a>Vynucování omezení prostředků pro uživatelské služby

Při použití zásad správného řízení prostředků u služby Service Fabric Services garantuje, že tyto služby, které řídí prostředky, nemůžou překročit kvótu prostředků, mnoho uživatelů stále potřebuje spouštět některé ze svých služeb Service Fabric v nespravovaných režimech. Při používání nespravovaných Service Fabric služeb je možné spustit v situacích, kdy "navýšení nespravovaných služeb" spotřebují všechny dostupné prostředky na Service Fabricch uzlech, což může vést k vážným problémům, jako je:

* Vyčerpání prostředků dalších služeb spuštěných na uzlech (včetně systémových služeb Service Fabric)
* Uzly končící ve stavu není v pořádku.
* Nereagující Service Fabric rozhraní API pro správu clusteru

Aby nedocházelo k těmto situacím, Service Fabric vám umožní *vyhodnotit omezení prostředků pro všechny Service Fabric uživatelské služby spuštěné v uzlu* (řídí se a neřídí se), aby se zaručilo, že uživatelské služby nikdy nebudou používat víc než zadané množství prostředků. Toho je dosaženo nastavením hodnoty pro EnforceUserServiceMetricCapacities config v oddílu PlacementAndLoadBalancing manifestem clusteru na hodnotu true. Toto nastavení je ve výchozím nastavení vypnuté.

```xml
<SectionName="PlacementAndLoadBalancing">
    <ParameterName="EnforceUserServiceMetricCapacities" Value="false"/>
</Section>
```

Další poznámky:

* Vynucení limitu prostředků se vztahuje jenom na `servicefabric:/_CpuCores` `servicefabric:/_MemoryInMB` metriky prostředků a.
* Vynucení limitu prostředků funguje jenom v případě, že jsou kapacity uzlů pro metriky prostředků dostupné Service Fabric, buď prostřednictvím mechanismu automatického zjišťování, nebo přes uživatele ručně zadáním kapacit uzlů (jak je vysvětleno v části [instalace clusteru pro povolení zásad správného řízení prostředků](service-fabric-resource-governance.md#cluster-setup-for-enabling-resource-governance) ).Pokud nejsou nakonfigurované kapacity uzlů, nelze použít funkci vynucení limitu prostředků, protože Service Fabric nemůže zjistit, kolik prostředků se má vyhradit pro uživatelské služby.Service Fabric vydá upozornění na stav, pokud je hodnota "EnforceUserServiceMetricCapacities" pravdivá, ale nejsou nakonfigurovány kapacity uzlů.

## <a name="other-resources-for-containers"></a>Další zdroje informací o kontejnerech

Kromě procesoru a paměti je možné určit další omezení prostředků pro kontejnery. Tato omezení jsou určena na úrovni balíčku kódu a jsou použita při spuštění kontejneru. Na rozdíl od CPU a paměti nemusí cluster Správce prostředků znát tyto prostředky a nebude pro ně provádět žádné kontroly kapacity ani vyrovnávání zatížení.

* *MemorySwapInMB*: velikost odkládací paměti, kterou může kontejner použít.
* *MemoryReservationInMB*: částečný limit pro zásady správného řízení paměti, která je vynutila pouze při zjištění kolizí paměti v uzlu.
* *CpuPercent*: procento využití procesoru, které může kontejner použít. Pokud jsou pro balíček služby zadané požadavky na procesor nebo omezení, tento parametr se efektivně ignoruje.
* *MaximumIOps*: maximální hodnota IOPS, kterou může kontejner použít (čtení a zápis).
* *MaximumIOBytesps*: maximální vstupně-výstupní operace (bajty za sekundu), které může kontejner použít (čtení a zápis).
* *BlockIOWeight*: zablokuje váhu v/v pro relativní vzhledem k ostatním kontejnerům.

Tyto prostředky je možné kombinovat s PROCESORem a pamětí. Tady je příklad, jak určit další prostředky pro kontejnery:

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ResourceGovernancePolicy CodePackageRef="FrontendService.Code" CpuPercent="5"
            MemorySwapInMB="4084" MemoryReservationInMB="1024" MaximumIOPS="20" />
        </Policies>
    </ServiceManifestImport>
```

## <a name="next-steps"></a>Další kroky

* Další informace o Správce prostředků clusteru najdete v tématu [představení služby Service Fabric cluster Resource Manager](service-fabric-cluster-resource-manager-introduction.md).
* Další informace o modelu aplikace, balíčcích služeb a balíčcích kódu – a o tom, jak jsou repliky mapovány, najdete v tématu [model aplikace v Service Fabric][application-model-link].

<!-- Links -->
[application-model-link]: service-fabric-application-model.md
[hosting-model-link]: service-fabric-hosting-model.md
[cluster-resource-manager-description-link]: service-fabric-cluster-resource-manager-cluster-description.md
