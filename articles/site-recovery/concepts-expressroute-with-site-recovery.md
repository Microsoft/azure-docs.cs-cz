---
title: Informace o používání Azure ExpressRoute pomocí Azure Site Recovery pro zotavení po havárii a migrace | Dokumentace Microsoftu
description: Popisuje, jak pomocí služby Azure Site Recovery pro zotavení po havárii a migraci do Azure ExpressRoute.
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: mayg
ms.openlocfilehash: 14d74c879ee37306f541c89d2ba325109a4ff7f3
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2018
ms.locfileid: "50214864"
---
# <a name="azure-expressroute-with-azure-site-recovery"></a>Azure ExpressRoute pomocí Azure Site Recovery

Microsoft Azure ExpressRoute umožňuje rozšířit vaše místní sítě do cloudu Microsoftu přes soukromé připojení zajišťované poskytovatelem připojení. Pomocí ExpressRoute může vytvořit připojení ke cloudovým službám Microsoftu, jako je například Microsoft Azure, Office 365 nebo Dynamics 365.

Tento článek popisuje, jak můžete pomocí Azure ExpressRoute pomocí Azure Site Recovery pro zotavení po havárii a migrace.

## <a name="expressroute-circuits"></a>Okruhy ExpressRoute

Okruh ExpressRoute představuje logické propojení mezi vaši infrastrukturou v místním a cloudovým službám Microsoftu prostřednictvím poskytovatele připojení. Můžete si objednat víc okruhů ExpressRoute. Každý okruh může být v jedné nebo několika oblastech a jde připojit k místním prostřednictvím poskytovatelů jiné připojení. Další informace o okruhy ExpressRoute [tady](../expressroute/expressroute-circuit-peerings.md).

## <a name="expressroute-routing-domains"></a>Domény směrování ExpressRoute

Okruh ExpressRoute má přidruženo více doménách směrování:
-   [Soukromý partnerský vztah Azure](../expressroute/expressroute-circuit-peerings.md#azure-private-peering) – výpočetní služby, a to virtuálních počítačích (IaaS), Azure a cloudové služby (PaaS), které jsou nasazené v rámci virtuální sítě můžete připojit přes privátní partnerský vztah domény. Privátní partnerský vztah domény se považuje za důvěryhodným rozšířením vaší základní sítě do Microsoft Azure.
-   [Veřejný partnerský vztah Azure](../expressroute/expressroute-circuit-peerings.md#azure-public-peering) -služeb, jako je Azure Storage, SQL Database a Websites se nabízejí na veřejné IP adresy. Soukromě můžete připojit ke službám hostovaným na veřejných IP adresách, včetně virtuálních IP adres z vašich cloudových služeb prostřednictvím veřejného partnerského vztahu domény směrování. Veřejný partnerský vztah se už nepoužívá pro vytvoření nové a Microsoft Peering byste měli použít místo toho pro služby Azure PaaS.
-   [Partnerský vztah Microsoftu](../expressroute/expressroute-circuit-peerings.md#microsoft-peering) – připojení k online službám Microsoftu (Office 365, Dynamics 365 a Azure PaaS služby), je prostřednictvím partnerského vztahu Microsoftu. Partnerský vztah Microsoftu je doporučené směrování domény pro připojení ke službám Azure PaaS.

Další informace a porovnání domény směrování ExpressRoute [tady](../expressroute/expressroute-circuit-peerings.md#routing-domain-comparison).

## <a name="on-premises-to-azure-replication-with-expressroute"></a>Místní replikace Azure pomocí ExpressRoute

Azure Site Recovery nabízí zotavení po havárii a migraci do Azure pro místní [virtuálních počítačů Hyper-V](hyper-v-azure-architecture.md), [virtuálních počítačů VMware](vmware-azure-architecture.md), a [fyzických serverů](physical-azure-architecture.md). Data replikace pro všechny místní do scénáře služby Azure, se odesílají a ukládají v účtu služby Azure Storage. Během replikace neplatíte žádné poplatky za virtuální počítače. Při spuštění převzetí služeb při selhání do Azure Site Recovery automaticky vytvoří virtuální počítače Azure IaaS.

Site Recovery replikuje data do účtu úložiště Azure přes veřejný koncový bod. Pro účely replikace Site Recovery ExpressRoute, můžete využít [veřejného partnerského vztahu](../expressroute/expressroute-circuit-peerings.md#azure-public-peering) nebo [partnerský vztah Microsoftu](../expressroute/expressroute-circuit-peerings.md#microsoft-peering). Partnerský vztah Microsoftu je doporučené směrování domény pro replikaci. Po virtuálních počítačů nebo serverů převzetí služeb při selhání ke službě Azure virtual network, k nim můžete přistupovat pomocí [soukromého partnerského vztahu](../expressroute/expressroute-circuit-peerings.md#azure-private-peering). Replikace není podporována přes privátní partnerský vztah.

Následující diagram je reprezentován kombinované scénáře: ![na místní – Azure pomocí ExpressRoute](./media/concepts-expressroute-with-site-recovery/site-recovery-with-expressroute.png)

## <a name="azure-to-azure-replication-with-expressroute"></a>Replikace z Azure do Azure pomocí ExpressRoute

Azure Site Recovery nabízí zotavení po havárii [virtuálních počítačů Azure](azure-to-azure-architecture.md). V závislosti na tom, jestli vaše virtuální počítače Azure pomocí [Azure Managed Disks](../virtual-machines/windows/managed-disks-overview.md), replikace se odešlou na účet služby Azure Storage nebo repliky v cílové oblasti Azure se spravovanými disky. I když jsou veřejné koncové body replikace, provoz replikace pro replikaci virtuálních počítačů Azure, ve výchozím nastavení, neprocházejí přes Internet, bez ohledu na to, které oblasti Azure zdrojová virtuální síť existuje v. Můžete přepsat Azure výchozí systémovou trasu pro předponu adresy 0.0.0.0/0 s [vlastní trasy](../virtual-network/virtual-networks-udr-overview.md#custom-routes) a přesměrovat provoz virtuálního počítače místní síťové virtuální zařízení (NVA), ale tato konfigurace se nedoporučuje pro Site Recovery replikace. Pokud používáte vlastní trasy, měli byste [vytvořit koncový bod služby virtuální sítě](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) ve vaší virtuální sítě pro "Úložiště" tak, aby provoz replikace, nenechává hranice Azure.

Pro zotavení po havárii virtuálního počítače Azure ve výchozím nastavení, ExpressRoute se nevyžaduje pro replikaci. Po virtuálním počítačům převzetí služeb při selhání do cílové oblasti Azure, k nim můžete přistupovat pomocí [soukromého partnerského vztahu](../expressroute/expressroute-circuit-peerings.md#azure-private-peering).

Pokud už používáte ExpressRoute pro připojení z místního datacentra do virtuálních počítačů Azure na zdrojové oblasti, můžete naplánovat pro obnovujete připojení ExpressRoute v cílové oblasti převzetí služeb při selhání. Stejnému okruhu ExpressRoute můžete použít pro připojení k cílové oblasti novou virtuální síťové připojení nebo využití samostatné okruh ExpressRoute a připojení pro zotavení po havárii. Různé možné scénáře jsou popsané [tady](azure-vm-disaster-recovery-with-expressroute.md#fail-over-azure-vms-when-using-expressroute).

Můžete replikovat virtuální počítače Azure do libovolné oblasti Azure v rámci stejné zeměpisné clusteru jako podrobné [tady](../site-recovery/azure-to-azure-support-matrix.md#region-support). Pokud není zvolené cílové oblasti Azure v rámci stejné geopolitické oblasti jako zdroj, může být potřeba povolit ExpressRoute Premium. Více informací najdete v [umístění ExpressRoute](../expressroute/expressroute-locations.md#azure-regions-to-expressroute-locations-within-a-geopolitical-region) a [ceny za ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute/).

## <a name="next-steps"></a>Další postup
- Další informace o [okruhy ExpressRoute](../expressroute/expressroute-circuit-peerings.md).
- Další informace o [domény směrování ExpressRoute](../expressroute/expressroute-circuit-peerings.md#expressroute-routing-domains).
- Další informace o [umístění ExpressRoute](../expressroute/expressroute-locations.md).
- Další informace o zotavení po havárii [virtuálních počítačů Azure pomocí ExpressRoute ](azure-vm-disaster-recovery-with-expressroute.md).
