---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 857d29f407c9939143fbb8263be40dadb040efdc
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/15/2018
ms.locfileid: "53439213"
---
[!INCLUDE [P2S FAQ All](vpn-gateway-faq-p2s-all-include.md)]

### <a name="is-radius-authentication-supported-on-all-azure-vpn-gateway-skus"></a>Podporuje se ověřování pomocí protokolu RADIUS ve všech skladových jednotkách (SKU) služby Azure VPN Gateway?

Ověřování pomocí protokolu RADIUS se podporuje pro SKU VpnGw1, VpnGw2 a VpnGw3. Pokud používáte starší verze SKU, podporuje se ověřování pomocí protokolu RADIUS u SKU pro standardní a vysoký výkon. Nepodporuje se u skladové jednotky SKU brány úrovně Basic. 
 
### <a name="is-radius-authentication-supported-for-the-classic-deployment-model"></a>Podporuje se ověřování pomocí protokolu RADIUS u klasického modelu nasazení?
 
Ne. Ověřování pomocí protokolu RADIUS se u klasického modelu nasazení nepodporuje.
 
### <a name="are-3rd-party-radius-servers-supported"></a>Podporují se servery RADIUS třetích stran?

Ano, servery RADIUS třetích stran se podporují.
 
### <a name="what-are-the-connectivity-requirements-to-ensure-that-the-azure-gateway-is-able-to-reach-an-on-premises-radius-server"></a>Jaké jsou požadavky na připojení, aby se zajistilo, že se brána Azure může spojit s místním serverem RADIUS?

Je nutné připojení VPN typu Site-to-Site k místní lokalitě, a to se správně nakonfigurovanými trasami.  
 
### <a name="can-traffic-to-an-on-premises-radius-server-from-the-azure-vpn-gateway-be-routed-over-an-expressroute-connection"></a>Je možné směrovat provoz do místního serveru RADIUS server (ze služby Azure VPN Gateway) přes připojení ExpressRoute?

Ne. Směrovat je možné jenom přes připojení typu Site-to-Site.
 
### <a name="is-there-a-change-in-the-number-of-sstp-connections-supported-with-radius-authentication-what-is-the-maximum-number-of-sstp-and-ikev2-connections-supported"></a>Došlo ke změně počtu připojení SSTP podporovaných ověřováním pomocí protokolu RADIUS? Jaký je maximální podporovaný počet připojení SSTP a IKEv2?

K žádné změně maximálního počtu připojení SSTP podporovaných ověřováním pomocí protokolu RADIUS nedošlo. Jich je 128 pro SSTP, ale závisí na SKU brány pro IKEv2. Další informace o počtu připojení podporovány, naleznete v tématu [skladové položky brány](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku).
 
### <a name="what-is-the-difference-between-doing-certificate-authentication-using-a-radius-server-vs-using-azure-native-certificate-authentication-by-uploading-a-trustedcertificate-to-azure"></a>Jaký je rozdíl mezi ověřováním certifikátů pomocí protokolu RADIUS a pomocí nativní ověřování certifikátů Azure (nahráním důvěryhodného certifikátu do Azure).

Při ověřování certifikátů pomocí protokolu RADIUS se žádost o ověření předá serveru RADIUS, který zpracuje vlastní ověření certifikátu. Tato možnost je užitečná, pokud chcete využít integraci s infrastrukturou ověřování certifikátů, kterou již prostřednictvím protokolu RADIUS máte.
  
Při použití Azure k ověřování certifikátů provádí ověření certifikátu služba Azure VPN Gateway. Do brány musíte nahrát veřejný klíč certifikátu. Můžete také zadat seznam odvolaných certifikátů, kterým by nemělo být povoleno připojení.

### <a name="does-radius-authentication-work-with-both-ikev2-and-sstp-vpn"></a>Funguje ověřování RADIUS s IKEv2 i SSTP VPN?

Ano, ověřování RADIUS je podporované jak pro IKEv2, tak i pro SSTP VPN. 
