---
title: Referenční informace k modulu Azure CDN Rules | Microsoft Docs
description: Referenční dokumentace ke službě Azure CDN Rules Engine podmínky a funkce.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus
ms.openlocfilehash: 5fc611af75a7f733576f9343a4375fb56cacc030
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2019
ms.locfileid: "67593152"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-reference"></a>Odkazy na modul Azure CDN z Verizon Premium Rules

V tomto článku jsou uvedené podrobné popisy dostupných podmínek a funkcí shody pro [modul pravidel](cdn-verizon-premium-rules-engine.md)Azure Content DELIVERY Network (CDN).

Modul pravidel je navržený tak, aby měl koncovou autoritu týkající se způsobu, jakým služba CDN zpracovává konkrétní typy požadavků.

**Běžné použití**:

- Přepsat nebo definovat vlastní zásadu mezipaměti.
- Zabezpečte nebo zakažte požadavky na citlivý obsah.
- Přesměrování požadavků.
- Ukládat data vlastního protokolu.

## <a name="terminology"></a>Terminologie

Pravidlo je definováno pomocí [**podmíněného výrazu**](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md), [**podmínek shody**](cdn-verizon-premium-rules-engine-reference-match-conditions.md)a [**funkcí**](cdn-verizon-premium-rules-engine-reference-features.md). Tyto prvky jsou zvýrazněny na následujícím obrázku:

 ![Podmínka párování CDN](./media/cdn-rules-engine-reference/cdn-rules-engine-terminology.png)

## <a name="syntax"></a>Syntaxe

Způsob, jakým jsou zpracovány speciální znaky, se liší podle toho, jak podmínka shody nebo funkce zpracovává textové hodnoty. Podmínka nebo funkce shody může interpretovat text jedním z následujících způsobů:

1. [**Hodnoty literálu**](#literal-values)
2. [**Zástupné hodnoty**](#wildcard-values)
3. [**Regulární výrazy**](#regular-expressions)

### <a name="literal-values"></a>Hodnoty literálu

Text, který je interpretován jako hodnota literálu, zpracovává všechny speciální znaky s výjimkou% symbol, jako součást hodnoty, která musí být shodná. Jinými slovy podmínka shody literálů nastavená na `\'*'\` je splněná jenom v případě, že se najde tato přesná hodnota (tj `\'*'\`.).

Symbol procenta se používá k označení kódování adresy URL (například `%20`).

### <a name="wildcard-values"></a>Zástupné hodnoty

Text, který je interpretován jako zástupná hodnota, přiřadí další význam pro speciální znaky. Následující tabulka popisuje, jak je interpretována následující sada znaků:

Znak | Popis
----------|------------
\ | Zpětné lomítko se používá k úniku znaků určených v této tabulce. Zpětné lomítko musí být zadáno přímo před speciálním znakem, který by měl být uvozen řídicím znakem.<br/>Například následující syntaxe řídí hvězdičku:`\*`
% | Symbol procenta se používá k označení kódování adresy URL (například `%20`).
\* | Hvězdička je zástupný znak, který reprezentuje jeden nebo více znaků.
Mezera | Znak mezery označuje, že podmínka shody může být splněna některou ze zadaných hodnot nebo vzorů.
osa | Jedna nabídka nemá zvláštní význam. Sada jednoduchých uvozovek však používá k označení toho, že by měla být hodnota považována za hodnotu literálu. Dá se použít následujícími způsoby:<br><br/>– Umožňuje splnění podmínky shody vždy, když zadaná hodnota odpovídá jakékoli části hodnoty porovnání.  Například `'ma'` by odpovídaly jakémukoli z následujících řetězců: <br/><br/>/business/**ma**rathon/asset.htm<br/>**ma**p.gif<br/>/business/template. **ma**p<br /><br />– Umožňuje zadat speciální znak jako literální znak. Například můžete zadat literální znak mezery ohraničujícím znakem mezery v rámci sady jednoduchých uvozovek (tj `' '` . nebo `'sample value'`).<br/>– Povoluje zadání prázdné hodnoty. Zadáním sady jednoduchých uvozovek (tj.) zadejte prázdnou hodnotu.<br /><br/>**Důležité:**<br/>– Pokud zadaná hodnota neobsahuje zástupný znak, je automaticky považována za literálovou hodnotu, což znamená, že není nutné zadávat sadu jednoduchých uvozovek.<br/>– Pokud zpětné lomítko neřídí jiný znak v této tabulce, je ignorováno, pokud je zadáno v rámci sady jednoduchých uvozovek.<br/>– Jiný způsob, jak zadat speciální znak jako literální znak, je vystavení pomocí zpětného lomítka (tj `\`.).

### <a name="regular-expressions"></a>Regulární výrazy

Regulární výrazy definují vzor, který je prohledáván v rámci textové hodnoty. Zápis regulárního výrazu definuje konkrétní význam pro nejrůznější symboly. Následující tabulka uvádí, jak jsou speciální znaky ošetřeny podmínkami shody a funkcemi, které podporují regulární výrazy.

Speciální znak | Popis
------------------|------------
\ | Zpětné lomítko řídí znak, který následuje, což způsobí, že tento znak bude považován za hodnotu literálu namísto převzetí jeho regulárního výrazu. Například následující syntaxe řídí hvězdičku:`\*`
% | Význam symbolu procenta závisí na jeho využití.<br/><br/> `%{HTTPVariable}`: Tato syntaxe identifikuje proměnnou HTTP.<br/>`%{HTTPVariable%Pattern}`: Tato syntaxe používá symbol procenta k identifikaci proměnné HTTP a jako oddělovače.<br />`\%`: Uvozovací znak v procentech umožňuje použití jako literálové hodnoty nebo k označení kódování adresy URL (například `\%20`).
\* | Hvězdička umožňuje, aby předchozí znak odpovídal nule nebo vícekrát.
Mezera | Znak mezery je obvykle považován za literální znak.
osa | Jednoduché uvozovky se považují za literální znaky. Sada jednoduchých uvozovek nemá zvláštní význam.

## <a name="next-steps"></a>Další postup

- [Podmínky shody stroje pravidel](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Podmíněné výrazy stroje pravidel](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Funkce stroje pravidel](cdn-verizon-premium-rules-engine-reference-features.md)
- [Přepsání chování HTTP pomocí modulu pravidel](cdn-verizon-premium-rules-engine.md)
- [Přehled Azure CDN](cdn-overview.md)