---
title: Hlavičky HTTP specifické pro Verizon pro stroj pravidel Azure CDN | Dokumentace Microsoftu
description: Tento článek popisuje, jak pomocí Azure CDN stroj pravidel hlavičky HTTP specifické pro Verizon.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: magattus
ms.openlocfilehash: 7fa76a2c5b01e623e490edd0091f7fb372b7085f
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2018
ms.locfileid: "49093234"
---
# <a name="verizon-specific-http-headers-for-azure-cdn-rules-engine"></a>Hlavičky HTTP specifické pro Verizon pro Azure CDN stroje pravidel

Pro **Azure CDN Premium od Verizonu** produkty, když požadavek HTTP je odeslána na server původu, point-of-presence serveru (POP) můžete přidat jeden nebo více rezervovaných hlavičky (nebo speciálními záhlavími proxy serveru) v požadavku klienta protokolu POP služby. Tyto hlavičky doplňují standard předávání přijaté záhlaví. Informace o standardní žádosti o hlavičkách najdete v tématu [žádosti pole](https://en.wikipedia.org/wiki/List_of_HTTP_header_fields#Request_fields).

Pokud chcete zabránit jednu z těchto rezervovaných záhlaví přidávání v žádosti o Azure CDN (Content Delivery Network) POP na zdrojový server, musíte vytvořit pravidlo s [funkce Proxy speciálními záhlavími](cdn-rules-engine-reference-features.md#proxy-special-headers) v stroj pravidel. V tomto pravidle vylučte hlavičky, kterou chcete odebrat z výchozího seznamu hlaviček v poli hlavičky. Pokud jste povolili [funkce ladění hlavičky odpovědi mezipaměti](cdn-rules-engine-reference-features.md#debug-cache-response-headers), nezapomeňte přidat nezbytné `X-EC-Debug` záhlaví. 

Například, chcete-li odebrat `Via` záhlaví pole hlavičky pravidla by měl obsahovat následující seznam hlaviček: *X-předané-u, X-Forwarded-Proto, X-Host, X-Midgress, X-Gateway-List, X-ES-Name, Host*. 

![Pravidlo speciálními záhlavími proxy](./media/cdn-http-headers/cdn-proxy-special-header-rule.png)

Následující tabulka obsahuje záhlaví, které můžete přidat Verizon CDN POP v požadavku:

Hlavička požadavku | Popis | Příklad:
---------------|-------------|--------
[prostřednictvím](#via-request-header) | Určuje, který směrovány přes proxy server POP server požadavek na původním serveru. | ECS HTTP/1.1 (dca/1A2B)
X-předané pro | Označuje žadatele IP adresu.| 10.10.10.10
X-Forwarded-Proto | Určuje protokol žádosti. | http
X-hostitel | Určuje název hostitele žádosti. | CDN.mydomain.com
X-Midgress | Určuje, zda byla žádost směrovány přes proxy server prostřednictvím dalšího serveru pro CDN. Například server server původu shield POP nebo server brány server ADN POP. <br />Tato hlavička se přidá do požadavek pouze v případě, že provoz midgress probíhá. V takovém případě záhlaví je nastavená na 1 označuje, že žádost byla směrovány přes proxy server prostřednictvím dalšího serveru pro CDN.| 1
[Hostitel](#host-request-header) | Identifikuje hostitele a port, kde může najít požadovaný obsah. | marketing.mydomain.com:80
[X-Gateway List](#x-gateway-list-request-header) | ADN: Identifikuje seznamu převzetí služeb při selhání serverů brány ADN přiřazené na původní název zákazníka. <br />Shield původu: označuje sadu původu shield servery přiřazené pro zákazníka původu. | `icn1,hhp1,hnd1`
X-ES -_&lt;název&gt;_ | Hlavičky žádosti, které začínají *X-ES* (například X-ES-Tag, [X-ES-Debug](cdn-http-debug-headers.md)) jsou vyhrazené pro použití systémem CDN.| produkční waf

## <a name="via-request-header"></a>Prostřednictvím hlavičky žádosti
Formát, pomocí kterého `Via` požadavek hlavička značí serveru POP určený pomocí následující syntaxe:

`Via: Protocol from Platform (POP/ID)` 

Termíny používané v syntaxi jsou definovány takto:
- Protokol: Označuje, že verze protokolu (třeba HTTP/1.1) použita k proxy serveru žádosti. 

- Platforma: Určuje platformu, na kterém byl vyžádán obsah. Následující kódy jsou platné pro toto pole: 
    Kód | Platforma
    -----|---------
    Banky ECAcc | Velké HTTP
    ECS   | HTTP (krátkodobé používání)
    ECD   | Síť pro doručování aplikací (ADN)

- POP: Označuje, [POP](cdn-pop-abbreviations.md) , který zpracovává požadavek. 

- ID: jenom pro interní použití.

### <a name="example-via-request-header"></a>Příklad prostřednictvím hlavičky žádosti

`Via: HTTP/1.1 ECD (dca/1A2B)`

## <a name="host-request-header"></a>Hlavička hostitele požadavku
Přepíše POP servery `Host` záhlaví, pokud jsou splněny obě následující podmínky:
- Zdroj pro požadovaný obsah je zdrojový server zákazníka.
- Možnost odpovídající zákazníka původu hlavičku hostitele požadavku HTTP není prázdný.

`Host` Záhlaví požadavku se přepíšou tak, aby odrážely hodnota definovaná v rámci hlavičku hostitele požadavku HTTP.
Pokud zákazník původu hlavičku hostitele požadavku HTTP možnost nastavená na prázdný, pak `Host` hlavičku požadavku, který je odeslaná žadateli se předají na server původu zákazníka.

## <a name="x-gateway-list-request-header"></a>Hlavička žádosti X-Gateway List
Serveru POP přidat a přepíše "X-Gateway List hlavičku požadavku, pokud některá z následujících podmínek jsou splněny:
- Žádost se odkazuje na ADN platformy.
- Požadavek je předán na server původu zákazníka, který je chráněný funkcí ochrany zdroje.

