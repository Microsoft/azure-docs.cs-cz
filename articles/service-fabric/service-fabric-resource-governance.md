---
title: Zásady správného řízení prostředků pro kontejnery a služby
description: Azure Service Fabric umožňuje určit omezení prostředků pro služby spuštěné uvnitř nebo vně kontejnerů.
ms.topic: conceptual
ms.date: 8/9/2017
ms.openlocfilehash: 85520876d7f0c89450b572d28dee6cb66ed2231d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75772376"
---
# <a name="resource-governance"></a>Zásady správného řízení prostředků

Pokud používáte více služeb na stejném uzlu nebo clusteru, je možné, že jedna služba může spotřebovat více prostředků, hladovějící chutnající chutnou další služby v procesu. Tento problém se označuje jako "hlučný soused" problém. Azure Service Fabric umožňuje vývojáři zadat rezervace a omezení na službu zaručit prostředky a omezit využití prostředků.

> Než budete pokračovat v tomto článku, doporučujeme seznámit se s [modelem aplikace Service Fabric](service-fabric-application-model.md) a [model hostování Service Fabric](service-fabric-hosting-model.md).
>

## <a name="resource-governance-metrics"></a>Metriky zásad správného řízení zdrojů

Zásady správného řízení prostředků je podporována v Service Fabric v souladu s [balíčkem služeb](service-fabric-application-model.md). Prostředky, které jsou přiřazeny k balíčku služeb lze dále rozdělit mezi balíčky kódu. Zadaná omezení prostředků také znamenají rezervaci prostředků. Service Fabric podporuje určení procesoru a paměti na balíček [služby,](service-fabric-cluster-resource-manager-metrics.md)se dvěma vestavěné metriky :

* *CPU* (název `servicefabric:/_CpuCores`metriky): Logické jádro, které je k dispozici v hostitelském počítači. Všechna jádra ve všech uzlech jsou vážena stejně.

* *Paměť* (název `servicefabric:/_MemoryInMB`metriky): Paměť je vyjádřena v megabajtech a mapuje se na fyzickou paměť, která je k dispozici v počítači.

Pro tyto dvě metriky [Správce prostředků clusteru](service-fabric-cluster-resource-manager-cluster-description.md) sleduje celkovou kapacitu clusteru, zatížení každého uzlu v clusteru a zbývající prostředky v clusteru. Tyto dvě metriky jsou ekvivalentní jakékoli jiné uživatele nebo vlastní metriky. S nimi lze použít všechny stávající funkce:

* Cluster lze [vyvážit](service-fabric-cluster-resource-manager-balancing.md) podle těchto dvou metrik (výchozí chování).
* Cluster lze [defragmentovat](service-fabric-cluster-resource-manager-defragmentation-metrics.md) podle těchto dvou metrik.
* Při [popisu clusteru](service-fabric-cluster-resource-manager-cluster-description.md)lze pro tyto dvě metriky nastavit kapacitu ve vyrovnávací paměti.

[Dynamické vykazování zatížení](service-fabric-cluster-resource-manager-metrics.md) není pro tyto metriky podporováno a zatížení těchto metrik jsou definována v době vytvoření.

## <a name="resource-governance-mechanism"></a>Mechanismus řízení zdrojů

Service Fabric runtime aktuálně neposkytuje rezervace pro prostředky. Při otevření procesu nebo kontejneru nastaví runtime omezení prostředků na zatížení, která byla definována v době vytvoření. Kromě toho runtime odmítne otevření nové balíčky služeb, které jsou k dispozici při překročení prostředků. Chcete-li lépe pochopit, jak proces funguje, vezměme si příklad uzlu se dvěma jádry procesoru (mechanismus pro zásady správného řízení paměti je ekvivalentní):

1. Nejprve je kontejner umístěn na uzlu a požaduje jedno jádro procesoru. Runtime otevře kontejner a nastaví limit procesoru na jedno jádro. Kontejner nebude moci použít více než jedno jádro.

2. Potom je replika služby umístěna na uzlu a odpovídající balíček služby určuje limit jednoho jádra procesoru. Runtime otevře balíček kódu a nastaví jeho limit procesoru na jedno jádro.

V tomto okamžiku se součet limitů rovná kapacitě uzlu. Proces a kontejner jsou spuštěny s jedním jádrem každý a není vzájemně zasahovat. Service Fabric neumístí žádné další kontejnery nebo repliky, když určují limit procesoru.

Existují však dvě situace, ve kterých jiné procesy mohou bojovat o procesor. V těchto situacích proces a kontejner z našeho příkladu může dojít k problému s hlučným sousedem:

* *Míchání řízené a neřízené služby a kontejnery*: Pokud uživatel vytvoří službu bez jakékoli zásady správného řízení prostředků, runtime vidí jako náročné žádné prostředky a můžete umístit na uzlu v našem příkladu. V tomto případě tento nový proces efektivně spotřebovává některé procesoru na úkor služeb, které jsou již spuštěny na uzlu. Existují dvě řešení tohoto problému. Buď nekombinujte řízené a neřízené služby ve stejném clusteru, nebo použijte [omezení umístění,](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md) aby tyto dva typy služeb neskončily na stejné sadě uzlů.

* *Při spuštění jiného procesu na uzlu, mimo Service Fabric (například služba operačního systému)*: V této situaci proces mimo Service Fabric také tvrdí, procesor u existujících služeb. Řešením tohoto problému je správně nastavit kapacity uzlů, aby se zohlednila režie operačního prostoru, jak je znázorněno v další části.

## <a name="cluster-setup-for-enabling-resource-governance"></a>Nastavení clusteru pro povolení zásad správného řízení prostředků

Když uzel spustí a připojí clusteru, Service Fabric zjistí dostupné množství paměti a dostupný počet jader a potom nastaví kapacity uzlů pro tyto dva prostředky.

Chcete-li ponechat vyrovnávací paměti pro operační systém a pro jiné procesy může být spuštěna na uzlu, Service Fabric používá pouze 80 % dostupných prostředků v uzlu. Toto procento je konfigurovatelné a lze jej změnit v manifestu clusteru.

Zde je příklad, jak dát pokyn Service Fabric používat 50 % dostupného procesoru a 70 % dostupné paměti:

```xml
<Section Name="PlacementAndLoadBalancing">
    <!-- 0.0 means 0%, and 1.0 means 100%-->
    <Parameter Name="CpuPercentageNodeCapacity" Value="0.5" />
    <Parameter Name="MemoryPercentageNodeCapacity" Value="0.7" />
</Section>
```

Pro většinu zákazníků a scénářů je automatická detekce kapacit uzlů pro procesor a paměť doporučená konfigurace (automatická detekce je ve výchozím nastavení zapnutá). Pokud však potřebujete úplné ruční nastavení kapacit uzlů, můžete je nakonfigurovat na typ uzlu pomocí mechanismu pro popis uzlů v clusteru. Zde je příklad nastavení typu uzlu se čtyřmi jádry a 2 GB paměti:

```xml
    <NodeType Name="MyNodeType">
      <Capacities>
        <Capacity Name="servicefabric:/_CpuCores" Value="4"/>
        <Capacity Name="servicefabric:/_MemoryInMB" Value="2048"/>
      </Capacities>
    </NodeType>
```

Pokud je povoleno automatické zjišťování dostupných prostředků a kapacity uzlů jsou ručně definovány v manifestu clusteru, service fabric zkontroluje, zda uzel má dostatek prostředků pro podporu kapacity, kterou uživatel definoval:

* Pokud jsou kapacity uzlů, které jsou definovány v manifestu, menší nebo rovny dostupným prostředkům v uzlu, pak Service Fabric používá kapacity, které jsou zadány v manifestu.

* Pokud jsou kapacity uzlů, které jsou definovány v manifestu, větší než dostupné prostředky, service fabric používá dostupné prostředky jako kapacity uzlů.

Automatické zjišťování dostupných prostředků lze vypnout, pokud není vyžadováno. Chcete-li jej vypnout, změňte následující nastavení:

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="AutoDetectAvailableResources" Value="false" />
</Section>
```

Pro optimální výkon by mělo být v manifestu clusteru také zapnuto následující nastavení:

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="PreventTransientOvercommit" Value="true" />
    <Parameter Name="AllowConstraintCheckFixesDuringApplicationUpgrade" Value="true" />
</Section>
```

> [!IMPORTANT]
> Počínaje Service Fabric verze 7.0 jsme aktualizovali pravidlo pro způsob výpočtu kapacit prostředků uzlu v případech, kdy uživatel ručně poskytuje hodnoty pro kapacity prostředků uzlu. Podívejme se na následující scénář:
>
> * Na uzlu je celkem 10 jader procesoru
> * SF je nakonfigurován tak, aby používal 80 % celkových prostředků pro uživatelské služby (výchozí nastavení), což ponechává vyrovnávací paměť 20 % pro ostatní služby spuštěné na uzlu (včetně systémových služeb Service Fabric).
> * Uživatel se rozhodne ručně přepsat kapacitu prostředků uzlu pro metriku jader procesoru a nastaví ji na 5 jader.
>
> Změnili jsme pravidlo o tom, jak se počítá dostupná kapacita pro uživatelské služby Service Fabric následujícím způsobem:
>
> * Před service fabric 7.0, dostupná kapacita pro uživatelské služby by být vypočtena na **5 jader** (kapacita vyrovnávací paměti 20 % je ignorována)
> * Počínaje Service Fabric 7.0, dostupná kapacita pro uživatelské služby by se vypočítala na **4 jádra** (vyrovnávací paměť kapacity 20% není ignorována)

## <a name="specify-resource-governance"></a>Určení zásad správného řízení zdrojů

Omezení zásad správného řízení prostředků jsou určena v manifestu aplikace (ServiceManifestImport části), jak je znázorněno v následujícím příkladu:

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

V tomto příkladu balíček služby s názvem **ServicePackageA** získá jedno jádro na uzlech, kde je umístěn. Tento balíček služeb obsahuje dva balíčky kódu (**CodeA1** a **CodeA2**) a oba určují `CpuShares` parametr. Podíl CpuShares 512:256 rozděluje jádro mezi dva balíčky kódu.

Proto v tomto příkladu CodeA1 získá dvě třetiny jádra a CodeA2 získá jednu třetinu jádra (a rezervace měkké záruky stejné). Pokud CpuShares nejsou určeny pro balíčky kódu, Service Fabric rozdělí jádra rovnoměrně mezi nimi.

Limity paměti jsou absolutní, takže oba balíčky kódu jsou omezeny na 1024 MB paměti (a rezervace měkké záruky stejné). Balíčky kódu (kontejnery nebo procesy) nelze přidělit více paměti než toto omezení a pokus o to má za následek výjimku mimo paměť. Aby vynucení omezení prostředků fungovala, musí být omezení paměti zadaná pro všechny balíčky kódu v rámci balíčku služby.

### <a name="using-application-parameters"></a>Použití parametrů aplikace

Při zadávání nastavení zásad správného řízení prostředků je možné použít [parametry aplikace](service-fabric-manage-multiple-environment-app-configuration.md) ke správě více konfigurací aplikací. Následující příklad ukazuje použití parametrů aplikace:

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

V tomto příkladu jsou výchozí hodnoty parametrů nastaveny pro produkční prostředí, kde by každý balíček služeb získal 4 jádra a 2 GB paměti. Je možné měnit výchozí hodnoty pomocí souborů parametrů aplikace. V tomto příkladu jeden soubor parametrů lze použít pro testování aplikace místně, kde by získat méně prostředků než v produkčním prostředí:

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
> Určení zásad správného řízení prostředků s parametry aplikace je k dispozici počínaje Service Fabric verze 6.1.<br>
>
> Při použití parametrů aplikace k určení zásad správného řízení prostředků, Service Fabric nelze downgraded verzi před verzí 6.1.

## <a name="enforcing-the-resource-limits-for-user-services"></a>Vynucení limitů prostředků pro uživatelské služby

Při použití zásad správného řízení prostředků na služby Service Fabric zaručuje, že tyto služby řízené prostředky nemůže překročit jejich kvótu prostředků, mnoho uživatelů stále potřebují spustit některé ze svých služeb Service Fabric v režimu neřízený. Při použití neřízené služby Service Fabric, je možné narazit na situace, kdy "runaway" neřízené služby spotřebovávají všechny dostupné prostředky na uzly Service Fabric, což může vést k vážným problémům, jako je:

* Hladovění prostředků u jiných služeb spuštěných na uzlech (včetně systémových služeb Service Fabric)
* Uzly končí v nestavu
* Nereagující rozhraní API pro správu clusteru Service Fabric

Chcete-li zabránit výskytu těchto situací, Service Fabric umožňuje *vynutit omezení prostředků pro všechny služby Service Fabric uživatele spuštěné na uzlu* (řízené i neřízené) zaručit, že uživatelské služby nikdy nebude používat více než zadané množství prostředků. Toho je dosaženo nastavením hodnoty pro EnforceUserServiceMetricCapacities config v placementandloadbalancing části ClusterManifest na true. Toto nastavení je ve výchozím nastavení vypnuto.

```xml
<SectionName="PlacementAndLoadBalancing">
    <ParameterName="EnforceUserServiceMetricCapacities" Value="false"/>
</Section>
```

Další poznámky:

* Vynucení limitu prostředků `servicefabric:/_CpuCores` se `servicefabric:/_MemoryInMB` vztahuje pouze na metriky a metriky zdrojů.
* Vynucení limitu prostředků funguje pouze v případě, že jsou k dispozici kapacity uzlů pro metriky prostředků service fabric, a to buď prostřednictvím mechanismu automatického zjišťování, nebo prostřednictvím uživatelů ručně určujících kapacity uzlů (jak je vysvětleno v [nastavení clusteru pro povolení oddílu zásad správného řízení prostředků).](service-fabric-resource-governance.md#cluster-setup-for-enabling-resource-governance)Pokud nejsou nakonfigurovány kapacity uzlů, nelze použít funkci vynucení limitu prostředků, protože service fabric nemůže vědět, kolik prostředků rezervovat pro uživatelské služby.Service Fabric vydá upozornění na stav, pokud "EnforceUserServiceMetricCapacities" je true, ale node kapacity nejsou nakonfigurovány.

## <a name="other-resources-for-containers"></a>Další prostředky pro kontejnery

Kromě procesoru a paměti je možné zadat další omezení prostředků pro kontejnery. Tato omezení jsou určena na úrovni balíčku kódu a jsou použity při spuštění kontejneru. Na rozdíl od procesoru a paměti správce prostředků clusteru neví o těchto prostředcích a nebude pro ně dělat žádné kontroly kapacity ani vyrovnávání zatížení.

* *MemorySwapInMB:* Množství odkládací paměti, které kontejner můžete použít.
* *MemoryReservationInMB:* Měkké omezení pro zásady správného řízení paměti, která je vynucena pouze v případě, že je zjištěna tvrzení paměti v uzlu.
* *CpuPercent*: Procento procesoru, který může kontejner použít. Pokud jsou pro balíček služeb zadány limity procesoru, je tento parametr účinně ignorován.
* *MaximumIOps*: Maximální viopy, které kontejner můžete použít (čtení a zápis).
* *MaximumIOBytesps*: Maximální vi (bajty za sekundu), které kontejner může použít (čtení a zápis).
* *BlockIOWeight*: Hmotnost bloku Vo vzhledem k ostatním kontejnerům.

Tyto prostředky lze kombinovat s procesorem a pamětí. Zde je příklad, jak zadat další prostředky pro kontejnery:

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

* Další informace o Správci prostředků clusteru naleznete [v části Představení Správce prostředků clusteru Service Fabric](service-fabric-cluster-resource-manager-introduction.md).
* Další informace o aplikačním modelu, balíčcích služeb a balíčcích kódu a o tom, jak je repliky mapují, najdete [v tématu Model aplikace v service fabric .](service-fabric-application-model.md)
