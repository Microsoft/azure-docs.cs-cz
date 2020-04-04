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
ms.date: 04/02/2020
ms.author: diberry
ms.openlocfilehash: 7560fdcbfc77ea2655e8af641794478ead4c11c7
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631444"
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

1. Seznam frází se může vztahovat na celou aplikaci s **globálním** nastavením nebo na konkrétní model (záměr nebo entitu). Pokud vytvoříte seznam frází, jako _popisovač_ ze záměru nebo entity, přepínač je nastavena na není globální. V tomto případě význam přepínače je, že popisovač je funkce pouze tento model, proto _není globální_ pro aplikaci.

1. Vyberte **Done** (Hotovo). Nový deskriptor je přidán na stránku **Deskriptory.**

<a name="edit-phrase-list"></a>
<a name="delete-phrase-list"></a>
<a name="deactivate-phrase-list"></a>

> [!Note]
> Deskriptor můžete odstranit nebo deaktivovat z kontextového panelu nástrojů na stránce **Deskriptory.**

## <a name="next-steps"></a>Další kroky

Po přidání, úpravě, odstranění nebo deaktivaci popisovače [znovu zaškolte a otestujte aplikaci,](luis-interactive-test.md) abyste zjistili, zda se výkon zlepší.
