---
title: Modul pravidel pro architekturu a terminologii front-and Azure
description: Tento článek poskytuje přehled funkce modulu pravidel pro přední dveře Azure.
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 9/29/2020
ms.author: duau
ms.openlocfilehash: 8e478cebcf8c5c9365100ade23c3d610c24930ba
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "91569749"
---
# <a name="what-is-rules-engine-for-azure-front-door"></a>Co je to modul pravidel pro přední dveře Azure? 

Modul pravidel umožňuje přizpůsobit zpracování požadavků HTTP na hranici a poskytuje lepší chování vaší webové aplikace. Modul pravidel pro přední dveře Azure obsahuje několik klíčových funkcí, včetně:

* Vynutila HTTPS, aby zajistila, že všichni koncoví uživatelé budou pracovat s vaším obsahem přes zabezpečené připojení.
* Implementuje hlavičky zabezpečení, aby nedocházelo k ohrožením zabezpečení založeného na prohlížeči, jako je HTTP Strict-Transport-Security (HSTS), X-XSS-Protection, Content-Security-Policy, X-frame-Options a Access-Control-Allow-Origin Headers pro scénáře sdílení prostředků mezi zdroji (CORS). Atributy založené na zabezpečení je také možné definovat pomocí souborů cookie.
* Směrovat požadavky na mobilní nebo desktopové verze vaší aplikace na základě vzorců obsahu hlaviček žádostí, souborů cookie nebo řetězců dotazů.
* Možnosti přesměrování můžete použít k vrácení 301, 302, 307 a 308 přesměrování na klienta, aby bylo možné směrovat na nové názvy hostitelů, cesty nebo protokoly.
- Dynamicky Upravte konfiguraci ukládání do mezipaměti trasy na základě příchozích požadavků.
- Přepište cestu URL požadavku a předejte požadavek do příslušného back-endu v nakonfigurovaném fondu back-endu.

## <a name="architecture"></a>Architektura 

Modul pravidel zpracovává požadavky na hraničních zařízeních. Když požadavek narazí na koncový bod pro frontu, spustí se nejprve WAF a pak se vytvoří konfigurace modulu pravidel přidružená k front-endu nebo doméně. Pokud se spustí konfigurace modulu pravidel, znamená to, že pravidlo nadřazeného směrování už odpovídá. Aby se daly provést všechny akce v každém pravidle, musí být splněné všechny podmínky shody v rámci pravidla. Pokud se požadavek neshoduje s žádnou z podmínek v konfiguraci modulu pravidel, bude provedeno výchozí pravidlo směrování. 

Například v následujícím diagramu se modul pravidel nakonfiguruje tak, aby přidal hlavičku odpovědi. Hlavička změní maximální stáří řízení mezipaměti, pokud je splněna podmínka shody. 

![akce hlavičky odpovědi](./media/front-door-rules-engine/rules-engine-architecture-3.png)

V jiném příkladu vidíte, že je modul pravidel nakonfigurovaný tak, aby odesílal uživatele do mobilní verze lokality, pokud je podmínka shody, typ zařízení, true. 

![přepsání konfigurace směrování](./media/front-door-rules-engine/rules-engine-architecture-1.png)

V obou těchto příkladech platí, že pokud není splněna žádná podmínka shody, bude zadané pravidlo pro směrování provedeno. 

## <a name="terminology"></a>Terminologie 

Pomocí modulu pravidel AFD můžete vytvořit kombinaci konfigurací modulu pravidel, z nichž každá se skládá ze sady pravidel. Následující text popisuje určitou užitečnou terminologii, kterou se vám bude při konfiguraci modulu pravidel nacházet. 

- *Konfigurace modulu pravidel*: sada pravidel, která se aplikují na pravidlo pro jedno směrování. Každá konfigurace je omezená na 25 pravidel. Můžete vytvořit až 10 konfigurací. 
- *Pravidlo stroje pravidel*: pravidlo složené z až 10 podmínek shody a 5 akcí.
- *Podmínka shody*: existuje mnoho podmínek shody, které lze využít k analýze příchozích požadavků. Pravidlo může obsahovat až 10 podmínek shody. Podmínky shody jsou vyhodnocovány pomocí operátoru **and** . Úplný seznam podmínek shody najdete [tady](front-door-rules-engine-match-conditions.md). 
- *Akce*: akce určují, co se stane s vašimi příchozími požadavky – akce a hlavičky požadavku a odpovědi, přesměrování, přesměrování a Přepisy jsou dostupné dnes. Pravidlo může obsahovat až pět akcí. pravidlo ale může obsahovat jenom jedno přepsání konfigurace trasy.  Úplný seznam akcí najdete [tady](front-door-rules-engine-actions.md).


## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak nakonfigurovat [konfiguraci modulu prvního pravidla](front-door-tutorial-rules-engine.md). 
- Přečtěte si, jak [vytvořit Front Door](quickstart-create-front-door.md).
- Přečtěte si, [jak služba Front Door funguje](front-door-routing-architecture.md).
