---
title: Vzorce – předpověď pro LUIS
titleSuffix: Azure Cognitive Services
description: Vzor umožňuje získat vyšší přesnost pro záměru bez zadání projevy mnoho více.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: diberry
ms.openlocfilehash: 32b9b42ee0962353405616e501de95b19e40ea1a
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280859"
---
# <a name="patterns-improve-prediction-accuracy"></a>Vzory zvyšte přesnost předpovědi
Vzory jsou navržené pro zlepšení přesnosti, když několik projevy jsou velmi podobné.  Vzor umožňuje získat vyšší přesnost pro záměru bez zadání projevy mnoho více. 

## <a name="patterns-solve-low-intent-confidence"></a>Způsoby řešení s nízkou spolehlivostí záměru
Vezměte v úvahu aplikaci lidských zdrojů, která generuje sestavy v organizační grafu ve vztahu k zaměstnance. Zadaný název a relace zaměstnance, LUIS vrátí zaměstnanci zahrnuté. Vezměte v úvahu zaměstnanec Petr, se správcem název Alice a tým s názvem podřízené uzly: Michael Milena a Carl.

![Obrázek organizačního diagramu](./media/luis-concept-patterns/org-chart.png)

|Projevy|Cílem předpovídat|Záměru skóre|
|--|--|--|
|Kdo je na Tom podřízený?|GetOrgChart|.30|
|Kdo je podřízenou položkou tohoto Tom?|GetOrgChart|.30|

Pokud aplikace má mezi 10 a 20 projevy pomocí různých délek věty, jiné pořadí slov a dokonce během různých slova (synonymům těchto "podřízený", "manage", "zpráva"), může vrátit LUIS nízká pravděpodobnost. Vytvořte vzor, který pomůže LUIS pochopit důležitost pořadí slov. 

Způsoby řešení těchto situacích: 

* Skóre záměru je nízké.
* Správný záměr není nejvyšší skóre, ale příliš blízko k hornímu skóre. 

## <a name="patterns-are-not-a-guarantee-of-intent"></a>Vzory nejsou zárukou záměr
Vzory pomocí kombinace technologií předpovědi. Nastavení pro šablony utterance záměru ve vzorku není zárukou záměru předpovědí, ale je silný signál. 

<a name="patterns-do-not-improve-entity-detection"/></a>

## <a name="patterns-do-not-improve-machine-learned-entity-detection"></a>Vzory nezlepšují detekci entit zjištěné počítačem

Vzor je primárně určen pro lepší předpověď záměrů a rolí. _Vzor. Každá_ entita se používá k extrakci entit volných formulářů. I když vzory používají entity, vzor nedokáže detekovat entitu získanou počítačem.  

Neočekává se, že se lepší předpověď entity zobrazí, pokud sbalíte více projevy do jediného vzoru. Aby bylo možné jednoduché entity aktivovat, je nutné přidat projevy nebo použít entity seznamu jinak se váš vzor neaktivuje.

## <a name="patterns-use-entity-roles"></a>Vzory použít entitu role
Pokud jsou dvě nebo více entit ve vzorci kontextově závislé, vzory používají [role](luis-concept-roles.md) entit k extrakci kontextových informací o entitách.  

## <a name="prediction-scores-with-and-without-patterns"></a>Predikce skóre a bez nich vzory
Zadaný dostatek příklad projevy, LUIS bylo by možné zvýšit důvěru předpovědi vzorů. Vzory zvýšení skóre spolehlivosti aniž by musel zadávat tolik projevy.  

## <a name="pattern-matching"></a>Porovnávání vzorů
Vzor je nalezena shoda podle nejprve zjišťování entit v modelu a ověření zbytek slova a pořadí slov, vzoru. Entity jsou nutné ve vzoru pro odpovídající vzor. Vzor se použije na úrovni tokenu není úroveň znak. 

## <a name="pattern-only-apps"></a>Pouze vzorové aplikace
Můžete vytvořit aplikaci s záměry, které nemají žádný vzorový projevy, pokud existuje vzor pro každý záměr. V případě aplikace jenom se vzorkem by neměl vzor obsahovat entity, které se naučily počítačem, protože to vyžaduje příklad projevy. 

## <a name="best-practices"></a>Osvědčené postupy
Seznamte se s [osvědčenými postupy](luis-concept-best-practices.md).

## <a name="pattern-syntax"></a>Vzor syntaxe

Přečtěte si syntaxi vzorů z [odkazu syntaxe vzoru](reference-pattern-syntax.md). 

## <a name="next-steps"></a>Další kroky

Další informace o vzorcích:

* [Postup přidání vzorů](luis-how-to-model-intent-pattern.md)
* [Postup přidání vzoru. kterákoli entita](luis-how-to-add-entities.md##add-a-patternany-entity)
* [Syntaxe vzorů](reference-pattern-syntax.md)

> [!div class="nextstepaction"]
> [Naučte se implementovat vzory v tomto kurzu.](luis-tutorial-pattern.md)
