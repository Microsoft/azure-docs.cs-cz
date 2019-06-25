---
title: Přehled zásad protokolu SSL pro službu Azure Application Gateway | Dokumentace Microsoftu
description: Další informace o tom, jak Azure Application Gateway umožňuje konfigurovat zásady protokolu SSL
services: application gateway
documentationcenter: na
author: amsriva
manager: ''
editor: ''
tags: azure resource manager
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure services
ms.date: 08/03/2017
ms.author: amsriva
ms.openlocfilehash: 46a823e4e230656b53a93a97f195d0879fd08bf2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "62122230"
---
# <a name="application-gateway-ssl-policy-overview"></a>Přehled zásad protokolu SSL bránu aplikace

Azure Application Gateway můžete centralizovat správu certifikátů SSL a snížit režijní náklady na šifrování a dešifrování z back endovém serveru farmy. Tento centrální SSL zpracování také umožňuje určit centrální zásady protokolu SSL, který je vhodný pro požadavky vaší organizace na zabezpečení. To pomáhá splnit požadavky na dodržování předpisů, jakož i pokyny pro zabezpečení a doporučené postupy.

Zásady protokolu SSL obsahuje ovládací prvek verze protokolu SSL a šifrovacích sad a pořadí, ve kterém jsou použity šifry během ověření typu handshake SSL. Služba Application Gateway nabízí dva mechanismy pro řízení zásad protokolu SSL. Můžete použít předdefinované zásady nebo vlastní zásadu.

## <a name="predefined-ssl-policy"></a>Předdefinované zásady protokolu SSL

Služba Application Gateway obsahuje tři předdefinované zásady zabezpečení. Nakonfigurujte bránu s jakékoli z těchto zásad na odpovídající úroveň zabezpečení. Názvy zásad je anotována podle roku a měsíce, ve kterém byly nakonfigurovány. Každé zásady nabízí různé SSL protokol verze a šifrovacích sad. Doporučujeme použít nejnovější zásady SSL zajistit nejlepší zabezpečení SSL.

### <a name="appgwsslpolicy20150501"></a>AppGwSslPolicy20150501

|Vlastnost  |Hodnota  |
|---|---|
|Name     | AppGwSslPolicy20150501        |
|MinProtocolVersion     | TLSv1_0        |
|Výchozí| True (Pokud je zadáno žádné předdefinované zásady) |
|CipherSuites     |TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_DHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_DHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_DHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_DHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA<br>TLS_DHE_DSS_WITH_AES_256_CBC_SHA256<br>TLS_DHE_DSS_WITH_AES_128_CBC_SHA256<br>TLS_DHE_DSS_WITH_AES_256_CBC_SHA<br>TLS_DHE_DSS_WITH_AES_128_CBC_SHA<br>TLS_RSA_WITH_3DES_EDE_CBC_SHA<br>TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA |
  
### <a name="appgwsslpolicy20170401"></a>AppGwSslPolicy20170401
  
|Vlastnost  |Hodnota  |
|   ---      |  ---       |
|Name     | AppGwSslPolicy20170401        |
|MinProtocolVersion     | TLSv1_1        |
|Výchozí| False |
|CipherSuites     |TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA |
  
### <a name="appgwsslpolicy20170401s"></a>AppGwSslPolicy20170401S

|Vlastnost  |Hodnota  |
|---|---|
|Název     | AppGwSslPolicy20170401S        |
|MinProtocolVersion     | TLSv1_2        |
|Výchozí| False |
|CipherSuites     |TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 <br>    TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384 <br>    TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA <br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA <br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA<br> |

## <a name="custom-ssl-policy"></a>Vlastní zásady protokolu SSL

Pokud předdefinované zásady protokolu SSL je potřeba nakonfigurovat pro vaše požadavky, je nutné definovat vlastní vlastní zásady protokolu SSL. Pomocí vlastních zásad protokolu SSL máte plnou kontrolu nad minimální verze protokolu SSL pro podporu, a také podporovaná šifrovací sada a jejich pořadí podle priority.
 
### <a name="ssl-protocol-versions"></a>Verze protokolu SSL

* Protokol SSL 2.0 a 3.0 jsou zakázané ve výchozím nastavení pro všechny brány application Gateway. Tyto verze protokolu se nedají konfigurovat.
* Vlastní zásady protokolu SSL umožňuje vybrat některou z těchto tří protokolů jako minimální verzi protokolu SSL pro bránu: TLSv1_0, TLSv1_1, TLSv1_2 a přání.
* Pokud není nadefinovaná žádná zásada SSL, jsou povolené všechny tři protokoly (TLSv1_0, TLSv1_1 a TLSv1_2).

### <a name="cipher-suites"></a>Šifrovací sady

Služba Application Gateway podporuje následující šifrovací sada, z nichž můžete použít vlastní zásady. Řazení šifrovací sada určuje pořadí podle priority při vyjednávání protokolu SSL.


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

## <a name="next-steps"></a>Další postup

Pokud chcete další informace o konfiguraci zásad protokolu SSL najdete [zásady Konfigurace protokolu SSL ve službě application gateway](application-gateway-configure-ssl-policy-powershell.md).
