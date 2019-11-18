---
title: 'Azure VPN Gateway: kryptografické požadavky'
description: Tento článek popisuje kryptografické požadavky a brány Azure VPN.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 10/17/2019
ms.author: yushwang
ms.openlocfilehash: f18e5dbdc257aa1ae647505c323c17f9f197d360
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150991"
---
# <a name="about-cryptographic-requirements-and-azure-vpn-gateways"></a>O kryptografických požadavcích a branách Azure VPN Gateway

Tento článek popisuje, jak můžete nakonfigurovat brány VPN Azure tak, aby splňovaly vaše kryptografické požadavky pro tunelové propojení VPN mezi místními sítěmi a připojení typu VNet-to-VNet v rámci Azure.

## <a name="about-ikev1-and-ikev2-for-azure-vpn-connections"></a>Informace o připojeních VPN IKEv1 a IKEv2 pro Azure

Tradičně jsme povolili připojení IKEv1 jenom pro základní SKU a povolená připojení IKEv2 pro všechny skladové položky služby VPN Gateway kromě základních SKU. Základní skladové položky (SKU) umožňují pouze 1 připojení a společně s dalšími omezeními, jako je například výkon, zákazníci používající starší zařízení, která podporují pouze IKEv1 protokoly, mají omezené prostředí. Abychom vylepšili možnosti zákazníků pomocí IKEv1 protokolů, teď pro všechny skladové položky služby VPN Gateway povolujeme připojení IKEv1. Další informace najdete v tématu [VPN Gateway SKU](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings#gwsku).

![Připojení k Azure VPN Gateway IKEv1 a IKEv2](./media/vpn-gateway-about-compliance-crypto/ikev1-ikev2-connections.png)

Pokud jsou připojení IKEv1 a IKEv2 použita u stejné brány VPN, je přenos mezi těmito dvěma připojeními povolen automaticky.

## <a name="about-ipsec-and-ike-policy-parameters-for-azure-vpn-gateways"></a>Parametry zásad IPsec a IKE pro Azure VPN Gateway

Protokol IPsec a IKE standard podporuje široké spektrum kryptografických algoritmů v různých kombinacích. Pokud nepožadujete určitou kombinaci kryptografických algoritmů a parametrů, Azure VPN Gateway použije sadu výchozích návrhů. Výchozí sady zásad byly zvoleny pro maximalizaci interoperability se široké škálou zařízení VPN jiných výrobců ve výchozích konfiguracích. V důsledku toho zásady a počet návrhů nemůžou pokrýt všechny možné kombinace dostupných kryptografických algoritmů a silných klíčů.

Výchozí nastavení zásad pro Azure VPN Gateway najdete v článku: [informace o zařízeních VPN a parametrech protokolu IPSec/IKE pro připojení typu Site-to-site VPN Gateway](vpn-gateway-about-vpn-devices.md).

## <a name="cryptographic-requirements"></a>Kryptografické požadavky

Pro komunikaci, která vyžaduje konkrétní kryptografické algoritmy nebo parametry, obvykle kvůli požadavkům na dodržování předpisů nebo zabezpečení, teď můžete nakonfigurovat své brány VPN Azure tak, aby používaly vlastní zásadu IPsec/IKE s konkrétními kryptografickými algoritmy a síly klíčů, nikoli výchozí zásady Azure.

Například zásady hlavního režimu IKEv2 pro služby Azure VPN Gateway využívají jenom skupinu Diffie-Hellman Group 2 (1024 bitů), zatímco možná budete muset zadat silnější skupiny, které se mají použít v protokolu IKE, jako je například skupina 14 (2048 bitů), skupina 24 (2048-bit MODP Group) nebo ECP (eliptická křivka skupiny) 256 nebo 384 (skupina 19 a skupina 20 v uvedeném pořadí). Podobné požadavky platí i pro zásady rychlého režimu IPsec.

## <a name="custom-ipsecike-policy-with-azure-vpn-gateways"></a>Vlastní zásady IPsec/IKE pomocí Azure VPN Gateway

Azure VPN Gateway teď podporují zásadu pro připojení, vlastní zásady IPsec/IKE. U připojení typu Site-to-site nebo VNet-to-VNet můžete zvolit konkrétní kombinaci kryptografických algoritmů pro protokol IPsec a IKE s požadovanou silou klíče, jak je znázorněno v následujícím příkladu:

![IPSec – IKE – zásada](./media/vpn-gateway-about-compliance-crypto/ipsecikepolicy.png)

Můžete vytvořit zásadu IPsec/IKE a použít ji pro nové nebo existující připojení.

### <a name="workflow"></a>Pracovní postup

1. Vytvořte virtuální sítě, brány sítě VPN nebo brány místní sítě pro topologii připojení, jak je popsáno v tématu další dokumenty s postupy.
2. Vytvoření zásady IPsec/IKE
3. Zásady můžete použít při vytváření připojení S2S nebo VNet-to-VNet.
4. Pokud je připojení už vytvořené, můžete zásady použít nebo aktualizovat na existující připojení.

## <a name="ipsecike-policy-faq"></a>Nejčastější dotazy týkající se zásad IPsec/IKE

[!INCLUDE [vpn-gateway-ipsecikepolicy-faq-include](../../includes/vpn-gateway-faq-ipsecikepolicy-include.md)]

## <a name="next-steps"></a>Další kroky

Podrobné pokyny ke konfiguraci vlastních zásad IPsec/IKE pro připojení najdete v tématu [Konfigurace zásad IPSec/IKE](vpn-gateway-ipsecikepolicy-rm-powershell.md) .

Další informace o možnosti UsePolicyBasedTrafficSelectors najdete taky v tématu [připojení několika zařízení VPN založených na zásadách](vpn-gateway-connect-multiple-policybased-rm-ps.md) .