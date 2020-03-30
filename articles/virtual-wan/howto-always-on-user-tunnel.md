---
title: Konfigurace nepřetržitého uživatelského tunelu VPN
titleSuffix: Azure Virtual WAN
description: Tento článek popisuje, jak nakonfigurovat tunelový propojení uživatelů sítě VPN vždy na síti VPN pro virtuální síť WAN
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: cherylmc
ms.openlocfilehash: dd5b215b143fbaf487325744a158bb8b05707951
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502864"
---
# <a name="configure-an-always-on-vpn-user-tunnel-for-virtual-wan"></a>Konfigurace uživatelského tunelu always on VPN pro virtuální síť WAN

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="prerequisites"></a>Požadavky

Je nutné vytvořit konfiguraci bodu k webu a upravit přiřazení virtuálního centra. Pokyny naleznete v následujících částech:

* [Vytvoření konfigurace P2S](virtual-wan-point-to-site-portal.md#p2sconfig)
* [Úprava přiřazení centra](virtual-wan-point-to-site-portal.md#edit)

## <a name="configure-a-user-tunnel"></a>Konfigurace uživatelského tunelu

[!INCLUDE [user tunnel](../../includes/vpn-gateway-vwan-always-on-user.md)]

## <a name="to-remove-a-profile"></a>Odebrání profilu

Chcete-li profil odebrat, postupujte takto:

1. Spusťte následující příkaz:

   ```powershell
   C:\> Remove-VpnConnection UserTest  
   ```

1. Odpojte připojení a zrušte zaškrtnutí políčka **Připojit automaticky.**

   ![Vyčištění](./media/howto-always-on-user-tunnel/disconnect.jpg)

## <a name="next-steps"></a>Další kroky

Další informace o virtuální paměti WAN naleznete v [nejčastějších dotazech](virtual-wan-faq.md).
