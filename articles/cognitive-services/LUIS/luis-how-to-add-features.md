---
title: Přidání funkcí aplikace LUIS | Dokumentace Microsoftu
description: Pomocí Language Understanding (LUIS) přidejte funkce aplikací, které můžete zlepšit zjišťování nebo předpověď záměry a entity, které kategorie a vzory
services: cognitive-services
author: v-geberr
manager: Kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/30/2018
ms.author: v-geberr
ms.openlocfilehash: 3a63aa13b7c3c98fb4c9c0177474e2c848aa7d1f
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/06/2018
ms.locfileid: "37869190"
---
# <a name="use-features-to-improve-your-luis-apps-performance"></a>Použití funkcí pro zvýšení výkonu aplikace LUIS  

Přidání funkcí do své aplikace LUIS a zvýšit jeho přesnost. Funkce pomáhají LUIS tím, že poskytuje pomocné parametry tohoto slova a frází jsou součástí kategorii. Pokud služba LUIS naučí, jak rozpoznat jeden člen kategorie, ho ostatní řešte podobně.

## <a name="add-phrase-list"></a>Přidat seznam frází

1. Otevřete aplikaci klepnutím na jeho název na **Moje aplikace** stránce a potom klikněte na **sestavení**, pak klikněte na tlačítko **frázi seznamy** v levém panelu vaší aplikace. 

    ![Navigační seznam fráze](./media/luis-add-features/phrase-list-nav.png)

2. Na **frázi seznamy** klikněte na **vytvořit nový seznam frázi**. 
 
    ![Vytvořit nový seznam fráze](./media/luis-add-features/create-new-phrase-list.png)
    
3. V **Přidat frázi seznamu** dialogového okna zadejte "Cities" jako název seznamu frázi. V **hodnotu** zadejte hodnoty seznamu frázi. Zadejte jednu hodnotu, nebo sadu hodnot oddělených čárkami a potom stiskněte klávesu **Enter**.

    ![Přidat frázi seznam měst](./media/luis-add-features/add-phrase-list-cities.png)

4. Služba LUIS můžete navrhnout související hodnoty pro přidání do seznamu frázi. Klikněte na tlačítko **doporučujeme** zobrazíte skupinu navrhované hodnoty, které se týkají sémanticky added value(s). Získáte po kliknutí na navrhované hodnoty, nebo klikněte na tlačítko **přidat všechny** je přidat všechny.

    ![Seznam frází navrhované hodnoty](./media/luis-add-features/related-values.png)

5. Klikněte na tlačítko **tyto hodnoty jsou zaměnitelné** Pokud jsou hodnoty seznamu přidal frázi alternativy, které můžete používat Zaměnitelně.

    ![Seznam frází navrhované hodnoty](./media/luis-add-features/interchangeable.png)

6. Klikněte na **Uložit**. Přidání seznamu frázi "Cities" **frázi seznamy** stránky.

    ![Přidat seznam frází](./media/luis-add-features/phrase-list-cities.png)

## <a name="edit-phrase-list"></a>Upravit seznam fráze

Klikněte na název seznamu frázi **frázi seznamy** stránky. V **úpravy seznamu frázi** dialogové okno, že se otevře, provedli požadované úpravy změny a pak klikněte na tlačítko **Uložit**.

 ![Přidat seznam frází](./media/luis-add-features/edit-phrase-list.png)

## <a name="delete-phrase-list"></a>Odstranění seznamu fráze 

Klikněte na tlačítko se třemi tečkami (***...*** ) tlačítko na konci řádku a vyberte **odstranit**.

 ![Odstranění seznamu přidán](./media/luis-add-features/delete-phrase-list.png)

## <a name="deactivate-phrase-list"></a>Deaktivovat seznam frází 

Klikněte na tlačítko se třemi tečkami (***...*** ) tlačítko na konci řádku a vyberte **deaktivovat**.

 ![Deaktivovat seznamu přidán](./media/luis-add-features/deactivate-phrase-list.png)

## <a name="pattern-regular-expression-feature"></a>Funkce vzorku (regulární výraz) 
**Tato funkce je zastaralá**. Nové funkce vzor nelze přidat do služby LUIS. Všechny existující funkce vzoru jsou podporovány května 2018. Přispívat standardní LUIS porovnávání regulárních výrazů s žádostí o přijetí změn do [úložiště Github rozpoznávání textu](https://github.com/Microsoft/Recognizers-Text). 

## <a name="next-steps"></a>Další postup

Po přidání, úprava, odstranění nebo deaktivace seznam frázi [trénování a testování aplikace](interactive-test.md) znovu, abyste viděli, zda se výkon zlepšil.
