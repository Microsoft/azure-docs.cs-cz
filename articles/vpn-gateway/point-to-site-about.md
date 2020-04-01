---
title: O připojení CHOd Azure k webu VPN | Brána VPN
description: Tento článek vám pomůže porozumět připojení typu Point-to-Site a pomůže vám rozhodnout, který typ ověřování brány P2S VPN se má použít.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: cherylmc
ms.openlocfilehash: 381aad5d0a56362d9966ed54b931a8478f2f6bf2
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80410489"
---
# <a name="about-point-to-site-vpn"></a>O síti VPN z bodu na web

Připojení brány VPN typu Point-to-Site (P2S) umožňuje vytvořit zabezpečené připojení k virtuální síti z jednotlivých klientských počítačů. Připojení P2S se vytvoří jeho zahájením z klientského počítače. Toto řešení je užitečné pro osoby pracující z domova, které se chtějí k virtuálním sítím Azure připojit ze vzdáleného umístění, například z domova nebo z místa konání konference. Síť VPN P2S je také užitečným řešením nahrazujícím síť VPN S2S, pokud máte pouze několik klientů, kteří se potřebují připojit k virtuální síti. Tento článek se týká modelu nasazení Resource Manager.

## <a name="what-protocol-does-p2s-use"></a><a name="protocol"></a>Jaký protokol používá P2S?

Point-to-site VPN můžete použít jeden z následujících protokolů:

* **OpenVPN® Protocol**, protokol VPN založený na Protokolu SSL/TLS. Řešení TLS VPN může proniknout firewally, protože většina firewallů otevírá tcp port 443 odchozí, který TLS používá. OpenVPN lze použít pro připojení z Android, iOS (verze 11.0 a vyšší), Windows, Linux a Mac zařízení (OSX verze 10.13 a vyšší).

* Protokol SSTP (Secure Socket Tunneling Protocol), proprietární protokol VPN založený na protokolu TLS. Řešení TLS VPN může proniknout firewally, protože většina firewallů otevírá tcp port 443 odchozí, který TLS používá. SSTP je podporována pouze na zařízeních se systémem Windows. Azure podporuje všechny verze Windows, které mají SSTP (Windows 7 a novější).

* IKEv2 VPN, řešení IPsec VPN založené na standardech. IKEv2 VPN je možné použít k připojení ze zařízení se systémem Mac (OSX verze 10.11 a vyšší).


>[!NOTE]
>IKEv2 a OpenVPN pro P2S jsou k dispozici pouze pro model nasazení Resource Manager. Nejsou k dispozici pro model klasického nasazení.
>

## <a name="how-are-p2s-vpn-clients-authenticated"></a><a name="authentication"></a>Jak jsou klienti P2S VPN ověřeni?

Než Azure přijme připojení VPN P2S, musí být uživatel nejprve ověřen. Existují dva mechanismy, které Azure nabízí k ověření připojení uživatele.

### <a name="authenticate-using-native-azure-certificate-authentication"></a>Ověření pomocí nativního ověřování certifikátu Azure

Při použití nativního ověřování certifikátu Azure se k ověření připojujícího uživatele používá klientský certifikát, který se v zařízení nachází. Klientské certifikáty jsou generovány z důvěryhodného kořenového certifikátu a poté nainstalovány v každém klientském počítači. Můžete použít kořenový certifikát, který byl vygenerován pomocí řešení Enterprise, nebo můžete vygenerovat certifikát podepsaný svým držitelem.

Ověření klientského certifikátu provádí brána VPN a probíhá při navazování připojení P2S VPN. Kořenový certifikát je vyžadován pro ověření a musí být odeslán do Azure.

### <a name="authenticate-using-native-azure-active-directory-authentication"></a>Ověření pomocí nativního ověřování služby Azure Active Directory

Ověřování Azure AD umožňuje uživatelům připojit se k Azure pomocí jejich přihlašovacích údajů služby Azure Active Directory. Nativní ověřování Azure AD je podporované jenom pro protokol OpenVPN a Windows 10 a vyžaduje použití [klienta Azure VPN](https://go.microsoft.com/fwlink/?linkid=2117554)Client .

Díky nativnímu ověřování Azure AD můžete využít funkce podmíněného přístupu Azure AD i funkce vícefaktorového ověřování (MFA) pro VPN.

Na vysoké úrovni je třeba provést následující kroky ke konfiguraci ověřování Azure AD:

1. [Konfigurace tenanta Azure AD](openvpn-azure-ad-tenant.md)

2. [Povolení ověřování Azure AD na bráně](openvpn-azure-ad-tenant.md#enable-authentication)

3. [Stažení a konfigurace klienta Azure VPN](https://go.microsoft.com/fwlink/?linkid=2117554)


### <a name="authenticate-using-active-directory-ad-domain-server"></a>Ověření pomocí serveru domény služby Active Directory (AD)

Ověřování domény služby AD umožňuje uživatelům připojit se k Azure pomocí přihlašovacích údajů k jejich organizační doméně. Vyžaduje server RADIUS, který se integruje se serverem AD. Organizace mohou také využít své stávající nasazení RADIUS.
  
Server RADIUS může být nasazený místně nebo ve vaší virtuální síti Azure. Během ověřování funguje brána Azure VPN Gateway jako předávání a předává ověřovací zprávy tam a zpět mezi serverem RADIUS a připojujícím se zařízením. Takže přístupnost brány na server RADIUS je důležitá. Pokud je server RADIUS k dispozici místně, je pro dosažení potřeba připojení VPN S2S z Azure k místnímu webu.  
  
Server RADIUS lze také integrovat se službami certifikátu AD. To vám umožní použít server RADIUS a nasazení podnikového certifikátu pro ověřování certifikátů P2S jako alternativu k ověřování certifikátů Azure. Výhodou je, že do Azure nemusíte nahrávat kořenové certifikáty a odvolané certifikáty.

Server RADIUS lze také integrovat s jinými externími systémy identit. To otevírá spoustu možností ověřování pro P2S VPN, včetně vícefaktorových možností.

![bod na místo](./media/point-to-site-about/p2s.png "Point-to-Site")

## <a name="what-are-the-client-configuration-requirements"></a>Jaké jsou požadavky na konfiguraci klienta?

>[!NOTE]
>Pro klienty windows musíte mít práva správce na klientském zařízení, abyste mohli zahájit připojení VPN z klientského zařízení do Azure.
>

Uživatelé používají nativní klienty VPN na zařízeních Se systémem Windows a Mac pro P2S. Azure poskytuje soubor ZIP konfigurace klienta VPN, který obsahuje nastavení vyžadovaná těmito nativními klienty pro připojení k Azure.

* Pro zařízení se systémem Windows se konfigurace klienta VPN skládá z instalačního balíčku, který uživatelé instalují na svých zařízeních.
* Pro zařízení Mac se skládá z souboru mobileconfig, který uživatelé instalují do svých zařízení.

Soubor zip také poskytuje hodnoty některých důležitých nastavení na straně Azure, které můžete použít k vytvoření vlastního profilu pro tato zařízení. Některé hodnoty zahrnují adresu brány VPN, nakonfigurované typy tunelů, trasy a kořenový certifikát pro ověření brány.

>[!NOTE]
>[!INCLUDE [TLS version changes](../../includes/vpn-gateway-tls-change.md)]
>

## <a name="which-gateway-skus-support-p2s-vpn"></a><a name="gwsku"></a>Které brány SKU podporují P2S VPN?

[!INCLUDE [aggregate throughput sku](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

* Doporučení skladové položky brány najdete [v tématu O nastavení brány VPN](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

>[!NOTE]
>Skladová položka Basic nepodporuje ověřování IKEv2 ani RADIUS.
>

## <a name="what-ikeipsec-policies-are-configured-on-vpn-gateways-for-p2s"></a><a name="IKE/IPsec policies"></a>Jaké zásady Protokolu IKE/IPsec jsou konfigurovány v branách VPN pro P2S?


**IKEv2**

|**Šifra** | **Integrita** | **PRF** | **Skupina DH** |
|---        | ---           | ---       | ---   |
|GCM_AES256 |   GCM_AES256  | SHA384    | GROUP_24 |
|GCM_AES256 |   GCM_AES256  | SHA384    | GROUP_14 |
|GCM_AES256 |   GCM_AES256  | SHA384    | GROUP_ECP384 |
|GCM_AES256 |   GCM_AES256  | SHA384    | GROUP_ECP256 |
|GCM_AES256 |   GCM_AES256  | SHA256    | GROUP_24 |
|GCM_AES256 |   GCM_AES256  | SHA256    | GROUP_14 |
|GCM_AES256 |   GCM_AES256  | SHA256    | GROUP_ECP384 |
|GCM_AES256 |   GCM_AES256  | SHA256    | GROUP_ECP256 |
|AES256     |   SHA384      | SHA384    | GROUP_24 |
|AES256     |   SHA384      | SHA384    | GROUP_14 |
|AES256     |   SHA384      | SHA384    | GROUP_ECP384 |
|AES256     |   SHA384      | SHA384    | GROUP_ECP256 |
|AES256     |   SHA256      | SHA256    | GROUP_24 |
|AES256     |   SHA256      | SHA256    | GROUP_14 |
|AES256     |   SHA256      | SHA256    | GROUP_ECP384 |
|AES256     |   SHA256      | SHA256    | GROUP_ECP256 |
|AES256     |   SHA256      | SHA256    | GROUP_2 |

**Protokol IPsec**

|**Šifra** | **Integrita** | **Skupina PFS** |
|---        | ---           | ---       |
|GCM_AES256 | GCM_AES256 | GROUP_NONE |
|GCM_AES256 | GCM_AES256 | GROUP_24 |
|GCM_AES256 | GCM_AES256 | GROUP_14 |
|GCM_AES256 | GCM_AES256 | GROUP_ECP384 |
|GCM_AES256 | GCM_AES256 | GROUP_ECP256 |
| AES256    | SHA256 | GROUP_NONE |
| AES256    | SHA256 | GROUP_24 |
| AES256    | SHA256 | GROUP_14 |
| AES256    | SHA256 | GROUP_ECP384 |
| AES256    | SHA256 | GROUP_ECP256 |
| AES256    | SHA1 | GROUP_NONE |

## <a name="what-tls-policies-are-configured-on-vpn-gateways-for-p2s"></a><a name="TLS policies"></a>Jaké zásady TLS jsou konfigurovány na branách VPN pro P2S?
**Tls**

|**Zásady** |
|---| 
|TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 |
|TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384 |
|TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 |
|TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 |
|TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256 |
|TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384 |
|TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 |
|TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 |
|TLS_RSA_WITH_AES_128_GCM_SHA256 |
|TLS_RSA_WITH_AES_256_GCM_SHA384 |
|TLS_RSA_WITH_AES_128_CBC_SHA256 |
|TLS_RSA_WITH_AES_256_CBC_SHA256 |

## <a name="how-do-i-configure-a-p2s-connection"></a><a name="configure"></a>Jak nakonfiguruji připojení P2S?

Konfigurace P2S vyžaduje poměrně málo konkrétních kroků. Následující články obsahují kroky, které vás provedou konfigurací P2S, a odkazy na konfiguraci klientských zařízení VPN:

* [Konfigurace připojení P2S – ověřování RADIUS](point-to-site-how-to-radius-ps.md)

* [Konfigurace připojení P2S – ověřování nativního certifikátu Azure](vpn-gateway-howto-point-to-site-rm-ps.md)

* [Konfigurace OpenVPN](vpn-gateway-howto-openvpn.md)

### <a name="to-remove-the-configuration-of-a-p2s-connection"></a>Odebrání konfigurace připojení P2S

Postup najdete v [nejčastějších dotazech](#removeconfig)níže.
 
## <a name="faq-for-native-azure-certificate-authentication"></a><a name="faqcert"></a>Nejčastější dotazy k nativnímu ověřování certifikátů Azure

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="faq-for-radius-authentication"></a><a name="faqradius"></a>Nejčastější dotazy k ověřování v rámci radius

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="next-steps"></a>Další kroky

* [Konfigurace připojení P2S – ověřování RADIUS](point-to-site-how-to-radius-ps.md)

* [Konfigurace připojení P2S – ověřování nativního certifikátu Azure](vpn-gateway-howto-point-to-site-rm-ps.md)

**"OpenVPN" je ochranná známka společnosti OpenVPN Inc.**
