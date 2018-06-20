---
title: Pochopení iterativní aplikace LEOŠ návrh - Azure | Microsoft Docs
description: LEOŠ aplikace vyžadují iterace návrhu ke cvičení LEOŠ nejlepší extrahování dat získat.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 02/12/2018
ms.author: v-geberr
ms.openlocfilehash: b7f8dd46dc8289322726934f330761b0f1ab94bd
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265933"
---
# <a name="authoring-cycle"></a>Vytváření cyklu
LEOŠ zjišťuje nejvhodnější v iterační cyklus změn modelu, příklady utterance, publikování a shromažďování dat z koncového bodu dotazů. 

![Vytváření cyklu](./media/luis-concept-app-iteration/iteration.png)

## <a name="building-a-luis-model"></a>Vytváření LEOŠ modelu
Účelem modelu je zjistit, co uživatel se žádostí o (mimo jiné nebo záměr) a které části otázka poskytují podrobnosti (entit), které pomohou určit odpověď. 

Model musí být specifické pro doménu aplikace, aby bylo možné zjistit slova a slovní spojení, že jsou relevantní, jakož i typické word řazení. 

Tento model zahrnuje záměr entity. 

## <a name="add-training-examples"></a>Přidat příklady školení
LEOŠ musí utterances příklad v záměry. Příklady potřebovat dostatek variace word výběr a pořadí slov být schopní určit, které záměr utterance jsou určené výhradně pro. Každý příklad utterance musí mít všechna požadovaná data označený jako entity. 

Dáte pokyn LEOŠ ignorovat utterances, které nejsou relevantní pro vaše aplikace domény přiřazením utterance k **žádné** záměr. Jakákoli slova nebo fráze, které nepotřebujete vyžádat mimo utterance nemusíte být označené. Neexistuje žádný štítek pro hesla či fráze ignorovat. 
<!--
## Not just yet
Do not add features such as a [phrase list](luis-concept-feature.md) feature in your first cycle. Phrase lists are phrases that would be specific to your app's subject area.  
-->
## <a name="train-and-publish-the-app"></a>Trénování a publikování aplikace
Až budete mít různé utterances 10 až 15 v každém záměr s požadované entity s názvem bez přípony, cvičení LEOŠ poté publikujte získat koncové body. Ujistěte se, k vytvoření aplikace a publikování aplikace, aby bylo k dispozici v [koncový bod oblasti](luis-reference-regions.md) potřebujete. 

## <a name="https-endpoint-testing"></a>Testování koncový bod HTTPS
Můžete testovat aplikaci LEOŠ z uvedených na koncový bod HTTPS **[publikovat](publishapp.md)** stránky. Testování z koncového bodu umožňuje LEOŠ zvolit všechny utterances s jistotou nízká ke kontrole.  

## <a name="recycle"></a>Recyklace
Až skončíte s cyklus vytváření, můžete začít znovu. Začněte prostudováním utterances koncový bod, LEOŠ označená s jistotou nízká. Zkontrolujte tyto utterances záměr a entity. Po prostudování utterances, zkontrolujte seznamu by měla být prázdná.  

## <a name="batch-testing"></a>Testování batch
Testování batch je způsob, jak zobrazit, kolik utterances příkladu jsou skóre pro magnitudu podle LEOŠ. Příklady by měl být nový LEOŠ a by měl být správně označen s záměr a subjekty, které chcete LEOŠ najít. Výsledky testů označuje, jak dobře by LEOŠ provádět na této sadě utterances. 

## <a name="next-steps"></a>Další postup

Seznamte se s koncepty [spolupráce](luis-concept-collaborator.md).

[luis-reference-prebuilt-domains]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-prebuilt-domains