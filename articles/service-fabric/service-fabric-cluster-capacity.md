---
title: Plánování kapacity clusteru Service Fabric | Microsoft Docs
description: Service Fabric požadavky na plánování kapacity clusteru. NodeType, operace, odolnost a úrovně spolehlivosti
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: chackdan
editor: ''
ms.assetid: 4c584f4a-cb1f-400c-b61f-1f797f11c982
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/09/2019
ms.author: pepogors
ms.openlocfilehash: 1cbbc1fde22262d5841766978d40487f812e0963
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2019
ms.locfileid: "72333111"
---
# <a name="service-fabric-cluster-capacity-planning-considerations"></a>Service Fabric požadavky na plánování kapacity clusteru
Pro jakékoli provozní nasazení je plánování kapacity důležitým krokem. Tady je několik položek, které je třeba vzít v úvahu jako součást tohoto procesu.

* Počet typů uzlů, se kterými cluster musí být spuštěný
* Vlastnosti každého typu uzlu (velikost, primární, internetový přístup, počet virtuálních počítačů atd.)
* Spolehlivost a odolnost clusteru

> [!NOTE]
> Během plánování byste měli zkontrolovat, že **nepovolené** hodnoty zásad upgradu budou minimální. K tomu je potřeba, abyste správně nastavili hodnoty a zmírnili vypalování clusteru z důvodu nezměněných nastavení konfigurace systému. 
> 

Podíváme se na každou z těchto položek krátce.

## <a name="the-number-of-node-types-your-cluster-needs-to-start-out-with"></a>Počet typů uzlů, se kterými cluster musí být spuštěný
Nejdřív musíte zjistit, k čemu se má vytvářený cluster používat.  Jaké druhy aplikací plánujete nasadit do tohoto clusteru? Pokud nechcete, aby se pro účely clusteru neprojevily, pravděpodobně zatím nejste připravení zadat proces plánování kapacity.

Stanovte počet typů uzlů, se kterými je cluster potřeba začít.  Každý typ uzlu je namapován na sadu škálování virtuálního počítače. Pro každý typ uzlu je pak možné nezávislé vertikální navyšování nebo snižování kapacity, otevírání různých sad portů a používání různých metrik kapacity. Proto rozhodnutí o počtu typů uzlů v podstatě nastane v následujících ohledech:

* Má vaše aplikace více služeb a některé z nich musí být veřejné nebo internetové? Typické aplikace obsahují front-endové službu brány, která přijímá vstup od klienta a jednu nebo více back-endové služby, které komunikují s front-end službami. Takže v tomto případě skončíte s alespoň dvěma typy uzlů.
* Mají vaše služby (z vaší aplikace) různé požadavky na infrastrukturu, jako je větší nebo vyšší počet cyklů procesoru? Předpokládejme například, že aplikace, kterou chcete nasadit, obsahuje front-end službu a back-endové služby. Front-end služba může běžet na menších virtuálních počítačích (velikosti virtuálních počítačů jako D2), které mají porty otevřené na internetu.  Back-end služba je však náročné na výpočetní výkon a musí běžet na větších virtuálních počítačích (s velikostí virtuálních počítačů, jako je D4, D6, D15), které nejsou na internetu.
  
  V tomto příkladu, i když se můžete rozhodnout umístit všechny služby na jeden typ uzlu, doporučujeme, abyste je umístili do clusteru se dvěma typy uzlů.  To umožňuje, aby každý typ uzlu měl různé vlastnosti, jako je například připojení k Internetu nebo velikost virtuálního počítače. Počet virtuálních počítačů je možné škálovat nezávisle.  
* Vzhledem k tomu, že nemůžete předpovědět budoucnost, Projděte si fakta, kterou znáte, a vyberte počet typů uzlů, se kterými musí aplikace začít. Můžete kdykoli přidat nebo odebrat typy uzlů později. Cluster Service Fabric musí mít alespoň jeden typ uzlu.

## <a name="the-properties-of-each-node-type"></a>Vlastnosti každého typu uzlu
**Typ uzlu** lze zobrazit jako ekvivalent rolí v Cloud Services. Typy uzlů definují velikosti virtuálních počítačů, počet virtuálních počítačů a jejich vlastnosti. Každý typ uzlu, který je definovaný v clusteru Service Fabric, se mapuje na [sadu škálování virtuálního počítače](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview).  
Každý typ uzlu je odlišná sada škálování a dá se škálovat směrem nahoru nebo dolů, mít různé sady portů otevřené a mají různé metriky kapacity. Další informace o vztazích mezi typy uzlů a sadami škálování virtuálních počítačů, způsobu připojení protokolu RDP k jedné z instancí, způsobu otevření nových portů a tak dále, najdete v tématu [Service Fabric typy uzlů clusteru](service-fabric-cluster-nodetypes.md).

Cluster Service Fabric se může skládat z více než jednoho typu uzlu. V takovém případě se cluster skládá z jednoho primárního typu uzlu a jednoho nebo více typů neprimárních uzlů.

Jeden typ uzlu nemůže spolehlivě škálovat více než 100 uzlů na sadu škálování virtuálního počítače pro SF aplikace; pro spolehlivější dosahování více než 100 uzlů bude nutné přidat další sady škálování virtuálních počítačů.

### <a name="primary-node-type"></a>Typ primárního uzlu

Systémové služby Service Fabric (například služba Správce clusterů nebo služba Image Store) jsou umístěné na primárním typu uzlu. 

![Snímek obrazovky clusteru, který má dva typy uzlů][SystemServices]

* **Minimální velikost virtuálních počítačů** pro typ primárního uzlu závisí na zvolené **úrovni odolnosti** . Výchozí úroveň odolnosti je bronzová. Další podrobnosti najdete v části [charakteristiky odolnosti clusteru](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster) .  
* **Minimální počet virtuálních počítačů** pro typ primárního uzlu závisí na zvolené **úrovni spolehlivosti** . Výchozí úroveň spolehlivosti je stříbrná. Další podrobnosti najdete v tématu [charakteristiky spolehlivosti clusteru](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-reliability-characteristics-of-the-cluster) .  

V šabloně Azure Resource Manager je typ primárního uzlu nakonfigurován s atributem `isPrimary` v rámci [definice typu uzlu](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/clusters#nodetypedescription-object).

### <a name="non-primary-node-type"></a>Typ bez primárního uzlu

V clusteru s více typy uzlů je jeden primární typ uzlu a zbytek není primární.

* **Minimální velikost virtuálních počítačů** pro neprimární typy uzlů závisí na zvolené **úrovni odolnosti** . Výchozí úroveň odolnosti je bronzová. Další informace najdete v tématu [charakteristiky odolnosti clusteru](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster).  
* **Minimální počet virtuálních počítačů** pro typy, které nejsou primárními uzly, je jedna. Toto číslo byste ale měli zvolit na základě počtu replik aplikace nebo služeb, které chcete spustit v tomto typu uzlu. Počet virtuálních počítačů v typu uzlu se dá po nasazení clusteru zvýšit.

## <a name="the-durability-characteristics-of-the-cluster"></a>Charakteristiky odolnosti clusteru
Úroveň odolnosti se používá k označení systému oprávnění, která vaše virtuální počítače mají s podkladovou infrastrukturou Azure. V primárním typu uzlu toto oprávnění umožňuje Service Fabric pozastavit jakoukoli žádost o infrastrukturu na úrovni virtuálního počítače (třeba restartování virtuálního počítače, obnovení virtuálního počítače nebo migraci virtuálního počítače), která mají vliv na požadavky kvora pro systémové služby a stavové služby. V neprimárních typech uzlů toto oprávnění umožňuje Service Fabric pozastavit všechny požadavky infrastruktury na úrovni virtuálního počítače (třeba restartování virtuálního počítače, obnovení bitové kopie virtuálního počítače a migrace virtuálních počítačů), které mají vliv na požadavky na kvorum pro stavové služby.

| Úroveň odolnosti  | Požadovaný minimální počet virtuálních počítačů | Podporované SKU virtuálních počítačů                                                                  | Aktualizace provedené v rámci sady škálování virtuálních počítačů                               | Aktualizace a údržba iniciované Azure                                                              | 
| ---------------- |  ----------------------------  | ---------------------------------------------------------------------------------- | ----------------------------------------------------------- | ------------------------------------------------------------------------------------------------------- |
| Gold             | 5                              | SKU na plný uzel vyhrazené pro jednoho zákazníka (například L32s úrovně, GS5, G5, DS15_v2, D15_v2) | Může se zpozdit až po schválení clusterem Service Fabric. | Dá se pozastavit na 2 hodiny na UD, aby se repliky obnovily z předchozích selhání. |
| Silver           | 5                              | Virtuální počítače s jedním jádrem nebo novějším s minimálně 50 GB místní jednotky SSD                      | Může se zpozdit až po schválení clusterem Service Fabric. | Nelze zpozdit na jakékoli významné časové období.                                                    |
| Bronzová           | 1\. místo                              | Virtuální počítače s minimálně 50 GB místní jednotky SSD                                              | Nebude zpožděný Service Fabricým clusterem.           | Nelze zpozdit na jakékoli významné časové období.                                                    |

> [!WARNING]
> Typy uzlů běžící s bronzovou trvanlivostí nezískají _žádná oprávnění_. To znamená, že úlohy infrastruktury, které mají vliv na stavové úlohy, nebudou zastaveny ani zpožděny, což může mít vliv na vaše úlohy. Používejte pouze bronzové typy uzlů, které spouštějí pouze bezstavové úlohy. Pro produkční úlohy se doporučuje používat stříbro nebo vyšší. 
> 
> Nezávisle na jakékoli úrovni trvanlivosti zruší operace zrušení [přidělení](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/deallocate) v sadě VM scaleing cluster.

**Výhody používání stříbrné nebo zlaté úrovně odolnosti**
 
- Sníží počet požadovaných kroků v operaci škálování na více verzí (to znamená, že deaktivace uzlu a Remove-ServiceFabricNodeState se zavolá automaticky).
- Snižuje riziko ztráty dat z důvodu operace změny skladové položky (SKU) iniciované na místě nebo operací infrastruktury Azure.

**Nevýhody používání stříbrné nebo zlaté úrovně odolnosti**
 
- Nasazení do sady škálování virtuálních počítačů a dalších souvisejících prostředků Azure se můžou zpozdit, můžou vyprší časový limit nebo můžou být zablokované zcela problémy v clusteru nebo na úrovni infrastruktury. 
- Zvyšuje počet [událostí životního cyklu repliky](service-fabric-reliable-services-lifecycle.md) (například primární swapy) z důvodu automatizovaného deaktivace uzlu během operací infrastruktury Azure.
- Přebírá po dobu provozu uzlů v době, kdy dochází k aktualizacím softwaru platformy Azure nebo k činnostem údržby hardwaru. V průběhu těchto aktivit se můžou zobrazit uzly se stavem zakázán/zakázáno. Tím se snižuje kapacita clusteru dočasně, ale nemělo by to mít vliv na dostupnost vašeho clusteru nebo aplikací.

### <a name="recommendations-for-when-to-use-silver-or-gold-durability-levels"></a>Doporučení pro použití úrovní odolnosti stříbra nebo zlata

Použijte stříbro nebo zlatou odolnost pro všechny typy uzlů, které hostují stavové služby, které očekáváte k horizontálnímu navýšení kapacity (omezení počtu instancí virtuálních počítačů), a dáváte přednost tomu, aby se operace nasazení zpozdily a aby se snížila kapacita při zjednodušení těchto škálování. Operations. Scénáře škálování na více instancí (přidávají se instance virtuálních počítačů) Nehrají podle vašeho výběru úrovně odolnosti, stačí pouze škálovat.

### <a name="changing-durability-levels"></a>Změna úrovní odolnosti
- Typy uzlů s úrovní odolnosti stříbrného nebo zlata nejde downgradovat na bronz.
- Upgrade z bronzu na stříbro nebo Gold může trvat několik hodin.
- Když měníte úroveň odolnosti, nezapomeňte ji aktualizovat v konfiguraci rozšíření Service Fabric v prostředku sady škálování virtuálního počítače a v definici typu uzlu ve vašem prostředku Service Fabric clusteru. Tyto hodnoty se musí shodovat.

### <a name="operational-recommendations-for-the-node-type-that-you-have-set-to-silver-or-gold-durability-level"></a>Provozní doporučení pro typ uzlu, který jste nastavili na stříbro nebo zlato úrovně odolnosti.

- Udržujte cluster a aplikace pořád v pořádku a ujistěte se, že aplikace reagují na všechny [události životního cyklu repliky služby](service-fabric-reliable-services-lifecycle.md) (jako replika v buildu se zablokuje) včas.
- Přijmout bezpečnější způsob, jak provést změnu SKU virtuálního počítače (horizontální navýšení kapacity): Změna SKU virtuálního počítače pro sadu škálování virtuálního počítače vyžaduje několik kroků a aspektů. Tady je postup, který vám umožní vyhnout se běžným problémům.
    - **Pro neprimární typy uzlů:** Doporučuje se vytvořit novou sadu škálování virtuálních počítačů, Upravit omezení umístění služby tak, aby zahrnovala novou sadu virtuálních počítačů nebo typ uzlu, a potom omezit starý počet instancí sady škálování virtuálního počítače na hodnotu nula, jeden uzel v čase (to se provede Ujistěte se, že odebrání uzlů nemá vliv na spolehlivost clusteru).
    - **Pro primární typ uzlu:** Pokud se skladová položka, kterou jste vybrali, nastavila na kapacitu a chcete přejít na větší SKU virtuálního počítače, postupujte podle pokynů v části [vertikální škálování pro typ primárního uzlu](https://docs.microsoft.com/azure/service-fabric/service-fabric-scale-up-node-type). 

- Udržujte minimální počet pěti uzlů pro všechny sady škálování virtuálních počítačů, které mají povolenou úroveň odolnosti Gold nebo stříbrné.
- Každá sada škálování virtuálního počítače s úrovní odolnosti stříbrného nebo zlata musí být v Service Fabricm clusteru namapována na vlastní typ uzlu. Mapování několika sad Virtual Machine Scale Sets na jeden typ uzlu zabráníte správnému fungování koordinace mezi Service Fabricm clusterem a infrastrukturou Azure.
- Neodstraňujte náhodné instance virtuálních počítačů, vždy použijte funkci horizontálního navýšení kapacity sady virtuálních počítačů. Odstranění náhodných instancí virtuálních počítačů může mít za následek vytvoření nerovnováhy v instanci virtuálního počítače napříč UD a FD. Tato nerovnováha by mohla negativně ovlivnit schopnost systémů správně vyrovnávat zatížení mezi instancemi služby nebo replikami služeb.
- Pokud používáte automatické škálování, nastavte pravidla tak, aby se škálovat (odebírání instancí virtuálních počítačů) prováděla pouze v jednom uzlu. Horizontální navýšení kapacity více než jedné instance není bezpečné.
- Při odstraňování nebo rušení přidělování virtuálních počítačů u primárního typu uzlu byste nikdy neměli snížit počet přidělených virtuálních počítačů pod tím, co vyžaduje úroveň spolehlivosti. Tyto operace budou v rámci škálované sady s úrovní odolnosti stříbrného nebo zlata zablokované na neomezenou dobu.

## <a name="the-reliability-characteristics-of-the-cluster"></a>Charakteristiky spolehlivosti clusteru
Úroveň spolehlivosti slouží k nastavení počtu replik systémových služeb, které chcete v tomto clusteru spustit na primárním typu uzlu. Větší počet replik, což je spolehlivější systémové služby ve vašem clusteru.  

Úroveň spolehlivosti může mít následující hodnoty:

* Platinum – spusťte systémové služby s cílovou sadou repliky devět.
* Gold – spusťte systémové služby s cílovým nastavením repliky 7.
* Stříbrná – spusťte systémové služby s cílovou sadou repliky o 5. 
* Bronzové – spusťte systémové služby s cílovou sadou repliky tři

> [!NOTE]
> Úroveň spolehlivosti, kterou zvolíte, určuje minimální počet uzlů, které musí mít primární typ uzlu. 
> 
> 

### <a name="recommendations-for-the-reliability-tier"></a>Doporučení pro úroveň spolehlivosti

Když zvětšíte nebo zmenšíte velikost clusteru (součet instancí virtuálních počítačů ve všech typech uzlů), musíte aktualizovat spolehlivost clusteru z jedné úrovně na jinou. Tím se aktivují upgrady clusteru potřebné ke změně počtu sad replik systémových služeb. Před provedením jakýchkoli dalších změn v clusteru, jako je přidání uzlů, počkejte na dokončení probíhajícího upgradu.  Průběh upgradu můžete monitorovat na Service Fabric Explorer nebo spuštěním [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps)

Tady je doporučení pro výběr úrovně spolehlivosti.  Počet počátečních uzlů je také nastaven na minimální počet uzlů pro úroveň spolehlivosti.  Například pro cluster se spolehlivostí Gold se nachází 7 počátečních uzlů.

| **Počet uzlů clusteru** | **Úroveň spolehlivosti** |
| --- | --- |
| 1\. místo |Nezadávejte parametr úrovně spolehlivosti, systém ho vypočítá. |
| 3 |Bronzová |
| 5 nebo 6|Silver |
| 7 nebo 8 |Gold |
| 9 a více |Podpora |

## <a name="primary-node-type---capacity-guidance"></a>Pokyny pro typ primárního uzlu – doprovodné materiály pro kapacitu

Tady je návod pro plánování kapacity typu primárního uzlu:

- **Počet instancí virtuálních počítačů pro spuštění jakékoli provozní úlohy v Azure:** Je nutné zadat minimální velikost primárního typu uzlu na hodnotu 5 a úroveň spolehlivosti stříbrného.  
- **Počet instancí virtuálních počítačů pro spouštění testovacích úloh v Azure** Můžete zadat minimální velikost primárního typu uzlu 1 nebo 3. Cluster s jedním uzlem se spouští se zvláštní konfigurací, takže horizontální navýšení kapacity z tohoto clusteru se nepodporuje. Cluster s jedním uzlem, který nemá žádnou spolehlivost, tedy i v šabloně Správce prostředků, je nutné odebrat/neurčit, že konfigurace (není nastavena hodnota konfigurace není dostatečná). Pokud nastavíte cluster s jedním uzlem nastaveným prostřednictvím portálu, konfigurace se automaticky postará o. Clustery s jedním a třemi uzly nejsou podporované pro spuštěné produkční úlohy. 
- **SKU virtuálního počítače:** Typ primárního uzlu je místo, kde se spouštějí systémové služby, takže si pro ni můžete vybrat SKLADOVOU položku, kterou si pro ni zvolíte, a přitom vzít v úvahu celkové špičkové zatížení, které chcete do clusteru umístit. Tady je příklad, který ukazuje, co znamená, že se jedná o typ primárního uzlu jako "plíce", je to, co poskytuje kyslík pro mozek, a tak v případě, že mozek nezíská dostatek kyslíku, tělo je nedostatečné. 

Vzhledem k tomu, že úlohy, které chcete spustit v clusteru, se určují podle potřeb kapacity clusteru, ale nemůžeme vám poskytnout doprovodné materiály k vašim konkrétním úlohám, ale zde najdete obecné pokyny, které vám pomůžou začít.

Pro produkční úlohy: 

- Doporučuje se vyhradit primární typ uzlu clusterů pro systémové služby a použít omezení umístění k nasazení aplikace do sekundárních NodeType.
- Doporučená SKU virtuálního počítače je standard D2_V2 nebo ekvivalentní minimálně 50 GB místní jednotky SSD.
- Minimální podporovaná skladová SKU virtuálního počítače je Standard_D2_V3 nebo Standard D1_V2 nebo se rovná minimálně 50 GB místní jednotky SSD. 
- Naše doporučení je minimálně 50 GB. Pro vaše úlohy, zejména při spuštění kontejnerů Windows, se vyžadují větší disky. 
- Dílčí skladové jednotky virtuálních počítačů, jako je standard a0, se pro produkční úlohy nepodporují.
- SKU virtuálních počítačů řady se pro produkční úlohy nepodporují z důvodů výkonu.
- Virtuální počítače s nízkou prioritou se nepodporují.

> [!WARNING]
> Změna velikosti SKU virtuálního počítače primárního uzlu na spuštěném clusteru, je operace škálování a zdokumentovaná v dokumentaci k [horizontálnímu škálování sady virtuálních počítačů](virtual-machine-scale-set-scale-node-type-scale-out.md) .

## <a name="non-primary-node-type---capacity-guidance-for-stateful-workloads"></a>Neprimární typ uzlu – doprovodné materiály k kapacitě pro stavové úlohy

Tyto doprovodné materiály jsou pro stavové úlohy využívající [spolehlivé kolekce Service Fabric nebo spolehlivé aktéry](service-fabric-choose-framework.md) , které spouštíte v neprimárním typu uzlu.

**Počet instancí virtuálních počítačů:** U produkčních úloh, které jsou stavové, doporučujeme spouštět je s minimálním a cílovým počtem replik 5. To znamená, že ve stabilním stavu skončíte s replikou (ze sady replik) v každé doméně selhání a upgradovací doméně. Kompletní koncept úrovně spolehlivosti pro typ primárního uzlu je způsob, jak určit toto nastavení pro systémové služby. Totéž platí i pro stavové služby.

V případě produkčních úloh je minimální doporučená velikost neprimárního typu uzlu 5, pokud v ní spouštíte stavové úlohy.

**SKU virtuálního počítače:** Toto je typ uzlu, ve kterém jsou spuštěny vaše aplikační služby, takže si SKU, kterou si pro něj zvolíte, musí vzít v úvahu nejvyšší zatížení, které plánujete do každého uzlu. Nároky na kapacitu typu uzlu jsou určené úlohou, kterou plánujete spustit v clusteru, takže nemůžeme poskytnout doporučení pro konkrétní pracovní vytížení, ale tady je obecné pokyny, které vám pomůžou začít.

Pro produkční úlohy 

- Doporučená SKU virtuálního počítače je standard D2_V2 nebo ekvivalentní minimálně 50 GB místní jednotky SSD.
- Minimální podporovaná skladová SKU virtuálního počítače je Standard_D2_V3 nebo Standard D1_V2 nebo se rovná minimálně 50 GB místní jednotky SSD. 
- Dílčí skladové jednotky virtuálních počítačů, jako je standard a0, se pro produkční úlohy nepodporují.
- SKU virtuálních počítačů řady se pro produkční úlohy nepodporují z důvodů výkonu.

## <a name="non-primary-node-type---capacity-guidance-for-stateless-workloads"></a>Neprimární typ uzlu – pokyny pro kapacitu pro bezstavové úlohy

Tyto pokyny týkající se bezstavových úloh, které běží na neprimárním typu uzlu.

**Počet instancí virtuálních počítačů:** U produkčních úloh, které mají stav bez stavu, je minimální podporovaná velikost neprimárního typu uzlu 2. To vám umožní spouštět dvě bezstavové instance aplikace a umožní, aby služba zadržela ztrátu instance virtuálního počítače. 

**SKU virtuálního počítače:** Toto je typ uzlu, ve kterém jsou spuštěny vaše aplikační služby, takže si SKU, kterou si pro něj zvolíte, musí vzít v úvahu nejvyšší zatížení, které plánujete do každého uzlu. Požadavky na kapacitu typu uzlu závisí na zatížení, které chcete v clusteru spustit. Nemůžeme vám poskytnout informace o kvalitativních pokynech k vašemu konkrétnímu zatížení.  Zde je však uveden seznam hlavních pokynů, které vám pomohou začít.

Pro produkční úlohy 

- Doporučená SKU virtuálního počítače je standardní D2_V2 nebo ekvivalentní. 
- Minimální podporovaná jednotka pro použití virtuálního počítače je Standard D1 nebo Standard, D1_V2 nebo ekvivalentní. 
- Dílčí skladové jednotky virtuálních počítačů, jako je standard a0, se pro produkční úlohy nepodporují.
- SKU virtuálních počítačů řady se pro produkční úlohy nepodporují z důvodů výkonu.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="next-steps"></a>Další kroky
Po dokončení plánování kapacity a nastavení clusteru si přečtěte následující informace:

* [Service Fabric zabezpečení clusteru](service-fabric-cluster-security.md)
* [Service Fabric škálování clusteru](service-fabric-cluster-scaling.md)
* [Plánování zotavení po havárii](service-fabric-disaster-recovery.md)
* [Vztah NodeType k sadě škálování virtuálních počítačů](service-fabric-cluster-nodetypes.md)

<!--Image references-->
[SystemServices]: ./media/service-fabric-cluster-capacity/SystemServices.png
