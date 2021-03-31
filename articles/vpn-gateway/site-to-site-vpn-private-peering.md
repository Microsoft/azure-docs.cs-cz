---
title: Připojení VPN typu Site-to-site přes privátní partnerský vztah ExpressRoute
description: Tento článek vám pomůže povolit síť Site-to-Site VPN přes privátní partnerský vztah ExpressRoute, aby bylo možné šifrovat provoz.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 11/16/2020
ms.author: cherylmc
ms.openlocfilehash: 01d87bcb5697326fa87b25b20354897049900d9d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98880521"
---
# <a name="configure-a-site-to-site-vpn-connection-over-expressroute-private-peering"></a>Konfigurace připojení VPN typu Site-to-site přes privátní partnerský vztah ExpressRoute

Můžete nakonfigurovat síť VPN typu Site-to-site k bráně virtuální sítě přes privátní partnerský vztah ExpressRoute pomocí IP adresy RFC 1918. Tato konfigurace přináší následující výhody:

* Provoz přes privátní partnerský vztah je zašifrovaný.

* Uživatelé připojení typu Point-to-site, kteří se připojují k bráně virtuální sítě, můžou k přístupu k místním prostředkům používat ExpressRoute (prostřednictvím tunelového propojení typu Site-to-site).

>[!NOTE]
>Tato funkce je podporována pouze u bran redundantních v zóně. Například VpnGw1AZ, VpnGw2AZ atd.
>

Pro dokončení této konfigurace ověřte, že splňujete následující požadavky:

* Máte funkční okruh ExpressRoute, který se odkazuje na virtuální síť, ve které je (nebo bude) vytvořena brána sítě VPN.

* Přes okruh ExpressRoute můžete ve virtuální síti dosáhnout prostředků přes RFC1918 (soukromou) IP adresu.

## <a name="routing"></a><a name="routing"></a>Směrování

**Obrázek 1** ukazuje příklad připojení VPN přes privátní partnerský vztah ExpressRoute. V tomto příkladu se zobrazí síť v místní síti, která je připojená k bráně VPN centra Azure pomocí privátního partnerského vztahu ExpressRoute. Důležitým aspektem této konfigurace je směrování mezi místními sítěmi a Azure prostřednictvím cest ExpressRoute a VPN.

Obrázek 1

:::image type="content" source="media/site-to-site-vpn-private-peering/routing.png" alt-text="Obrázek 1":::

Navázání připojení je jednoduché:

1. Navažte připojení ExpressRoute pomocí okruhu ExpressRoute a privátního partnerského vztahu.

1. Pomocí kroků v tomto článku navažte připojení k síti VPN.

### <a name="traffic-from-on-premises-networks-to-azure"></a>Provoz z místních sítí do Azure

V případě provozu z místních sítí do Azure jsou předpony Azure inzerovány prostřednictvím protokolu BGP privátního partnerského vztahu ExpressRoute a protokolu BGP VPN. Výsledkem jsou dvě síťové trasy (cesty) směrem k Azure z místních sítí:

• Jedna síťová trasa přes cestu chráněnou protokolem IPsec.

• Jedna síť se směruje přímo přes ExpressRoute bez ochrany IPsec.

Pokud chcete pro komunikaci použít šifrování, musíte se ujistit, že pro síť připojenou k síti VPN na **obrázku 1** jsou upřednostňovány trasy Azure přes místní BRÁNu VPN prostřednictvím přímé ExpressRoute cesty.

### <a name="traffic-from-azure-to-on-premises-networks"></a>Provoz z Azure do místních sítí

Stejný požadavek se vztahuje na provoz z Azure do místních sítí. Chcete-li zajistit, aby byla cesta protokolu IPsec upřednostňována přes přímou cestu ExpressRoute (bez protokolu IPsec), máte dvě možnosti:

• **Inzerovat konkrétnější předpony v relaci protokolu BGP sítě VPN pro síť připojenou** k síti VPN. Můžete inzerovat větší rozsah, který zahrnuje síť připojenou k síti VPN přes privátní partnerský vztah ExpressRoute, a pak v relaci protokolu BGP sítě VPN více konkrétních rozsahů. Můžete například inzerovat 10.0.0.0/16 přes ExpressRoute a 10.0.1.0/24 prostřednictvím sítě VPN.

• **Inzerujte nesouvislé předpony pro VPN a ExpressRoute**. Pokud jsou síťové rozsahy připojené k síti VPN nesouvislé z jiných sítí propojených s ExpressRoute, můžete předpony inzerovat v relacích VPN a ExpressRoute BGP. Můžete například inzerovat 10.0.0.0/24 přes ExpressRoute a 10.0.1.0/24 prostřednictvím sítě VPN.

V obou těchto příkladech pošle Azure provoz do 10.0.1.0/24 přes připojení VPN, nikoli přímo přes ExpressRoute bez ochrany VPN.

>[!Warning]
>Pokud budete stejné předpony inzerovat přes ExpressRoute i připojení VPN, >Azure použije cestu ExpressRoute přímo bez ochrany VPN.
>

## <a name="portal-steps"></a><a name="portal"></a>Kroky portálu

1. Nakonfigurujte připojení typu Site-to-site. Postup najdete v článku věnovaném [konfiguraci site-to-site](./tutorial-site-to-site-portal.md) . Ujistěte se, že jste pro bránu vybrali SKU redundantní brány v zóně. 

   Záložní sklady v zóně mají na konci SKU "AZ". Například **VpnGw1AZ**. Redundantní brány v zóně jsou dostupné jenom v oblastech, kde je služba zóny dostupnosti k dispozici. Informace o oblastech, ve kterých podporujeme zóny dostupnosti, najdete v tématu [oblasti, které podporují zóny dostupnosti](../availability-zones/az-region.md).

   :::image type="content" source="media/site-to-site-vpn-private-peering/gateway.png" alt-text="Privátní IP adresy brány":::
1. Povolte v bráně privátní IP adresy. Vyberte **Konfigurace** a pak nastavte **soukromé IP adresy brány** na **povoleno**. Vyberte **Uložit** a uložte tak provedené změny.
1. Na stránce **Přehled** vyberte **Zobrazit další** a ZOBRAZTE tak soukromou IP adresu. Zapište tyto informace pro pozdější použití v krocích konfigurace.

   :::image type="content" source="media/site-to-site-vpn-private-peering/gateway-overview.png" alt-text="Stránka přehledu" lightbox="media/site-to-site-vpn-private-peering/gateway-overview.png":::
1. Pokud chcete pro připojení povolit **používání privátní IP adresy Azure** , vyberte  **Konfigurace**. Nastavte **použít privátní IP adresu Azure** na **povoleno** a pak vyberte **Uložit**.

   :::image type="content" source="media/site-to-site-vpn-private-peering/connection.png" alt-text="Privátní IP adresy brány – povoleno":::
1. Z brány firewall pomocí příkazového testu otestujte privátní IP adresu, kterou jste vytvořili v kroku 3. Privátní IP adresa by měla být dosažitelná v rámci privátního partnerského vztahu ExpressRoute.
1. Tato privátní IP adresa se používá jako Vzdálená IP adresa na místní bráně firewall k navázání tunelového propojení typu Site-to-site přes privátní partnerský vztah ExpressRoute.

## <a name="powershell-steps"></a><a name="powershell"></a>Kroky PowerShellu

1. Nakonfigurujte připojení typu Site-to-site. Postup najdete v článku [Konfigurace sítě Site-to-Site VPN](./tutorial-site-to-site-portal.md) . Ujistěte se, že jste pro bránu vybrali SKU redundantní brány v zóně. Záložní sklady v zóně mají na konci SKU "AZ". Například VpnGw1AZ.
1. Pomocí následujících příkazů PowerShellu nastavte příznak pro použití privátní IP adresy v bráně:

   ```azurepowershell-interactive
   $Gateway = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroup <name of resource group>

   Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway -EnablePrivateIpAddress $true
   ```

   Měla by se zobrazit veřejná a privátní IP adresa. Zapište IP adresu v části "TunnelIpAddresses" výstupu. Tyto informace budete používat v pozdějším kroku.
1. Pomocí následujícího příkazu prostředí PowerShell nastavte připojení k použití privátní IP adresy:

   ```azurepowershell-interactive
   $Connection = get-AzVirtualNetworkGatewayConnection -Name <name of the connection> -ResourceGroupName <name of resource group>

   Set-AzVirtualNetworkGatewayConnection --VirtualNetworkGatewayConnection $Connection -UseLocalAzureIpAddress $true
   ```
1. Z brány firewall pomocí příkazového testu otestujte privátní IP adresu, kterou jste napsali v kroku 2. Měl by být dosažitelný prostřednictvím privátního partnerského vztahu ExpressRoute.
1. Tato privátní IP adresa se používá jako Vzdálená IP adresa na místní bráně firewall k navázání tunelového propojení typu Site-to-site přes privátní partnerský vztah ExpressRoute.

## <a name="next-steps"></a>Další kroky

Další informace o VPN Gateway najdete v tématu [co je VPN Gateway?](vpn-gateway-about-vpngateways.md)