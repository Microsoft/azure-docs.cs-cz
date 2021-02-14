---
title: Jak nakonfigurovat OpenVPN v Azure VPN Gateway
description: Naučte se používat PowerShell k povolení protokolu OpenVPN v Azure VPN Gateway pro prostředí Point-to-site.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/05/2021
ms.author: cherylmc
ms.openlocfilehash: 4b48e98ab35b620030b15165d4c9341c0f11b440
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100393271"
---
# <a name="configure-openvpn-for-azure-point-to-site-vpn-gateway"></a>Konfigurace OpenVPN pro Azure Point-to-Site VPN Gateway

Tento článek vám pomůže nastavit **Protokol OpenVPN®** v Azure VPN Gateway. Můžete použít buď portál, nebo pokyny pro PowerShell.

## <a name="prerequisites"></a>Požadavky

* Článek předpokládá, že již máte funkční prostředí typu Point-to-site. Pokud to neuděláte, vytvořte ho pomocí jedné z následujících metod.

  * [Portál – vytvoření Point-to-site](vpn-gateway-howto-point-to-site-resource-manager-portal.md)

  * [PowerShell – vytvoření Point-to-site](vpn-gateway-howto-point-to-site-rm-ps.md)

* Ověřte, že vaše brána VPN nepoužívá základní SKU. Základní skladová položka není pro OpenVPN podporovaná.

## <a name="portal"></a>Portál

1. Na portálu přejděte na **bránu virtuální sítě – > konfiguraci Point-to-site**.
1. V poli **Typ tunelu** vyberte **OpenVPN (SSL)** z rozevíracího seznamu.

   :::image type="content" source="./media/vpn-gateway-howto-openvpn/portal.png" alt-text="Z rozevíracího seznamu vyberte OpenVPN SSL.":::
1. Uložte změny a pokračujte dalšími **kroky**.

## <a name="enable-openvpn-on-your-gateway-using-powershell"></a>Povolte v bráně OpenVPN pomocí PowerShellu.

1. V bráně povolte OpenVPN pomocí následujícího příkladu:

   ```azurepowershell-interactive
   $gw = Get-AzVirtualNetworkGateway -ResourceGroupName $rgname -name $name
   Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientProtocol OpenVPN
   ```
1. Pokračujte **dalšími kroky**.

## <a name="next-steps"></a>Další kroky

Pokud chcete nakonfigurovat klienty pro OpenVPN, přečtěte si téma [Konfigurace klientů OpenVPN](vpn-gateway-howto-openvpn-clients.md).

**"OpenVPN" je ochranná známka společnosti OpenVPN Inc.**
