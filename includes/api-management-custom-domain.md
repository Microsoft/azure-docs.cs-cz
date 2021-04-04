---
author: vladvino
ms.service: api-management
ms.topic: include
ms.date: 11/09/2018
ms.author: vlvinogr
ms.openlocfilehash: 1858317d40efa59b188ce894534be93a1f11b287
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96027397"
---
## <a name="how-apim-proxy-server-responds-with-ssl-certificates-in-the-tls-handshake"></a>Jak proxy server APIM reaguje na metodu handshake protokolu TLS odesláním certifikátů SSL

### <a name="clients-calling-with-sni-header"></a>Klienti volající s hlavičkou SNI
Pokud má zákazník minimálně jednu vlastní doménu nakonfigurovanou pro proxy, APIM může reagovat na požadavky HTTPS z vlastní domény (třeba contoso.com) a také jako výchozí doménu (například apim-service-name.azure-api.net). Na základě informací v hlavičce Indikace názvu serveru (SNI) APIM odpoví příslušným certifikátem serveru.

### <a name="clients-calling-without-sni-header"></a>Klienti volající bez hlavičky SNI
Pokud zákazník používá klienta, který neodesílá hlavičku [sni](https://tools.ietf.org/html/rfc6066#section-3) , vytvoří APIM odpovědi na základě následující logiky:

* Pokud má služba jenom jednu vlastní doménu nakonfigurovanou pro proxy, výchozí certifikát je certifikát, který byl vydán pro vlastní doménu proxy serveru.
* Pokud služba nakonfigurovala více vlastních domén pro proxy (podporované v úrovni **Developer** a **Premium** ), může zákazník určit, který certifikát by měl být výchozím certifikátem. Chcete-li nastavit výchozí certifikát, vlastnost [defaultSslBinding](/rest/api/apimanagement/2019-12-01/apimanagementservice/createorupdate#hostnameconfiguration) by měla být nastavena na hodnotu true ("defaultSslBinding": "true"). Pokud zákazník tuto vlastnost nenastaví, výchozí certifikát je certifikát vydaný výchozí proxy doméně hostované v umístění *. azure-api.net.

## <a name="support-for-putpost-request-with-large-payload"></a>Podpora pro požadavek PUT/POST s velkou datovou částí

APIM proxy server podporuje požadavek s velkou datovou částí při použití certifikátů na straně klienta v protokolu HTTPS (například datová část > 40 KB). Aby se zabránilo zamrznutí požadavku serveru, zákazníci můžou nastavit vlastnost ["negotiateClientCertificate": "true"](/rest/api/apimanagement/2019-12-01/ApiManagementService/CreateOrUpdate#hostnameconfiguration) na názvu hostitele proxy serveru. Pokud je vlastnost nastavena na hodnotu true, klientský certifikát je požadován v době připojení SSL/TLS před všemi výměnami požadavků HTTP. Vzhledem k tomu, že nastavení platí na úrovni **hostitele proxy** , všechny požadavky na připojení žádají o certifikát klienta. Zákazníci můžou nakonfigurovat až 20 vlastních domén pro proxy (podporované jenom na úrovni **Premium** ) a toto omezení obejít.