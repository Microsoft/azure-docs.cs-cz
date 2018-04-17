---
title: Škálování Azure Service Fabric clusteru | Microsoft Docs
description: Další informace o škálování clusterů Service Fabric v nebo out a nahoru nebo dolů.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/09/2018
ms.author: ryanwi;aljo
ms.openlocfilehash: 897370e48b4eb0db5c99c1102910c89f6f56cc23
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="scaling-service-fabric-clusters"></a>Clustery škálování Service Fabric
Cluster Service Fabric je sada virtuální nebo fyzické počítače, do kterých jsou nasazené a spravovat vaše mikroslužeb připojené k síti. Počítač nebo virtuální počítač, který je součástí clusteru, se nazývá uzel. Clustery může obsahovat potenciálně tisících uzlů. Po vytvoření clusteru Service Fabric, je možné škálovat clusteru vodorovně (změnit počet uzlů) nebo svisle (změnit prostředky uzly).  Je možné škálovat clusteru kdykoli, i když jsou úlohy běžící v clusteru.  Jako cluster škáluje, vaše aplikace se automaticky také škálování.

Proč škálování clusteru? Požadavky na aplikace v průběhu času mění.  Může se stát, že je třeba zvýšit prostředky clusteru splňovat aplikace vyšší zatížení nebo síťový provoz nebo snižte prostředky clusteru při vyžádání zahodí.

### <a name="scaling-in-and-out-or-horizontal-scaling"></a>Škálování a odhlašování nebo vodorovné škálování
Změní počet uzlů v clusteru.  Po připojení ke clusteru, nových uzlů [správce prostředků clusteru](service-fabric-cluster-resource-manager-introduction.md) přesune services k nim, což snižuje zátěž na uzlech existujícího.  Pokud prostředky clusteru nejsou využívány efektivně, může snížit počet uzlů.  Jako uzly nechte cluster, služby přesunout mimo tyto uzly a zátěž zvyšuje ve zbývajících uzlech.  Snížení počtu uzlů v clusteru se systémem v Azure můžete ušetřit peníze, vzhledem k tomu, že platíte za počet virtuálních počítačů můžete použít a není zatížením na těchto virtuálních počítačích.  

- Výhody: Nekonečné škálování, teoreticky.  Pokud vaše aplikace je určená pro škálovatelnost, můžete povolit neomezenou růstu přidáním další uzly.  Nástrojů v prostředí cloudu umožňuje snadno přidávat nebo odebírat uzly, takže lze snadno upravit kapacitu a platit pouze pro prostředky, které můžete použít.  
- Nevýhody: Aplikace musí být [určená pro škálovatelnost](service-fabric-concepts-scalability.md).  Databáze aplikace a trvalost může vyžadovat další práci architektury škálovat také.  [Spolehlivé kolekce](service-fabric-reliable-services-reliable-collections.md) v Service Fabric stavové služby, ale bylo mnohem snazší škálování data aplikací.

### <a name="scaling-up-and-down-or-vertical-scaling"></a>Škálování nahoru a dolů, nebo svislé škálování 
Změní prostředků (procesor, paměť nebo úložiště) uzly v clusteru.
- Výhody: Softwaru a architektura aplikace zůstává stejný.
- Nevýhody: Konečné škálování, protože je omezena na kolik může zvýšit prostředky v jednotlivých uzlech. Výpadky, protože budete muset provést fyzické nebo virtuální počítače do režimu offline za účelem přidat nebo odebrat prostředky.

## <a name="scaling-an-azure-cluster-in-or-out"></a>Škálování clusteru služby Azure příchozí nebo odchozí
Sady škálování virtuálního počítače se Azure výpočtový prostředek, který můžete použít k nasazení a správě kolekci jako sada virtuálních počítačů. Každý typ uzlu, který je definován v clusteru služby Azure je [nastavený jako samostatné škálovací sadu](service-fabric-cluster-nodetypes.md). Každý typ uzlu můžete škálovat v nebo na nezávisle, mají různé sady otevřené porty a může mít různé kapacity metriky. 

Při zvětšení velikosti Azure clusteru, mějte tyto pokyny:
- typy primárního uzlu spuštění úlohy v produkčním prostředí by měl mít vždy pět nebo víc uzlů.
- typy uzlů neprimární spuštěná stavová produkčním prostředí by měl mít vždy pět nebo víc uzlů.
- typy uzlů neprimární systémem bezstavové produkčním prostředí by měl mít vždy dva nebo víc uzlů.
- Jakýkoli typ uzlu [úroveň odolnosti](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) zlatý nebo Silver by měl mít vždy pět nebo víc uzlů.
- Není odeberte náhodných instancí virtuálního počítače/uzly z typu uzlu, vždy použít měřítko sady škálování virtuálního počítače dolů funkce. Odstranění náhodných instance virtuálních počítačů může nepříznivě ovlivnit systémy schopnost správně Vyrovnávání zatížení.
- Pokud pomocí pravidel automatického škálování, nastavte pravidla tak, aby se jeden uzel provádí škálování v (odebrání instance virtuálních počítačů) v čase. Škálování dolů více než jednu instanci v čase není bezpečná.

Vzhledem k tomu, že Service Fabric typy uzlů v clusteru se skládají z sadách škálování virtuálních počítačů v back-end, můžete [nastavení pravidel škálování automaticky nebo ručně škálovat,](service-fabric-cluster-scale-up-down.md) každý uzel typu nebo virtuální počítač škálovací sadu.

### <a name="programmatic-scaling"></a>Programová změna měřítka
V mnoha scénářích [ručně nebo pomocí pravidel automatického škálování škálování clusteru](service-fabric-cluster-scale-up-down.md) jsou dobré řešení. Pro pokročilejší scénáře ale jejich nemusí být vpravo fit. Potenciální nedostatky do těchto přístupů patří:

- Ruční škálování vyžaduje, abyste se přihlaste a explicitní žádost o škálování operace. Pokud operace škálování se často vyžadují, nebo v nepředvídatelným časech, tento přístup nemusí být vhodné řešení.
- Při pravidel automatického škálování odebrání instance škálovací sadu virtuálních počítačů, jejich neodebírejte automaticky znalosti o tomto uzlu z clusteru Service Fabric přidružené Pokud typ uzlu má úroveň odolnosti Silver nebo zlatý. Protože pravidel automatického škálování fungovat ve velkém měřítku, nastavte úroveň (nikoli na úrovni Service Fabric), automatickému škálování pravidla můžete odebrat uzly Service Fabric bez jejich řádně vypínání. Odebrání tohoto článku neslušní uzlu ponechá 'neodstraněných' stav uzlu Service Fabric po škálování v operacích. Pravidelně vyčistěte stav odebraném uzlu v clusteru Service Fabric potřebovat fyzickou (nebo služby).
- Typ uzlu s úrovní odolnost zlatý nebo Silver automaticky vyčistí odebrané uzly, je potřeba žádné další čištění.
- I když jsou [mnoho metriky](../monitoring-and-diagnostics/insights-autoscale-common-metrics.md) podporovaná pravidel automatického škálování, je stále omezenou sadu. Pokud váš scénář vyžaduje škálování podle některé metrika nejsou zahrnuté v dané sadě, pravidel automatického škálování, nemusí být vhodný.

Jak by měl postupovat, Service Fabric škálování závisí na vašem scénáři. Pokud škálování neobvyklé, možnost přidávat nebo odebírat uzly ručně je pravděpodobně dostatečná. Složitější scénáře pravidel automatického škálování a sady SDK vystavení schopnost škálování prostřednictvím kódu programu nabízí výkonné alternativy.

Rozhraní API Azure existují, které umožňují aplikacím prostřednictvím kódu programu pracovat s virtuálním počítačem škálování sady a clusterů Service Fabric. Pokud pro váš scénář nepodporují existující možnosti automatického škálování, tato rozhraní API umožňují k implementaci vlastní logiky škálování. 

Jeden ze způsobů implementace tato "domovské ze" funkce automatického škálování je přidání nové bezstavové služby do aplikace Service Fabric ke správě operací škálování. Vytvoření vlastního škálování služby zajišťuje, že je na nejvyšší úrovni řízení a možnosti přizpůsobení přes aplikaci škálování chování. To může být užitečné pro scénářům, které vyžadují přesnou kontrolu nad při nebo jak aplikace škáluje příchozí nebo odchozí. Tento ovládací prvek se ale dodává s kompromis složitost kódu. Tento přístup, znamená to, budete muset vlastní škálování kód, který je netriviální. V rámci služby `RunAsync` metody sadu aktivační události můžete zjistit, jestli škálování vyžaduje (včetně kontrolu parametry, jako je například maximální velikost clusteru a škálování cooldowns).   

Rozhraní API používá pro interakce sady škálování virtuálního počítače, (obojí ke kontrole aktuální počet instancí virtuálního počítače a upravit ho) je [fluent Azure Compute správy knihovny](https://www.nuget.org/packages/Microsoft.Azure.Management.Compute.Fluent/). Knihovna fluent výpočetní poskytuje rozhraní API snadno použitelné pro interakci s sady škálování virtuálního počítače.  Chcete-li pracovat s samotného clusteru Service Fabric, použijte [System.Fabric.FabricClient](/dotnet/api/system.fabric.fabricclient).

Pro spuštění jako služba v clusteru a být změněna, ale nemusí kód škálování. Obě `IAzure` a `FabricClient` může připojit k jejich přidružené prostředky Azure vzdáleně, tak službu škálování může být snadno konzolové aplikace nebo služba systému Windows spuštěna z mimo aplikace Service Fabric.

Podle těchto omezení, budete pravděpodobně chtít [implementace více přizpůsobené automatické škálování modely](service-fabric-cluster-programmatic-scaling.md).


## <a name="scaling-a-standalone-cluster-in-or-out"></a>Škálování clusteru samostatné příchozí nebo odchozí
Samostatné clustery umožňují nasadit Service Fabric clusteru místně nebo ve zprostředkovateli cloudu podle svého výběru.  Typy uzlů se skládají z fyzických počítačů nebo virtuální počítače, v závislosti na nasazení. Ve srovnání s clustery spuštěná v Azure, je proces škálování clusteru s podporou samostatné zapojí o něco víc.  Musíte ručně změnit počet uzlů v clusteru a poté spuštění upgradu konfigurace clusteru.

Odebrání uzlů může zahájit více upgradů. Některé uzly jsou označené jako `IsSeedNode=”true”` značky a lze identifikovat podle dotazování clusteru manifestu pomocí [Get-ServiceFabricClusterManifest](/powershell/module/servicefabric/get-servicefabricclustermanifest). Odebrání těchto uzlů může trvat déle než jiné, protože uzly počáteční hodnoty musí přesunout v těchto scénářích. Cluster musí zachovat minimálně tři uzly typ primárního uzlu.

Při zvětšení velikosti samostatné clusteru, mějte tyto pokyny:
- Nahrazení primární uzly by měl být provádí jeden uzel za druhým, místo odebráním a potom přidat v dávkách.
- Před odebráním typ uzlu, zkontrolujte, zda jsou všechny uzly odkazující na typ uzlu. Odeberte tyto uzly před odebráním odpovídající typ uzlu. Po odebrání všech odpovídajících uzlů můžete odebrat typ uzlu z clusteru konfigurace a zahájit konfiguraci upgradovat pomocí [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade).

Další informace najdete v tématu [škálování clusteru samostatné](service-fabric-cluster-windows-server-add-remove-nodes.md).

## <a name="scaling-an-azure-cluster-up-or-down"></a>Škálování clusteru služby Azure nahoru nebo dolů
Sady škálování virtuálního počítače se Azure výpočtový prostředek, který můžete použít k nasazení a správě kolekci jako sada virtuálních počítačů. Každý typ uzlu, který je definován v clusteru služby Azure je [nastavený jako samostatné škálovací sadu](service-fabric-cluster-nodetypes.md). Každý typ uzlu je pak možné spravovat samostatně.  Škálování typ uzlu nahoru nebo dolů zahrnuje změnu SKU instancí virtuálního počítače v sadě škálování. 

> [!WARNING]
> Doporučujeme, pokud se spouští ve není změnit SKU virtuálních počítačů typu sady nebo uzel škálování [stříbrnou odolnost nebo větší](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster). Změna velikosti virtuálních počítačů SKU je operace destruktivní dat místní infrastrukturu. Bez některé možnost zpoždění nebo sledovat tuto změnu je možné, že operaci může způsobit ztrátu dat pro stavové služby nebo způsobit další nepředpokládaného provozní problémy, i pro Bezstavová zatížení. 
>

Při zvětšení velikosti Azure clusteru, mějte tyto zásady:
- Pokud škálování dolů typu primárního uzlu, můžete by nikdy snižovat je více než co [úroveň spolehlivosti](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) umožňuje.

Proces nahoru nebo dolů škálování typ uzlu se liší v závislosti na tom, jestli je typem neprimární nebo primární uzel.

### <a name="scaling-non-primary-node-types"></a>Škálování není primární uzel typy
Vytvoření nového typu uzlu s prostředky, které potřebujete.  Aktualizujte omezení umístění spuštěných služby zahrnuté nového typu uzlu.  Postupně (jeden po druhém), zmenšete počet instancí staré počet instancí typu uzlu na nulu, tak, aby spolehlivost clusteru nemá vliv.  Služby budou postupně migrovat do nového typu uzlu jako starý typ uzlu je decommisioned.

### <a name="scaling-the-primary-node-type"></a>Škálování typ primárního uzlu
Doporučujeme neměnit SKU virtuálních počítačů typu primárního uzlu. Pokud potřebujete větší kapacitu clusteru, doporučujeme přidávání více instancí. 

Pokud není možné, můžete vytvořit nový cluster a [obnovit stav aplikace](service-fabric-reliable-services-backup-restore.md) (pokud existuje) z původního clusteru. Není nutné obnovit všechny služby stavu systému, že jsou vytvořeny při nasazení aplikací do nového clusteru. Pokud jste právě spuštěna bezstavové aplikace v clusteru, je všechny, které můžete provést nasazení aplikací do nového clusteru, máte žádné položky k obnovení. Pokud se rozhodnete přejít nepodporované trasy a chcete změnit SKU virtuální počítač, pak provedete změny škálování virtuálního počítače nastavit modelu definici, aby odpovídala nové verze SKU. Pokud váš cluster jenom jeden uzel typ, ujistěte se, že všechny stavové aplikace reagovat na všechny [služby události životního cyklu repliky](service-fabric-reliable-services-lifecycle.md) (jako je zablokované repliky v sestavení) v a včasné a které vaše služba repliky sestavit Doba trvání je méně než pět minut (úroveň stříbrným odolnost). 

## <a name="next-steps"></a>Další postup
* Další informace o [škálovatelnost aplikace](service-fabric-concepts-scalability.md).
* [Škálování clusteru služby Azure příchozí nebo odchozí](service-fabric-tutorial-scale-cluster.md).
* [Škálování clusteru služby Azure prostřednictvím kódu programu](service-fabric-cluster-programmatic-scaling.md) pomocí fluent Azure výpočetní SDK.
* [Škálování clusteru standaone příchozí nebo odchozí](service-fabric-cluster-windows-server-add-remove-nodes.md).

