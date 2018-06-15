---
title: Označení navrhované utterances s LEOŠ | Microsoft Docs
description: Použijte jazyk Principy (LEOŠ) ke popisku navrhované utterances, abyste nárůst active strojové učení.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/08/2017
ms.author: v-geberr
ms.openlocfilehash: 91a2fe738743d359677392bfb79aac885702b440
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35346012"
---
# <a name="review-endpoint-utterances"></a>Zkontrolujte utterances koncový bod

Funkci revoluční LEOŠ je [koncept](luis-concept-review-endpoint-utterances.md) active učení. Jakmile vaše LEOŠ dotazy koncový bod, LEOŠ používá active učení ke zlepšení kvality výsledků. V procesu active learning LEOŠ prozkoumá všechny utterances koncový bod a vybere utterances, které je jisti. Pokud můžete označit tyto utterances, školení a publikovat, pak LEOŠ identifikuje utterances přesněji. 

## <a name="filter-utterances"></a>Filtr utterances
1. Otevřete aplikaci (například TravelAgent) tak, že vyberete svůj název na **Moje aplikace** stránky a pak vyberte **sestavení** na horním panelu.

2. V části **zlepšit výkon aplikace**, vyberte **zkontrolujte koncový bod utterances**.

    ![Zkontrolujte utterances](./media/label-suggested-utterances/review.png)

3. Na **zkontrolujte koncový bod utterances** vyberte v **seznam filtrů záměr nebo entity** textové pole. Tento rozevírací seznam obsahuje všechny záměry pod **záměry** a všechny entity v části **entity**.

    ![Utterances filtru](./media/label-suggested-utterances/filter.png)

4. V rozevíracím seznamu vyberte kategorii (záměry nebo entity) a zkontrolujte utterances.

    ![Záměrné utterances](./media/label-suggested-utterances/intent-utterances.png)

## <a name="label-entities"></a>Popisek entity
LEOŠ nahradí názvy entit modře zvýrazněný entity tokeny (slova). Pokud utterance má bez popisku entity, označte je utterance. 

1. Vyberte na slova v utterance. 

2. Vyberte entitu ze seznamu.

    ![Popisek entity](./media/label-suggested-utterances/label-entity.png)

## <a name="align-single-utterance"></a>Zarovnat jeden utterance

Každý utterance má navrhované záměr zobrazí v **zarovnán záměr** sloupce. 

1. Pokud budete souhlasit s tohoto návrhu, vyberte na zaškrtnutí políčka.

    ![Zachovat zarovnaný záměru](./media/label-suggested-utterances/align-intent-check.png)

2. Pokud jste Nesouhlasím se návrhu, zarovnaný záměrné rozevíracím seznamu vyberte správné záměr a potom vyberte na zaškrtnutí vpravo zarovnaný záměr. 

    ![Zarovnat záměru](./media/label-suggested-utterances/align-intent.png)

3. Po výběru na zaškrtnutí, je utterance odebrat ze seznamu. 

## <a name="align-several-utterances"></a>Zarovnat několik utterances

Zarovnat několik utterances, zaškrtněte políčko nalevo od utterances a potom vyberte na **přidat vybrané** tlačítko. 

![Zarovnat několik](./media/label-suggested-utterances/add-selected.png)

## <a name="verify-aligned-intent"></a>Ověřte zarovnaný záměru
Můžete ověřit utterance byl souladu s správné záměr přechodem na **záměry** stránky, vyberte název záměrné a kontrola utterances. Utterance z **zkontrolujte koncový bod utterances** je v seznamu.

## <a name="delete-utterance"></a>Odstranit utterance
Každý utterance můžete odstranit ze seznamu revize. Po odstranění, nebude se zobrazovat v seznamu znovu. To platí i v případě, že uživatel zadá stejné utterance z koncového bodu. 

Pokud si nejste jistí, pokud utterance, měli byste odstranit, buď ho přesunout do záměrné, None nebo vytvořit nový záměr, jako je například "ostatní" a přesuňte utterance tohoto záměru. 

## <a name="delete-several-utterances"></a>Odstranit několik utterances
Pokud chcete odstranit několik utterances, vyberte všechny položky a vyberete na odpadkový koš napravo **přidat vybrané** tlačítko.

![Odstranit několik](./media/label-suggested-utterances/delete-several.png)

## <a name="next-steps"></a>Další postup

Pokud chcete testovat, jak zlepšuje výkon po označení navrhované utterances, dostanete konzole testovací výběrem **testování** v horním panelu. Pokyny k testování aplikace pomocí konzoly nástroje test najdete v tématu [Train a testování aplikace](Train-Test.md).
