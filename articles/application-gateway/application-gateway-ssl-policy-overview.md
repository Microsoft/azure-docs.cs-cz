---
title: Přehled zásad TLS pro Azure Application Gateway
description: Naučte se konfigurovat zásady TLS pro Azure Application Gateway a snížit náklady na šifrování a dešifrování ze serverové farmy back-end serveru.
services: application gateway
author: amsriva
ms.service: application-gateway
ms.topic: conceptual
ms.date: 12/17/2020
ms.author: amsriva
ms.openlocfilehash: 77239cd8586b8fb07abf6862be436979541bdb99
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2020
ms.locfileid: "97631686"
---
# <a name="application-gateway-tls-policy-overview"></a>Přehled zásad Application Gateway TLS

Azure Application Gateway můžete použít k centralizaci správy certifikátů TLS/SSL a snížení režie šifrování a dešifrování ze serverové farmy back-end serveru. Toto centralizované zpracování TLS také umožňuje zadat centrální zásady TLS, které jsou vhodné pro vaše požadavky na zabezpečení vaší organizace. To vám pomůže splnit požadavky na dodržování předpisů a pokyny pro zabezpečení a Doporučené postupy.

Zásada TLS zahrnuje kontrolu verze protokolu TLS a šifrovací sady a pořadí, ve kterém se šifry používají při použití metody handshake TLS. Application Gateway nabízí dva mechanismy pro řízení zásad TLS. Můžete použít předdefinovanou zásadu nebo vlastní zásadu.

## <a name="predefined-tls-policy"></a>Předdefinované zásady TLS

Application Gateway má tři předdefinované zásady zabezpečení. Bránu můžete nakonfigurovat pomocí kterékoli z těchto zásad, abyste získali odpovídající úroveň zabezpečení. Názvy zásad jsou opatřeny poznámkami v roce a měsíci, ve kterých byly nakonfigurovány. Každá zásada nabízí různé verze protokolu TLS a šifrovací sady. Pro zajištění nejlepšího zabezpečení TLS doporučujeme použít nejnovější zásady TLS.

## <a name="known-issue"></a>Známý problém
Application Gateway v2 nepodporuje následující šifry DHE a nepoužívají se pro připojení TLS s klienty, i když jsou zmíněné v předdefinovaných zásadách. Místo DHEch šifr jsou doporučovány zabezpečené a rychlejší šifry ECDH.

- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_128_CBC_SHA
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_DHE_RSA_WITH_AES_256_CBC_SHA
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA

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

> [!IMPORTANT]
> Pokud používáte vlastní zásadu protokolu SSL v Application Gateway verze V1 SKU (Standard nebo WAF), ujistěte se, že do seznamu přidáte povinnou šifru "TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256". Tato šifra je nutná k povolení metrik a protokolování SKU Application Gateway v1.
> Toto není povinné pro Application Gateway verze V2 SKU (Standard_v2 nebo WAF_v2).
 
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

## <a name="next-steps"></a>Další kroky

Pokud se chcete dozvědět, jak nakonfigurovat zásady TLS, přečtěte si téma [Konfigurace verzí zásad TLS a šifrovacích sad na Application Gateway](application-gateway-configure-ssl-policy-powershell.md).
