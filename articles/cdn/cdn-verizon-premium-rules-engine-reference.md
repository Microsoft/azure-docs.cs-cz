---
title: Referenční informace ke stroji pravidel Azure CDN | Dokumentace Microsoftu
description: Referenční dokumentace pro Azure CDN pravidla podmínky shody stroje a funkce.
services: cdn
author: mdgattuso
ms.service: cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus
ms.openlocfilehash: a04fcd3eaaed5c3e43f631ad1fbb6fed93ea29fb
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2019
ms.locfileid: "66481681"
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
% | Význam symbol procenta závisí na jeho využití.<br/><br/> `%{HTTPVariable}`: Tato syntaxe identifikuje proměnnou HTTP.<br/>`%{HTTPVariable%Pattern}`: Tato syntaxe používá k identifikaci protokolu HTTP, proměnných a jako oddělovač symbol procenta.<br />`\%`: Uvozovací znaky symbol procenta umožňuje použít jako hodnotu literálu nebo označení kódování URL (například `\%20`).
\* | Hvězdička umožňuje předchozí znak pro porovnání nulakrát nebo vícekrát.
Kosmické aktivity | Znak mezery, je obvykle považována za literální znak.
"hodnota" | Jednoduché uvozovky jsou považovány za literální znaky. Sada jednoduchých uvozovek a nemá žádné zvláštní význam.

## <a name="next-steps"></a>Další postup

- [Podmínky shody stroje pravidel](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Podmíněné výrazy stroje pravidel](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Funkce stroje pravidel](cdn-verizon-premium-rules-engine-reference-features.md)
- [Potlačení chování HTTP pomocí stroje pravidel](cdn-verizon-premium-rules-engine.md)
- [Přehled služby Azure CDN](cdn-overview.md)