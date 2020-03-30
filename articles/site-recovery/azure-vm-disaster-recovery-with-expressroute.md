---
title: Integrace zotavení po havárii virtuálního počítače Azure Azure azure s Azure Site Recovery
description: Popisuje, jak nastavit zotavení po havárii pro virtuální počítače Azure pomocí Azure Site Recovery a Azure ExpressRoute
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mayg
ms.openlocfilehash: bf12a5b7850a56d945e1082be6c522c31738669c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73954085"
---
# <a name="integrate-expressroute-with-disaster-recovery-for-azure-vms"></a>Integrace ExpressRoute s zotavením po havárii pro virtuální počítače Azure


Tento článek popisuje, jak integrovat Azure ExpressRoute s [Azure Site Recovery](site-recovery-overview.md), když nastavíte zotavení po havárii pro virtuální počítače Azure do sekundární oblasti Azure.

Site Recovery umožňuje zotavení po havárii virtuálních počítačích Azure replikací dat virtuálních zařízení Azure do Azure.

- Pokud virtuální počítače Azure používají [spravované disky Azure](../virtual-machines/windows/managed-disks-overview.md), data virtuálních počítačů se replikují na replikovaný spravovaný disk v sekundární oblasti.
- Pokud virtuální počítače Azure nepoužívají spravované disky, data virtuálních počítačů se replikují do účtu úložiště Azure.
- Koncové body replikace jsou veřejné, ale provoz replikace pro virtuální počítače Azure nepřekračuje internet.

ExpressRoute umožňuje rozšířit místní sítě do cloudu Microsoft Azure přes privátní připojení, které usnadňuje poskytovatel připojení. Pokud máte expressroute nakonfigurován, integruje se site recovery takto:

- **Během replikace mezi oblastmi Azure**: Provoz replikace pro zotavení po havárii virtuálního počítače Azure je jenom v Azure a ExpressRoute není potřeba nebo se používá pro replikaci. Pokud se ale připojujete z místní lokality k virtuálním virtuálním počítačům Azure v primární lokalitě Azure, existuje řada problémů, které je třeba znát při nastavování zotavení po havárii pro tyto virtuální počítače Azure.
- **Převzetí služeb při selhání mezi oblastmi Azure:** Když dojde k výpadkům, můžete přepojit virtuální počítače Azure z primární do sekundární oblasti Azure. Po převzetí služby při selhání do sekundární oblasti, existuje několik kroků, které je třeba provést pro přístup k virtuálním počítačům Azure v sekundární oblasti pomocí ExpressRoute.


## <a name="before-you-begin"></a>Než začnete

Než začnete, ujistěte se, že rozumíte následujícím pojmům:

- [Obvody](../expressroute/expressroute-circuit-peerings.md) ExpressRoute
- [Směrovací domény](../expressroute/expressroute-circuit-peerings.md#routingdomains) ExpressRoute
- ExpressRoute [umístění](../expressroute/expressroute-locations.md).
- [Architektura replikace](azure-to-azure-architecture.md) virtuálních počítačů Azure
- Jak [nastavit replikaci](azure-to-azure-tutorial-enable-replication.md) pro virtuální počítače Azure.
- Jak [převzetí služeb při selhání](azure-to-azure-tutorial-failover-failback.md) virtuálních počítačích Azure.


## <a name="general-recommendations"></a>Obecná doporučení

Chcete-li osvědčených postupů a zajistit efektivní cíle doby obnovení (RTO) pro zotavení po havárii, doporučujeme provést následující kroky při nastavování site recovery pro integraci s ExpressRoute:

- Zřizování síťových součástí před převzetím služeb při selhání do sekundární oblasti:
    - Když povolíte replikaci pro virtuální počítače Azure, site recovery můžete automaticky nasadit síťové prostředky, jako jsou sítě, podsítě a brány v cílové oblasti Azure, na základě nastavení zdrojové sítě.
    - Obnovení webu nemůže automaticky nastavit síťové prostředky, jako jsou brány virtuální sítě.
    - Doporučujeme zřídit tyto další síťové prostředky před převzetím služeb při selhání. K tomuto nasazení je přidružena malá prostoje, která může mít vliv na celkovou dobu obnovení, pokud jste ji nezohlednili během plánování nasazení.
- Spouštět pravidelné cvičení zotavení po havárii:
    - Postup ověří vaši strategii replikace bez ztráty dat, bez výpadku a bez dopadu na vaše produkční prostředí. Pomáhá vyhnout se problémům s konfigurací na poslední chvíli, které mohou nepříznivě ovlivnit RTO.
    - Při spuštění testu převzetí služeb při selhání pro vrtačku, doporučujeme použít samostatnou síť virtuálních počítačích Azure, namísto výchozí sítě, která je nastavena při povolení replikace.
- Pokud máte jeden okruh ExpressRoute, použijte různé prostory IP adres.
    - Doporučujeme použít jiný ip adresní prostor pro cílovou virtuální síť. Tím se zabrání problémům při navazování připojení během regionálních výpadků.
    - Pokud nemůžete použít samostatný adresní prostor, nezapomeňte spustit převzetí služeb při selhání testu pro zotavení po havárii v samostatné testovací síti s různými adresami IP. Dvě virtuální sítě s překrývajícím se adresním prostorem IP nelze připojit ke stejnému okruhu ExpressRoute.

## <a name="replicate-azure-vms-when-using-expressroute"></a>Replikace virtuálních počítačů Azure při použití ExpressRoute


Pokud chcete nastavit replikaci pro virtuální počítače Azure v primární lokalitě a připojujete se k těmto virtuálním počítačům z místní lokality přes ExpressRoute, musíte to udělat tady:

1. [Povolte replikaci](azure-to-azure-tutorial-enable-replication.md) pro každý virtuální počítač Azure.
2. Volitelně nechte site recovery nastavit síť:
    - Když nakonfigurujete a povolíte replikaci, obnovení lokality nastaví sítě, podsítě a podsítě brány v cílové oblasti Azure tak, aby odpovídaly sítím ve zdrojové oblasti. Site Recovery také mapuje mezi zdrojovými a cílovými virtuálními sítěmi.
    - Pokud nechcete, aby to služba Site Recovery dělala automaticky, vytvořte před povolením replikace síťové prostředky na cílové straně.
3. Vytvořte další síťové prvky:
    - Site Recovery nevytváří směrovací tabulky, brány virtuální sítě, připojení brány virtuální sítě, partnerský vztah virtuální sítě ani jiné síťové prostředky a připojení v sekundární oblasti.
    - Je třeba vytvořit tyto další síťové prvky v sekundární oblasti, kdykoli před spuštěním převzetí služeb při selhání z primární oblasti.
    - K nastavení a připojení těchto síťových prostředků můžete použít [plány obnovení](site-recovery-create-recovery-plans.md) a skripty automatizace.
1. Pokud máte síťové virtuální zařízení (NVA) nasazené k řízení toku síťového provozu, mějte na paměti, že:
    - Výchozí systémová trasa Azure pro replikaci virtuálních počítačů Azure je 0.0.0.0/0.
    - Nasazení nva obvykle také definovat výchozí trasu (0.0.0.0/0), která nutí odchozí internetový provoz tok prostřednictvím nva. Výchozí trasa se používá, pokud nelze najít žádnou jinou konkrétní konfiguraci trasy.
    - Pokud se jedná o tento případ, může být přetížena služba NVA, pokud veškerý provoz replikace prochází sítí nva.
    - Stejné omezení platí také při použití výchozích tras pro směrování veškerý provoz virtuálních zařízení Azure do místních nasazení.
    - V tomto scénáři doporučujeme [vytvořit koncový bod síťové služby](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) ve vaší virtuální síti pro službu Microsoft.Storage tak, aby provoz replikace neopustí hranice Azure.

## <a name="replication-example"></a>Příklad replikace

Obvykle mají podniková nasazení úlohy rozdělené mezi více virtuálních sítí Azure, s centrálním centrem připojení pro externí připojení k internetu a k místním webům. Topologie rozbočovače a paprsku se obvykle používá společně s ExpressRoute.

![Místní Azure s ExpressRoute před převzetím služeb při selhání](./media/azure-vm-disaster-recovery-with-expressroute/site-recovery-with-expressroute-before-failover.png)

- **Region**. Aplikace se nasazují v oblasti Azure – východní Asie.
- **Virtuální sítě spoke**. Aplikace se nasazují ve dvou virtuálních sítích s paprsky:
    - **Zdroj vNet1**: 10.1.0.0/24.
    - **Zdroj vNet2**: 10.2.0.0/24.
    - Každá virtuální síť s paprsky je připojená k **virtuální síti Hub**.
- **Síť hubů**. K dispozici je **virtuální síť hubu**virtuální sítě virtuální sítě: 10.10.10.0/24.
  - Tato virtuální síť centra funguje jako gatekeeper.
  - Všechna komunikace mezi podsítěmi prochází tímto centrem.
    - **Podsítě virtuální sítě rozbočovače**. Virtuální síť rozbočovače má dvě podsítě:
    - **Podsíť NVA**: 10.10.10.0/25. Tato podsíť obsahuje síťové virtuální zařízení (10.10.10.10).
    - **Podsíť brány**: 10.10.10.128/25. Tato podsíť obsahuje bránu ExpressRoute připojenou k připojení ExpressRoute, která vede do místní sítě prostřednictvím domény směrování soukromého partnerského vztahu.
- Místní datové centrum má připojení okruhu ExpressRoute přes okraj partnera v Hongkongu.
- Veškeré směrování je řízeno prostřednictvím směrovacích tabulek Azure (UDR).
- Veškerý odchozí provoz mezi virtuálními sítěmi nebo do místního datového centra je směrován přes síťové virtuální zařízení.

### <a name="hub-and-spoke-peering-settings"></a>Nastavení partnerského vztahu hubu a paprsku

#### <a name="spoke-to-hub"></a>Od paprsku k centru

**Směru** | **Nastavení** | **Stav**
--- | --- | ---
Od paprsku k centru | Povolit adresu virtuální sítě | Povoleno
Od paprsku k centru | Povolit předávaný provoz | Povoleno
Od paprsku k centru | Povolit tranzit brány | Zakázáno
Od paprsku k centru | Použití odebrat brány | Povoleno

 ![Konfigurace partnerského vztahu paprsku do centra](./media/azure-vm-disaster-recovery-with-expressroute/spoke-to-hub-peering-configuration.png)

#### <a name="hub-to-spoke"></a>Od centra k paprsku

**Směru** | **Nastavení** | **Stav**
--- | --- | ---
Od centra k paprsku | Povolit adresu virtuální sítě | Povoleno
Od centra k paprsku | Povolit předávaný provoz | Povoleno
Od centra k paprsku | Povolit tranzit brány | Povoleno
Od centra k paprsku | Použití odebrat brány | Zakázáno

 ![Konfigurace centra pro partnerský vztah paprsku](./media/azure-vm-disaster-recovery-with-expressroute/hub-to-spoke-peering-configuration.png)

### <a name="example-steps"></a>Příklad kroků

V našem příkladu by mělo dojít k následujícímu při povolení replikace pro virtuální počítače Azure ve zdrojové síti:

1. [Povolíte replikaci](azure-to-azure-tutorial-enable-replication.md) pro virtuální ho.
2. Site Recovery vytvoří virtuální sítě replik, podsítě a podsítě brány v cílové oblasti.
3. Site Recovery vytvoří mapování mezi zdrojovými sítěmi a cílovými sítěmi replik, které vytvoří.
4. Ručně vytvořit brány virtuální sítě, připojení brány virtuální sítě brány, partnerský vztah virtuální sítě nebo jiné síťové prostředky nebo připojení.


## <a name="fail-over-azure-vms-when-using-expressroute"></a>Převzetí služeb při selhání virtuálních počítačích Azure při použití ExpressRoute

Po selhání virtuálních počítačích Azure se k cílové oblasti Azure pomocí site recovery, můžete k nim přistupovat pomocí [ExpressRoute privátní partnerského vztahu](../expressroute/expressroute-circuit-peerings.md#privatepeering).

- ExpressRoute je potřeba připojit k cílové virtuální síti s novým připojením. Existující připojení ExpressRoute se automaticky nepřenese.
- Způsob, jakým nastavíte připojení ExpressRoute k cílové virtuální síti, závisí na topologii ExpressRoute.


### <a name="access-with-two-circuits"></a>Přístup se dvěma obvody

#### <a name="two-circuits-with-two-peering-locations"></a>Dva okruhy se dvěma partnerskými místy

Tato konfigurace pomáhá chránit obvody ExpressRoute před regionální katastrofou. Pokud vaše primární umístění partnerského vztahu přejde dolů, připojení můžete pokračovat z jiného umístění.

- Okruh připojený k produkčnímu prostředí je obvykle primární. Sekundární okruh má obvykle nižší šířku pásma, která může být zvýšena, pokud dojde k havárii.
- Po převzetí služeb při selhání můžete navázat připojení ze sekundárního okruhu ExpressRoute k cílové virtuální síti. Případně můžete mít připojení nastavena a připravena v případě havárie, snížit celkovou dobu obnovení.
- Při současných připojeních k primárním i cílovým virtuálním sítím se ujistěte, že místní směrování používá pouze sekundární okruh a připojení po převzetí služeb při selhání.
- Zdrojové a cílové virtuální sítě můžou po převzetí služeb při selhání přijímat nové IP adresy nebo zachovat stejné. V obou případech lze sekundární připojení navázat před převzetím služeb při selhání.


#### <a name="two-circuits-with-single-peering-location"></a>Dva obvody s jedním umístěním partnerského vztahu

Tato konfigurace pomáhá chránit před selháním primárního okruhu ExpressRoute, ale ne v případě, že dojde k výpadku jednoho umístění partnerského vztahu ExpressRoute, což má vliv na oba okruhy.

- Můžete mít souběžná připojení z místního datového centra ke zdroji vNEt s primárním okruhem a k cílové virtuální síti se sekundárním okruhem.
- Při současných připojeních k primárnímu a cílovému se ujistěte, že místní směrování používá pouze sekundární okruh a připojení po převzetí služeb při selhání.
-   Oba okruhy nelze připojit ke stejné virtuální síti, když jsou okruhy vytvořeny ve stejném umístění partnerského vztahu.

### <a name="access-with-a-single-circuit"></a>Přístup s jedním obvodem

V této konfiguraci je pouze jeden okruh Expressroute. Přestože okruh má redundantní připojení v případě, že jeden přejde dolů, jeden okruh trasy nebude poskytovat odolnost, pokud vaše peering oblast přejde dolů. Poznámky:

- Virtuální počítače Azure můžete replikovat do libovolné oblasti Azure ve [stejném geografickém umístění](azure-to-azure-support-matrix.md#region-support). Pokud cílová oblast Azure není ve stejném umístění jako zdroj, musíte povolit ExpressRoute Premium, pokud používáte jeden okruh ExpressRoute. Informace o [umístěních ExpressRoute](../expressroute/expressroute-locations.md) a [cenách ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute/).
- Zdrojové a cílové virtuální sítě nelze připojit současně k okruhu, pokud se v cílové oblasti používá stejný adresní prostor IP. V tomto scénáři:    
    -  Odpojte připojení na straně zdroje a potom vytvořte připojení na straně cíle. Tuto změnu připojení lze skriptovat jako součást plánu obnovení webu. Poznámky:
        - V případě, že primární oblast není přístupná, může dojít k selhání operace odpojení v oblasti. To může mít vliv na vytvoření připojení k cílové oblasti.
        - Pokud jste vytvořili připojení v cílové oblasti a primární oblast se obnoví později, může dojít k poklesu paketů, pokud se dvě souběžná připojení pokusí připojit ke stejnému adresnímu prostoru.
        - Chcete-li tomu zabránit, okamžitě ukončete primární připojení.
        - Po navrácení služeb po obnovení virtuálního počítači do primární oblasti lze znovu navázat primární připojení po odpojení sekundárního připojení.
-   Pokud se na cílové virtuální síti používá jiný adresní prostor, můžete se současně připojit ke zdrojovým a cílovým virtuálním sítím ze stejného okruhu ExpressRoute.


## <a name="failover-example"></a>Příklad převzetí služeb při selhání

V našem příkladu používáme následující topologii:

- Dva různé okruhy ExpressRoute ve dvou různých umístěních partnerského vztahu.
- Po převzetí služeb při selhání si uchovávejte privátní IP adresy pro virtuální počítače Azure.
- Cílová oblast obnovení je Azure Jihovýchodní Asie.
- Sekundární připojení okruhu ExpressRoute je vytvořeno prostřednictvím partnerské hrany v Singapuru.

Pro jednoduchou topologii, která používá jeden okruh ExpressRoute se stejnou IP adresou po převzetí služeb při selhání, [přečtěte si tento článek](site-recovery-retain-ip-azure-vm-failover.md#hybrid-resources-full-failover).

### <a name="example-steps"></a>Příklad kroků
Chcete-li automatizovat obnovení v tomto příkladu, musíte udělat toto:

1. Podle pokynů nastavte replikaci.
2. [Převzetí služeb při selhání virtuálních počítačích Azure](azure-to-azure-tutorial-failover-failback.md), s těmito dalšími kroky během nebo po převzetí služeb při selhání.

    a. Vytvořte bránu Azure ExpressRoute ve virtuální síti centra cílové oblasti. To je potřeba připojit virtuální síť cílového rozbočovače k okruhu ExpressRoute.

    b. Vytvořte připojení z virtuální sítě cílového rozbočovače k cílovému okruhu ExpressRoute.

    c. Nastavte partnerské partnerské servery virtuální sítě mezi centrem cílové oblasti a virtuálními sítěmi pro paprsky. Vlastnosti partnerského vztahu v cílové oblasti budou stejné jako ve zdrojové oblasti.

    d. Nastavte UDR ve virtuální síti rozbočovače a dvoupaprskové virtuální sítě.

    - Vlastnosti udr na cílové straně jsou stejné jako vlastnosti na straně zdroje při použití stejných adres IP.
    - U různých cílových IP adres by měly být odpovídajícím způsobem upraveny UDR.


Výše uvedené kroky lze skriptovat jako součást [plánu obnovení](site-recovery-create-recovery-plans.md). V závislosti na připojení aplikace a požadavky na dobu obnovení výše uvedené kroky lze také provést před spuštěním převzetí služeb při selhání.

#### <a name="after-recovery"></a>Po zotavení

Po obnovení virtuálních zařízení a dokončení připojení, prostředí pro obnovení je následující.

![Místní Azure s ExpressRoute po převzetí služeb při selhání](./media/azure-vm-disaster-recovery-with-expressroute/site-recovery-with-expressroute-after-failover.png)



## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o používání [plánů obnovení](site-recovery-create-recovery-plans.md) k automatizaci převzetí služeb při selhání aplikace.
