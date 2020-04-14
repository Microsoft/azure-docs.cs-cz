---
title: Odkaz na modul pravidel Azure CDN | Dokumenty společnosti Microsoft
description: Referenční dokumentace pro pravidla Azure CDN modul odpovídají podmínky a funkce.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: allensu
ms.openlocfilehash: bda817712faf1f54287e880dc62ef2b08273ff42
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81253386"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-reference"></a>Azure CDN od Verizon Premium pravidla odkaz na modul

Tento článek obsahuje podrobný popis dostupných podmínek shody a funkcí pro [modul pravidel](cdn-verizon-premium-rules-engine.md)sítě pro doručování obsahu Azure (CDN).

Modul pravidel je navržen tak, aby byl konečným orgánem o tom, jak jsou konkrétní typy požadavků zpracovávány cdn.

**Běžná použití**:

- Přepište nebo definujte vlastní zásady mezipaměti.
- Zabezpečte nebo zamítnete požadavky na citlivý obsah.
- Požadavky na přesměrování.
- Uložte vlastní data protokolu.

## <a name="terminology"></a>Terminologie

Pravidlo je definováno pomocí [**podmíněných výrazů**](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md), [**podmínek shody**](cdn-verizon-premium-rules-engine-reference-match-conditions.md)a [**prvků**](cdn-verizon-premium-rules-engine-reference-features.md). Tyto prvky jsou zvýrazněny na následujícím obrázku:

 ![Podmínka shody CDN](./media/cdn-rules-engine-reference/cdn-rules-engine-terminology.png)

## <a name="syntax"></a>Syntaxe

Způsob, jakým jsou zpracovány speciální znaky se liší v závislosti na tom, jak podmínka shody nebo funkce zpracovává textové hodnoty. Podmínka nebo funkce shody může interpretovat text jedním z následujících způsobů:

1. [**Literálové hodnoty**](#literal-values)
2. [**Hodnoty zástupných symbolů**](#wildcard-values)
3. [**Regulární výrazy**](#regular-expressions)

### <a name="literal-values"></a>Literálové hodnoty

Text, který je interpretován jako hodnota literálu, zachází se všemi speciálními znaky, s výjimkou symbolu %, jako součást hodnoty, která musí odpovídat. Jinými slovy, podmínka doslovné `\'*'\` shody nastavená pouze v případě, `\'*'\`že je nalezena přesná hodnota (to znamená).

Symbol procenta se používá k označení kódování `%20`adresy URL (například).

### <a name="wildcard-values"></a>Hodnoty zástupných symbolů

Text, který je interpretován jako zástupná hodnota, přiřazuje speciálním znakům další význam. Následující tabulka popisuje, jak je interpretována následující sada znaků:

Znak | Popis
----------|------------
\ | Zpětné lomítko se používá k úniku všech znaků uvedených v této tabulce. Zpětné lomítko musí být zadáno přímo před speciálníznak, který by měl být uvozen.<br/>Například následující syntaxe unikne hvězdičkou:`\*`
% | Symbol procenta se používá k označení kódování `%20`adresy URL (například).
\* | Hvězdička je zástupný znak, který představuje jeden nebo více znaků.
Místo | Mezerník označuje, že podmínka shody může být splněna některou ze zadaných hodnot nebo vzorků.
"hodnota" | Jedna citace nemá zvláštní význam. Sada jednoduchých uvozovek se však používá k označení, že hodnota by měla být považována za hodnotu literálu. Může být použit následujícími způsoby:<br><br/>- Umožňuje splnění podmínky shody vždy, když zadaná hodnota odpovídá libovolné části srovnávací hodnoty.  Například `'ma'` by odpovídaly některé z následujících řetězců: <br/><br/>/business/**ma**rathon/asset.htm<br/>**ma**p.gif<br/>/business/template. **ma**p<br /><br />- To umožňuje speciální znak, který má být specifikován jako doslovný znak. Můžete například zadat znak mezery literálu tím, že ohraničíme `' '` znak `'sample value'`mezery v sadě jednotlivých uvozovek (to znamená nebo ).<br/>- Umožňuje zadat prázdnou hodnotu. Zadejte prázdnou hodnotu zadáním sady jednoduchých uvozovek (tj.<br /><br/>**Důležité:**<br/>- Pokud zadaná hodnota neobsahuje zástupný znak, pak je automaticky považována za hodnotu literálu, což znamená, že není nutné zadat sadu jednoduchých uvozovek.<br/>- Pokud zpětné lomítko neunikne jinému znaku v této tabulce, je ignorováno, pokud je zadáno v rámci sady jednotlivých uvozovek.<br/>- Dalším způsobem, jak určit speciální znak jako literál znak je `\`uniknout pomocí zpětného lomítka (to znamená), ).

### <a name="regular-expressions"></a>Regulární výrazy

Regulární výrazy definují vzorek, který je vyhledáván v textové hodnotě. Zápis regulárního výrazu definuje specifické významy různých symbolů. V následující tabulce jsou uvedeny, jak jsou speciální znaky zpracovány podmínkami shody a funkcemi, které podporují regulární výrazy.

Speciální znak | Popis
------------------|------------
\ | Zpětné lomítko unikne znak následuje, což způsobí, že znak má být považován za hodnotu literálu namísto převzetí jeho regulární výraz význam. Například následující syntaxe unikne hvězdičkou:`\*`
% | Význam symbolu procenta závisí na jeho použití.<br/><br/> `%{HTTPVariable}`: Tato syntaxe identifikuje proměnnou HTTP.<br/>`%{HTTPVariable%Pattern}`: Tato syntaxe používá symbol procenta k identifikaci proměnné HTTP a jako oddělovač.<br />`\%`: Unikání procentuálního symbolu umožňuje použití jako literálovou hodnotu `\%20`nebo označení kódování adresy URL (například).
\* | Hvězdička umožňuje předchozí znak, který má odpovídat nula nebo vícekrát.
Místo | Znak mezery je obvykle považován za literál znak.
"hodnota" | Jednoduché uvozovky jsou považovány za literál znaky. Sada jednoduchých uvozovek nemá zvláštní význam.

Podmínky a funkce shody, které podporují regulární výrazy, přijímají vzory definované regulárními výrazy kompatibilními s perlem (PCRE).

## <a name="next-steps"></a>Další kroky

- [Pravidla, které jsou v souladu s podmínkami motoru](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Podmíněné výrazy modulu pravidel](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Funkce modulu pravidel](cdn-verizon-premium-rules-engine-reference-features.md)
- [Přepsat chování PROTOKOLU HTTP pomocí modulu pravidel](cdn-verizon-premium-rules-engine.md)
- [Přehled azure cdn](cdn-overview.md)
