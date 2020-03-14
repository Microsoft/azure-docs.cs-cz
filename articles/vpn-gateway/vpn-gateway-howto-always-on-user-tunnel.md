---
title: Konfigurace tunelového propojení uživatelů při trvalém připojení
titleSuffix: Azure VPN Gateway
description: Tento článek popisuje, jak pro vaši bránu VPN nakonfigurovat uživatelské tunelové propojení VPN typu Always On.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: cherylmc
ms.openlocfilehash: bf9dbd0cef19ad54ba6c3b58f2b9b3071b98bd93
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/14/2020
ms.locfileid: "79370961"
---
# <a name="configure-an-always-on-vpn-user-tunnel"></a>Konfigurace tunelu uživatele VPN AlwaysOn

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="configure-the-gateway"></a>Konfigurace brány

 Podle pokynů v článku [Konfigurace připojení VPN typu Point-to-site](vpn-gateway-howto-point-to-site-resource-manager-portal.md) nakonfigurujte bránu VPN tak, aby používala protokol IKEv2 a ověřování na základě certifikátů.

[!INCLUDE [user configuration](../../includes/vpn-gateway-vwan-always-on-user.md)]

## <a name="to-remove-a-profile"></a>Postup odebrání profilu

Chcete-li odebrat profil, použijte následující postup:

1. Spusťte následující příkaz:

   ```powershell
   C:\> Remove-VpnConnection UserTest  
   ```

1. Odpojte připojení a zrušte zaškrtnutí políčka **Připojit automaticky** .

   ![Čištění](./media/vpn-gateway-howto-always-on-user-tunnel/disconnect.jpg)

## <a name="next-steps"></a>Další kroky

Pokud chcete řešit problémy s připojením, ke kterým může dojít, přečtěte si téma [problémy s připojením Point-to-site v Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
