---
title: O připojeních k síti VPN typu Point-to-site | VPN Gateway
description: Tento článek vám pomůže pochopit připojení typu Point-to-site a pomůže vám určit, který typ ověřování brány VPN P2S použít.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/02/2020
ms.author: cherylmc
ms.openlocfilehash: 6caf72884ce2566ad25b7dd6d80b15d737bbe664
ms.sourcegitcommit: 44edde1ae2ff6c157432eee85829e28740c6950d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/25/2021
ms.locfileid: "105543580"
---
# <a name="about-point-to-site-vpn"></a>O síti VPN typu Point-to-site

Připojení brány VPN typu Point-to-Site (P2S) umožňuje vytvořit zabezpečené připojení k virtuální síti z jednotlivých klientských počítačů. Připojení P2S se vytvoří jeho zahájením z klientského počítače. Toto řešení je užitečné pro osoby pracující z domova, které se chtějí k virtuálním sítím Azure připojit ze vzdáleného umístění, například z domova nebo z místa konání konference. Síť VPN P2S je také užitečným řešením nahrazujícím síť VPN S2S, pokud máte pouze několik klientů, kteří se potřebují připojit k virtuální síti. Tento článek se týká modelu nasazení Resource Manager.

## <a name="what-protocol-does-p2s-use"></a><a name="protocol"></a>Jaký protokol P2S použít?

SÍŤ VPN typu Point-to-site může používat jeden z následujících protokolů:

* **Protokol OpenVPN®**, protokol VPN založený na protokolu SSL/TLS. Řešení TLS VPN může proniknout do brány firewall, protože většina bran firewall otevírá odchozí port TCP 443, který používá protokol TLS. OpenVPN se dá použít k připojení z Androidu, iOS (verze 11,0 a vyšší), Windows, Linux a Mac (OSX verze 10,13 a vyšší).

* Protokol SSTP (Secure Socket Tunneling Protocol), což je proprietární protokol VPN založený na protokolu TLS. Řešení TLS VPN může proniknout do brány firewall, protože většina bran firewall otevírá odchozí port TCP 443, který používá protokol TLS. SSTP se podporuje jenom na zařízeních s Windows. Azure podporuje všechny verze Windows, které mají protokol SSTP (Windows 7 a novější).

* IKEv2 VPN, řešení IPsec VPN založené na standardech. IKEv2 VPN je možné použít k připojení ze zařízení se systémem Mac (OSX verze 10.11 a vyšší).


>[!NOTE]
>IKEv2 a OpenVPN pro P2S jsou k dispozici pouze pro model nasazení Správce prostředků. Nejsou k dispozici pro model nasazení Classic.
>

## <a name="how-are-p2s-vpn-clients-authenticated"></a><a name="authentication"></a>Jak se ověřují klienti VPN P2S?

Než Azure přijme připojení VPN P2S, musíte nejdřív ověřit uživatele. Existují dva mechanismy, které Azure nabízí k ověřování připojujícího se uživatele.

### <a name="authenticate-using-native-azure-certificate-authentication"></a>Ověřování pomocí nativního ověřování certifikátů Azure

Při použití nativního ověřování certifikátů Azure se k ověřování připojujícího uživatele používá klientský certifikát, který je k dispozici v zařízení. Klientské certifikáty se generují z důvěryhodného kořenového certifikátu a pak se nainstalují do každého klientského počítače. Můžete použít kořenový certifikát, který byl vygenerován pomocí podnikového řešení, nebo můžete vygenerovat certifikát podepsaný svým držitelem.

Ověření klientského certifikátu se provádí pomocí brány VPN a probíhá během navazování připojení P2S VPN. Pro ověření se vyžaduje kořenový certifikát a musí se nahrát do Azure.

### <a name="authenticate-using-native-azure-active-directory-authentication"></a>Ověřování pomocí nativního ověřování Azure Active Directory

Ověřování Azure AD umožňuje uživatelům připojit se k Azure pomocí svých Azure Active Directory přihlašovacích údajů. Nativní ověřování Azure AD se podporuje jenom pro protokol OpenVPN a Windows 10 a vyžaduje použití [klienta Azure VPN](https://go.microsoft.com/fwlink/?linkid=2117554).

S nativním ověřováním Azure AD můžete využít podmíněný přístup Azure AD a také funkce Multi-Factor Authentication (MFA) pro VPN.

Pokud chcete nakonfigurovat ověřování Azure AD, musíte na vysoké úrovni provést následující kroky:

1. [Konfigurace tenanta Azure AD](openvpn-azure-ad-tenant.md)

2. [Povolit ověřování Azure AD v bráně](openvpn-azure-ad-tenant.md#enable-authentication)

3. [Stažení a konfigurace klienta Azure VPN](https://go.microsoft.com/fwlink/?linkid=2117554)


### <a name="authenticate-using-active-directory-ad-domain-server"></a>Ověřování pomocí serveru domény služby Active Directory (AD)

Ověřování domény AD umožňuje uživatelům připojit se k Azure pomocí svých přihlašovacích údajů domény organizace. Vyžaduje server RADIUS, který se integruje se serverem AD. Organizace můžou využít i stávající nasazení RADIUS.

Server RADIUS se dá nasadit místně nebo ve vaší virtuální síti Azure. Při ověřování funguje VPN Gateway Azure jako předávací a přesměruje ověřovací zprávy mezi serverem RADIUS a připojeným zařízením. Proto je důležité dostupnost brány pro server RADIUS. Pokud se server RADIUS nachází v místním prostředí, vyžaduje se pro dostupnost připojení S2S VPN z Azure do místní lokality.

Server RADIUS se taky může integrovat se službou AD Certificate Services. To vám umožní používat server RADIUS a nasazení podnikového certifikátu pro ověřování certifikátů P2S jako alternativu k ověřování certifikátů Azure. Výhodou je, že nemusíte do Azure nahrávat kořenové certifikáty a odvolané certifikáty.

Server RADIUS se taky může integrovat s jinými externími systémy identit. Tím se otevře dostatek možností ověřování pro P2S VPN, včetně Multi-Factor Options.

![Diagram, který zobrazuje síť VPN typu Point-to-site s místní lokalitou.](./media/point-to-site-about/p2s.png)

## <a name="what-are-the-client-configuration-requirements"></a>Jaké jsou požadavky na konfiguraci klienta?

>[!NOTE]
>U klientů se systémem Windows musíte mít v klientském zařízení práva správce, aby bylo možné iniciovat připojení VPN z klientského zařízení do Azure.
>

Uživatelé používají nativní klienty VPN na zařízeních s Windows a Mac pro P2S. Azure poskytuje soubor zip konfigurace klienta VPN, který obsahuje nastavení požadovaná těmito nativními klienty pro připojení k Azure.

* V případě zařízení s Windows se konfigurace klienta VPN skládá z balíčku instalačního programu, který si uživatelé nainstalují na svá zařízení.
* Pro zařízení Mac se skládá ze souboru mobileconfig, který uživatelé nainstalují na svá zařízení.

Soubor zip také poskytuje hodnoty některých důležitých nastavení na straně Azure, které můžete použít k vytvoření vlastního profilu pro tato zařízení. Mezi některé z těchto hodnot patří adresa brány VPN, nakonfigurované typy tunelového propojení, trasy a kořenový certifikát pro ověření brány.

>[!NOTE]
>[!INCLUDE [TLS version changes](../../includes/vpn-gateway-tls-change.md)]
>

## <a name="which-gateway-skus-support-p2s-vpn"></a><a name="gwsku"></a>Které skladové položky brány podporují P2S VPN?

[!INCLUDE [aggregate throughput sku](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

* Doporučení pro SKU brány najdete v tématu [informace o nastaveních VPN Gateway](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

>[!NOTE]
>Skladová položka Basic nepodporuje ověřování IKEv2 ani RADIUS.
>

## <a name="what-ikeipsec-policies-are-configured-on-vpn-gateways-for-p2s"></a><a name="IKE/IPsec policies"></a>Jaké zásady IKE/IPsec jsou nakonfigurované na bránách VPN pro P2S?


**IKEv2**

| **Šifr** | **Integrita** | **PRF** | **Skupina DH** |
|--|--|--|--|
| GCM_AES256 | GCM_AES256 | SHA384 | GROUP_24 |
| GCM_AES256 | GCM_AES256 | SHA384 | GROUP_14 |
| GCM_AES256 | GCM_AES256 | SHA384 | GROUP_ECP384 |
| GCM_AES256 | GCM_AES256 | SHA384 | GROUP_ECP256 |
| GCM_AES256 | GCM_AES256 | SHA256 | GROUP_24 |
| GCM_AES256 | GCM_AES256 | SHA256 | GROUP_14 |
| GCM_AES256 | GCM_AES256 | SHA256 | GROUP_ECP384 |
| GCM_AES256 | GCM_AES256 | SHA256 | GROUP_ECP256 |
| AES256 | SHA384 | SHA384 | GROUP_24 |
| AES256 | SHA384 | SHA384 | GROUP_14 |
| AES256 | SHA384 | SHA384 | GROUP_ECP384 |
| AES256 | SHA384 | SHA384 | GROUP_ECP256 |
| AES256 | SHA256 | SHA256 | GROUP_24 |
| AES256 | SHA256 | SHA256 | GROUP_14 |
| AES256 | SHA256 | SHA256 | GROUP_ECP384 |
| AES256 | SHA256 | SHA256 | GROUP_ECP256 |
| AES256 | SHA256 | SHA256 | GROUP_2 |

**IPsec**

| **Šifr** | **Integrita** | **Skupina PFS** |
|--|--|--|
| GCM_AES256 | GCM_AES256 | GROUP_NONE |
| GCM_AES256 | GCM_AES256 | GROUP_24 |
| GCM_AES256 | GCM_AES256 | GROUP_14 |
| GCM_AES256 | GCM_AES256 | GROUP_ECP384 |
| GCM_AES256 | GCM_AES256 | GROUP_ECP256 |
| AES256 | SHA256 | GROUP_NONE |
| AES256 | SHA256 | GROUP_24 |
| AES256 | SHA256 | GROUP_14 |
| AES256 | SHA256 | GROUP_ECP384 |
| AES256 | SHA256 | GROUP_ECP256 |
| AES256 | SHA1 | GROUP_NONE |

## <a name="what-tls-policies-are-configured-on-vpn-gateways-for-p2s"></a><a name="TLS policies"></a>Jaké zásady TLS jsou nakonfigurované na bránách VPN pro P2S?
**PROTOKOLY**

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

## <a name="how-do-i-configure-a-p2s-connection"></a><a name="configure"></a>Návody nakonfigurovat připojení P2S?

Konfigurace P2S vyžaduje pár specifických kroků. Následující články obsahují postup, který vás provede konfigurací P2S a odkazy na konfiguraci zařízení klienta VPN:

* [Konfigurace připojení P2S – ověřování pomocí protokolu RADIUS](point-to-site-how-to-radius-ps.md)

* [Konfigurace připojení P2S – ověření nativního certifikátu Azure](vpn-gateway-howto-point-to-site-rm-ps.md)

* [Konfigurace OpenVPN](vpn-gateway-howto-openvpn.md)

### <a name="to-remove-the-configuration-of-a-p2s-connection"></a>Odebrání konfigurace připojení P2S

Postup najdete v části [Nejčastější dotazy](#removeconfig).
 
## <a name="faq-for-native-azure-certificate-authentication"></a><a name="faqcert"></a>Nejčastější dotazy k nativnímu ověřování certifikátů Azure

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="faq-for-radius-authentication"></a><a name="faqradius"></a>Nejčastější dotazy k ověřování RADIUS

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="next-steps"></a>Další kroky

* [Konfigurace připojení P2S – ověřování pomocí protokolu RADIUS](point-to-site-how-to-radius-ps.md)

* [Konfigurace připojení P2S – ověření nativního certifikátu Azure](vpn-gateway-howto-point-to-site-rm-ps.md)

**"OpenVPN" je ochranná známka společnosti OpenVPN Inc.**
