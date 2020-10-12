---
title: Zásady správného řízení prostředků pro kontejnery a služby
description: Azure Service Fabric umožňuje zadat omezení prostředků pro služby běžící uvnitř nebo vně kontejnerů.
ms.topic: conceptual
ms.date: 8/9/2017
ms.openlocfilehash: 11ca6e29829d911717a829b3e4dee0a190856a52
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "81115147"
---
# <a name="resource-governance"></a>Zásady správného řízení prostředků

Pokud spouštíte více služeb na stejném uzlu nebo clusteru, je možné, že jedna služba může spotřebovat víc prostředků, omezují se v procesu další služby. Tento problém se označuje jako problém s "vysokou úrovní souseda". Azure Service Fabric umožňuje vývojářům určit rezervace a omezení pro službu za účelem zajištění prostředků a omezení využití prostředků.

> Než budete pokračovat v tomto článku, doporučujeme vám seznámit se s [Service Fabric aplikačním modelem](service-fabric-application-model.md) a [modelem Service Fabric hostování](service-fabric-hosting-model.md).
>

## <a name="resource-governance-metrics"></a>Metriky zásad správného řízení prostředků

Zásady správného řízení prostředků jsou podporované v Service Fabric v souladu s [balíčkem služby](service-fabric-application-model.md). Prostředky, které jsou přiřazeny k balíčku služby, mohou být dále rozděleny mezi balíčky kódu. Zadaná omezení prostředků také znamenají rezervaci prostředků. Service Fabric podporuje určení procesoru a paměti na balíček služby se dvěma integrovanými [metrikami](service-fabric-cluster-resource-manager-metrics.md):

* *CPU* (název metriky `servicefabric:/_CpuCores` ): Logická jádra, která je k dispozici na hostitelském počítači. Všechny jádra ve všech uzlech jsou vážená na stejnou.

* *Paměť* (název metriky `servicefabric:/_MemoryInMB` ): paměť je vyjádřena v megabajtech a je namapována na fyzickou paměť, která je k dispozici v počítači.

Pro tyto dvě metriky [cluster správce prostředků](service-fabric-cluster-resource-manager-cluster-description.md) sleduje celkovou kapacitu clusteru, zatížení každého uzlu v clusteru a zbývající prostředky v clusteru. Tyto dvě metriky jsou ekvivalentní libovolné jiné uživateli nebo vlastní metriky. U těchto funkcí se dají použít všechny existující funkce:

* Cluster může být [vyvážený](service-fabric-cluster-resource-manager-balancing.md) podle těchto dvou metrik (výchozí chování).
* Cluster se dá [defragmentovat](service-fabric-cluster-resource-manager-defragmentation-metrics.md) podle těchto dvou metrik.
* Při [popisu clusteru](service-fabric-cluster-resource-manager-cluster-description.md)je možné nastavit kapacitu vyrovnávací paměti pro tyto dvě metriky.

> [!NOTE]
> [Generování sestav dynamického načtení](service-fabric-cluster-resource-manager-metrics.md) není pro tyto metriky podporováno. zatížení těchto metrik je definováno při vytvoření.

## <a name="resource-governance-mechanism"></a>Mechanismus řízení prostředků

Modul runtime Service Fabric v současné době neposkytuje rezervaci pro prostředky. Při otevření procesu nebo kontejneru nastaví modul runtime omezení prostředků na zátěže, která byla definována během vytváření. Kromě toho modul runtime odmítne otevírání nových balíčků služby, které jsou k dispozici, když dojde k překročení prostředků. Abychom lépe porozuměli tomu, jak proces funguje, Podívejme se na příklad uzlu se dvěma jádry procesoru (mechanismus řízení paměti je ekvivalentní):

1. Nejdřív se kontejner umístí na uzel, který vyžaduje jeden procesor. Modul runtime otevře kontejner a nastaví limit procesoru na jeden Core. Kontejner nebude moci používat více než jedno jádro.

2. Poté se na uzel umístí replika služby a odpovídající balíček služby určí limit jednoho jádra procesoru. Modul runtime otevře balíček kódu a nastaví jeho limit pro procesor na jednu jádro.

V tomto okamžiku součet omezení se rovná kapacitě uzlu. Proces a kontejner jsou spuštěné s jedním jádrem a vzájemně nekolidují. Service Fabric neobsahují žádné další kontejnery ani repliky, když zadávají limit procesoru.

Existují však dvě situace, kdy mohou být pro procesor soupeří jiné procesy. V takových situacích se může stát, že proces a kontejner z našeho příkladu nastanou problém s sousedním směrovačem v hlučnosti:

* *Kombinování řízených a neřízených služeb a kontejnerů*: Pokud uživatel vytvoří službu bez zadaného zásad správného řízení prostředků, modul runtime ji uvidí, že nespotřebovává žádné prostředky, a může je umístit na uzel v našem příkladu. V tomto případě tento nový proces efektivně spotřebovává určitý procesor na úkor služeb, které jsou již spuštěny na uzlu. Existují dvě řešení tohoto problému. Buď nemíchejte řízení a neřídící služby ve stejném clusteru, nebo použijte [omezení umístění](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md) , aby tyto dva typy služeb neukončily stejnou sadu uzlů.

* *Když je na uzlu spuštěný jiný proces, mimo Service Fabric (například pro službu operačního systému)*: v této situaci se proces mimo Service Fabric taky zamýšlí pro procesor s existujícími službami. Řešením tohoto problému je správné nastavení kapacity uzlů pro účet pro režijní náklady na operační systém, jak je znázorněno v další části.

## <a name="cluster-setup-for-enabling-resource-governance"></a>Nastavení clusteru pro povolení zásad správného řízení prostředků

Když se uzel spustí a připojí ke clusteru, Service Fabric zjistí dostupné množství paměti a dostupný počet jader a pak nastaví kapacity uzlů pro tyto dva prostředky.

Pro opuštění prostoru vyrovnávací paměti pro operační systém a pro jiné procesy může na uzlu běžet Service Fabric používá pouze 80% dostupných prostředků na uzlu. Toto procento je konfigurovatelné a v manifestu clusteru je možné ho změnit.

Tady je příklad, jak dát Service Fabric k použití 50% dostupného procesoru a 70% dostupné paměti:

```xml
<Section Name="PlacementAndLoadBalancing">
    <!-- 0.0 means 0%, and 1.0 means 100%-->
    <Parameter Name="CpuPercentageNodeCapacity" Value="0.5" />
    <Parameter Name="MemoryPercentageNodeCapacity" Value="0.7" />
</Section>
```

Pro většinu zákazníků a scénářů se jako doporučená konfigurace používá automatické zjišťování kapacit uzlů pro procesor a paměť (ve výchozím nastavení je automatické zjišťování zapnuté). Pokud ale potřebujete úplnou ruční instalaci kapacit uzlů, můžete je nakonfigurovat podle typu uzlu pomocí mechanismu pro popis uzlů v clusteru. Tady je příklad, jak nastavit typ uzlu se čtyřmi jádry a 2 GB paměti:

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
> * Na uzlu je celkem 10 jader procesoru.
> * Hodnota SF je nakonfigurovaná tak, aby používala 80% celkových prostředků pro služby uživatelů (výchozí nastavení), což ponechá vyrovnávací paměť 20% pro ostatní služby běžící na uzlu (včetně systémových služeb Service Fabric).
> * Uživatel rozhodne ručně přepsat kapacitu prostředků uzlu pro metriku jader procesoru a nastaví ji na 5 jader.
>
> Změnili jsme pravidlo způsobu výpočtu dostupné kapacity pro Service Fabric uživatelských služeb následujícím způsobem:
>
> * Před Service Fabric 7,0 bude dostupná kapacita pro uživatelské služby vypočítaná na **5 jader** (vyrovnávací paměť kapacity 20% se ignoruje).
> * Počínaje Service Fabric 7,0 budou dostupné kapacity pro uživatelské služby vypočítány na **4 jádra** (vyrovnávací paměť kapacity 20% se Neignoruje).

## <a name="specify-resource-governance"></a>Zadat zásady správného řízení prostředků

Omezení zásad správného řízení prostředků jsou uvedena v manifestu aplikace (oddíl ServiceManifestImport), jak je znázorněno v následujícím příkladu:

```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='https://www.w3.org/2001/XMLSchema-instance'>

  <!--
  ServicePackageA has the number of CPU cores defined, but doesn't have the MemoryInMB defined.
  In this case, Service Fabric sums the limits on code packages and uses the sum as 
  the overall ServicePackage limit.
  -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName='ServicePackageA' ServiceManifestVersion='v1'/>
    <Policies>
      <ServicePackageResourceGovernancePolicy CpuCores="1"/>
      <ResourceGovernancePolicy CodePackageRef="CodeA1" CpuShares="512" MemoryInMB="1000" />
      <ResourceGovernancePolicy CodePackageRef="CodeA2" CpuShares="256" MemoryInMB="1000" />
    </Policies>
  </ServiceManifestImport>
```

V tomto příkladu balíček služby s názvem **ServicePackageA** získá jeden základní uzel na uzlech, kde je umístěn. Tento balíček služby obsahuje dva balíčky kódu (**CodeA1** a **CodeA2**) a oba určují `CpuShares` parametr. Podíl CpuShares 512:256 vydělí jádro napříč dvěma balíčky kódu.

Proto v tomto příkladu CodeA1 získá dvě třetiny základního typu a CodeA2 získá jednu třetinu jádra (a rezervaci se zárukou). Pokud nejsou zadány CpuShares pro balíčky kódu, Service Fabric rozděluje mezi nimi stejné jádra.

Omezení paměti jsou absolutní, takže balíčky kódu jsou omezené na 1024 MB paměti (a rezervace se zárukou samé). Balíčky kódu (kontejnery nebo procesy) nemůžou přidělovat větší množství paměti, než je toto omezení, a pokus o to může mít za následek výjimku nedostatku paměti. Aby vynucení omezení prostředků fungovala, musí být omezení paměti zadaná pro všechny balíčky kódu v rámci balíčku služby.

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
* *CpuPercent*: procento využití procesoru, které může kontejner použít. Pokud jsou pro balíček služby zadané limity procesoru, tento parametr se efektivně ignoruje.
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
* Další informace o modelu aplikace, balíčcích služeb a balíčcích kódu – a o tom, jak jsou repliky mapovány, najdete v tématu [model aplikace v Service Fabric](service-fabric-application-model.md).
