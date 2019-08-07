---
title: 'Vytvořte bránu sítě VPN založenou na trasách: Azure Portal | Microsoft Docs'
description: Vytvoření VPN Gateway založeného na směrování pomocí Azure Portal
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 08/02/2019
ms.author: cherylmc
ms.openlocfilehash: 2a04c0fa2d92514103377c2aef420290d1bdd057
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/05/2019
ms.locfileid: "68781170"
---
# <a name="create-a-route-based-vpn-gateway-using-the-azure-portal"></a>Vytvoření brány sítě VPN založené na trasách pomocí Azure Portal

Tento článek vám pomůže rychle vytvořit bránu Azure VPN založenou na směrování pomocí Azure Portal.  Brána sítě VPN se používá při vytváření připojení VPN k místní síti. K připojení virtuální sítě můžete použít taky bránu VPN. 

Kroky v tomto článku vytvoří virtuální síť, podsíť, podsíť brány a bránu VPN založenou na směrování (bránu virtuální sítě). Po dokončení vytváření brány můžete vytvořit připojení. Tyto kroky vyžadují předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="vnet"></a>Vytvoření virtuální sítě

[!INCLUDE [create-gateway](../../includes/vpn-gateway-create-virtual-network-portal-include.md)]

## <a name="gwsubnet"></a>Přidat podsíť brány

[!INCLUDE [gateway subnet](../../includes/vpn-gateway-add-gateway-subnet-portal-include.md)]

## <a name="gwvalues"></a>Konfigurace a vytvoření brány

[!INCLUDE [create-gateway](../../includes/vpn-gateway-add-gw-p2s-rm-portal-include.md)]

>[!NOTE]
>SKU brány úrovně Basic nepodporuje ověřování IKEv2 nebo RADIUS. Pokud plánujete, že se klienti se systémem Mac připojí k vaší virtuální síti, nepoužívejte základní SKU.

## <a name="viewgw"></a>Zobrazit bránu VPN

1. Po vytvoření brány na portálu přejděte na VNet1. Brána sítě VPN se zobrazí na stránce Přehled jako připojené zařízení.

   ![Připojená zařízení](./media/create-routebased-vpn-gateway-portal/view-connected-devices.png "Připojená zařízení")

2. V seznamu zařízení klikněte na **VNet1GW** . zobrazí se další informace.

   ![Zobrazit bránu VPN Gateway](./media/create-routebased-vpn-gateway-portal/view-gateway.png "Zobrazit bránu VPN Gateway")

## <a name="next-steps"></a>Další postup

Po vytvoření brány můžete vytvořit připojení mezi vaší virtuální sítí a jinou virtuální sítí. Nebo vytvořte připojení mezi vaší virtuální sítí a místním umístěním.

> [!div class="nextstepaction"]
> [Vytvoření připojení typu Site-to-site](vpn-gateway-howto-site-to-site-resource-manager-portal.md)<br><br>
> [Vytvoření připojení typu Point-to-site](vpn-gateway-howto-point-to-site-resource-manager-portal.md)<br><br>
> [Vytvoření připojení k jiné virtuální síti](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
