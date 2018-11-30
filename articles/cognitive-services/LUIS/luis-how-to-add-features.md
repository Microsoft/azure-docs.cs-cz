---
title: Fráze v seznamech vylepšení detekce entity
titleSuffix: Azure Cognitive Services
description: Pomocí Language Understanding (LUIS) přidejte funkce aplikací, které můžete zlepšit zjišťování nebo předpověď záměry a entity, které kategorie a vzory
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 11/27/2018
ms.author: diberry
ms.openlocfilehash: feb51cd55801addaf5ce2486e5527542f794bbc5
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/29/2018
ms.locfileid: "52580946"
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

    ![Seznam frází navrhované hodnoty](./media/luis-add-features/related-values.png)

5. Klikněte na tlačítko **tyto hodnoty jsou zaměnitelné** Pokud jsou hodnoty seznamu přidal frázi alternativy, které můžete používat Zaměnitelně.

    ![Seznam frází navrhované hodnoty](./media/luis-add-features/interchangeable.png)

6. Klikněte na **Uložit**. Přidání seznamu frázi "Cities" **frázi seznamy** stránky.

<a name="edit-phrase-list"></a>
<a name="delete-phrase-list"></a>
<a name="deactivate-phrase-list"></a>

> [!Note]
> Můžete odstranit nebo deaktivovat frázi seznamu z panelu nástrojů kontextové na **frázi seznamy** stránky.

## <a name="next-steps"></a>Další postup

Po přidání, úprava, odstranění nebo deaktivace seznam frázi [trénování a testování aplikace](luis-interactive-test.md) znovu, abyste viděli, zda se výkon zlepšil.
