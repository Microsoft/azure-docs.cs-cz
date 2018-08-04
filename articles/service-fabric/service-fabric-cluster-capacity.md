---
title: Plánování kapacity clusteru Service Fabric | Dokumentace Microsoftu
description: Co zvážit při plánování kapacity clusteru Service Fabric. Úrovně Nodetypes, Operations, odolnosti a spolehlivosti
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: ''
ms.assetid: 4c584f4a-cb1f-400c-b61f-1f797f11c982
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/27/2018
ms.author: chackdan
ms.openlocfilehash: 0a5c73728f939fc239f4af79f5f084867856581a
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/03/2018
ms.locfileid: "39494204"
---
# <a name="service-fabric-cluster-capacity-planning-considerations"></a>Co zvážit při plánování kapacity clusteru Service Fabric
Pro každého produkčního nasazení plánování kapacity je důležitý krok. Tady jsou některé položky, které musíte zvážit jako součást tohoto procesu.

* Počet typů uzlů clusteru se musí začínat
* Vlastnosti každého typu uzlu (velikost, primární, internetového, počet virtuálních počítačů atd.)
* Spolehlivost a odolnost clusteru

> [!NOTE]
> Minimálně byste měli zkontrolovat všechny **nepovoluje** při plánování upgradu hodnoty zásad. Toto je a ujistěte se, že nastavíte hodnoty odpovídajícím způsobem zmírnit vypalování clusteru později z důvodu nastavení konfigurace jakákoliv systému. 
> 

Stručně dejte nám prosím zkontrolujte, každá z těchto položek.

## <a name="the-number-of-node-types-your-cluster-needs-to-start-out-with"></a>Počet typů uzlů clusteru se musí začínat
Nejprve budete muset zjistit, co se děje, kterou vytváříte cluster má být použit pro.  Jaké typy aplikací, které máte v úmyslu nasadit do tohoto clusteru? Pokud si nejste vymazat cíleně clusteru, budou s největší pravděpodobností není ještě připraven k zadání proces plánování kapacity.

Počet typů uzlů clusteru se musí nejprve navázat.  Každý typ uzlu je namapována na škálovací sadu virtuálních počítačů. Pro každý typ uzlu je pak možné nezávislé vertikální navyšování nebo snižování kapacity, otevírání různých sad portů a používání různých metrik kapacity. Proto rozhodnutí počet typů uzlů v podstatě se vrátí do následující aspekty:

* Vaše aplikace mít několik služeb, a některý z nich musí být public nebo připojení k Internetu? Typická aplikace obsahují služba front-end bránu, která přijímá vstup z klienta a jeden nebo více back endovým službám, které komunikují s front-endové služby. Proto v tomto případě ale nakonec bude mít aspoň dva typy uzlů.
* Vaše služby, (, které tvoří vaši aplikaci) mají potřeby různé infrastruktury, jako je například větší paměť RAM nebo vyšší cyklů procesoru? Předpokládejme například, dejte nám prosím, že obsahuje aplikaci, kterou chcete nasadit službu front-endu a back-end služby. Front-end služba může běžet na menších virtuálních počítačů (velikosti virtuálních počítačů jako D2), které mají porty otevřené Internetu.  Back-end služby, ale je náročné na výpočetní prostředky a je potřeba spustit na větších virtuálních počítačů s (s velikostí virtuálních počítačů jako D4, D6, D15), které nejsou internet otočena směrem.
  
  V tomto příkladu i když jste se rozhodli umístit všechny služby na jeden uzel typu, doporučujeme umístit je v clusteru se dvěma typy uzlů.  To umožňuje u každého typu uzlu mít různé vlastnosti, jako je připojení k Internetu nebo velikost virtuálního počítače. Počet virtuálních počítačů je možné škálovat nezávisle na sobě, stejně.  
* Protože nelze předvídat budoucnost, přejděte pomocí faktů, které už znáte a zvolte počet typů uzlů, které vaše aplikace musí začínat. Vždy můžete přidat nebo odebrat typy uzlů později. Cluster Service Fabric musí mít alespoň jeden uzel typu.

## <a name="the-properties-of-each-node-type"></a>Vlastnosti každého typu uzlu
**Typ uzlu** se dají považovat za ekvivalent rolí v cloudových službách. Typy uzlů definují velikosti virtuálních počítačů, počet virtuálních počítačů a jejich vlastnosti. Každý typ uzlu, který je definován v clusteru Service Fabric se mapuje [škálovací sadu virtuálních počítačů](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview).  
Každý typ uzlu je odlišné škálovací nastavit a je možné škálovat nebo dolů nezávisle na sobě mají různé sady otevřených portů a různé metriky kapacity. Další informace o relacích mezi typy uzlů a škálovací sady virtuálních počítačů, jak pro protokol RDP do jedné z instancí, jak otevřít nové porty a atd., naleznete v tématu [typy uzlů clusteru Service Fabric](service-fabric-cluster-nodetypes.md).

Cluster Service Fabric se může skládat z více než jeden typ uzlu. V takovém případě se cluster skládá z jedné primární typ uzlu a jeden nebo více typů bez primárního uzlu.

Jednoho typu může mít maximálně pouze 100 uzlů na škálovací sadu virtuálních počítačů. Budete muset přidat škálovacích sad virtuálních počítačů na cílovém škálování a automatické škálování nemůže automagically přidat škálovací sady virtuálních počítačů. Přidání škálovacích sad virtuálních počítačů na místě k živému clusteru je náročné úlohy a výsledkem je obvykle uživatelům zřizování nových clusterů se příslušný uzel typy zřízený v okamžiku vytvoření. 

### <a name="primary-node-type"></a>Primární typ uzlu

Systémové služby Service Fabric (například Správce clusteru služby nebo Image Store service) se umístí na primární typ uzlu. 

![Snímek obrazovky, který má dva typy uzlů clusteru][SystemServices]

* **Minimální velikost virtuálních počítačů** pro primární uzel typu závisí **úroveň odolnosti** zvolíte. Výchozí úroveň odolnosti je Bronzová. Zobrazit [vlastnosti odolnosti clusteru](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster) další podrobnosti.  
* **Minimálnímu počtu virtuálních počítačů** pro primární uzel typu závisí **úroveň spolehlivosti** zvolíte. Výchozí úroveň spolehlivosti se Silver. Zobrazit [spolehlivost clusteru](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-reliability-characteristics-of-the-cluster) další podrobnosti.  

Ze šablony Azure Resource Manageru, má nakonfigurovanou primární typ uzlu `isPrimary` atribut [definice typu uzlu](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/clusters#nodetypedescription-object).

### <a name="non-primary-node-type"></a>Typ uzlu non-primary

V clusteru s více typy uzlů jeden primární typ uzlu a zbývající jsou jiné než primární.

* **Minimální velikost virtuálních počítačů** pro jiné než primární uzel typy závisí **úroveň odolnosti** zvolíte. Výchozí úroveň odolnosti je Bronzová. Zobrazit [vlastnosti odolnosti clusteru](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster) další podrobnosti.  
* **Minimálnímu počtu virtuálních počítačů** pro typy jiné než primární uzel je jednou. Nicméně měli byste zvolit toto číslo na základě počtu replik aplikace/služby, které chcete spustit v tomto typu uzlu. Po nasazení clusteru je možné zvýšit počet virtuálních počítačů v typu uzlu.

## <a name="the-durability-characteristics-of-the-cluster"></a>Vlastnosti odolnost clusteru
Úroveň odolnosti se používá k označení systému oprávnění, které mají virtuální počítače se základní infrastrukturou Azure. Toto oprávnění umožňuje v primární typ uzlu, Service Fabric na požadavek na pozastavení všech virtuálních počítačů úrovně infrastruktury (například restartování virtuálního počítače, obnovení z Image virtuálního počítače nebo migraci virtuálních počítačů), které ovlivnit požadavky kvora pro systémové služby a stavové služby. Toto oprávnění umožňuje typy jiné než primární uzel Service Fabric se pozastavit všechny virtuální počítač úrovně infrastruktury žádosti (například restartování virtuálního počítače, obnovení z Image virtuálního počítače a migraci virtuálních počítačů), které ovlivňují požadavky kvora pro stavové služby.

| Úroveň odolnosti  | Požadované minimální počet virtuálních počítačů | SKU podporovaných virtuálních počítačů                                                                  | Aktualizace, které provedete na vaše VMSS                               | Aktualizace a údržba iniciované platformou Azure                                                              | 
| ---------------- |  ----------------------------  | ---------------------------------------------------------------------------------- | ----------------------------------------------------------- | ------------------------------------------------------------------------------------------------------- |
| Zlatá             | 5                              | SKU plně uzlu vyhrazený pro jediného zákazníka (například L32s GS5, G5, DS15_v2, D15_v2) | Může zpozdit až do schválení clusterem Service Fabric | Můžete pozastavit po dobu 2 hodin na UD počkejte na další repliky pro obnovení z předchozích chyb |
| Stříbrný           | 5                              | Virtuální počítače s jedním jádrem nebo vyšší                                                        | Může zpozdit až do schválení clusterem Service Fabric | Nejde zpozdit žádné významné časové období                                                    |
| Bronzová           | 1                              | Vše                                                                                | Nebude zpozdit clusteru Service Fabric           | Nejde zpozdit žádné významné časové období                                                    |

> [!WARNING]
> Získat typy uzlů s bronzovou odolnosti _žádná oprávnění_. To znamená, že úlohy infrastruktury, které ovlivnit Bezstavová zatížení nebude mohly zastavit nebo zpoždění, které může mít vliv na vaše úlohy. Použijte bronzová pouze pro typy uzlů, na kterých běží pouze Bezstavová zatížení. Pro produkční úlohy Silver spuštěna nebo k výše se doporučuje. 
>

**Mezi výhody používání stříbrné nebo zlaté úrovně odolnosti**
 
- Snižuje počet požadovaných kroků v rámci operace škálování na méně instancí (to znamená, že uzel deaktivovat a odebrat ServiceFabricNodeState je volána automaticky).
- Snižuje riziko ztráty dat kvůli operaci změnit iniciovaných zákazníkem skladovou Položku virtuálního počítače na místě nebo operace infrastruktury Azure.

**Nevýhody použití stříbrné nebo zlaté úrovně odolnosti**
 
- Nasazení do virtuálního počítače škálovací nastaveno a další související prostředky Azure můžou být zpožděné, můžete časový limit nebo může být blokovány zcela problémy ve vašem clusteru nebo na úrovni infrastruktury. 
- Zvýší počet [události životního cyklu repliky](service-fabric-reliable-services-lifecycle.md) (například primární záměna) z důvodu automatizované deaktivací uzlu během operací infrastruktury Azure.
- Přijímá uzly mimo službu po dobu při platformy Azure prostřednictvím aktualizací softwaru nebo hardwaru údržby, které se vyskytují aktivity. Může se zobrazit uzly se stavem zakázání/zakázáno během těchto činností. Dočasně zmenšuje kapacitu vašeho clusteru, ale by neměla mít vliv na dostupnost aplikace nebo clusteru.

### <a name="recommendations-for-when-to-use-silver-or-gold-durability-levels"></a>Doporučení pro použití stříbrné nebo zlaté úrovně odolnosti

Použít stříbrné nebo zlaté úrovně odolnosti pro všechny typy uzlů, které jsou hostiteli stavové služby, které očekáváte, že pro škálování na méně instancí (snížit počet instancí virtuálních počítačů) často, a chcete raději, že operace nasazení zpozdit a kapacity sníží a zjednodušení tyto škálování na méně instancí operace. Horizontální navýšení kapacity scénáře (přidání instancí virtuálních počítačů) Nepřehrávat do zvoleného úroveň odolnosti, pouze škálování na méně instancí nemá.

### <a name="changing-durability-levels"></a>Změna úrovně odolnosti
- Typy uzlů úrovně odolnosti stříbrné nebo zlaté úrovně se nedají downgradovat na Bronzová.
- Upgrade z bronzová na stříbrné nebo zlaté úrovně může trvat několik hodin.
- Při změně úrovně odolnosti, nezapomeňte aktualizovat v obou Service Fabric konfiguraci rozšíření ve svém prostředku škálovací sady virtuálních počítačů a v definici typu uzlu v prostředku clusteru Service Fabric. Tyto hodnoty musí odpovídat.

### <a name="operational-recommendations-for-the-node-type-that-you-have-set-to-silver-or-gold-durability-level"></a>Provozní doporučení pro uzel typu, že jste nastavili na odolnost stříbrné nebo zlaté úrovně.

- Zachovat cluster a aplikace jsou v pořádku po celou dobu a ujistěte se, že aplikace reagovat na všechny [služby události životního cyklu repliky](service-fabric-reliable-services-lifecycle.md) (jako jsou repliky v sestavení se zasekne) včas.
- Přijmout bezpečnější způsoby, jak provést změnu skladovou Položku virtuálního počítače (škálovat nahoru/dolů): Změna skladovou Položku virtuálního počítače škálovací sady virtuálních počítačů je ze své podstaty nebezpečné operace a proto by se jim vyhnout Pokud je to možné. Tady je proces, můžete provést, abyste se vyhnuli běžných problémů.
    - **Pro typy jiné než primární uzel:** je doporučeno vytvořit novou škálovací sadu virtuálních počítačů, změnit omezení umístění služby zahrnout nový typ virtuálního počítače škálovací sady/uzlu a pak omezit původní instanci virtuálního počítače škálovací sady Spolehněte se na hodnotu 0, jeden uzel v čase (to je zajistit, že odebrání uzlů nemají vliv spolehlivosti clusteru).
    - **Pro primární typ uzlu:** doporučujeme neměnit skladovou Položku virtuálního počítače z primární typ uzlu. Změna typu primárního uzlu SKU se nepodporuje. Je-li důvodem pro novou skladovou Položku kapacity, doporučujeme přidat více instancí. Pokud to není možné vytvořit nový cluster a [obnovit stav aplikace](service-fabric-reliable-services-backup-restore.md) (Pokud je k dispozici) z původního clusteru. Není potřeba obnovit jakékoli služby stavu systému, že se opětovně vytvoří při nasazení aplikací do nového clusteru. Pokud jste právě spuštěna bezstavové aplikace v clusteru, pak vše, co můžete udělat, je nasazení aplikací do nového clusteru, nemáte nic k obnovení. Pokud se rozhodnete přejít nepodporované trasy a chcete změnit skladovou Položku virtuálního počítače, Model definice tak, aby odrážely novou skladovou Položku sady škálování virtuálního počítače pak provedete změny. Pokud váš cluster má pouze jeden uzel typu, ujistěte se, že všechny stavové aplikace reagovat na všechny [služby události životního cyklu repliky](service-fabric-reliable-services-lifecycle.md) (jako jsou repliky v sestavení se zasekne) a včasné a repliku služby sestavení Doba trvání je méně než pět minut (pro úroveň Silver odolnosti). 
    
- Udržujte minimální počet pět uzlů pro všechny škálovací sady virtuálních počítačů, která má úroveň odolnosti Gold a Silver povolena.
- Škálovací sady s úroveň odolnosti stříbrné nebo zlaté úrovně jednotlivých virtuálních počítačů musí být namapovaný na svůj vlastní typ uzlu v clusteru Service Fabric. Mapování více virtuálních počítačů škálovací sady do jednoho typu zabrání koordinaci mezi clusterem Service Fabric a infrastrukturu Azure funguje správně.
- Není odstranit náhodné instancí virtuálních počítačů, vždy použijte škálovací sadu virtuálních počítačů škálovací dolů funkce. Odstranění instancí virtuálních počítačů náhodné má potenciál vytváření rozdílů v instanci virtuálního počítače, které jsou rozděleny mezi UD a FD. Tuto míru nevyrovnaného může nepříznivě ovlivnit systémů správně načíst rovnováhu mezi instance/služby repliky služby.
- Pokud používáte automatické škálování, nastavte pravidla tak, aby škálování (odebírání instancí virtuálních počítačů) se provádějí jenom jeden uzel v čase. Škálování více než jednu instanci v čase není bezpečné.
- Pokud odstranění nebo zrušení přidělení virtuální počítače na primární typ uzlu, měli byste nikdy snížit počet přidělených virtuálních počítačů pod co vyžaduje úroveň spolehlivosti. Tyto operace se zablokuje po neomezenou dobu ve škálovací sadě se úroveň odolnosti stříbrné nebo zlaté úrovně.

## <a name="the-reliability-characteristics-of-the-cluster"></a>Spolehlivost clusteru
Úroveň spolehlivosti se používá k nastavení počtu replik systémových služeb, které chcete spustit v tomto clusteru na primární typ uzlu. Další počet replik, vyšší spolehlivost systémové služby jsou ve vašem clusteru.  

Úroveň spolehlivosti můžete provést následující hodnoty:

* Platinum – spouštění služeb systému s cílem sady replik počet devíti
* Zlatá – spouštění služeb systému s cílem sady replik počet částí sedmidílné série
* Silver – spouštění služeb systému s cílem sady replik počtu pěti 
* Bronzová – spouštění služeb systému s cílovým počtem tři sady replik

> [!NOTE]
> Úroveň spolehlivosti, kterou zvolíte Určuje minimální počet uzlů, které musí mít svůj typ primárního uzlu. 
> 
> 

### <a name="recommendations-for-the-reliability-tier"></a>Doporučení pro úroveň spolehlivosti

Když zvětšit nebo zmenšit velikost vašeho clusteru (součet instancí virtuálních počítačů ve všech typech uzlu), je nutné aktualizovat spolehlivosti clusteru z jedné vrstvy do druhé. Tím se aktivuje upgrady clusteru potřeba změnit repliky služby systému nastaví počet. Počkejte, upgrade, abyste ji mohli dokončit před provedením dalších změn do clusteru, jako je přidání uzlů.  Můžete sledovat průběh upgradu v Service Fabric Exploreru nebo spuštěním [Get ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps)

Tady je doporučení na výběr úrovně spolehlivosti.

| **Velikost clusteru** | **Úroveň spolehlivosti** |
| --- | --- |
| 1 |Nezadávejte parametr úroveň spolehlivosti, systém vypočítá ho |
| 3 |Bronzová |
| 5 nebo 6|Stříbrný |
| 7 nebo 8 |Zlatá |
| 9 a vyšší |Platinum |

## <a name="primary-node-type---capacity-guidance"></a>Primární typ uzlu - pokyny kapacity

Tady je pokyny pro plánování kapacity typ primárního uzlu:

- **Počet instancí virtuálních počítačů pro spuštění jakékoli produkční úlohy v Azure:** je nutné zadat minimální velikost primárního uzlu typu 5. 
- **Počet instancí virtuálních počítačů v Azure spouštět testovací úlohy** můžete zadat velikost písma minimální primárního uzlu 1 nebo 3. Jeden uzel clusteru, spouští se speciální konfigurací a tak, měřítko mimo tento cluster není podporované. Jeden uzel clusteru, nemá žádné spolehlivost a proto v šabloně Resource Manageru, budete muset odebrat nebo není zadejte tuto konfiguraci (není nastavení konfigurační hodnoty nestačí). Pokud jste nastavili jeden uzel clusteru, nastavte si přes portál, potom konfigurace automaticky postará. Clustery s jeden a tři uzly nejsou podporovány pro spouštění úloh v produkčním prostředí. 
- **Skladovou Položku virtuálního počítače:** primární typ uzlu je, kde spouštění systémových služeb, skladovou Položku virtuálního počítače, které zvolíte, musí vzít v úvahu špičky celkové zatížení můžete plánovat umístit do clusteru. Zde je analogicky k ilustraci, co mám na mysli to tady – představit primární typ uzlu jako vaše "plíce", je to, co nabízí oxygen do své mysli, a proto pokud mozek nedostane dostatek oxygen, textu vašeho požadavku odkážete. 

Protože kapacitu clusteru se určuje podle úlohy, které máte v úmyslu spustit v clusteru, nelze zajišťuje, že jste s kvalitativní pokyny pro konkrétní úlohu, ale tady je široké pokyny, které vám pomůžou začít

Pro produkční úlohy: 

- Doporučujeme vyhradit clusterům primární NodeType systémové služby a omezení umístění používá k nasazení aplikace na sekundární NodeTypes.
- Doporučené skladovou Položku virtuálního počítače je Standard D3 nebo standardní D3_V2 nebo ekvivalentní s minimálně 14 GB místní disk SSD.
- Minimální podporované použití skladovou Položku virtuálního počítače je Standard D1 nebo standardní D1_V2 nebo ekvivalentní s minimálně 14 GB místní disk SSD. 
- 14 GB místní SSD je minimální požadavek. Naše doporučení je minimálně 50 GB. Pro vaše úlohy, zejména při spouštění kontejnerů Windows se vyžaduje větších disků. 
- Částečné core skladové položky virtuálních počítačů jako standardní instance A0 nejsou podporovány pro produkční úlohy.
- Standardní A1 SKU není podporována pro produkční úlohy z důvodů výkonu.

> [!WARNING]
> V současné době změna primárního uzlu velikost skladovou Položku virtuálního počítače na spuštěný cluster se nepodporuje. Takže pečlivě primární typ uzlu skladovou Položku virtuálního počítače, zohledněním budoucích potřeb kapacity. V tuto chvíli jediný podporovaný způsob, jak přesunout svůj typ primárního uzlu na nové SKU virtuálního počítače (menší nebo větší) je k vytvoření nového clusteru s správné kapacity, nasazení aplikace a pak opětné uložení stavu aplikace (Pokud je k dispozici) z [ nejnovější služby zálohování](service-fabric-reliable-services-backup-restore.md) jste udělali z původního clusteru. Není potřeba obnovit jakékoli služby stavu systému, že se opětovně vytvoří při nasazování aplikací do nového clusteru. Pokud jste právě spuštěna bezstavové aplikace v clusteru, pak vše, co můžete udělat, je nasazení aplikací do nového clusteru, nemáte nic k obnovení.
> 

## <a name="non-primary-node-type---capacity-guidance-for-stateful-workloads"></a>Typ uzlu non-primary - kapacity pokyny pro zatížení se sdílením stavu

Tento průvodce je určený pro stavová zatížení pomocí Service fabric [spolehlivé kolekce nebo reliable Actors](service-fabric-choose-framework.md) , které běží v jiné než primární uzel typu.

**Počet instancí virtuálních počítačů:** pro produkční úlohy, které jsou stavová, doporučujeme ji spustit s minimální a cílové počet replik 5. To znamená, že do stabilního stavu skončíte se replika (ze sady replik) v každé doméně selhání a upgradovací domény. Koncept úroveň celý spolehlivost pro primární typ uzlu je způsob, jak určit tato nastavení systémových služeb. Tak se stejné hledisko platí pro vaše stavové služby.

Proto pro produkční úlohy, minimální doporučené bez primárního uzlu typu velikost je 5, pokud používáte stavová zatížení v ní.

**Skladovou Položku virtuálního počítače:** Toto je typ uzlu spuštěným aplikační služby, takže skladovou Položku virtuálního počítače zvolíte, musí vzít v úvahu zátěž ve špičce, máte v plánu umístí do každého uzlu. Kapacitu typ uzlu, se určuje podle úlohy, které máte v úmyslu běží v clusteru, takže nemůžeme poskytovat vám kvalitativní pokyny pro konkrétní úlohu, ale tady je široké pokyny, které vám pomůžou začít s

Pro produkční úlohy 

- Doporučené skladovou Položku virtuálního počítače je Standard D3 nebo standardní D3_V2 nebo ekvivalentní s minimálně 14 GB místní disk SSD.
- Minimální podporované použití skladovou Položku virtuálního počítače je Standard D1 nebo standardní D1_V2 nebo ekvivalentní s minimálně 14 GB místní disk SSD. 
- Částečné core skladové položky virtuálních počítačů jako standardní instance A0 nejsou podporovány pro produkční úlohy.
- Skladová položka Standard A1 konkrétně není podporován pro produkční úlohy z důvodů výkonu.

## <a name="non-primary-node-type---capacity-guidance-for-stateless-workloads"></a>Typ uzlu non-primary - pokyny kapacity u bezstavových zatížení

Tento návod bezstavových úloh, které jsou spuštěny v jiné než primární nodetype.

**Počet instancí virtuálních počítačů:** pro produkční úlohy, které jsou bezstavové, minimální podporovaný bez - primární typ velikosti uzlu je 2. To umožňuje spusťte dvě instance bezstavové aplikace, a povolení služby nezbytné k překonání ztráty instance virtuálního počítače. 

**Skladovou Položku virtuálního počítače:** Toto je typ uzlu spuštěným aplikační služby, takže skladovou Položku virtuálního počítače zvolíte, musí vzít v úvahu zátěž ve špičce, máte v plánu umístí do každého uzlu. Kapacitu typu uzlu se určuje podle úlohy, které máte v úmyslu běží v clusteru, takže nemůžeme poskytovat vám kvalitativní pokyny pro konkrétní úlohu, ale tady je široké pokyny, které vám pomůžou začít s

Pro produkční úlohy 

- Doporučené skladovou Položku virtuálního počítače je Standard D3 nebo standardní D3_V2 nebo ekvivalentní. 
- Minimální podporované použití skladovou Položku virtuálního počítače je Standard D1 nebo standardní D1_V2 nebo ekvivalentní. 
- Částečné core skladové položky virtuálních počítačů jako standardní instance A0 nejsou podporovány pro produkční úlohy.
- Standardní A1 SKU není podporována pro produkční úlohy z důvodů výkonu.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="next-steps"></a>Další postup
Po dokončení plánování kapacity a nastavení clusteru, přečtěte si následující:

* [Zabezpečení clusteru Service Fabric](service-fabric-cluster-security.md)
* [Škálování clusteru Service Fabric](service-fabric-cluster-scaling.md)
* [Plánování zotavení po havárii](service-fabric-disaster-recovery.md)
* [Nastavit vztah Nodetypes škálování virtuálního počítače](service-fabric-cluster-nodetypes.md)

<!--Image references-->
[SystemServices]: ./media/service-fabric-cluster-capacity/SystemServices.png
