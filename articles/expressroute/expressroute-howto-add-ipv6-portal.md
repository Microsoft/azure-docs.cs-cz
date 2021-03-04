---
title: 'Azure ExpressRoute: Přidání podpory protokolu IPv6 pomocí Azure Portal'
description: Naučte se, jak přidat podporu protokolu IPv6 pro připojení k nasazením Azure pomocí Azure Portal.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 2/9/2021
ms.author: duau
ms.openlocfilehash: ef6ea9017a9aaa98e153df0d67f0b54fe5a2b64d
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102124155"
---
# <a name="add-ipv6-support-for-private-peering-using-the-azure-portal-preview"></a>Přidání podpory protokolu IPv6 pro privátní partnerské vztahy pomocí Azure Portal (Preview)

Tento článek popisuje, jak přidat podporu protokolu IPv6 pro připojení prostřednictvím ExpressRoute k prostředkům v Azure pomocí Azure Portal. 

> [!Note]
> Tato funkce je v tuto chvíli dostupná pro verzi Preview v [oblastech Azure s zóny dostupnosti](https://docs.microsoft.com/azure/availability-zones/az-region#azure-regions-with-availability-zones). Okruh ExpressRoute je proto možné vytvořit pomocí libovolného umístění partnerského vztahu, ale nasazení založená na protokolu IPv6, ke kterému se připojuje, musí být v oblasti s Zóny dostupnosti.

## <a name="register-for-public-preview"></a>Zaregistrujte se Public Preview
Před přidáním podpory protokolu IPv6 musíte nejdřív zaregistrovat své předplatné. Pokud se chcete zaregistrovat, udělejte to prosím pomocí Azure PowerShell:
1.  Přihlaste se k Azure a vyberte předplatné. Musíte to udělat u předplatného obsahujícího okruh ExpressRoute a předplatné, které obsahuje vaše nasazení Azure (Pokud se liší).

    ```azurepowershell-interactive
    Connect-AzAccount 

    Select-AzSubscription -Subscription "<SubscriptionID or SubscriptionName>"
    ```

2. Zaregistrujte své předplatné pro Public Preview pomocí následujícího příkazu:
    ```azurepowershell-interactive
    Register-AzProviderFeature -FeatureName AllowIpv6PrivatePeering -ProviderNamespace Microsoft.Network
    ```

Váš požadavek bude potom tým ExpressRoute schválit do 2-3 pracovních dnů.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

V prohlížeči přejdete na [Azure Portal](https://portal.azure.com)a potom se přihlásíte pomocí svého účtu Azure.

## <a name="add-ipv6-private-peering-to-your-expressroute-circuit"></a>Přidání privátního partnerského vztahu IPv6 do okruhu ExpressRoute

1. [Vytvořte okruh ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-howto-circuit-portal-resource-manager) nebo přejděte k existujícímu okruhu, který chcete změnit.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/navigate-to-circuit.png" alt-text="Přejít na okruh":::

2. Vyberte konfiguraci **privátního** partnerského vztahu Azure.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/navigate-to-peering.png" alt-text="Přejít na partnerský vztah":::

3. Přidejte privátní partnerský vztah IPv6 do existující konfigurace privátního partnerského vztahu IPv4, a to tak, že v **podsítích** vyberete obojí a v novém okruhu povolíte jenom privátní partnerské vztahy IPv6. stačí vybrat IPv6. Zadejte dvojici podsítí IPv6 (/126), které vlastníte pro primární propojení a sekundární odkazy. Z každé z těchto podsítí přiřadíte směrovač první použitelná IP adresa, protože společnost Microsoft použije pro svůj směrovač druhou použitelnou IP adresu. Po zadání všech parametrů **uložte** konfiguraci partnerského vztahu.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/add-ipv6-peering.png" alt-text="Přidat privátní partnerský vztah IPv6":::

4. Po úspěšném přijetí konfigurace by se měla zobrazit podobný příklad jako v následujícím příkladu.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/view-ipv6-peering.png" alt-text="Zobrazení privátního partnerského vztahu IPv6":::

## <a name="update-your-connection-to-an-existing-virtual-network"></a>Aktualizace připojení k existující virtuální síti

Použijte následující postup, pokud máte existující prostředí prostředků Azure v oblasti s Zóny dostupnosti, kterou chcete použít pro privátní partnerské vztahy IPv6 s nástrojem.

1. Přejděte do virtuální sítě, ke které je připojený okruh ExpressRoute.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/navigate-to-vnet.png" alt-text="Přejít na virtuální síť":::

2. Přejděte na kartu **adresní prostor** a přidejte do své virtuální sítě adresní prostor IPv6. **Uložte** adresní prostor.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/add-ipv6-space.png" alt-text="Přidat adresní prostor IPv6":::

3. Přejděte na kartu **podsítě** a vyberte **GatewaySubnet**. Ověřte **Přidání adresního prostoru IPv6** a zadejte adresní prostor IPv6 pro vaši podsíť. Podsíť brány IPv6 pro bránu by měla být/64 nebo větší. Po zadání všech parametrů **uložte** konfiguraci.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/add-ipv6-gateway-space.png" alt-text="Přidat adresní prostor IPv6 do podsítě":::

4. Pokud máte existující zónu redundantní bránu, přejděte do brány ExpressRoute a aktualizujte prostředek, aby se povolilo připojení IPv6. V opačném případě [vytvořte bránu virtuální sítě](https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-portal-resource-manager) pomocí redundantní SKU zóny (ErGw1AZ, ErGw2AZ, ErGw3AZ). Pokud plánujete používat FastPath, použijte ErGw3AZ.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/refresh-gateway.png" alt-text="Aktualizace brány":::

## <a name="create-a-connection-to-a-new-virtual-network"></a>Vytvoření připojení k nové virtuální síti

Postupujte podle následujících kroků, pokud se chcete připojit k nové sadě prostředků Azure v oblasti s Zóny dostupnosti pomocí privátního partnerského vztahu IPv6.

1. Vytvořte virtuální síť se dvěma zásobníky s adresním prostorem IPv4 i IPv6. Další informace najdete v tématu [vytvoření virtuální sítě](https://docs.microsoft.com/azure/virtual-network/quick-create-portal#create-a-virtual-network).

2. [Vytvořte podsíť brány Dual-Stack](https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-portal-resource-manager#create-the-gateway-subnet).

3. [Vytvořte bránu virtuální sítě](https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-portal-resource-manager#create-the-virtual-network-gateway) pomocí zóny – redundantní SKU (ErGw1AZ, ErGw2AZ, ErGw3AZ). Pokud máte v úmyslu používat FastPath, použijte ErGw3AZ (Všimněte si, že je k dispozici pouze pro okruhy s přímým přístupem ExpressRoute).

4. [Připojte svoji virtuální síť k okruhu ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager).

## <a name="limitations"></a>Omezení
I když je podpora protokolu IPv6 dostupná pro připojení k nasazením v oblastech s Zóny dostupnosti, nepodporuje následující případy použití:

* Připojení k nasazením v Azure prostřednictvím neaz ExpressRoute Gateway SKU
* Připojení k nasazením v oblastech mimo AZ
* Připojení Global Reach mezi okruhy ExpressRoute
* Použití ExpressRoute s virtuální sítí WAN
* FastPath s přímými okruhy ExpressRoute
* Koexistence s VPN Gateway

## <a name="next-steps"></a>Další kroky

Pokud chcete řešit problémy s ExpressRoute, přečtěte si následující články:

* [Ověření možností připojení ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
* [Řešení potíží s výkonem sítě](expressroute-troubleshooting-network-performance.md)
