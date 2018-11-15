---
title: Škálování clusteru v Azure Service Fabric | Dokumentace Microsoftu
description: Další informace o škálování clusterů Azure Service Fabric v nebo navýšení kapacity a nahoru nebo dolů.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: aljo
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/13/2018
ms.author: ryanwi
ms.openlocfilehash: 0890ce0342024229b99d92a2eddba5b49cc59595
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2018
ms.locfileid: "51633933"
---
# <a name="scaling-azure-service-fabric-clusters"></a>Clustery škálování Azure Service Fabric
Cluster Service Fabric je síťově propojená sada virtuálních nebo fyzických počítačů, do které se nasazují a spravují mikroslužby. Počítač nebo virtuální počítač, který je součástí clusteru, se nazývá uzel. Clustery můžou potenciálně obsahovat tisících uzlech. Po vytvoření clusteru Service Fabric, je možné škálovat cluster vodorovně (změnit počet uzlů), nebo svisle (změnit prostředky uzly).  Je možné škálovat cluster v okamžiku, i když spouštění úloh v clusteru.  Škálování clusteru, vaše aplikace automaticky škálovat směrem také.

Proč škálování clusteru? Požadavky na aplikace v průběhu času měnit.  Budete muset zvýšit prostředky clusteru k zajištění provozu aplikace vyšší zatížení nebo sítě nebo omezovat prostředky clusteru, jakmile poptávka poklesne.

## <a name="scaling-in-and-out-or-horizontal-scaling"></a>Dovnitř a ven vertikální nebo horizontální škálování
Změní z počtu uzlů v clusteru.  Jakmile nových uzlů připojit ke clusteru, [Cluster Resource Manageru](service-fabric-cluster-resource-manager-introduction.md) přesune služby k nim, což snižuje zátěž existujících uzlů.  Pokud prostředky clusteru nejsou využívány efektivně, může snížit počet uzlů.  Nechat uzly clusteru, služby přestali ty uzly a zvyšuje na zbývajících uzlech zátěž.  Snížení počtu uzlů v clusteru běžícího v Azure dají ušetřit peníze, protože platíte za počet virtuálních počítačů, které není úloha v těchto virtuálních počítačů a použití.  

- Výhody: Neomezeně Škálované, teoreticky.  Pokud vaše aplikace je určená pro škálovatelnost, můžete povolit neomezené růstu přidáním více uzlů.  Nástrojů v prostředí cloudu umožňuje snadno přidávat a odebírat uzly, tak snadno upravit kapacitu a platíte jenom za prostředky, které používáte.  
- Nevýhody: Aplikace musí být [určená pro škálovatelnost](service-fabric-concepts-scalability.md).  Aplikačních databází a trvalost můžou vyžadovat další architektury práce jako správné škálování.  [Spolehlivé kolekce](service-fabric-reliable-services-reliable-collections.md) v Service Fabric stavové služby, ale to značně zjednodušují škálování dat aplikací.

Škálovací sady virtuálních počítačů jsou výpočetním prostředkem Azure, můžete použít k nasazení a správě kolekce virtuálních počítačů jako sady. Každý typ uzlu, který je definován v clusteru Azure je [nastavit jako samostatné škálovací sada](service-fabric-cluster-nodetypes.md). U každého typu uzlu pak možné horizontálně snížit nebo navýšení kapacity nezávisle na sobě mají různé sady otevřených portů a může mít různé metriky kapacity. 

Při škálování clusteru Azure, mějte podle následujících pokynů:
- zpracovává produkční zátěž s typy primárního uzlu by měl mít vždy pět nebo více uzlů.
- typy jiné než primární uzel spuštěná stavová produkční úlohy by měl vždy mít pět nebo více uzlů.
- typy uzlů neprimární spuštěná Bezstavová produkční úlohy by měl mít vždy dva nebo víc uzlů.
- Jakýkoli typ uzlu [úroveň odolnosti](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) Gold a Silver by měli vždy mít pět nebo více uzlů.
- Není náhodné instance virtuálního počítače/uzly odebrat typ uzlu, vždy používejte škálovací sady škálování virtuálních počítačů mimo provoz funkce. Odstranění instancí virtuálních počítačů náhodné nepříznivě ovlivnit schopnost systémů správně Vyrovnávání zatížení.
- Při použití pravidel automatického škálování, nastavte pravidla tak, aby se jeden uzel provádí škálování v (odebírání instancí virtuálních počítačů) v čase. Škálování více než jednu instanci v čase není bezpečné.

Protože typy uzlů Service Fabric v clusteru skládají se z škálovacích sad virtuálních počítačů v back-endu, můžete [nastavit pravidla automatického škálování nebo ručně škálovat](service-fabric-cluster-scale-up-down.md) každý uzel typu nebo virtuální počítače škálovací sady.

### <a name="programmatic-scaling"></a>Programová změna velikosti
V mnoha případech [škálování clusteru ručně nebo pomocí pravidel automatického škálování](service-fabric-cluster-scale-up-down.md) jsou dobré řešení. Pro pokročilejší scénáře ale jejich nemusí být správné přizpůsobit. Potenciální nedostatky tyto postupy zahrnují:

- Ruční škálování vyžaduje, abyste pro přihlášení a explicitní žádost o operace škálování. Pokud operace škálování často nebo po dobu nepředvídatelné, tento přístup nemusí být dobrým řešením.
- Pokud pravidel automatického škálování odebrání instance škálovací sady virtuálních počítačů, jejich neodebírejte automaticky znalosti o tomto uzlu z přidružených clusteru Service Fabric, pokud typ uzlu neobsahuje úroveň odolnosti stříbrné nebo zlaté úrovně. Vzhledem k tomu, že pravidla automatického škálování pracovat ve velkém měřítku, nastavte úroveň (spíše než na úrovni Service Fabric), pravidel automatického škálování můžete odebrat uzly Service Fabricu bez jejich řádné vypnutí. Odebrání tohoto článku neslušní uzel ponechá "skryté" stav uzlu Service Fabric po operacích škálování na méně instancí. Osoba (nebo služby), třeba tak, aby pravidelně stav odebraném uzlu v clusteru Service Fabric.
- Typ uzlu s úrovní odolnosti Gold a Silver automaticky vyčistí odebrané uzly, takže je potřeba žádné další čištění.
- I když jsou [mnoha metrik](../monitoring-and-diagnostics/insights-autoscale-common-metrics.md) podporovaná pravidel automatického škálování, je stále omezenou sadu. Pokud váš scénář vyžaduje škálování na základě některé metriky nejsou zahrnuta v dané sadě, pravidel automatického škálování, nemusí být vhodný.

Jak by měl postupovat, Service Fabric škálování závisí na vaší situaci. Pokud škálování je běžné, možnost přidávat a odebírat uzly ručně je pravděpodobně dostatečná. Pro složitější scénáře nabízejí výkonné alternativy pravidel automatického škálování a sady SDK vystavení nezávislého škálování prostřednictvím kódu programu.

Rozhraní API pro Azure existují, které umožňují aplikacím prostřednictvím kódu programu pracovat s virtuálního počítače škálovací sady a clustery Service Fabric. Pokud stávající možnosti automatické škálování nefunguje pro váš scénář, tato rozhraní API umožňují implementovat vlastní logiku na škálování. 

Jeden ze způsobů implementace tato "domovské ze" funkce automatického škálování je přidání nové bezstavovou službu do aplikace Service Fabric ke správě operací škálování. Vytváření vlastních, škáluje se služba poskytuje že nejvyšší stupeň ovládacího prvku a přizpůsobitelnost přes aplikaci prvku škálování chování. To může být užitečné pro scénáře nutnosti mít naprostou kontrolu nad a aplikace se škáluje snížení nebo navýšení kapacity. Tento ovládací prvek však zahrnuje kompromis složitost kódu. Tento přístup znamená, že je potřeba vlastní škálování kód, který je netriviální. V rámci služby `RunAsync` metody sady aktivačních událostí můžete určit, zda škálování vyžaduje (včetně kontroly parametrů, jako je například maximální velikost clusteru a škálování cooldowns).   

Rozhraní API používá pro virtuální počítač škálovací sady interakce (obojí ke kontrole aktuální počet instancí virtuálních počítačů a upravte ho) je [fluent knihovny správy Azure Compute](https://www.nuget.org/packages/Microsoft.Azure.Management.Compute.Fluent/). Knihovny fluent výpočetní poskytuje rozhraní API snadno použitelné pro interakci se škálovacími sadami virtuálních počítačů.  Chcete-li pracovat s samotného clusteru Service Fabric, použijte [System.Fabric.FabricClient](/dotnet/api/system.fabric.fabricclient).

Ke spuštění jako služby v clusteru škálování, ale nemusí škálování kódu. Obě `IAzure` a `FabricClient` můžete vzdálené připojení k jejich souvisejících prostředcích Azure, takže škálování služby může být snadno Konzolová aplikace nebo služba Windows spuštěná z mimo aplikací Service Fabric.

Podle těchto omezení, možná budete chtít [implementovat více přizpůsobené automatické škálování modely](service-fabric-cluster-programmatic-scaling.md).

## <a name="scaling-up-and-down-or-vertical-scaling"></a>Vertikální navýšení nebo snížení kapacity nebo vertikální škálování 
Změní prostředky (procesor, paměť nebo úložiště) z uzlů v clusteru.
- Výhody: Softwaru a architekturu aplikace zůstala stejná.
- Nevýhody: Konečné škálování, protože neexistuje omezení můžete zvýšit množství prostředky v jednotlivých uzlech. Výpadky, protože budete muset provést fyzických nebo virtuálních počítačů do režimu offline za účelem přidání nebo odebrání prostředků.

Škálovací sady virtuálních počítačů jsou výpočetním prostředkem Azure, můžete použít k nasazení a správě kolekce virtuálních počítačů jako sady. Každý typ uzlu, který je definován v clusteru Azure je [nastavit jako samostatné škálovací sada](service-fabric-cluster-nodetypes.md). Každý typ uzlu je pak spravovat samostatně.  Typ uzlu škálování směrem nahoru nebo dolů postup zahrnuje změnu SKU instance virtuálních počítačů ve škálovací sadě. 

> [!WARNING]
> Doporučujeme vám, pokud se spouští ve není změnit skladovou Položku virtuálního počítače škálovací sady/uzel typu [Silver odolnosti nebo větší](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster). Změna velikosti skladové položky virtuálních počítačů je operace destruktivní dat místní infrastrukturu. Bez některé možnosti zpoždění nebo sledovat tuto změnu je možné, že operace může způsobit ztrátu dat pro stavové služby nebo způsobit jiné nepředvídatelné provozní problémy, dokonce i u bezstavových zatížení. 
>

Při škálování clusteru Azure, mějte následujícími pravidly:
- Pokud vertikálního snižování typ primárního uzlu by nikdy ji převedete dolů více, než nabízí [úroveň spolehlivosti](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) umožňuje.

Proces typ uzlu vertikálního navýšení nebo snížení kapacity se liší v závislosti na tom, zda je typ neprimární nebo primárního uzlu.

### <a name="scaling-non-primary-node-types"></a>Škálování typy jiné než primární uzel
Vytvoření nového typu uzlu s prostředky, které potřebujete.  Aktualizujte omezení umístění spuštění služby zahrnuté nového typu uzlu.  Postupně (postupně po jednom), snížit počet instancí starý počet instancí typu uzlu na nulu, tak, aby spolehlivosti clusteru nemá vliv.  Služby se postupně migrovat do nového typu uzlu decommisioned je původní typ uzlu.

### <a name="scaling-the-primary-node-type"></a>Škálování primární typ uzlu
Doporučujeme neměnit skladovou Položku virtuálního počítače z primární typ uzlu. Pokud potřebujete víc kapacity clusteru, doporučujeme přidat více instancí. 

Pokud není možné, můžete vytvořit nový cluster a [obnovit stav aplikace](service-fabric-reliable-services-backup-restore.md) (Pokud je k dispozici) z původního clusteru. Není potřeba obnovit jakékoli služby stavu systému, že se opětovně vytvoří při nasazení aplikací do nového clusteru. Pokud jste právě spuštěna bezstavové aplikace v clusteru, pak vše, co můžete udělat, je nasazení aplikací do nového clusteru, nemáte nic k obnovení. Pokud se rozhodnete přejít nepodporované trasy a chcete změnit skladovou Položku virtuálního počítače, Model definice tak, aby odrážely novou skladovou Položku sady škálování virtuálního počítače pak provedete změny. Pokud váš cluster má pouze jeden uzel typu, ujistěte se, že všechny stavové aplikace reagovat na všechny [služby události životního cyklu repliky](service-fabric-reliable-services-lifecycle.md) (jako jsou repliky v sestavení se zasekne) a včasné a repliku služby sestavení Doba trvání je méně než pět minut (pro úroveň Silver odolnosti). 

## <a name="next-steps"></a>Další postup
* Další informace o [aplikace škálovatelnost](service-fabric-concepts-scalability.md).
* [Škálování clusteru Azure snížení nebo navýšení kapacity](service-fabric-tutorial-scale-cluster.md).
* [Škálování clusteru Azure prostřednictvím kódu programu](service-fabric-cluster-programmatic-scaling.md) pomocí fluent Azure compute SDK.
* [Horizontální snížení nebo navýšení kapacity clusteru samostatná](service-fabric-cluster-windows-server-add-remove-nodes.md).

