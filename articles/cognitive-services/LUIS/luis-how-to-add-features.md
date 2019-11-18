---
title: Popisovače – LUIS
titleSuffix: Azure Cognitive Services
description: Pomocí Language Understanding (LUIS) přidejte funkce aplikací, které můžete zlepšit zjišťování nebo předpověď záměry a entity, které kategorie a vzory
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: diberry
ms.openlocfilehash: 2b5046bb61dcafbba0b0540935e08777fbd747a5
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123153"
---
# <a name="use-descriptors-to-boost-signal-of-word-list"></a>Použití popisovačů k posílení signálu v seznamu slov

Přidání funkcí do své aplikace LUIS a zvýšit jeho přesnost. Funkce pomáhají LUIS tím, že poskytuje pomocné parametry tohoto slova a frází jsou součástí slovníku domény aplikaci. 

[Popisovač](luis-concept-feature.md) (seznam frází) obsahuje skupinu hodnot (slova nebo fráze), které patří do stejné třídy a musí se nacházet podobně (například názvy měst nebo produktů). Služba LUIS dozvídá o jeden z nich se automaticky využije na ostatní také. Tento seznam není stejný jako [entita seznamu](reference-entity-list.md) (shoda přes text) odpovídajících slov.

Popisovač přidá do slovníku domény aplikace jako druhý signál k LUIS o těchto slovech.

Seznamte se s [Koncepty funkcí](luis-concept-feature.md) a zjistěte, kdy a proč použít popisovač. 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="add-descriptor"></a>Přidat popisovač

1. Otevřete aplikaci kliknutím na její název na stránce **Moje aplikace** a pak klikněte na **sestavit**a pak na **popisovače** v levém panelu vaší aplikace. 

1. Na stránce **popisovače** klikněte na **+ Přidat popisovač**. 
 
1. V dialogovém okně **vytvořit nový popisovač seznamu frází** zadejte název pro popisovač, například `Cities`. Do pole **hodnota** zadejte hodnoty popisovačů, například `Seattle`. Zadejte jednu hodnotu, nebo sadu hodnot oddělených čárkami a potom stiskněte klávesu **Enter**.

    > [!div class="mx-imgBorder"]
    > ![přidat města deskriptoru](./media/luis-add-features/add-phrase-list-cities.png)

    Až zadáte dostatek hodnot pro LUIS, zobrazí se návrhy. Můžete **+ Přidat všechny** navrhované hodnoty nebo vybrat jednotlivé výrazy.

1. Zachovat **tyto hodnoty jsou zaměnitelné** , pokud jsou přidané hodnoty popisovače alternativami, které lze použít zaměnitelné.

1. Vyberte **Done** (Hotovo). Nový popisovač se přidá na stránku **popisovače** .

<a name="edit-phrase-list"></a>
<a name="delete-phrase-list"></a>
<a name="deactivate-phrase-list"></a>

> [!Note]
> Popisovač můžete odstranit nebo deaktivovat z kontextového panelu nástrojů na stránce **popisovače** .

## <a name="next-steps"></a>Další kroky

Po přidání, úpravě, odstranění nebo deaktivaci deskriptoru, [školení a otestování aplikace](luis-interactive-test.md) znovu, abyste viděli, jestli se zvyšuje výkon.
