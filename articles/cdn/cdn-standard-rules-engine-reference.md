---
title: Referenční příručka stroje standardních pravidel pro Azure CDN | Microsoft Docs
description: Referenční dokumentace pro podmínky a akce shody v modulu Standard rules pro Azure Content Delivery Network (Azure CDN).
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: magattus
ms.openlocfilehash: aa401150ee7a0f02e809ad702b8247e18081c8a3
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2019
ms.locfileid: "74171564"
---
# <a name="standard-rules-engine-reference-for-azure-cdn"></a>Referenční příručka stroje standardních pravidel pro Azure CDN

V [modulu Standard Rules](cdn-standard-rules-engine.md) pro Azure Content Delivery Network (Azure CDN) se pravidlo skládá z jedné nebo více podmínek shody a akci. Tento článek obsahuje podrobný popis podmínek shody a funkcí, které jsou k dispozici v modulu Standard rules pro Azure CDN.

Modul pravidel je navržený tak, aby měl koncovou autoritu, jak jsou konkrétní typy požadavků zpracovávány standardním Azure CDN.

**Společná použití pro pravidla**:

- Přepsat nebo definovat vlastní zásadu mezipaměti.
- Přesměrování požadavků.
- Úprava hlaviček požadavků a odpovědí HTTP.

## <a name="terminology"></a>Terminologie

Chcete-li definovat pravidlo v modulu pravidel, nastavte [podmínky](cdn-standard-rules-engine-match-conditions.md) a [Akce](cdn-standard-rules-engine-actions.md)shody:

 ![Struktura Azure CDNch pravidel](./media/cdn-standard-rules-engine-reference/cdn-rules-structure.png)

Každé pravidlo může mít až čtyři podmínky shody a tři akce. Každý koncový bod Azure CDN může mít až pět pravidel. 

Zahrnuté v aktuálním omezení s pěti pravidly pro Azure CDN koncový bod je výchozí *globální pravidlo*. Globální pravidlo nemá podmínky shody a akce, které jsou definovány v globálním pravidle, budou vždy aktivovány.

## <a name="syntax"></a>Syntaxe

Způsob zpracování speciálních znaků v pravidle se liší v závislosti na tom, jak různé podmínky shody a akce zpracovávají textové hodnoty. Podmínka nebo akce shody mohou interpretovat text jedním z následujících způsobů:

- [Hodnoty literálu](#literal-values)
- [Zástupné hodnoty](#wildcard-values)


### <a name="literal-values"></a>Hodnoty literálu

Text, který je interpretován jako hodnota literálu, zpracovává všechny speciální znaky *s výjimkou% symbol* jako součást hodnoty, která musí být v pravidle shodná. Například podmínka shody literálů nastavená na `'*'` je splněna pouze v případě, že je nalezena přesná hodnota `'*'`.

K označení kódování adresy URL (například `%20`) se používá znak procenta.

### <a name="wildcard-values"></a>Zástupné hodnoty

Text, který je interpretován jako zástupná hodnota, přiřadí další význam pro speciální znaky. Následující tabulka popisuje, jak se interpretují konkrétní speciální znaky v modulu Standard Rules:

Znak | Popis
----------|------------
\ | Zpětné lomítko se používá k úniku znaků určených v této tabulce. Zpětné lomítko musí být zadáno přímo před speciálním znakem, který by měl být uvozen řídicím znakem. Například následující syntaxe řídí hvězdičku: `\*`
% | K označení kódování adresy URL (například `%20`) se používá znak procenta.
\* | Hvězdička je zástupný znak, který reprezentuje jeden nebo více znaků.
Space | Znak mezery označuje, že podmínka shody může být splněna některou ze zadaných hodnot nebo vzorů.
jednoduché uvozovky | Jednoduché uvozovky nemají zvláštní význam. Sada jednoduchých uvozovek však označuje, že hodnota by měla být považována za hodnotu literálu. Jednoduché uvozovky lze použít následujícími způsoby:<ul><li>Aby byla splněna podmínka shody pokaždé, když zadaná hodnota odpovídá jakékoli části hodnoty porovnání.  `'ma'` by například odpovídaly jakémukoli z následujících řetězců: <ul><li>/business/**ma**rathon/asset.htm</li><li>**ma**p.gif</li><li>/business/template. **ma**p</li></ul><li>Aby bylo možné zadat speciální znak jako literální znak. Můžete například zadat literální znak mezery přidáním znaku mezery do sady jednoduchých uvozovek (`' '` nebo `'<sample value>'`).</li><li>Povoluje zadání prázdné hodnoty. Zadejte prázdnou hodnotu zadáním sady jednoduchých uvozovek (' **'** ).</li></ul>**Důležité**informace:<br /><ul><li>Pokud zadaná hodnota neobsahuje zástupný znak, hodnota se automaticky považuje za hodnotu literálu. Pro hodnotu literálu není nutné zadávat sadu jednoduchých uvozovek.</li><li>Pokud zpětné lomítko není použito k úniku jiného znaku v této tabulce, je zpětné lomítko ignorováno, je-li zadáno v sadě jednoduchých uvozovek.</li><li>Dalším způsobem, jak zadat speciální znak jako literální znak, je vystavení řídicím znakem pomocí zpětného lomítka (`\`).</li></ul>

## <a name="next-steps"></a>Další kroky

- [Podmínky shody v modulu Standard Rules](cdn-standard-rules-engine-match-conditions.md)
- [Akce v modulu Standard Rules](cdn-standard-rules-engine-actions.md)
- [Vynutilit HTTPS pomocí modulu Standard Rules](cdn-standard-rules-engine.md)
- [Přehled Azure CDN](cdn-overview.md)
