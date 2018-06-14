---
title: Verizon konkrétní hlavičky protokolu HTTP pro stroj pravidel Azure CDN | Microsoft Docs
description: Tento článek popisuje způsob použití hlaviček protokolu HTTP specifické Verizon s stroj pravidel Azure CDN.
services: cdn
documentationcenter: ''
author: dksimpson
manager: akucer
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: v-deasim
ms.openlocfilehash: 1a4bb48efe2a91c85b773341bb38b0f3ce4c7d9b
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
ms.locfileid: "31517447"
---
# <a name="verizon-specific-http-headers-for-azure-cdn-rules-engine"></a>Verizon konkrétní hlavičky protokolu HTTP pro stroj pravidel Azure CDN

Pro **Azure CDN Premium od společnosti Verizon** produktů, pokud požadavek HTTP je odeslána na zdrojový server serverem bodů přítomnosti (POP) můžete přidat jeden nebo více vyhrazené hlavičky (nebo speciálními záhlavími proxy) v požadavku klienta protokolu POP. Kromě standardní předávání hlavičky přijatých jsou tyto hlavičky. Informace o standardní žádost o hlavičkách najdete v tématu [požadavku pole](https://en.wikipedia.org/wiki/List_of_HTTP_header_fields#Request_fields).

Pokud chcete zabránit jednu z těchto vyhrazené hlaviček přidávání v požadavku POP Azure CDN (Content Delivery Network) na zdrojový server, musíte vytvořit pravidlo s [funkce Proxy speciálními záhlavími](cdn-rules-engine-reference-features.md#proxy-special-headers) v stroj pravidel. V tomto pravidle vylučte hlavičky, kterou chcete odebrat ze seznamu výchozí hlavičky v poli hlavičky. Pokud jste povolili [funkce ladění hlavičky odpovědi mezipaměti](cdn-rules-engine-reference-features.md#debug-cache-response-headers), nezapomeňte přidat nezbytné `X-EC-Debug` hlavičky. 

Například odebrat `Via` záhlaví, pole hlavičky pravidla by měla obsahovat následující seznam hlaviček: *X-předávaných-pro, X-předávaných-proto –, X-hostitel, X-Midgress X-brány-List X-ES-Name, Host*. 

![Pravidlo speciálními záhlavími proxy serveru](./media/cdn-http-headers/cdn-proxy-special-header-rule.png)

Následující tabulka popisuje seznam hlaviček, které přidáte tak, že Verizon CDN POP v požadavku:

Hlavička požadavku | Popis | Příklad:
---------------|-------------|--------
[prostřednictvím](#via-request-header) | Identifikuje server POP, který směrovány přes proxy server požadavek na původním serveru. | ECS HTTP/1.1 (dca/1A2B)
X-předávaných pro | Označuje žadatele IP adresu.| 10.10.10.10
X předávaných Proto | Určuje protokol žádosti. | http
X-hostitel | Určuje název hostitele žádosti. | CDN.mydomain.com
X Midgress | Určuje, zda byla žádost směrovány přes proxy server pomocí dalšího serveru CDN. Například server počátek štítu serveru POP nebo server brány serveru ADN POP. <br />Tuto hlavičku se přidá na požadavek pouze v případě, že midgress provoz probíhá. V takovém případě záhlaví nastavena na hodnotu 1 k označení, že žádost byla směrovány přes proxy server pomocí dalšího serveru CDN.| 1
[Hostitel](#host-request-header) | Identifikuje hostitele a port, kde lze najít požadovaný obsah. | marketing.mydomain.com:80
[Seznam X brány](#x-gateway-list-request-header) | ADN: Identifikuje převzetí služeb při selhání seznam serverů brány ADN přiřazené počátek zákazníka. <br />Původ štítu: označuje sadu počátek štítu servery přiřazené pro počátek zákazníka. | `icn1,hhp1,hnd1`
X-ES -_&lt;název&gt;_ | Hlavičky žádosti, které začínají *X-ES* (například X-ES-Tag, [X-ES-Debug](cdn-http-debug-headers.md)) jsou rezervovány pro použití ve CDN.| produkční firewall webových aplikací

## <a name="via-request-header"></a>Prostřednictvím hlavička požadavku
Formát, pomocí kterého `Via` záhlaví identifikuje POP server žádost je zadána následující syntaxi:

`Via: Protocol from Platform (POP/ID)` 

Termínů používaných v syntaxi jsou definovány takto:
- Protokol: Označuje, že verze protokolu (například HTTP/1.1) použita k proxy serveru žádosti. 

- Platforma: Označuje platformy, na kterém byl požadovaný obsah. Následující kódy jsou platné pro pole: 
    Kód | Platforma
    -----|---------
    Banky ECAcc | Velké HTTP
    ECS   | Malá HTTP
    ECD   | Sítě pro doručování aplikace (ADN)

- POP: Označuje [POP](cdn-pop-abbreviations.md) který zpracovává žádosti. 

- ID: pouze pro interní použití.

### <a name="example-via-request-header"></a>Příklad prostřednictvím hlavička požadavku

`Via: HTTP/1.1 ECD (dca/1A2B)`

## <a name="host-request-header"></a>Hlavička hostitele požadavku
Servery POP přepíše `Host` záhlaví, pokud jsou splněny obě následující podmínky:
- Zdroj pro požadovaný obsah je zdrojový server zákazníka.
- Možnost Hlavička hostitele HTTP odpovídající zákazníka počátek není prázdný.

`Host` Hlavičky žádosti budou přepsány tak, aby odrážela hodnota definovaná v možnosti hostitele hlavičky protokolu HTTP.
Pokud možnost HTTP hlavičku hostitele pro počátek zákazníka je nastavena na prázdný, pak `Host` hlavička požadavku, který je odeslaná žadateli se předají zákazníka zdrojový server.

## <a name="x-gateway-list-request-header"></a>Hlavička požadavku seznam X brány
Serveru POP přidat/přepíše ' X seznam brány hlavička požadavku, pokud některá z následujících podmínek jsou splněny:
- Žádost se odkazuje na ADN platformy.
- Žádost se předají zákazníka zdrojový server, který je chráněný pomocí funkce štítu původu.

