---
title: 'Vytvoření brány VPN založené na směrování: portál'
titleSuffix: Azure VPN Gateway
description: Vytvoření brány VPN založené na trase pomocí portálu Azure
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 03/03/2020
ms.author: cherylmc
ms.openlocfilehash: 6156d689a29ee348f9b1974d1520eb7d186a8d8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78331345"
---
# <a name="create-a-route-based-vpn-gateway-using-the-azure-portal"></a>Vytvoření brány VPN založené na trase pomocí portálu Azure

Tento článek vám pomůže rychle vytvořit bránu Azure VPN založenou na trase pomocí portálu Azure.  Brána VPN se používá při vytváření připojení VPN k místní síti. K připojení virtuálních sítí můžete taky použít bránu VPN. 

Kroky v tomto článku vytvoří virtuální síť, podsíť, podsíť brány a bránu VPN založenou na trasách (bránu virtuální sítě). Po dokončení vytvoření brány pak můžete vytvořit připojení. Tyto kroky vyžadují předplatné Azure. Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="create-a-virtual-network"></a><a name="vnet"></a>Vytvoření virtuální sítě

[!INCLUDE [Basic Point-to-Site VNet](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="configure-and-create-the-gateway"></a><a name="gwvalues"></a>Konfigurace a vytvoření brány

V tomto kroku vytvoříte bránu virtuální sítě pro svou virtuální síť. Vytvoření brány může obvykle trvat 45 minut nebo déle, a to v závislosti na vybrané skladové jednotce (SKU) brány.

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

[!INCLUDE [Create a gateway](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

>[!NOTE]
>Skladová položka základní brány nepodporuje ověřování IKEv2 nebo RADIUS. Pokud máte v plánu, aby se klienti Macu připojuli k vaší virtuální síti, nepoužívejte základní skladovou položku.

[!INCLUDE [NSG warning](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="view-the-vpn-gateway"></a><a name="viewgw"></a>Zobrazení brány VPN

1. Po vytvoření brány přejděte na VNet1 na portálu. Brána VPN se zobrazí na stránce Přehled jako připojené zařízení.

   ![Připojená zařízení](./media/create-routebased-vpn-gateway-portal/view-connected-devices.png "Připojená zařízení")

2. V seznamu zařízení klikněte na **VNet1GW,** abyste zobrazili další informace.

   ![Zobrazit bránu VPN](./media/create-routebased-vpn-gateway-portal/view-gateway.png "Zobrazit bránu VPN")

## <a name="next-steps"></a>Další kroky

Po dokončení vytváření brány můžete vytvořit připojení mezi virtuální sítí a jinou virtuální sítí. Nebo vytvořte připojení mezi virtuální sítí a místním umístěním.

> [!div class="nextstepaction"]
> [Vytvoření připojení mezi lokalitami](vpn-gateway-howto-site-to-site-resource-manager-portal.md)<br><br>
> [Vytvoření připojení typu Point-to-Site](vpn-gateway-howto-point-to-site-resource-manager-portal.md)<br><br>
> [Vytvoření připojení k jiné virtuální síti](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
