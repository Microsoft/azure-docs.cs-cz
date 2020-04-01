---
author: vladvino
ms.service: api-management
ms.topic: include
ms.date: 11/09/2018
ms.author: vlvinogr
ms.openlocfilehash: b9e601c72395b4910850714460321a83a3113e69
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77649533"
---
## <a name="how-apim-proxy-server-responds-with-ssl-certificates-in-the-tls-handshake"></a>Jak proxy server APIM reaguje na metodu handshake protokolu TLS odesláním certifikátů SSL

### <a name="clients-calling-with-sni-header"></a>Klienti volající s hlavičkou SNI
Pokud má zákazník jednu nebo více vlastních domén nakonfigurovaných pro proxy server, apim může reagovat na požadavky HTTPS z vlastní domény (například contoso.com) a také na výchozí doménu (například apim-service-name.azure-api.net). Na základě informací v hlavičce SNI (Název serveru) apim odpoví příslušným certifikátem serveru.

### <a name="clients-calling-without-sni-header"></a>Klienti volající bez hlavičky SNI
Pokud zákazník používá klienta, který neodesílá hlavičku [SNI,](https://tools.ietf.org/html/rfc6066#section-3) APIM vytvoří odpovědi na základě následující logiky:

* Pokud má služba pouze jednu vlastní doménu nakonfigurovanou pro proxy server, je výchozí certifikát certifikátem vydaným vlastní doméně proxy serveru.
* Pokud služba nakonfigurovala více vlastních domén pro proxy server (podporované na úrovni **Vývojář** a **Premium),** může zákazník určit, který certifikát by měl být výchozím certifikátem. Chcete-li nastavit výchozí certifikát, [defaultSslBinding](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/apimanagementservice/createorupdate#hostnameconfiguration) vlastnost by měla být nastavena na hodnotu true ("defaultSslBinding":"true"). Pokud zákazník vlastnost nenastaví, je výchozím certifikátem certifikát vydaný výchozí doméně proxy hostované na adrese *.azure-api.net.

## <a name="support-for-putpost-request-with-large-payload"></a>Podpora pro požadavek PUT/POST s velkým nosným zatížením

Server proxy APIM podporuje požadavek s velkou datovou částí při použití certifikátů na straně klienta v protokolu HTTPS (například datové části > 40 kB). Chcete-li zabránit zamrznutí požadavku serveru, mohou zákazníci nastavit vlastnost [negotiateClientCertificate: "true"](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/ApiManagementService/CreateOrUpdate#hostnameconfiguration) na názvu hostitele proxy. Pokud je vlastnost nastavena na hodnotu true, klientský certifikát je požadován v době připojení SSL/TLS před výměnou požadavků HTTP. Vzhledem k tomu, že nastavení platí na úrovni **proxy hostname,** všechny požadavky na připojení požádat o klientský certifikát. Zákazníci mohou nakonfigurovat až 20 vlastních domén pro proxy server (podporované pouze na úrovni **Premium)** a toto omezení obejít.

