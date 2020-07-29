---
title: 'Azure VPN Gateway: informace o profilech klienta VPN P2S'
description: To vám pomůže pracovat se souborem profilu klienta.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 05/13/2020
ms.author: cherylmc
ms.openlocfilehash: 4526611fa8b80a7b97ee7317f5e285c50f05b0de
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "83650647"
---
# <a name="about-p2s-vpn-client-profiles"></a>Informace o profilech klienta VPN P2S

Stažený soubor profilu obsahuje informace potřebné ke konfiguraci připojení VPN. Tento článek vám pomůže získat a pochopit informace, které jsou nezbytné pro profil klienta VPN.

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* **Složka OpenVPN** obsahuje profil *ovpn* , který je potřeba upravit tak, aby zahrnoval klíč a certifikát. Další informace najdete v tématu [Konfigurace OpenVPN klientů pro Azure VPN Gateway](vpn-gateway-howto-openvpn-clients.md#windows). Pokud je v bráně VPN vybraná možnost ověřování Azure AD, tato složka není v souboru zip k dispozici. Místo toho přejděte do složky AzureVPN a vyhledejte azurevpnconfig.xml.

## <a name="next-steps"></a>Další kroky

Další informace o Point-to-site najdete v tématu [informace o Point-to-site](point-to-site-about.md).
