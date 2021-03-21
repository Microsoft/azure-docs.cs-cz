---
title: 'Azure VPN Gateway: informace o profilech klienta VPN P2S'
description: V tomto článku najdete informace, které potřebujete pro profil klienta VPN.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/08/2021
ms.author: cherylmc
ms.openlocfilehash: 9bb66363d525648df08f32451842402ad1d0d93b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99979062"
---
# <a name="working-with-p2s-vpn-client-profile-files"></a>Práce se soubory profilů klienta VPN v P2S

Soubory profilu obsahují informace potřebné ke konfiguraci připojení VPN. Tento článek vám pomůže získat a pochopit informace, které jsou nezbytné pro profil klienta VPN.

## <a name="generate-and-download-profile"></a>Vygenerovat a stáhnout profil

Konfigurační soubory klienta můžete vygenerovat pomocí PowerShellu nebo pomocí Azure Portal. Kterákoli z metod vrátí stejný soubor zip.

### <a name="portal"></a>Portál

1. V Azure Portal přejděte do brány virtuální sítě pro virtuální síť, ke které se chcete připojit.
1. Na stránce Brána virtuální sítě vyberte **Konfigurace Point-to-site**.
1. V horní části stránky konfigurace typu Point-to-site vyberte možnost **stáhnout klienta VPN**. Generování konfiguračního balíčku klienta může trvat několik minut.
1. V prohlížeči se zobrazí zpráva, že je k dispozici soubor zip konfigurace klienta. Má název stejný jako brána. Rozbalte soubor, aby se zobrazily složky.

### <a name="powershell"></a>PowerShell

K vygenerování pomocí PowerShellu můžete použít následující příklad:

1. Při generování konfiguračních souborů klienta VPN je hodnota pro "-AuthenticationMethod" EapTls ". Pomocí následujícího příkazu vygenerujte konfigurační soubory klienta VPN:

   ```azurepowershell-interactive
   $profile=New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls"

   $profile.VPNProfileSASUrl
   ```

1. Zkopírujte adresu URL do prohlížeče a Stáhněte soubor zip a potom rozbalte soubor, abyste viděli složky.

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* **Složka OpenVPN** obsahuje profil *ovpn* , který je potřeba upravit tak, aby zahrnoval klíč a certifikát. Další informace najdete v tématu [Konfigurace OpenVPN klientů pro Azure VPN Gateway](vpn-gateway-howto-openvpn-clients.md#windows). Pokud je v bráně VPN vybraná možnost ověřování Azure AD, tato složka není v souboru zip k dispozici. Místo toho přejděte do složky AzureVPN a vyhledejte azurevpnconfig.xml.

## <a name="next-steps"></a>Další kroky

Další informace o Point-to-site najdete v tématu [informace o Point-to-site](point-to-site-about.md).
