---
title: Pochopení funkce v aplikacích LUIS v Azure | Dokumentace Microsoftu
description: Další informace o funkcích, které pomůže zlepšit výkon aplikace LUIS. Funkce zahrnují seznamy frázi a vzory pro rozpoznání regulární výrazy.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 04/18/2018
ms.author: v-geberr
ms.openlocfilehash: 597948947303b7fdf16f24576620d6f39d7c51f4
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/06/2018
ms.locfileid: "37887442"
---
# <a name="phrase-list-features-in-luis"></a>Seznam funkcí frázi v LUIS

Ve službě machine learning *funkce* rozlišovací vlastností nebo atribut data, která dodržuje vašeho systému. 

Přidání funkcí do jazykového modelu poskytnout nápovědu, jak rozpoznat vstup, který chcete klasifikovat a označovat. Funkce pomáhají LUIS rozpoznat záměry a entity, ale funkce nejsou záměry a entity sami. Místo toho funkce může poskytnout příklady jejími podmínkami.  

## <a name="what-is-a-phrase-list-feature"></a>Co je součástí seznamu frází?
Fráze seznam obsahuje skupinu hodnot (slova nebo fráze), která patří do stejné třídy a musí být zacházeno podobně (například názvy města nebo produkty). Služba LUIS dozvídá o jeden z nich se automaticky využije na ostatní také. Toto není uzavřená [seznam entit](luis-concept-entity-types.md#types-of-entities) (přesné shody text) z odpovídajících slov.

Seznam frází přidá do slovníku domény aplikace jako druhý signál k LUIS o těchto slov.

## <a name="how-to-use-phrase-lists"></a>Použití seznamů fráze
V aplikaci agenta cesty vytvořte seznam frázi s názvem "Cities", který obsahuje hodnoty, Londýn, Paříž a Cairo. Pokud jedna z těchto hodnot označit jako jednoduchou entitu v [příklad utterance](luis-how-to-add-example-utterances.md#add-simple-entity-label) v záměru, naučí rozeznat ostatní LUIS. 

Fráze seznamu může být zaměnitelné nebo zaměnitelné. *Zaměnitelné* je frázi seznam hodnot, které jsou synonyma, a *-zaměnitelné* frázi seznam je určený pro hodnoty, které nejsou synonyma, ale jsou podobné jiným způsobem. 

## <a name="phrase-lists-help-identify-simple-exchangeable-entities"></a>Fráze uvádí nápovědy identifikovat jednoduchých exchangeable entit
Exchangeable frázi seznamy jsou dobrým způsobem, jak optimalizovat výkon vaší aplikace LUIS. Pokud má vaše aplikace potíže při predikci projevy na správné záměr nebo rozpoznávání entit, zamyslete se, jestli projevy obsahovat neobvyklé slova ani slova, která může být nejednoznačný ve smyslu. Tato slova jsou vhodnými kandidáty zahrnout do seznamu frázi.

## <a name="phrase-lists-help-identify-intents-by-better-understanding-context"></a>Fráze uvádí nápovědy identifikovat záměry lepší porozumění kontextu
Použijte frázi seznamy pro vzácné, proprietární a cizí slova. LUIS možná nebude moci rozpoznat vzácné a proprietární slova, jakož i cizí slova (mimo jazykovou verzi aplikace), a proto měly by být přidány do seznamu frázi. Tento seznam frázi by měla být označena bez zaměňovat, označuje, že sadu výjimečných slova tvoří třídu, která by měla služba LUIS učení se rozpoznávání, ale nejsou synonyma nebo mezi sebou vzájemně zaměnitelné.

Seznam frázi není instrukce k LUIS provést odpovídající strict nebo vždy označit všechny podmínky v seznamu frázi stejně. Je jednoduše nápovědu. Například můžete mít seznam frázi, která označuje, že "Patti" a "Selma" jsou názvy, ale LUIS můžete stále použít kontextové informace k rozpoznání něco jiného v znamenají "Provést rezervaci 2 ve vaší Patti Diner večeře" a "Nepracuju, řízení Pokyny k Selma, Gruzie". 

Přidání seznamu frázi je alternativa k přidání další příklad projevy záměru. 

## <a name="when-to-use-phrase-lists-versus-list-entities"></a>Kdy použít seznamy frázi a seznam entit
Zatímco frázi seznamu a seznam entit může mít vliv na projevy přes všechny záměrů, mají funkce to jiným způsobem. Pomocí seznamu frázi vliv na skóre záměru předpovědi. Použijte seznam entit ovlivnit extrakce entity shodu přesný text. 

### <a name="use-a-phrase-list"></a>Použijte seznam fráze
Se seznamem frázi LUIS stále vezměte v úvahu kontext a generalizace k identifikaci položky, které jsou podobné, ale není přesná shoda, jako položky v seznamu. Pokud potřebujete aplikaci LUIS tak, aby mohli generalize a identifikaci nových položek v kategorii, použijte seznam frázi. 

Pokud chcete být schopni rozpoznat nové instance entity, jako je plánovače schůzku, který by měl rozpoznávat názvy nových kontaktů, nebo aplikaci inventář, který by měl rozpoznat nové produkty, použijte jiný typ počítače zjistili entitu, jako je jednoduchý nebo hierarchické entity. Pak vytvořte frázi seznam slova a slovní spojení, která pomáhá LUIS, hledání podobně jako v entitě jiná slova. Tento seznam provede LUIS rozpoznat příklady entity tak, že přidáte další význam hodnotu z těchto slov. 

Fráze seznamy jsou jako slovník jazyka specifického pro doménu, která usnadní vylepšení kvality znalost záměry a entity. Běžné použití seznamu frázi je podstatná jména správných například názvy měst. Název města, může být několik slova, včetně pomlček nebo apostrofy.
 
### <a name="dont-use-a-phrase-list"></a>Nepoužívejte seznam frází 
Seznam entit explicitně definuje každá hodnota může trvat entity a identifikuje pouze hodnoty, které přesně odpovídají. Seznam entit může být vhodné pro aplikace 00Z všechny instance entity jsou známé a nemění často, stejně jako potravin položky v nabídce restaurace, která mění jen zřídka. Pokud potřebujete přesný text výskyty entity, nepoužívejte seznam frázi. 

## <a name="best-practices"></a>Osvědčené postupy
Přečtěte si [osvědčené postupy](luis-concept-best-practices.md).

## <a name="next-steps"></a>Další postup

Zobrazit [přidat funkce](luis-how-to-add-features.md) získat další informace o tom, jak přidat funkce do vaší aplikace LUIS.
