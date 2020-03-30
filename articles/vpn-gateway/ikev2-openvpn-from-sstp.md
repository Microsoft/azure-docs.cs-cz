---
title: Přechod na OpenVPN nebo IKEv2 z SSTP | Brána Azure VPN
description: Tento článek vám pomůže pochopit způsoby, jak překonat limit souběžného připojení 128 SSTP.
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/22/2020
ms.author: alzam
ms.openlocfilehash: 60c3a7f4f12993f475b3d03fd0915971d7673a5c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80143102"
---
# <a name="transition-to-openvpn-protocol-or-ikev2-from-sstp"></a>Přechod na protokol OpenVPN nebo IKEv2 z SSTP

Připojení brány VPN typu Point-to-Site (P2S) umožňuje vytvořit zabezpečené připojení k virtuální síti z jednotlivých klientských počítačů. Připojení P2S se vytvoří jeho zahájením z klientského počítače. Tento článek se vztahuje na model nasazení Resource Manager a hovoří o způsobech, jak překonat limit souběžného připojení 128 SSTP přechodem na protokol OpenVPN nebo IKEv2.

## <a name="what-protocol-does-p2s-use"></a><a name="protocol"></a>Jaký protokol používá P2S?

Point-to-site VPN můžete použít jeden z následujících protokolů:

* **OpenVPN&reg; Protocol**, protokol VPN založený na SSL/TLS. Řešení SSL VPN může proniknout firewally, protože většina firewallů otevírá tcp port 443 odchozí, který Používá SSL. OpenVPN lze použít pro připojení z Android, iOS (verze 11.0 a vyšší), Windows, Linux a Mac zařízení (OSX verze 10.13 a vyšší).

* **Protokol SSTP (Secure Socket Tunneling Protocol)**, proprietární protokol VPN založený na protokolu SSL. Řešení SSL VPN může proniknout firewally, protože většina firewallů otevírá tcp port 443 odchozí, který Používá SSL. SSTP je podporována pouze na zařízeních se systémem Windows. Azure podporuje všechny verze Windows, které mají SSTP (Windows 7 a novější). **SSTP podporuje až 128 souběžných připojení pouze bez ohledu na skladovou položku brány**.

* IKEv2 VPN, řešení IPsec VPN založené na standardech. IKEv2 VPN je možné použít k připojení ze zařízení se systémem Mac (OSX verze 10.11 a vyšší).


>[!NOTE]
>IKEv2 a OpenVPN pro P2S jsou k dispozici pouze pro model nasazení Resource Manager. Nejsou k dispozici pro model klasického nasazení. Základní brána SKU nepodporuje protokoly IKEv2 nebo OpenVPN. Pokud používáte základní skladovou položku, budete muset odstranit a znovu vytvořit produkční bránu virtuální sítě sku.
>

## <a name="migrating-from-sspt-to-ikev2-or-openvpn"></a>Migrace z SSPT na IKEv2 nebo OpenVPN

Mohou nastat případy, kdy chcete podporovat více než 128 souběžné připojení P2S k bráně VPN, ale používáte SSTP. V takovém případě musíte přejít na protokol IKEv2 nebo OpenVPN.

### <a name="option-1---add-ikev2-in-addition-to-sstp-on-the-gateway"></a>Možnost 1 - Přidat IKEv2 kromě SSTP na bráně

To je nejjednodušší možnost. SSTP a IKEv2 může existovat společně na stejné bráně a poskytují vyšší počet souběžných připojení. Můžete jednoduše povolit IKEv2 na existující bráně a znovu stáhnout klienta.

Přidání IKEv2 do existující brány SSTP VPN neovlivní stávající klienty a můžete je nakonfigurovat tak, aby používali IKEv2 v malých dávkách nebo jen nakonfigurovat nové klienty tak, aby používali IKEv2. Pokud je klient systému Windows nakonfigurován pro SSTP i IKEv2, pokusí se nejprve připojit pomocí ikev2 a pokud se to nezdaří, vrátí se zpět na SSTP.

**IKEv2 používá nestandardní porty UDP, takže je třeba zajistit, aby tyto porty nebyly blokovány na bráně firewall uživatele. Porty, které jsou používány, jsou UDP 500 a 4500.**

Chcete-li přidat IKEv2 do existující brány, jednoduše přejděte na kartu Konfigurace bodu k webu pod bránou virtuální sítě na portálu a v rozevíracím sezname vyberte **IKEv2 a SSTP (SSTP).**

![bod na místo](./media/ikev2-openvpn-from-sstp/sstptoikev2.png "IKEv2")


### <a name="option-2---remove-sstp-and-enable-openvpn-on-the-gateway"></a>Možnost 2 - Odebrat SSTP a povolit OpenVPN na bráně

Vzhledem k tomu, že SSTP a OpenVPN jsou protokoly založené na TLS, nemohou existovat společně na stejné bráně. Pokud se rozhodnete přejít od SSTP k OpenVPN, budete muset zakázat SSTP a povolit OpenVPN na bráně. Tato operace způsobí, že stávající klienti ztratí připojení k bráně VPN, dokud nebude nový profil nakonfigurován v klientovi.

Můžete povolit OpenVPN spolu s IKEv2, pokud si přejete. OpenVPN je založen na TLS a používá standardní port TCP 443. Chcete-li přepnout na OpenVPN, přejděte na kartu "konfigurace point-to-site" pod bránou virtuální sítě na portálu a z rozevíracího pole vyberte **OpenVPN (SSL)** nebo **IKEv2 a OpenVPN (SSL).**

![bod na místo](./media/ikev2-openvpn-from-sstp/sstptoopenvpn.png "OpenVPN")

Jakmile bude brána nakonfigurována, stávající klienti se nebudou moci připojit, dokud [nenasadíte a nenakonfigurujete klienty OpenVPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-openvpn-clients).

Pokud používáte Windows 10, můžete taky použít [Azure VPN Client pro Windows](https://docs.microsoft.com/azure/vpn-gateway/openvpn-azure-ad-client#to-download-the-azure-vpn-client)


## <a name="frequently-asked-questions"></a>Nejčastější dotazy
### <a name="what-are-the-client-configuration-requirements"></a>Jaké jsou požadavky na konfiguraci klienta?

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

### <a name="which-gateway-skus-support-p2s-vpn"></a><a name="gwsku"></a>Které brány SKU podporují P2S VPN?

[!INCLUDE [aggregate throughput sku](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

* Doporučení skladové položky brány najdete [v tématu O nastavení brány VPN](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

>[!NOTE]
>Skladová položka Basic nepodporuje ověřování IKEv2 ani RADIUS.
>

### <a name="what-ikeipsec-policies-are-configured-on-vpn-gateways-for-p2s"></a><a name="IKE/IPsec policies"></a>Jaké zásady Protokolu IKE/IPsec jsou konfigurovány v branách VPN pro P2S?


**IKEv2**

|**Šifra** | **Integrita** | **PRF** | **Skupina DH** |
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

**Protokol IPsec**

|**Šifra** | **Integrita** | **Skupina PFS** |
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

### <a name="what-tls-policies-are-configured-on-vpn-gateways-for-p2s"></a><a name="TLS policies"></a>Jaké zásady TLS jsou konfigurovány na branách VPN pro P2S?
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

### <a name="how-do-i-configure-a-p2s-connection"></a><a name="configure"></a>Jak nakonfiguruji připojení P2S?

Konfigurace P2S vyžaduje poměrně málo konkrétních kroků. Následující články obsahují kroky, které vás provedou konfigurací P2S, a odkazy na konfiguraci klientských zařízení VPN:

* [Konfigurace připojení P2S – ověřování RADIUS](point-to-site-how-to-radius-ps.md)

* [Konfigurace připojení P2S – ověřování nativního certifikátu Azure](vpn-gateway-howto-point-to-site-rm-ps.md)

* [Konfigurace OpenVPN](vpn-gateway-howto-openvpn.md)

## <a name="next-steps"></a>Další kroky

* [Konfigurace připojení P2S – ověřování RADIUS](point-to-site-how-to-radius-ps.md)

* [Konfigurace připojení P2S – ověřování nativního certifikátu Azure](vpn-gateway-howto-point-to-site-rm-ps.md)

**"OpenVPN" je ochranná známka společnosti OpenVPN Inc.**
