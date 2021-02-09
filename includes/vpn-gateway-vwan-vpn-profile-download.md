---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/08/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: f3eb2d9469ab3a3d2c1d09e4adc3ee2cb1f86e6e
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2021
ms.locfileid: "99979061"
---
## <a name="extract-the-zip-file"></a>Extrakce souboru ZIP

Rozbalte soubor zip. Soubor obsahuje následující složky:

* AzureVPN
* Obecné
* OpenVPN (Pokud jste povolili OpenVPN s **certifikátem Azure** nebo nastavením **ověřování RADIUS** pro bránu). Vyberte příslušný článek, který odpovídá vaší konfiguraci pro vytvoření tenanta.

  * [VPN Gateway – Vytvoření tenanta](../articles/vpn-gateway/openvpn-azure-ad-tenant.md)
  * [Virtuální síť WAN – vytvoření tenanta](../articles/virtual-wan/openvpn-azure-ad-tenant.md)

## <a name="retrieve-information"></a>Načíst informace

Ve složce **AzureVPN** přejděte do souboru **_azurevpnconfig.xml_** a otevřete ho pomocí poznámkového bloku. Zaznamenejte si text mezi následujícími značkami.

```
<audience>          </audience>
<issuer>            </issuer>
<tennant>           </tennant>
<fqdn>              </fqdn>
<serversecret>      </serversecret>
```

## <a name="profile-details"></a>Podrobnosti profilu

Když přidáte připojení, použijte informace, které jste shromáždili v předchozím kroku, na stránce s podrobnostmi profilu. Pole odpovídají následujícím informacím:

* **Cílová skupina:** Identifikuje prostředek příjemce, pro který je token určen.
* **Vystavitel:** Identifikuje službu tokenů zabezpečení (STS), která vygenerovala token, i klienta služby Azure AD.
* **Tenant:** Obsahuje neproměnlivý jedinečný identifikátor tenanta adresáře, který token vystavil.
* **Plně kvalifikovaný název domény:** Plně kvalifikovaný název domény (FQDN) ve službě Azure VPN Gateway.
* **ServerSecret:** Předsdílený klíč brány VPN Gateway.

## <a name="folder-contents"></a>Obsah složky

* **Obecná složka** obsahuje certifikát veřejného serveru a soubor VpnSettings.xml. Soubor VpnSettings.xml obsahuje informace potřebné ke konfiguraci obecného klienta.

* Stažený soubor ZIP může také obsahovat složky **WindowsAmd64** a **WindowsX86** . Tyto složky obsahují instalační program pro služby SSTP a IKEv2 pro klienty Windows. K instalaci klienta potřebujete práva správce.
