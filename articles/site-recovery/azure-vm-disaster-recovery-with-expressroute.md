---
title: Zotavení po havárii virtuálního počítače Azure pomocí ExpressRoute | Microsoft Docs
description: Popisuje, jak používat Azure ExpressRoute s Azure Site Recovery pro virtuální počítač Azure zotavení po havárii
services: site-recovery
documentationcenter: ''
author: mayanknayar
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/30/2018
ms.author: manayar
ms.openlocfilehash: 8b4c7bb561d311796f59b94a6e2fbcd9b571d2cd
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/01/2018
---
# <a name="using-expressroute-with-azure-virtual-machine-disaster-recovery"></a>Pomocí ExpressRoute zotavení po havárii virtuální počítač Azure

Microsoft Azure ExpressRoute umožňuje rozšířit vaše místní sítě do cloudu Microsoftu přes soukromé připojení zajišťované poskytovatelem připojení. Tento článek popisuje, jak používat ExpressRoute s Site Recovery pro zotavení po havárii virtuální počítače Azure.

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že rozumíte:
-   ExpressRoute [okruhy](../expressroute/expressroute-circuit-peerings.md)
-   ExpressRoute [domény směrování](../expressroute/expressroute-circuit-peerings.md#expressroute-routing-domains)
-   Virtuální počítač Azure [architektura replikace](azure-to-azure-architecture.md)
-   [Nastavení replikace](azure-to-azure-tutorial-enable-replication.md) pro virtuální počítače Azure
-   [Přebírání služeb při selhání](azure-to-azure-tutorial-failover-failback.md) virtuální počítače Azure

## <a name="expressroute-and-azure-virtual-machine-replication"></a>ExpressRoute a Azure replikaci virtuálního počítače

Pokud chráníte virtuální počítače Azure pomocí Site Recovery, data replikace je odeslána do účtu úložiště Azure nebo repliky spravované Disk v cílovém oblast Azure, v závislosti na tom, jestli vaše Azure virtuální počítače, použijte [Azure spravované disky](../virtual-machines/windows/managed-disks-overview.md). I když jsou veřejné koncové body replikace, provoz replikace pro replikaci virtuálního počítače Azure, ve výchozím nastavení, neprochází Internetu, bez ohledu na to, které oblasti Azure zdrojovou virtuální síť v existuje.

Pro virtuální počítač Azure zotavení po havárii jako data replikace, nenechává Azure hranic ExpressRoute se nevyžaduje pro replikaci. Po virtuálním počítačům převzetí služeb při selhání k cíli oblast Azure, se dostanete pomocí [soukromého partnerského vztahu](../expressroute/expressroute-circuit-peerings.md#azure-private-peering).

## <a name="replicating-azure-deployments"></a>Replikace nasazení Azure

Předchozí [článku](site-recovery-retain-ip-azure-vm-failover.md#on-premises-to-azure-connectivity), popsané jednoduché nastavení jedna virtuální síť Azure připojené k zákazníka místního datového centra prostřednictvím ExpressRoute. Typické podnikové nasazení provádějí úlohy rozdělit do více virtuálních sítí Azure a centrální připojení k rozbočovači vytvoří externí připojení k Internetu a pro místní nasazení.

Tento příklad popisuje hvězdicové topologie, které je běžné v podnikových nasazeních:
-   Nasazení je ve **Azure východní Asie** oblasti a místním datacentru má připojení okruh ExpressRoute prostřednictvím partnera hrany v Hongkong.
-   Se aplikace nasadí mezi dvě virtuální sítě ramenem – **zdroj VNet1** s 10.1.0.0/24 prostoru adres a **VNet2 zdroj** s 10.2.0.0/24 prostor adres.
-   Virtuální síť rozbočovače **zdroj rozbočovače VNet**, s adresním prostorem 10.10.10.0/24 funguje jako těchto pravidel. Veškerá komunikace mezi podsítěmi projde rozbočovače.
-   Virtuální síť rozbočovače má dvě podsítě – **podsíť hodnocení chyb zabezpečení** s 10.10.10.0/25 prostoru adres a **podsíť brány** s 10.10.10.128/25 prostoru adres.
-   **Hodnocení chyb zabezpečení podsíť** má virtuální síťové zařízení s IP adresou. 10.10.10.10.
-   **Podsíť brány** na připojení ExpressRoute, který směruje na zákazníka místního datového centra prostřednictvím soukromého partnerského vztahu směrování domény připojil bránu ExpressRoute.
-   Každý ramenem virtuální síť je připojená k virtuální síti rozbočovače a všechny směrování v rámci této topologie sítě je řízena pomocí Azure trasy tabulky (UDR). Všechny odchozí přenosy z jedné virtuální sítě do jiné sítě VNet nebo do místního datového centra je směrován přes hodnocení chyb zabezpečení.

![Na místní do Azure prostřednictvím ExpressRoute před převzetí služeb při selhání](./media/azure-vm-disaster-recovery-with-expressroute/site-recovery-with-expressroute-before-failover.png)

### <a name="hub-and-spoke-peering"></a>Středem a ramenem partnerského vztahu

Ramenem na partnerský vztah rozbočovače má následující konfiguraci:
-   Povolit virtuální síťová adresa: povoleno
-   Povolit přesměrovaných přenosů: povoleno
-   Povolit bránu přenosu: zakázáno
-   Použití odeberte brány: povoleno

 ![Příčky konfiguraci partnerského vztahu rozbočovače](./media/azure-vm-disaster-recovery-with-expressroute/spoke-to-hub-peering-configuration.png)

Rozbočovače k příčkou partnerský vztah má následující konfiguraci:
-   Povolit virtuální síťová adresa: povoleno
-   Povolit přesměrovaných přenosů: povoleno
-   Povolit bránu přenosu: povoleno
-   Použití odeberte brány: zakázáno

 ![Centrum pro příčkou konfiguraci partnerského vztahu](./media/azure-vm-disaster-recovery-with-expressroute/hub-to-spoke-peering-configuration.png)

### <a name="enabling-replication-for-the-deployment"></a>Povolení replikace pro nasazení

Pro výše uvedené nastavení první [nastavit zotavení po havárii](azure-to-azure-tutorial-enable-replication.md) pro každý virtuální počítač pomocí Site Recovery. Site Recovery můžete vytvořit repliky virtuálních sítí (včetně podsítě a podsítě brány) na cílová oblast a vytvořte požadovaná mapování mezi zdrojovými a cílovými virtuální sítě. Můžete také předem vytvořit cíl straně sítě a podsítě a používat stejné při povolení replikace.

Site Recovery se nereplikuje směrovací tabulky, brány virtuální sítě, připojení brány virtuální sítě, partnerský vztah virtuální sítě, nebo jakékoli jiné sítě prostředky nebo připojení. Tyto a další prostředky, které nejsou součástí [replikační proces](azure-to-azure-architecture.md#replication-process) je třeba vytvořit během nebo před převzetí služeb při selhání a připojený k příslušné prostředky. Můžete použít Azure Site Recovery výkonné [plány obnovení](site-recovery-create-recovery-plans.md) automatizovat vytvoření a připojení dalších prostředků pomocí skripty pro automatizaci.

Ve výchozím nastavení nenechává provoz replikace Azure hranic. Obvykle se nasazení hodnocení chyb zabezpečení taky definovat výchozí trasa (0.0.0.0/0), který vynutí odchozí přenosy z Internetu do procházet skrz hodnocení chyb zabezpečení. V takovém případě zařízení může získat omezena Pokud postupuje veškerý provoz replikace do hodnocení chyb zabezpečení. Totéž platí i v případě použití výchozí trasy pro směrování všechny přenosy virtuálních počítačů Azure do místního nasazení. Doporučujeme, abyste [vytvoření koncového bodu služby virtuální sítě](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) ve vaší virtuální sítě pro "Úložiště" tak, aby provoz replikace, nenechává Azure hranic.

## <a name="failover-models-with-expressroute"></a>Modely převzetí služeb při selhání s ExpressRoute

Když virtuální počítače Azure se převzetí služeb při selhání v jiné oblasti, existující připojení ExpressRoute do virtuální sítě zdroj nebudou převedeny automaticky na cílové virtuální síť v oblasti obnovení. Nové připojení je vyžadován pro připojení ExpressRoute na cílové virtuální síť.

Virtuální počítače Azure může replikovat na všechny oblasti Azure v rámci stejné zeměpisné clusteru jako podrobné [zde](azure-to-azure-support-matrix.md#region-support). Pokud není zvolený cíl oblast Azure v rámci stejné geopolitické oblasti jako zdroj, budete muset povolit ExpressRoute Premium, pokud používáte jeden okruh ExpressRoute pro zdrojové a cílové oblasti připojení. Další podrobnosti naleznete [umístění ExpressRoute](../expressroute/expressroute-locations.md#azure-regions-to-expressroute-locations-within-a-geopolitical-region) a [ExpressRoute ceny](https://azure.microsoft.com/en-us/pricing/details/expressroute/).

### <a name="two-expressroute-circuits-in-two-different-expressroute-peering-locations"></a>Dva okruhy ExpressRoute ve dvou různých umístění partnerského vztahu ExpressRoute
-   Tato konfigurace je užitečná, pokud chcete zajistit proti selhání primární okruhu ExpressRoute a rozsáhlé regionální havárie, které by mohly mít vliv na umístění partnerského vztahu ExpressRoute také a přerušit váš primární okruh ExpressRoute.
-   Za normálních okolností okruh připojené do produkčního prostředí se používá jako okruh primární a sekundární okruh je bezporuchový a obvykle s menší šířkou pásma. V případě havárie, při sekundární musí převzetí jako primární lze zvětšit šířku pásma sekundární.
-   Pomocí této konfigurace můžete vytvořit připojení z vaší sekundární okruh ExpressRoute k virtuální síti cíl post převzetí služeb při selhání nebo mají připojení navázáno a připravena k deklaraci po havárii snižuje celkovou dobu obnovení. S současných připojení k obě primární a cílová oblast virtuálních sítí, zajistěte, aby místní směrování používá sekundární okruhu a připojení až po převzetí služeb při selhání.
-   Zdrojové a cílové virtuální sítě pro virtuální počítače chráněné pomocí Site Recovery může mít stejný nebo jiný IP adresy na převzetí služeb při selhání na váš požadavek. V obou případech sekundární připojení lze navázat před převzetí služeb při selhání.

### <a name="two-expressroute-circuits-in-the-same-expressroute-peering-location"></a>Dva okruhy ExpressRoute ve stejném umístění partnerského vztahu ExpressRoute
-   Pomocí této konfigurace můžete lze uzavřít pro případ selhání primárního okruhu ExpressRoute, ale před rozsáhlé regionální havárie, který nelze mít vliv na umístění partnerského vztahu ExpressRoute. Posledně jmenované můžete získat primární a sekundární okruhy dopad.
-   Další podmínky pro IP adresy a připojení zůstat stejné jako v případě, že starší. Můžete mít souběžných připojení z místního datového centra pro zdrojové virtuální síť s primární okruh a na cílové virtuální síť s sekundární okruh. S současných připojení k obě primární a cílová oblast virtuálních sítí, zajistěte, aby místní směrování používá sekundární okruhu a připojení až po převzetí služeb při selhání.
-   Obě okruhy nemůže připojit k stejné virtuální síti okruhy vytvořené ve stejném umístění partnerského vztahu.

### <a name="single-expressroute-circuit"></a>Jeden okruh ExpressRoute
-   Tato konfigurace není uzavřít pro případ havárie místní ve velkém měřítku, což by mohlo mít vliv na umístění partnerského vztahu ExpressRoute.
-   S jeden okruh ExpressRoute nelze připojit zdrojové a cílové virtuální sítě k okruhu současně, pokud se v oblasti cíl používá stejné adresní prostor IP adres.
-   Když na cílová oblast se používá stejné adresní prostor IP adres, připojení straně zdroj by měl odpojit, a vytvořené po tomto datu připojení straně cíl. Tato změna připojení možnost využívat skripty jako součást plánu obnovení.
-   Místní selhání Pokud primární oblasti je dostupná, operace odpojení, může nezdaří. Takové výpadku by mohlo mít vliv vytvoření připojení k cílová oblast, když se na cílový virtuální sítě používá stejné adresní prostor IP adres.
-   Pokud vytvoření připojení se podaří na cíli a obnoví primární oblasti později, můžete čelí paketu vyřazuje, když se pokusí připojit ke stejnému adresnímu prostoru adres dvě souběžných připojení. Pokud chcete zabránit vyřazuje paketů, by měl být primární připojení ukončeno okamžitě. POST navrácení služeb po obnovení virtuálních počítačů na primární oblasti, může primární připojení znovu navázat po odpojení sekundární připojení.
-   Pokud na cílové virtuální síť se používá jiným adresním prostorem, pak můžete současně připojit ke zdroji a cílové virtuální sítě od stejnému okruhu ExpressRoute.

## <a name="recovering-azure-deployments"></a>Obnovení nasazení Azure
Vezměte v úvahu model převzetí služeb při selhání se dvěma jiné okruhy ExpressRoute ve dvou různých umístění partnerského vztahu a jejich uchovávání soukromé IP adresy pro chráněné virtuální počítače Azure. Cílová oblast pro obnovení je jihovýchodní Asie Azure a prostřednictvím partnera hrany v Singapur se naváže připojení k sekundární okruhu ExpressRoute.

K automatizaci obnovení celého nasazení, kromě replikace virtuálních počítačů a virtuálními sítěmi a další relevantní síťových prostředků a připojení musí být vytvořen. Pro starší hvězdicové topologie sítě jsou uvedené další kroky je třeba přijmout během nebo po [převzetí služeb při selhání](azure-to-azure-tutorial-failover-failback.md) operace:
1.  Vytvoření brány ExpressRoute Azure ve virtuální síti cílové oblasti rozbočovače. Bránu ExpressRoute je požadované pro připojení rozbočovače cílový virtuální sítě k okruhu ExpressRoute.
2.  Vytvoření připojení virtuální sítě z cílové virtuální síti rozbočovače k cíli okruh ExpressRoute.
3.  Nastavte virtuální síť partnerských vztahů mezi cílová oblast rozbočovače a ramenem virtuální sítě. Vlastnosti partnerského vztahu na cílová oblast bude stejné jako na zdrojovém oblast.
4.  Nastavte udr v centru virtuální síť a dvě ramenem virtuální sítě. Vlastnosti straně cíle udr jsou stejné jako na straně zdroje při použití stejné IP adres. S jinou cílovou IP adresy třeba udr upravit odpovídajícím způsobem.

Výše uvedené kroky můžete provádět skriptování v rámci [plán obnovení](site-recovery-create-recovery-plans.md). V závislosti na připojení k aplikaci a požadavky na čas obnovení lze výše uvedené kroky také dokončit před spuštěním převzetí služeb při selhání.

Konečná obnovení virtuálních počítačů a další kroky pro připojení, obnovení prostředí vypadá takto: ![na místní do Azure prostřednictvím ExpressRoute po převzetí služeb při selhání](./media/azure-vm-disaster-recovery-with-expressroute/site-recovery-with-expressroute-after-failover.png)

Příklad jednoduchou topologie pro zotavení po havárii virtuální počítač Azure s jeden okruh ExpressRoute, se stejnou IP Adresou na cílových virtuálních počítačů, je podrobně popsán [zde](site-recovery-retain-ip-azure-vm-failover.md#on-premises-to-azure-connectivity).

## <a name="recovery-time-objective-rto-considerations"></a>Aspekty čas cíl (RTO) obnovení
Pokud chcete zkrátit celkovou dobu obnovení pro vaše nasazení, doporučujeme zřizování a nasazování dalších cílová oblast [síťovými součástmi](azure-vm-disaster-recovery-with-expressroute.md#enabling-replication-for-the-deployment) například předem brány virtuální sítě. Malé výpadek souvisí s nasazením další prostředky, a tento výpadek může mít vliv na celkovou dobu obnovení, není-li pozornost při plánování.

Doporučujeme spustit regular [podrobněji obnovení po havárii](azure-to-azure-tutorial-dr-drill.md) pro chráněný nasazení. Přechod ověří strategie replikace bez ztráty dat nebo výpadek a nemá vliv produkční prostředí. Spuštění procházení zabraňuje také problémy s konfigurací poslední minutu, které může nepříznivě ovlivnit plánovanou dobu obnovení. Doporučujeme použít samostatnou síť virtuálního počítače Azure testovací převzetí služeb při selhání, namísto výchozí sítí, která byla vytvořená při povolení replikace.

Pokud používáte jeden okruh ExpressRoute, doporučujeme použít jiný prostor IP adres pro cílový virtuální síť abyste předešli problémům s navázání připojení během regionální havárie. Pokud pomocí různých IP adres není vhodná pro obnovené provozním prostředí, po havárii obnovení procházení testovací převzetí služeb při selhání by mělo být provedeno v samostatné testovací síti pomocí různých IP adres jako dvě virtuální sítě nemůžete se připojit s překrývajícími se IP adresní prostor ke stejnému okruhu ExpressRoute.

## <a name="next-steps"></a>Další postup
- Další informace o [okruhy ExpressRoute](../expressroute/expressroute-circuit-peerings.md).
- Další informace o [domény směrování ExpressRoute](../expressroute/expressroute-circuit-peerings.md#expressroute-routing-domains).
- Další informace o [umístění ExpressRoute](../expressroute/expressroute-locations.md).
- Další informace o [plány obnovení](site-recovery-create-recovery-plans.md) automatizovat převzetí služeb při selhání aplikace.
