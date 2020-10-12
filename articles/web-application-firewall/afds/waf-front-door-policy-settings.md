---
title: Nastavení zásad pro Firewall webových aplikací pomocí front-dveří Azure
description: Zjistěte, jak Firewall webových aplikací (WAF).
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: 08b21ccd7f7958f00546583f680ecb8cde4a20c8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "75932618"
---
# <a name="policy-settings-for-web-application-firewall-on-azure-front-door"></a>Nastavení zásad pro Firewall webových aplikací na front-dveřích Azure

Zásady firewallu webových aplikací (WAF) umožňují řídit přístup k webovým aplikacím pomocí sady vlastních a spravovaných pravidel. Název zásad WAF musí být jedinečný. Pokud se pokusíte použít stávající název, zobrazí se chyba ověřování. Pro všechna pravidla zadaná pro tuto zásadu platí více nastavení na úrovni zásad, jak je popsáno v tomto článku.

## <a name="waf-state"></a>Stav WAF

Zásady WAF pro přední dveře můžou být v jednom z následujících dvou stavů:
- **Povoleno:** Když je povolená zásada, WAF aktivně kontroluje příchozí žádosti a v souladu s definicemi pravidel provede odpovídající akce.
- **Disabled:** – když je zásada zakázaná, WAF kontrola se pozastaví. Příchozí žádosti budou obejít WAF a budou odesílány do back-endu na základě směrování front dveří.

## <a name="waf-mode"></a>WAF režim

Zásady WAF je možné nakonfigurovat tak, aby běžely v následujících dvou režimech:

- **Režim detekce** Při spuštění v režimu detekce WAF neprovede žádné jiné akce než monitorování a zaprotokoluje požadavek a odpovídající WAF pravidlo do protokolů WAF. Zapnout diagnostiku protokolování pro přední dveře (Pokud používáte portál, můžete to dosáhnout tak, že v Azure Portal v části **diagnostiku** zadáte).

- **Režim prevence** Pokud je nakonfigurováno spuštění v režimu prevence, WAF provede zadanou akci, pokud požadavek odpovídá pravidlu. V protokolech WAF se zaznamenávají i všechny odpovídající požadavky.

## <a name="waf-response-for-blocked-requests"></a>WAF odpověď na blokované požadavky

Ve výchozím nastavení, když WAF zablokuje požadavek z důvodu spárovaného pravidla, vrátí stavový kód 403 s- **požadavek je zablokovaná** zpráva. Pro protokolování se také vrátí řetězec odkazu.

Můžete definovat vlastní kód stavu odpovědi a zprávu odpovědi, pokud je požadavek zablokován nástrojem WAF. Podporují se tyto vlastní stavové kódy:

- 200 OK
- 403 zakázané
- Metoda 405 není povolená.
- 406 není přijatelné
- 429 příliš mnoho požadavků

Vlastní kód stavu odpovědi a zpráva odpovědi jsou nastavení úrovně zásad. Po nakonfigurování získá všechny blokované požadavky stejný stav vlastní odpovědi a zprávu s odpovědí.

## <a name="uri-for-redirect-action"></a>Identifikátor URI pro akci přesměrování

Je nutné definovat identifikátor URI pro přesměrování požadavků, pokud je vybrána akce **přesměrování** pro některá pravidla obsažená v zásadách WAF. Tento identifikátor URI pro přesměrování musí být platným serverem HTTP (S) a po nakonfigurování budou všechny požadavky, které odpovídají pravidlům "přesměrování", přesměrovány na zadanou lokalitu.


## <a name="next-steps"></a>Další kroky
- Naučte se definovat [vlastní odpovědi](waf-front-door-configure-custom-response-code.md) WAF
