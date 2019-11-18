---
title: Přehled zásad protokolu SSL pro Azure Application Gateway
description: Naučte se konfigurovat zásady SSL pro Azure Application Gateway a snížit zatížení a dešifrovací režii z back-endové serverové farmy.
services: application gateway
author: amsriva
ms.service: application-gateway
ms.topic: article
ms.date: 11/16/2019
ms.author: amsriva
ms.openlocfilehash: 6b3d52f1f6bd0390ab6ccafa80b2979cb0e498fd
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/16/2019
ms.locfileid: "74130404"
---
# <a name="application-gateway-ssl-policy-overview"></a>Application Gateway Přehled zásad protokolu SSL

Azure Application Gateway můžete použít k centralizaci správy certifikátů SSL a snížení režie šifrování a dešifrování ze serverové farmy back-end serveru. Toto centralizované zpracování protokolu SSL také umožňuje zadat centrální zásady protokolu SSL, které jsou vhodné pro vaše požadavky na zabezpečení vaší organizace. To vám pomůže splnit požadavky na dodržování předpisů a pokyny pro zabezpečení a Doporučené postupy.

Zásada protokolu SSL zahrnuje kontrolu verze protokolu SSL a šifrovací sady a pořadí, ve kterém se šifry používají při ověřování SSL. Application Gateway nabízí dva mechanismy pro řízení zásad protokolu SSL. Můžete použít předdefinovanou zásadu nebo vlastní zásadu.

## <a name="predefined-ssl-policy"></a>Předdefinované zásady SSL

Application Gateway má tři předdefinované zásady zabezpečení. Bránu můžete nakonfigurovat pomocí kterékoli z těchto zásad, abyste získali odpovídající úroveň zabezpečení. Názvy zásad jsou opatřeny poznámkami v roce a měsíci, ve kterých byly nakonfigurovány. Každá zásada nabízí různé verze protokolu SSL a šifrovací sady. Pro zajištění nejlepšího zabezpečení SSL doporučujeme použít nejnovější zásady SSL.

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

## <a name="custom-ssl-policy"></a>Vlastní zásada SSL

Pokud je potřeba nakonfigurovat předdefinované zásady SSL pro vaše požadavky, musíte definovat vlastní zásadu protokolu SSL. Díky vlastním zásadám SSL máte plnou kontrolu nad minimální verzí protokolu SSL, která se má podporovat, a také s podporovanými šifrovacími sadami a jejich pořadím priority.
 
### <a name="ssl-protocol-versions"></a>Verze protokolu SSL

* SSL 2,0 a 3,0 jsou ve výchozím nastavení pro všechny aplikační brány zakázané. Tyto verze protokolu se nedají konfigurovat.
* Vlastní zásada SSL vám umožní vybrat libovolný z následujících tří protokolů jako minimální verzi protokolu SSL pro bránu: TLSv1_0, TLSv1_1 a TLSv1_2.
* Pokud nejsou definované žádné zásady protokolu SSL, jsou povolené všechny tři protokoly (TLSv1_0, TLSv1_1 a TLSv1_2).

### <a name="cipher-suites"></a>Šifrovací sady

Application Gateway podporuje následující šifrovací sady, ze kterých můžete zvolit vlastní zásady. Řazení šifrovacích sad určuje pořadí priority během vyjednávání SSL.


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
> Šifrovací sady SSL použité pro připojení jsou založené i na typu používaného certifikátu. V klientech připojení služby Application Gateway jsou použité šifrovací sady založené na typu certifikátů serveru v naslouchací službě Application Gateway. V případě připojení služby Application Gateway do fondu back-endu jsou použité šifrovací sady založené na typu certifikátů serveru na serverech fondu back-endu.

## <a name="next-steps"></a>Další kroky

Pokud se chcete dozvědět, jak nakonfigurovat zásadu protokolu SSL, přečtěte si téma [Konfigurace zásad protokolu SSL na aplikační bráně](application-gateway-configure-ssl-policy-powershell.md).
