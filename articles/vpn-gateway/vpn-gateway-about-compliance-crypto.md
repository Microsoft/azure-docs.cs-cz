---
title: 'Brána Azure VPN: Kryptografické požadavky'
description: Tento článek popisuje kryptografické požadavky a brány Azure VPN
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 01/10/2020
ms.author: yushwang
ms.openlocfilehash: 93e13592d9c434b159ad4f4c10ef30328941c64e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75902826"
---
# <a name="about-cryptographic-requirements-and-azure-vpn-gateways"></a>Kryptografické požadavky a brány Azure VPN

Tento článek popisuje, jak můžete nakonfigurovat brány Azure VPN tak, aby splňovaly vaše kryptografické požadavky pro tunely VPN mezi místními servery S2S a připojení virtuální sítě k virtuální síti v rámci Azure.

## <a name="about-ikev1-and-ikev2-for-azure-vpn-connections"></a>O připojeních VPN IKEv1 a IKEv2 pro Azure

Tradičně jsme povolili připojení IKEv1 pouze pro základní skuanové sítě a povolili připojení IKEv2 pro všechny sloky brány VPN než pro základní sloky. Základní skum umožňují pouze 1 připojení a spolu s dalšími omezeními, jako je například výkon, zákazníci, kteří používají starší zařízení, která podporují pouze protokoly IKEv1, měli omezené zkušenosti. Abychom zlepšili možnosti zákazníků používajících protokoly IKEv1, povolujeme nyní připojení IKEv1 pro všechny skladové položky brány VPN, s výjimkou základní skladové položky. Další informace naleznete v tématu [VPN Gateway Sku .](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings#gwsku)

![Připojení brány Azure VPN IKEv1 a IKEv2](./media/vpn-gateway-about-compliance-crypto/ikev1-ikev2-connections.png)

Při připojení IKEv1 a IKEv2 jsou použity na stejnou bránu VPN, přenos mezi těmito dvěma připojeními je automaticky povolena.

## <a name="about-ipsec-and-ike-policy-parameters-for-azure-vpn-gateways"></a>Parametry zásad Protokolu IPsec a IKE pro brány Azure VPN

Standard protokolu IPsec a IKE podporuje širokou škálu kryptografických algoritmů v různých kombinacích. Pokud nepožadujete konkrétní kombinaci kryptografických algoritmů a parametrů, brány Azure VPN používají sadu výchozích návrhů. Výchozí sady zásad byly vybrány pro maximalizaci interoperability s širokou škálou zařízení VPN jiných výrobců ve výchozích konfiguracích. V důsledku toho zásady a počet návrhů nemohou zahrnovat všechny možné kombinace dostupných kryptografických algoritmů a silných stránek klíčů.

Výchozí sada zásad pro bránu Azure VPN je uvedená v článku: [O zařízeních VPN a parametrech IPsec/IKE pro připojení brány VPN site-to-site](vpn-gateway-about-vpn-devices.md).

## <a name="cryptographic-requirements"></a>Kryptografické požadavky

Pro komunikaci, která vyžaduje specifické kryptografické algoritmy nebo parametry, obvykle z důvodu požadavků na dodržování předpisů nebo zabezpečení, teď můžete nakonfigurovat jejich brány Azure VPN tak, aby používaly vlastní zásady IPsec/IKE se specifickými kryptografickými algoritmy a silnými stránkami klíčů, nikoli výchozí sady zásad Azure.

Například zásady hlavního režimu IKEv2 pro brány Azure VPN využívají pouze Skupinu Diffie-Hellman Group 2 (1024 bitů), zatímco možná budete muset zadat silnější skupiny, které se mají používat v protokolu IKE, například Skupina 14 (2048 bitů), Skupina 24 (2048bitová Skupina MODP) nebo ECP (eliptická křivka). skupiny) 256 nebo 384 bitů (skupina 19 a skupina 20). Podobné požadavky platí také pro zásady rychlého režimu IPsec.

## <a name="custom-ipsecike-policy-with-azure-vpn-gateways"></a>Vlastní zásady Protokolu IPsec/IKE s bránami Azure VPN

Brány Azure VPN teď podporují vlastní zásady IPsec/IKE pro připojení na připojení. Pro připojení Site-to-Site nebo VNet-to-VNet můžete zvolit konkrétní kombinaci kryptografických algoritmů pro IPsec a IKE s požadovanou silou klíče, jak je znázorněno v následujícím příkladu:

![ipsec-ike-politika](./media/vpn-gateway-about-compliance-crypto/ipsecikepolicy.png)

Můžete vytvořit zásadu IPsec/IKE a použít nové nebo existující připojení.

### <a name="workflow"></a>Pracovní postup

1. Vytvoření virtuálních sítí, bran VPN nebo místních síťových bran pro topologii připojení, jak je popsáno v jiných dokumentech s návody
2. Vytvoření zásady IPsec/IKE
3. Zásadu můžete použít při vytváření připojení S2S nebo Virtuální sítě k virtuální síti.
4. Pokud je připojení již vytvořeno, můžete zásadu použít nebo aktualizovat na existující připojení.

## <a name="ipsecike-policy-faq"></a>Nejčastější dotazy k zásadám IPsec/IKE

[!INCLUDE [vpn-gateway-ipsecikepolicy-faq-include](../../includes/vpn-gateway-faq-ipsecikepolicy-include.md)]

## <a name="next-steps"></a>Další kroky

Podrobné pokyny ke konfiguraci vlastních zásad Protokolu IPsec/IKE najdete v tématu [Konfigurace zásad protokolu IPsec/IKE](vpn-gateway-ipsecikepolicy-rm-powershell.md) pro připojení.

Další informace o možnosti UsePolicyBasedTrafficSelectors najdete také v [tématu Připojení více zařízení VPN založených na zásadách.](vpn-gateway-connect-multiple-policybased-rm-ps.md)
