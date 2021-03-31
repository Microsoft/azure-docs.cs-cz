---
title: Konfigurace tunelového propojení uživatele Always-On VPN
titleSuffix: Azure VPN Gateway
description: Tento článek popisuje, jak pro vaši bránu VPN nakonfigurovat uživatelské tunelové propojení VPN typu Always On.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: cherylmc
ms.openlocfilehash: 99d21222a62ed6b27a6a1b2a73b704f4cb26457b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "89435792"
---
# <a name="configure-an-always-on-vpn-user-tunnel"></a>Konfigurace tunelu uživatele VPN AlwaysOn

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="configure-the-gateway"></a>Konfigurace brány

 Podle pokynů v článku [Konfigurace připojení VPN typu Point-to-site](vpn-gateway-howto-point-to-site-resource-manager-portal.md) nakonfigurujte bránu VPN tak, aby používala protokol IKEv2 a ověřování na základě certifikátů.

## <a name="configure-a-user-tunnel"></a>Konfigurace tunelového propojení uživatele

[!INCLUDE [user configuration](../../includes/vpn-gateway-vwan-always-on-user.md)]

## <a name="to-remove-a-profile"></a>Postup odebrání profilu

Chcete-li odebrat profil, použijte následující postup:

1. Spusťte následující příkaz:

   ```powershell
   C:\> Remove-VpnConnection UserTest  
   ```

1. Odpojte připojení a zrušte zaškrtnutí políčka **Připojit automaticky** .

   ![Vyčištění](./media/vpn-gateway-howto-always-on-user-tunnel/disconnect.jpg)

## <a name="next-steps"></a>Další kroky

Pokud chcete řešit problémy s připojením, ke kterým může dojít, přečtěte si téma [problémy s připojením Point-to-site v Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
