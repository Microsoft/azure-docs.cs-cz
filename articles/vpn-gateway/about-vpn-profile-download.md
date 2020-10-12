---
title: 'Azure VPN Gateway: informace o profilech klienta VPN P2S'
description: V tomto článku najdete informace, které potřebujete pro profil klienta VPN.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/03/2020
ms.author: cherylmc
ms.openlocfilehash: 6a09767a7992a5f902adea6f99e937f3fc6fa7fa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90985932"
---
# <a name="about-p2s-vpn-client-profiles"></a>Informace o profilech klienta VPN P2S

Stažený soubor profilu obsahuje informace potřebné ke konfiguraci připojení VPN. Tento článek vám pomůže získat a pochopit informace, které jsou nezbytné pro profil klienta VPN.

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* **Složka OpenVPN** obsahuje profil *ovpn* , který je potřeba upravit tak, aby zahrnoval klíč a certifikát. Další informace najdete v tématu [Konfigurace OpenVPN klientů pro Azure VPN Gateway](vpn-gateway-howto-openvpn-clients.md#windows). Pokud je v bráně VPN vybraná možnost ověřování Azure AD, tato složka není v souboru zip k dispozici. Místo toho přejděte do složky AzureVPN a vyhledejte azurevpnconfig.xml.

## <a name="next-steps"></a>Další kroky

Další informace o Point-to-site najdete v tématu [informace o Point-to-site](point-to-site-about.md).
