---
title: Použití funkcí ke zlepšení seznamu LUIS slov
titleSuffix: Azure Cognitive Services
description: Pomocí Language Understanding (LUIS) můžete přidat funkce aplikace, které můžou zlepšit detekci nebo předpovědi záměrů a entit, které kategorie a vzory.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/17/2020
ms.openlocfilehash: 33435d2367b92f312d41f0e32fdbb3a93d59a9fd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91540876"
---
# <a name="use-features-to-boost-signal-of-word-list"></a>Použití funkcí k posílení signálu v seznamu slov

Do aplikace LUIS můžete přidat funkce, aby se zlepšila její přesnost. Funkce nápovědy LUIS poskytují nápovědu, že určitá slova a fráze jsou součástí slovníku domény aplikace.

Seznamte se s [Koncepty](luis-concept-feature.md) a zjistěte, kdy a proč používat funkci.

## <a name="add-phrase-list-as-a-feature"></a>Přidat seznam frází jako funkci

1. Přihlaste se k [portálu Luis](https://www.luis.ai)a vyberte své **předplatné** a **prostředek pro vytváření obsahu** , abyste viděli aplikace přiřazené k tomuto zdrojovému prostředku.
1. Otevřete svou aplikaci tak, že na stránce **Moje aplikace** vyberete její název.
1. Vyberte **sestavit**a pak na levém panelu vaší aplikace vyberte **funkce** .

1. Na stránce **funkce** vyberte **+ vytvořit**.

1. V dialogovém okně **vytvořit nové fráze funkce seznamu** zadejte název, například `Cities` . Do pole **hodnota** zadejte příklady měst, například `Seattle` . Najednou můžete zadat jednu hodnotu nebo sadu hodnot oddělených čárkami. potom stiskněte **ENTER**.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky s přidáním funkce (seznamu frází) města](./media/luis-add-features/add-phrase-list-cities.png)

    Až zadáte dostatek hodnot pro LUIS, zobrazí se návrhy. Můžete **+ Přidat všechny** navrhované hodnoty nebo vybrat jednotlivé výrazy.

1. Zachovat **tyto hodnoty jsou** zkontrolovány, pokud je možné fráze použít zaměnitelné.

1. Seznam frází se může vztahovat na celou aplikaci s **globálním** nastavením nebo na konkrétní model (záměr nebo entitu). Pokud vytvoříte seznam frází jako _funkci_ z záměru nebo entity, přepínač není nastaven pro globální. V tomto případě je významem přepínače, že funkce je v místním prostředí pouze pro tento model, tedy nikoli z _globálních_ do aplikace.

1. Vyberte **Hotovo**. Nová funkce se přidá na stránku **funkcí ml** .

<a name="edit-phrase-list"></a>
<a name="delete-phrase-list"></a>
<a name="deactivate-phrase-list"></a>


> [!Note]
> Seznam frází můžete odstranit nebo deaktivovat z kontextového panelu nástrojů na stránce **funkce ml** .

## <a name="global-phrase-list-applies-to-entire-app"></a>Seznam globálních frází se vztahuje na celou aplikaci.

Seznam frází by měl být použit na záměr nebo entitu, které má být k dispozici, ale může nastat situace, kdy by měl být seznam frází použit pro celou aplikaci jako **globální** funkce.

Na stránce funkce ML vyberte seznam frází a potom v horním kontextovém panelu nástrojů vyberte **vytvořit globální** .

## <a name="model-as-a-feature"></a>Model jako funkce

Entita může být [funkcí záměru nebo entity](luis-concept-feature.md).

Chcete-li přidat entitu jako funkci k záměru, vyberte záměr ze stránky záměry a pak vyberte **+ Přidat funkci** nad kontextový panel nástrojů. Seznam bude obsahovat všechny seznamy frází a entity, které se dají použít jako funkce.

Chcete-li přidat entitu jako funkci do jiné entity, můžete ji přidat buď na stránce s podrobnostmi záměru pomocí [palety entity](label-entity-example-utterance.md#adding-entity-as-a-feature-from-the-entity-palette) , nebo můžete [Přidat funkci](luis-how-to-add-entities.md#add-a-feature-to-a-machine-learned-entity) na stránce s podrobnostmi entity.

## <a name="next-steps"></a>Další kroky

Po přidání, úpravě, odstranění nebo deaktivaci funkce, [školení a otestování aplikace](luis-interactive-test.md) znovu, abyste viděli, jestli se zvyšuje výkon.
