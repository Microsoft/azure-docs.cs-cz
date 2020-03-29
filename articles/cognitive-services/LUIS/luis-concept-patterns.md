---
title: Vzory pomáhají predikci - LUIS
titleSuffix: Azure Cognitive Services
description: Vzor umožňuje získat větší přesnost pro záměr bez poskytnutí mnoho dalšíprojevy.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: diberry
ms.openlocfilehash: 6c1b548de25369c162b4a08dfa20fce62c17f99f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "75890289"
---
# <a name="patterns-improve-prediction-accuracy"></a>Vzory zlepšují přesnost predikce
Vzorky jsou navrženy tak, aby zlepšit přesnost při několik projevy jsou velmi podobné.  Vzor umožňuje získat větší přesnost pro záměr bez poskytnutí mnoho dalšíprojevy. 

## <a name="patterns-solve-low-intent-confidence"></a>Vzory řeší nízkou spolehlivost záměru
Zvažte aplikaci lidských zdrojů, která se hlásí na organizačním diagramu ve vztahu k zaměstnanci. Vzhledem k názvu a vztahu zaměstnance vrátí služba LUIS zúčastněné zaměstnance. Vezměme si zaměstnance, Tom, s manažerem jméno Alice, a tým podřízených s názvem: Michael, Rebecca a Carl.

![Obrázek organizačního diagramu](./media/luis-concept-patterns/org-chart.png)

|Projevy|Záměr předpovídaný|Skóre záměru|
|--|--|--|
|Kdo je Tomův podřízený?|GetOrgChart|.30|
|Kdo je tomovým podřízeným?|GetOrgChart|.30|

Pokud aplikace má mezi 10 a 20 projevy s různými délkami věty, různé pořadí slov a dokonce i různá slova (synonyma "podřízené", "spravovat", "sestava"), LUIS může vrátit nízké skóre spolehlivosti. Vytvořte vzor, který službě LUIS pomůže pochopit důležitost pořadí slov. 

Vzory řeší následující situace: 

* Skóre záměru je nízké
* Správný záměr není nejvyšší skóre, ale příliš blízko k nejvyššímu skóre. 

## <a name="patterns-are-not-a-guarantee-of-intent"></a>Vzory nejsou zárukou záměru
Vzory používají kombinaci predikčních technologií. Nastavení záměru pro promluvu šablony ve vzoru není zárukou záměru předpověď, ale je silný signál. 

<a name="patterns-do-not-improve-entity-detection"/></a>

## <a name="patterns-do-not-improve-machine-learned-entity-detection"></a>Vzory nezlepšují detekci počítače

Vzor je primárně určen k pomoci předpověď záměry a role. Entita _pattern.any_ se používá k extrahování entit volného tvaru. Zatímco vzorky používají entity, vzorek nepomůže rozpoznat entitu znanou stroj.  

Neočekávejte, že zobrazíte lepší predikci entity, pokud sbalíte více projevy do jednoho vzoru. Pro jednoduché entity k požáru, je třeba přidat projevy nebo použít seznam entit jinak váš vzor nebude požární.

## <a name="patterns-use-entity-roles"></a>Vzory používají role entit
Pokud dvě nebo více entit ve vzoru jsou kontextově související, vzory použít [role](luis-concept-roles.md) entity extrahovat kontextové informace o entitách.  

## <a name="prediction-scores-with-and-without-patterns"></a>Predikční skóre se vzory a bez vzorů
Vzhledem k tomu, dostatek příklad projevy, LUIS by být schopen zvýšit spolehlivost předpověď bez vzory. Vzory zvýšit skóre spolehlivosti bez nutnosti poskytovat tolik projevy.  

## <a name="pattern-matching"></a>Porovnávání vzorů
Vzorek je porovnán na základě zjištění entit uvnitř vzoru jako první, pak ověření zbytek slova a pořadí slov vzoru. Entity jsou požadovány ve vzoru pro vzorek tak, aby odpovídaly. Vzorek se použije na úrovni tokenu, nikoli na úrovni znaků. 

## <a name="pattern-only-apps"></a>Aplikace pouze pro vzory
Můžete vytvořit aplikaci s záměry, které nemají žádné příklad projevy, tak dlouho, dokud je vzor pro každý záměr. Pro pouze vzor aplikace vzor by neměl obsahovat počítačem naučil entity, protože tyto vyžadují příklad projevy. 

## <a name="best-practices"></a>Osvědčené postupy
Seznamte se s [doporučenými postupy](luis-concept-best-practices.md).

## <a name="pattern-syntax"></a>Syntaxe vzoru

Naučte se syntaxi vzoru z [odkazu na syntaxi vzoru](reference-pattern-syntax.md). 

## <a name="next-steps"></a>Další kroky

Další informace o vzorcích:

* [Jak přidat vzory](luis-how-to-model-intent-pattern.md)
* [Jak přidat entitu pattern.any](luis-how-to-add-entities.md#add-a-patternany-entity)
* [Syntaxe vzorů](reference-pattern-syntax.md)

> [!div class="nextstepaction"]
> [Naučte se implementovat vzory v tomto kurzu](luis-tutorial-pattern.md)
