---
title: 'Azure VPN Gateway: inzerovat vlastní trasy pro klienty VPN P2S'
description: Postup inzerce vlastních tras do klientů Point-to-site
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: cherylmc
ms.openlocfilehash: a02bd5519b776a063646c11be2a34366fe429f99
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "89392387"
---
# <a name="advertise-custom-routes-for-p2s-vpn-clients"></a>Inzerce vlastních tras pro klienty P2S VPN

Je možné, že budete chtít inzerovat vlastní trasy všem klientům VPN typu Point-to-site. Například pokud jste ve virtuální síti povolili koncové body úložiště a chcete, aby vzdálení uživatelé měli přístup k těmto účtům úložiště přes připojení VPN. IP adresu koncového bodu úložiště můžete inzerovat všem vzdáleným uživatelům, aby přenosy dat do účtu úložiště procházely přes tunelové připojení VPN, nikoli z veřejného Internetu.

![Příklad propojení Multi-Site pomocí Azure VPN Gateway](./media/vpn-gateway-p2s-advertise-custom-routes/custom-routes.png)

## <a name="to-advertise-custom-routes"></a>Inzerování vlastních tras

K inzerování vlastních tras použijte `Set-AzVirtualNetworkGateway cmdlet` . Následující příklad ukazuje, jak inzerovat protokol IP pro [tabulky účtu úložiště contoso](https://contoso.table.core.windows.net).

1. Proveďte test *contoso.Table.Core.Windows.NET* a poznamenejte si IP adresu. Například:

    ```cmd
    C:\>ping contoso.table.core.windows.net
    Pinging table.by4prdstr05a.store.core.windows.net [13.88.144.250] with 32 bytes of data:
    ```

2. Spusťte následující příkazy PowerShellu:

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute 13.88.144.250/32
    ```

3. Chcete-li přidat více vlastních tras, použijte k oddělení adres čárku a mezery. Například:

    ```azurepowershell-interactive
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute x.x.x.x/xx , y.y.y.y/yy
    ```
## <a name="to-view-custom-routes"></a>Zobrazení vlastních tras

Pomocí následujícího příkladu můžete zobrazit vlastní trasy:

  ```azurepowershell-interactive
  $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
  $gw.CustomRoutes | Format-List
  ```
## <a name="to-delete-custom-routes"></a>Postup odstranění vlastních tras

K odstranění vlastních tras použijte následující příklad:

  ```azurepowershell-interactive
  $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
  Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute @0
  ```
## <a name="next-steps"></a>Další kroky

Další informace o směrování v P2S najdete v tématu [o směrování Point-to-site](vpn-gateway-about-point-to-site-routing.md).
