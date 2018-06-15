---
title: Přehled zásad SSL pro Azure Application Gateway | Microsoft Docs
description: Další informace o tom, jak Azure Application Gateway umožňuje konfigurovat zásady protokolu SSL
services: application gateway
documentationcenter: na
author: amsriva
manager: ''
editor: ''
tags: azure resource manager
ms.service: application gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure services
ms.date: 08/03/2017
ms.author: amsriva
ms.openlocfilehash: ec36af282bbfdc22ff88082412dd18cd2a85f245
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23836949"
---
# <a name="application-gateway-ssl-policy-overview"></a>Přehled zásad brány SSL aplikace

Azure Application Gateway můžete centralizovat správu certifikátu SSL a snížit režijní náklady na šifrování a dešifrování z farmy back-end serverů. Tento centrální SSL zpracování také umožňuje určit centrální zásady protokolu SSL, který je vhodný pro požadavky vaší organizace zabezpečení. To pomáhá splnit požadavky na dodržování předpisů, jakož i pokyny pro zabezpečení a doporučené postupy.

Zásady protokolu SSL zahrnuje kontrolu nad verzi protokolu SSL a také šifrovacích sad a pořadí, ve kterém se používají šifry během metody handshake pro protokol SSL. Aplikační brána nabízí dva mechanismy pro řízení zásady protokolu SSL. Můžete použít předdefinované zásady nebo vlastní zásady.

## <a name="predefined-ssl-policy"></a>Předdefinované zásady protokolu SSL

Aplikační brána obsahuje tři předdefinované zásady zabezpečení. Bránu můžete nakonfigurovat pomocí některé z těchto zásad získat odpovídající úroveň zabezpečení. Názvy zásad jsou opatřeny poznámkami ve za rok a měsíc, ve kterém byly nakonfigurovány. Každé zásady nabízí různé SSL protocol verze a šifrovací sady. Doporučujeme vám, že používáte nejnovější zásady protokolu SSL zajistit nejlepší zabezpečení SSL.

### <a name="appgwsslpolicy20150501"></a>AppGwSslPolicy20150501

|Vlastnost  |Hodnota  |
|---|---|
|Name (Název)     | AppGwSslPolicy20150501        |
|MinProtocolVersion     | TLSv1_0        |
|Výchozí| Hodnota TRUE, (Pokud je zadán žádné předdefinované zásady) |
|AES     |TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_DHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_DHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_DHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_DHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA<br>TLS_DHE_DSS_WITH_AES_256_CBC_SHA256<br>TLS_DHE_DSS_WITH_AES_128_CBC_SHA256<br>TLS_DHE_DSS_WITH_AES_256_CBC_SHA<br>TLS_DHE_DSS_WITH_AES_128_CBC_SHA<br>TLS_RSA_WITH_3DES_EDE_CBC_SHA<br>TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA |
  
  ### <a name="appgwsslpolicy20170401"></a>AppGwSslPolicy20170401
  
|Vlastnost  |Hodnota  |
|   ---      |  ---       |
|Name (Název)     | AppGwSslPolicy20170401        |
|MinProtocolVersion     | TLSv1_1        |
|Výchozí| False |
|AES     |TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA |
  
### <a name="appgwsslpolicy20170401s"></a>AppGwSslPolicy20170401S

|Vlastnost  |Hodnota  |
|---|---|
|Name (Název)     | AppGwSslPolicy20170401S        |
|MinProtocolVersion     | TLSv1_2        |
|Výchozí| False |
|AES     |TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 <br>    TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384 <br>    TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA <br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA <br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA<br> |

## <a name="custom-ssl-policy"></a>Vlastní zásady protokolu SSL

Pokud předdefinované zásady protokolu SSL je potřeba nakonfigurovat pro vaše požadavky, je nutné definovat vlastní vlastní zásady protokolu SSL. Pomocí vlastních zásad SSL máte plnou kontrolu nad minimální verzi protokolu SSL pro podporu, a také podporované šifrovací sady a jejich pořadí podle priority.
 
### <a name="ssl-protocol-versions"></a>Verze protokolu SSL

* Protokol SSL 2.0 a 3.0 jsou zakázány ve výchozím nastavení pro všechny application Gateway. Tyto verze protokolu se nedají konfigurovat.
* Vlastní zásady protokolu SSL vám dává možnost vybrat jednu z následujících tří protokolů jako minimální verzi protokolu SSL pro bránu: TLSv1_0, TLSv1_1 a TLSv1_2.
* Pokud není nadefinovaná žádná zásada protokolu SSL, jsou povolené všechny tři protokoly (TLSv1_0, TLSv1_1 a TLSv1_2).

### <a name="cipher-suites"></a>Šifrovací sady

Aplikační brána podporuje následující šifrovací sady, ze kterých můžete vlastní zásady. Při vyjednávání SSL řazení šifrovací sada určuje pořadí podle priority.


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

## <a name="next-steps"></a>Další kroky

Pokud chcete získat další konfigurace zásady protokolu SSL, najdete v článku [zásady Konfigurace protokolu SSL na aplikační brány](application-gateway-configure-ssl-policy-powershell.md).
