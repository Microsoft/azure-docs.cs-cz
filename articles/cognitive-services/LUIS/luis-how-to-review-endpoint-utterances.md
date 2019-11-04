---
title: Kontrola uživatele projevy-LUIS
titleSuffix: Azure Cognitive Services
description: Active Learning zachycuje dotazy koncových bodů a vybere koncové body uživatele projevy, že si nejste jistí. Projděte si tyto projevy a vyberte záměr a označte entity pro tyto projevyy na úrovni Read-World. Přijměte tyto změny do svého ukázkového projevyu a potom proveďte výuku a publikování. LUIS pak identifikuje projevy přesněji.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: d5652857f2f35e392d3f512001044fd06bc0a0c9
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499075"
---
# <a name="how-to-review-endpoint-utterances-in-luis-portal-for-active-learning"></a>Postup kontroly služby Endpoint projevy na portálu LUIS pro aktivní učení

[Active Learning](luis-concept-review-endpoint-utterances.md) zachycuje dotazy koncových bodů a vybere koncové body uživatele projevy, že si nejste jistí. Projděte si tyto projevy a vyberte záměr a označte entity pro tyto projevyy na úrovni Read-World. Přijměte tyto změny do svého ukázkového projevyu a potom proveďte výuku a publikování. LUIS pak identifikuje projevy přesněji.

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

## <a name="enable-active-learning"></a>Povolit aktivní učení

Pokud chcete povolit aktivní učení, Zaprotokolujte uživatelské dotazy. Toho je možné dosáhnout nastavením [dotazu koncového bodu](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint) s parametrem `log=true` QueryString a hodnotou.

## <a name="disable-active-learning"></a>Zakázat aktivní učení

Pokud chcete zakázat aktivní učení, Neprotokolujte uživatelské dotazy. Toho je možné dosáhnout nastavením [dotazu koncového bodu](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint) s parametrem `log=false` QueryString a hodnotou.

## <a name="filter-utterances"></a>Filtrovat projevy

1. Otevřete aplikaci (například TravelAgent) tak, že na stránce **Moje aplikace** vyberete její název a pak v horním panelu vyberete **sestavit** .

1. V části **zlepšit výkon aplikace**vyberte **zkontrolovat koncový bod projevy**.

1. Na stránce **zkontrolovat koncový bod projevy** vyberte v textovém poli **filtrovat seznam podle záměru nebo entity** . Tento rozevírací seznam obsahuje všechny záměry v rámci **záměrů** a všech entit v **entitách**.

    ![Filtr projevy](./media/label-suggested-utterances/filter.png)

1. V rozevíracím seznamu vyberte kategorii (záměry nebo entity) a Prohlédněte si projevy.

    ![Projevy záměru](./media/label-suggested-utterances/intent-utterances.png)

## <a name="label-entities"></a>Označení entit
LUIS nahrazuje tokeny entit (slova) názvy entit, které byly zvýrazněny modře. Pokud má utterance entity bez popisku, označte je v utterance. 

1. V utterance vyberte slovo (y). 

1. Vyberte entitu ze seznamu.

    ![Označit entitu](./media/label-suggested-utterances/label-entity.png)

## <a name="align-single-utterance"></a>Zarovnat jeden utterance

Každý utterance má navrhovaný záměr zobrazený ve sloupci **zarovnaný záměr** . 

1. Pokud s tímto návrhem souhlasíte, zaškrtněte políčko na zaškrtnutí.

    ![Zachovat zarovnaný záměr](./media/label-suggested-utterances/align-intent-check.png)

1. Pokud nesouhlasím s návrhem, vyberte v rozevíracím seznamu zarovnaný záměr správný záměr a potom zaškrtněte políčko vpravo od zarovnanáho záměru. 

    ![Zarovnat záměr](./media/label-suggested-utterances/align-intent.png)

1. Po zaškrtnutí políčka se utterance odebere ze seznamu. 

## <a name="align-several-utterances"></a>Zarovnat několik projevy

Pokud chcete zarovnat několik projevy, zaškrtněte políčko nalevo od projevy a pak vyberte na tlačítku **Přidat vybrané** . 

![Zarovnat několik](./media/label-suggested-utterances/add-selected.png)

## <a name="verify-aligned-intent"></a>Ověřit zarovnaný záměr

Na stránce **záměry** můžete ověřit, že utterance byl zarovnán se správným záměrem, vybrat název záměru a zkontrolovat projevy. Utterance z **kontroly koncového bodu projevy** je v seznamu.

## <a name="delete-utterance"></a>Odstranit utterance

Jednotlivé utterancey je možné odstranit ze seznamu revizí. Po odstranění se v seznamu znovu nezobrazí. To platí i v případě, že uživatel zadá stejný utterance z koncového bodu. 

Pokud si nejste jistí, jestli byste měli utterance odstranit, přesuňte ho buď na záměr None, nebo vytvořte nový záměr, jako je "různé", a přesuňte utterance k tomuto záměru. 

## <a name="delete-several-utterances"></a>Odstranit několik projevy

Pokud chcete odstranit několik projevy, vyberte každou položku a v koši napravo od tlačítka **Přidat vybrané** vyberte.

![Odstranit několik](./media/label-suggested-utterances/delete-several.png)


## <a name="next-steps"></a>Další kroky

Chcete-li otestovat, jak se zvyšuje výkon po označení navrhovaných projevy, můžete získat přístup ke konzole test výběrem možnosti **test** na horním panelu. Pokyny k otestování aplikace pomocí testovací konzoly najdete v tématu [výuka a testování vaší aplikace](luis-interactive-test.md).
