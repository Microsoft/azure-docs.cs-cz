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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066227"
---
## <a name="1-download-the-file"></a>1. Stáhněte si soubor

Spusťte následující příkazy. Zkopírujte adresu URL výsledků do prohlížeče, abyste mohli stáhnout soubor zip profilu.

```azurepowershell-interactive
$profile = New-AzVpnClientConfiguration -ResourceGroupName AADAuth -Name AADauthGW -AuthenticationMethod "EapTls"
   
$PROFILE.VpnProfileSASUrl
```

## <a name="2-extract-the-zip-file"></a>2. Extrahujte soubor zip

Rozbalte soubor zip. Soubor obsahuje následující složky:

* AzureVPN
* Obecné
* OpenVPN (Pokud jste povolili nastavení ověřování OpenVPN a Azure AD na bráně. Informace o bráně VPN najdete [v tématu Vytvoření klienta](../articles/vpn-gateway/openvpn-azure-ad-tenant.md). Virtuální wan najdete [v tématu Vytvoření klienta - VWAN](../articles/virtual-wan/openvpn-azure-ad-tenant.md).

## <a name="3-retrieve-information"></a>3. Získat informace

Ve složce **AzureVPN** přejděte do souboru ***azurevpnconfig.xml*** a otevřete ho pomocí poznámkového bloku. Poznamenejte si text mezi následujícími značkami.

```
<audience>          </audience>
<issuer>            </issuer>
<tennant>           </tennant>
<fqdn>              </fqdn>
<serversecret>      </serversecret>
```

## <a name="profile-details"></a>Podrobnosti profilu

Když přidáte připojení, použijte informace shromážděné v předchozím kroku pro stránku podrobností profilu. Pole odpovídají následujícím informacím:

   * **Okruh uživatelů:** Identifikuje prostředek příjemce, pro který je token určen.
   * **Emitent:** Identifikuje službu tokenů zabezpečení (STS), která vyzařovala token, stejně jako klienta Azure AD
   * **Nájemce:** Obsahuje neměnný jedinečný identifikátor klienta adresáře, který vydal token.
   * **FQDN:** Plně kvalifikovaný název domény (FQDN) v bráně Azure VPN
   * **ServerSecret:** Předsdílený klíč brány VPN

## <a name="folder-contents"></a>Obsah složky

* **Obecná složka** obsahuje certifikát veřejného serveru a soubor VpnSettings.xml. Soubor VpnSettings.xml obsahuje informace potřebné ke konfiguraci obecného klienta.

* Stažený soubor ZIP může také obsahovat složky **WindowsAmd64** a **WindowsX86.** Tyto složky obsahují instalační program pro sstp a IKEv2 pro klienty systému Windows. K jejich instalaci potřebujete práva správce klienta.