---
title: Deskriptory – LUIS
titleSuffix: Azure Cognitive Services
description: Přidání funkcí aplikace pomocí funkce jazyka (Language Understanding) může zlepšit zjišťování nebo predikci záměrů a entit, které kategorie a vzory
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "74123153"
---
# <a name="use-descriptors-to-boost-signal-of-word-list"></a>Použití popisovačů k posílení signálu seznamu slov

Do aplikace LUIS můžete přidat funkce, které zlepší její přesnost. Funkce pomáhají LUIS tím, že poskytuje rady, že určitá slova a fráze jsou součástí slovníku domény aplikace. 

[Deskriptor](luis-concept-feature.md) (seznam frází) obsahuje skupinu hodnot (slova nebo fráze), které patří do stejné třídy a musí se s nimi zacházet podobně (například názvy měst nebo produktů). Co luis dozví o jednom z nich se automaticky použije na ostatní také. Tento seznam není totéž jako [entita seznamu](reference-entity-list.md) (přesná shoda textu) odpovídajících slov.

Deskriptor přidá do slovníku domény aplikace jako druhý signál LUIS o těchto slovech.

Zkontrolujte [koncepty funkcí,](luis-concept-feature.md) abyste pochopili, kdy a proč použít popisovač. 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="add-descriptor"></a>Přidání popisovače

1. Otevřete aplikaci tak, že kliknete na její název na stránce **Moje aplikace** a potom klikněte na **Vytvořit**a potom klikněte na **Deskriptory** v levém panelu aplikace. 

1. Na stránce **Deskriptory** klikněte na **+ Přidat popisovač**. 
 
1. V dialogovém okně **Vytvořit nový seznam frází** zadejte název, například `Cities` pro popisovač. Do pole **Hodnota** zadejte hodnoty popisovačů, například `Seattle`. Můžete zadat jednu hodnotu najednou nebo sadu hodnot oddělených čárkami a pak stiskněte **Enter**.

    > [!div class="mx-imgBorder"]
    > ![Přidat města deskriptoru](./media/luis-add-features/add-phrase-list-cities.png)

    Po zadání dostatečného množství hodnot pro službu LUIS se zobrazí návrhy. Můžete **+ Přidat všechny** navrhované hodnoty nebo vybrat jednotlivé termíny.

1. Zachovat **Tyto hodnoty jsou zaměnitelné** zkontrolovat, pokud přidané hodnoty popisovače jsou alternativy, které lze použít zaměnitelně.

1. Vyberte **Done** (Hotovo). Nový deskriptor je přidán na stránku **Deskriptory.**

<a name="edit-phrase-list"></a>
<a name="delete-phrase-list"></a>
<a name="deactivate-phrase-list"></a>

> [!Note]
> Deskriptor můžete odstranit nebo deaktivovat z kontextového panelu nástrojů na stránce **Deskriptory.**

## <a name="next-steps"></a>Další kroky

Po přidání, úpravě, odstranění nebo deaktivaci popisovače [znovu zaškolte a otestujte aplikaci,](luis-interactive-test.md) abyste zjistili, zda se výkon zlepší.
