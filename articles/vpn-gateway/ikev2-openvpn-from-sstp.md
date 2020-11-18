---
title: Přechod na OpenVPN nebo IKEv2 z SSTP | VPN Gateway Azure
description: Tento článek vám pomůže porozumět způsobům, jak překonat 128 souběžného počtu připojení SSTP.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: alzam
ms.openlocfilehash: 59aa6f5560917651d8f60c667145b0953bf72ef5
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94660861"
---
# <a name="transition-to-openvpn-protocol-or-ikev2-from-sstp"></a>Přechod na Protokol OpenVPN nebo IKEv2 z SSTP

Připojení brány VPN typu Point-to-Site (P2S) umožňuje vytvořit zabezpečené připojení k virtuální síti z jednotlivých klientských počítačů. Připojení P2S se vytvoří jeho zahájením z klientského počítače. Tento článek se týká modelu nasazení Správce prostředků a hovoří o způsobech, jak překonat limit souběžného počtu 128 souběžných připojení, a to přechodem na Protokol OpenVPN nebo IKEv2.

## <a name="what-protocol-does-p2s-use"></a><a name="protocol"></a>Jaký protokol P2S použít?

SÍŤ VPN typu Point-to-site může používat jeden z následujících protokolů:

* **OpenVPN &reg; Protokol**, protokol VPN založený na protokolu SSL/TLS. Řešení SSL VPN může proniknout do brány firewall, protože většina bran firewall otevírá odchozí TCP port 443, který používá protokol SSL. OpenVPN se dá použít k připojení z Androidu, iOS (verze 11,0 a vyšší), Windows, Linux a Mac (OSX verze 10,13 a vyšší).

* **Protokol SSTP (Secure Socket Tunneling Protocol)**, což je proprietární protokol VPN založený na protokolu SSL. Řešení SSL VPN může proniknout do brány firewall, protože většina bran firewall otevírá odchozí TCP port 443, který používá protokol SSL. SSTP se podporuje jenom na zařízeních s Windows. Azure podporuje všechny verze Windows, které mají protokol SSTP (Windows 7 a novější). **SSTP podporuje až 128 souběžných připojení jenom bez ohledu na SKU brány**.

* IKEv2 VPN, řešení IPsec VPN založené na standardech. IKEv2 VPN je možné použít k připojení ze zařízení se systémem Mac (OSX verze 10.11 a vyšší).


>[!NOTE]
>IKEv2 a OpenVPN pro P2S jsou k dispozici pouze pro model nasazení Správce prostředků. Nejsou k dispozici pro model nasazení Classic. SKU základní brány nepodporuje protokoly IKEv2 ani OpenVPN. Pokud používáte základní SKU, budete muset Virtual Network bránu odstranit a znovu vytvořit provozní SKU.
>

## <a name="migrating-from-sstp-to-ikev2-or-openvpn"></a>Migrace z SSTP na IKEv2 nebo OpenVPN

Můžou nastat případy, kdy budete chtít podporovat více než 128 souběžného připojení P2S k bráně VPN, ale používáte protokol SSTP. V takovém případě je potřeba přejít na protokol IKEv2 nebo OpenVPN.

### <a name="option-1---add-ikev2-in-addition-to-sstp-on-the-gateway"></a>Možnost 1 – Přidání IKEv2 kromě protokolu SSTP v bráně

Toto je nejjednodušší možnost. Protokol SSTP a IKEv2 můžou existovat ve stejné bráně a získat větší počet souběžných připojení. V existující bráně můžete jednoduše povolit IKEv2 a znovu stáhnout klienta.

Přidání IKEv2 do existující brány VPN SSTP nebude mít vliv na stávající klienty a můžete je nakonfigurovat tak, aby používaly IKEv2 v malých dávkách, nebo jenom nakonfigurovat nové klienty na používání IKEv2. Pokud je klient Windows nakonfigurovaný pro protokol SSTP i IKEv2, pokusí se připojit pomocí protokolu IKEV2 jako první a pokud se to nepovede, vrátí se k SSTP.

**IKEv2 používá nestandardní porty UDP, takže je potřeba zajistit, že tyto porty nejsou blokované v bráně firewall uživatele. Používané porty jsou UDP 500 a 4500.**

Pokud chcete přidat IKEv2 do existující brány, stačí přejít na kartu Konfigurace Point-to-site na portálu Virtual Network Gateway na portálu a v rozevíracím seznamu vybrat **IKEv2 a SSTP (SSL)** .

![Snímek obrazovky zobrazující stránku konfigurace Point-to-site s otevřeným rozevíracím seznamem Typ tunelového propojení a vybraný protokol IKEv2 a SSTP (SSL).](./media/ikev2-openvpn-from-sstp/sstptoikev2.png "IKEv2")


### <a name="option-2---remove-sstp-and-enable-openvpn-on-the-gateway"></a>Možnost 2 – odebrání SSTP a povolení OpenVPN v bráně

Vzhledem k tomu, že SSTP a OpenVPN jsou protokolem založeným na protokolu TLS, nemůžou existovat ve stejné bráně. Pokud se rozhodnete přesunout mimo protokol SSTP do OpenVPN, budete muset protokol SSTP zakázat a povolit OpenVPN v bráně. Tato operace způsobí ztrátu připojení stávajících klientů k bráně VPN, dokud nebude na klientovi nakonfigurovaný nový profil.

Pokud si přejete, můžete povolit OpenVPN spolu s IKEv2. OpenVPN je založen na TLS a používá standardní port TCP 443. Pokud chcete přepnout na OpenVPN, přejděte na kartu Konfigurace Point-to-site na portálu Virtual Network bránu a v rozevíracím seznamu vyberte **OpenVPN (SSL)** nebo **IKEv2 a OpenVPN (SSL)** .

![Point-to-site](./media/ikev2-openvpn-from-sstp/sstptoopenvpn.png "OpenVPN")

Po dokončení konfigurace brány se stávající klienti nebudou moct připojit, dokud neprovedete [nasazení a konfiguraci klientů OpenVPN](./vpn-gateway-howto-openvpn-clients.md).

Pokud používáte Windows 10, můžete použít také [klienta Azure VPN pro Windows](./openvpn-azure-ad-client.md#to-download-the-azure-vpn-client) .


## <a name="frequently-asked-questions"></a>Nejčastější dotazy
### <a name="what-are-the-client-configuration-requirements"></a>Jaké jsou požadavky na konfiguraci klienta?

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

### <a name="which-gateway-skus-support-p2s-vpn"></a><a name="gwsku"></a>Které skladové položky brány podporují P2S VPN?

[!INCLUDE [aggregate throughput sku](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

* Doporučení pro SKU brány najdete v tématu [informace o nastaveních VPN Gateway](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

>[!NOTE]
>Skladová položka Basic nepodporuje ověřování IKEv2 ani RADIUS.
>

### <a name="what-ikeipsec-policies-are-configured-on-vpn-gateways-for-p2s"></a><a name="IKE/IPsec policies"></a>Jaké zásady IKE/IPsec jsou nakonfigurované na bránách VPN pro P2S?


**IKEv2**

|**Šifr** | **Integrita** | **PRF** | **Skupina DH** |
|---        | ---            | ---        | ---     |
|GCM_AES256 |    GCM_AES256    | SHA384    | GROUP_24 |
|GCM_AES256 |    GCM_AES256    | SHA384    | GROUP_14 |
|GCM_AES256 |    GCM_AES256    | SHA384    | GROUP_ECP384 |
|GCM_AES256 |    GCM_AES256    | SHA384    | GROUP_ECP256 |
|GCM_AES256 |    GCM_AES256    | SHA256    | GROUP_24 |
|GCM_AES256 |    GCM_AES256    | SHA256    | GROUP_14 |
|GCM_AES256 |    GCM_AES256    | SHA256    | GROUP_ECP384 |
|GCM_AES256 |    GCM_AES256    | SHA256    | GROUP_ECP256 |
|AES256     |   SHA384        | SHA384    | GROUP_24 |
|AES256     |   SHA384        | SHA384    | GROUP_14 |
|AES256     |   SHA384        | SHA384    | GROUP_ECP384 |
|AES256     |   SHA384        | SHA384    | GROUP_ECP256 |
|AES256     |   SHA256        | SHA256    | GROUP_24 |
|AES256     |   SHA256        | SHA256    | GROUP_14 |
|AES256     |   SHA256        | SHA256    | GROUP_ECP384 |
|AES256     |   SHA256        | SHA256    | GROUP_ECP256 |
|AES256     |   SHA256        | SHA256    | GROUP_2 |

**IPsec**

|**Šifr** | **Integrita** | **Skupina PFS** |
|---        | ---            | ---        |
|GCM_AES256    | GCM_AES256 | GROUP_NONE |
|GCM_AES256    | GCM_AES256 | GROUP_24 |
|GCM_AES256    | GCM_AES256 | GROUP_14 |
|GCM_AES256    | GCM_AES256 | GROUP_ECP384 |
|GCM_AES256    | GCM_AES256 | GROUP_ECP256 |
| AES256    | SHA256 | GROUP_NONE |
| AES256    | SHA256 | GROUP_24 |
| AES256    | SHA256 | GROUP_14 |
| AES256    | SHA256 | GROUP_ECP384 |
| AES256    | SHA256 | GROUP_ECP256 |
| AES256    | SHA1 | GROUP_NONE |

### <a name="what-tls-policies-are-configured-on-vpn-gateways-for-p2s"></a><a name="TLS policies"></a>Jaké zásady TLS jsou nakonfigurované na bránách VPN pro P2S?
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

### <a name="how-do-i-configure-a-p2s-connection"></a><a name="configure"></a>Návody nakonfigurovat připojení P2S?

Konfigurace P2S vyžaduje pár specifických kroků. Následující články obsahují postup, který vás provede konfigurací P2S a odkazy na konfiguraci zařízení klienta VPN:

* [Konfigurace připojení P2S – ověřování pomocí protokolu RADIUS](point-to-site-how-to-radius-ps.md)

* [Konfigurace připojení P2S – ověření nativního certifikátu Azure](vpn-gateway-howto-point-to-site-rm-ps.md)

* [Konfigurace OpenVPN](vpn-gateway-howto-openvpn.md)

## <a name="next-steps"></a>Další kroky

* [Konfigurace připojení P2S – ověřování pomocí protokolu RADIUS](point-to-site-how-to-radius-ps.md)

* [Konfigurace připojení P2S – ověření nativního certifikátu Azure](vpn-gateway-howto-point-to-site-rm-ps.md)

**"OpenVPN" je ochranná známka společnosti OpenVPN Inc.**