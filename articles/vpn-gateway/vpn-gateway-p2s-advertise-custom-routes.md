---
title: 'Brána Azure VPN: Inzerování vlastních tras pro klienty P2S VPN'
description: Postup inzerce vlastních tras klientům z bodu na pracoviště
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 11/11/2019
ms.author: cherylmc
ms.openlocfilehash: 7a904857b8aa0ed2aa18fc2a1b81fe31541e6f9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74151908"
---
# <a name="advertise-custom-routes-for-p2s-vpn-clients"></a>Inzerování vlastních tras pro klienty P2S VPN

Můžete chtít inzerovat vlastní trasy pro všechny klienty VPN point-to-site. Například pokud jste povolili koncové body úložiště ve virtuální síti a chcete, aby vzdálení uživatelé měli přístup k těmto účtům úložiště přes připojení VPN. Adresu IP koncového bodu úložiště můžete inzerovat všem vzdáleným uživatelům tak, aby přenosy na účet úložiště přecvály přes tunel VPN a nikoli veřejný Internet.

![Příklad propojení Multi-Site pomocí Azure VPN Gateway](./media/vpn-gateway-p2s-advertise-custom-routes/custom-routes.png)

## <a name="to-advertise-custom-routes"></a>Inzerce vlastních tras

Chcete-li inzerovat vlastní `Set-AzVirtualNetworkGateway cmdlet`trasy, použijte . Následující příklad ukazuje, jak inzerovat IP adresu pro [tabulky účtů úložiště Contoso](https://contoso.table.core.windows.net).

1. Příkaz *contoso.table.core.windows.net* contoso.table.core.windows.net příkazem ping a poznamenejte si adresu IP. Například:

    ```cmd
    C:\>ping contoso.table.core.windows.net
    Pinging table.by4prdstr05a.store.core.windows.net [13.88.144.250] with 32 bytes of data:
    ```

2. Spusťte následující příkazy PowerShellu:

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute 13.88.144.250/32
    ```

3. Chcete-li přidat více vlastních tras, oddělte adresy pomocí kómatu a mezer. Například:

    ```azurepowershell-interactive
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute x.x.x.x/xx , y.y.y.y/yy
    ```
## <a name="to-view-custom-routes"></a>Zobrazení vlastních tras

Vlastní trasy slouží k následujícímu příkladu:

  ```azurepowershell-interactive
  $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
  $gw.CustomRoutes | Format-List
  ```
## <a name="to-delete-custom-routes"></a>Odstranění vlastních tras

K odstranění vlastních tras použijte následující příklad:

  ```azurepowershell-interactive
  $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
  Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute @0
  ```
## <a name="next-steps"></a>Další kroky

Další informace o směrování P2S naleznete [v tématu O směrování z bodu na pracoviště](vpn-gateway-about-point-to-site-routing.md).
