---
title: Plánování kapacity clusteru Service Fabric
description: Důležité informace o plánování kapacity clusteru Service Fabric. Stupně nodetypů, operací, odolnosti a spolehlivosti
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: pepogors
ms.openlocfilehash: 6e60fc10dd7e0eec24de4a089d09d914624dcfbc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258912"
---
# <a name="service-fabric-cluster-capacity-planning-considerations"></a>Aspekty plánování kapacity clusteru Service Fabric
Důležitým krokem každého produkčního nasazení je plánování kapacity. Zde jsou některé z položek, které musíte zvážit jako součást tohoto procesu.

* Počet typů uzlů, které váš cluster potřebuje, aby začal
* Vlastnosti každého typu uzlu (velikost, primární, internet čelí, počet virtuálních počítače, atd.)
* Spolehlivost a odolnost clusteru

> [!NOTE]
> Měli byste minimálně zkontrolovat všechny **nepovolené** hodnoty zásad upgradu během plánování. Tím zajistíte, že správně nastavíte hodnoty a později zmírníte vypalování clusteru z důvodu neměnného nastavení konfigurace systému. 
> 

Podívejme se na každou z těchto položek.

## <a name="the-number-of-node-types-your-cluster-needs-to-start-out-with"></a>Počet typů uzlů, které váš cluster potřebuje, aby začal
Nejprve je třeba zjistit, k čemu bude cluster, který vytváříte, použit.  Jaké typy aplikací plánujete nasadit do tohoto clusteru? Pokud nejste jasné, o účelu clusteru, s největší pravděpodobností ještě nejsou připraveni vstoupit do procesu plánování kapacity.

Zjistěte počet typů uzlů, se kterými musí cluster začít.  Každý typ uzlu je mapován na škálovací sadu virtuálních strojů. Pro každý typ uzlu je pak možné nezávislé vertikální navyšování nebo snižování kapacity, otevírání různých sad portů a používání různých metrik kapacity. Takže rozhodnutí o počtu typů uzlů v podstatě přijde na následující úvahy:

* Má vaše aplikace více služeb a musí být kterákoli z nich veřejná nebo internetová? Typické aplikace obsahují službu front-endbrány, která přijímá vstup y od klienta a jednu nebo více back-endových služeb, které komunikují s front-endovými službami. Takže v tomto případě skončíte s alespoň dva typy uzlů.
* Mají vaše služby (které tvoří vaši aplikaci) různé potřeby infrastruktury, jako je větší paměť RAM nebo vyšší cykly procesoru? Předpokládejme například, že aplikace, kterou chcete nasadit, obsahuje službu front-end u a back-endovou službu. Front-endová služba může běžet na menších virtuálních počítačích (velikosti virtuálních počítačů, jako je D2), které mají porty otevřené pro internet.  Back-endová služba je však náročná na výpočty a potřebuje běžet na větších virtuálních počítačích (s velikostmi virtuálních zařízení, jako jsou D4, D6, D15), které nejsou orientované na internet.
  
  V tomto příkladu, i když se můžete rozhodnout umístit všechny služby na jeden typ uzlu, doporučujeme umístit je do clusteru se dvěma typy uzlů.  To umožňuje každému typu uzlu mít odlišné vlastnosti, jako je připojení k internetu nebo velikost virtuálního počítače. Počet virtuálních mkénců lze škálovat nezávisle, také.  
* Vzhledem k tomu, že nelze předpovědět budoucnost, přejděte s fakty, které znáte, a zvolte počet typů uzlů, které vaše aplikace potřebují začít. Typy uzlů můžete vždy přidat nebo odebrat později. Cluster Service Fabric musí mít alespoň jeden typ uzlu.

## <a name="the-properties-of-each-node-type"></a>Vlastnosti každého typu uzlu
**Typ uzlu** lze považovat za ekvivalentní rolím v cloudových službách. Typy uzlů definují velikosti virtuálních počítačů, počet virtuálních počítačů a jejich vlastnosti. Každý typ uzlu, který je definován v clusteru Service Fabric, se mapuje na [škálovací sadu virtuálních strojů](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview).  
Každý typ uzlu je odlišná škálovací sada a lze škálovat nahoru nebo dolů nezávisle, mají různé sady portů otevřené a mají různé metriky kapacity. Další informace o vztazích mezi typy uzlů a škálovacími sadami virtuálních počítačů, jak přejít k přístupu k přístupu k přístupu k počítači do jedné z instancí, jak otevřít nové porty a tak dále, naleznete v tématu [Typy uzlů clusteru Service Fabric](service-fabric-cluster-nodetypes.md).

Cluster Service Fabric se může skládat z více než jednoho typu uzlu. V takovém případě se cluster skládá z jednoho typu primárního uzlu a jednoho nebo více typů neprimárních uzlů.

Jeden typ uzlu nelze spolehlivě škálovat nad 100 uzlů na škálovací sadu virtuálních strojů pro aplikace SF; dosažení větší než 100 uzlů spolehlivě, bude vyžadovat přidání dalších škálovacích sad virtuálních strojů.

### <a name="primary-node-type"></a>Typ primárního uzlu

Systémové služby Service Fabric (například služba Cluster Manager nebo služba Úložiště bitových obrazů) jsou umístěny na primárním typu uzlu. 

![Snímek obrazovky clusteru se dvěma typy uzlů][SystemServices]

* **Minimální velikost virtuálních počítače** pro typ primárního uzlu je určena úrovní **odolnosti,** kterou zvolíte. Výchozí úroveň odolnosti je Bronz. Další podrobnosti najdete [v tématu Charakteristiky odolnosti clusteru.](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster)  
* **Minimální počet virtuálních her** pro typ primárního uzlu je určen úrovní **spolehlivosti,** kterou zvolíte. Výchozí úroveň spolehlivosti je Stříbrná. Další podrobnosti naleznete [v tématu Charakteristiky spolehlivosti clusteru.](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-reliability-characteristics-of-the-cluster)  

Ze šablony Azure Resource Manager je primární typ `isPrimary` uzlu nakonfigurován s atributem v [definici typu uzlu](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/clusters#nodetypedescription-object).

### <a name="non-primary-node-type"></a>Neprimární typ uzlu

V clusteru s více typy uzlů existuje jeden typ primárního uzlu a zbytek je neprimární.

* **Minimální velikost virtuálních počítačů** pro typy neprimárních uzlů je určena úrovní **odolnosti,** kterou zvolíte. Výchozí úroveň odolnosti je Bronz. Další informace naleznete [v tématu Charakteristiky odolnosti clusteru](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster).  
* **Minimální počet virtuálních počítačů** pro jiné typy uzlů je jeden. Toto číslo byste však měli zvolit na základě počtu replik aplikace nebo služeb, které chcete spustit v tomto typu uzlu. Počet virtuálních discích v typu uzlu lze zvýšit po nasazení clusteru.

## <a name="the-durability-characteristics-of-the-cluster"></a>Charakteristika odolnosti clusteru
Úroveň odolnosti se používá k označení systému oprávnění, která vaše virtuální počítače mají s základní infrastrukturou Azure. V primárním typu uzlu toto oprávnění umožňuje service fabric pozastavit všechny požadavky na infrastrukturu na úrovni virtuálního počítače (například restartování virtuálního počítače, reimage virtuálního počítače nebo migrace virtuálního počítače), které mají vliv na požadavky na kvorum pro systémové služby a stavové služby. V typech neprimárních uzlů toto oprávnění umožňuje service fabric pozastavit všechny požadavky infrastruktury na úrovni virtuálního počítače (například restartování virtuálního počítače, opětovné image virtuálního počítače a migrace virtuálních počítačů), které mají vliv na požadavky na kvora pro vaše stavové služby.

| Úroveň odolnosti  | Požadovaný minimální počet virtuálních dispozicí | Podporované virtuální virtuální virtuální ženy SKUs                                                                  | Aktualizace škálovací sady virtuálních strojů                               | Aktualizace a údržba iniciovaná Azure                                                              | 
| ---------------- |  ----------------------------  | ---------------------------------------------------------------------------------- | ----------------------------------------------------------- | ------------------------------------------------------------------------------------------------------- |
| Gold             | 5                              | SKU s plným uzlem určených pro jednoho zákazníka (například L32s, GS5, G5, DS15_v2, D15_v2) | Může být odloženo, dokud nebude schváleno clusterem Service Fabric | Lze pozastavuje po dobu 2 hodin na ud povolit další čas pro repliky obnovit z dřívějších selhání |
| Silver           | 5                              | Virtuální virtuální paměť s jedním jádrem nebo vyšším s alespoň 50 GB místního SSD                      | Může být odloženo, dokud nebude schváleno clusterem Service Fabric | Nelze se zpozdit o žádnou významnou dobu                                                    |
| Bronz           | 1                              | Virtuální virtuální paměť s alespoň 50 GB místního SSD                                              | Nebude zpožděncluster service fabric           | Nelze se zpozdit o žádnou významnou dobu                                                    |

> [!WARNING]
> Typy uzlů se bronzovou odolností _nezískávají žádná oprávnění_. To znamená, že úlohy infrastruktury, které mají vliv na vaše stavové úlohy, nebudou zastaveny nebo zpožděny, což může mít vliv na vaše úlohy. Používejte pouze bronzové typy uzlů, které běží pouze bezstavové úlohy. Pro produkční úlohy se doporučuje spuštění silver nebo vyšší. 
> 
> Bez ohledu na úroveň odolnosti operace [Deallocation](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/deallocate) na škálovací sadě virtuálních vod zničí cluster

**Výhody použití úrovně odolnosti stříbra nebo zlata**
 
- Snižuje počet požadovaných kroků v operaci škálování (to znamená, že deaktivace uzlu a Remove-ServiceFabricNodeState se nazývá automaticky).
- Snižuje riziko ztráty dat v důsledku operace změny virtuálního počítače iniciované zákazníkem nebo operací infrastruktury Azure.

**Nevýhody použití úrovně odolnosti stříbra nebo zlata**
 
- Nasazení do škálovací sady virtuálních strojů a dalšísouvisející prostředky Azure může být zpožděno, může časový režim nebo může být blokováno výhradně problémy ve vašem clusteru nebo na úrovni infrastruktury. 
- Zvyšuje počet [událostí životního cyklu repliky](service-fabric-reliable-services-lifecycle.md) (například primární swapy) z důvodu automatických deaktivací uzlů během operací infrastruktury Azure.
- Bere uzly mimo provoz po určitou dobu, zatímco azure platformy aktualizace softwaru nebo aktivity údržby hardwaru dochází. Během těchto aktivit se mohou zobrazit uzly se stavem Zakázání/Zakázání. To dočasně snižuje kapacitu clusteru, ale nemělo by to mít vliv na dostupnost clusteru nebo aplikací.

### <a name="recommendations-for-when-to-use-silver-or-gold-durability-levels"></a>Doporučení, kdy použít úrovně odolnosti stříbra nebo zlata

Používejte odolnost silver nebo gold pro všechny typy uzlů, které hostují stavové služby, které očekáváte, že často škálují (snížíte počet instancí virtuálních počítačů) a dáváte přednost tomu, aby byly operace nasazení zpožděny a kapacita byla snížena ve prospěch zjednodušení těchto škálování Operace. Scénáře horizontálního navýšení kapacity (přidání instancí virtuálních počítačů) nehrají do vašeho výběru úrovně odolnosti, pouze škálování.

### <a name="changing-durability-levels"></a>Změna úrovně trvanlivosti
- Typy uzlů s úrovní odolnosti stříbra nebo zlata nelze snížit na bronz.
- Upgrade z bronzu na stříbro nebo zlato může trvat několik hodin.
- Při změně úrovně odolnosti, nezapomeňte aktualizovat v konfiguraci rozšíření Service Fabric ve vašem prostředku škálovací sady virtuálního počítače a v definici typu uzlu v prostředku clusteru Service Fabric. Tyto hodnoty se musí shodovat.

### <a name="operational-recommendations-for-the-node-type-that-you-have-set-to-silver-or-gold-durability-level"></a>Provozní doporučení pro typ uzlu, který jste nastavili na úroveň stříbrné nebo zlaté trvanlivosti.

- Udržujte svůj cluster a aplikace v pořádku po celou dobu a ujistěte se, že aplikace reagují na všechny [události životního cyklu repliky služby](service-fabric-reliable-services-lifecycle.md) (jako je replika v sestavení je zablokovaný) včas.
- Přijmout bezpečnější způsoby, jak provést změnu skladové položky virtuálního počítače (vertikálně nahoru nebo dolů): Změna skladové položky virtuálního počítače škálovací sady virtuálního počítače vyžaduje řadu kroků a úvah. Zde je proces, který můžete sledovat, abyste se vyhnuli běžným problémům.
    - **Pro neprimární typy uzlů:** Doporučujeme vytvořit novou škálovací sadu virtuálních strojů, upravit omezení umístění služby tak, aby zahrnovalo nový typ škálovací sady/uzlu virtuálního počítače, a potom snížit počet instancí sady velikosti starého virtuálního počítače na nulu, jeden uzel najednou (to je zajistit, aby odebrání uzlů nemělo vliv na spolehlivost clusteru).
    - **Pro typ primárního uzlu:** Pokud je skladová položka virtuálního počítače, kterou jste vybrali, na kapacitě a chcete přejít na větší skladovou položku virtuálního počítače, postupujte podle našich pokynů pro [vertikální změnu měřítka pro typ primárního uzlu](https://docs.microsoft.com/azure/service-fabric/service-fabric-scale-up-node-type). 

- Udržujte minimální počet pěti uzlů pro libovolnou škálovací sadu virtuálních strojů, která má povolenou úroveň odolnosti zlaťáků nebo stříbra.
- Každá škálovací sada virtuálního počítače s úrovní odolnosti silver nebo gold musí být mapována na svůj vlastní typ uzlu v clusteru Service Fabric. Mapování více škálovacích sad virtuálních strojů na jeden typ uzlu zabrání správné fungování koordinace mezi clusterem Service Fabric a infrastrukturou Azure.
- Neodstraňujte náhodné instance virtuálních počítače, vždy používejte funkci škálování škálování virtuálních strojů. Odstranění náhodných instancí virtuálních počítačů má potenciál vytvářet nerovnováhy v instanci virtuálního počítači rozložené napříč UD a FD. Tato nerovnováha může nepříznivě ovlivnit schopnost systémů správně vyvažovat zatížení mezi instancemi služby/repliky služby.
- Pokud používáte automatické škálování, nastavte pravidla tak, aby škálování v (odebrání instancí virtuálních zařízení) se provádí pouze jeden uzel najednou. Škálování více než jednu instanci najednou není bezpečné.
- Pokud odstranění nebo zrušení přidělení virtuálních počítačů na primární typ uzlu, nikdy byste neměli snížit počet přidělených virtuálních počítačů pod co vyžaduje úroveň spolehlivosti. Tyto operace budou blokovány na dobu neurčitou v škálovací sadě s úrovní odolnosti stříbra nebo zlata.

## <a name="the-reliability-characteristics-of-the-cluster"></a>Charakteristiky spolehlivosti clusteru
Úroveň spolehlivosti se používá k nastavení počtu replik systémových služeb, které chcete spustit v tomto clusteru na typu primárního uzlu. Čím více počtu replik, tím spolehlivější jsou systémové služby v clusteru.  

Úroveň spolehlivosti může mít následující hodnoty:

* Platinum - Spuštění systémových služeb s cílovým počtem replik devíti
* Zlaťáky – spusťte systémové služby s počtem cílových replik sedmi
* Silver – spusťte systémové služby s počtem cílových replik sady pět 
* Bronz - Spusťte systémové služby s cílovým počtem replik tří

> [!NOTE]
> Úroveň spolehlivosti, kterou zvolíte, určuje minimální počet uzlů, které musí mít váš primární typ uzlu. 
> 
> 

### <a name="recommendations-for-the-reliability-tier"></a>Doporučení pro úroveň spolehlivosti

Když zvětšíte nebo zmenšíte velikost clusteru (součet instancí virtuálních počítačů ve všech typech uzlů), je nutné aktualizovat spolehlivost clusteru z jedné vrstvy na druhou. Tím se spustí upgrady clusteru potřebné ke změně počtu sad replik systémových služeb. Počkejte na dokončení upgradu před provedením dalších změn v clusteru, jako je přidání uzlů.  Průběh upgradu můžete sledovat v aplikaci Service Fabric Explorer nebo spuštěním [služby Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps)

Zde je doporučení pro výběr úrovně spolehlivosti.  Počet osivových uzlů je také nastaven na minimální počet uzlů pro úroveň spolehlivosti.  Například pro cluster se spolehlivostí Gold existuje 7 uzly osiva.

| **Počet uzlů clusteru** | **Úroveň spolehlivosti** |
| --- | --- |
| 1 |Nezadávejte parametr Spolehlivost, systém jej vypočítá |
| 3 |Bronz |
| 5 nebo 6|Silver |
| 7 nebo 8 |Gold |
| 9 a více |Platinum |

## <a name="primary-node-type---capacity-guidance"></a>Typ primárního uzlu – navádění kapacity

Zde je návod pro plánování kapacity typu primárního uzlu:

- **Počet instancí virtuálních počítačů pro spuštění všech produkčních úloh v Azure:** Je nutné zadat minimální velikost typu primárního uzlu 5 a úroveň spolehlivosti silver.  
- **Počet instancí virtuálních počítačů pro spuštění testovacích úloh v Azure** Můžete určit minimální velikost typu primárního uzlu 1 nebo 3. Cluster jednoho uzlu, běží se speciální konfigurací a proto není podporováno horizontální navýšení kapacity tohoto clusteru. Cluster jednoho uzlu, nemá žádnou spolehlivost a proto v šabloně Správce prostředků je nutné tuto konfiguraci odebrat/nespecifikovat (nenastavování hodnoty konfigurace nestačí). Pokud nastavíte cluster jednoho uzlu nastavený prostřednictvím portálu, bude o konfiguraci automaticky postaráno. Clustery jednoho a tří uzlů nejsou podporovány pro spouštění produkčních úloh. 
- **Skladová položka virtuálního měn:** Primární typ uzlu je místo, kde jsou spuštěny systémové služby, takže skladová položka virtuálního řezu, kterou pro něj zvolíte, musí brát v úvahu celkové zatížení ve špičce, které plánujete umístit do clusteru. Zde je analogie pro ilustraci toho, co mám na mysli zde - Myslete na primární typ uzlu jako vaše "plíce", to je to, co poskytuje kyslík do mozku, a tak v případě, že mozek nemá dostatek kyslíku, vaše tělo trpí. 

Vzhledem k tomu, že potřeby kapacity clusteru jsou určeny úlohou, kterou plánujete spustit v clusteru, nemůžeme vám poskytnout kvalitativní pokyny pro konkrétní pracovní vytížení, ale zde je obecné pokyny, které vám pomohou začít

Pro produkční úlohy: 

- Doporučujeme vyhradit primární uzel clusterů systémovým službám a použít omezení umístění k nasazení aplikace do sekundárních nodetypů.
- Doporučená skladová položka virtuálního řezu je standardní D2_V2 nebo ekvivalentní s minimálně 50 GB místního ssd.
- Minimální podporované použití sku virtuálních discích je Standard_D2_V3 nebo standardní D1_V2 nebo ekvivalentní s minimálně 50 GB místního ssd. 
- Naše doporučení je minimálně 50 GB. Pro vaše úlohy, zejména při spuštění kontejnerů systému Windows, jsou vyžadovány větší disky. 
- Částečné základní virtuální chudinské paměti, jako je standardní A0 nejsou podporovány pro produkční úlohy.
- Řady virtuálních mitu nejsou podporovány pro produkční úlohy z důvodů výkonu.
- Virtuální aplikace s nízkou prioritou nejsou podporované.

> [!WARNING]
> Změna velikosti virtuálního počítače virtuálního počítače primárního uzlu v běžícím clusteru, je operace škálování a dokumentována v dokumentaci [škálování škálování virtuálního počítače.](virtual-machine-scale-set-scale-node-type-scale-out.md)

## <a name="non-primary-node-type---capacity-guidance-for-stateful-workloads"></a>Typ uzlu, který není primární – pokyny k kapacitě pro stavové úlohy

Tento návod je pro stavové úlohy pomocí service fabric [spolehlivé kolekce nebo spolehlivé objekty actor,](service-fabric-choose-framework.md) které jsou spuštěny v typu uzlu bez primárního uzlu.

**Počet instancí virtuálních vod:** Pro produkční úlohy, které jsou stavové, je doporučeno spustit je s minimální a cílový počet replik 5. To znamená, že v ustáleném stavu skončíte s replikou (ze sady replik) v každé doméně selhání a doméně upgradu. Celý koncept úrovně spolehlivosti pro typ primárního uzlu je způsob, jak určit toto nastavení pro systémové služby. Takže stejná úvaha platí i pro vaše stavové služby stejně.

Takže pro produkční úlohy je minimální doporučená velikost typu neprimárního uzlu 5, pokud v něm spouštěte stavové úlohy.

**Skladová položka virtuálního měn:** Toto je typ uzlu, kde jsou spuštěny služby aplikace, takže skladová položka virtuálního počítače, kterou pro něj zvolíte, musí brát v úvahu zatížení ve špičce, které chcete umístit do každého uzlu. Potřeby kapacity typu uzlu jsou určeny úlohou, kterou plánujete spustit v clusteru, takže vám nemůžeme poskytnout kvalitativní pokyny pro konkrétní pracovní vytížení, ale zde je obecné pokyny, které vám pomohou začít

Pro produkční úlohy 

- Doporučená skladová položka virtuálního řezu je standardní D2_V2 nebo ekvivalentní s minimálně 50 GB místního ssd.
- Minimální podporované použití sku virtuálních discích je Standard_D2_V3 nebo standardní D1_V2 nebo ekvivalentní s minimálně 50 GB místního ssd. 
- Částečné základní virtuální chudinské paměti, jako je standardní A0 nejsou podporovány pro produkční úlohy.
- Řady virtuálních mitu nejsou podporovány pro produkční úlohy z důvodů výkonu.

## <a name="non-primary-node-type---capacity-guidance-for-stateless-workloads"></a>Typ uzlu, který není primární – pokyny k kapacitě pro bezstavové úlohy

Tento návod bezstavové úlohy, které jsou spuštěny na typ uzlu, který není primární.

**Počet instancí virtuálních vod:** Pro produkční úlohy, které jsou bezstavové, minimální podporovaná velikost typu neprimárního uzlu je 2. To vám umožní spustit dvě bezstavové instance vaší aplikace a umožňuje vaší službě přežít ztrátu instance virtuálního počítače. 

**Skladová položka virtuálního měn:** Toto je typ uzlu, kde jsou spuštěny služby aplikace, takže skladová položka virtuálního počítače, kterou pro něj zvolíte, musí brát v úvahu zatížení ve špičce, které chcete umístit do každého uzlu. Potřeby kapacity typu uzlu je určena zatížení, které chcete spustit v clusteru. Nemůžeme vám poskytnout kvalitativní pokyny pro vaši konkrétní pracovní zátěž.  Nicméně, zde je obecný návod, který vám pomůže začít.

Pro produkční úlohy 

- Doporučená skladová položka virtuálního řezu je standardní D2_V2 nebo ekvivalentní. 
- Minimální podporované použití sku virtuálního měna je standardní D1 nebo standardní D1_V2 nebo ekvivalentní. 
- Částečné základní virtuální chudinské paměti, jako je standardní A0 nejsou podporovány pro produkční úlohy.
- Řady virtuálních mitu nejsou podporovány pro produkční úlohy z důvodů výkonu.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="next-steps"></a>Další kroky
Po dokončení plánování kapacity a nastavení clusteru si přečtěte následující:

* [Zabezpečení clusteru Service Fabric](service-fabric-cluster-security.md)
* [Škálování clusteru Service Fabric](service-fabric-cluster-scaling.md)
* [Plánování zotavení po havárii](service-fabric-disaster-recovery.md)
* [Vztah nodetypů k škálovací sadě virtuálních počítačů](service-fabric-cluster-nodetypes.md)

<!--Image references-->
[SystemServices]: ./media/service-fabric-cluster-capacity/SystemServices.png
