---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/17/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c61378510fbfc8bdc13f35ba1063a0d9316d88e3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80066227"
---
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
* OpenVPN (Pokud jste povolili nastavení ověřování OpenVPN a Azure AD v bráně. VPN Gateway najdete v tématu [Vytvoření tenanta](../articles/vpn-gateway/openvpn-azure-ad-tenant.md). Informace o virtuální síti WAN najdete v tématu [Vytvoření tenanta – VWAN](../articles/virtual-wan/openvpn-azure-ad-tenant.md).

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

* **Obecná složka** obsahuje certifikát veřejného serveru a soubor VpnSettings. XML. Soubor VpnSettings. XML obsahuje informace potřebné ke konfiguraci obecného klienta.

* Stažený soubor ZIP může také obsahovat složky **WindowsAmd64** a **WindowsX86** . Tyto složky obsahují instalační program pro služby SSTP a IKEv2 pro klienty Windows. K instalaci klienta potřebujete práva správce.