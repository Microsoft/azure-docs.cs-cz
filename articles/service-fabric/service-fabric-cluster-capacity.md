---
title: Plánování kapacity clusteru Service Fabric | Microsoft Docs
description: Informace o plánování kapacity clusteru Service Fabric. Nodetypes, operace, odolnost a spolehlivost vrstev
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: ''
ms.assetid: 4c584f4a-cb1f-400c-b61f-1f797f11c982
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/04/2018
ms.author: chackdan
ms.openlocfilehash: b39c22fb45b0e20a3aa7a6dcf59619a87df32ca1
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="service-fabric-cluster-capacity-planning-considerations"></a>Aspekty plánování kapacity služby cluster Service Fabric
Pro všechna produkční nasazení plánování kapacity je důležitý krok. Zde jsou některé položky, které je nutné zvážit jako součást tohoto procesu.

* Počet typů uzlů, které cluster potřebuje začít s
* Vlastnosti každého typu uzlu (velikost, primární, internetový bod, počet virtuálních počítačů, atd.)
* Spolehlivost a odolnost clusteru

Stručně dejte nám zkontrolujte, každá z těchto položek.

## <a name="the-number-of-node-types-your-cluster-needs-to-start-out-with"></a>Počet typů uzlů, které cluster potřebuje začít s
Je třeba nejprve, zjistěte, co se děje clusteru, který vytváříte má být použit pro a toho, jaké typy aplikací se chystáte nasadit do tohoto clusteru. Pokud si nejste vymazat na účel clusteru, se pravděpodobně ještě připravené k zadání proces plánování kapacity.

Určete počet typy uzlů, které cluster potřebuje začít s.  Každý typ uzlu je namapována na sadu škálování virtuálního počítače. Pro každý typ uzlu je pak možné nezávislé vertikální navyšování nebo snižování kapacity, otevírání různých sad portů a používání různých metrik kapacity. Proto rozhodnutí počtu typy uzlů v podstatě dodává na následující aspekty:

* Má vaše aplikace více služeb, a některý z nich musí být veřejné nebo internetové? Typická aplikace obsahují služby front-endu brány, která přijímá vstup z klienta a jeden nebo více back-end služby, které komunikují s front-endové služby. Proto v tomto případě se vám stát tím, že mají alespoň dva typy uzlů.
* Mají vašim službám (které tvoří vaši aplikaci) musí jiné infrastruktuře například větší paměti RAM nebo vyšší cyklů procesoru? Předpokládejme třeba, dejte nám, obsahuje aplikaci, kterou chcete nasadit služby front-end a back endové službě. Front-endu služba může běžet na menší virtuálních (velikosti virtuálních počítačů jako D2), které mají porty, otevřete k Internetu.  Back endové služby, ale je náročné na výpočetní prostředky a je spustit na větší VMs (s velikostí virtuálního počítače jako D4 D6, D15), které nejsou internet setkávají.
  
  V tomto příkladu i když se rozhodnete umístí všechny služby typu jeden uzel, doporučujeme umístit je v clusteru se dvěma typy uzlů.  To umožňuje pro každý typ uzlu tak, aby měl jedinečné vlastnosti, například připojení k Internetu nebo velikost virtuálního počítače. Počet virtuálních počítačů je možné rozšířit nezávisle, stejně.  
* Vzhledem k tomu, že nelze předpovídat budoucí, pomocí faktů, které znáte z a rozhodnout, počet typy uzlů, které vaše aplikace musí začínat. Vždy můžete přidat nebo odebrat typy uzlů později. Cluster Service Fabric musí mít alespoň jeden uzel typu.

## <a name="the-properties-of-each-node-type"></a>Vlastnosti každého typu uzlu
**Typ uzlu** se dají považovat za ekvivalent rolí v cloudových služeb. Typy uzlů definovat velikosti virtuálních počítačů, počet virtuálních počítačů a jejich vlastnosti. Každý typ uzlu, který je definován v clusteru Service Fabric je nastavený jako sada škálování samostatný virtuální počítač. Škálovací sadu virtuálních počítačů je prostředek výpočtů Azure, které můžete použít k nasazení a správě kolekci jako sada virtuálních počítačů. Definovaný jako škálovací sadu virtuálních počítačů distinct, každý typ uzlu je možné škálovat pak nebo dolů nezávisle, mají různé sady otevřené porty a může mít různé kapacity metriky.

Čtení [tento dokument](service-fabric-cluster-nodetypes.md) další podrobnosti o vztah Nodetypes k škálovací sadu virtuálních počítačů, jak pro připojení RDP jedna z instancí, otevřete nové porty atd.

Cluster může mít více než jeden typ uzlu, ale tento typ primárního uzlu (první, kterou definujete na portálu) musí mít aspoň pět virtuálních počítačů pro clustery používat pro produkční zatížení (nebo minimálně tři virtuální počítače pro test clustery). Pokud chcete vytvořit cluster pomocí šablony Resource Manageru, pak Hledat **je primární** atribut v definici typu uzlu. Typ primárního uzlu je typ uzlu, kde jsou umístěny Service Fabric systémových služeb.  

### <a name="primary-node-type"></a>Typ primárního uzlu
Pro cluster s několika typy uzlů budete muset zvolit jeden z nich jako primární. Zde jsou vlastnosti typu primárního uzlu:

* **Minimální velikosti virtuálních počítačů** pro primární uzel je dáno typ **odolnost vrstvy** zvolíte. Výchozí hodnota pro úroveň odolnosti je Bronzová. Posuňte se dolů podrobné informace o novinky vrstvě odolnost a hodnoty, které může trvat.  
* **Minimální počet virtuálních počítačů** pro primární uzel je dáno typ **úroveň spolehlivosti** zvolíte. Výchozí hodnota pro úroveň spolehlivosti je Silver. Posuňte se dolů podrobné informace o novinky úroveň spolehlivosti a hodnoty, které může trvat. 


* Systémové služby Service Fabric (například Správce clusteru služby nebo služby úložiště Image Store) jsou umístěny na primárním uzlu typu a, spolehlivost a odolnost clusteru je dáno vrstvy hodnotu a odolnost úroveň spolehlivosti hodnotou, kterou jste Vyberte pro typ primárního uzlu.

![Snímek obrazovky, který má dva typy uzlů clusteru ][SystemServices]

### <a name="non-primary-node-type"></a>Typ uzlu není primární
Pro cluster s několika typy uzlů je jeden typ primárního uzlu a zbývající z nich jsou není primární. Zde jsou vlastnosti uzlu není primární typu:

* Minimální velikost virtuálních počítačů pro tento typ uzlu je určen podle odolnost vrstvy, které zvolíte. Výchozí hodnota pro úroveň odolnosti je Bronzová. Posuňte se dolů podrobné informace o novinky vrstvě odolnost a hodnoty, které může trvat.  
* Minimální počet virtuálních počítačů pro tento typ uzlu může být jedna. Měli byste ale vybrat toto číslo na základě počtu replik aplikace nebo služby, které chcete spustit v tomto typu uzlu. Po nasazení clusteru je možné zvýšit počet virtuálních počítačů v uzlu typu.

## <a name="the-durability-characteristics-of-the-cluster"></a>Charakteristiky odolnost clusteru
Úroveň odolnosti je slouží k určení systému oprávnění, které mají virtuální počítače se základní infrastruktura Azure. V primárním uzlu, který typ toto oprávnění umožňuje Service Fabric se pozastavit žádosti úrovni infrastruktury virtuálního počítače (například restartování virtuálního počítače, obnovení z Image virtuálního počítače nebo migrace virtuálních počítačů), vliv na požadavky kvora pro systémové služby a vaše stavové služby. V uzlu neprimární typy toto oprávnění umožňuje Service Fabric se pozastavit všechny virtuální počítač úrovně infrastruktury žádosti restartování virtuálního počítače, obnovení z Image virtuálních počítačů, virtuálních počítačů migrace atd., které mají vliv požadavky kvora pro váš stavové služby spuštěné v ní.

Toto oprávnění je vyjádřena v následující hodnoty:

* Pozastavit lze po dobu dvou hodin za UD zlatý - infrastruktury úloh. Gold odolnost lze povolit pouze na úplné uzlu virtuálního počítače SKU L32s, GS5, G5, DS15_v2, D15_v2 atd (obecně uvedeny všechny velikosti virtuálních počítačů v http://aka.ms/vmspecs, které jsou označené jako "Instance je izolovaná hardware vyhrazený pro jednoho zákazníka" v poznámce, jsou virtuální počítače úplné uzlu)
* Stříbrná - úloh infrastruktury můžete pozastaven po dobu 10 minut na jednu UD a je k dispozici na všechny standardní virtuální počítače z jediného jádra a vyšší.
* Bronzová - žádná oprávnění. Toto je výchozí hodnota. Tato úroveň odolnosti používat jenom pro typy uzlů, které používají _pouze_ Bezstavová zatížení. 

> [!WARNING]
> NodeTypes s bronzová odolnost získat _žádné oprávnění_. To znamená, že nebude infrastruktury úlohy, které mají vliv Bezstavová zatížení zastavit nebo zpoždění. Je možné tyto úlohy může ovlivnit stále úlohy, způsobuje výpadek nebo jiných problémů. Pro všechny řazení produkční úlohy běží s aspoň se doporučuje Silver. Minimální počet uzlů 5 pro jakýkoli uzel typ, který má odolnost zlatý nebo Silver musí zachovat. 
> 

Získáte zvolte si úroveň odolnost pro každou z vaší typy uzlů. Můžete zvolit jeden typ uzlu tak, aby měl odolnost úrovně Gold nebo stříbrný a dalších mají bronzová ve stejném clusteru. **Musí zachovat minimální počet uzlů 5 pro jakýkoli uzel typ, který má odolnost zlatý nebo silver**. 

**Výhody používání úrovně Silver nebo zlatý odolnost**
 
1. Snižuje počet požadovaných kroků v rámci škálování operace (to znamená, deaktivace uzlu a odebrat ServiceFabricNodeState se nazývá automaticky)
2. Snižuje riziko ztráty dat z důvodu operaci změnit spouštěná zákazníka SKU virtuálních počítačů v místní nebo provozu infrastruktury Azure.
     
**Nevýhody použití úrovně Silver nebo zlatý odolnost**
 
1. Nasazení pro sadu škálování virtuálního počítače a dalších souvisejících prostředků Azure) může být zpoždění, můžete vypršení časového limitu nebo může být blokovány zcela problémy v clusteru nebo na úrovni infrastruktury. 
2. Zvyšuje počet [události životního cyklu repliky](service-fabric-reliable-services-lifecycle.md) (například primární záměna) z důvodu automatizované deaktivací uzlu během operací infrastruktury Azure.
3. Trvá uzly mimo provoz za období při platformy Azure aktualizace softwaru nebo hardwaru údržby, které se vyskytnou aktivity. Během tyto aktivity se může zobrazit uzly se stavem zakážete/zakázáno. To zmenšuje kapacitu vašeho clusteru dočasně, ale nesmí mít dopad na dostupnost aplikace nebo clusteru.

### <a name="recommendations-on-when-to-use-silver-or-gold-durability-levels"></a>Doporučení týkající se použití úrovně Silver nebo zlatý odolnost

Použít Silver nebo zlatý odolnost pro všechny typy uzlů, které jsou hostiteli stavové služby, které byste měli škálování v (snížit počet instancí virtuálního počítače) často, a si přejete, že operace nasazení se odloží a kapacity snížení považuje zjednodušit tyto škálování v operace. Scénáře Škálováním na více systémů (Přidání instance virtuálních počítačů) do zvoleného vrstvy odolnost nejsou dostupné, jenom škálování v nepodporuje.

### <a name="changing-durability-levels"></a>Změna úrovně odolnosti
- Typy uzlů s odolnost úrovně Silver nebo zlatý nelze převést na bronzová nižší.
- Upgrade z bronzová Silver nebo zlatý může trvat několik hodin.
- Při změně úrovně odolnosti, ujistěte se, zda je aktualizace v obou Service Fabric konfigurace rozšíření v prostředku VMSS a v definici typu uzlu v prostředku clusteru Service Fabric. Tyto hodnoty se musí shodovat.

### <a name="operational-recommendations-for-the-node-type-that-you-have-set-to-silver-or-gold-durability-level"></a>Provozní doporučení pro uzel zadejte, že jste nastavili stříbrným nebo gold odolnost úrovně.

1. Zachovat cluster a aplikace v pořádku po celou dobu a ujistěte se, že aplikace reagovat na všechny [služby události životního cyklu repliky](service-fabric-reliable-services-lifecycle.md) (jako jsou repliky v sestavení zasekl) včas.
2. Přijmout bezpečnější způsoby, jak udělat změnu SKU virtuálních počítačů (škálování nahoru/dolů): Změna SKU virtuálních počítačů sady škálování virtuálního počítače je ze své podstaty nezabezpečené operace a proto je nutno Pokud je to možné. Tady je proces postupovat, aby se zabránilo běžné problémy.
    - **Pro neprimární nodetypes:** se doporučuje vytvořit novou sadu škálování virtuálního počítače, je třeba změnit omezení umístění služby, které zahrnují nový typ sady škálování virtuálních počítačů nebo uzel a potom snížit na původní instanci sadu škálování virtuálního počítače počet na hodnotu 0, jeden uzel v čase (to je zajistit, že odebrání uzlů nemají vliv spolehlivost clusteru).
    - **Primární NodeType:** doporučujeme neměnit SKU virtuálních počítačů typu primárního uzlu. Změna typu primárního uzlu, který není podporován SKU. Pokud z důvodu pro nové SKU je kapacita, doporučujeme přidávání více instancí. Pokud tento není možné, vytvořte nový cluster a [obnovit stav aplikace](service-fabric-reliable-services-backup-restore.md) (pokud existuje) z původního clusteru. Není nutné obnovit všechny služby stavu systému, že jsou vytvořeny při nasazení aplikací do nového clusteru. Pokud jste právě spuštěna bezstavové aplikace v clusteru, je všechny, které můžete provést nasazení aplikací do nového clusteru, máte žádné položky k obnovení. Pokud se rozhodnete přejít nepodporované trasy a chcete změnit SKU virtuálního počítače, proveďte úpravy definici modelu nastavit škálování virtuálního počítače, aby odpovídala nové verze SKU. Pokud cluster obsahuje pouze jeden typ uzlu, ujistěte se, že všechny stavové aplikace reagovat na všechny [služby události životního cyklu repliky](service-fabric-reliable-services-lifecycle.md) (jako je zablokované repliky v sestavení) v a včasné a které vaše služba repliky sestavit Doba trvání je méně než pět minut (úroveň stříbrným odolnost). 


> [!WARNING]
> Změna velikosti virtuálního počítače SKU pro škálovatelné sady virtuálních počítačů není spuštěna alespoň stříbrným odolnost se nedoporučuje. Změna velikosti virtuálních počítačů SKU je operace destruktivní dat místní infrastrukturu. Bez alespoň některé možnost zpoždění nebo sledovat tuto změnu je možné, že operaci může způsobit ztrátu dat pro stavové služby nebo způsobit další nepředpokládaného provozní problémy, i pro Bezstavová zatížení. 
> 
    
3. Udržovat minimální počet pět uzlů pro všechny sadu škálování virtuálního počítače s odolnost úrovně Gold nebo Silver povoleno
4. Není odstranit náhodných instance virtuálních počítačů, vždy použijte sadu škálování virtuálního počítače vertikálně funkce. Odstranění náhodných instance virtuálních počítačů má potenciál vytváření nevyváženosti v instanci virtuálního počítače, které jsou rozloženy UD a FD. Tato nevyváženosti může nepříznivě ovlivnit systémy schopnost správně vyrovnávat mezi instancí a službám replik služby zatížení.
6. Pokud používáte škálování, nastavte pravidla, tak, aby měřítka ve (odebrání instancí virtuálního počítače) se provádějí pouze jeden uzel v čase. Škálování dolů více než jednu instanci v čase není bezpečná.
7. Pokud škálování dolů typu primárního uzlu, je by nikdy škálovat dolů více než jakou úroveň spolehlivosti umožňuje.


## <a name="the-reliability-characteristics-of-the-cluster"></a>Spolehlivost charakteristiky clusteru
Úroveň spolehlivosti se používá k nastavení počtu repliky systémových služeb, které chcete spustit v tomto clusteru na primárním uzlu typu. Další počet replik, větší spolehlivost systémové služby byly v clusteru.  

Úroveň spolehlivosti provést následující hodnoty:

* Platinové - spouštění služeb systému s cílem sady replik počet 9
* Zlatý - spouštění služeb systému s cílem sady replik počet 7
* Stříbrná - spouštění služeb systému s cílem sady replik počet 5 
* Bronzová - spouštění služeb systému s cílem sady replik počet 3

> [!NOTE]
> Úroveň spolehlivosti, vybraných Určuje minimální počet uzlů, které musí mít typ vašeho primárního uzlu. 
> 
> 


### <a name="recommendations-for-the-reliability-tier"></a>Doporučení pro úroveň spolehlivosti.

 Když zvětšit nebo zmenšit velikost clusteru (sum instance virtuálních počítačů v všechny typy uzlů), musíte aktualizovat spolehlivost clusteru z jedné vrstvy do druhé. Díky tomuto aktivuje upgrady clusteru potřeba změnit repliky služby systému nastavit počet. Počkejte, probíhá dokončit před prováděním jiné změny do clusteru, jako je přidání uzlů upgrade.  Můžete sledovat průběh upgradu v Service Fabric Explorer nebo spuštěním [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps)

Zde je doporučení na výběr úroveň spolehlivosti.

| **Velikost clusteru** | **Úroveň spolehlivosti** |
| --- | --- |
| 1 |Nezadávejte parametr úroveň spolehlivosti, systém je vypočte |
| 3 |Bronzová |
| 5 nebo 6|Stříbrný |
| 7 nebo 8 |Zlatá |
| 9 a vyšší |Platinové |




## <a name="primary-node-type---capacity-guidance"></a>Typ primárního uzlu – pokyny kapacity

Tady najdete pokyny pro plánování kapacity typ primárního uzlu

1. **Počet instancí virtuálního počítače na spuštění žádné produkční úlohy v Azure:** musíte zadat minimální velikost písma primárního uzlu 5. 
2. **Počet instancí virtuálního počítače ke spouštění testovací úlohy v Azure** můžete zadat velikost písma minimální primárního uzlu 1 nebo 3. Jeden uzel clusteru, spustí se zvláštní konfiguraci a tak, škálování mimo tento cluster nepodporuje. Jeden uzel clusteru, nemá žádné spolehlivost a proto v šabloně Resource Manager, budete muset odebrat nebo nebyla určit tato konfigurace (není nastavení hodnota konfigurace není dost). Pokud jste nastavili nastavit prostřednictvím portálu jeden uzel clusteru, pak konfigurace se automaticky stará. clustery s uzly 1 a 3 nejsou podporovány pro spuštění úlohy v produkčním prostředí. 
3. **Virtuální počítač SKU:** typ primárního uzlu je kde spouštění systémových služeb, takže SKU virtuálních počítačů zvolíte, musí vzít v úvahu celkové ve špičce zatížení můžete naplánovat umístit do clusteru. Tady je analogie ilustrují I znamenat zde-myslíte typu primárního uzlu jako vaše "plíce", je co nabízí kyslíku k práci, a proto pokud mozku nedostane dostatek kyslíku, vaše textu vyskytne. 

Vzhledem k tomu, že požadavků na kapacitu clusteru je určen podle zatížení, které chcete spustit v clusteru, jsme nelze zadat, že vám kvalitativní pokyny pro konkrétní úlohy, ale tady najdete obecné pokyny vám pomohou začít

Pro produkční zatížení 


- Doporučené SKU virtuálního počítače je standardní D3 nebo standardní D3_V2 nebo ekvivalentní minimálně 14 GB místní SSD.
- Minimální podporované použití virtuálních počítačů SKU je standardní D1 nebo standardní D1_V2 nebo ekvivalentní minimálně 14 GB místní SSD. 
- Částečné základní SKU virtuálního počítače jako standardní A0 nejsou podporovány pro úlohy v produkčním prostředí.
- Standardní SKU A1 není podporována pro produkční zatížení z důvodů výkonu.

> [!WARNING]
> V současné době změna primárního uzlu velikost SKU virtuálních počítačů v clusteru s podporou spuštěné není podporována. Proto pečlivě zvolte typ primárního uzlu SKU virtuálních počítačů, vezme v úvahu vaše budoucí kapacita musí. V tomto okamžiku jediným podporovaným způsobem přesunout vašeho typu primárního uzlu pro nový virtuální počítač SKU (menší nebo větší) je vytvoření nového clusteru s správné kapacitu, nasazení aplikace a pak obnovení stavu aplikace (pokud existuje) z [ nejnovější služby zálohování](service-fabric-reliable-services-backup-restore.md) jste provedli z původního clusteru. Není nutné obnovit všechny služby stavu systému, že jsou vytvořeny při nasazení aplikací do nového clusteru. Pokud jste právě spuštěna bezstavové aplikace v clusteru, je všechny, které můžete provést nasazení aplikací do nového clusteru, máte žádné položky k obnovení.
> 

## <a name="non-primary-node-type---capacity-guidance-for-stateful-workloads"></a>Typ uzlu není primární - kapacity pokyny pro stavová zatížení

Tento průvodce je určený pro stavová zatížení pomocí Service fabric [spolehlivé kolekce nebo reliable Actors](service-fabric-choose-framework.md) , kterou používáte v typ uzlu není primární.


**Počet instancí virtuálního počítače:** pro produkční zatížení, které jsou stavové, doporučujeme je spustit s minimální a cílové repliky počtu 5. To znamená, že v stabilního stavu skončili s replikou (ze sady replik) v každé doméně selhání a upgradu domény. Koncept vrstvy celou spolehlivost pro typ primárního uzlu je způsob, jak toto nastavení pro systémových služeb. Proto stejné částky platí pro vaše stavové služby.

Proto pro úlohy v produkčním prostředí minimální doporučené jiný primární uzlem typu velikost je 5, pokud je v něm spuštěná stavová zatížení.


**Virtuální počítač SKU:** Toto je typ uzlu spuštěným aplikační služby, takže SKU virtuálního počítače jste vybrali pro, musí vzít v úvahu zátěž ve špičce plánujete umístit do každého uzlu. Kapacita potřebám nodetype, je dáno zatížení, které chcete spustit v clusteru, takže jsme nemůže poskytovat vám kvalitativní pokyny pro konkrétní úlohy, ale tady najdete obecné pokyny vám pomohou začít

Pro produkční zatížení 

- Doporučené SKU virtuálního počítače je standardní D3 nebo standardní D3_V2 nebo ekvivalentní minimálně 14 GB místní SSD.
- Minimální podporované použití virtuálních počítačů SKU je standardní D1 nebo standardní D1_V2 nebo ekvivalentní minimálně 14 GB místní SSD. 
- Částečné základní SKU virtuálního počítače jako standardní A0 nejsou podporovány pro úlohy v produkčním prostředí.
- Standardní SKU A1 konkrétně není podporován pro produkční zatížení z důvodů výkonu.


## <a name="non-primary-node-type---capacity-guidance-for-stateless-workloads"></a>Typ uzlu není primární - pokyny Bezstavová zatížení, kapacity

V tomto návodu bezstavových úloh, které běží na neprimární nodetype.

**Počet instancí virtuálního počítače:** pro produkční zatížení, které jsou bezstavové, je velikost písma minimální podporované jiný primární uzlem 2. Umožňuje vám spustit dvě instance bezstavové aplikace a povolení služby zůstanou platné i po ztrátě instance virtuálního počítače. 

> [!NOTE]
> Pokud váš cluster běží v service fabric starší verze než 5.6, z důvodu vadu v modulu runtime (Tento problém vyřešen v 5.6), škálování dolů typu uzlu neprimární na méně než 5, výsledkem stavu clusteru vypnutí není v pořádku, dokud zavoláte [ Odebrat ServiceFabricNodeState cmd](https://docs.microsoft.com/powershell/servicefabric/vlatest/Remove-ServiceFabricNodeState) s názvem příslušné uzlu. Čtení [provést cluster Service Fabric příchozí nebo odchozí](service-fabric-cluster-scale-up-down.md) další podrobnosti
> 
>

**Virtuální počítač SKU:** Toto je typ uzlu spuštěným aplikační služby, takže SKU virtuálního počítače jste vybrali pro, musí vzít v úvahu zátěž ve špičce plánujete umístit do každého uzlu. Kapacita potřebám nodetype, je dáno zatížení, které chcete spustit v clusteru, takže jsme nemůže poskytovat vám kvalitativní pokyny pro konkrétní úlohy, ale tady najdete obecné pokyny vám pomohou začít

Pro produkční zatížení 


- Doporučené SKU virtuálního počítače je standardní D3 nebo standardní D3_V2 nebo ekvivalentní. 
- Minimální podporované použití virtuálních počítačů SKU je standardní D1 nebo standardní D1_V2 nebo ekvivalentní. 
- Částečné základní SKU virtuálního počítače jako standardní A0 nejsou podporovány pro úlohy v produkčním prostředí.
- Standardní SKU A1 není podporována pro produkční zatížení z důvodů výkonu.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="next-steps"></a>Další postup
Po dokončení plánování kapacity a nastavení clusteru, přečtěte si následující:

* [Zabezpečení clusteru Service Fabric](service-fabric-cluster-security.md)
* [Plánování pro zotavení po havárii](service-fabric-disaster-recovery.md)
* [Nastavit vztah Nodetypes škálování virtuálního počítače](service-fabric-cluster-nodetypes.md)

<!--Image references-->
[SystemServices]: ./media/service-fabric-cluster-capacity/SystemServices.png
