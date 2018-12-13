---
title: Seznamy frází
titleSuffix: Language Understanding - Azure Cognitive Services
description: Pomocí Language Understanding (LUIS) přidejte funkce aplikací, které můžete zlepšit zjišťování nebo předpověď záměry a entity, které kategorie a vzory
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 11/27/2018
ms.author: diberry
ms.openlocfilehash: 39a6ead041b1783a3effcb9659c59ea788cd2bf6
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53139499"
---
# <a name="use-phrase-lists-to-boost-signal-of-word-list"></a>Použijte frázi v seznamech boost signál seznam slov

Přidání funkcí do své aplikace LUIS a zvýšit jeho přesnost. Funkce pomáhají LUIS tím, že poskytuje pomocné parametry tohoto slova a frází jsou součástí slovníku domény aplikaci. 

A [seznam frází](luis-concept-feature.md) obsahuje skupinu hodnot (slova nebo fráze), která patří do stejné třídy a musí být zacházeno podobně (například názvy města nebo produkty). Služba LUIS dozvídá o jeden z nich se automaticky využije na ostatní také. Tento seznam není uzavřený seznam entit (přesný text odpovídá) z odpovídajících slov.

Seznam frází přidá do slovníku domény aplikace jako druhý signál k LUIS o těchto slov.

## <a name="add-phrase-list"></a>Přidat seznam frází

1. Otevřete aplikaci klepnutím na jeho název na **Moje aplikace** stránce a potom klikněte na **sestavení**, pak klikněte na tlačítko **frázi seznamy** v levém panelu vaší aplikace. 

2. Na **frázi seznamy** klikněte na **vytvořit nový seznam frázi**. 
 
3. V **Přidat frázi seznamu** dialogového okna zadejte "Cities" jako název seznamu frázi. V **hodnotu** zadejte hodnoty seznamu frázi. Zadejte jednu hodnotu, nebo sadu hodnot oddělených čárkami a potom stiskněte klávesu **Enter**.

    ![Přidat frázi seznam měst](./media/luis-add-features/add-phrase-list-cities.png)

4. Služba LUIS můžete navrhnout související hodnoty pro přidání do seznamu frázi. Klikněte na tlačítko **doporučujeme** zobrazíte skupinu navrhované hodnoty, které se týkají sémanticky added value(s). Získáte po kliknutí na navrhované hodnoty, nebo klikněte na tlačítko **přidat všechny** je přidat všechny.

    ![Navrhované hodnoty seznamu frázi – přidání všech](./media/luis-add-features/related-values.png)

5. Klikněte na tlačítko **tyto hodnoty jsou zaměnitelné** Pokud jsou hodnoty seznamu přidal frázi alternativy, které můžete používat Zaměnitelně.

    ![Navrhované hodnoty seznamu frázi – políčko zaměnitelné ](./media/luis-add-features/interchangeable.png)

6. Klikněte na **Uložit**. Přidání seznamu frázi "Cities" **frázi seznamy** stránky.

<a name="edit-phrase-list"></a>
<a name="delete-phrase-list"></a>
<a name="deactivate-phrase-list"></a>

> [!Note]
> Můžete odstranit nebo deaktivovat frázi seznamu z panelu nástrojů kontextové na **frázi seznamy** stránky.

## <a name="next-steps"></a>Další postup

Po přidání, úprava, odstranění nebo deaktivace seznam frázi [trénování a testování aplikace](luis-interactive-test.md) znovu, abyste viděli, zda se výkon zlepšil.
