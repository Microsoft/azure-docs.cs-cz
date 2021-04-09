---
title: Zobrazit stav a metriky protokolu BGP
titleSuffix: Azure VPN Gateway
description: Zobrazit důležité informace související s protokolem BGP pro řešení potíží.
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: sample
ms.date: 03/10/2021
ms.author: alzam
ms.openlocfilehash: f97bbccc980705699af822ba2730233239cdfd5f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "103148798"
---
# <a name="view-bgp-metrics-and-status"></a>Zobrazení metrik a stavu protokolu BGP

Metriky a stav protokolu BGP můžete zobrazit pomocí Azure Portal nebo pomocí Azure PowerShell.

## <a name="azure-portal"></a>portál Azure

V Azure Portal můžete zobrazit partnerské uzly protokolu BGP, zjištěné trasy a inzerované trasy. Můžete si také stáhnout soubory. csv obsahující tato data.

1. V [Azure Portal](https://portal.azure.com)přejděte na bránu virtuální sítě.
1. V části **monitorování** vyberte **partnerské uzly protokolu BGP** a otevřete stránku partnerské uzly protokolu BGP.

   :::image type="content" source="./media/bgp-diagnostics/bgp-portal.jpg" alt-text="Snímek obrazovky s metrikami v Azure Portal.":::

### <a name="learned-routes"></a>Zjištěné trasy

1. Na portálu můžete zobrazit až 50 zjištěných tras.

   :::image type="content" source="./media/bgp-diagnostics/learned-routes.jpg" alt-text="Snímek naučených tras.":::

1. Můžete si také stáhnout zjištěné soubory tras. Pokud máte více než 50 zjištěných tras, jediným způsobem, jak je zobrazit, je stažení a zobrazení souboru. csv. Pokud si chcete stáhnout, vyberte **Stáhnout zjištěné trasy**.

   :::image type="content" source="./media/bgp-diagnostics/download-routes.jpg" alt-text="Snímek obrazovky se stažením zjištěných tras":::
1. Pak soubor zobrazte.

   :::image type="content" source="./media/bgp-diagnostics/learned-routes-file.jpg" alt-text="Snímek obrazovky se staženými zjištěnými trasami":::

### <a name="advertised-routes"></a>Inzerované trasy

1. Chcete-li zobrazit inzerované trasy, vyberte **...** na konci sítě, které chcete zobrazit, a potom klikněte na tlačítko **Zobrazit inzerované trasy**.

   :::image type="content" source="./media/bgp-diagnostics/select-route.png" alt-text="Snímek obrazovky ukazující, jak zobrazit inzerované trasy." lightbox="./media/bgp-diagnostics/route-expand.png":::
1. Na stránce **trasy inzerované na stranu peer** můžete zobrazit až 50 inzerovaných tras.

   :::image type="content" source="./media/bgp-diagnostics/advertised-routes.jpg" alt-text="Snímek obrazovky inzerovaných tras.":::
1. Můžete si také stáhnout soubor inzerovaných tras. Pokud máte více než 50 inzerovaných tras, jediným způsobem, jak je zobrazit, je stažení a zobrazení souboru. csv. Pokud si chcete stáhnout, vyberte **Stáhnout inzerované trasy**.

   :::image type="content" source="./media/bgp-diagnostics/download-advertised.jpg" alt-text="Snímek obrazovky s výběrem stažených inzerovaných tras":::
1. Pak soubor zobrazte.

   :::image type="content" source="./media/bgp-diagnostics/advertised-routes-file.jpg" alt-text="Snímek obrazovky stažených inzerovaných tras.":::

### <a name="bgp-peers"></a>Partnerské uzly protokolu BGP

1. Na portálu můžete zobrazit až 50 partnerských uzlů protokolu BGP.

   :::image type="content" source="./media/bgp-diagnostics/peers.jpg" alt-text="Snímek obrazovky s partnerskými uzly protokolu BGP":::
1. Můžete si také stáhnout soubor partnerských vztahů protokolu BGP. Pokud máte více než 50 partnerských uzlů protokolu BGP, jediným způsobem, jak je zobrazit, je stažení a zobrazení souboru. csv. Pro stažení vyberte na stránce portálu možnost **Stáhnout partnerské uzly protokolu BGP** .

   :::image type="content" source="./media/bgp-diagnostics/download-peers.jpg" alt-text="Snímek obrazovky se stažením partnerských uzlů protokolu BGP":::
1. Pak soubor zobrazte.

   :::image type="content" source="./media/bgp-diagnostics/peers-file.jpg" alt-text="Snímek obrazovky stažených partnerských uzlů protokolu BGP":::

## <a name="powershell"></a>PowerShell

K zobrazení všech partnerských uzlů protokolu BGP a stavu použijte **příkaz Get-AzVirtualNetworkGatewayBGPPeerStatus** .

[!INCLUDE [VPN Gateway PowerShell instructions](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayBgpPeerStatus -ResourceGroupName resourceGroup -VirtualNetworkGatewayName gatewayName

Asn               : 65515
ConnectedDuration : 9.01:04:53.5768637
LocalAddress      : 10.1.0.254
MessagesReceived  : 14893
MessagesSent      : 14900
Neighbor          : 10.0.0.254
RoutesReceived    : 1
State             : Connected
```

Pomocí **Get-AzVirtualNetworkGatewayLearnedRoute** můžete zobrazit všechny trasy, se kterými se brána učí prostřednictvím protokolu BGP.

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayLearnedRoute -ResourceGroupName resourceGroup -VirtualNetworkGatewayname gatewayName

AsPath       :
LocalAddress : 10.1.0.254
Network      : 10.1.0.0/16
NextHop      :
Origin       : Network
SourcePeer   : 10.1.0.254
Weight       : 32768

AsPath       :
LocalAddress : 10.1.0.254
Network      : 10.0.0.254/32
NextHop      :
Origin       : Network
SourcePeer   : 10.1.0.254
Weight       : 32768

AsPath       : 65515
LocalAddress : 10.1.0.254
Network      : 10.0.0.0/16
NextHop      : 10.0.0.254
Origin       : EBgp
SourcePeer   : 10.0.0.254
Weight       : 32768
```

Pomocí **Get-AzVirtualNetworkGatewayAdvertisedRoute** můžete zobrazit všechny trasy, které brána inzeruje svým partnerům prostřednictvím protokolu BGP.

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayAdvertisedRoute -VirtualNetworkGatewayName gatewayName -ResourceGroupName resourceGroupName -Peer 10.0.0.254
```

## <a name="next-steps"></a>Další kroky

Další informace o protokolu BGP najdete v tématu [Konfigurace protokolu BGP pro VPN Gateway](bgp-howto.md).
