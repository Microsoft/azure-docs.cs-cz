---
title: Azure ExpressRoute s Azure Site Recovery | Microsoft Docs
description: Popisuje, jak používat Azure ExpressRoute s Azure Site Recovery pro zotavení po havárii a migrace
services: site-recovery
documentationcenter: ''
author: mayanknayar
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 05/11/2018
ms.author: manayar
ms.openlocfilehash: 7cc4c84ebae7ade4169f8d85a2d5cc11f1df6f87
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
---
# <a name="azure-expressroute-with-azure-site-recovery"></a>Azure ExpressRoute s Azure Site Recovery

Microsoft Azure ExpressRoute umožňuje rozšířit vaše místní sítě do cloudu Microsoftu přes soukromé připojení zajišťované poskytovatelem připojení. Pomocí ExpressRoute může vytvořit připojení ke cloudovým službám Microsoftu, jako je například Microsoft Azure, Office 365 nebo Dynamics 365.

Tento článek popisuje, jak používat Azure ExpressRoute s Azure Site Recovery pro zotavení po havárii a migrace.

## <a name="expressroute-circuits"></a>Okruhy ExpressRoute

Okruh ExpressRoute představuje logické připojení mezi místní infrastrukturu a cloudových služeb Microsoftu prostřednictvím poskytovatele připojení. Můžete uspořádat více okruhů ExpressRoute. Každý okruh může být ve stejné nebo různých oblastí a může být připojen k vaší místní prostřednictvím poskytovatelů jiné připojení. Další informace o okruhy ExpressRoute [zde](../expressroute/expressroute-circuit-peerings.md).

## <a name="expressroute-routing-domains"></a>Domény směrování ExpressRoute

Okruh ExpressRoute má přidruženo více domény směrování:
-   [Soukromý partnerský vztah Azure](../expressroute/expressroute-circuit-peerings.md#azure-private-peering) – Azure výpočetní služby, konkrétně virtuální počítače (IaaS), a cloudové služby (PaaS), které jsou nasazeny v rámci virtuální sítě může být připojen prostřednictvím soukromého partnerského vztahu domény. Soukromého partnerského vztahu domény je považován za důvěryhodný rozšíření základní sítě do Microsoft Azure.
-   [Veřejný partnerský vztah Azure](../expressroute/expressroute-circuit-peerings.md#azure-public-peering) -jsou nabízené služby, například Azure Storage, databáze SQL a weby na veřejné IP adresy. Soukromě můžete připojit ke službám hostovaným na veřejné IP adresy, včetně virtuální IP adresy z vašich cloudových služeb prostřednictvím veřejného partnerského vztahu doménu směrování. Veřejný partnerský vztah se už nepoužívá pro nové vytváření a Microsoft Peering se mají použít pro služby Azure PaaS místo.
-   [Partnerský vztah Microsoftu](../expressroute/expressroute-circuit-peerings.md#microsoft-peering) -připojení ke službám Microsoft online services (služby Office 365, Dynamics 365 a Azure PaaS) je prostřednictvím partnerského vztahu Microsoftu. Partnerský vztah Microsoftu je doporučené domény směrování pro připojení ke službám Azure PaaS.

Další informace a porovnání domény směrování ExpressRoute [zde](../expressroute/expressroute-circuit-peerings.md#routing-domain-comparison).

## <a name="on-premises-to-azure-replication-with-expressroute"></a>Místním nasazením a Azure replikace s ExpressRoute

Azure Site Recovery umožňuje zotavení po havárii a migrace do Azure pro místní [virtuální počítače Hyper-V](hyper-v-azure-architecture.md), [virtuálních počítačů VMware](vmware-azure-architecture.md), a [fyzických serverů](physical-azure-architecture.md). Data replikace je pro všechny místní Azure scénářů, odesílají a ukládají v účtu Azure Storage. Během replikace nemusíte platit žádné poplatky za virtuální počítače. Při spuštění převzetí služeb při selhání do Azure, Site Recovery automaticky vytvoří virtuální počítače Azure IaaS.

Site Recovery replikuje data do účtu úložiště Azure přes veřejný koncový bod. Chcete-li použít ExpressRoute pro replikaci Site Recovery, můžete použít [veřejného partnerského vztahu](../expressroute/expressroute-circuit-peerings.md#azure-public-peering) nebo [partnerského vztahu Microsoftu](../expressroute/expressroute-circuit-peerings.md#microsoft-peering). Partnerský vztah Microsoftu je doporučené domény směrování pro replikaci. Po virtuální počítače nebo servery převzetí služeb při selhání pro virtuální síť Azure, se dostanete pomocí [soukromého partnerského vztahu](../expressroute/expressroute-circuit-peerings.md#azure-private-peering). Replikace není podporována prostřednictvím soukromého partnerského vztahu.

V následujícím diagramu je reprezentována kombinované scénář: ![na místní do Azure prostřednictvím ExpressRoute](./media/concepts-expressroute-with-site-recovery/site-recovery-with-expressroute.png)

## <a name="azure-to-azure-replication-with-expressroute"></a>Replikace Azure do Azure prostřednictvím ExpressRoute

Azure Site Recovery umožňuje zotavení po havárii [virtuální počítače Azure](azure-to-azure-architecture.md). V závislosti na tom, jestli vaše Azure virtuální počítače, použijte [Azure spravované disky](../virtual-machines/windows/managed-disks-overview.md), data replikace je odeslána do účtu úložiště Azure nebo repliky spravované Disk na cíli oblast Azure. I když jsou veřejné koncové body replikace, provoz replikace pro replikaci virtuálního počítače Azure, ve výchozím nastavení, neprochází Internetu, bez ohledu na to, které oblasti Azure zdrojovou virtuální síť v existuje. Můžete přepsat Azure výchozí systému trasu pro předpony adres 0.0.0.0/0 s [vlastní trasy](../virtual-network/virtual-networks-udr-overview.md#custom-routes) a přesměrovat přenosy virtuálních počítačů pro virtuální zařízení místní síti (hodnocení chyb zabezpečení), ale tato konfigurace se nedoporučuje pro obnovení lokality replikace. Pokud používáte vlastní trasy, měli byste [vytvoření koncového bodu služby virtuální sítě](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) ve vaší virtuální sítě pro "Úložiště" tak, aby provoz replikace, nenechává Azure hranic.

Pro zotavení po havárii virtuálních počítačů Azure ve výchozím nastavení, ExpressRoute se nevyžaduje pro replikaci. Po virtuálním počítačům převzetí služeb při selhání k cíli oblast Azure, se dostanete pomocí [soukromého partnerského vztahu](../expressroute/expressroute-circuit-peerings.md#azure-private-peering).

Pokud už používáte ExpressRoute připojit z vašeho místního datového centra k virtuálním počítačům Azure v oblasti zdroje, můžete naplánovat pro opětovné vytvoření připojení ExpressRoute v oblasti cíl převzetí služeb při selhání. Stejnému okruhu ExpressRoute slouží k připojení k cílová oblast prostřednictvím připojení k nové virtuální sítě nebo využívat samostatné okruh ExpressRoute a připojení pro zotavení po havárii. Jsou popsané různé možné scénáře [zde](azure-vm-disaster-recovery-with-expressroute.md#failover-models-with-expressroute).

Virtuální počítače Azure může replikovat na všechny oblasti Azure v rámci stejné zeměpisné clusteru jako podrobné [zde](../site-recovery/azure-to-azure-support-matrix.md#region-support). Pokud není zvolený cíl oblast Azure v rámci stejné geopolitické oblasti jako zdroj, může musíte povolit ExpressRoute Premium. Další podrobnosti naleznete [umístění ExpressRoute](../expressroute/expressroute-locations.md#azure-regions-to-expressroute-locations-within-a-geopolitical-region) a [ExpressRoute ceny](https://azure.microsoft.com/pricing/details/expressroute/).

## <a name="next-steps"></a>Další postup
- Další informace o [okruhy ExpressRoute](../expressroute/expressroute-circuit-peerings.md).
- Další informace o [domény směrování ExpressRoute](../expressroute/expressroute-circuit-peerings.md#expressroute-routing-domains).
- Další informace o [umístění ExpressRoute](../expressroute/expressroute-locations.md).
- Další informace o zotavení po havárii [virtuální počítače Azure prostřednictvím ExpressRoute ](azure-vm-disaster-recovery-with-expressroute.md).
