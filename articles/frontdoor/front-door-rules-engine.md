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
ms.date: 9/14/2020
ms.author: duau
ms.openlocfilehash: 94770e4fcad9659594854f81732c7b4e4a97051c
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90530875"
---
# <a name="what-is-rules-engine-for-azure-front-door"></a>Co je to modul pravidel pro přední dveře Azure? 

Modul pravidel umožňuje přizpůsobit způsob zpracování požadavků HTTP na hraničních zařízeních a poskytuje větší kontrolu nad chováním vaší webové aplikace. Modul pravidel pro přední dveře Azure zahrnuje několik klíčových funkcí, včetně:

- Vyvynuťte protokol HTTPS, ujistěte se, že všichni koncoví uživatelé komunikují s vaším obsahem prostřednictvím zabezpečeného připojení.
- Implementujte hlavičky zabezpečení, abyste zabránili ohrožením zabezpečení založeného na prohlížeči, jako je HTTP Strict-Transport-Security (HSTS), X-XSS-Protection, Content-Security-Policy, X-frame-Options, stejně jako hlavičky Access-Control-Allow-Origin pro scénáře sdílení prostředků mezi zdroji (CORS). Atributy založené na zabezpečení je také možné definovat pomocí souborů cookie.
- Směrování požadavků na mobilní nebo desktopové verze vaší aplikace na základě vzorů v obsahu hlaviček požadavků, souborů cookie nebo řetězců dotazů.
- K přesměrování na nové názvy hostitelů, cesty nebo protokoly použijte možnosti přesměrování pro vrácení 301, 302, 307 a 308 přesměrování na klienta.
- Dynamicky Upravte konfiguraci ukládání do mezipaměti trasy na základě příchozích požadavků.
- Přepište cestu URL požadavku a předejte požadavek do příslušného back-endu v nakonfigurovaném fondu back-endu.

## <a name="architecture"></a>Architektura 

Modul pravidel zpracovává požadavky na hraničních zařízeních. Po nakonfigurování modulu pravidel, když požadavek narazí na koncový bod předních dveří, se WAF spustí jako první a pak bude následovat konfigurace modulu pravidel, která je přidružená k front-endu nebo doméně. Když se spustí konfigurace modulu pravidel, znamená to, že pravidlo nadřazeného směrování už odpovídá. Bez ohledu na to, jestli jsou spuštěné všechny akce v každé z pravidel v rámci konfigurace modulu pravidel, platí pro splnění všech podmínek shody v rámci daného pravidla. Pokud požadavek neodpovídá žádné z podmínek v konfiguraci modulu pravidel, bude provedeno výchozí pravidlo směrování. 

Například v níže uvedené konfiguraci je modul pravidel nakonfigurován tak, aby přidal hlavičku odpovědi, která změní maximální stáří řízení mezipaměti, pokud je splněna podmínka shody. 

![akce hlavičky odpovědi](./media/front-door-rules-engine/rules-engine-architecture-3.png)

V jiném příkladu vidíte, že je modul pravidel nakonfigurovaný tak, aby odesílal uživatele do mobilní verze lokality, pokud je podmínka shody, typ zařízení, true. 

![přepsání konfigurace směrování](./media/front-door-rules-engine/rules-engine-architecture-1.png)

V obou těchto příkladech platí, že pokud není splněna žádná podmínka shody, bude zadané pravidlo pro směrování provedeno. 

## <a name="terminology"></a>Terminologie 

Pomocí modulu pravidel AFD můžete vytvořit řadu konfigurací modulu pravidel, z nichž každá se skládá ze sady pravidel. Následující text popisuje určitou užitečnou terminologii, kterou se vám bude při konfiguraci modulu pravidel nacházet. 

- *Konfigurace modulu pravidel*: sada pravidel, která se aplikují na pravidlo pro jedno směrování. Každá konfigurace je omezená na 25 pravidel. Můžete vytvořit až 10 konfigurací. 
- *Pravidlo stroje pravidel*: pravidlo složené z až 10 podmínek shody a 5 akcí.
- *Podmínka shody*: existuje mnoho podmínek shody, které lze využít k analýze příchozích požadavků. Pravidlo může obsahovat až 10 podmínek shody. Podmínky shody jsou vyhodnocovány pomocí operátoru **and** . Úplný seznam podmínek shody najdete [tady](front-door-rules-engine-match-conditions.md). 
- *Akce*: akce určují, co se stane s vašimi příchozími požadavky – akce a hlavičky požadavku a odpovědi, přesměrování, přesměrování a Přepisy jsou dostupné dnes. Pravidlo může obsahovat až 5 akcí. pravidlo ale může obsahovat jenom 1 přepsání konfigurace trasy.  Úplný seznam akcí najdete [tady](front-door-rules-engine-actions.md).


## <a name="next-steps"></a>Další kroky

- Naučte se nastavit [konfiguraci modulu](front-door-tutorial-rules-engine.md)pro první pravidla. 
- Přečtěte si, jak [vytvořit Front Door](quickstart-create-front-door.md).
- Přečtěte si, [jak služba Front Door funguje](front-door-routing-architecture.md).
