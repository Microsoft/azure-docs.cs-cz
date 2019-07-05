---
title: Informace o používání Azure ExpressRoute pomocí Azure Site Recovery pro zotavení po havárii a migrace | Dokumentace Microsoftu
description: Popisuje, jak pomocí služby Azure Site Recovery pro zotavení po havárii a migraci do Azure ExpressRoute.
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 6/27/2019
ms.author: mayg
ms.openlocfilehash: 35fa26112a6026ab05bd59b38621de7ee802c715
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491900"
---
# <a name="azure-expressroute-with-azure-site-recovery"></a>Azure ExpressRoute pomocí Azure Site Recovery

Microsoft Azure ExpressRoute umožňuje rozšířit vaše místní sítě do cloudu Microsoftu přes soukromé připojení zajišťované poskytovatelem připojení. Pomocí ExpressRoute může vytvořit připojení ke cloudovým službám Microsoftu, jako je například Microsoft Azure, Office 365 nebo Dynamics 365.

Tento článek popisuje, jak můžete pomocí Azure ExpressRoute pomocí Azure Site Recovery pro zotavení po havárii a migrace.

## <a name="expressroute-circuits"></a>Okruhy ExpressRoute

Okruh ExpressRoute představuje logické propojení mezi vaši infrastrukturou v místním a cloudovým službám Microsoftu prostřednictvím poskytovatele připojení. Můžete si objednat víc okruhů ExpressRoute. Každý okruh může být v jedné nebo několika oblastech a jde připojit k místním prostřednictvím poskytovatelů jiné připojení. Další informace o okruhy ExpressRoute [tady](../expressroute/expressroute-circuit-peerings.md).

Okruh ExpressRoute má víc domén směrování s ním spojená. Další informace a porovnání domény směrování ExpressRoute [tady](../expressroute/expressroute-circuit-peerings.md#peeringcompare).

## <a name="on-premises-to-azure-replication-with-expressroute"></a>Místní replikace Azure pomocí ExpressRoute

Azure Site Recovery nabízí zotavení po havárii a migraci do Azure pro místní [virtuálních počítačů Hyper-V](hyper-v-azure-architecture.md), [virtuálních počítačů VMware](vmware-azure-architecture.md), a [fyzických serverů](physical-azure-architecture.md). Data replikace pro všechny místní do scénáře služby Azure, se odesílají a ukládají v účtu služby Azure Storage. Během replikace neplatíte žádné poplatky za virtuální počítače. Při spuštění převzetí služeb při selhání do Azure Site Recovery automaticky vytvoří virtuální počítače Azure IaaS.

Site Recovery replikuje data do účtu Azure Storage nebo repliky v cílové oblasti Azure se spravovanými disky přes veřejný koncový bod. Pokud chcete použít ExpressRoute pro provoz replikace Site Recovery, můžete využít [partnerský vztah Microsoftu](../expressroute/expressroute-circuit-peerings.md#microsoftpeering) nebo z existujícího [veřejného partnerského vztahu](../expressroute/expressroute-circuit-peerings.md#publicpeering) (zastaralé pro vytvoření nového). Partnerský vztah Microsoftu je doporučené směrování domény pro replikaci. Všimněte si, že je replikace se nepodporuje přes privátní partnerský vztah.

Ujistěte se, že [síťové požadavky](vmware-azure-configuration-server-requirements.md#network-requirements) pro konfigurační Server jsou splněny. Konfigurační Server vyžaduje připojení ke konkrétní adresy URL pro orchestraci replikace Site Recovery. ExpressRoute nelze použít pro toto připojení. 

V případě použití proxy serveru na místní a chcete použít ExpressRoute pro provoz replikace, budete muset nakonfigurovat seznam obcházení proxy serveru na konfigurační Server a procesových serverů. Postupujte následovně:

- Stáhněte si nástroj PsExec z [tady](https://aka.ms/PsExec) pro přístup k systému uživatelský kontext.
- Spusťte aplikaci Internet Explorer v kontextu uživatele systému spuštěním následujícího příkazového řádku psexec -s -i "%programfiles%\Internet Explorer\iexplore.exe"
- Přidat nastavení proxy serveru v Internet Exploreru
- V seznamu obcházení přidejte adresu URL služby Azure storage *. blob.core.windows.net

Tím se zajistí, že pouze provoz replikace prochází přes ExpressRoute, ale můžete komunikaci přes proxy server.

Po virtuálních počítačů nebo serverů převzetí služeb při selhání ke službě Azure virtual network, k nim můžete přistupovat pomocí [soukromého partnerského vztahu](../expressroute/expressroute-circuit-peerings.md#privatepeering). 

Kombinované scénáře je reprezentován v následujícím diagramu: ![V místní – Azure pomocí ExpressRoute](./media/concepts-expressroute-with-site-recovery/site-recovery-with-expressroute.png)

## <a name="azure-to-azure-replication-with-expressroute"></a>Replikace z Azure do Azure pomocí ExpressRoute

Azure Site Recovery nabízí zotavení po havárii [virtuálních počítačů Azure](azure-to-azure-architecture.md). V závislosti na tom, jestli vaše virtuální počítače Azure pomocí [Azure Managed Disks](../virtual-machines/windows/managed-disks-overview.md), replikace se odešlou na účet služby Azure Storage nebo repliky v cílové oblasti Azure se spravovanými disky. I když jsou veřejné koncové body replikace, provoz replikace pro replikaci virtuálních počítačů Azure, ve výchozím nastavení, neprocházejí přes Internet, bez ohledu na to, které oblasti Azure zdrojová virtuální síť existuje v. Můžete přepsat Azure výchozí systémovou trasu pro předponu adresy 0.0.0.0/0 s [vlastní trasy](../virtual-network/virtual-networks-udr-overview.md#custom-routes) a přesměrovat provoz virtuálního počítače místní síťové virtuální zařízení (NVA), ale tato konfigurace se nedoporučuje pro Site Recovery replikace. Pokud používáte vlastní trasy, měli byste [vytvořit koncový bod služby virtuální sítě](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) ve vaší virtuální sítě pro "Úložiště" tak, aby provoz replikace, nenechává hranice Azure.

Pro zotavení po havárii virtuálního počítače Azure ve výchozím nastavení, ExpressRoute se nevyžaduje pro replikaci. Po virtuálním počítačům převzetí služeb při selhání do cílové oblasti Azure, k nim můžete přistupovat pomocí [soukromého partnerského vztahu](../expressroute/expressroute-circuit-peerings.md#privatepeering).

Pokud už používáte ExpressRoute pro připojení z místního datacentra do virtuálních počítačů Azure na zdrojové oblasti, můžete naplánovat pro obnovujete připojení ExpressRoute v cílové oblasti převzetí služeb při selhání. Stejnému okruhu ExpressRoute můžete použít pro připojení k cílové oblasti novou virtuální síťové připojení nebo využití samostatné okruh ExpressRoute a připojení pro zotavení po havárii. Různé možné scénáře jsou popsané [tady](azure-vm-disaster-recovery-with-expressroute.md#fail-over-azure-vms-when-using-expressroute).

Můžete replikovat virtuální počítače Azure do libovolné oblasti Azure v rámci stejné zeměpisné clusteru jako podrobné [tady](../site-recovery/azure-to-azure-support-matrix.md#region-support). Pokud není zvolené cílové oblasti Azure v rámci stejné geopolitické oblasti jako zdroj, může být potřeba povolit ExpressRoute Premium. Více informací najdete v [umístění ExpressRoute](../expressroute/expressroute-locations.md#azure-regions-to-expressroute-locations-within-a-geopolitical-region) a [ceny za ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute/).

## <a name="next-steps"></a>Další postup
- Další informace o [okruhy ExpressRoute](../expressroute/expressroute-circuit-peerings.md).
- Další informace o [domény směrování ExpressRoute](../expressroute/expressroute-circuit-peerings.md#peeringcompare).
- Další informace o [umístění ExpressRoute](../expressroute/expressroute-locations.md).
- Další informace o zotavení po havárii [virtuálních počítačů Azure pomocí ExpressRoute](azure-vm-disaster-recovery-with-expressroute.md).
