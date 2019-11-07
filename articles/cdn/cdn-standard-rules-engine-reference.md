---
title: Referenční příručka stroje Azure CDN Standard Rules | Microsoft Docs
description: Referenční dokumentace pro Azure CDN podmínky a akce shody stroje standardních pravidel
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: magattus
ms.openlocfilehash: 6fb7e704f3d33cff8c29386b8aba9d8289037cbb
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2019
ms.locfileid: "73615933"
---
# <a name="azure-cdn-from-microsoft-rules-engine-reference"></a>Referenční informace pro Azure CDN od Microsoft Rule Engine

V tomto článku jsou uvedené podrobné popisy dostupných podmínek a funkcí shody pro [modul Standard Rules](cdn-standard-rules-engine.md)Azure Content DELIVERY Network (CDN).

Modul pravidel je navržený tak, aby měl koncovou autoritu týkající se způsobu, jakým služba CDN zpracovává konkrétní typy požadavků.

**Běžné použití**:

- Přepsat nebo definovat vlastní zásadu mezipaměti.
- Přesměrování požadavků.
- Úprava hlaviček požadavků a odpovědí HTTP

## <a name="terminology"></a>Terminologie

Pravidlo je definováno pomocí [**podmínek shody**](cdn-standard-rules-engine-match-conditions.md)a [**akcí**](cdn-standard-rules-engine-actions.md). Tyto prvky jsou zvýrazněny na následujícím obrázku:

 ![Struktura pravidel CDN](./media/cdn-standard-rules-engine-reference/cdn-rules-structure.png)

Každé pravidlo může mít až 4 podmínky shody a 3 akce. Existuje maximálně 5 pravidel na koncový bod CDN. Kromě toho je ve výchozím nastavení použito pravidlo s názvem **globální pravidlo**. Jedná se o pravidlo bez podmínek shody, kde se akce definované v rámci budou vždycky aktivovat. Toto pravidlo je zahrnuté v aktuálním omezení 5 pravidel.

## <a name="syntax"></a>Syntaxe

Způsob, jakým jsou zpracovány speciální znaky, se liší podle toho, jak podmínka shody nebo actopm zpracovává textové hodnoty. Podmínka nebo funkce shody může interpretovat text jedním z následujících způsobů:

1. [**Hodnoty literálu**](#literal-values)
2. [**Zástupné hodnoty**](#wildcard-values)


### <a name="literal-values"></a>Hodnoty literálu

Text, který je interpretován jako hodnota literálu, zpracovává všechny speciální znaky s výjimkou% symbol, jako součást hodnoty, která musí být shodná. Jinými slovy podmínka shody literálů nastavená na `\'*'\` je splněna pouze v případě, že je zjištěna přesná hodnota (tj. `\'*'\`).

K označení kódování adresy URL (například `%20`) se používá znak procenta.

### <a name="wildcard-values"></a>Zástupné hodnoty

Text, který je interpretován jako zástupná hodnota, přiřadí další význam pro speciální znaky. Následující tabulka popisuje, jak je interpretována následující sada znaků:

Optické | Popis
----------|------------
\ | Zpětné lomítko se používá k úniku znaků určených v této tabulce. Zpětné lomítko musí být zadáno přímo před speciálním znakem, který by měl být uvozen řídicím znakem.<br/>Například následující syntaxe řídí hvězdičku: `\*`
% | K označení kódování adresy URL (například `%20`) se používá znak procenta.
\* | Hvězdička je zástupný znak, který reprezentuje jeden nebo více znaků.
Kosmické aktivity | Znak mezery označuje, že podmínka shody může být splněna některou ze zadaných hodnot nebo vzorů.
osa | Jedna nabídka nemá zvláštní význam. Sada jednoduchých uvozovek však používá k označení toho, že by měla být hodnota považována za hodnotu literálu. Dá se použít následujícími způsoby:<br><br/>– Umožňuje splnění podmínky shody vždy, když zadaná hodnota odpovídá jakékoli části hodnoty porovnání.  `'ma'` by například odpovídaly jakémukoli z následujících řetězců: <br/><br/>/Business/**ma**rathon/Asset.htm<br/>**ma**p. gif<br/>/business/template. **ma**p<br /><br />– Umožňuje zadat speciální znak jako literální znak. Můžete například zadat literální znak mezery ohraničujícím znakem mezery v rámci sady jednoduchých uvozovek (tj. `' '` nebo `'sample value'`).<br/>– Povoluje zadání prázdné hodnoty. Zadáním sady jednoduchých uvozovek (tj.) zadejte prázdnou hodnotu.<br /><br/>**Důležité:**<br/>– Pokud zadaná hodnota neobsahuje zástupný znak, je automaticky považována za literálovou hodnotu, což znamená, že není nutné zadávat sadu jednoduchých uvozovek.<br/>– Pokud zpětné lomítko neřídí jiný znak v této tabulce, je ignorováno, pokud je zadáno v rámci sady jednoduchých uvozovek.<br/>– Jiný způsob, jak zadat speciální znak jako literální znak, je vystavení řídicího znaku pomocí zpětného lomítka (to znamená `\`).

## <a name="next-steps"></a>Další kroky

- [Podmínky shody stroje standardních pravidel](cdn-standard-rules-engine-match-conditions.md)
- [Akce stroje standardních pravidel](cdn-standard-rules-engine-actions.md)
- [Vynutilit HTTPS pomocí modulu Standard Rules](cdn-standard-rules-engine.md)
- [Přehled Azure CDN](cdn-overview.md)
