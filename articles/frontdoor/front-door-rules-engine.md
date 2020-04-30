---
title: Přední dvířka Azure | Microsoft Docs
description: Tento článek obsahuje přehled služby Azure Front Door. Zjistěte, zda se jedná o správnou volbu pro síťový provoz vyrovnávání zatížení pro vaši aplikaci.
services: frontdoor
documentationcenter: ''
author: megan-beatty
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 4/30/2020
ms.author: mebeatty
ms.openlocfilehash: 19deb763c8e750490854892c90d0293d3e209c09
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "82515549"
---
# <a name="what-is-rules-engine-for-azure-front-door"></a>Co je to modul pravidel pro přední dveře Azure? 

Modul pravidel umožňuje přizpůsobit způsob zpracování požadavků HTTP na hraničních zařízeních a poskytuje větší kontrolu nad chováním vaší webové aplikace. Modul pravidel pro přední dveře Azure zahrnuje několik klíčových funkcí, včetně:

- Směrování na základě hlaviček – požadavky na směrování na základě vzorů v obsahu hlaviček požadavků, souborů cookie a řetězců dotazů.
- Směrování založené na parametrech – můžete využít řadu podmínek shody, včetně argumentů post, dotazovacích řetězců, souborů cookie a metod žádosti, a směrovat požadavky na základě parametrů požadavku HTTP. 
- Přepsání konfigurací směrování: 
    - Pomocí možností přesměrování vraťte 301/302/307/308 přesměrování na klienta, aby se přesměrovala na nové názvy hostitelů, cesty a protokoly. 
    - Pomocí možností předávání přepište cestu URL požadavku, aniž byste museli provést tradiční přesměrování a předat požadavek příslušnému back-endu v nakonfigurovaném fondu back-end. 
    - Přizpůsobte konfiguraci ukládání do mezipaměti a dynamicky měníte trasu od předávání do mezipaměti na základě podmínek shody. 

> [!IMPORTANT]
> Tato verze Public Preview se poskytuje bez smlouvy o úrovni služeb a neměla by se používat pro úlohy v produkčním prostředí. Některé funkce nemusí být podporované, můžou mít omezené možnosti nebo nemusí být dostupné ve všech umístěních Azure. Podrobnosti najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="architecture"></a>Architektura 

Modul pravidel zpracovává požadavky na hraničních zařízeních. Po nakonfigurování modulu pravidel, když požadavek narazí na koncový bod předních dveří, se WAF spustí jako první a pak bude následovat konfigurace modulu pravidel, která je přidružená k front-endu nebo doméně. Když se spustí konfigurace modulu pravidel, znamená to, že pravidlo nadřazeného směrování už odpovídá. Bez ohledu na to, jestli jsou spuštěné všechny akce v každé z pravidel v rámci konfigurace modulu pravidel, platí pro splnění všech podmínek shody v rámci daného pravidla. Pokud požadavek neodpovídá žádné z podmínek v konfiguraci modulu pravidel, bude provedeno výchozí pravidlo směrování. 

Například v níže uvedené konfiguraci je modul pravidel nakonfigurován tak, aby přidal hlavičku odpovědi, která změní maximální stáří řízení mezipaměti, pokud je splněna podmínka shody. 

![akce hlavičky odpovědi](./media/front-door-rules-engine/rules-engine-architecture-3.png)

V jiném příkladu vidíte, že je modul pravidel nakonfigurovaný tak, aby odesílal uživatele do mobilní verze lokality, pokud je podmínka shody, typ zařízení, true. 

![přepsání konfigurace směrování](./media/front-door-rules-engine/rules-engine-architecture-1.png)

V obou těchto příkladech platí, že pokud není splněna žádná podmínka shody, bude zadané pravidlo pro směrování provedeno. 

## <a name="terminology"></a>Terminologie 

Pomocí modulu pravidel AFD můžete vytvořit řadu konfigurací modulu pravidel, z nichž každá se skládá ze sady pravidel. Následující text popisuje určitou užitečnou terminologii, kterou se vám bude při konfiguraci modulu pravidel nacházet. 

- *Konfigurace modulu pravidel*: sada pravidel, která se aplikují na pravidlo pro jedno směrování. Každá konfigurace je omezená na 5 pravidel. Můžete vytvořit až 10 konfigurací. 
- *Pravidlo stroje pravidel*: pravidlo složené z až 10 podmínek shody a 5 akcí.
- *Podmínka shody*: existuje mnoho podmínek shody, které lze využít k analýze příchozích požadavků. Pravidlo může obsahovat až 10 podmínek shody. Podmínky shody jsou vyhodnocovány pomocí operátoru **and** . Úplný seznam podmínek shody najdete [tady](front-door-rules-engine-match-conditions.md). 
- *Akce*: akce určují, co se stane s vašimi příchozími požadavky – akce a hlavičky požadavku a odpovědi, přesměrování, přesměrování a Přepisy jsou dostupné dnes. Pravidlo může obsahovat až 5 akcí. pravidlo ale může obsahovat jenom 1 přepsání konfigurace trasy.  Úplný seznam akcí najdete [tady](front-door-rules-engine-actions.md).


## <a name="next-steps"></a>Další kroky

- Naučte se nastavit [konfiguraci modulu](front-door-tutorial-rules-engine.md)pro první pravidla. 
- Přečtěte si, jak [vytvořit Front Door](quickstart-create-front-door.md).
- Přečtěte si, [jak služba Front Door funguje](front-door-routing-architecture.md).
