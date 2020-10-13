---
title: Vzorce – předpověď pro LUIS
titleSuffix: Azure Cognitive Services
description: Vzor vám umožní získat větší přesnost záměru bez dalších projevy.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/11/2019
ms.openlocfilehash: 44a7a89740be3d6d502c5d22c79b46582034d88a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91535950"
---
# <a name="patterns-improve-prediction-accuracy"></a>Vzorce zlepšují přesnost předpovědi
Vzory jsou navržené tak, aby se zlepšila přesnost, když je několik projevy velmi podobné.  Vzor vám umožní získat větší přesnost záměru bez dalších projevy.

## <a name="patterns-solve-low-intent-confidence"></a>Vzorce řeší důvěru v nízkém záměru.
Vezměte v úvahu aplikaci lidských zdrojů, která v souvislosti s zaměstnancem hlásí v organizačním grafu. V případě jména a vztahu zaměstnance LUIS vrátí zaměstnance, kterých se účastní. Berte v úvahu zaměstnance, to, s názvem manažera Alice a týmu podřízených položek s názvem: Michael, Carl a.

![Obrázek organizačního diagramu](./media/luis-concept-patterns/org-chart.png)

|Výroky|Předpokládané záměr|Skóre záměru|
|--|--|--|
|Kdo je k podřízenému?|GetOrgChart|.30|
|Kdo je podřízená?|GetOrgChart|.30|

Pokud má aplikace více než 10 až 20 projevy s různou délkou věty, jiné pořadí slov a dokonce i různá slova (synonyma "podřízeného", "Manage", "Report"), LUIS může vracet skóre s nízkou mírou spolehlivosti. Vytvořte vzor, který pomůže LUIS pochopit důležitost pořadí slov.

Vzory řeší následující situace:

* Skóre záměru je nízké.
* Správný záměr není nejvyšší skóre, ale příliš blízko k hornímu skóre.

## <a name="patterns-are-not-a-guarantee-of-intent"></a>Vzory nejsou zárukou záměru.
Vzory využívají kombinaci předpovědních technologií. Nastavení záměru pro šablonu utterance ve vzoru není zárukou předpovědi záměru, ale jedná se o silný signál.

<a name="patterns-do-not-improve-entity-detection"></a>

## <a name="patterns-do-not-improve-machine-learning-entity-detection"></a>Vzory nezlepšují detekci entit strojového učení

Vzor je primárně určen pro lepší předpověď záměrů a rolí. _Vzor. Každá_ entita se používá k extrakci entit volných formulářů. I když vzory používají entity, vzor nedokáže detekovat entitu strojového učení.

Neočekává se, že se lepší předpověď entity zobrazí, pokud sbalíte více projevy do jediného vzoru. Aby bylo možné jednoduché entity aktivovat, je nutné přidat projevy nebo použít entity seznamu jinak se váš vzor neaktivuje.

## <a name="patterns-use-entity-roles"></a>Vzory používají role entit
Pokud jsou dvě nebo více entit ve vzorci kontextově závislé, vzory používají [role](luis-concept-roles.md) entit k extrakci kontextových informací o entitách.

## <a name="prediction-scores-with-and-without-patterns"></a>Předpověď skóre s použitím vzorů a bez nich
LUIS by měl mít dostatečný příklad projevy, což by mohlo zvýšit důvěru v předpovědi bez vzorců. Vzorce zvyšují skóre spolehlivosti bez nutnosti poskytovat tolik projevy.

## <a name="pattern-matching"></a>Porovnávání vzorů
Vzor se shoduje na základě zjištění entit uvnitř vzoru a pak ověřování zbývajících slov a pořadí slov ve vzorci. V modelu jsou vyžadovány entity, aby bylo možné model porovnat. Vzor je použit na úrovni tokenu, nikoli na úrovni znaků.

## <a name="pattern-only-apps"></a>Pouze vzorové aplikace
Můžete vytvořit aplikaci s záměry, které nemají žádný vzorový projevy, pokud existuje vzor pro každý záměr. Pro aplikaci pouze se vzorkem by neměl vzor obsahovat entity strojového učení, protože to vyžaduje příklad projevy.

## <a name="patternany-entity"></a>Entita Pattern.any

[!INCLUDE [Pattern.any entity - concepts](./includes/pattern-any-entity.md)]

## <a name="best-practices"></a>Osvědčené postupy
Seznamte se s [osvědčenými postupy](luis-concept-best-practices.md).

## <a name="pattern-syntax"></a>Syntaxe vzoru

Přečtěte si syntaxi vzorů z [odkazu syntaxe vzoru](reference-pattern-syntax.md).

## <a name="next-steps"></a>Další kroky

Další informace o vzorcích:

* [Postup přidání vzorů](luis-how-to-model-intent-pattern.md)
* [Postup přidání vzoru. kterákoli entita](luis-how-to-add-entities.md#add-a-patternany-entity)
* [Syntaxe vzorů](reference-pattern-syntax.md)

> [!div class="nextstepaction"]
> [Naučte se implementovat vzory v tomto kurzu.](luis-tutorial-pattern.md)
