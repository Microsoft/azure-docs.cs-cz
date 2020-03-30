---
title: 'Azure Virtual WAN: Uživatelské profily klientů VPN'
description: To vám pomůže pracovat se souborem profilu klienta
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 03/18/2020
ms.author: cherylmc
ms.openlocfilehash: b88be5cccdaeb452288a2cb2f1dd4b4690a8b274
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066219"
---
# <a name="working-with-user-vpn-client-profiles"></a>Práce s profily klienta VPN uživatele

Stažený soubor profilu obsahuje informace, které jsou nezbytné pro konfiguraci připojení VPN. Tento článek vám pomůže získat a pochopit informace nezbytné pro profil klienta VPN uživatele.

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* **Složka OpenVPN** obsahuje profil *ovpn,* který je třeba upravit tak, aby zahrnoval klíč a certifikát. Další informace naleznete v [tématu Konfigurace klientů OpenVPN](../virtual-wan/howto-openvpn-clients.md#windows).

## <a name="next-steps"></a>Další kroky

Další informace o virtuální síti VPN uživatele sítě WAN naleznete [v tématu Vytvoření připojení VPN uživatele .](virtual-wan-point-to-site-portal.md)
