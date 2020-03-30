---
title: Nastavení zásad pro bránu firewall webových aplikací s azure front door
description: Naučte se bránu firewall webových aplikací (WAF).
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: 08b21ccd7f7958f00546583f680ecb8cde4a20c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75932618"
---
# <a name="policy-settings-for-web-application-firewall-on-azure-front-door"></a>Nastavení zásad pro bránu firewall webových aplikací na Azure Front Door

Zásady brány waf (Web Application Firewall) umožňují řídit přístup k webovým aplikacím pomocí sady vlastních a spravovaných pravidel. Název zásad waf musí být jedinečný. Pokud se pokusíte použít existující název, zobrazí se chyba ověření. Existuje více nastavení na úrovni zásad, které platí pro všechna pravidla zadaná pro tuto zásadu, jak je popsáno v tomto článku.

## <a name="waf-state"></a>Stav WAF

Zásada WAF pro přední dveře může být v jednom z následujících dvou stavů:
- **Povoleno:** Pokud je zásada povolena, waf aktivně kontroluje příchozí požadavky a provádí odpovídající akce podle definic pravidel
- **Zakázáno:** - Pokud je zásada zakázána, kontrola WAF je pozastavena. Příchozí požadavky obejdou WAF a jsou odesílány do back-endů na základě směrování předních dveří.

## <a name="waf-mode"></a>Režim WAF

Zásady WAF lze nakonfigurovat tak, aby běžely v následujících dvou režimech:

- **Detekční režim** Při spuštění v režimu zjišťování waf neprovádí žádné jiné akce než sledovat a protokolovat požadavek a jeho odpovídající WAF pravidlo waf protokoly. Zapněte diagnostiku protokolování pro front door (při použití portálu toho lze dosáhnout v části **Diagnostika** na webu Azure Portal).

- **Režim prevence** Při konfiguraci pro spuštění v režimu prevence provede waf zadanou akci, pokud požadavek odpovídá pravidlu. Všechny odpovídající požadavky jsou také zaznamenány v protokolech WAF.

## <a name="waf-response-for-blocked-requests"></a>Odpověď WAF pro blokované požadavky

Ve výchozím nastavení, když WAF blokuje požadavek z důvodu odpovídající pravidlo, vrátí stavový kód 403 s - **Požadavek je blokován** zprávu. Referenční řetězec je také vrácena pro protokolování.

Můžete definovat vlastní kód stavu odpovědi a zprávu odpovědi, když je požadavek blokován waf. Podporovány jsou následující vlastní stavové kódy:

- 200 OK
- 403 Zakázáno
- Metoda 405 není povolena.
- 406 Nepřijatelné
- 429 Příliš mnoho požadavků

Vlastní kód stavu odpovědi a zpráva odpovědi je nastavení úrovně zásad. Jakmile je nakonfigurován, všechny blokované požadavky získat stejný stav vlastní odpovědi a odpověď zprávy.

## <a name="uri-for-redirect-action"></a>Identifikátor URI pro akci přesměrování

Musíte definovat identifikátor URI, na který chcete přesměrovat požadavky, pokud je akce **PŘESMĚROVÁNÍ** vybrána pro některá z pravidel obsažených v zásadách WAF. Tento identifikátor URI přesměrování musí být platným webem HTTP(S) a po konfiguraci budou všechny požadavky odpovídající pravidla s akcí PŘESMĚROVÁNÍ přesměrovány na zadanou lokalitu.


## <a name="next-steps"></a>Další kroky
- Přečtěte si, jak definovat [vlastní odpovědi](waf-front-door-configure-custom-response-code.md) WAF
