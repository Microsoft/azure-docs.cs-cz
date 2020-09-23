---
title: 'Azure VPN Gateway: Správa relací VPN typu Point-to-site'
description: Tento článek vám pomůže zobrazit a odpojit relace VPN typu Point-to-site.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 08/26/2020
ms.author: cherylmc
ms.openlocfilehash: 3c9023e23795fd51e5519727a0ab174dc6f75830
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90935976"
---
# <a name="point-to-site-vpn-session-management"></a>Správa relací VPN typu Point-to-site

Brány virtuální sítě Azure poskytují snadný způsob, jak zobrazit a odpojit aktuální relace VPN typu Point-to-site. Tento článek vám pomůže zobrazit a odpojit aktuální relace.

>[!NOTE]
>Stav relace se aktualizuje každých 5 minut. Neaktualizuje se hned.
>

## <a name="portal"></a>Portál

Zobrazení a odpojení relace na portálu:

1. Přejděte na bránu VPN.
1. V části **monitorování** vyberte **relace Point-to-site**.

   :::image type="content" source="./media/p2s-session-management/portal.png" alt-text="Příklad portálu":::
1. Všechny aktuální relace můžete zobrazit v WindowPane.
1. Jako relaci, kterou chcete odpojit, vyberte **"..."** a pak vyberte **Odpojit**.

## <a name="powershell"></a>PowerShell

Zobrazení a odpojení relace pomocí prostředí PowerShell:

1. K vypsání aktivních relací spusťte následující příkaz prostředí PowerShell:

   ```azurepowershell-interactive
   Get-AzVirtualNetworkGatewayVpnClientConnectionHealth -VirtualNetworkGatewayName <name of the gateway>  -ResourceGroupName <name of the resource group>
   ```
1. Zkopírujte **VpnConnectionId** relace, kterou chcete odpojit.

   :::image type="content" source="./media/p2s-session-management/powershell.png" alt-text="Příklad PowerShellu":::
1. Chcete-li odpojit relaci, spusťte následující příkaz:

   ```azurepowershell-interactive
   Disconnect-AzVirtualNetworkGatewayVpnConnection -VirtualNetworkGatewayName <name of the gateway> -ResourceGroupName <name of the resource group> -VpnConnectionId <VpnConnectionId of the session>
   ```

## <a name="next-steps"></a>Další kroky

Další informace o připojení typu Point-to-site najdete v tématu věnovaném [síti VPN typu Point-to-site](point-to-site-about.md).
