---
title: Hlavičky HTTP specifické pro verizon pro modul pravidel Azure CDN | Dokumenty společnosti Microsoft
description: Tento článek popisuje, jak používat hlavičky HTTP specifické pro Verizon s modulem pravidel Azure CDN.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: magattus
ms.openlocfilehash: a5881bea578f2791f8dc0d6e760fd15c6f47e435
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67593250"
---
# <a name="verizon-specific-http-headers-for-azure-cdn-rules-engine"></a>Hlavičky HTTP specifické pro Verizon pro modul pravidel Azure CDN

Pro **Azure CDN Premium od** produktů Verizon při odeslání požadavku HTTP na zdrojový server může server point-of-presence (POP) přidat jednu nebo více vyhrazených hlaviček (nebo speciálních záhlaví proxy serveru) do požadavku klienta do protokolu POP. Tato záhlaví jsou navíc ke standardním přijatým záhlavím předávání. Informace o hlavičkách standardních požadavků naleznete v [tématu Request fields](https://en.wikipedia.org/wiki/List_of_HTTP_header_fields#Request_fields).

Pokud chcete zabránit přidání jedné z těchto vyhrazených záhlaví do požadavku POP Azure CDN (Content Delivery Network) na zdrojový server, musíte vytvořit pravidlo s [funkcí Proxy Special Headers](cdn-verizon-premium-rules-engine-reference-features.md#proxy-special-headers) v modulu pravidel. V tomto pravidle vylučte záhlaví, které chcete odebrat, z výchozího seznamu záhlaví v poli záhlaví. Pokud jste povolili [funkci Ladění záhlaví odpovědí mezipaměti](cdn-verizon-premium-rules-engine-reference-features.md#debug-cache-response-headers), `X-EC-Debug` nezapomeňte přidat potřebná záhlaví. 

Chcete-li například `Via` odebrat záhlaví, mělo by pole záhlaví pravidla obsahovat následující seznam záhlaví: *X-Forwarded-For, X-Forwarded-Proto, X-Host, X-Midgress, X-Gateway-List, X-EC-Name, Host*. 

![Pravidlo Speciální záhlaví proxy serveru](./media/cdn-http-headers/cdn-proxy-special-header-rule.png)

Následující tabulka popisuje záhlaví, která mohou být přidána do protokolu POP VERIZON CDN POP v požadavku:

Hlavička požadavku | Popis | Příklad
---------------|-------------|--------
[Via](#via-request-header) | Identifikuje server POP, který proxied požadavek na zdrojový server. | HTTP/1.1 ECS (dca/1A2B)
X-Forwarded-Pro | Označuje IP adresu uchazeče.| 10.10.10.10
X-Forwarded-Proto | Označuje protokol požadavku. | HTTP
X-Hostitel | Označuje název hostitele požadavku. | cdn.mydomain.com
X-Středový průnik | Označuje, zda byl požadavek proxied prostřednictvím dalšího serveru CDN. Například štítový server POP-to-origin nebo server brány POP-to-ADN. <br />Tato hlavička je přidána do požadavku pouze v případě, že dojde k přenosu mezipřenosy. V tomto případě je záhlaví nastaveno na 1, což znamená, že požadavek byl proxied prostřednictvím dalšího serveru CDN.| 1
[Hostitel](#host-request-header) | Identifikuje hostitele a port, kde může být nalezen požadovaný obsah. | marketing.mydomain.com:80
[Seznam brány X](#x-gateway-list-request-header) | ADN: Identifikuje seznam převzetí služeb při selhání serverů brány ADN přiřazených k původu zákazníka. <br />Štít původu: Označuje sadu serverů štítu původu přiřazených k původu zákazníka. | `icn1,hhp1,hnd1`
X-EC-_&lt;název&gt;_ | Hlavičky požadavků, které začínají *x-EC* (například X-EC-Tag, [X-EC-Debug](cdn-http-debug-headers.md)) jsou vyhrazeny pro použití CDN.| výroba waf

## <a name="via-request-header"></a>Hlavička požadavku
Formát, jehož `Via` prostřednictvím hlavička požadavku identifikuje server POP, je určen následující syntaxí:

`Via: Protocol from Platform (POP/ID)` 

Termíny použité v syntaxi jsou definovány takto:
- Protokol: Označuje verzi protokolu (například HTTP/1.1) použitou k proxy požadavku. 

- Platforma: Označuje platformu, na které byl požadován obsah. Pro toto pole platí následující kódy: 

    kód | Platforma
    -----|---------
    ECAcc | HTTP velké
    Ecs   | HTTP malé
    Ecd   | Síť pro doručování aplikací (ADN)

- POP: Označuje [protokol POP,](cdn-pop-abbreviations.md) který požadavek zpracoval. 

- ID: Pouze pro interní použití.

### <a name="example-via-request-header"></a>Příklad Záhlaví požadavku

`Via: HTTP/1.1 ECD (dca/1A2B)`

## <a name="host-request-header"></a>Hlavička požadavku hostitele
Servery POP přepíší `Host` záhlaví, pokud jsou splněny obě následující podmínky:
- Zdrojem požadovaného obsahu je zdrojový server zákazníka.
- Možnost hlavičky hostitele HTTP odpovídajícího původu zákazníka není prázdná.

Hlavička `Host` požadavku bude přepsána tak, aby odrážela hodnotu definovanou v možnosti Hlavička hostitele HTTP.
Pokud je možnost hlavičky hostitele HTTP původu zákazníka `Host` nastavena na prázdnou, bude hlavička požadavku odeslaná žadatelem předána na zdrojový server zákazníka.

## <a name="x-gateway-list-request-header"></a>Hlavička požadavku X-Gateway-List
Server POP přidá nebo přepíše hlavičku požadavku X-Gateway-List, pokud je splněna některá z následujících podmínek:
- Požadavek odkazuje na platformu ADN.
- Požadavek je předán serveru původu zákazníka, který je chráněn funkcí Origin Shield.

