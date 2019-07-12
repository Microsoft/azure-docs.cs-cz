---
title: Referenční informace ke stroji pravidel Azure CDN | Dokumentace Microsoftu
description: Referenční dokumentace pro Azure CDN pravidla podmínky shody stroje a funkce.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus
ms.openlocfilehash: 5fc611af75a7f733576f9343a4375fb56cacc030
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593152"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-reference"></a>Azure CDN od Verizonu Premium referenční informace ke stroji pravidel

Tento článek obsahuje podrobný popis funkcí a podmínky k dispozici shody pro sítě Azure Content Delivery Network (CDN) [stroj pravidel](cdn-verizon-premium-rules-engine.md).

Stroj pravidel je navržena jako poslední oprávnění na tom, jak konkrétní typy žádostí jsou zpracovány CDN.

**Běžná použití**:

- Přepsat nebo můžete definovat vlastní mezipaměti zásad.
- Zajištění nebo zamítnutí žádosti o citlivého obsahu.
- Přesměrování požadavků.
- Vlastní protokol data Store.

## <a name="terminology"></a>Terminologie

Pravidlo je definováno prostřednictvím [ **podmíněné výrazy**](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md), [ **splňují podmínky**](cdn-verizon-premium-rules-engine-reference-match-conditions.md), a [ **funkce**](cdn-verizon-premium-rules-engine-reference-features.md). Na následujícím obrázku jsou zvýrazněné tyto prvky:

 ![Podmínka shody CDN](./media/cdn-rules-engine-reference/cdn-rules-engine-terminology.png)

## <a name="syntax"></a>Syntaxe

Způsob, ve kterém speciální znaky jsou zpracovávány se liší podle způsobu, jakým funkci a podmínce shody zpracovává textové hodnoty. Podmínky shody nebo funkce může interpretovat text v jednom z následujících způsobů:

1. [**Hodnoty literálu**](#literal-values)
2. [**Hodnoty zástupných znaků**](#wildcard-values)
3. [**Regulární výrazy**](#regular-expressions)

### <a name="literal-values"></a>Hodnoty literálu

Text, který je interpretován jako hodnota literálu zpracovává všechny speciální znaky s výjimkou % symbol, jako součást hodnotu, která musí odpovídat. Jinými slovy, literál odpovídají podmínce nastavena na `\'*'\` je splněna pouze při, který přesnou hodnotu (to znamená, `\'*'\`) se nenašel.

Symbol procenta slouží k určení kódování URL (například `%20`).

### <a name="wildcard-values"></a>Hodnoty zástupných znaků

Text, který je interpretován jako hodnota zástupného znaku přiřadí další významy speciální znaky. Následující tabulka popisuje, jak je interpretován následujícím množinu znaků:

Znak | Popis
----------|------------
\ | Zpětné lomítko se používá k uvození znaky zadané v této tabulce. Zpětné lomítko je třeba zadat přímo před speciální znak, který by měl být uvozeny řídicími znaky.<br/>Například následující syntaxe řídicí sekvence hvězdičku: `\*`
% | Symbol procenta slouží k určení kódování URL (například `%20`).
\* | Hvězdičku představuje zástupný znak, který představuje jeden nebo více znaků.
Kosmické aktivity | Znak mezery označuje, že může být splněna podmínka shoda podle zadané hodnoty nebo vzory.
"hodnota" | Jednoduchá uvozovka nemá žádné zvláštní význam. K označení, že by měla s hodnotou zacházet jako s hodnotou literálu se ale používá sadu jednoduchých uvozovek. Je možné následujícími způsoby:<br><br/>– Umožňuje podmínku shody vyhovět pokaždé, když se zadanou hodnotou odpovídá jakékoli její části hodnotu porovnání.  Například `'ma'` by shodují s některým z následujících řetězců: <br/><br/>/business/**ma**rathon/asset.htm<br/>**ma**p.gif<br/>/ obchodní/šablony. **ma**p<br /><br />– Umožňuje zadat jako literální znak zvláštní znak. Můžete například zadat literální znak mezery uzavřením znak mezery v rámci jednoduché uvozovky (to znamená `' '` nebo `'sample value'`).<br/>– Umožňuje zadat prázdnou hodnotu. Zadejte prázdnou hodnotu tak, že zadáte sadu jednoduché uvozovky (to znamená, '').<br /><br/>**Důležité:**<br/>– Pokud je zadaná hodnota neobsahuje zástupný znak, pak bude automaticky považován za hodnotu literálu, což znamená, že není nutné určit sadu jednoduchých uvozovek.<br/>– Pokud je zpětné lomítko neuvozuje další znak v této tabulce, je ignorována, pokud je zadaný v rámci jednoduchých uvozovek.<br/>-Další způsob, jak zadat speciální znaky jako literální znak escape pomocí zpětného lomítka (to znamená `\`).

### <a name="regular-expressions"></a>Regulární výrazy

Regulární výrazy definovat vzor, který se hledá v rámci textovou hodnotu. Zápis regulárního výrazu definuje konkrétní význam širokou škálu symboly. Následující tabulka uvádí, jak speciálních znaků nakládá podmínky shody a funkcí, které podporují regulární výrazy.

Speciální znak | Popis
------------------|------------
\ | Zpětné lomítko řídicí sekvence znaku pomocí následujícího it, což způsobí, že daný znak do zacházet jako s hodnotou literálu místo s ohledem na jeho význam regulární výraz. Například následující syntaxe řídicí sekvence hvězdičku: `\*`
% | The meaning of a percentage symbol depends on its usage.<br/><br/> `%{HTTPVariable}`: This syntax identifies an HTTP variable.<br/>`%{HTTPVariable%Pattern}`: This syntax uses a percentage symbol to identify an HTTP variable and as a delimiter.<br />`\%`: Escaping a percentage symbol allows it to be used as a literal value or to indicate URL encoding (for example, `\%20`).
\* | An asterisk allows the preceding character to be matched zero or more times.
Space | A space character is typically treated as a literal character.
'value' | Single quotes are treated as literal characters. A set of single quotes does not have special meaning.

## Next steps

- [Rules engine match conditions](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Rules engine conditional expressions](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Rules engine features](cdn-verizon-premium-rules-engine-reference-features.md)
- [Override HTTP behavior using the rules engine](cdn-verizon-premium-rules-engine.md)
- [Azure CDN overview](cdn-overview.mdVýznam symbol procenta závisí na jeho využití. Docs
descr`%{HTTPVariable}`: Tato syntaxe identifikuje proměnnou HTTP.match`%{HTTPVariable%Pattern}`: Tato syntaxe používá k identifikaci protokolu HTTP, proměnných a jako oddělovač symbol procenta.1/2019`\``: Escaping a percentage symbol allows it to be used as a literal value or to indicate URL encoding (for example, `\`20`).f the available match conditions and features for the Azure Content Delivery Network (CDN) [rules engine](cdn-verizon-premium-rules-engine.md).

The rules engine is designed to be the final authority on how specific types of requests are processed by the CDN.

**Common uses**:

- Override or define a custom cache policy.
- Secure or deny requests for sensitive content.
- Redirect requests.
- Store custom log data.

## Terminology

A rule is defined through the use of [**conditional expressions**](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md), [**match conditions**](cdn-verizon-premium-rules-engine-reference-match-conditions.md), and [**features**](cdn-verizon-premium-rules-engine-reference-features.md). These elements are highlighted in the following illustration:

 ![CDN match condition](./media/cdn-rules-engine-reference/cdn-rules-engine-terminology.png)

## Syntax

The manner in which special characters are treated varies according to how a match condition or feature handles text values. A match condition or feature may interpret text in one of the following ways:

1. [**Literal values**](#literal-values)
2. [**Wildcard values**](#wildcard-values)
3. [**Regular expressions**](#regular-expressions)

### Literal values

Text that is interpreted as a literal value treats all special characters, with the exception of the % symbol, as a part of the value that must be matched. In other words, a literal match condition set to `\'*'\` is only satisfied when that exact value (that is, `\'*'\`) is found.

A percentage symbol is used to indicate URL encoding (for example, `%20`).

### Wildcard values

Text that is interpreted as a wildcard value assigns additional meaning to special characters. The following table describes how the following set of characters is interpreted:

Character | Description
----------|------------
\ | A backslash is used to escape any of the characters specified in this table. A backslash must be specified directly before the special character that should be escaped.<br/>For example, the following syntax escapes an asterisk: `\*`
% | A percentage symbol is used to indicate URL encoding (for example, `%20`).
\* | An asterisk is a wildcard that represents one or more characters.
Space | A space character indicates that a match condition may be satisfied by either of the specified values or patterns.
'value' | A single quote does not have special meaning. However, a set of single quotes is used to indicate that a value should be treated as a literal value. It can be used in the following ways:<br><br/>- It allows a match condition to be satisfied whenever the specified value matches any portion of the comparison value.  For example, `'ma'` would match any of the following strings: <br/><br/>/business/**ma**rathon/asset.htm<br/>**ma**p.gif<br/>/business/template.**ma**p<br /><br />- It allows a special character to be specified as a literal character. For example, you may specify a literal space character by enclosing a space character within a set of single quotes (that is, `' '` or `'sample value'`).<br/>- It allows a blank value to be specified. Specify a blank value by specifying a set of single quotes (that is, '').<br /><br/>**Important:**<br/>- If the specified value does not contain a wildcard, then it is automatically considered a literal value, which means that it is not necessary to specify a set of single quotes.<br/>- If a backslash does not escape another character in this table, it is ignored when it is specified within a set of single quotes.<br/>- Another way to specify a special character as a literal character is to escape it using a backslash (that is, `\`).

### Regular expressions

Regular expressions define a pattern that is searched for within a text value. Regular expression notation defines specific meanings to a variety of symbols. The following table indicates how special characters are treated by match conditions and features that support regular expressions.

Special Character | Description
------------------|------------
\ | A backslash escapes the character the follows it, which causes that character to be treated as a literal value instead of taking on its regular expression meaning. For example, the following syntax escapes an asterisk: `\*`
% | The meaning of a percentage symbol depends on its usage.<br/><br/> `%{HTTPVariable}`: This syntax identifies an HTTP variable.<br/>`%{HTTPVariable%Pattern}`: This syntax uses a percentage symbol to identify an HTTP variable and as a delimiter.<br />`\%`: Escaping a percentage symbol allows it to be used as a literal value or to indicate URL encoding (for example, `\%20`).
\* | Hvězdička umožňuje předchozí znak pro porovnání nulakrát nebo vícekrát.
Kosmické aktivity | Znak mezery, je obvykle považována za literální znak.
"hodnota" | Jednoduché uvozovky jsou považovány za literální znaky. Sada jednoduchých uvozovek a nemá žádné zvláštní význam.

## <a name="next-steps"></a>Další postup

- [Podmínky shody stroje pravidel](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Podmíněné výrazy stroje pravidel](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Funkce stroje pravidel](cdn-verizon-premium-rules-engine-reference-features.md)
- [Potlačení chování HTTP pomocí stroje pravidel](cdn-verizon-premium-rules-engine.md)
- [Přehled služby Azure CDN](cdn-overview.md)