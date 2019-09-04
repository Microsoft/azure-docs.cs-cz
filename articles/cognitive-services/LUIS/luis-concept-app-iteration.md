---
title: Návrh iterační aplikace – LUIS
titleSuffix: Azure Cognitive Services
description: Služba LUIS učí nejlepší v iterativní cyklus změny modelu, příklady utterance, publikování a shromažďování dat z koncového bodu dotazů.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/03/2019
ms.author: diberry
ms.openlocfilehash: 4356d9e1cd3d6f1a924603f7405d612814d35859
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2019
ms.locfileid: "70256929"
---
# <a name="authoring-cycle-for-your-luis-app"></a>Cyklus vytváření aplikací pro LUIS
Služba LUIS učí nejlepší v iterativní cyklus změny modelu, příklady utterance, publikování a shromažďování dat z koncového bodu dotazů. 

![Cyklus vytváření](./media/luis-concept-app-iteration/iteration.png)

## <a name="building-a-luis-model"></a>Sestavení modelu LUIS
Účel modelu je zjistit, co uživatel žádá o (záměr nebo záměr) a které části otázky zadejte podrobnosti (entity), které pomáhají určit odpověď. 

Model musí být specifické pro doménu aplikace, aby bylo možné zjistit slova a slovní spojení, že jsou relevantní, jakož i typické slovo řazení. 

Model vyžaduje záměry a _by měl mít_ entity. 

## <a name="add-training-examples"></a>Přidat příklady školení
Služba LUIS potřebuje příkladu projevy v příkazů. V příkladech potřebovat dostatek varianta možnosti aplikace word a slovosled být schopní určit, které záměr utterance je určená pro. Každý příklad utterance musí mít všechna požadovaná data označená jako entity. 

Dáte pokyn LUIS ignorovat projevy, které nejsou relevantní pro doménu vaší aplikace tak, že přiřadíte utterance k **žádný** záměr. Jakákoli slova nebo fráze, které nepotřebujete získaných ze utterance nemusíte být označené. Neexistuje žádný popisek slov nebo slovních spojení ignorovat. 

## <a name="train-and-publish-the-app"></a>Trénování a publikování aplikace
Až 15 až 30 různých projevy v každém záměru, s povinnými entitami, které jsou označené, budete muset [vlak](luis-how-to-train.md) [publikovat](luis-how-to-publish-app.md). Od publikovat oznámení o úspěchu použijte odkaz zobrazíte vašich koncových bodů. Ujistěte se, že jste aplikaci vytvořili a publikovali tak, aby byla dostupná v [oblastech koncového bodu](luis-reference-regions.md) , které potřebujete. 

## <a name="https-prediction-endpoint-testing"></a>Testování koncového bodu předpovědi HTTPS
Aplikaci LUIS můžete testovat z koncového bodu předpovědi HTTPS. Testování z koncového bodu předpovědi umožňuje LUIS vybrat libovolný projevy s nízkou mírou spolehlivosti ke [kontrole](luis-how-to-review-endpoint-utterances.md).  

## <a name="recycle"></a>Koš

Jakmile budete hotovi s cyklem vytváření, můžete začít znovu. Začněte s [revizí koncového bodu projevy](luis-how-to-review-endpoint-utterances.md) Luis označeného s nízkou spolehlivostí. Zkontrolujte tyto projevy záměr a entity. Jakmile zkontrolujete projevy, zkontrolujte seznam by měl být prázdný.  

Zvažte [klonování](luis-concept-version.md#clone-a-version) aktuální verze do nové verze a pak zahajte změny v obsahu nové verze. 

## <a name="batch-testing"></a>Dávkové testování

[Dávkové testování](luis-concept-batch-test.md) je způsob, jak zjistit, kolik projevy je skóre Luis. Příklady by měl být novým LUIS a by měly být popsány správně s cílem a entity, které chcete, aby služba LUIS k vyhledání. Výsledky testů určit, jak dobře by na této sadě projevy provedl LUIS. 

## <a name="next-steps"></a>Další postup

Seznamte se s koncepty [spolupráci](luis-concept-keys.md).
