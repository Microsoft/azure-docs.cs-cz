---
title: Odkaz na modul standardních pravidel pro Azure CDN | Dokumenty společnosti Microsoft
description: Referenční dokumentace pro podmínky a akce shody v modulu standardních pravidel pro síť doručování obsahu Azure (Azure CDN).
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: magattus
ms.openlocfilehash: aa401150ee7a0f02e809ad702b8247e18081c8a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74171564"
---
# <a name="standard-rules-engine-reference-for-azure-cdn"></a>Odkaz na modul standardních pravidel pro Azure CDN

V [modulu standardnípravidla](cdn-standard-rules-engine.md) pro Azure Content Delivery Network (Azure CDN) se pravidlo skládá z jedné nebo více podmínek shody a akce. Tento článek obsahuje podrobný popis podmínek shody a funkce, které jsou k dispozici v modulu standardní pravidla pro Azure CDN.

Modul pravidel je navržen tak, aby byl konečným orgánem pro zpracování konkrétních typů požadavků pomocí standardu Azure CDN.

**Běžné použití pravidel**:

- Přepište nebo definujte vlastní zásady mezipaměti.
- Požadavky na přesměrování.
- Upravte hlavičky požadavků a odpovědí http.

## <a name="terminology"></a>Terminologie

Chcete-li definovat pravidlo v modulu pravidel, nastavte [podmínky shody](cdn-standard-rules-engine-match-conditions.md) a [akce](cdn-standard-rules-engine-actions.md):

 ![Struktura pravidel Azure CDN](./media/cdn-standard-rules-engine-reference/cdn-rules-structure.png)

Každé pravidlo může mít až čtyři podmínky shody a tři akce. Každý koncový bod Azure CDN může mít až pět pravidel. 

Součástí aktuálního limitu pěti pravidel pro koncový bod Azure CDN je výchozí *globální pravidlo*. Globální pravidlo nemá podmínky shody a akce, které jsou definovány v globálním pravidle, se vždy aktivují.

## <a name="syntax"></a>Syntaxe

Způsob, jakým jsou speciální znaky zpracovány v pravidle, se liší v závislosti na tom, jak různé podmínky shody a akce zpracovávají textové hodnoty. Podmínka nebo akce shody může text interpretovat jedním z následujících způsobů:

- [Literálové hodnoty](#literal-values)
- [Hodnoty zástupných symbolů](#wildcard-values)


### <a name="literal-values"></a>Literálové hodnoty

Text, který je interpretován jako literál hodnota zachází všechny speciální znaky *s výjimkou symbolu %* jako součást hodnoty, která musí být uzavřeno v pravidle. Například podmínka doslovné shody nastavená na `'*'` je `'*'` splněna pouze v případě, že je nalezena přesná hodnota.

Znak procenta se používá k označení kódování `%20`adresy URL (například).

### <a name="wildcard-values"></a>Hodnoty zástupných symbolů

Text, který je interpretován jako zástupná hodnota, přiřazuje speciálním znakům další význam. Následující tabulka popisuje, jak jsou v modulu standardních pravidel interpretovány konkrétní speciální znaky:

Znak | Popis
----------|------------
\ | Zpětné lomítko se používá k úniku všech znaků uvedených v této tabulce. Zpětné lomítko musí být zadáno přímo před speciálníznak, který by měl být uvozen. Například následující syntaxe unikne hvězdičkou:`\*`
% | Znak procenta se používá k označení kódování `%20`adresy URL (například).
\* | Hvězdička je zástupný znak, který představuje jeden nebo více znaků.
mezera | Mezerník označuje, že podmínka shody může být splněna buď zadanými hodnotami nebo vzorky.
jednoduché uvozovky | Jedna uvozovka nemá zvláštní význam. Sada jednoduchých uvozovek však označuje, že hodnota by měla být považována za hodnotu literálu. Jednoduché uvozovky lze použít následujícími způsoby:<ul><li>Chcete-li povolit splnění podmínky shody vždy, když zadaná hodnota odpovídá libovolné části srovnávací hodnoty.  Například `'ma'` by odpovídaly některé z následujících řetězců: <ul><li>/business/**ma**rathon/asset.htm</li><li>**ma**p.gif</li><li>/business/template. **ma**p</li></ul><li>Chcete-li povolit speciální znak, který má být určen jako literál znak. Můžete například zadat znak mezery literálu tak, že znak mezery`' '` `'<sample value>'`založíte do sady jednoduchých uvozovek ( nebo ).</li><li>Chcete-li povolit zadaný prázdný hodnotu. Zadejte prázdnou hodnotu zadáním sady jednoduchých uvozovek (**''**).</li></ul>**Důležité**:<br /><ul><li>Pokud zadaná hodnota neobsahuje zástupný znak, hodnota se automaticky považuje za hodnotu literálu. Není nutné zadat sadu jednoduchých uvozovek pro hodnotu literálu.</li><li>Pokud zpětné lomítko není použit k úniku jiný znak v této tabulce, zpětné lomítko je ignorována, pokud je zadánv sadě jednoduchých uvozovek.</li><li>Dalším způsobem, jak určit speciální znak jako literálový znak,`\`je uniknout pomocí zpětného lomítka ( ).</li></ul>

## <a name="next-steps"></a>Další kroky

- [Podmínky shody v modulu standardních pravidel](cdn-standard-rules-engine-match-conditions.md)
- [Akce v modulu standardních pravidel](cdn-standard-rules-engine-actions.md)
- [Vynucení HTTPS s využitím stroje pravidel Standard](cdn-standard-rules-engine.md)
- [Přehled azure cdn](cdn-overview.md)
