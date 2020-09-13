---
title: Konfigurace tunelového propojení uživatelů při trvalém připojení
titleSuffix: Azure Virtual WAN
description: Tento článek popisuje, jak pro virtuální síť WAN nakonfigurovat uživatelské tunelové propojení VPN typu Always On.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/10/2020
ms.author: cherylmc
ms.openlocfilehash: 14790d4be91ffd0463436b6ca1d5c8794c102697
ms.sourcegitcommit: 43558caf1f3917f0c535ae0bf7ce7fe4723391f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2020
ms.locfileid: "90016347"
---
# <a name="configure-an-always-on-vpn-user-tunnel-for-virtual-wan"></a>Konfigurace tunelového připojení VPN typu Always On pro virtuální síť WAN

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="prerequisites"></a>Požadavky

Musíte vytvořit konfiguraci Point-to-site a upravit přiřazení virtuálního rozbočovače. Pokyny najdete v následujících částech:

* [Vytvoření konfigurace P2S](virtual-wan-point-to-site-portal.md#p2sconfig)
* [Vytvoření centra s bránou P2S](virtual-wan-point-to-site-portal.md#hub)

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
