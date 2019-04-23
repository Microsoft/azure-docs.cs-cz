---
title: Projděte si uživatel projevy
titleSuffix: Language Understanding - Azure Cognitive Services
description: Aktivní učení koncový bod dotazy zachytí a vybere uživatele koncový bod projevy, které je jistí, jaké. Můžete zkontrolovat tyto projevy vyberte záměr a označování entit, pro tyto projevy čtení reálného světa. Přijmout tyto změny do vaší příklad projevy a trénování a publikování. Služba LUIS pak identifikuje projevy přesněji.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 03/25/2019
ms.author: diberry
ms.openlocfilehash: 8fac360682ef11c438cdec333fac21d6f8cfc117
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59791045"
---
# <a name="how-to-review-endpoint-utterances-in-luis-portal-for-active-learning"></a>Kontrola projevy koncový bod portálu služby LUIS aktivní učení

[Aktivní učení](luis-concept-review-endpoint-utterances.md) koncový bod dotazy zachytí a vybere uživatele koncový bod projevy, které je jistí, jaké. Můžete zkontrolovat tyto projevy vyberte záměr a označování entit, pro tyto projevy čtení reálného světa. Přijmout tyto změny do vaší příklad projevy a trénování a publikování. Služba LUIS pak identifikuje projevy přesněji.


## <a name="enable-active-learning"></a>Povolit aktivní učení

Umožňuje aktivně učit přihlaste dotazy uživatelů. To lze provést tak, že nastavíte [dotazu koncový bod](luis-get-started-create-app.md#query-the-endpoint-with-a-different-utterance) s `log=true` řetězec dotazu parametr a hodnotu.

## <a name="disable-active-learning"></a>Zakázat aktivní učení

Zakázat active learning, nemusíte protokolu dotazy uživatelů. To lze provést tak, že nastavíte [dotazu koncový bod](luis-get-started-create-app.md#query-the-endpoint-with-a-different-utterance) s `log=false` řetězec dotazu parametr a hodnotu.

## <a name="filter-utterances"></a>Filtrovat projevy

1. Otevřete aplikaci (například TravelAgent) tak, že vyberete jeho název na **Moje aplikace** stránce a pak vyberte **sestavení** v horním panelu.

1. V části **zvýšit výkon aplikace**vyberte **zkontrolujte koncový bod projevy**.

1. Na **zkontrolujte koncový bod projevy** stránky, vyberte v **seznam filtrů záměr nebo entity** textového pole. Tento rozevírací seznam obsahuje všechny příkazy v rámci **záměry** a všechny entity v rámci **entity**.

    ![Projevy filtru](./media/label-suggested-utterances/filter.png)

1. V rozevíracím seznamu vyberte kategorii (záměry a entity) a zkontrolujte projevy.

    ![Záměr projevů](./media/label-suggested-utterances/intent-utterances.png)

## <a name="label-entities"></a>Popis entity
Služba LUIS nahradí entity tokeny (slova) názvy entit modře zvýrazněna. Pokud utterance má neoznačených entity, označte je utterance. 

1. Vyberte na slova v utterance. 

1. Vyberte entitu ze seznamu.

    ![Popis entity](./media/label-suggested-utterances/label-entity.png)

## <a name="align-single-utterance"></a>Zarovnat jednoho utterance

Každý utterance má navrhované záměr zobrazí v **zarovnané záměr** sloupce. 

1. Pokud budete souhlasit s návrhem, vyberte na zaškrtávací políčko.

    ![Zachovat zarovnané záměr](./media/label-suggested-utterances/align-intent-check.png)

1. Pokud jste Nesouhlasím s návrhem, vyberte správné záměr zarovnané záměru rozevíracího seznamu a potom vyberte na značku zaškrtnutí vpravo zarovnaný záměr. 

    ![Zarovnat záměr](./media/label-suggested-utterances/align-intent.png)

1. Po výběru na značku zaškrtnutí utterance se odebere ze seznamu. 

## <a name="align-several-utterances"></a>Zarovnat několik projevy

Chcete-li zarovnat několik projevy, zaškrtněte políčko nalevo od projevy a pak vyberte na **přidat vybrané** tlačítko. 

![Zarovnat několik](./media/label-suggested-utterances/add-selected.png)

## <a name="verify-aligned-intent"></a>Ověřte zarovnané záměr

Můžete ověřit, utterance byla souladu s správné záměr tak, že přejdete **záměry** stránky, vyberte název záměru a kontrola projevy. Utterance z **zkontrolujte koncový bod projevy** je v seznamu.

## <a name="delete-utterance"></a>Odstranit utterance

Každý utterance lze odstranit ze seznamu revize. Po odstranění, nebude se zobrazovat v seznamu znovu. To platí i v případě, že uživatel zadá stejný utterance z koncového bodu. 

Pokud jste si jisti, jestli byste měli odstranit utterance, buď ji přesunout do záměru, None nebo vytvořit nové záměr, jako je například "různé" a přesunout utterance tohoto záměru. 

## <a name="delete-several-utterances"></a>Odstranit několik projevy

Chcete-li odstranit několik projevy, vyberte všechny položky a vyberte na koše napravo od **přidat vybrané** tlačítko.

![Odstranit více](./media/label-suggested-utterances/delete-several.png)


## <a name="next-steps"></a>Další postup

Pokud chcete otestovat, jak vylepšuje výkon po popisek navrhované projevy, dostanete testovací konzole tak, že vyberete **testování** v horním panelu. Pokyny o tom, jak testovat svou aplikaci pomocí testovací konzole najdete v tématu [trénování a testování vaší aplikace](luis-interactive-test.md).
