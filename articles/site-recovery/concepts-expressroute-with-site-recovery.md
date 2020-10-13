---
title: O použití ExpressRoute s Azure Site Recovery
description: Popisuje, jak používat Azure ExpressRoute se službou Azure Site Recovery pro zotavení po havárii a pro migraci.
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/13/2019
ms.author: mayg
ms.openlocfilehash: 68671a98c7703514af11de2f8c505110129fe713
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91398642"
---
# <a name="azure-expressroute-with-azure-site-recovery"></a>Azure ExpressRoute s Azure Site Recovery

Microsoft Azure ExpressRoute umožňuje rozšířit vaše místní sítě do cloudu Microsoftu přes soukromé připojení zajišťované poskytovatelem připojení. V ExpressRoute můžete vytvořit připojení ke cloudovým službám, jako je Microsoft Azure, Microsoft 365 a Dynamics 365.

Tento článek popisuje, jak můžete použít Azure ExpressRoute s Azure Site Recovery pro zotavení po havárii a migraci.

## <a name="expressroute-circuits"></a>Okruhy ExpressRoute

Okruh ExpressRoute představuje logické propojení mezi vaší místní infrastrukturou a cloudovou službou Microsoftu prostřednictvím poskytovatele připojení. Můžete objednat více okruhů ExpressRoute. Každý okruh může být ve stejné nebo jiné oblasti a může být připojen k vašemu prostoru prostřednictvím různých poskytovatelů připojení. Další informace o okruhech ExpressRoute [najdete tady](../expressroute/expressroute-circuit-peerings.md).

K okruhu ExpressRoute je přidruženo více domén směrování. Další informace o a porovnání domén směrování ExpressRoute [najdete tady](../expressroute/expressroute-circuit-peerings.md#peeringcompare).

## <a name="on-premises-to-azure-replication-with-expressroute"></a>Replikace z místního prostředí do Azure pomocí ExpressRoute

Azure Site Recovery umožňuje zotavení po havárii a migraci do Azure pro místní [virtuální počítače Hyper-V](hyper-v-azure-architecture.md), [virtuální počítače VMware](vmware-azure-architecture.md)a [fyzické servery](physical-azure-architecture.md). Pro všechny místní scénáře do Azure se data replikace odesílají do účtu Azure Storage a ukládají se do něj. Během replikace neplatíte žádné poplatky za virtuální počítače. Když spustíte převzetí služeb při selhání do Azure, Site Recovery automaticky vytvoří virtuální počítače Azure s IaaS.

Site Recovery replikuje data na účet Azure Storage nebo replikovaný disk repliky v cílové oblasti Azure prostřednictvím veřejného koncového bodu. Pokud chcete pro Site Recovery provozu replikace použít službu ExpressRoute, můžete využít [partnerský vztah Microsoftu](../expressroute/expressroute-circuit-peerings.md#microsoftpeering) nebo stávající [veřejný partnerský vztah](../expressroute/about-public-peering.md) (nepoužívané pro nová vytvoření). Partnerský vztah Microsoftu je doporučená doména směrování pro replikaci. Všimněte si, že replikace není podporovaná přes privátní partnerský vztah.

Zajistěte, aby byly splněny také [požadavky na síť](vmware-azure-configuration-server-requirements.md#network-requirements) pro konfigurační server. Konfigurační server vyžaduje k orchestraci replikace Site Recovery připojení ke konkrétním adresám URL. ExpressRoute nelze použít pro toto připojení. 

V případě, že používáte proxy v místním prostředí a chcete pro provoz replikace použít ExpressRoute, musíte nakonfigurovat seznam obcházení proxy serveru na konfiguračním serveru a procesových serverech. Postupujte následovně:

- Stáhněte si z [tohoto místa](https://aka.ms/PsExec) nástroj PsExec pro přístup k systémovému kontextu uživatele.
- Spusťte aplikaci Internet Explorer v kontextu uživatele systému spuštěním následujícího příkazového řádku PsExec-s-i "%programfiles%\Internet Explorer\iexplore.exe".
- Přidání nastavení proxy serveru v IE
- V seznamu vynechat přidejte adresu URL úložiště Azure *. blob.core.windows.net.

Tím se zajistí, že se během komunikace prostřednictvím proxy serveru procházejí jenom přenosy dat replikace přes ExpressRoute.

Po převzetí služeb při selhání virtuálních počítačů nebo serverů do služby Azure Virtual Network můžete k nim přistupovat pomocí [privátního partnerského vztahu](../expressroute/expressroute-circuit-peerings.md#privatepeering). 

Kombinovaný scénář je reprezentován v následujícím diagramu: ![ místní – Azure s ExpressRoute](./media/concepts-expressroute-with-site-recovery/site-recovery-with-expressroute.png)

## <a name="azure-to-azure-replication-with-expressroute"></a>Replikace z Azure do Azure pomocí ExpressRoute

Azure Site Recovery umožňuje zotavení po havárii [virtuálních počítačů Azure](azure-to-azure-architecture.md). V závislosti na tom, jestli vaše virtuální počítače Azure používají [azure Managed disks](../virtual-machines/managed-disks-overview.md), se data replikace odesílají na účet Azure Storage nebo na spravovaný disk repliky v cílové oblasti Azure. I když jsou koncové body replikace veřejné, provoz replikace pro replikaci virtuálních počítačů Azure ve výchozím nastavení neprojde internetem, bez ohledu na to, ve které oblasti Azure ve zdrojové virtuální síti existuje. Výchozí systémovou trasu Azure pro předponu adresy 0.0.0.0/0 můžete přepsat [vlastní trasou](../virtual-network/virtual-networks-udr-overview.md#custom-routes) a přesměrováním provozu virtuálního počítače do místního síťového virtuálního zařízení (síťové virtuální zařízení), ale tato konfigurace se nedoporučuje pro Site Recovery replikaci. Pokud používáte vlastní trasy, měli byste ve virtuální síti [vytvořit koncový bod služby virtuální sítě](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) pro úložiště, aby provoz replikace neopouští hranice Azure.

Pro zotavení po havárii virtuálního počítače Azure se ve výchozím nastavení ExpressRoute nevyžaduje pro replikaci. Po převzetí služeb virtuálních počítačů do cílové oblasti Azure získáte přístup k nim pomocí [privátního partnerského vztahu](../expressroute/expressroute-circuit-peerings.md#privatepeering). Počítejte s tím, že ceny za přenos dat platí bez ohledu na režim replikace dat napříč oblastmi Azure.

Pokud už používáte ExpressRoute k připojení z místního datacentra k virtuálním počítačům Azure ve zdrojové oblasti, můžete v cílové oblasti převzetí služeb při selhání naplánovat opětovné vytvoření připojení ExpressRoute. Stejný okruh ExpressRoute můžete použít pro připojení k cílové oblasti prostřednictvím nového připojení k virtuální síti, nebo využít samostatný okruh ExpressRoute a připojení k zotavení po havárii. [Zde](azure-vm-disaster-recovery-with-expressroute.md#fail-over-azure-vms-when-using-expressroute)jsou popsány různé možné scénáře.

Virtuální počítače Azure můžete replikovat do jakékoli oblasti Azure v rámci stejného geografického clusteru, jak je popsáno [zde](../site-recovery/azure-to-azure-support-matrix.md#region-support). Pokud vybraná cílová oblast Azure není ve stejné geopolitické oblasti jako zdroj, možná budete muset povolit ExpressRoute Premium. Další podrobnosti najdete v [ExpressRoute umístěních](../expressroute/expressroute-locations.md) a [cenách ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute/).

## <a name="next-steps"></a>Další kroky
- Přečtěte si další informace o [okruhech ExpressRoute](../expressroute/expressroute-circuit-peerings.md).
- Přečtěte si další informace o [doménách směrování ExpressRoute](../expressroute/expressroute-circuit-peerings.md#peeringcompare).
- Přečtěte si další informace o [ExpressRoute umístěních](../expressroute/expressroute-locations.md).
- Přečtěte si další informace o zotavení po havárii [virtuálních počítačů Azure pomocí ExpressRoute](azure-vm-disaster-recovery-with-expressroute.md).
