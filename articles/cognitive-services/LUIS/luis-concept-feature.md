---
title: Funkce v aplikacích Azure Cognitive Services LUIS
titleSuffix: Azure Cognitive Services
description: Přidání funkcí do jazykového modelu poskytnout nápovědu, jak rozpoznat vstup, který chcete klasifikovat a označovat. Funkce pomáhají LUIS rozpoznat záměry a entity.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 015679b6020e9d2a4d702f9d6e723ecd9499d8dc
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47034463"
---
# <a name="phrase-list-features-in-luis"></a>Seznam funkcí frázi v LUIS

Ve službě machine learning *funkce* rozlišovací vlastností nebo atribut data, která dodržuje vašeho systému. 

Přidání funkcí do jazykového modelu poskytnout nápovědu, jak rozpoznat vstup, který chcete klasifikovat a označovat. Funkce pomáhají LUIS rozpoznat záměry a entity, ale funkce nejsou záměry a entity sami. Místo toho funkce může poskytnout příklady jejími podmínkami.  

## <a name="what-is-a-phrase-list-feature"></a>Co je součástí seznamu frází?
Fráze seznam obsahuje skupinu hodnot (slova nebo fráze), která patří do stejné třídy a musí být zacházeno podobně (například názvy města nebo produkty). Služba LUIS dozvídá o jeden z nich se automaticky využije na ostatní také. Tento seznam není uzavřená [seznam entit](luis-concept-entity-types.md#types-of-entities) (přesné shody text) z odpovídajících slov.

Seznam frází přidá do slovníku domény aplikace jako druhý signál k LUIS o těchto slov.

## <a name="how-to-use-phrase-lists"></a>Použití seznamů fráze
V aplikaci lidských zdrojů [kurz jednoduchou entitu](luis-quickstart-primary-and-secondary-data.md), tato aplikace používá **úlohy** frázi seznam typů úloh, jako je například programátora, roofer a bylo. Pokud popisek jednu z těchto hodnot jako entita se naučili počítač LUIS naučí rozeznat ostatní. 

Fráze seznamu může být zaměnitelné nebo zaměnitelné. *Zaměnitelné* je frázi seznam hodnot, které jsou synonyma, a *-zaměnitelné* frázi seznam je určený pro hodnoty, které nejsou synonyma však stále potřebovat další signál v aplikaci. 

<a name="phrase-lists-help-identify-simple-exchangeable-entities"></a>
## <a name="phrase-lists-help-identify-simple-interchangeable-entities"></a>Fráze uvádí nápovědy identifikovat jednoduchých zaměnitelné entit
Zaměňovat frázi seznamy jsou dobrým způsobem, jak optimalizovat výkon vaší aplikace LUIS. Pokud má vaše aplikace potíže při predikci projevy na správné záměr nebo rozpoznávání entit, zamyslete se, jestli projevy obsahovat neobvyklé slova ani slova, která může být nejednoznačný ve smyslu. Tato slova jsou vhodnými kandidáty zahrnout do seznamu frázi.

## <a name="phrase-lists-help-identify-intents-by-better-understanding-context"></a>Fráze uvádí nápovědy identifikovat záměry lepší porozumění kontextu
Seznam frázi není instrukce k LUIS provést odpovídající strict nebo vždy označit všechny podmínky v seznamu frázi stejně. Je jednoduše nápovědu. Například můžete mít seznam frázi, která označuje, že "Patti" a "Selma" jsou názvy, ale LUIS můžete stále použít kontextové informace k rozpoznání něco jiného v znamenají "Provést rezervaci 2 ve vaší Patti Diner večeře" a "Nepracuju, řízení Pokyny k Selma, Gruzie". 

Přidání seznamu frázi je alternativa k přidání další příklad projevy záměru. 

## <a name="an-interchangeable-phrase-list"></a>V seznamu zaměnitelné fráze
Při vytváření seznamu slov nebo frází třídu nebo skupinu, pomocí seznamu zaměnitelné frázi. Příkladem je seznam měsíců jako "Od", "Února", "Března"; nebo "John", "Jan", "Frank", jako jsou názvy.  Tyto seznamy jsou zaměnitelné, utterance by popisek se stejným cílem nebo entity, pokud byly použity různé slova v seznamu frázi. Například pokud "Zobrazit kalendáře pro leden" má stejnou záměru jako "Zobrazit kalendáře pro dne" a pak slova by měla být v přehledu zaměnitelné. 

## <a name="a-non-interchangeable-phrase-list"></a>Seznam-zaměnitelné frází
Použijte seznam bez zaměnitelné frázi-synonymní slova nebo fráze, které mohou být seskupeny ve vaší doméně. 

Jako příklad pomocí seznamu-zaměnitelné frázi vzácné, proprietární a cizí slova. Služba LUIS možná nebudete moct rozpoznat vzácné a proprietární slova, jakož i cizí slova (mimo jazykovou verzi aplikace). Zaměnitelné nastavení znamená, že sadu výjimečných slova tvoří třídu, která by měla služba LUIS učení se rozpoznávání, ale nejsou synonyma nebo mezi sebou vzájemně zaměnitelné.

## <a name="when-to-use-phrase-lists-versus-list-entities"></a>Kdy použít seznamy frázi a seznam entit
Zatímco frázi seznamu a seznam entit může mít vliv na projevy přes všechny záměrů, mají funkce to jiným způsobem. Pomocí seznamu frázi vliv na skóre záměru předpovědi. Použijte seznam entit ovlivnit extrakce entity shodu přesný text. 

### <a name="use-a-phrase-list"></a>Použijte seznam fráze
Se seznamem frázi LUIS stále vezměte v úvahu kontext a generalizace k identifikaci položky, které jsou podobné, ale není přesná shoda, jako položky v seznamu. Pokud potřebujete aplikaci LUIS tak, aby mohli generalize a identifikaci nových položek v kategorii, použijte seznam frázi. 

Pokud chcete být schopni rozpoznat nové instance entity, jako je plánovače schůzku, který by měl rozpoznávat názvy nových kontaktů, nebo aplikaci inventář, který by měl rozpoznat nové produkty, použijte jiný typ počítače zjistili entitu, jako je jednoduchý nebo hierarchické entity. Pak vytvořte frázi seznam slova a slovní spojení, která pomáhá LUIS, hledání podobně jako v entitě jiná slova. Tento seznam provede LUIS rozpoznat příklady entity tak, že přidáte další význam hodnotu z těchto slov. 

Fráze seznamy jsou jako slovník jazyka specifického pro doménu, která usnadní vylepšení kvality znalost záměry a entity. Běžné použití seznamu frázi je podstatná jména správných například názvy měst. Název města, může být několik slova, včetně pomlček nebo apostrofy.
 
### <a name="dont-use-a-phrase-list"></a>Nepoužívejte seznam frází 
Seznam entit explicitně definuje každá hodnota může trvat entity a identifikuje pouze hodnoty, které přesně odpovídají. Seznam entit může být vhodný pro aplikaci, ve kterém je známo, že všechny instance entity a neměnit často. Příklady jsou potravin položky v nabídce restaurace, která mění jen zřídka. Pokud potřebujete přesný text výskyty entity, nepoužívejte seznam frázi. 

## <a name="best-practices"></a>Osvědčené postupy
Přečtěte si [osvědčené postupy](luis-concept-best-practices.md).

## <a name="next-steps"></a>Další postup

Zobrazit [přidat funkce](luis-how-to-add-features.md) získat další informace o tom, jak přidat funkce do vaší aplikace LUIS.
