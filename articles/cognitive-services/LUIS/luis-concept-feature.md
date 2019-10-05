---
title: Funkce – LUIS
titleSuffix: Azure Cognitive Services
description: Přidáním funkcí do jazykového modelu poskytněte nápovědu týkající se rozpoznávání vstupu, který chcete označit nebo klasifikovat.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: dab4b4c6f41a95623a40e5d3fd859f9613afac27
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2019
ms.locfileid: "71949599"
---
# <a name="phrase-list-features-in-your-luis-app"></a>Funkce seznamu frází ve vaší aplikaci LUIS

Ve službě Machine Learning je *funkce* odlišující se vlastností a atributů dat, která systém sleduje. 

Přidáním funkcí do jazykového modelu poskytněte nápovědu týkající se rozpoznávání vstupu, který chcete označit nebo klasifikovat. Funkce Help LUIS rozpoznávají jak záměry, tak i entity, ale funkce nejsou záměrem ani samotné entity. Místo toho můžou funkce poskytnout příklady souvisejících podmínek.  

## <a name="what-is-a-phrase-list-feature"></a>Co je funkce seznamu frází?
Seznam frází je seznam slov nebo frází, které jsou pro vaši aplikaci významné, více než ostatní slova v projevy. Seznam frází se přidá do slovníku domény aplikace jako další signál, který LUIS o těchto slovech. K čemu se LUIS informace o jednom z nich automaticky aplikuje i na ostatní. Tento seznam není uzavřenou [entitou seznamu](luis-concept-entity-types.md#types-of-entities) přesně vyhovujících textů.

Seznamy frází neumožňují odvozování, takže je třeba přidat příklady utterance, které používají různé výrazy pro všechna významná slova a fráze typu slovního.

## <a name="phrase-lists-help-all-models"></a>Seznamy frází, které pomůžou všechny modely

Seznamy frází nejsou propojené s konkrétním záměrem nebo entitou, ale přidávají se jako významné zvýšení na všechny záměry a entity. Jejím účelem je zlepšit detekci záměrů a klasifikaci entit.

## <a name="how-to-use-phrase-lists"></a>Používání seznamů frází

[Vytvořte seznam frází](luis-how-to-add-features.md) , pokud má vaše aplikace slova nebo fráze, které jsou pro aplikaci důležité, například:

* oborové výrazy
* slangem
* zkratky
* jazyk specifický pro společnost
* jazyk, který pochází z jiného jazyka, ale často se používá ve vaší aplikaci
* Klíčová slova a fráze v příkladu projevy

Po zadání několika slov nebo frází můžete najít související hodnoty pomocí funkce **doporučit** . Před přidáním do hodnot seznamu frází Zkontrolujte související hodnoty.

Seznam frází je pro hodnoty, které jsou synonymy. Například pokud chcete, aby byly nalezeny všechny útvary vody a jako příklad projevy například: 

* Jaká města jsou blízko skvělých laků? 
* Jakou cestu spouštíte podél jezera Havasu?
* Kde Nile začíná a končí? 

Každá utterance by měla být určena pro záměr i entity bez ohledu na tělo vody: 

* Jaká města se blíží k [bodyOfWater]?
* Jakou cestu spouštíte společně [bodyOfWater]?
* Kde [bodyOfWater] začíná a končí? 

Vzhledem k tomu, že slova nebo fráze pro tělo vody jsou synonyma a je možné je v projevy použít zaměnitelné. 

<a name="phrase-lists-help-identify-simple-exchangeable-entities"></a>

## <a name="phrase-lists-help-identify-simple-interchangeable-entities"></a>Seznamy frází, které vám pomůžou identifikovat jednoduchá zaměnitelné entity
Seznam frází, které lze zaměnit, je dobrým způsobem, jak vyladit výkon aplikace LUIS. Pokud má vaše aplikace problémy s předvídáním projevy správného záměru nebo rozpoznáním entit, zamyslete se nad tím, zda projevy obsahuje neobvyklá slova nebo slova, která mohou být nejednoznačná v tomto smyslu. Tato slova jsou vhodnými kandidáty, které je vhodné zahrnout do seznamu frází.

## <a name="phrase-lists-help-identify-intents-by-better-understanding-context"></a>Seznam frází, které vám pomůžou identifikovat záměry díky lepšímu porozumění kontextu
Seznam frází není pokyn, aby LUIS provádět striktní shodu nebo vždycky označovat všechny fráze v seznamu frází přesně stejně. Je to prostě Nápověda. Můžete mít například seznam frází, který indikuje, že "Patti" a "Selma" jsou názvy, ale LUIS může i nadále používat kontextové informace k tomu, aby dokázala rozpoznat, že se jedná o něco jiného než "udělat rezervaci 2 na Patti Diner pro večeři" a "najít řízení". pokyny k Selma, Gruzie ". 

Přidání seznamu frází je alternativou k přidání dalších příkladů projevy k záměru. 

## <a name="when-to-use-phrase-lists-versus-list-entities"></a>Kdy použít seznamy frází versus seznam entit
Přestože seznam frází i [entity seznamu](reference-entity-list.md) mohou ovlivnit projevy napříč všemi záměry, každý z nich má jiný způsob. Pomocí seznamu frází můžete ovlivnit skóre předpovědi záměrů. Použijte entitu seznam k ovlivnění extrakce entit pro přesnější shodu textu. 

### <a name="use-a-phrase-list"></a>Použití seznamu frází
V seznamu frází může LUIS stále brát v úvahu kontext a zobecnit k identifikaci položek, které jsou podobné, ale nikoli přesné shody jako položky v seznamu. Pokud potřebujete, aby vaše aplikace LUIS dokázala zobecnit a identifikovat nové položky v kategorii, použijte seznam frází. 

Pokud chcete mít přehled o nových instancích entity, jako je Plánovač schůzky, který by měl rozpoznávat názvy nových kontaktů, nebo aplikaci inventáře, která by měla rozpoznávat nové produkty, použijte jiný typ entity, která se naučila počítače, jako je například jednoduchá entita. Pak vytvořte seznam frází slov a frází, které pomáhají LUIS najít další slova podobná dané entitě. Tento seznam provede LUIS k rozpoznání příkladů entity přidáním dalšího významu k hodnotě těchto slov. 

Seznamy frází jsou podobně jako slovníky specifické pro doménu, které vám pomůžou se zvýšením kvality porozumění jejich záměrům a entitám. Běžné použití seznamu frází je správné podstatné jméno, například názvy měst. Název města může být několik slov, včetně spojovníků nebo apostrofů.
 
### <a name="dont-use-a-phrase-list"></a>Nepoužívat seznam frází 
Entita seznamu explicitně definuje každou hodnotu, kterou může entita převzít, a identifikuje jenom hodnoty, které přesně odpovídají. Entita seznamu může být vhodná pro aplikaci, ve které jsou všechny instance entity známé a často se nemění. Příklady položek jídla v nabídce restaurace, které se mění zřídka. Pokud potřebujete přesnou shodu textu entity, nepoužívejte seznam frází. 

## <a name="best-practices"></a>Osvědčené postupy
Seznamte se s [osvědčenými postupy](luis-concept-best-practices.md).

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak přidat funkce do aplikace LUIS, najdete v tématu věnovaném [Přidání funkcí](luis-how-to-add-features.md) .
