---
title: Konfigurace tunelového propojení uživatelů při trvalém připojení
titleSuffix: Azure Virtual WAN
description: Tento článek popisuje, jak pro virtuální síť WAN nakonfigurovat uživatelské tunelové propojení VPN typu Always On.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: cherylmc
ms.openlocfilehash: dd5b215b143fbaf487325744a158bb8b05707951
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "79502864"
---
# <a name="configure-an-always-on-vpn-user-tunnel-for-virtual-wan"></a>Konfigurace tunelového připojení VPN typu Always On pro virtuální síť WAN

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="prerequisites"></a>Požadavky

Musíte vytvořit konfiguraci Point-to-site a upravit přiřazení virtuálního rozbočovače. Pokyny najdete v následujících částech:

* [Vytvoření konfigurace P2S](virtual-wan-point-to-site-portal.md#p2sconfig)
* [Upravit přiřazení centra](virtual-wan-point-to-site-portal.md#edit)

## <a name="configure-a-user-tunnel"></a>Konfigurace tunelového propojení uživatele

[!INCLUDE [user tunnel](../../includes/vpn-gateway-vwan-always-on-user.md)]

## <a name="to-remove-a-profile"></a>Postup odebrání profilu

Chcete-li odebrat profil, použijte následující postup:

1. Spusťte následující příkaz:

   ```powershell
   C:\> Remove-VpnConnection UserTest  
   ```

1. Odpojte připojení a zrušte zaškrtnutí políčka **Připojit automaticky** .

   ![Vyčištění](./media/howto-always-on-user-tunnel/disconnect.jpg)

## <a name="next-steps"></a>Další kroky

Další informace o virtuální síti WAN najdete v části [Nejčastější dotazy](virtual-wan-faq.md).
