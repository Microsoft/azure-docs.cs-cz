---
title: Popisovače – LUIS
titleSuffix: Azure Cognitive Services
description: Pomocí Language Understanding (LUIS) můžete přidat funkce aplikace, které můžou zlepšit detekci nebo předpovědi záměrů a entit, které kategorie a vzory.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/02/2020
ms.author: diberry
ms.openlocfilehash: 7560fdcbfc77ea2655e8af641794478ead4c11c7
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "80631444"
---
# <a name="use-descriptors-to-boost-signal-of-word-list"></a>Použití popisovačů k posílení signálu v seznamu slov

Do aplikace LUIS můžete přidat funkce, aby se zlepšila její přesnost. Funkce nápovědy LUIS poskytují nápovědu, že určitá slova a fráze jsou součástí slovníku domény aplikace.

[Popisovač](luis-concept-feature.md) (seznam frází) obsahuje skupinu hodnot (slova nebo fráze), které patří do stejné třídy a musí se nacházet podobně (například názvy měst nebo produktů). K čemu se LUIS informace o jednom z nich automaticky aplikuje i na ostatní. Tento seznam není stejný jako [entita seznamu](reference-entity-list.md) (shoda přes text) odpovídajících slov.

Popisovač přidá do slovníku domény aplikace jako druhý signál k LUIS o těchto slovech.

Seznamte se s [Koncepty funkcí](luis-concept-feature.md) a zjistěte, kdy a proč použít popisovač.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="add-descriptor"></a>Přidat popisovač

1. Otevřete aplikaci kliknutím na její název na stránce **Moje aplikace** a pak klikněte na **sestavit**a pak na **popisovače** v levém panelu vaší aplikace.

1. Na stránce **popisovače** klikněte na **+ Přidat popisovač**.

1. V dialogovém okně **vytvořit nový popisovač seznamu frází** zadejte název, například `Cities` pro popisovač. Do pole **hodnota** zadejte hodnoty popisovačů, například `Seattle`. Najednou můžete zadat jednu hodnotu nebo sadu hodnot oddělených čárkami. potom stiskněte **ENTER**.

    > [!div class="mx-imgBorder"]
    > ![Přidat města deskriptoru](./media/luis-add-features/add-phrase-list-cities.png)

    Až zadáte dostatek hodnot pro LUIS, zobrazí se návrhy. Můžete **+ Přidat všechny** navrhované hodnoty nebo vybrat jednotlivé výrazy.

1. Zachovat **tyto hodnoty jsou zaměnitelné** , pokud jsou přidané hodnoty popisovače alternativami, které lze použít zaměnitelné.

1. Seznam frází se může vztahovat na celou aplikaci s **globálním** nastavením nebo na konkrétní model (záměr nebo entitu). Pokud vytvoříte seznam frází jako _deskriptor_ z záměru nebo entity, přepínač je nastaven na hodnotu ne na globální. V tomto případě je významem přepínače, že popisovač je funkce pouze pro tento model, tedy _ne globální_ do aplikace.

1. Vyberte **Done** (Hotovo). Nový popisovač se přidá na stránku **popisovače** .

<a name="edit-phrase-list"></a>
<a name="delete-phrase-list"></a>
<a name="deactivate-phrase-list"></a>

> [!Note]
> Popisovač můžete odstranit nebo deaktivovat z kontextového panelu nástrojů na stránce **popisovače** .

## <a name="next-steps"></a>Další kroky

Po přidání, úpravě, odstranění nebo deaktivaci deskriptoru, [školení a otestování aplikace](luis-interactive-test.md) znovu, abyste viděli, jestli se zvyšuje výkon.
