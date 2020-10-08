---
title: Service Fabric požadavky na plánování kapacity clusteru
description: Typy uzlů, odolnost, spolehlivost a další věci, které je potřeba vzít v úvahu při plánování Service Fabricho clusteru.
ms.topic: conceptual
ms.date: 05/21/2020
ms.author: pepogors
ms.custom: sfrev
ms.openlocfilehash: d2b303c22eea9fb46a68bb3c8e36991d47d61554
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/07/2020
ms.locfileid: "91817744"
---
# <a name="service-fabric-cluster-capacity-planning-considerations"></a>Service Fabric požadavky na plánování kapacity clusteru

Plánování kapacity clusteru je důležité pro každé Service Fabric produkční prostředí. Mezi klíčové důležité akce patří:

* **Počáteční počet a vlastnosti *typů uzlů* clusteru**

* **Úroveň *odolnosti* každého typu uzlu**, která určuje Service Fabric oprávnění virtuálních počítačů v infrastruktuře Azure

* **Úroveň *spolehlivosti* clusteru**, která určuje stabilitu Service Fabric systémových služeb a celkovou funkci clusteru

Tento článek vás provede důležitými rozhodovacími body pro každou z těchto oblastí.

## <a name="initial-number-and-properties-of-cluster-node-types"></a>Počáteční počet a vlastnosti typů uzlů clusteru

*Typ uzlu* definuje velikost, číslo a vlastnosti pro sadu uzlů (virtuálních počítačů) v clusteru. Každý typ uzlu, který je definovaný v clusteru Service Fabric, se mapuje na [sadu škálování virtuálního počítače](../virtual-machine-scale-sets/overview.md).

Vzhledem k tomu, že každý typ uzlu je odlišná sada škálování, dá se škálovat směrem nahoru nebo dolů, mít různé sady portů otevřené a mají různé metriky kapacity. Další informace o vztahu mezi typy uzlů a sadami škálování virtuálních počítačů najdete v tématu [Service Fabric typy uzlů clusteru](service-fabric-cluster-nodetypes.md).

Každý cluster vyžaduje jeden **primární typ uzlu**, který spouští důležité systémové služby, které poskytují Service Fabric možnosti platformy. Přestože je možné také použít primární typy uzlů ke spouštění aplikací, doporučuje se je vyhradit výhradně pro spouštění systémových služeb.

**Typy neprimárních uzlů** lze použít k definování aplikačních rolí (například *front-endové* a *back-endové* služby) a k fyzické izolaci služeb v rámci clusteru. Clustery Service Fabric mohou mít nula nebo více typů neprimárních uzlů.

Typ primárního uzlu je nakonfigurován pomocí `isPrimary` atributu v rámci definice typu uzlu v šabloně nasazení Azure Resource Manager. Úplný seznam vlastností typu uzlu naleznete v [objektu NodeTypeDescription](/azure/templates/microsoft.servicefabric/clusters#nodetypedescription-object) . Například použití otevřete libovolný *AzureDeploy.js* v souboru v části [Service Fabric ukázky clusteru](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/) a *vyhledejte na stránce* hledání `nodeTypes` objektu.

### <a name="node-type-planning-considerations"></a>Požadavky na plánování typu uzlu

Počet počátečních uzlů závisí na účelu clusteru a aplikacích a službách, které jsou v něm spuštěné. Zvažte následující otázky:

* ***Má vaše aplikace více služeb a některé z nich musí být veřejné nebo internetové?***

    Typické aplikace obsahují front-endové službu brány, která přijímá vstup od klienta, a jednu nebo více back-endové služby, které komunikují s front-end službami, s oddělenými sítěmi mezi front-end a back-end službami. Tyto případy obvykle vyžadují tři typy uzlů: jeden typ primárního uzlu a dva neprimární typy uzlů (jeden pro front-end službu).

* ***Mají služby, které tvoří vaši aplikaci, různé potřeby infrastruktury, jako je například větší nebo vyšší cykly procesoru?***

    Front-end služba může být často spuštěná na menších virtuálních počítačích (velikosti virtuálních počítačů jako D2), které mají porty otevřené na internetu.  Výpočetní služby náročné na výpočetní výkon můžou být potřeba spustit na větších virtuálních počítačích (s velikostmi virtuálních počítačů, jako je D4, D6, D15), které nejsou přístupné z Internetu. Definování různých typů uzlů pro tyto služby vám umožní zefektivnit a zabezpečit používání základních Service Fabric virtuálních počítačů a umožňuje jim jejich horizontální navýšení kapacity. Další informace o odhadu množství prostředků, které budete potřebovat, najdete v tématu [plánování kapacity pro Service Fabric aplikace](service-fabric-capacity-planning.md) .

* ***Bude nutné, aby vaše aplikační služby měly horizontální navýšení kapacity nad rámec 100 uzlů?***

    Jeden typ uzlu nemůže spolehlivě škálovat nad rámec 100 uzlů na sadu škálování virtuálního počítače pro Service Fabric aplikace. Spuštění více než 100 uzlů vyžaduje další služby Virtual Machine Scale Sets (a tudíž další typy uzlů).

* ***Bude váš cluster rozložen mezi Zóny dostupnosti?***

    Service Fabric podporuje clustery, které jsou rozloženy mezi [zóny dostupnosti](../availability-zones/az-overview.md) , nasazením typů uzlů, které jsou připnuté na konkrétní zóny, a zajišťuje tak vysokou dostupnost vašich aplikací. Zóny dostupnosti vyžadovat další plánování typu uzlu a minimální požadavky. Podrobnosti najdete v tématu [doporučená topologie pro typ primárního uzlu Service Fabricch clusterů, které jsou rozloženy mezi zóny dostupnosti](service-fabric-cross-availability-zones.md#recommended-topology-for-primary-node-type-of-azure-service-fabric-clusters-spanning-across-availability-zones). 

Při určování počtu a vlastností typů uzlů pro počáteční vytvoření clusteru mějte na paměti, že po nasazení clusteru můžete kdykoli přidat, upravit nebo odebrat (neprimární) typy uzlů. [Typy primárních uzlů je také možné upravovat](service-fabric-scale-up-primary-node-type.md) ve spuštěných clusterech (i když tyto operace vyžadují skvělé řešení plánování a opatrnosti v produkčních prostředích).

Dalším aspektem vlastností typu uzlu je úroveň odolnosti, která určuje oprávnění, které virtuální počítače typu uzlu mají v infrastruktuře Azure. Použijte velikost virtuálních počítačů, které jste si zvolili pro svůj cluster, a počet instancí, které přiřadíte pro jednotlivé typy uzlů, které vám pomůžou určit vhodnou úroveň odolnosti pro každý typ uzlu, jak je popsáno dále.

## <a name="durability-characteristics-of-the-cluster"></a>Charakteristiky odolnosti clusteru

*Úroveň trvanlivosti* Určuje oprávnění, která Service Fabric virtuální počítače s podkladovou infrastrukturou Azure. Toto oprávnění umožňuje Service Fabric pozastavit požadavky infrastruktury na úrovni virtuálního počítače (třeba restartování, obnovení obrazu nebo migrace), které mají vliv na požadavky kvora pro Service Fabric systémových služeb a stavové služby.

> [!IMPORTANT]
> Úroveň odolnosti je nastavena na typ uzlu. Pokud není zadaný žádný, použije se *bronzová* vrstva, ale neposkytne automatické upgrady operačního systému. Pro úlohy v produkčním prostředí se doporučuje *stříbrná* nebo *zlatá* trvanlivost.

Následující tabulka uvádí Service Fabric vrstev odolnosti, jejich požadavků a affordances.

| Úroveň odolnosti  | Požadovaný minimální počet virtuálních počítačů | Podporované velikosti virtuálních počítačů                                                                  | Aktualizace provedené v rámci sady škálování virtuálních počítačů                               | Aktualizace a údržba iniciované Azure                                                              | 
| ---------------- |  ----------------------------  | ---------------------------------------------------------------------------------- | ----------------------------------------------------------- | ------------------------------------------------------------------------------------------------------- |
| Gold             | 5                              | Velikosti celého uzlu vyhrazené pro jednoho zákazníka (například L32s úrovně, GS5, G5, DS15_v2 D15_v2) | Může se zpozdit až po schválení clusterem Service Fabric. | Může být pozastaveno po dobu 2 hodin na upgradované doméně, aby se mohly další časy zotavit z předchozích chyb. |
| Silver           | 5                              | Virtuální počítače s jedním jádrem nebo novějším s minimálně 50 GB místní jednotky SSD                      | Může se zpozdit až po schválení clusterem Service Fabric. | Nelze zpozdit na jakékoli významné časové období.                                                    |
| Bronzová          | 1                              | Virtuální počítače s minimálně 50 GB místní jednotky SSD                                              | Nebude zpožděný Service Fabricým clusterem.           | Nelze zpozdit na jakékoli významné časové období.                                                    |

> [!WARNING]
> V případě bronzové odolnosti není k dispozici automatický upgrade pro image operačního systému. I když se [aplikace Orchestration](service-fabric-patch-orchestration-application.md) (určená jenom pro clustery, které nejsou hostované na Azure) *nedoporučují* pro stříbrné nebo vyšší úrovně odolnosti, jedná se o jedinou možnost pro automatizaci aktualizací Windows s ohledem na Service Fabric upgradovacích domén.

> [!IMPORTANT]
> Bez ohledu na úroveň odolnosti spustí operace zrušení [přidělení](/rest/api/compute/virtualmachinescalesets/deallocate) na sadě škálování virtuálního počítače cluster.

### <a name="bronze"></a>Bronzová

Typy uzlů běžící s bronzovou trvanlivostí nezískají žádná oprávnění. To znamená, že úlohy infrastruktury, které mají vliv na stavové úlohy, nebudou zastaveny ani zpožděny. Pro typy uzlů, které spouštějí pouze stavové úlohy, používejte bronzovou odolnost. Pro produkční úlohy se doporučuje používat stříbro nebo vyšší.

### <a name="silver-and-gold"></a>Stříbrná a zlatá

Používejte stříbro nebo zlatou odolnost pro všechny typy uzlů, které hostují stavové služby, které chcete často škálovat, a kde chcete, aby byly operace nasazení zpožděny a aby bylo možné snížit kapacitu pro zjednodušení procesu. Scénáře škálování na více instancí by neměly mít vliv na zvolenou úroveň odolnosti.

#### <a name="advantages"></a>Výhody

* Omezuje počet požadovaných kroků pro operace škálování na více systému (deaktivace uzlu a Remove-ServiceFabricNodeState se nazývají automaticky).
* Snižuje riziko ztráty dat z důvodu místní operace změny velikosti virtuálního počítače a operací infrastruktury Azure.

#### <a name="disadvantages"></a>Nevýhody

* Nasazení do služby Virtual Machine Scale Sets a dalších souvisejících prostředků Azure může vyprší časový limit, být zpožděné nebo zablokované zcela problémy v clusteru nebo na úrovni infrastruktury.
* Zvyšuje počet [událostí životního cyklu repliky](service-fabric-reliable-services-lifecycle.md) (například primární swapy) z důvodu automatizovaného deaktivace uzlu během operací infrastruktury Azure.
* Přebírá po dobu provozu uzlů v době, kdy dochází k aktualizacím softwaru platformy Azure nebo k činnostem údržby hardwaru. V průběhu těchto aktivit se můžou zobrazit uzly se stavem zakázán/zakázáno. Tím se snižuje kapacita clusteru dočasně, ale nemělo by to mít vliv na dostupnost vašeho clusteru nebo aplikací.

#### <a name="best-practices-for-silver-and-gold-durability-node-types"></a>Osvědčené postupy pro typy uzlů odolnosti stříbra a Gold

Použijte tato doporučení pro správu typů uzlů pomocí stříbrné nebo zlaté odolnosti:

* Udržujte cluster a aplikace pořád v pořádku a ujistěte se, že aplikace reagují na všechny [události životního cyklu repliky služby](service-fabric-reliable-services-lifecycle.md) (jako replika v buildu se zablokuje) včas.
* Zajištění bezpečnějšího způsobu změny velikosti virtuálního počítače (horizontální navýšení kapacity) Změna velikosti virtuálního počítače pro sadu škálování virtuálního počítače vyžaduje pečlivé plánování a zvýšení opatrnosti. Podrobnosti najdete v tématu [horizontální navýšení kapacity Service Fabric typu uzlu](service-fabric-scale-up-primary-node-type.md) .
* Udržujte minimální počet pěti uzlů pro všechny sady škálování virtuálních počítačů, které mají povolenou úroveň odolnosti Gold nebo stříbrné. Cluster zadáte chybový stav, pokud budete škálovat pod tuto prahovou hodnotu a budete muset ručně vyčistit stav ( `Remove-ServiceFabricNodeState` ) odebraných uzlů.
* Každá sada škálování virtuálního počítače s úrovní odolnosti stříbrného nebo zlata musí být v Service Fabricm clusteru namapována na vlastní typ uzlu. Mapování několika sad Virtual Machine Scale Sets na jeden typ uzlu zabráníte správnému fungování koordinace mezi Service Fabricm clusterem a infrastrukturou Azure.
* Neodstraňujte náhodné instance virtuálních počítačů, ve funkci vždy používejte škálování Virtual Machine Scale-Scale. Odstranění náhodných instancí virtuálních počítačů může mít za následek vytvoření nerovnováhy v instanci virtuálního počítače mezi [doménami upgradu](service-fabric-cluster-resource-manager-cluster-description.md#upgrade-domains) a [doménami selhání](service-fabric-cluster-resource-manager-cluster-description.md#fault-domains). Tato nerovnováha by mohla negativně ovlivnit schopnost systémů správně vyrovnávat zatížení mezi instancemi služby nebo replikami služeb.
* Pokud používáte automatické škálování, nastavte pravidla tak, aby se operace škálování (odebírání instancí virtuálních počítačů) prováděly pouze v jednom uzlu. Horizontální navýšení kapacity více než jedné instance není bezpečné.
* Při odstraňování nebo rušení přidělování virtuálních počítačů na primárním uzlu nikdy snižte počet přidělených virtuálních počítačů pod tím, co vyžaduje úroveň spolehlivosti. Tyto operace budou v rámci škálované sady s úrovní odolnosti stříbrného nebo zlata zablokované na neomezenou dobu.

### <a name="changing-durability-levels"></a>Změna úrovní odolnosti

V rámci určitých omezení se dá upravit úroveň odolnosti typu uzlu:

* Typy uzlů s úrovní odolnosti stříbrného nebo zlata nejde downgradovat na bronz.
* Upgrade z bronzu na stříbro nebo Gold může trvat několik hodin.
* Když měníte úroveň odolnosti, nezapomeňte ji aktualizovat v konfiguraci rozšíření Service Fabric v prostředku sady škálování virtuálního počítače a v definici typu uzlu ve vašem prostředku Service Fabric clusteru. Tyto hodnoty se musí shodovat.

Další aspektem je, že plánování kapacity je úroveň spolehlivosti pro váš cluster, což určuje stabilitu systémových služeb a celkový cluster, jak je popsáno v následující části.

## <a name="reliability-characteristics-of-the-cluster"></a>Charakteristiky spolehlivosti clusteru

*Úroveň spolehlivosti* clusteru určuje počet replik systémových služeb běžících na primárním uzlu typu clusteru. Víc replik, tím spolehlivější jsou systémové služby (a proto cluster jako celek).

> [!IMPORTANT]
> Úroveň spolehlivosti je nastavena na úrovni clusteru a určuje minimální počet uzlů typu primární uzel. Produkční úlohy vyžadují úroveň spolehlivosti stříbrného (větší nebo rovna pěti uzlům) nebo vyšší.  

Úroveň spolehlivosti může mít následující hodnoty:

* Služba **Platinum** – systémové služby se spouštějí s počtem sad cílových replik za devět.
* **Gold** – systémové služby spuštěné s počtem sad cílových replik 7
* **Stříbrné** – systémové služby se spouštějí s počtem sad cílových replik 5.
* **Bronzové** systémové služby s počtem sad cílových replik tři

Tady je doporučení pro výběr úrovně spolehlivosti. Počet počátečních uzlů je také nastaven na minimální počet uzlů pro úroveň spolehlivosti.


| **Počet uzlů** | **Úroveň spolehlivosti** |
| --- | --- |
| 1 | *Nezadávejte `reliabilityLevel` parametr: systém ho počítá.* |
| 3 | Bronzová |
| 5 nebo 6| Silver |
| 7 nebo 8 | Gold |
| 9 a více | Podpora |

Při zvětšení nebo zmenšení velikosti clusteru (součet instancí virtuálních počítačů ve všech typech uzlů) zvažte aktualizaci spolehlivosti clusteru z jedné úrovně na jinou. Tím se aktivují upgrady clusteru potřebné ke změně počtu sad replik systémových služeb. Před provedením jakýchkoli dalších změn v clusteru, jako je přidání uzlů, počkejte na dokončení probíhajícího upgradu.  Průběh upgradu můžete monitorovat na Service Fabric Explorer nebo spuštěním [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps)

### <a name="capacity-planning-for-reliability"></a>Plánování kapacity pro spolehlivost

Nároky na kapacitu vašeho clusteru se určí podle vašich konkrétních požadavků na zatížení a spolehlivost. V této části najdete obecné pokyny, které vám pomůžou začít s plánováním kapacity.

#### <a name="virtual-machine-sizing"></a>Velikost virtuálního počítače

**U produkčních úloh je doporučená velikost virtuálního počítače (SKU) [standardní D2_V2](../virtual-machines/dv2-dsv2-series.md) (nebo ekvivalentní) s minimálně 50 GB místní SSD, 2 jádry a 4 GiB paměti.** Doporučuje se minimálně 50 GB místní SSD, ale některé úlohy (například s kontejnery Windows) budou potřebovat větší disky. Při výběru jiných [velikostí virtuálních počítačů](../virtual-machines/sizes-general.md) pro produkční úlohy Pamatujte na následující omezení:

- Částečné velikosti virtuálních počítačů, jako je standard a0, se nepodporují.
- *Řada a-Series* Velikosti virtuálních počítačů se z důvodů výkonu nepodporují.
- Virtuální počítače s nízkou prioritou se nepodporují.

#### <a name="primary-node-type"></a>Typ primárního uzlu

**Produkční úlohy** v Azure vyžadují minimálně pět primárních uzlů (instancí virtuálních počítačů) a úroveň spolehlivosti stříbrného. Doporučuje se vyhradit typ primárního uzlu clusteru systémovým službám a použít omezení umístění k nasazení aplikace na typy sekundárních uzlů.

**Testovací zatížení** v Azure může spustit minimálně jeden nebo tři primární uzly. Pokud chcete nakonfigurovat cluster s jedním uzlem, ujistěte se, že `reliabilityLevel` nastavení je v šabloně správce prostředků zcela vynecháno (zadání prázdné hodnoty řetězce pro `reliabilityLevel` není dostatečné). Pokud nastavíte cluster s jedním uzlem nastaveným na Azure Portal, tato konfigurace se provádí automaticky.

> [!WARNING]
> Clustery s jedním uzlem běží se zvláštní konfigurací bez spolehlivosti a nejsou podporované horizontální navýšení kapacity.

#### <a name="non-primary-node-types"></a>Typy bez primárních uzlů

Minimální počet uzlů pro neprimární typ uzlu závisí na konkrétní [úrovni odolnosti](#durability-characteristics-of-the-cluster) typu uzlu. Počet uzlů (a úroveň odolnosti) byste měli naplánovat na základě počtu replik aplikací nebo služeb, které chcete spustit pro daný typ uzlu, a v závislosti na tom, jestli je úloha stavový nebo Bezstavová. Mějte na paměti, že můžete zvýšit nebo snížit počet virtuálních počítačů v typu uzlu kdykoli po nasazení clusteru.

##### <a name="stateful-workloads"></a>Stavové úlohy

Pro stavová produkční zatížení pomocí Service Fabric [spolehlivých kolekcí nebo spolehlivých aktérů](service-fabric-choose-framework.md)se doporučuje minimální a cílová replika pět. V tomto případě se v ustáleném stavu ukončí s replikou (ze sady replik) v každé doméně selhání a upgradovací doméně. Obecně platí, že úroveň spolehlivosti, kterou jste nastavili pro systémové služby, můžete použít jako vodítko pro počet replik, který používáte pro stavové služby.

##### <a name="stateless-workloads"></a>Bezstavové úlohy

U bezstavových produkčních úloh je minimální podporovaná velikost neprimárního typu uzlu tři pro zachování kvora, ale doporučuje se velikost typu uzlu pět.

## <a name="next-steps"></a>Další kroky

Před konfigurací clusteru si Projděte `Not Allowed` [zásady upgradu clusteru](service-fabric-cluster-fabric-settings.md) , které vám umožní zmírnit opětovné vytvoření clusteru, a to z toho důvodu jinak neměnitelné nastavení konfigurace systému.

Další informace o plánování clusteru najdete v těchto tématech:

* [Škálování a plánování výpočetních prostředků](service-fabric-best-practices-capacity-scaling.md)
* [Plánování kapacity pro aplikace Service Fabric](service-fabric-capacity-planning.md)
* [Plánování zotavení po havárii](service-fabric-disaster-recovery.md)

<!--Image references-->
[SystemServices]: ./media/service-fabric-cluster-capacity/SystemServices.png
