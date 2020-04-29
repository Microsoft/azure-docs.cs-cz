---
title: 'Azure Virtual WAN: profily klientů VPN uživatele'
description: To vám pomůže pracovat se souborem profilu klienta.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 03/18/2020
ms.author: cherylmc
ms.openlocfilehash: b88be5cccdaeb452288a2cb2f1dd4b4690a8b274
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80066219"
---
# <a name="working-with-user-vpn-client-profiles"></a>Práce s profily klienta VPN uživatele

Stažený soubor profilu obsahuje informace potřebné ke konfiguraci připojení VPN. Tento článek vám pomůže získat a pochopit informace, které jsou nezbytné pro profil klienta VPN uživatele.

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* **Složka OpenVPN** obsahuje profil *ovpn* , který je potřeba upravit tak, aby zahrnoval klíč a certifikát. Další informace najdete v tématu [Konfigurace klientů OpenVPN](../virtual-wan/howto-openvpn-clients.md#windows).

## <a name="next-steps"></a>Další kroky

Další informace o virtuální síti VPN uživatele sítě WAN najdete v tématu [vytvoření připojení k síti VPN uživatele](virtual-wan-point-to-site-portal.md).
