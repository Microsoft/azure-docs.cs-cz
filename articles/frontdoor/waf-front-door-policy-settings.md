---
title: Nastavení zásad pro firewall webových aplikací s Azure branou
description: Přečtěte si firewall webových aplikací (WAF).
services: frontdoor
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/08/2019
ms.author: tyao;kumud
ms.openlocfilehash: 4c2f070e9b3c972f063008df8880b196ddb069cc
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2019
ms.locfileid: "59362956"
---
# <a name="policy-settings-for-web-application-firewall-with-azure-front-door"></a>Nastavení zásad pro firewall webových aplikací s Azure branou

Zásady brány Firewall webových aplikací (WAF) umožňuje řídit přístup k webovým aplikacím pomocí sady pravidel pro vlastní a spravované. Název zásad WAF musí být jedinečný. Zobrazí se chyba ověření a pokud se pokusíte použít existující název. Existuje více úrovně nastavení zásad, které platí pro všechna pravidla zadaný pro tuto zásadu, jak je popsáno v tomto článku.

## <a name="waf-state"></a>Stav WAF

Zásadu WAF pro vstupní brána lze v jednom z následujících dvou stavů:
- **Povoleno:** Když je povolené zásady, WAF aktivně kontroluje příchozí požadavky a provede odpovídající akce podle definice pravidla
- **Zakázáno:** – když se zásada je zakázána, kontroly WAF je pozastavená. Příchozí požadavky obejde WAF a jsou odeslány do back EndY založené na směrování branou.

## <a name="waf-mode"></a>Režim WAF

WAF zásad může být konfigurované pro běh v těchto dvou režimech:

- **Režim detekce** při spuštění v režimu detekce, WAF nepodporuje provádět žádné akce než monitorování a protokolovat žádosti a jeho odpovídající pravidlo WAF do protokolů WAF. Zapnutí protokolování diagnostiky pro branou (když používáte portál, jde tohoto dosáhnout tak, že přejdete **diagnostiky** části webu Azure Portal).

- **Režim ochrany před únikem informací** při nakonfigurované na spouštění v režimu ochrany před únikem informací, WAF provede zadanou akci Pokud požadavek odpovídá pravidlu. Žádné odpovídající požadavky jsou také zaznamenána protokolů WAF.

## <a name="waf-response-for-blocked-requests"></a>Odpověď WAF pro blokované požadavky

Ve výchozím nastavení, blokování WAF požadavek z důvodu odpovídající pravidlo, vrátí kód 403 stavu- **požadavek je blokován** zprávy. Pro protokolování je také vrácen řetězec odkazu.

Stavový kód vlastní odpovědi a zprávy s odpovědí můžete definovat při požadavku je blokována WAF. Podporují se následující vlastní stavové kódy:

- 200    OK
- 403 Zakázáno
- 405 Metoda není povolena
- 406 není přijatelná
- 429 příliš mnoho žádostí

Zpráva stavu kódu a odpověď vlastní odpovědi je nastavení úrovně zásad. Jakmile se nakonfiguruje, všechny blokované požadavky získat stav stejné vlastní odpovědi a zprávy s odpovědí.

## <a name="uri-for-redirect-action"></a>Identifikátor URI pro přesměrování akce

Je nutné definovat identifikátor URI pro přesměrování žádostí, když **PŘESMĚROVÁNÍ** akce je vybrán pro žádné z pravidel obsažená v zásadách WAF. Toto přesměrování identifikátor URI musí být platný web HTTP (S) a po nakonfigurování všech požadavků odpovídající pravidla s akcí "PŘESMĚROVÁNÍ" budete přesměrováni na konkrétní lokalitu.


## <a name="next-steps"></a>Další postup
- Další informace o definování WAF [vlastní odpovědi](waf-front-door-configure-custom-response-code.md)
