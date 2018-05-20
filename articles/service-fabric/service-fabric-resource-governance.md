---
title: Azure Service Fabric prostředků zásad správného řízení pro kontejnery a služby | Microsoft Docs
description: Azure Service Fabric můžete zadat omezení prostředků pro služby spuštěné uvnitř nebo mimo kontejnery.
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: subramar
ms.openlocfilehash: 49c7e2c99cce13880781a67806543b1cde0c12b6
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="resource-governance"></a>Zásady správného řízení prostředků 

Pokud používáte víc služeb na stejném uzlu nebo clusteru, je možné, že jedna služba může spotřebují více prostředků, starving jiných služeb v procesu. Tento problém se označuje jako "aktivní sousedním" problém. Azure Service Fabric umožňuje vývojáři zadejte rezervace a omezení pro službu zajistit prostředky a omezit využití prostředků.

> Než budete pokračovat v tomto článku, doporučujeme vám seznámit se s [model aplikace Service Fabric](service-fabric-application-model.md) a [model hostování Service Fabric](service-fabric-hosting-model.md).
>

## <a name="resource-governance-metrics"></a>Metrika prostředků zásad správného řízení 

Zásady správného řízení prostředků je podporována v Service Fabric v souladu s [balíček služby](service-fabric-application-model.md). Prostředky, které jsou přiřazeny k balíčku služby lze dále rozdělit mezi balíčky kódu. Omezení prostředků, které jsou určené taky znamenat rezervace prostředků. Service Fabric podporuje určení procesoru a paměti na balíček služby s dvě předdefinované [metriky](service-fabric-cluster-resource-manager-metrics.md):

* *Procesor* (název metriky `servicefabric:/_CpuCores`): logická jádra, která je k dispozici na hostitelském počítači. Všechny jader pro všechny uzly jsou vážené stejné.

* *Paměť* (název metriky `servicefabric:/_MemoryInMB`): paměti je vyjádřeno v megabajtech a se mapuje na fyzické paměti, která je k dispozici na počítači.

Pro tyto dvě metriky [správce prostředků clusteru](service-fabric-cluster-resource-manager-cluster-description.md) sleduje celkové clusteru kapacitu, zatížení na každém uzlu v clusteru a zbývající prostředků v clusteru. Tyto dvě metriky odpovídají na jiné uživatele nebo vlastní metriku. Všechny existující funkce lze použít s nimi:
* Cluster může být [vyrovnáváním](service-fabric-cluster-resource-manager-balancing.md) podle tyto dvě metriky (výchozí nastavení).
* Cluster může být [defragmentovat](service-fabric-cluster-resource-manager-defragmentation-metrics.md) podle tyto dvě metriky.
* Když [popisující cluster](service-fabric-cluster-resource-manager-cluster-description.md), ve vyrovnávací paměti kapacity lze nastavit pro tyto dvě metriky.

[Dynamické načtení reporting](service-fabric-cluster-resource-manager-metrics.md) není podporována pro tyto metriky a načte pro tyto metriky, které jsou definovány v okamžiku vytvoření.

## <a name="resource-governance-mechanism"></a>Mechanismus zásad správného řízení prostředků

Modulu runtime Service Fabric aktuálně neposkytuje rezervace pro prostředky. Při otevření procesu nebo kontejner, modul runtime nastaví limitů prostředků na zatížení, které byly definovány v okamžiku vytvoření. Kromě toho modul runtime odmítne otevření nové balíčky služeb, které jsou k dispozici, pokud se překročí prostředky. Abyste lépe pochopili, jak tento proces funguje, podívejme příkladem uzel s dvěma jader procesoru (mechanismus pro řízení paměti je ekvivalentní):

1. Nejprve je kontejner umístěn na uzlu, požaduje jádrech procesoru. Modul runtime otevře kontejneru a nastaví limit procesoru jednoho jádra. Kontejner nebudou moci používat víc než jednoho jádra.

2. Poté repliku služby je umístěn na uzlu, a odpovídající balíček služby určuje maximální počet jádrech procesoru. Modul runtime otevře balíček kódu a nastaví její omezení procesoru na jednoho jádra.

V tomto okamžiku je shodná s kapacitou uzlu součet omezení. Proces a kontejner jsou spuštěna s jednoho jádra a nebudou se vzájemně rušit není. Service Fabric není umístit žádné další kontejnery nebo repliky, pokud jejich jsou určení limit procesoru.

Existují však dvou případech, ve kterých může jiné procesy soupeří o využití procesoru. V těchto situacích může proces a kontejner z našich příkladu dochází k danému problému aktivní sousedním:

* *Kombinování služby upraveny a řídí a kontejnery*: Pokud uživatel vytváří službu bez jakékoli zásady správného řízení prostředků zadán, modul runtime jej považuje za využívání žádné prostředky a můžete umístit na uzlu v našem příkladu. V takovém případě tento nový proces efektivně využívá některé procesoru za cenu služby, které je již spuštěn na uzlu. Existují dvě řešení tohoto problému. Buď nemáte kombinovat upraveny a řídí služby ve stejném clusteru, nebo použijte [omezení umístění](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md) tak, aby tyto dva typy služeb není skončili na stejnou sadu uzlů.

* *Při spuštění jiným procesem na uzlu, mimo Service Fabric (například služby operačního systému)*: V této situaci se proces mimo Service Fabric také contends pro procesor s existujícími službami. Řešení tohoto problému je nastavit uzel kapacity správně k účtu pro režijní náklady na operačního systému, jak je znázorněno v následujícím oddílu.

## <a name="cluster-setup-for-enabling-resource-governance"></a>Nastavení clusteru s podporou pro povolení zásad správného řízení prostředků

Pokud uzel spustí a připojí ke clusteru, Service Fabric zjistí, je k dispozici množství paměti a dostupný počet jader a nastaví uzlu kapacity pro tyto dva prostředky. 

Prostor vyrovnávací paměti pro operační systém a jiné procesy může být spuštěn na uzlu, Service Fabric používá jenom 80 % dostupné prostředky na uzlu. Toto procento lze konfigurovat a lze změnit v manifestu clusteru. 

Tady je příklad toho, jak se udělit pokyny nástroji Service Fabric používat 50 % z dostupné kapacity procesoru a 70 % dostupné paměti: 

```xml
<Section Name="PlacementAndLoadBalancing">
    <!-- 0.0 means 0%, and 1.0 means 100%-->
    <Parameter Name="CpuPercentageNodeCapacity" Value="0.5" />
    <Parameter Name="MemoryPercentageNodeCapacity" Value="0.7" />
</Section>
```

Pokud potřebujete úplné ruční instalaci uzlu kapacit, můžete použít regulární mechanismus pro popisující uzly v clusteru. Tady je příklad toho, jak nastavit uzel s čtyři jader a 2 GB paměti: 

```xml
    <NodeType Name="MyNodeType">
      <Capacities>
        <Capacity Name="servicefabric:/_CpuCores" Value="4"/>
        <Capacity Name="servicefabric:/_MemoryInMB" Value="2048"/>
      </Capacities>
    </NodeType>
```

Pokud je povoleno automatické zjišťování dostupných prostředků a kapacity uzlu se ručně definované v manifestu clusteru, Service Fabric zkontroluje, že uzel má dostatek prostředků k podpoře kapacitu, kterou uživatel nastavil:
* Pokud uzel kapacity, které jsou definovány v manifestu jsou menší než nebo rovna hodnotě dostupné prostředky na uzlu, Service Fabric používá kapacity, které jsou určené v manifestu.

* Pokud uzel kapacity, které jsou definovány v manifestu je větší než dostupné prostředky, Service Fabric používá jako uzel kapacity dostupné prostředky.

Automatické zjišťování dostupných prostředků, může být vypnuto, pokud není potřeba. Chcete-li ho vypnout, změňte následující nastavení:

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="AutoDetectAvailableResources" Value="false" />
</Section>
```

Pro optimální výkon toto nastavení by také být zapnut v manifestu clusteru: 

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="PreventTransientOvercommit" Value="true" /> 
    <Parameter Name="AllowConstraintCheckFixesDuringApplicationUpgrade" Value="true" />
</Section>
```


## <a name="specify-resource-governance"></a>Zadejte zásady správného řízení prostředků 

Omezení zásad správného řízení prostředků jsou určené v manifest aplikace (ServiceManifestImport oddílu), jak je znázorněno v následujícím příkladu:

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
  
V tomto příkladu se nazývá balíček služby **ServicePackageA** získá jednoho jádra na uzlech, kde je umístěn. Tento balíček služby obsahuje dva balíčky pro kód (**CodeA1** a **CodeA2**), a určit, jak `CpuShares` parametr. Poměr CpuShares 512:256 vydělí základní mezi dvěma kód balíčky. 

Proto v tomto příkladu CodeA1 získá 2 / 3 jádro a CodeA2 získá jednu třetinu jádro (a rezervace konfigurace soft záruku stejného). Když CpuShares nejsou zadané pro balíčky kódu, Service Fabric vydělí jader rovnoměrně mezi nimi.

Omezení paměti jsou absolutní, takže oba balíčky kódu jsou omezeny na 1024 MB paměti (a rezervace konfigurace soft záruku stejného). Kód balíčky (kontejnerů a procesy) nemůžete přidělit víc paměti než tento limit a pokus Uděláte to tak má za následek výjimku z důvodu nedostatku paměti. Aby vynucení omezení prostředků fungovala, musí být omezení paměti zadaná pro všechny balíčky kódu v rámci balíčku služby.

### <a name="using-application-parameters"></a>Pomocí parametrů aplikace

Při zadávání zásad správného řízení prostředků je možné použít [parametry aplikace](service-fabric-manage-multiple-environment-app-configuration.md) ke správě konfigurací s více aplikací. Následující příklad ukazuje použití aplikace parametry:

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

Výchozí hodnoty parametrů jsou v tomto příkladu nastavené pro produkční prostředí, kde každý balíček služby by získat 4 jádra a 2 GB paměti. Je možné změnit výchozí hodnoty s soubory parametrů aplikace. V tomto příkladu jeden parametr soubor lze použít pro testování aplikace místně, kde ji získat méně prostředků než v provozním prostředí: 

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
> Zadání prostředků zásad správného řízení s parametry aplikace je k dispozici počínaje Service Fabric verze 6.1.<br> 
>
> Pokud parametry aplikace používají k určení zásad správného řízení prostředků, Service Fabric nelze převést na nižší verze starší než verze 6.1. 


## <a name="other-resources-for-containers"></a>Další zdroje informací pro kontejnery
Kromě toho procesoru a paměti je možné zadat další omezení prostředků pro kontejnery. Tato omezení jsou zadány na úrovni balíček kódu a se použijí při spuštění kontejneru. Na rozdíl od s procesoru a paměti, správce prostředků clusteru není informace o těchto prostředků a nebude se žádné kontroly kapacity nebo pro ně Vyrovnávání zatížení. 

* *MemorySwapInMB*: množství paměti odkládacího souboru, které můžete použít kontejner.
* *MemoryReservationInMB*: logicky limit pro řízení paměti, která je vynucená jenom v případě, že je zjištěna kolizí paměti na uzlu.
* *CpuPercent*: procento využití procesoru, který můžete použít kontejneru. Pokud procesor omezení nejsou určené pro balíček služby, je tento parametr efektivně ignorován.
* *MaximumIOps*: maximální hodnotu IOPS, můžete použít kontejner (čtení a zápis).
* *MaximumIOBytesps*: maximální vstupně-výstupní operace (v bajtech za sekundu), můžete použít kontejner (čtení a zápis).
* *BlockIOWeight*: bloku vstupně-výstupní operace vážené pro relativně k jiných kontejnerů.

Tyto prostředky můžete kombinovat s procesoru a paměti. Tady je příklad toho, jak určit další prostředky pro kontejnery:

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
* Další informace o službě Správce prostředků clusteru, přečtěte si [představení správce prostředků clusteru Service Fabric](service-fabric-cluster-resource-manager-introduction.md).
* Další informace o modelu aplikace, balíčky služeb a balíčky kód – a jak mapování repliky na jejich – číst [Model aplikace v Service Fabric](service-fabric-application-model.md).
