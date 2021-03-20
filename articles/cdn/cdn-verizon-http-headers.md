---
title: Hlavičky protokolu HTTP specifické pro Verizon pro modul pravidel pro Azure CDN | Microsoft Docs
description: Tento článek popisuje, jak používat hlavičky HTTP specifické pro Verizon s modulem Azure CDN Rules.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: allensu
ms.openlocfilehash: e20f6ce9540d357b61ae2cfdf0e8f96d127dc6c0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "84343213"
---
# <a name="verizon-specific-http-headers-for-azure-cdn-rules-engine"></a>Hlavičky protokolu HTTP specifické pro Verizon pro modul pravidel pro Azure CDN

V případě **Azure CDN Premium z produktů Verizon** se při odeslání požadavku HTTP na zdrojový server může server POP (Point-of-prezence) přidat do požadavku na adresu klienta jednu nebo více rezervovaných hlaviček (nebo speciálních hlaviček proxy serveru). Tyto hlavičky jsou kromě standardních přijatých hlaviček přesměrování. Další informace o hlavičkách standardních požadavků najdete v tématu [pole žádostí](https://en.wikipedia.org/wiki/List_of_HTTP_header_fields#Request_fields).

Pokud chcete zabránit tomu, aby se jedna z těchto rezervovaných hlaviček přidala do serveru služby Azure CDN (Content Delivery Network) na zdrojový server, musíte vytvořit pravidlo s [funkcí speciální hlavičky proxy serveru](https://docs.vdms.com/cdn/Content/HRE/F/Proxy-Special-Headers.htm) v modulu pravidel. V tomto pravidle vylučte hlavičku, kterou chcete odebrat, z výchozího seznamu hlaviček v poli Headers. Pokud jste povolili [funkci hlavičky odpovědí mezipaměti pro ladění](https://docs.vdms.com/cdn/Content/HRE/F/Debug-Cache-Response-Headers.htm), nezapomeňte přidat potřebné `X-EC-Debug` hlavičky. 

Chcete-li například odebrat `Via` hlavičku, pole Headers (hlavičky) by měla obsahovat následující seznam hlaviček: x-předávaných-for, x--a x- *Host, x-Midgress, x-Gateway-list, x-ES-Name, Host*. 

![Pravidlo pro speciální hlavičky proxy serveru](./media/cdn-http-headers/cdn-proxy-special-header-rule.png)

V následující tabulce jsou popsány hlavičky, které mohou být přidány pomocí protokolu POP Verizon CDN v žádosti:

Hlavička požadavku | Popis | Příklad
---------------|-------------|--------
[Vedení](#via-request-header) | Identifikuje server POP, který odeslal požadavek na zdrojový server. | Protokol HTTP/1.1 ECS (DCA/1A2B)
X-předané – pro | Určuje IP adresu žadatele.| 10.10.10.10
X-předáno – proto | Označuje protokol žádosti. | http
X-host | Určuje název hostitele žádosti. | cdn.mydomain.com
X – Midgress | Určuje, zda byl požadavek proxy serverem prostřednictvím dalšího serveru CDN. Například server, který je na serveru POP nebo server brány POP serveru pro a. <br />Tato hlavička se přidá do žádosti pouze v případě, že dojde k midgress provozu. V tomto případě je hlavička nastavená na hodnotu 1, aby označovala, že žádost byla proxy serverem prostřednictvím dalšího serveru CDN.| 1
[Hostitel](#host-request-header) | Identifikuje hostitele a port, kde může být nalezen požadovaný obsah. | marketing.mydomain.com:80
[X-Gateway – seznam](#x-gateway-list-request-header) | A: identifikuje seznam převzetí služeb při selhání serverů brány a přiřazených ke zdroji zákazníka. <br />Počátek štítku: označuje sadu počátečních ochranných serverů přiřazených ke zdroji zákazníka. | `icn1,hhp1,hnd1`
X-EC –_&lt; název &gt;_ | Hlavičky požadavku začínající *x-EC* (například x-EC-tag, [x-EC-Debug](cdn-http-debug-headers.md)) jsou vyhrazené pro použití v CDN.| WAF-produkční

## <a name="via-request-header"></a>Přes hlavičku žádosti
Formát, pomocí kterého `Via` Hlavička požadavku identifikuje server POP, je určen následující syntaxí:

`Via: Protocol from Platform (POP/ID)` 

Výrazy použité v syntaxi jsou definovány takto:
- Protokol: označuje verzi protokolu (například HTTP/1.1) použitou pro proxy požadavek. 

- Platforma: Určuje platformu, na které byl obsah požadován. Pro toto pole jsou platné následující kódy: 

    Kód | Platforma
    -----|---------
    ECAcc | Velký HTTP
    ECS   | HTTP – malý
    ECD   | Síť pro doručování aplikací (a)

- POP: označuje [bod POP](cdn-pop-abbreviations.md) , který zpracoval požadavek. 

- ID: pouze pro interní použití.

### <a name="example-via-request-header"></a>Příklad prostřednictvím hlavičky Request

`Via: HTTP/1.1 ECD (dca/1A2B)`

## <a name="host-request-header"></a>Hlavička žádosti hostitele
Servery POP přepíší hlavičku, `Host` Pokud jsou splněny obě následující podmínky:
- Zdroj požadovaného obsahu je zákazníkem, který je serverem původu.
- Možnost záhlaví hostitele HTTP odpovídající počátku zákazníka není prázdná.

`Host`Hlavička požadavku bude přepsána tak, aby odrážela hodnotu definovanou v HLAVIČCE http hostitele.
Pokud je možnost Hlavička hostitele protokolu HTTP původu zákazníka nastavena na hodnotu prázdné, bude `Host` Hlavička žádosti odeslaná žadatelem předána zdrojovému serveru zákazníka.

## <a name="x-gateway-list-request-header"></a>Hlavička žádosti o seznam X-Gateway-list
Server POP přidá nebo přepíše hlavičku žádosti "X-Gateway-list", pokud je splněna některá z následujících podmínek:
- Požadavek odkazuje na platformu a.
- Požadavek se přepošle na server původu zákazníka, který je chráněný funkcí zdroje ochrany.

