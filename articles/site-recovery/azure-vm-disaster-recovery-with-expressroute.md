---
title: Integrace zotavení po havárii virtuálního počítače Azure s ExpressRoute pomocí Azure Site Recovery
description: Popisuje, jak nastavit zotavení po havárii pro virtuální počítače Azure pomocí Azure Site Recovery a Azure ExpressRoute.
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mayg
ms.openlocfilehash: 0e1f670f2ba5ad31f29d56b2de40acd6e2bf18a9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88654374"
---
# <a name="integrate-expressroute-with-disaster-recovery-for-azure-vms"></a>Integrace ExpressRoute s zotavením po havárii pro virtuální počítače Azure


Tento článek popisuje, jak integrovat Azure ExpressRoute s [Azure Site Recovery](site-recovery-overview.md)při nastavování zotavení po havárii pro virtuální počítače Azure do sekundární oblasti Azure.

Site Recovery umožňuje zotavení po havárii virtuálních počítačů Azure tím, že replikuje data virtuálních počítačů Azure do Azure.

- Pokud virtuální počítače Azure používají [spravované disky Azure](../virtual-machines/managed-disks-overview.md), replikují se data virtuálních počítačů do replikovaného spravovaného disku v sekundární oblasti.
- Pokud virtuální počítače Azure nepoužívají spravované disky, replikují se data virtuálních počítačů do účtu služby Azure Storage.
- Koncové body replikace jsou veřejné, ale provoz replikace pro virtuální počítače Azure nepřekračuje Internet.

ExpressRoute umožňuje rozšiřování místních sítí do cloudu Microsoft Azure přes privátní připojení, které usnadňuje poskytovatel připojení. Pokud máte nakonfigurovanou ExpressRoute, integruje se Site Recovery následujícím způsobem:

- **Během replikace mezi oblastmi Azure**: provoz replikace pro zotavení po havárii virtuálního počítače Azure je jenom v Azure a ExpressRoute není potřeba ani se nepoužívá pro replikaci. Pokud se ale připojujete z místní lokality k virtuálním počítačům Azure v primární lokalitě Azure, při nastavování zotavení po havárii pro tyto virtuální počítače Azure máte vědět několik problémů.
- **Převzetí služeb při selhání mezi oblastmi Azure**: Pokud dojde k výpadku, dojde při selhání virtuálních počítačů Azure z primární do sekundární oblasti Azure. Po převzetí služeb při selhání do sekundární oblasti existuje několik kroků, které je potřeba provést, abyste měli přístup k virtuálním počítačům Azure v sekundární oblasti pomocí ExpressRoute.


## <a name="before-you-begin"></a>Než začnete

Než začnete, ujistěte se, že rozumíte následujícím koncepcím:

- [Okruhy](../expressroute/expressroute-circuit-peerings.md) ExpressRoute
- [Domény směrování](../expressroute/expressroute-circuit-peerings.md#routingdomains) ExpressRoute
- ExpressRoute [umístění](../expressroute/expressroute-locations.md).
- [Architektura replikace](azure-to-azure-architecture.md) virtuálních počítačů Azure
- Jak [nastavit replikaci](azure-to-azure-tutorial-enable-replication.md) pro virtuální počítače Azure.
- Jak [převzít služby při selhání](azure-to-azure-tutorial-failover-failback.md) virtuálních počítačů Azure.


## <a name="general-recommendations"></a>Obecná doporučení

Osvědčeným postupem a zajištění efektivního plánovaného času obnovení (RTO) při zotavení po havárii doporučujeme při nastavení Site Recovery pro integraci s ExpressRoute provést následující akce:

- Zřizování síťových součástí před převzetím služeb při selhání do sekundární oblasti:
    - Když povolíte replikaci pro virtuální počítače Azure, Site Recovery může automaticky nasadit síťové prostředky, jako jsou sítě, podsítě a brány, v cílové oblasti Azure na základě nastavení zdrojové sítě.
    - Site Recovery nemůžou automaticky nastavit síťové prostředky, třeba brány virtuální sítě.
    - Před převzetím služeb při selhání doporučujeme zřídit tyto další síťové prostředky. K tomuto nasazení je přidruženo malé výpadky a může mít vliv na celkovou dobu obnovení, pokud jste to nepoužili během plánování nasazení.
- Spustit pravidelné cvičení zotavení po havárii:
    - Postup ověří vaši strategii replikace bez ztráty dat, bez výpadku a bez dopadu na vaše produkční prostředí. Pomáhá předejít problémům s konfigurací poslední minuty, které by mohly mít nepříznivý dopad na RTO.
    - Při spuštění testovacího převzetí služeb při selhání pro procházení doporučujeme použít samostatnou síť virtuálních počítačů Azure namísto výchozí sítě, která je nastavená při povolení replikace.
- Pokud máte jeden okruh ExpressRoute, použijte jiné adresní prostory IP adres.
    - Doporučujeme pro cílovou virtuální síť použít jiný adresní prostor IP adres. Tím se vyhnete problémům při navazování připojení během regionálních výpadků.
    - Pokud nemůžete použít oddělený adresní prostor, nezapomeňte spustit testovací převzetí služeb při selhání pro zotavení po havárii v samostatné testovací síti s různými IP adresami. Nemůžete propojit dva virtuální sítě s překrývajícím se adresním prostorem IP adres na stejný okruh ExpressRoute.

## <a name="replicate-azure-vms-when-using-expressroute"></a>Replikace virtuálních počítačů Azure při použití ExpressRoute


Pokud chcete nastavit replikaci pro virtuální počítače Azure v primární lokalitě a připojujete se k těmto virtuálním počítačům z místního serveru přes ExpressRoute, je potřeba udělat tyto kroky:

1. [Povolte replikaci](azure-to-azure-tutorial-enable-replication.md) pro každý virtuální počítač Azure.
2. Volitelně Site Recovery nastavit síť:
    - Když konfigurujete a povolíte replikaci, Site Recovery nastaví sítě, podsítě a podsítě brány v cílové oblasti Azure tak, aby odpovídaly hodnotám ve zdrojové oblasti. Site Recovery se také mapují mezi zdrojovou a cílovou virtuální sítí.
    - Pokud nechcete, Site Recovery to udělat automaticky, vytvořte před povolením replikace síťové prostředky na straně cíle.
3. Vytvořit další prvky sítě:
    - Site Recovery nevytváří směrovací tabulky, brány virtuální sítě, připojení bran virtuální sítě ani jiné síťové prostředky a připojení v sekundární oblasti.
    - Tyto další prvky sítě je třeba vytvořit v sekundární oblasti, kdykoli před spuštěním převzetí služeb při selhání z primární oblasti.
    - K nastavení a připojení těchto síťových prostředků můžete použít [plány obnovení](site-recovery-create-recovery-plans.md) a skripty pro automatizaci.
1. Pokud máte nasazené síťové virtuální zařízení (síťové virtuální zařízení) pro řízení toku síťového provozu, pamatujte na to, že:
    - Výchozí systémová trasa Azure pro replikaci virtuálních počítačů Azure je 0.0.0.0/0.
    - Nasazení síťové virtuální zařízení obvykle definují výchozí trasu (0.0.0.0/0), která vynucuje tok odchozího internetového provozu prostřednictvím síťové virtuální zařízení. Výchozí trasa se používá v případě, že není možné najít žádnou jinou konkrétní konfiguraci směrování.
    - V takovém případě může být síťové virtuální zařízení přetížený, pokud veškerý provoz replikace projde přes síťové virtuální zařízení.
    - Stejné omezení platí i v případě, že používáte výchozí trasy pro směrování všech přenosů virtuálních počítačů Azure do místních nasazení.
    - V tomto scénáři doporučujeme [vytvořit koncový bod síťové služby](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) ve vaší virtuální síti pro službu Microsoft. Storage, aby provoz replikace nezůstal na hranici Azure.

## <a name="replication-example"></a>Příklad replikace

Podniková nasazení obvykle mají rozdělení úloh v několika virtuální sítě Azure s centrálním centrem pro připojení pro externí připojení k Internetu a místním webům. Topologie rozbočovače a paprsků se obvykle používá společně s ExpressRoute.

![Místní – Azure s ExpressRoute před převzetím služeb při selhání](./media/azure-vm-disaster-recovery-with-expressroute/site-recovery-with-expressroute-before-failover.png)

- **Oblast**. Aplikace se nasazují v oblasti Azure Východní Asie.
- **Paprskový virtuální sítě**. Aplikace se nasazují ve dvou paprskových virtuální sítě:
    - **Zdroj vNet1**: 10.1.0.0/24.
    - **Zdroj vNet2**: 10.2.0.0/24.
    - Každá virtuální síť paprsků je připojená k **virtuální síti centra**.
- **Virtuální síť centra** Existuje **virtuální síť rozbočovače zdroje**virtuální sítě: 10.10.10.0/24.
  - Tato virtuální síť centra funguje jako server gatekeeper.
  - Veškerá komunikace napříč podsítěmi prochází přes toto centrum.
    - **Podsítě virtuální sítě rozbočovače**. Virtuální síť centra má dvě podsítě:
    - **Síťové virtuální zařízení podsíť**: 10.10.10.0/25. Tato podsíť obsahuje síťové virtuální zařízení (10.10.10.10).
    - **Podsíť brány**: 10.10.10.128/25. Tato podsíť obsahuje ExpressRoute bránu připojenou k ExpressRoute připojení, které směruje na místní lokalitu prostřednictvím domény směrování privátního partnerského vztahu.
- Místní datacentrum má připojení okruhu ExpressRoute prostřednictvím hraničního partnera v Hongkongu.
- Všechna směrování se řídí prostřednictvím tabulek Azure Route (UDR).
- Veškerý odchozí provoz mezi virtuální sítě nebo místním datacentrem se směruje přes síťové virtuální zařízení.

### <a name="hub-and-spoke-peering-settings"></a>Nastavení partnerského vztahu hub a paprsků

#### <a name="spoke-to-hub"></a>Od paprsku k centru

**Směr** | **Nastavení** | **State**
--- | --- | ---
Od paprsku k centru | Povolení adresy virtuální sítě | Povoleno
Od paprsku k centru | Povolení přesměrovaného provozu | Povoleno
Od paprsku k centru | Povolení přenosu brány | Zakázáno
Od paprsku k centru | Použít odebrání bran | Povoleno

 ![Konfigurace partnerského vztahu paprsků k rozbočovači](./media/azure-vm-disaster-recovery-with-expressroute/spoke-to-hub-peering-configuration.png)

#### <a name="hub-to-spoke"></a>Od centra k paprsku

**Směr** | **Nastavení** | **State**
--- | --- | ---
Od centra k paprsku | Povolení adresy virtuální sítě | Povoleno
Od centra k paprsku | Povolení přesměrovaného provozu | Povoleno
Od centra k paprsku | Povolení přenosu brány | Povoleno
Od centra k paprsku | Použít odebrání bran | Zakázáno

 ![Konfigurace partnerského vztahu mezi rozbočovači a paprsky](./media/azure-vm-disaster-recovery-with-expressroute/hub-to-spoke-peering-configuration.png)

### <a name="example-steps"></a>Příklady kroků

V našem příkladu by se při povolování replikace virtuálních počítačů Azure ve zdrojové síti mělo provést následující:

1. [Povolíte replikaci](azure-to-azure-tutorial-enable-replication.md) pro virtuální počítač.
2. V cílové oblasti vytvoří Site Recovery virtuální sítě repliky, podsítě a podsítě brány.
3. Site Recovery vytvoří mapování mezi zdrojovými sítěmi a cílovými sítěmi repliky, které vytvoří.
4. Ručně vytvoříte brány virtuální sítě, připojení brány virtuální sítě, partnerský vztah virtuálních sítí nebo jakékoli jiné síťové prostředky nebo připojení.


## <a name="fail-over-azure-vms-when-using-expressroute"></a>Převzetí služeb při selhání virtuálních počítačů Azure při použití ExpressRoute

Po selhání virtuálních počítačů Azure přes cílovou oblast Azure pomocí Site Recovery můžete k nim přistupovat pomocí [privátního partnerského vztahu](../expressroute/expressroute-circuit-peerings.md#privatepeering)ExpressRoute.

- Musíte připojit ExpressRoute k cílové virtuální síti s novým připojením. Existující připojení ExpressRoute se automaticky nepřenáší.
- Způsob, jakým nastavíte připojení ExpressRoute k cílové virtuální síti, závisí na vaší topologii ExpressRoute.


### <a name="access-with-two-circuits"></a>Přístup se dvěma okruhy

#### <a name="two-circuits-with-two-peering-locations"></a>Dva okruhy se dvěma umístěními partnerských vztahů

Tato konfigurace pomáhá chránit okruhy ExpressRoute proti regionální havárii. Pokud se primární umístění partnerského vztahu neukončí, můžou připojení pokračovat z druhého umístění.

- Okruh připojený k produkčnímu prostředí je obvykle primární. Sekundární okruh má typicky menší šířku pásma, což se dá zvýšit, pokud dojde k havárii.
- Po převzetí služeb při selhání můžete navázat připojení ze sekundárního okruhu ExpressRoute k cílové virtuální síti. Případně můžete připojení nastavit a připravit v případě havárie, abyste snížili celkovou dobu obnovení.
- Při současných připojeních k primárním i cílovým virtuální sítě se ujistěte, že místní směrování používá jenom sekundární okruh a připojení po převzetí služeb při selhání.
- Zdrojový a cílový virtuální sítě můžou dostávat nové IP adresy nebo po převzetí služeb při selhání zachovat stejné. V obou případech je možné navázat sekundární připojení před převzetím služeb při selhání.


#### <a name="two-circuits-with-single-peering-location"></a>Dva okruhy s jedním umístěním partnerského vztahu

Tato konfigurace pomáhá chránit před selháním primárního okruhu ExpressRoute, ale ne v případě, že dojde k výpadku jednoho umístění partnerského vztahu ExpressRoute, což by ovlivnilo oba okruhy.

- Můžete mít současná připojení z místního datacentra ke zdrojové virtuální síti s primárním okruhem a k cílové virtuální síti se sekundárním okruhem.
- U současných připojení k primárnímu a cílovému cíli se ujistěte, že místní směrování používá pouze sekundární okruh a připojení po převzetí služeb při selhání.
-   Oba okruhy nemůžete připojit ke stejné virtuální síti, pokud jsou okruhy vytvářeny ve stejném umístění partnerského vztahu.

### <a name="access-with-a-single-circuit"></a>Přístup pomocí jednoho okruhu

V této konfiguraci je k dispozici pouze jeden okruh ExpressRoute. I když má okruh redundantní připojení v případě, že dojde k výpadku, jeden okruh trasy nebude poskytovat odolnost, pokud vaše oblast partnerského vztahu vychází z oblasti. Poznámky:

- Virtuální počítače Azure můžete replikovat do jakékoli oblasti Azure ve [stejném geografickém umístění](azure-to-azure-support-matrix.md#region-support). Pokud cílová oblast Azure není ve stejném umístění jako zdroj, musíte povolit ExpressRoute Premium, pokud používáte jeden okruh ExpressRoute. Přečtěte si o [ExpressRoute umístěních](../expressroute/expressroute-locations.md) a [cenách ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute/).
- Zdrojový a cílový virtuální sítě nejde současně připojit k okruhu, pokud je v cílové oblasti použit stejný adresní prostor IP adres. V tomto scénáři:    
    -  Odpojte zdrojové připojení a pak vytvořte cílové připojení na straně. Tato změna připojení se dá skriptovat jako součást plánu obnovení Site Recovery. Poznámky:
        - Pokud je primární region nepřístupná, může při selhání dojít k selhání operace odpojení. To může mít vliv na vytvoření připojení do cílové oblasti.
        - Pokud jste připojení vytvořili v cílové oblasti a primární oblast se obnoví později, může docházet k poklesu paketů v případě, že se dvě současná připojení pokusí připojit ke stejnému adresnímu prostoru.
        - Chcete-li tomu zabránit, ukončete okamžitě primární připojení.
        - Po navrácení služeb po obnovení virtuálního počítače do primární oblasti se po odpojení sekundárního připojení může znovu vytvořit primární připojení.
-   Pokud se v cílové virtuální síti používá jiný adresní prostor, můžete se připojit ke zdrojovému a cílovému virtuální sítě ze stejného okruhu ExpressRoute.


## <a name="failover-example"></a>Příklad převzetí služeb při selhání

V našem příkladu používáme následující topologii:

- Dva různé okruhy ExpressRoute ve dvou různých umístěních partnerských vztahů.
- Po převzetí služeb při selhání uchování privátních IP adres pro virtuální počítače Azure.
- Cílová oblast obnovení je Azure jihovýchodní Asie.
- Sekundární připojení okruhu ExpressRoute je vytvořeno prostřednictvím hraničního partnera v Singapuru.

V případě jednoduché topologie, která používá jeden okruh ExpressRoute se stejnou IP adresou po převzetí služeb při selhání, [si přečtěte tento článek](site-recovery-retain-ip-azure-vm-failover.md#hybrid-resources-full-failover).

### <a name="example-steps"></a>Příklady kroků
Chcete-li v tomto příkladu automatizovat obnovení, je třeba provést následující kroky:

1. Při nastavování replikace postupujte podle pokynů.
2. Převzetí služeb při selhání [virtuálními počítači Azure](azure-to-azure-tutorial-failover-failback.md)s těmito dalšími kroky během nebo po převzetí služeb při selhání.

    a. Vytvořte bránu Azure ExpressRoute ve virtuální síti centra cílové oblasti. To je potřeba k připojení cílové virtuální sítě rozbočovače k okruhu ExpressRoute.

    b. Vytvořte připojení z cílové virtuální sítě rozbočovače k cílovému okruhu ExpressRoute.

    c. Nastavte partnerské vztahy virtuální sítě mezi virtuálními sítěmi oblasti a paprsky cílové oblasti. Vlastnosti partnerského vztahu v cílové oblasti budou stejné jako u zdrojové oblasti.

    d. Nastavte udr ve virtuální síti centra a dvě paprskové virtuální sítě.

    - Vlastnosti udr cílové strany jsou stejné jako u na straně zdroje při použití stejných IP adres.
    - V případě odlišných cílových IP adres by se udr měl odpovídajícím způsobem upravit.


Výše uvedené kroky lze vytvořit pomocí skriptu v rámci [plánu obnovení](site-recovery-create-recovery-plans.md). V závislosti na požadavcích na připojení a dobu obnovení aplikace můžete před spuštěním převzetí služeb při selhání dokončit i výše uvedené kroky.

#### <a name="after-recovery"></a>Po obnovení

Po obnovení virtuálních počítačů a dokončení připojení je prostředí obnovení následující.

![Místní – Azure s ExpressRoute po převzetí služeb při selhání](./media/azure-vm-disaster-recovery-with-expressroute/site-recovery-with-expressroute-after-failover.png)



## <a name="next-steps"></a>Další kroky

Další informace o použití [plánů obnovení](site-recovery-create-recovery-plans.md) k automatizaci převzetí služeb při selhání aplikace.
