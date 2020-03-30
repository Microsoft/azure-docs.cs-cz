---
title: Konfigurace nepřetržitého uživatelského tunelu VPN
titleSuffix: Azure VPN Gateway
description: Tento článek popisuje, jak nakonfigurovat tunelový propojení uživatelů sítě VPN always on pro bránu VPN.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: cherylmc
ms.openlocfilehash: 56934dd13661d8f623e673e2817e87618675c7ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502273"
---
# <a name="configure-an-always-on-vpn-user-tunnel"></a>Konfigurace tunelu uživatele VPN AlwaysOn

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="configure-the-gateway"></a>Konfigurace brány

 Pomocí pokynů v článku [Konfigurace připojení VPN typu Point-to-Site](vpn-gateway-howto-point-to-site-resource-manager-portal.md) nakonfigurujte bránu VPN tak, aby používala protokol IKEv2 a ověřování založené na certifikátech.

## <a name="configure-a-user-tunnel"></a>Konfigurace uživatelského tunelu

[!INCLUDE [user configuration](../../includes/vpn-gateway-vwan-always-on-user.md)]

## <a name="to-remove-a-profile"></a>Odebrání profilu

Chcete-li profil odebrat, postupujte takto:

1. Spusťte následující příkaz:

   ```powershell
   C:\> Remove-VpnConnection UserTest  
   ```

1. Odpojte připojení a zrušte zaškrtnutí políčka **Připojit automaticky.**

   ![Vyčištění](./media/vpn-gateway-howto-always-on-user-tunnel/disconnect.jpg)

## <a name="next-steps"></a>Další kroky

Informace o řešení problémů s připojením naleznete v [tématu Problémy s připojením z bodu na pracoviště Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
