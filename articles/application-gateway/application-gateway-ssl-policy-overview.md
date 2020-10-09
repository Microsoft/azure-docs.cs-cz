---
title: Přehled zásad TLS pro Azure Application Gateway
description: Naučte se konfigurovat zásady TLS pro Azure Application Gateway a snížit náklady na šifrování a dešifrování ze serverové farmy back-end serveru.
services: application gateway
author: amsriva
ms.service: application-gateway
ms.topic: conceptual
ms.date: 11/16/2019
ms.author: amsriva
ms.openlocfilehash: 16c6dd28d47573c2ad5b0d5a331b0dc48e7aacef
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85253626"
---
# <a name="application-gateway-tls-policy-overview"></a>Přehled zásad Application Gateway TLS

Azure Application Gateway můžete použít k centralizaci správy certifikátů TLS/SSL a snížení režie šifrování a dešifrování ze serverové farmy back-end serveru. Toto centralizované zpracování TLS také umožňuje zadat centrální zásady TLS, které jsou vhodné pro vaše požadavky na zabezpečení vaší organizace. To vám pomůže splnit požadavky na dodržování předpisů a pokyny pro zabezpečení a Doporučené postupy.

Zásada TLS zahrnuje kontrolu verze protokolu TLS a šifrovací sady a pořadí, ve kterém se šifry používají při použití metody handshake TLS. Application Gateway nabízí dva mechanismy pro řízení zásad TLS. Můžete použít předdefinovanou zásadu nebo vlastní zásadu.

## <a name="predefined-tls-policy"></a>Předdefinované zásady TLS

Application Gateway má tři předdefinované zásady zabezpečení. Bránu můžete nakonfigurovat pomocí kterékoli z těchto zásad, abyste získali odpovídající úroveň zabezpečení. Názvy zásad jsou opatřeny poznámkami v roce a měsíci, ve kterých byly nakonfigurovány. Každá zásada nabízí různé verze protokolu TLS a šifrovací sady. Pro zajištění nejlepšího zabezpečení TLS doporučujeme použít nejnovější zásady TLS.

### <a name="appgwsslpolicy20150501"></a>AppGwSslPolicy20150501

|Vlastnost  |Hodnota  |
|---|---|
|Název     | AppGwSslPolicy20150501        |
|MinProtocolVersion     | TLSv1_0        |
|Výchozí| Pravda (pokud nejsou zadány žádné předdefinované zásady) |
|CipherSuites     |TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_DHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_DHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_DHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_DHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA<br>TLS_DHE_DSS_WITH_AES_256_CBC_SHA256<br>TLS_DHE_DSS_WITH_AES_128_CBC_SHA256<br>TLS_DHE_DSS_WITH_AES_256_CBC_SHA<br>TLS_DHE_DSS_WITH_AES_128_CBC_SHA<br>TLS_RSA_WITH_3DES_EDE_CBC_SHA<br>TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA |
  
### <a name="appgwsslpolicy20170401"></a>AppGwSslPolicy20170401
  
|Vlastnost  |Hodnota  |
|   ---      |  ---       |
|Název     | AppGwSslPolicy20170401        |
|MinProtocolVersion     | TLSv1_1        |
|Výchozí| Nepravda |
|CipherSuites     |TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA |
  
### <a name="appgwsslpolicy20170401s"></a>AppGwSslPolicy20170401S

|Vlastnost  |Hodnota  |
|---|---|
|Název     | AppGwSslPolicy20170401S        |
|MinProtocolVersion     | TLSv1_2        |
|Výchozí| Nepravda |
|CipherSuites     |TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 <br>    TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384 <br>    TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA <br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA <br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA<br> |

## <a name="custom-tls-policy"></a>Vlastní zásady TLS

Pokud je potřeba nakonfigurovat předdefinované zásady TLS pro vaše požadavky, musíte definovat vlastní zásady TLS. S vlastními zásadami TLS máte plnou kontrolu nad minimální verzí protokolu TLS, která se má podporovat, a také s podporovanými šifrovacími sadami a jejich pořadím priority.
 
### <a name="tlsssl-protocol-versions"></a>Verze protokolu TLS/SSL

* SSL 2,0 a 3,0 jsou ve výchozím nastavení pro všechny aplikační brány zakázané. Tyto verze protokolu se nedají konfigurovat.
* Vlastní zásada TLS vám umožní vybrat kterýkoli z následujících tří protokolů jako minimální verzi protokolu TLS pro bránu: TLSv1_0, TLSv1_1 a TLSv1_2.
* Pokud nejsou definované žádné zásady TLS, povolí se všechny tři protokoly (TLSv1_0, TLSv1_1 a TLSv1_2).

### <a name="cipher-suites"></a>Šifrovací sady

Application Gateway podporuje následující šifrovací sady, ze kterých můžete zvolit vlastní zásady. Řazení šifrovacích sad určuje pořadí priority během vyjednávání TLS.


- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_CBC_SHA
- TLS_DHE_RSA_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_AES_256_GCM_SHA384
- TLS_RSA_WITH_AES_128_GCM_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA256
- TLS_RSA_WITH_AES_128_CBC_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA
- TLS_RSA_WITH_AES_128_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_3DES_EDE_CBC_SHA
- TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA

> [!NOTE]
> Šifrovací sady TLS použité pro připojení jsou založené i na typu používaného certifikátu. V klientech připojení služby Application Gateway jsou použité šifrovací sady založené na typu certifikátů serveru v naslouchací službě Application Gateway. V případě připojení služby Application Gateway do fondu back-endu jsou použité šifrovací sady založené na typu certifikátů serveru na serverech fondu back-endu.

## <a name="known-issue"></a>Známý problém
Application Gateway V2 v současné době nepodporuje následující šifry:
- DHE-RSA-AES128-GCM-SHA256
- DHE-RSA-AES128-SHA
- DHE-RSA-AES256-GCM-SHA384
- DHE-RSA-AES256-SHA
- DHE-DSS-AES128-SHA256
- DHE-DSS-AES128-SHA
- DHE-DSS-AES256-SHA256
- DHE-DSS-AES256-SHA

## <a name="next-steps"></a>Další kroky

Pokud se chcete dozvědět, jak nakonfigurovat zásady TLS, přečtěte si téma [Konfigurace verzí zásad TLS a šifrovacích sad na Application Gateway](application-gateway-configure-ssl-policy-powershell.md).
