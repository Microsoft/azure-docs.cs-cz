---
title: Pomocí ExpressRoute s zotavení po havárii virtuálních počítačů Azure | Dokumentace Microsoftu
description: Popisuje, jak pomocí Azure ExpressRoute pomocí Azure Site Recovery pro zotavení po havárii virtuálních počítačů Azure
services: site-recovery
documentationcenter: ''
author: mayanknayar
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: manayar
ms.openlocfilehash: 73514b524f554affb9730ba63ccd608491497af2
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2018
ms.locfileid: "37920466"
---
# <a name="using-expressroute-with-azure-virtual-machine-disaster-recovery"></a>Zotavení po havárii virtuálních počítačů Azure pomocí ExpressRoute

Microsoft Azure ExpressRoute umožňuje rozšířit vaše místní sítě do cloudu Microsoftu přes soukromé připojení zajišťované poskytovatelem připojení. Tento článek popisuje, jak můžete použít ExpressRoute pomocí Site Recovery pro zotavení po havárii virtuálních počítačů Azure.

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že rozumíte:
-   ExpressRoute [okruhy](../expressroute/expressroute-circuit-peerings.md)
-   ExpressRoute [domény směrování](../expressroute/expressroute-circuit-peerings.md#expressroute-routing-domains)
-   Virtuální počítač Azure [architektura replikace](azure-to-azure-architecture.md)
-   [Nastavení replikace](azure-to-azure-tutorial-enable-replication.md) pro virtuální počítače Azure
-   [Převzetí služeb při selhání](azure-to-azure-tutorial-failover-failback.md) virtuálních počítačů Azure

## <a name="expressroute-and-azure-virtual-machine-replication"></a>Replikace virtuálních počítačů Azure a ExpressRoute

Při ochraně virtuálních počítačů Azure pomocí Site Recovery, replikace se odešlou na účet služby Azure Storage nebo repliky se spravovanými disky v cílové oblasti Azure v závislosti na tom, jestli vaše virtuální počítače Azure pomocí [Azure Managed Disks](../virtual-machines/windows/managed-disks-overview.md). I když jsou veřejné koncové body replikace, provoz replikace pro replikaci virtuálních počítačů Azure, ve výchozím nastavení, neprocházejí přes Internet, bez ohledu na to, které oblasti Azure zdrojová virtuální síť existuje v.

Pro zotavení po havárii virtuálního počítače Azure jak data replikace, nenechává hranici Azure ExpressRoute se nevyžaduje pro replikaci. Po virtuálním počítačům převzetí služeb při selhání do cílové oblasti Azure, k nim můžete přistupovat pomocí [soukromého partnerského vztahu](../expressroute/expressroute-circuit-peerings.md#azure-private-peering).

## <a name="replicating-azure-deployments"></a>Replikace nasazení Azure

Předchozí [článku](site-recovery-retain-ip-azure-vm-failover.md#on-premises-to-azure-connectivity), popsané jednoduché uspořádání s jednou virtuální sítí Azure připojení k místním datacentrem zákazníka prostřednictvím ExpressRoute. Typické podnikové nasazení mají úlohy rozdělit mezi více virtuálních sítí Azure a připojení k centrální rozbočovač vytvoří externí připojení k Internetu a pro místní nasazení.

Tento příklad popisuje hvězdicovou topologii, což je běžné v případě podnikového nasazení:
-   Probíhá nasazení **Azure východní Asie** oblasti a místním datovým centrem má připojení okruhu ExpressRoute prostřednictvím partnerů hrany v Hongkong –.
-   Nasadíte aplikace mezi dvěma virtuálními sítěmi paprsků – **zdroje ze sítě VNet1** s 10.1.0.0/24 adresa místa a **zdroje ze sítě VNet2** s 10.2.0.0/24 prostor adres.
-   Virtuální síť centra **zdrojová virtuální síť centra**, s adresním prostorem 10.10.10.0/24 funguje jako vrátný. Veškerá komunikace mezi podsítěmi prochází rozbočovače.
-   Centrální virtuální síti se dvěma podsítěmi – **podsíť síťových virtuálních zařízení** s 10.10.10.0/25 adresa místa a **podsíť brány** s 10.10.10.128/25 prostor adres.
-   **Podsíť síťových virtuálních zařízení** má síťové virtuální zařízení s IP adresou. 10.10.10.10.
-   **Podsíť brány** bránu ExpressRoute se připojil do připojení ExpressRoute, který přesměruje do místního datacentra zákazníka prostřednictvím soukromého partnerského vztahu domény směrování.
-   Virtuální síť každého paprsku je připojené k virtuální síti centra a všechny směrování v rámci této topologie sítě je řízena prostřednictvím tabulek Azure trasu (UDR). Veškerý odchozí provoz z jedné virtuální sítě do druhé virtuální sítě nebo do místního datového centra je směrován přes síťové virtuální zařízení.

![V místní – Azure pomocí ExpressRoute před převzetí služeb při selhání](./media/azure-vm-disaster-recovery-with-expressroute/site-recovery-with-expressroute-before-failover.png)

### <a name="hub-and-spoke-peering"></a>Centra a partnerský vztah paprsky

Paprsky do partnerského centra má následující konfiguraci:
-   Povolit virtuální síťová adresa: povoleno
-   Povolit přesměrovaný přenos: povoleno
-   Povolit průchod bránou: zakázáno
-   Použití odebrání brány: povoleno

 ![Paprsků centra konfigurace partnerského vztahu](./media/azure-vm-disaster-recovery-with-expressroute/spoke-to-hub-peering-configuration.png)

IOT hub a paprsků, partnerský vztah má následující konfiguraci:
-   Povolit virtuální síťová adresa: povoleno
-   Povolit přesměrovaný přenos: povoleno
-   Povolit průchod bránou: povoleno
-   Použití odebrání brány: zakázáno

 ![Centrum paprsků konfigurace partnerského vztahu](./media/azure-vm-disaster-recovery-with-expressroute/hub-to-spoke-peering-configuration.png)

### <a name="enabling-replication-for-the-deployment"></a>Povolení replikace pro nasazení

Pro nastavení výše první [nastavit zotavení po havárii](azure-to-azure-tutorial-enable-replication.md) pro každý virtuální počítač pomocí služby Site Recovery. Site Recovery můžete vytvořit virtuální sítě (včetně podsítí a podsítě brány) repliky v cílové oblasti a vytvořit požadované mapování mezi zdrojovou a cílovou virtuální sítí. Můžete také předem vytvořit cíl na straně sítě a podsítě a použít stejné při povolení replikace.

Site Recovery se nereplikují směrovací tabulky, brány virtuální sítě, připojení brány virtuální sítě, partnerský vztah virtuální sítě, nebo jakékoli jiné síťové prostředky nebo připojení. Tyto a další prostředky, které nejsou součástí [proces replikace](azure-to-azure-architecture.md#replication-process) je potřeba vytvořit při nebo před převzetí služeb při selhání a připojení na relevantní zdroje informací. Azure Site Recovery můžete používat Výkonné [plány obnovení](site-recovery-create-recovery-plans.md) automatizovat vytvoření a připojení dalších prostředků pomocí skriptů pro automatizaci.

Ve výchozím nastavení provoz replikace není neopustí hranice Azure. Nasazení síťové virtuální zařízení obvykle také definovat výchozí trasa (0.0.0.0/0), která vynutí odchozí internetové přenosy do probíhat přes síťové virtuální zařízení. V takovém případě zařízení může získat když se omezí všechny replikace provoz prochází přes síťové virtuální zařízení. Totéž platí také při použití výchozí trasy pro směrování veškerého provozu virtuálních počítačů Azure do místní nasazení. Doporučujeme [vytváření koncového bodu služby virtuální sítě](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) ve vaší virtuální sítě pro "Úložiště" tak, aby provoz replikace, nenechává hranice Azure.

## <a name="failover-models-with-expressroute"></a>Modely převzetí služeb při selhání s využitím ExpressRoute

Když virtuální počítače Azure se převzetí služeb při selhání do jiné oblasti, existující připojení ExpressRoute ke zdrojové síti virtuálních není automaticky převedena na cílovou virtuální síť v oblasti pro obnovení. Nové připojení se vyžaduje pro připojení ExpressRoute k cílovou virtuální sítí.

Můžete replikovat virtuální počítače Azure do libovolné oblasti Azure v rámci stejné zeměpisné clusteru jako podrobné [tady](azure-to-azure-support-matrix.md#region-support). Pokud není zvolené cílové oblasti Azure v rámci stejné geopolitické oblasti jako zdroj, je potřeba povolit ExpressRoute Premium, pokud používáte jeden okruh ExpressRoute pro zdrojovou a cílovou oblast připojení. Více informací najdete v [umístění ExpressRoute](../expressroute/expressroute-locations.md#azure-regions-to-expressroute-locations-within-a-geopolitical-region) a [ceny za ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute/).

### <a name="two-expressroute-circuits-in-two-different-expressroute-peering-locations"></a>Dva okruhy ExpressRoute ve dvou různých umístění partnerského vztahu ExpressRoute
-   Tato konfigurace je užitečná, pokud chcete zajistit proti selhání primární okruh ExpressRoute a ve velkém měřítku regionální katastrofy, které by mohly mít vliv na umístění partnerského vztahu ExpressRoute také a narušovat váš primární okruh ExpressRoute.
-   Okruh připojené do produkčního prostředí se používá obvykle bezporuchový je okruh primární a sekundární okruh a obvykle s menší šířkou pásma. Šířka pásma sekundární databáze je možné zvýšit v případě po havárii, když sekundární musí převzít kontrolu nad jako primární.
-   Pomocí této konfigurace může vytvořit připojení z vaší sekundární okruh ExpressRoute pro cílovou virtuální sítí vystavit převzetí služeb při selhání ani používat připojení navázané a připravený pro deklaraci po havárii, snižuje celkovou dobu obnovení. Souběžná připojení pro obě primární a cílové oblasti virtuální sítě, zajistit, aby používal směrování na místní sekundární okruh a připojení až po převzetí služeb při selhání.
-   Zdrojovou a cílovou virtuální sítí pro virtuální počítače chráněné pomocí Site Recovery může mít stejném nebo jiném IP adresy na převzetí služeb při selhání podle vašich požadavků. V obou případech je možné navázat připojení sekundární před převzetí služeb při selhání.

### <a name="two-expressroute-circuits-in-the-same-expressroute-peering-location"></a>Dva okruhy ExpressRoute ve stejném umístění partnerského vztahu ExpressRoute
-   Pomocí této konfigurace můžete zajistit proti selhání primární okruh ExpressRoute, ale před katastrofami ve velkém měřítku oblasti, které nelze mít vliv na umístění partnerského vztahu ExpressRoute. Posledně jmenované můžete získat ovlivněné primárních a sekundárních okruhů.
-   Další podmínky pro připojení a IP adresy zůstat stejné jako v předchozích případech. Souběžná připojení většího místního datacentra k virtuální síti zdroje s primární okruh a cílová virtuální síť s sekundární okruh může mít. Souběžná připojení pro obě primární a cílové oblasti virtuální sítě, zajistit, aby používal směrování na místní sekundární okruh a připojení až po převzetí služeb při selhání.
-   Nemůžete se připojit i okruhy ke stejné virtuální síti okruhy vytvořené ve stejném umístění partnerského vztahu.

### <a name="single-expressroute-circuit"></a>Jeden okruh ExpressRoute
-   Tato konfigurace není – pomáhat zajistit proti havárii regionální ve velkém měřítku, které by mohly ovlivnit umístění partnerského vztahu ExpressRoute.
-   Jeden okruh ExpressRoute nelze připojit zdrojové a cílové virtuální sítě k okruhu současně, pokud se používá stejné adresní prostor IP adres v cílové oblasti.
-   Při použití stejného adresní prostor IP adres v cílové oblasti připojení na straně zdroje by měly odpojen a na cílové straně navázat spojení po tomto datu. Tato změna připojení možnost využívat skripty jako součást plánu obnovení.
-   Selhání v oblasti pokud je primární oblast nedostupná operace odpojení, může selhat. Takové výpadku by mohlo mít vliv vytváření připojení k cílové oblasti, při použití stejného adresního prostoru IP adres na cílovou virtuální sítí.
-   Pokud připojení vytvoření úspěšné na cíli a obnoví primární oblasti později, může setkat drops paketů, dvě souběžná připojení pokusu o připojení na stejném adresním prostoru. Aby se zabránilo drops paketů, by měl primární připojení ukončeno okamžitě. Příspěvek navrácení služeb po obnovení virtuálních počítačů do primární oblasti, může primární připojení znovu navázat po odpojení sekundárního připojení.
-   Pokud na cílovou virtuální sítí se používá jiným adresním prostorem, pak můžete současně připojit ke zdroji a cílit na virtuálních sítí ze stejného okruhu ExpressRoute.

## <a name="recovering-azure-deployments"></a>Obnovení nasazení Azure
Vezměte v úvahu model převzetí služeb při selhání s dvěma jiné okruhy ExpressRoute ve dvou různých umístění partnerského vztahu a uchovávání dat soukromých IP adres pro chráněné virtuální počítače Azure. Sekundární připojení okruhu ExpressRoute pokládáme stav, prostřednictvím partnera edge in Singapore cílovou oblast pro obnovení je Azure jihovýchodní Asie.

K automatizaci obnovení celého nasazení, kromě replikace virtuálních počítačů a virtuálních sítí, připojení a další relevantní síťové prostředky musí být vytvořen. Pro starší hvězdicové síťové topologii jsou uvedené další kroky je třeba provést během nebo po [převzetí služeb při selhání](azure-to-azure-tutorial-failover-failback.md) operace:
1.  Vytvořte bránu Azure ExpressRoute v cílové oblasti centrální virtuální síti. Brány ExpressRoute se nejde připojit Centrum cílová virtuální síť k okruhu ExpressRoute.
2.  Vytvoření připojení virtuální sítě z cílová virtuální síť centra k cíli okruh ExpressRoute.
3.  Nastavte partnerské vztahy virtuálních sítí mezi centrem cílové oblasti a virtuálními sítěmi paprsků. Vlastnosti partnerského vztahu v cílové oblasti budou stejné jako ty ve zdrojové oblasti.
4.  Nastavte trasy definované uživatelem ve virtuální síti centra a dvě virtuální sítě paprsků. Vlastnosti straně cíle trasy definované uživatelem se shodují s nastaveními na straně zdroje při pomocí stejné IP adres. S jinou cílovou IP adresy třeba upravit odpovídajícím způsobem trasy definované uživatelem.

Jako součást možnost využívat skripty výše uvedené kroky [plánu obnovení](site-recovery-create-recovery-plans.md). V závislosti na připojení k aplikaci a požadavky na čas obnovení je možné výše uvedené kroky provést i před spuštěním převzetí služeb při selhání.

Po obnovení virtuálních počítačů a další kroky pro připojení se obnovení prostředí vypadá takto: ![na místní – Azure pomocí ExpressRoute po převzetí služeb při selhání](./media/azure-vm-disaster-recovery-with-expressroute/site-recovery-with-expressroute-after-failover.png)

Podrobný příklad jednoduchou topologie pro zotavení po havárii virtuálního počítače Azure pomocí jednoho okruhu ExpressRoute, se stejnou IP Adresou v cílových virtuálních počítačích [tady](site-recovery-retain-ip-azure-vm-failover.md#on-premises-to-azure-connectivity).

## <a name="recovery-time-objective-rto-considerations"></a>Aspekty zotavení cíle času (RTO)
Pokud chcete snížit celkový čas obnovení pro vaše nasazení, doporučujeme zřizování a nasazování dalších cílové oblasti [síťové součásti](azure-vm-disaster-recovery-with-expressroute.md#enabling-replication-for-the-deployment) například předem brány virtuální sítě. Malý výpadek souvisí s nasazením další zdroje informací a tohoto výpadku může mít vliv na celkový čas obnovení, není-li zvláštní pozornost během plánování.

Doporučujeme pravidelně [zotavení po havárii](azure-to-azure-tutorial-dr-drill.md) pro chráněné nasazení. Postup ověří vaši strategii replikace bez ztráty dat nebo výpadky a nemá vliv na vaše produkční prostředí. Postup spuštění se vyhnete také problémy s konfigurací poslední minutu, které může nepříznivě ovlivnit plánovanou dobu obnovení. Doporučujeme použít samostatnou síť virtuálních počítačů Azure testovací převzetí služeb při selhání, ne výchozí síť, kterou jste nastavili při povolování replikace.

Pokud používáte jeden okruh ExpressRoute, doporučujeme použít jiný prostor IP adres pro cílová virtuální síť abyste předešli problémům s připojení zařízení během oblastní havárií. Pokud pomocí různých IP adres není možné vydat je obnovený produkčním prostředí, po havárii obnovení procházení testovací převzetí služeb při selhání se má počítat na samostatný test sítě s jinou IP adresy jako nelze propojení dvou virtuálních sítí s překrývajícími se IP adresní prostor ke stejnému okruhu ExpressRoute.

## <a name="next-steps"></a>Další postup
- Další informace o [okruhy ExpressRoute](../expressroute/expressroute-circuit-peerings.md).
- Další informace o [domény směrování ExpressRoute](../expressroute/expressroute-circuit-peerings.md#expressroute-routing-domains).
- Další informace o [umístění ExpressRoute](../expressroute/expressroute-locations.md).
- Další informace o [plány obnovení](site-recovery-create-recovery-plans.md) automatizovat převzetí služeb při selhání aplikace.
