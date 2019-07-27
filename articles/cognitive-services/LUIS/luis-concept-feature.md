---
title: Funkce – LUIS
titleSuffix: Azure Cognitive Services
description: Přidání funkcí do jazykového modelu poskytnout nápovědu, jak rozpoznat vstup, který chcete klasifikovat a označovat.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: diberry
ms.openlocfilehash: 1aed6f9a0ceec18ca800e5030ec09bbb8d98cb76
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68560717"
---
# <a name="phrase-list-features-in-your-luis-app"></a>Funkce seznamu frází ve vaší aplikaci LUIS

Ve službě machine learning *funkce* rozlišovací vlastností nebo atribut data, která dodržuje vašeho systému. 

Přidání funkcí do jazykového modelu poskytnout nápovědu, jak rozpoznat vstup, který chcete klasifikovat a označovat. Funkce pomáhají LUIS rozpoznat záměry a entity, ale funkce nejsou záměry a entity sami. Místo toho funkce může poskytnout příklady jejími podmínkami.  

## <a name="what-is-a-phrase-list-feature"></a>Co je součástí seznamu frází?
Seznam frází je seznam slov nebo frází, které jsou pro vaši aplikaci významné, více než ostatní slova v projevy. Seznam frází se přidá do slovníku domény aplikace jako další signál, který LUIS o těchto slovech. Služba LUIS dozvídá o jeden z nich se automaticky využije na ostatní také. Tento seznam není uzavřenou [entitou seznamu](luis-concept-entity-types.md#types-of-entities) přesně vyhovujících textů.

Seznamy frází neumožňují odvozování, takže je třeba přidat příklady utterance, které používají různé výrazy pro všechna významná slova a fráze typu slovního.

## <a name="phrase-lists-help-all-models"></a>Seznamy frází, které pomůžou všechny modely

Seznamy frází nejsou propojené s konkrétním záměrem nebo entitou, ale přidávají se jako významné zvýšení na všechny záměry a entity. Jejím účelem je zlepšit detekci záměrů a klasifikaci entit.

## <a name="how-to-use-phrase-lists"></a>Použití seznamů fráze

Vytvořte seznam frází, pokud má vaše aplikace slova nebo fráze, které jsou pro aplikaci důležité, například:

* oborové výrazy
* slangem
* zkratky
* jazyk specifický pro společnost
* jazyk, který pochází z jiného jazyka, ale často se používá ve vaší aplikaci
* Klíčová slova a fráze v příkladu projevy

Po zadání několika slov nebo frází můžete najít související hodnoty pomocí funkce **doporučit** . Před přidáním do hodnot seznamu frází Zkontrolujte související hodnoty.

|Typ seznamu|Účel|
|--|--|
|Zaměnitelné|Synonyma nebo slova, která při změně na jiné slovo v seznamu mají stejný záměr a extrakci entit.|
|Bez zaměnitelné|Slovní slovník pro aplikace, který je specifický pro vaši aplikaci, je větší než obecně další slova v daném jazyce.|

### <a name="interchangeable-lists"></a>Proměnitelné seznamy

Seznam  frází, který je proměnitelné, je určen pro hodnoty, které jsou synonymy. Například pokud chcete, aby byly nalezeny všechny útvary vody a jako příklad projevy například: 

* Jaká města jsou blízko skvělých laků? 
* Jakou cestu spouštíte podél jezera Havasu?
* Kde Nile začíná a končí? 

Každá utterance by měla být určena pro záměr i entity bez ohledu na tělo vody: 

* Jaká města se blíží k [bodyOfWater]?
* Jakou cestu spouštíte společně [bodyOfWater]?
* Kde [bodyOfWater] začíná a končí? 

Vzhledem k tomu, že slova nebo fráze pro tělo vody jsou synonyma a je možné je v projevy použít zaměnitelné, použijte  nastavení proměnitelné v seznamu frází. 

### <a name="non-interchangeable-lists"></a>Seznamy, které nelze měnit

Seznam frází, která není zaměnitelné, je signál, který zvyšuje detekci na LUIS. Seznam frází označuje slova nebo fráze, které jsou důležitější, že jsou další slova významná. To pomáhá při určování záměru a detekce entit. Řekněme například, že máte doménu předmětu, jako je například cesta, která je globální (to znamená napříč kulturami, ale stále v jednom jazyce). Existují slova a fráze, které jsou pro aplikaci důležité, ale nejsou synonyma. 

Pro jiný příklad použijte seznam frází, které nemůžete měnit, pro vzácná, proprietární a cizí slova. Služba LUIS možná nebudete moct rozpoznat vzácné a proprietární slova, jakož i cizí slova (mimo jazykovou verzi aplikace). Zaměnitelné nastavení znamená, že sadu výjimečných slova tvoří třídu, která by měla služba LUIS učení se rozpoznávání, ale nejsou synonyma nebo mezi sebou vzájemně zaměnitelné.

Nepřidávejte do seznamu frází všechna možná slova nebo fráze, přidejte několik slov nebo frází najednou a pak je přehlaste a publikujte. 

Jak se seznam frází postupně rozrůstá, může se stát, že některé výrazy mají mnoho forem (synonym). Rozdělte je do jiného seznamu frází, které jsou zaměnitelné. 

<a name="phrase-lists-help-identify-simple-exchangeable-entities"></a>

## <a name="phrase-lists-help-identify-simple-interchangeable-entities"></a>Fráze uvádí nápovědy identifikovat jednoduchých zaměnitelné entit
Zaměňovat frázi seznamy jsou dobrým způsobem, jak optimalizovat výkon vaší aplikace LUIS. Pokud má vaše aplikace potíže při predikci projevy na správné záměr nebo rozpoznávání entit, zamyslete se, jestli projevy obsahovat neobvyklé slova ani slova, která může být nejednoznačný ve smyslu. Tato slova jsou vhodnými kandidáty zahrnout do seznamu frázi.

## <a name="phrase-lists-help-identify-intents-by-better-understanding-context"></a>Fráze uvádí nápovědy identifikovat záměry lepší porozumění kontextu
Seznam frázi není instrukce k LUIS provést odpovídající strict nebo vždy označit všechny podmínky v seznamu frázi stejně. Je jednoduše nápovědu. Například můžete mít seznam frázi, která označuje, že "Patti" a "Selma" jsou názvy, ale LUIS můžete stále použít kontextové informace k rozpoznání něco jiného v znamenají "Provést rezervaci 2 ve vaší Patti Diner večeře" a "Nepracuju, řízení Pokyny k Selma, Gruzie". 

Přidání seznamu frázi je alternativa k přidání další příklad projevy záměru. 

## <a name="when-to-use-phrase-lists-versus-list-entities"></a>Kdy použít seznamy frázi a seznam entit
Zatímco frázi seznamu a seznam entit může mít vliv na projevy přes všechny záměrů, mají funkce to jiným způsobem. Pomocí seznamu frázi vliv na skóre záměru předpovědi. Použijte seznam entit ovlivnit extrakce entity shodu přesný text. 

### <a name="use-a-phrase-list"></a>Použijte seznam fráze
Se seznamem frázi LUIS stále vezměte v úvahu kontext a generalizace k identifikaci položky, které jsou podobné, ale není přesná shoda, jako položky v seznamu. Pokud potřebujete aplikaci LUIS tak, aby mohli generalize a identifikaci nových položek v kategorii, použijte seznam frázi. 

Pokud chcete mít přehled o nových instancích entity, jako je Plánovač schůzky, který by měl rozpoznávat názvy nových kontaktů, nebo aplikaci inventáře, která by měla rozpoznávat nové produkty, použijte jiný typ entity, která se naučila počítače, jako je například jednoduchá entita. Pak vytvořte frázi seznam slova a slovní spojení, která pomáhá LUIS, hledání podobně jako v entitě jiná slova. Tento seznam provede LUIS rozpoznat příklady entity tak, že přidáte další význam hodnotu z těchto slov. 

Fráze seznamy jsou jako slovník jazyka specifického pro doménu, která usnadní vylepšení kvality znalost záměry a entity. Běžné použití seznamu frázi je podstatná jména správných například názvy měst. Název města, může být několik slova, včetně pomlček nebo apostrofy.
 
### <a name="dont-use-a-phrase-list"></a>Nepoužívejte seznam frází 
Seznam entit explicitně definuje každá hodnota může trvat entity a identifikuje pouze hodnoty, které přesně odpovídají. Seznam entit může být vhodný pro aplikaci, ve kterém je známo, že všechny instance entity a neměnit často. Příklady jsou potravin položky v nabídce restaurace, která mění jen zřídka. Pokud potřebujete přesný text výskyty entity, nepoužívejte seznam frázi. 

## <a name="best-practices"></a>Osvědčené postupy
Přečtěte si [osvědčené postupy](luis-concept-best-practices.md).

## <a name="next-steps"></a>Další postup

Zobrazit [přidat funkce](luis-how-to-add-features.md) získat další informace o tom, jak přidat funkce do vaší aplikace LUIS.
