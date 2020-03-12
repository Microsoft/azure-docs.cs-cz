---
title: 'Azure VPN Gateway: informace o profilech klienta VPN P2S'
description: To vám pomůže pracovat se souborem profilu klienta.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 03/11/2020
ms.author: cherylmc
ms.openlocfilehash: 762f62fa0901672c447da42f416e5b003e7419b2
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2020
ms.locfileid: "79127293"
---
# <a name="about-p2s-vpn-client-profiles"></a>Informace o profilech klienta VPN P2S

Stažený soubor profilu obsahuje informace potřebné ke konfiguraci připojení VPN. Tento článek vám pomůže získat a pochopit informace, které jsou nezbytné pro profil klienta VPN.

## <a name="1-download-the-file"></a>1. Stáhněte si soubor

Spusťte následující příkazy. Zkopírujte výslednou adresu URL do prohlížeče, aby bylo možné stáhnout soubor. zip profilu.

```azurepowershell-interactive
$profile = New-AzVpnClientConfiguration -ResourceGroupName AADAuth -Name AADauthGW -AuthenticationMethod "EapTls"
   
$PROFILE.VpnProfileSASUrl
```

## <a name="2-extract-the-zip-file"></a>2. Extrahujte soubor zip.

Rozbalte soubor zip. Soubor obsahuje následující složky:

* AzureVPN
* Obecné
* OpenVPN (Pokud jste povolili nastavení ověřování OpenVPN a Azure AD v bráně. Viz [Vytvoření tenanta](openvpn-azure-ad-tenant.md).)

## <a name="3-retrieve-information"></a>3. načtení informací

Ve složce **AzureVPN** přejděte do souboru ***azurevpnconfig. XML*** a otevřete ho pomocí poznámkového bloku. Zaznamenejte si text mezi následujícími značkami.

```
<audience>          </audience>
<issuer>            </issuer>
<tennant>           </tennant>
<fqdn>              </fqdn>
<serversecret>      </serversecret>
```

## <a name="profile-details"></a>Podrobnosti profilu

Když přidáte připojení, použijte informace, které jste shromáždili v předchozím kroku, na stránce s podrobnostmi profilu. Pole odpovídají následujícím informacím:

   * **Cílová skupina:** Identifikuje prostředek příjemce, pro který je token určený.
   * **Vystavitel:** Identifikuje službu tokenů zabezpečení (STS), která vygenerovala token, i klienta služby Azure AD.
   * **Tenant:** Obsahuje neproměnlivý jedinečný identifikátor tenanta adresáře, který token vystavil.
   * **Plně kvalifikovaný název domény:** Plně kvalifikovaný název domény (FQDN) ve službě Azure VPN Gateway
   * **ServerSecret:** Předsdílený klíč brány VPN Gateway

## <a name="folder-contents"></a>Obsah složky

* **Složka OpenVPN** obsahuje profil *ovpn* , který je potřeba upravit tak, aby zahrnoval klíč a certifikát. Další informace najdete v tématu [Konfigurace OpenVPN klientů pro Azure VPN Gateway](vpn-gateway-howto-openvpn-clients.md#windows). Pokud je v bráně VPN vybraná možnost ověřování Azure AD, nebude tato složka v souboru zip k dispozici. Místo toho bude azurevpnconfig. XML ve složce AzureVPN.

* **Obecná složka** obsahuje certifikát veřejného serveru a soubor VpnSettings. XML. Soubor VpnSettings. XML obsahuje informace potřebné ke konfiguraci obecného klienta.

* Stažený soubor ZIP může také obsahovat složky **WindowsAmd64** a **WindowsX86** . Tyto složky obsahují instalační program pro služby SSTP a IKEv2 pro klienty Windows. K instalaci klienta potřebujete práva správce.

## <a name="next-steps"></a>Další kroky

Další informace o Point-to-site najdete v tématu [informace o Point-to-site](point-to-site-about.md).
