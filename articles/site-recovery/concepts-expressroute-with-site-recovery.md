---
title: Použití ExpressRoute s azure site recovery
description: Popisuje, jak používat Azure ExpressRoute se službou Azure Site Recovery pro zotavení po havárii a migraci.
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/13/2019
ms.author: mayg
ms.openlocfilehash: e4525bdc6165e8e736db5f539c764d25250cb248
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258002"
---
# <a name="azure-expressroute-with-azure-site-recovery"></a>Azure ExpressRoute s azure site recovery

Microsoft Azure ExpressRoute umožňuje rozšířit vaše místní sítě do cloudu Microsoftu přes soukromé připojení zajišťované poskytovatelem připojení. Pomocí ExpressRoute může vytvořit připojení ke cloudovým službám Microsoftu, jako je například Microsoft Azure, Office 365 nebo Dynamics 365.

Tento článek popisuje, jak můžete použít Azure ExpressRoute s Azure Site Recovery pro zotavení po havárii a migrace.

## <a name="expressroute-circuits"></a>Okruhy ExpressRoute

Okruh ExpressRoute představuje logické připojení mezi místní infrastrukturou a cloudovými službami Microsoftu prostřednictvím poskytovatele připojení. Můžete si objednat více okruhů ExpressRoute. Každý okruh může být ve stejné nebo jiné oblasti a může být připojen k vašim prostorům prostřednictvím různých poskytovatelů připojení. Další informace o obvodech ExpressRoute [naleznete zde](../expressroute/expressroute-circuit-peerings.md).

Okruh ExpressRoute má více směrovacích domén, které jsou k němu přidruženy. Další informace o směrovacích doménách ExpressRoute a jejich porovnání [naleznete zde](../expressroute/expressroute-circuit-peerings.md#peeringcompare).

## <a name="on-premises-to-azure-replication-with-expressroute"></a>Místní replikace do Azure s ExpressRoute

Azure Site Recovery umožňuje zotavení po havárii a migraci do Azure pro místní [virtuální počítače Hyper-V](hyper-v-azure-architecture.md), [virtuální počítače VMware](vmware-azure-architecture.md)a [fyzické servery](physical-azure-architecture.md). Pro všechny místní scénáře Azure se data replikace odesílají a ukládají v účtu Azure Storage. Během replikace neplatíte žádné poplatky za virtuální počítače. Když spustíte převzetí služeb při selhání do Azure, Site Recovery automaticky vytvoří virtuální počítače Azure IaaS.

Site Recovery replikuje data na účet Azure Storage nebo replika spravovaného disku v cílové oblasti Azure přes veřejný koncový bod. Chcete-li použít provoz replikace ExpressRoute for Site Recovery, můžete využít [partnerský vztah Microsoftu](../expressroute/expressroute-circuit-peerings.md#microsoftpeering) nebo existující [veřejný partnerský vztah](../expressroute/about-public-peering.md) (zastaralé pro nové výtvory). Partnerský vztah společnosti Microsoft je doporučená směrovací doména pro replikaci. Všimněte si, že replikace není podporována přes soukromý partnerský vztah.

Ujistěte se, že jsou [splněny](vmware-azure-configuration-server-requirements.md#network-requirements) také síťové požadavky pro konfigurační server. Konfigurační server vyžaduje připojení ke konkrétním adresám URL pro orchestraci replikace obnovení lokality. ExpressRoute nelze použít pro toto připojení. 

V případě, že používáte proxy server v místním prostředí a chcete použít ExpressRoute pro přenosreplikace, je třeba nakonfigurovat seznam obejití proxy serveru na konfiguračním serveru a procesních serverech. Postupujte následovně:

- Stáhněte si nástroj PsExec [zde](https://aka.ms/PsExec) pro přístup k uživatelskému kontextu systému.
- Spouštět aplikaci Internet Explorer v kontextu systémového uživatele spuštěním následujícího příkazového řádku psexec -s -i "%programfiles%\Internet Explorer\iexplore.exe"
- Přidání nastavení proxy serveru v i. E.
- V seznamu bypass přidejte adresu URL úložiště Azure *.blob.core.windows.net

Tím zajistíte, že pouze přenosreplikace toky přes ExpressRoute, zatímco komunikace může projít proxy.

Po převzetí služeb při selhání virtuální ch od virtuální sítě Azure virtuální počítače virtuální počítače, můžete k nim přistupovat pomocí [privátního partnerského vztahu](../expressroute/expressroute-circuit-peerings.md#privatepeering). 

Kombinovaný scénář je reprezentován v ![následujícím diagramu: Místní Azure s ExpressRoute](./media/concepts-expressroute-with-site-recovery/site-recovery-with-expressroute.png)

## <a name="azure-to-azure-replication-with-expressroute"></a>Replikace Azure to Azure pomocí ExpressRoute

Azure Site Recovery umožňuje zotavení po havárii [virtuálních počítačů Azure](azure-to-azure-architecture.md). V závislosti na tom, jestli vaše virtuální počítače Azure používají [spravované disky Azure](../virtual-machines/windows/managed-disks-overview.md), se data replikace posílají na účet Úložiště Azure nebo na repliku spravovaného disku v cílové oblasti Azure. Přestože koncové body replikace jsou veřejné, provoz replikace pro replikaci virtuálních počítačů Azure ve výchozím nastavení neprochází internetem, bez ohledu na to, ve které oblasti Azure zdrojová virtuální síť existuje. Můžete přepsat výchozí systémovou trasu Azure pro předponu adresy 0.0.0.0/0 s [vlastní trasou](../virtual-network/virtual-networks-udr-overview.md#custom-routes) a přesměrovat provoz virtuálních počítačů na místní síťové virtuální zařízení (NVA), ale tato konfigurace se nedoporučuje pro replikaci obnovení lokality. Pokud používáte vlastní trasy, měli byste [vytvořit koncový bod virtuální síťové služby](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) ve virtuální síti pro "úložiště", aby provoz replikace neopustil hranici Azure.

Pro zotavení po havárii virtuálního počítače Azure ve výchozím nastavení ExpressRoute není vyžadováno pro replikaci. Po převzetí služeb při selhání virtuálních počítačů do cílové oblasti Azure, můžete k nim přistupovat pomocí [privátního partnerského vztahu](../expressroute/expressroute-circuit-peerings.md#privatepeering). Všimněte si, že ceny za přenos dat platí bez ohledu na způsob replikace dat v rámci oblastí Azure.

Pokud už používáte ExpressRoute pro připojení z místního datového centra k virtuálním počítačům Azure ve zdrojové oblasti, můžete naplánovat obnovení připojení ExpressRoute v cílové oblasti převzetí služeb při selhání. Stejný okruh ExpressRoute můžete použít k připojení k cílové oblasti prostřednictvím nového připojení virtuální sítě nebo můžete použít samostatný okruh ExpressRoute a připojení pro zotavení po havárii. Zde jsou [popsány](azure-vm-disaster-recovery-with-expressroute.md#fail-over-azure-vms-when-using-expressroute)různé možné scénáře .

Virtuální počítače Azure můžete replikovat do libovolné oblasti Azure ve stejném geografickém clusteru, jak [je podrobně popsáno zde](../site-recovery/azure-to-azure-support-matrix.md#region-support). Pokud vybraná cílová oblast Azure není ve stejné geopolitické oblasti jako zdroj, možná budete muset povolit ExpressRoute Premium. Další podrobnosti naleznete v [umístěních ExpressRoute](../expressroute/expressroute-locations.md) a [cenách ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute/).

## <a name="next-steps"></a>Další kroky
- Další informace o [obvodech ExpressRoute](../expressroute/expressroute-circuit-peerings.md).
- Další informace o [směrovacích doménách ExpressRoute](../expressroute/expressroute-circuit-peerings.md#peeringcompare).
- Další informace o [umístěních ExpressRoute](../expressroute/expressroute-locations.md).
- Další informace o zotavení po havárii [virtuálních počítačů Azure s ExpressRoute](azure-vm-disaster-recovery-with-expressroute.md).
