---
title: Odkaz na modul Azure CDN pravidla | Microsoft Docs
description: Referenční dokumentace pro Azure CDN pravidla shody stav motoru a funkce.
services: cdn
documentationcenter: ''
author: Lichard
manager: akucer
editor: ''
ms.assetid: 669ef140-a6dd-4b62-9b9d-3f375a14215e
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: rli
ms.openlocfilehash: 602b4303dd1940791c11b8b71ac6a27f0474a6d5
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2018
ms.locfileid: "29733675"
---
# <a name="azure-cdn-rules-engine-reference"></a>Odkaz na modul pravidla ve službě Azure CDN
Tento článek obsahuje seznam podrobný popis funkcí a podmínky k dispozici shodu pro Content Delivery Network (CDN) Azure [stroj pravidel](cdn-rules-engine.md).

Stroj pravidel slouží jako konečné právo na tom, jak konkrétní typy žádostí jsou zpracovávány CDN.

**Běžná použití**:

- Přepsání nebo definovat zásady vlastní mezipaměti.
- Zabezpečení nebo odmítnout požadavky pro citlivého obsahu.
- Přesměrování požadavků.
- Uložit data vlastního protokolu.

## <a name="terminology"></a>Terminologie
Pravidlo je definováno prostřednictvím [ **podmíněné výrazy**](cdn-rules-engine-reference-conditional-expressions.md), [ **splňují podmínky**](cdn-rules-engine-reference-match-conditions.md), a [ **funkce**](cdn-rules-engine-reference-features.md). Tyto prvky jsou vyznačené na následujícím obrázku:

 ![Stav shody CDN](./media/cdn-rules-engine-reference/cdn-rules-engine-terminology.png)

## <a name="syntax"></a>Syntaxe

Způsobem, ve kterém speciální znaky jsou zpracovávány závisí na způsobu, jakým podmínky shody nebo funkce zpracovává textové hodnoty. Stav shody nebo funkce mohou interpretovat text v jednom z následujících způsobů:

1. [**Literálových hodnot**](#literal-values) 
2. [**Hodnoty zástupných znaků**](#wildcard-values)
3. [**Regulární výrazy**](#regular-expressions)

### <a name="literal-values"></a>Literálových hodnot
Text, který je interpretována jako hodnota literálu zpracovává všechny speciální znaky, s výjimkou % symbol, jako součást hodnotu, která musí odpovídat. Jinými slovy, literál vyhovují podmínce nastavena na `\'*'\` je pouze splněné, při které přesnou hodnotu (tj, `\'*'\`) se nachází.
 
Symbol procenta slouží k označení kódování URL (například `%20`).

### <a name="wildcard-values"></a>Hodnoty zástupných znaků
Text, který je interpretován jako zástupný znak hodnotu přiřadí další významy speciální znaky. Následující tabulka popisuje, jak interpretovat následující sadu znaků:

Znak | Popis
----------|------------
\ | Abyste se vyhnuli znaky zadané v této tabulce se používá zpětné lomítko. Zpětné lomítko je třeba zadat přímo před speciální znak, který by měly být ukončeny.<br/>Například následující syntaxi řídicí sekvence hvězdičku: `\*`
% | Symbol procenta slouží k označení kódování URL (například `%20`).
* | Znak hvězdičky je zástupný znak, který reprezentuje jeden nebo více znaků.
Mezera | Znak mezery označuje, že shoda podmínku může vyhovět buď zadaným hodnotám nebo vzory.
'Hodnota' | V jednoduchých uvozovkách nemá zvláštní význam. Však sadu jednoduchých uvozovek a slouží k označení, že by měl být s hodnotou zacházet jako hodnota literálu. Dá se následujícími způsoby:<br><br/>– Umožňuje shodu podmínku, kterou chcete být splněna vždy, když je zadaná hodnota odpovídá jakékoli její části hodnotu porovnání.  Například `'ma'` odpovídá některé z následujících řetězců: <br/><br/>/business/**ma**rathon/asset.htm<br/>**ma**p.gif<br/>/business/template.**ma**p<br /><br />– Umožňuje zadat jako literál znak zvláštní znak. Můžete například určit literálu mezerou uzavřením znak mezery v rámci sady jednoduchých uvozovek (tedy `' '` nebo `'sample value'`).<br/>– Umožňuje na prázdnou hodnotu zadat. Zadejte prázdnou hodnotu zadáním sadu jednoduchých uvozovek (to znamená, ").<br /><br/>**Důležité:**<br/>– Pokud je zadaná hodnota neobsahuje zástupný znak, pak automaticky považuje literálovou hodnotou, což znamená, že není nutné zadávat sadu jednoduchých uvozovek.<br/>– Pokud zpětné lomítko není řídicí jiný znak v této tabulce, je ignorován, pokud je zadána v rámci sady jednoduchých uvozovek.<br/>-Jiný způsob, jak zadat speciální znaky, jako je literál znak pro přepnutí pomocí zpětné lomítko (tedy `\`).

### <a name="regular-expressions"></a>Regulární výrazy

Regulární výrazy definovat vzor, který je hledán v rámci textovou hodnotu. Zápis regulární výraz definuje určité významy pro celou řadu symboly. Následující tabulka uvádí, jak speciální znaky jsou považovány podmínky shody a funkce, které podporují regulární výrazy.

Speciální znak | Popis
------------------|------------
\ | Zpětné lomítko řídicí sekvence znak pomocí následujícího it, což způsobí, že tento znak, který má být považované za literálovou hodnotou místo s ohledem na její význam regulární výraz. Například následující syntaxi řídicí sekvence hvězdičku: `\*`
% | Význam symbol procento závisí na jeho použití.<br/><br/> `%{HTTPVariable}`: Tato syntaxe identifikuje Proměnná HTTP.<br/>`%{HTTPVariable%Pattern}`: Tento syntaxi symbol procenta používá k identifikaci protokolu HTTP, proměnné a jako oddělovač.<br />`\%`: To má být použit jako hodnota literálu nebo určete kódování URL uvozovací znaky symbol procento umožňuje (například `\%20`).
* | Znak hvězdičky umožňuje předchozí znak, který má odpovídat počtu nula či více krát. 
Mezera | Znak mezery je obvykle považovány za znak literálu. 
'Hodnota' | Jednoduchých uvozovek a jsou považovány za literály. Sadu jednoduchých uvozovek a nemá žádné zvláštní význam.


## <a name="next-steps"></a>Další postup
* [Stav shody motoru pravidla](cdn-rules-engine-reference-match-conditions.md)
* [Podmíněné výrazy stroj pravidel](cdn-rules-engine-reference-conditional-expressions.md)
* [Funkce modulu pravidla](cdn-rules-engine-reference-features.md)
* [Potlačení chování HTTP pomocí stroj pravidel](cdn-rules-engine.md)
* [Přehled služby Azure CDN](cdn-overview.md)
