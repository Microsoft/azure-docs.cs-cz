---
title: Informace o připojení Azure Point-to-Site VPN | Dokumentace Microsoftu
description: Tento článek vám pomůže pochopit připojení Point-to-Site a vám pomůže rozhodnout, typu ověřování P2S VPN gateway používat.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: cherylmc
ms.openlocfilehash: 682d1d123d3381a52c9d356cd1bf226d2a847df4
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2019
ms.locfileid: "54352865"
---
# <a name="about-point-to-site-vpn"></a>Informace o VPN typu Point-to-Site

Připojení brány VPN typu Point-to-Site (P2S) umožňuje vytvořit zabezpečené připojení k virtuální síti z jednotlivých klientských počítačů. Připojení P2S se vytvoří jeho zahájením z klientského počítače. Toto řešení je užitečné pro osoby pracující z domova, které se chtějí k virtuálním sítím Azure připojit ze vzdáleného umístění, například z domova nebo z místa konání konference. Síť VPN P2S je také užitečným řešením nahrazujícím síť VPN S2S, pokud máte pouze několik klientů, kteří se potřebují připojit k virtuální síti. Tento článek se týká modelu nasazení Resource Manager.

## <a name="protocol"></a>Protokol P2S používá?

Point-to-site VPN můžete použít jednu z následujících protokolů:

* OpenVPN, SSL/TLS na základě protokol VPN. Řešení typu VPN protokolu SSL umožňuje pronikat branami firewall, protože většina bran firewall otevírá port TCP 443, který používá protokol SSL. OpenVPN je možné se připojit z Android, iOS (verze 11.0 a vyšší), zařízení se systémy Linux a Mac (OSX verze 10.13 a vyšší).

* Zabezpečte SSTP Socket Tunneling Protocol (), speciální protokol VPN založený na protokolu SSL. Řešení typu VPN protokolu SSL umožňuje pronikat branami firewall, protože většina bran firewall otevírá port TCP 443, který používá protokol SSL. SSTP je podporována pouze na zařízeních s Windows. Azure podporuje všechny verze Windows, které mají SSTP (Windows 7 a novější).

* IKEv2 VPN, řešení IPsec VPN založené na standardech. IKEv2 VPN je možné použít k připojení ze zařízení se systémem Mac (OSX verze 10.11 a vyšší).


>[!NOTE]
>IKEv2 a OpenVPN pro P2S jsou k dispozici pro pouze modelu nasazení Resource Manager. Nejsou k dispozici pro model nasazení classic.
>

## <a name="authentication"></a>Jak se ověřuje klienty P2S VPN?

Předtím, než Azure akceptuje připojení VPN typu P2S, uživatel musí nejdřív ověřit. Existují dva mechanismy, které Azure nabízí pro ověření připojení uživatele.

### <a name="authenticate-using-native-azure-certificate-authentication"></a>Ověření pomocí nativního ověřování certifikátů Azure

Pokud používáte ověřování nativního certifikátu Azure, klientský certifikát, který je na zařízení se používá k ověření připojujícího se uživatele. Klientské certifikáty vygenerované z důvěryhodného kořenového certifikátu se nainstaluje na každý klientský počítač. Můžete použít kořenový certifikát vygenerovaný pomocí podnikového řešení, nebo můžete vygenerovat certifikát podepsaný svým držitelem.

Ověření certifikátu klienta se provádí pomocí VPN gateway a probíhá při navazování připojení k síti VPN P2S. Kořenový certifikát je vyžadován pro ověření a musí se nahrát do Azure.

### <a name="authenticate-using-active-directory-ad-domain-server"></a>Ověřování pomocí serveru domény Active Directory (AD)

Doménové ověřování AD umožňuje uživatelům připojit se k Azure pomocí svých firemních přihlašovacích údajů domény. Vyžaduje server RADIUS, která se integruje se serverem AD. Organizace také můžete využít své stávající nasazení pomocí protokolu RADIUS.   
  
RADIUS server může být nasazená místně nebo ve vaší virtuální síti Azure. Při ověřování Azure VPN Gateway slouží jako předávání a předávání ověřovacích zpráv vpřed a zpět mezi serverem RADIUS a připojení zařízení. Proto je důležité brány připojení k serveru RADIUS. Pokud RADIUS server je k dispozici místně, je připojení VPN S2S z Azure do místní lokality vyžaduje pro připojení.  
  
Server protokolu RADIUS můžete integrovat také s AD CS. To vám umožní používat RADIUS server a vaše podnikové nasazení certifikátů pro ověřování P2S certifikátu jako alternativu k ověřování certifikátů Azure. Výhodou je, že není nutné k odesílání kořenových certifikátů a odvolané certifikáty do Azure.

Server protokolu RADIUS můžete také integrovat s jinými systémy pro externí identity. Otevře spoustu možnosti ověřování pro P2S VPN, včetně možnosti služby Multi-Factor Authentication.

>[!NOTE]
>OpenVPN protokol není podporován ověřováním pomocí protokolu RADIUS.
>

![point-to-site](./media/point-to-site-about/p2s.png "Point-to-Site")

## <a name="what-are-the-client-configuration-requirements"></a>Jaké jsou požadavky na konfiguraci klienta?

>[!NOTE]
>Pro klienty Windows musí mít oprávnění správce v klientském zařízení zahájí připojení VPN z klientského počítače do Azure.
>

Uživatelé na zařízeních s Windows a Mac používat nativní klienty VPN p2s. Azure poskytuje klienta VPN zip konfigurační soubor, který obsahuje nastavení, které jsou potřebné tyto nativní klienty pro připojení k Azure.

* Pro zařízení s Windows konfigurace klienta VPN se skládá z instalačního balíčku, který uživatelé instalují na svých zařízeních.
* Pro zařízení se systémem Mac je tvořen mobileconfig soubor, který uživatelé instalují na svých zařízeních.

Soubor zip obsahuje také hodnoty některých důležitých nastavení na straně Azure, který vám pomůže vytvořit vlastní profil pro tato zařízení. Mezi hodnoty patří adresu brány VPN, typy nakonfigurované tunelové propojení, cesty a kořenový certifikát pro ověřování brány.

>[!NOTE]
>[!INCLUDE [TLS version changes](../../includes/vpn-gateway-tls-change.md)]
>

## <a name="gwsku"></a>Které skladové položky brány podporuje P2S VPN?

[!INCLUDE [aggregate throughput sku](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

* Doporučení SKU brány najdete v tématu [nastavení služby VPN Gateway](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

>[!NOTE]
>Skladová položka Basic nepodporuje ověřování IKEv2 ani RADIUS.
>

## <a name="configure"></a>Konfigurace připojení typu P2S

Konfigurace P2S vyžaduje poměrně musíte provést konkrétní postup. Následující články obsahují postup vás provede konfiguraci P2S a propojení lze nakonfigurovat klientská zařízení sítě VPN:

* [Konfigurace připojení typu P2S – ověřování pomocí protokolu RADIUS](point-to-site-how-to-radius-ps.md)

* [Konfigurace připojení typu P2S – nativní ověřování certifikátů Azure](vpn-gateway-howto-point-to-site-rm-ps.md)

* [Konfigurace OpenVPN](vpn-gateway-howto-openvpn.md)

## <a name="faqcert"></a>Nejčastější dotazy týkající se nativní ověřování certifikátů Azure

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="faqradius"></a>Nejčastější dotazy týkající se ověřování pomocí protokolu RADIUS

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="next-steps"></a>Další kroky

* [Konfigurace připojení typu P2S – ověřování pomocí protokolu RADIUS](point-to-site-how-to-radius-ps.md)

* [Konfigurace připojení typu P2S – nativní ověřování certifikátů Azure](vpn-gateway-howto-point-to-site-rm-ps.md)
