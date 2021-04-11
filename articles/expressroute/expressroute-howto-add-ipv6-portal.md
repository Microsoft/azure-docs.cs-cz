---
title: 'Azure ExpressRoute: Přidání podpory protokolu IPv6 pomocí Azure Portal'
description: Naučte se, jak přidat podporu protokolu IPv6 pro připojení k nasazením Azure pomocí Azure Portal.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 03/09/2021
ms.author: duau
ms.openlocfilehash: 7f5afc05a8d03d33366a2f76318bcf5e039d4d30
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105561657"
---
# <a name="add-ipv6-support-for-private-peering-using-the-azure-portal-preview"></a>Přidání podpory protokolu IPv6 pro privátní partnerské vztahy pomocí Azure Portal (Preview)

Tento článek popisuje, jak přidat podporu protokolu IPv6 pro připojení prostřednictvím ExpressRoute k prostředkům v Azure pomocí Azure Portal. 

> [!Note]
> Tato funkce je v tuto chvíli dostupná pro verzi Preview v [oblastech Azure s zóny dostupnosti](../availability-zones/az-region.md#azure-regions-with-availability-zones). Okruh ExpressRoute je proto možné vytvořit pomocí libovolného umístění partnerského vztahu, ale nasazení založená na protokolu IPv6, ke kterému se připojuje, musí být v oblasti s Zóny dostupnosti.

## <a name="register-for-public-preview"></a>Zaregistrujte se Public Preview
Před přidáním podpory protokolu IPv6 musíte nejdřív zaregistrovat své předplatné. Pokud se chcete zaregistrovat, spusťte následující příkazy prostřednictvím Azure PowerShell:

1.  Přihlaste se k Azure a vyberte předplatné. Spusťte tyto příkazy pro předplatné obsahující váš okruh ExpressRoute a předplatné obsahující vaše nasazení Azure (Pokud se liší).

    ```azurepowershell-interactive
    Connect-AzAccount 

    Select-AzSubscription -Subscription "<SubscriptionID or SubscriptionName>"
    ```

1. Zaregistrujte své předplatné pro Public Preview pomocí následujícího příkazu:
    ```azurepowershell-interactive
    Register-AzProviderFeature -FeatureName AllowIpv6PrivatePeering -ProviderNamespace Microsoft.Network
    ```

Váš požadavek bude potom tým ExpressRoute schválit do 2-3 pracovních dnů.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

V prohlížeči přejdete na [Azure Portal](https://portal.azure.com)a potom se přihlásíte pomocí svého účtu Azure.

## <a name="add-ipv6-private-peering-to-your-expressroute-circuit"></a>Přidání privátního partnerského vztahu IPv6 do okruhu ExpressRoute

1. [Vytvořte okruh ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) nebo přejděte k existujícímu okruhu, který chcete změnit.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/navigate-to-circuit.png" alt-text="Snímek obrazovky se seznamem okruhů ExpressRoute.":::

1. Vyberte konfiguraci **privátního** partnerského vztahu Azure.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/navigate-to-peering.png" alt-text="Snímek obrazovky se stránkou s přehledem ExpressRoute":::

1. Přidejte privátní partnerský vztah IPv6 do existující konfigurace privátního partnerského vztahu IPv4, a to tak, že v **podsítích** vyberete obojí a v novém okruhu povolíte jenom privátní partnerské vztahy IPv6. stačí vybrat IPv6. Zadejte dvojici podsítí IPv6 (/126), které vlastníte pro primární propojení a sekundární odkazy. Z každé z těchto podsítí přiřadíte směrovač první použitelná IP adresa, protože společnost Microsoft použije pro svůj směrovač druhou použitelnou IP adresu. Po zadání všech parametrů **uložte** konfiguraci partnerského vztahu.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/add-ipv6-peering.png" alt-text="Snímek obrazovky přidávání protokolu IPv6 na stránce privátního partnerského vztahu":::

1. Po úspěšném přijetí konfigurace by se měla zobrazit podobný příklad jako v následujícím příkladu.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/view-ipv6-peering.png" alt-text="Snímek IPv6 s nakonfigurovaným privátním partnerským vztahem":::

## <a name="update-your-connection-to-an-existing-virtual-network"></a>Aktualizace připojení k existující virtuální síti

Použijte následující postup, pokud máte existující prostředí prostředků Azure v oblasti s Zóny dostupnosti, kterou chcete použít pro privátní partnerské vztahy IPv6 s nástrojem.

1. Přejděte do virtuální sítě, ke které je připojený okruh ExpressRoute.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/navigate-to-vnet.png" alt-text="Snímek obrazovky se seznamem virtuálních sítí.":::

1. Přejděte na kartu **adresní prostor** a přidejte do své virtuální sítě adresní prostor IPv6. **Uložte** adresní prostor.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/add-ipv6-space.png" alt-text="Snímek obrazovky s přidáním adresního prostoru IPv6 do virtuální sítě":::

1. Přejděte na kartu **podsítě** a vyberte **GatewaySubnet**. Ověřte **Přidání adresního prostoru IPv6** a zadejte adresní prostor IPv6 pro vaši podsíť. Podsíť brány IPv6 pro bránu by měla být/64 nebo větší. Po zadání všech parametrů **uložte** konfiguraci.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/add-ipv6-gateway-space.png" alt-text="Snímek obrazovky s přidáním adresního prostoru IPv6 do podsítě":::

1. Pokud máte existující zónu redundantní bránu, přejděte do brány ExpressRoute a aktualizujte prostředek, aby se povolilo připojení IPv6. V opačném případě [vytvořte bránu virtuální sítě](expressroute-howto-add-gateway-portal-resource-manager.md) pomocí redundantní SKU zóny (ErGw1AZ, ErGw2AZ, ErGw3AZ). Pokud plánujete používat FastPath, použijte ErGw3AZ.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/refresh-gateway.png" alt-text="Snímek obrazovky s aktualizací brány":::

## <a name="create-a-connection-to-a-new-virtual-network"></a>Vytvoření připojení k nové virtuální síti

Postupujte podle následujících kroků, pokud se chcete připojit k nové sadě prostředků Azure v oblasti s Zóny dostupnosti pomocí privátního partnerského vztahu IPv6.

1. Vytvořte virtuální síť se dvěma zásobníky s adresním prostorem IPv4 i IPv6. Další informace najdete v tématu [vytvoření virtuální sítě](../virtual-network/quick-create-portal.md#create-a-virtual-network).

1. [Vytvořte podsíť brány Dual-Stack](expressroute-howto-add-gateway-portal-resource-manager.md#create-the-gateway-subnet).

1. [Vytvořte bránu virtuální sítě](expressroute-howto-add-gateway-portal-resource-manager.md#create-the-virtual-network-gateway) pomocí zóny – redundantní SKU (ErGw1AZ, ErGw2AZ, ErGw3AZ). Pokud plánujete používat FastPath, použijte ErGw3AZ (Všimněte si, že tato možnost je dostupná jenom pro okruhy pomocí ExpressRoute Direct).

1. [Připojte svoji virtuální síť k okruhu ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md).

## <a name="limitations"></a>Omezení
I když je podpora protokolu IPv6 dostupná pro připojení k nasazením v oblastech s Zóny dostupnosti, nepodporuje následující případy použití:

* Připojení k nasazením v Azure prostřednictvím neaz ExpressRoute Gateway SKU
* Připojení k nasazením v oblastech mimo AZ
* Připojení Global Reach mezi okruhy ExpressRoute
* Použití ExpressRoute s virtuální sítí WAN
* FastPath s přímými okruhy ExpressRoute
* FastPath pomocí okruhů v následujících umístěních partnerských vztahů: Dubaj
* Koexistence s VPN Gateway

## <a name="next-steps"></a>Další kroky

Pokud chcete řešit problémy s ExpressRoute, přečtěte si následující články:

* [Ověření možností připojení ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
* [Řešení potíží s výkonem sítě](expressroute-troubleshooting-network-performance.md)