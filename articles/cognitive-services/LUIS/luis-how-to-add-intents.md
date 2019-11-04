---
title: Přidat záměry – LUIS
titleSuffix: Azure Cognitive Services
description: Přidejte do aplikace LUIS záměry k identifikaci skupin otázek nebo příkazů, které mají stejné záměry.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.service: cognitive-services
ms.openlocfilehash: 1f2f001489552203f0157dd24356341eb3184c81
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73467542"
---
# <a name="add-intents-to-determine-user-intention-of-utterances"></a>Přidání záměrů k určení záměru uživatele projevy

Přidejte do aplikace LUIS [záměry](luis-concept-intent.md) k identifikaci skupin dotazů nebo příkazů, které mají stejný záměr. 

Záměry jsou spravovány v části **sestavení** v horním navigačním panelu a pak z **záměrů**v levém panelu. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

## <a name="add-intent"></a>Přidat záměr

1. Na stránce **Intents** (Záměry) vyberte **Create new intent** (Vytvořit nový záměr).

1. V dialogovém okně **vytvořit nový záměr** zadejte název záměru, `GetEmployeeInformation`a klikněte na **Hotovo**.

    ![Přidat záměr](./media/luis-how-to-add-intents/Addintent-dialogbox.png)

## <a name="add-an-example-utterance"></a>Přidat příklad utterance

Příkladem projevy jsou textové příklady dotazů a příkazů uživatele. Chcete-li naučit Language Understanding (LUIS), musíte do záměru přidat příklad projevy.

1. Na stránce Podrobnosti záměru **GetEmployeeInformation** zadejte relevantní utterance od uživatelů, například `Does John Smith work in Seattle?` v textovém poli pod názvem záměru a potom stiskněte klávesu ENTER.
 
    ![Stránka s podrobnostmi o záměrech se zvýrazněným utterance](./media/luis-how-to-add-intents/add-new-utterance-to-intent.png) 

    LUIS převede všechny projevy na malá písmena a přidá mezery kolem tokenů, jako jsou spojovníky.

<a name="#intent-prediction-discrepancy-errors"></a>

## <a name="intent-prediction-errors"></a>Chyby předpovědi záměru 

Příklad utterance záměru může mít chybu předpovědi záměru mezi záměrem, který je v současnosti v rámci, a záměrem, který je určen během školení. 

Chcete-li najít chyby předpovědi utterance a opravit je, použijte možnosti **vyhodnocení** nesprávné a nejasné **v kombinaci** s možností **zobrazení** **podrobného zobrazení**. 

![Chcete-li najít chyby předpovědi utterance a opravit je, použijte možnost filtr.](./media/luis-how-to-add-intents/find-intent-prediction-errors.png)

Pokud jsou filtry a zobrazení aplikovány a existují příklady projevy s chybami, zobrazí se v seznamu ukázkový utterance projevy a problémy.

![! [Při použití filtrů a zobrazení se zobrazí příklad projevy s chybami. v seznamu příkladů utterance se zobrazí projevy a problémy.] (./media/luis-how-to-add-intents/find-errors-in-utterances.png)](./media/luis-how-to-add-intents/find-errors-in-utterances.png#lightbox)

V každém řádku se zobrazuje skóre předpovědi aktuálního školení pro příklad utterance, nejbližšího skóre rival, což je rozdíl v těchto dvou skórech. 

### <a name="fixing-intents"></a>Opravení záměrů

Pokud se chcete dozvědět, jak opravit chyby předpovědi, použijte [řídicí panel souhrnu](luis-how-to-use-dashboard.md). Souhrnný řídicí panel poskytuje analýzu pro poslední školení aktivní verze a nabízí hlavní návrhy na opravu modelu.  

## <a name="add-a-custom-entity"></a>Přidat vlastní entitu

Po přidání utterance k záměru můžete vybrat text v rámci utterance a vytvořit vlastní entitu. Vlastní entita je způsob, jak označit text k extrakci spolu se správným záměrem. 

Další informace najdete v tématu [Přidání entity do utterance](luis-how-to-add-example-utterances.md) .

## <a name="entity-prediction-discrepancy-errors"></a>Chyby nerozdílů předpovědi entit 

Entita je podtržena červeně, aby označovala [nesoulad předpovědi entity](luis-how-to-add-example-utterances.md#entity-status-predictions). Vzhledem k tomu, že se jedná o první výskyt entity, není k dispozici dostatek příkladů pro LUIS, že je tento text označený správnou entitou. Tato neshoda se odstraní, když se aplikace vyškole. 

![Stránka s podrobnostmi o záměrech, název vlastní entity byl zvýrazněn modře](./media/luis-how-to-add-intents/create-custom-entity-name-blue-highlight.png) 

Text je zvýrazněn modře, který značí entitu.  

## <a name="add-a-prebuilt-entity"></a>Přidat předem vytvořenou entitu

Informace najdete v tématu [předem vytvořená entita](luis-how-to-add-entities.md#add-a-prebuilt-entity-to-your-app).

## <a name="using-the-contextual-toolbar"></a>Použití kontextového panelu nástrojů

Když v seznamu vyberete jeden nebo více příkladů projevy, můžete zaškrtnutím políčka vlevo od utterance zobrazit panel nástrojů nad seznamem utterance a provést následující akce:

* Opětovné přiřazení záměru: přesunout utterancey na jiný záměr
* Odstranit utterance (y)
* Filtry entit: zobrazit jenom projevy obsahující filtrované entity
* Zobrazit pouze všechny chyby: Zobrazit projevy s chybami předpovědi nebo zobrazit všechny projevy
* Entity/zobrazení tokenů: zobrazení zobrazení entit s názvy entit nebo zobrazení nezpracovaného textu utterance
* Zvětšení skla: hledání projevy obsahujícího konkrétní text

## <a name="working-with-an-individual-utterance"></a>Práce s jednotlivými utterance

Následující akce lze provádět u jednotlivých utterance z nabídky se třemi tečkami vpravo od utterance:

* Upravit: Změna textu utterance
* Odstranit: odebrat utterance z záměru. Pokud stále chcete utterance, lepší způsob je přesunout do záměru **none** . 
* Přidání vzoru: vzor umožňuje vzít v úvahu běžný utterance a označit nahraditelný text a ignorovat text, což snižuje nutnost dalších projevy v úmyslu. 

Sloupec s **popisem záměru** umožňuje změnit záměr utterance.

## <a name="train-your-app-after-changing-model-with-intents"></a>Výuka aplikace po změně modelu s záměry

Po přidání, úpravě nebo odebrání záměrů, [školení](luis-how-to-train.md) a [publikování](luis-how-to-publish-app.md) aplikace, aby byly změny aplikovány na dotazy koncového bodu. 

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o přidání [ukázkového projevyu](luis-how-to-add-example-utterances.md) s entitami. 
