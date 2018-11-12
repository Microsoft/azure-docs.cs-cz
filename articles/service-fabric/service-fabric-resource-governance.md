---
title: Azure Service Fabric zásady správného řízení prostředků pro kontejnery a služby | Dokumentace Microsoftu
description: Azure Service Fabric můžete zadat omezení prostředků pro služby spuštěné uvnitř nebo mimo kontejnery.
services: service-fabric
documentationcenter: .net
author: TylerMSFT
manager: timlt
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: twhitney, subramar
ms.openlocfilehash: f2898de030a70d578eb45e81c9ccbef90bce96c8
ms.sourcegitcommit: d372d75558fc7be78b1a4b42b4245f40f213018c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2018
ms.locfileid: "51300468"
---
# <a name="resource-governance"></a>Zásady správného řízení prostředků 

Pokud používáte víc služeb na stejném clusteru nebo uzlu, je možné, že jedna služba může spotřebují více prostředků, omezují další služby v procesu. Tento problém se označuje jako problém "hlučného souseda". Azure Service Fabric umožňuje vývojářům zadat rezervace a limity služby k zajištění prostředky a omezit využití prostředků.

> Než budete pokračovat s tímto článkem, doporučujeme vám seznámit se s [aplikačním modelem Service Fabric](service-fabric-application-model.md) a [model hostingu Service Fabric](service-fabric-hosting-model.md).
>

## <a name="resource-governance-metrics"></a>Metriky zásad správného řízení prostředků 

Zásady správného řízení prostředků je podporována v Service Fabric v souladu s maticí [balíček služby](service-fabric-application-model.md). Prostředky, které jsou přiřazeny k balíčku služby je možné dále rozdělit mezi balíčky kódu. Omezení prostředků, které jsou uvedeny také znamenat rezervované prostředky. Service Fabric podporuje zadávání procesoru a paměti na jeden balíček služby se dvě předdefinované [metriky](service-fabric-cluster-resource-manager-metrics.md):

* *Procesor* (název metriky `servicefabric:/_CpuCores`): logické jádro, které jsou k dispozici na hostitelském počítači. Všechna jádra napříč všemi uzly jsou váha stejné.

* *Paměť* (název metriky `servicefabric:/_MemoryInMB`): je vyjádřena paměti v megabajtech a mapuje se na fyzické paměti, která je k dispozici na počítači.

Pro tyto dvě metriky [Cluster Resource Manageru](service-fabric-cluster-resource-manager-cluster-description.md) sleduje celková kapacita clusteru, zatížení na každém uzlu v clusteru a zbývající prostředky v clusteru. Tyto dvě metriky jsou ekvivalentní pro všechny uživatele nebo vlastní metriky. Všechny existující funkce lze použít s nimi:
* Cluster může být [balanced](service-fabric-cluster-resource-manager-balancing.md) podle tyto dvě metriky (výchozí chování).
* Cluster může být [defragmentovat](service-fabric-cluster-resource-manager-defragmentation-metrics.md) podle tyto dvě metriky.
* Když [Popis clusteru](service-fabric-cluster-resource-manager-cluster-description.md), ve vyrovnávací paměti kapacity můžete nastavit pro tyto dvě metriky.

[Generování sestav dynamického zatížení](service-fabric-cluster-resource-manager-metrics.md) není podporována pro tyto metriky a načte pro tyto metriky jsou definovány v okamžiku vytvoření.

## <a name="resource-governance-mechanism"></a>Mechanismu zásad správného řízení prostředků

Modul runtime Service Fabric aktuálně neposkytuje rezervace pro prostředky. Při otevření procesu nebo kontejneru, modul runtime nastaví omezení prostředků na zatížení, které byly definovány v okamžiku vytvoření. Kromě toho modul runtime odmítne otevření nové balíčky služeb, které jsou k dispozici při překročení prostředků. Abyste lépe pochopili, jak funguje proces, Vezměme si příklad uzlu s dvě jádra procesoru (mechanismus pro řízení paměti je ekvivalentní):

1. Nejprve je kontejner umístěn na uzlu, požadování jedno Procesorové jádro. Modul runtime spustí kontejner a nastaví limit procesoru na jedno jádro. Kontejner nebude možné použít více než jedno jádro.

2. Potom repliku služby je umístěn na uzlu a odpovídající balíček služby určuje limit jedno Procesorové jádro. Modul runtime otevře balíček kódu a nastaví jeho limit procesoru na jedno jádro.

V tomto okamžiku je rovna kapacitě uzlu součet omezení. Proces kontejner spuštěný s jedno jádro a není konfliktu mezi sebou. Service Fabric nebude umisťovat žádné další kontejnery nebo replik, když zadáváte limit procesoru.

Nicméně existují dvě situace, ve kterých může být jiné procesy soupeří o využití procesoru. V těchto situacích může být proces a kontejner v našem příkladu dochází k danému problému hlučného souseda:

* *Kombinování řídí a nespravované služby a kontejnery*: Pokud uživatel vytváří službu bez jakékoli zásady správného řízení prostředků zadané, modul runtime považuje využívání žádné prostředky a můžete umístit na uzlu v našem příkladu. V takovém případě tento nový proces efektivně využívá některé procesoru za služby, které jsou již spuštěny na uzlu cenu. Existují dva způsoby řešení tohoto problému. Buď není kombinovat řídí a nespravované služby ve stejném clusteru, nebo použijte [omezení umístění](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md) tak, aby tyto dva typy služeb není ukládaly na stejnou sadu uzlů.

* *Při spuštění jiným procesem na uzlu, mimo Service Fabric (například služby operačního systému)*: V takovém případě procesu mimo Service Fabric také contends pro procesor s existujícími službami. Řešení tohoto problému je nastavení kapacity uzlů správně k účtu pro režijní náklady na operační systém, jak je znázorněno v následující části.

## <a name="cluster-setup-for-enabling-resource-governance"></a>Nastavení clusteru pro povolení zásad správného řízení prostředků

Při spuštění uzlu a připojí ke clusteru, Service Fabric zjistí dostupné množství paměti a počet dostupných jader a pak nastaví kapacity uzlů pro tyto dva prostředky. 

Ponechte vyrovnávací paměť pro operační systém a jiné procesy běží na uzlu, Service Fabric používá pouze 80 % dostupné prostředky na uzlu. Určuje toto procento lze konfigurovat a lze změnit v manifestu clusteru. 

Tady je příklad toho, jak dát pokyn Service Fabric pomocí 50 % z dostupné kapacity procesoru a 70 % dostupné paměti: 

```xml
<Section Name="PlacementAndLoadBalancing">
    <!-- 0.0 means 0%, and 1.0 means 100%-->
    <Parameter Name="CpuPercentageNodeCapacity" Value="0.5" />
    <Parameter Name="MemoryPercentageNodeCapacity" Value="0.7" />
</Section>
```

Pokud potřebujete úplná ruční nastavení kapacity uzlů, můžete použít regulární mechanismus pro popis uzly v clusteru. Tady je příklad toho, jak nastavit uzel s čtyři jádra a 2 GB paměti: 

```xml
    <NodeType Name="MyNodeType">
      <Capacities>
        <Capacity Name="servicefabric:/_CpuCores" Value="4"/>
        <Capacity Name="servicefabric:/_MemoryInMB" Value="2048"/>
      </Capacities>
    </NodeType>
```

Pokud je povoleno automatické zjišťování dostupných prostředků a kapacity uzlů se ručně definované v manifestu clusteru, Service Fabric zkontroluje, že uzel má dostatek prostředků pro podporu kapacitu, kterou uživatel nastavil:
* Pokud kapacity uzlů, které jsou definovány v manifestu je menší než nebo rovna hodnotě dostupné prostředky na uzlu, Service Fabric používá kapacit, které jsou určené v manifestu.

* Pokud kapacity uzlů, které jsou definovány v manifestu je větší než dostupné prostředky, Service Fabric používá jako kapacity uzlů dostupné prostředky.

Automatické zjišťování dostupných prostředků může být vypnuto, pokud se nevyžaduje. Chcete-li ho vypnout, změňte toto nastavení:

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="AutoDetectAvailableResources" Value="false" />
</Section>
```

Pro zajištění optimálního výkonu toto nastavení by měl také zapnout v manifestu clusteru: 

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="PreventTransientOvercommit" Value="true" /> 
    <Parameter Name="AllowConstraintCheckFixesDuringApplicationUpgrade" Value="true" />
</Section>
```


## <a name="specify-resource-governance"></a>Zadejte zásady správného řízení prostředků 

Omezení zásad správného řízení prostředků jsou určené v manifestu aplikace (ServiceManifestImport oddíl), jak je znázorněno v následujícím příkladu:

```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='http://www.w3.org/2001/XMLSchema-instance'>

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
  
V tomto příkladu balíček služby volá **ServicePackageA** získá jedno jádro na uzlech, kde je umístěný. Tento balíček služby obsahuje dva balíčky kódu (**CodeA1** a **CodeA2**), a jak určit `CpuShares` parametru. Podíl CpuShares 512:256 rozdělí základní napříč balíčky dvě kódu. 

Díky tomu se v tomto příkladu CodeA1 získá dvě třetiny základní a CodeA2 získá jednu třetinu jádro (a softwarově stejného). Pokud CpuShares nejsou určeny pro balíčky kódu, Service Fabric rozdělí jader rovnoměrně mezi nimi.

Omezení paměti jsou absolutní, takže oba balíčky kódu jsou omezená na 1024 MB paměti (a softwarově stejného). Balíčky kódu (kontejnery a procesy) nemůže přidělit víc paměti než toto omezení a pokusili Ano za následek výjimku mimo z důvodu nedostatku paměti. Aby vynucení omezení prostředků fungovala, musí být omezení paměti zadaná pro všechny balíčky kódu v rámci balíčku služby.

### <a name="using-application-parameters"></a>Pomocí parametrů aplikace

Při zadávání zásad správného řízení prostředků je možné použít [parametry aplikace](service-fabric-manage-multiple-environment-app-configuration.md) ke správě více konfigurací aplikací. Následující příklad ukazuje použití parametrů aplikace:

```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='http://www.w3.org/2001/XMLSchema-instance'>

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

V tomto příkladu nastavené výchozí hodnoty parametrů pro produkční prostředí, kde každý balíček služby by získat 4 jádra a 2 GB paměti. Je možné změnit výchozí hodnoty se soubory parametrů aplikace. V tomto příkladu je jeden soubor s parametry použít pro testování aplikace v místním prostředí, kde ji získat méně prostředků než v produkčním prostředí: 

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

> [!IMPORTANT]  Zadat zdroj zásad správného řízení s parametry aplikace je dostupná od s využitím Service Fabric verze 6.1.<br> 
>
> Pokud parametry aplikace se používají k určení zásady správného řízení prostředků, Service Fabric se nedají downgradovat na verzi nižší než verze 6.1. 


## <a name="other-resources-for-containers"></a>Další zdroje informací pro kontejnery
Kromě procesoru a paměti je možné určit další omezení prostředků pro kontejnery. Tato omezení jsou určeny na úrovni balíček kódu a se použijí při spuštění kontejneru. Na rozdíl od s procesoru a paměti, Cluster Resource Manageru není si vědom těchto prostředků a nebude provádět žádné kontroly kapacity nebo Vyrovnávání zatížení pro ně. 

* *MemorySwapInMB*: velikost paměti odkládacího souboru, který můžete použít kontejner.
* *MemoryReservationInMB*: doporučeného limitu pro zásady správného řízení paměti, které je vynucuje pouze v případě, že se detekuje kolize paměti na uzlu.
* *CpuPercent*: procento využití procesoru, které kontejner může použít. Pokud omezení procesoru jsou určeny pro balíček služby, tento parametr je ignorován efektivně.
* *MaximumIOps*: maximální vstupně-výstupních operací, které můžete použít kontejner (čtení a zápis).
* *MaximumIOBytesps*: maximální vstupně-výstupní (bajty za sekundu), můžete použít kontejner (čtení a zápis).
* *BlockIOWeight*: bloku vstupně-výstupních operací váha pro relativní vzhledem k jiných kontejnerů.

Tyto prostředky lze kombinovat s procesoru a paměti. Tady je příklad toho, jak určit další zdroje informací o kontejnerů:

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ResourceGovernancePolicy CodePackageRef="FrontendService.Code" CpuPercent="5"
            MemorySwapInMB="4084" MemoryReservationInMB="1024" MaximumIOPS="20" />
        </Policies>
    </ServiceManifestImport>
```

## <a name="next-steps"></a>Další postup
* Další informace o Cluster Resource Manageru, [Představujeme Service Fabric cluster resource manager](service-fabric-cluster-resource-manager-introduction.md).
* Další informace o modelu aplikace, balíčky služeb a balíčky kódu – a jak k nim – mapování repliky číst [modelování aplikace v Service Fabric](service-fabric-application-model.md).
