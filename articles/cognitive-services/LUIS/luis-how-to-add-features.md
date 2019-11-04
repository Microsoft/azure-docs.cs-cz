---
title: Seznamy frází – LUIS
titleSuffix: Azure Cognitive Services
description: Pomocí Language Understanding (LUIS) můžete přidat funkce aplikace, které můžou zlepšit detekci nebo předpovědi záměrů a entit, které kategorie a vzory.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: diberry
ms.openlocfilehash: 0e3e4226eaaa0505eea96d8b3aca820f2327349e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73467612"
---
# <a name="use-phrase-lists-to-boost-signal-of-word-list"></a>Použití seznamů frází ke zvýšení signálu v seznamu slov

Do aplikace LUIS můžete přidat funkce, aby se zlepšila její přesnost. Funkce nápovědy LUIS poskytují nápovědu, že určitá slova a fráze jsou součástí slovníku domény aplikace. 

[Seznam frází](luis-concept-feature.md) obsahuje skupinu hodnot (slova nebo fráze), které patří do stejné třídy a musí se nacházet podobně (například názvy měst nebo produktů). K čemu se LUIS informace o jednom z nich automaticky aplikuje i na ostatní. Tento seznam není stejný jako [entita seznamu](reference-entity-list.md) (shoda přes text) odpovídajících slov.

Seznam frází se do slovníku domény aplikace přidá jako druhý signál, který LUIS o těchto slovech.

Seznamte se s [Koncepty funkcí](luis-concept-feature.md) a zjistěte, kdy a proč použít seznam frází. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

## <a name="add-phrase-list"></a>Přidat seznam frází

LUIS umožňuje až 10 seznamů frází na aplikaci. 

1. Otevřete aplikaci kliknutím na její název na stránce **Moje aplikace** a pak klikněte na **sestavit**a pak na na levém panelu vaší aplikace klikněte na **seznamy frází** . 

1. Na stránce **seznam frází** klikněte na **vytvořit nový seznam frází**. 
 
1. Do dialogového okna **Přidat seznam frází** zadejte `Cities` jako název seznamu frází. Do pole **hodnota** zadejte hodnoty seznamu frází. Najednou můžete zadat jednu hodnotu nebo sadu hodnot oddělených čárkami. potom stiskněte **ENTER**.

    ![Přidat města seznamu frází](./media/luis-add-features/add-phrase-list-cities.png)

1. LUIS může navrhovat související hodnoty pro přidání do seznamu frází. Kliknutím na tlačítko **doporučit** získáte skupinu navrhovaných hodnot, které jsou sémanticky spojeny s přidanými hodnotami. Můžete kliknout na libovolnou z navrhovaných hodnot nebo přidat všechny kliknutím na **Přidat vše** .

    ![Seznam frází navrhovaných hodnot – přidat vše](./media/luis-add-features/related-values.png)

1. Klikněte na **tyto hodnoty jsou zaměnitelné** , pokud jsou přidané fráze hodnoty alternativy, které je možné použít zaměnitelné.

    ![Seznam frází navrhovaných hodnot – výběr proměnitelné pole](./media/luis-add-features/interchangeable.png)

1. Klikněte na **Done** (Hotovo). Seznam frází města je přidán na stránku **seznamy frází** .

<a name="edit-phrase-list"></a>
<a name="delete-phrase-list"></a>
<a name="deactivate-phrase-list"></a>

> [!Note]
> Seznam frází můžete odstranit nebo deaktivovat z kontextového panelu nástrojů na stránce **seznam frází** .

## <a name="next-steps"></a>Další kroky

Po přidání, úpravě, odstranění nebo deaktivaci seznamu frází znovu spusťte [výuku a otestujte aplikaci](luis-interactive-test.md) , abyste viděli, jestli se výkon zlepšuje.
