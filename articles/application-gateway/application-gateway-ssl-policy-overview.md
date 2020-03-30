---
title: Přehled zásad TLS pro Aplikační bránu Azure
description: Zjistěte, jak nakonfigurovat zásady TLS pro Azure Application Gateway a snížit nároky na šifrování a dešifrování z back-endové serverové farmy.
services: application gateway
author: amsriva
ms.service: application-gateway
ms.topic: article
ms.date: 11/16/2019
ms.author: amsriva
ms.openlocfilehash: 871cb930e867002d8af1e7755de27d4873327543
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80257372"
---
# <a name="application-gateway-tls-policy-overview"></a>Přehled zásad TLS aplikační brány

Pomocí brány Aplikace Azure můžete centralizovat správu certifikátů TLS/SSL a snížit nároky na šifrování a dešifrování z back-endové serverové farmy. Toto centralizované zpracování TLS také umožňuje určit centrální zásady TLS, které jsou vhodné pro vaše požadavky na zabezpečení organizace. To vám pomůže splnit požadavky na dodržování předpisů, bezpečnostní pokyny a doporučené postupy.

Zásady TLS zahrnují řízení verze protokolu TLS, stejně jako šifrovací sady a pořadí, ve kterém jsou šifry používány během handshake TLS. Aplikační brána nabízí dva mechanismy pro řízení zásad TLS. Můžete použít předdefinovanou zásadu nebo vlastní zásadu.

## <a name="predefined-tls-policy"></a>Předdefinovaná zásada TLS

Aplikační brána má tři předdefinované zásady zabezpečení. Bránu můžete nakonfigurovat s některou z těchto zásad, abyste získali odpovídající úroveň zabezpečení. Názvy zásad jsou anotovány podle roku a měsíce, ve kterém byly nakonfigurovány. Každá zásada nabízí různé verze protokolu TLS a šifrovací sady. Doporučujeme používat nejnovější zásady TLS k zajištění nejlepšího zabezpečení TLS.

### <a name="appgwsslpolicy20150501"></a>AppGwSslPolicy20150501

|Vlastnost  |Hodnota  |
|---|---|
|Name (Název)     | AppGwSslPolicy20150501        |
|MinProtocolVersion     | TLSv1_0        |
|Výchozí| True (pokud není zadána žádná předdefinovaná zásada) |
|CipherSuites     |TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_DHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_DHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_DHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_DHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA<br>TLS_DHE_DSS_WITH_AES_256_CBC_SHA256<br>TLS_DHE_DSS_WITH_AES_128_CBC_SHA256<br>TLS_DHE_DSS_WITH_AES_256_CBC_SHA<br>TLS_DHE_DSS_WITH_AES_128_CBC_SHA<br>TLS_RSA_WITH_3DES_EDE_CBC_SHA<br>TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA |
  
### <a name="appgwsslpolicy20170401"></a>AppGwSslPolicy20170401
  
|Vlastnost  |Hodnota  |
|   ---      |  ---       |
|Name (Název)     | AppGwSslPolicy20170401        |
|MinProtocolVersion     | TLSv1_1        |
|Výchozí| False |
|CipherSuites     |TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA |
  
### <a name="appgwsslpolicy20170401s"></a>AppGwSslPolicy20170401S

|Vlastnost  |Hodnota  |
|---|---|
|Name (Název)     | AppGwSslPolicy20170401S        |
|MinProtocolVersion     | TLSv1_2        |
|Výchozí| False |
|CipherSuites     |TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 <br>    TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384 <br>    TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA <br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA <br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA<br> |

## <a name="custom-tls-policy"></a>Vlastní zásady TLS

Pokud je třeba nakonfigurovat předdefinovanou zásadu TLS pro vaše požadavky, musíte definovat vlastní zásady TLS. S vlastní zásady TLS máte úplnou kontrolu nad minimální verzí protokolu TLS pro podporu, stejně jako podporované šifrovací sady a jejich pořadí priorit.
 
### <a name="tlsssl-protocol-versions"></a>Verze protokolů TLS/SSL

* SSL 2.0 a 3.0 jsou ve výchozím nastavení zakázány pro všechny aplikační brány. Tyto verze protokolu nelze konfigurovat.
* Vlastní zásady TLS vám dávají možnost vybrat některý z následujících tří protokolů jako minimální verzi protokolu TLS pro vaši bránu: TLSv1_0, TLSv1_1 a TLSv1_2.
* Pokud není definována žádná zásada TLS, jsou povoleny všechny tři protokoly (TLSv1_0, TLSv1_1 a TLSv1_2).

### <a name="cipher-suites"></a>Šifrovací apartmány

Aplikační brána podporuje následující šifrovací sady, ze kterých si můžete vybrat vlastní zásady. Řazení šifrovacích sad určuje pořadí priorit během vyjednávání TLS.


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
> Šifrovací sady TLS používané pro připojení jsou také založeny na typu používaného certifikátu. V připojení klienta k bráně aplikace jsou použité šifrovací sady založeny na typu certifikátů serveru na naslouchacím procesu aplikační brány. V aplikaci gateway do back-endového fondu připojení, šifrovací sady používané jsou založeny na typu certifikátů serveru na serverech back-endfondu.

## <a name="known-issue"></a>Známý problém
Aplikační brána v2 aktuálně nepodporuje následující šifry:
- DHE-RSA-AES128-GCM-SHA256
- DHE-RSA-AES128-SHA
- DHE-RSA-AES256-GCM-SHA384
- DHE-RSA-AES256-SHA
- DHE-DSS-AES128-SHA256
- DHE-DSS-AES128-SHA
- DHE-DSS-AES256-SHA256
- DHE-DSS-AES256-SHA

## <a name="next-steps"></a>Další kroky

Pokud se chcete naučit konfigurovat zásady TLS, přečtěte si informace [o konfiguraci verzí zásad TLS a šifrovacích sad v application gateway](application-gateway-configure-ssl-policy-powershell.md).
