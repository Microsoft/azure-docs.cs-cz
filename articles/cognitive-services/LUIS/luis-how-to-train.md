---
title: Cvičení aplikace LEOŠ - Azure | Microsoft Docs
description: Principy jazyka (LEOŠ) použijte k natrénování modelu.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/14/2018
ms.author: v-geberr
ms.openlocfilehash: 4593954e9e0a60beaa5ee86df848f908b23c6b20
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343714"
---
# <a name="train-your-luis-app"></a>Cvičení LEOŠ aplikace

Školení je proces nastavení aplikace znalosti jazyka (LEOŠ) lepší pochopení jeho přirozeného jazyka. Cvičení aplikace LEOŠ po aktualizacích, jako je například přidávání, úpravy, označování a odstranění entity, záměry nebo utterances do modelu. 

<!--
When you train a LUIS app by example, LUIS generalizes from the examples you have labeled, and it learns to recognize the relevant intents and entities. This teaches LUIS to improve classification accuracy in the future. -->

Školení a [testování](luis-concept-test.md) aplikace je iterativní proces. Poté, co jste cvičení LEOŠ aplikaci, otestovat s ukázka utterances a zjistěte, zda jsou správně rozpoznány tříd Intent a entity. Pokud nejsou, proveďte aktualizace znovu pro aplikaci LEOŠ, train a testu. 

## <a name="train-your-app"></a>Cvičení vaší aplikace
Pokud chcete spustit iterativní proces, musíte nejprve ke cvičení aplikace LEOŠ alespoň jednou. Ujistěte se, že každý záměr má alespoň jeden utterance před školení.

1. Přístup k aplikaci tak, že vyberete na jeho název **Moje aplikace** stránky. 

2. V aplikaci, vyberte **Train** v horním panelu. 

    ![Tlačítko Train](./media/luis-how-to-train/train-button.png)

3. Po dokončení školení zelená oznamovací pruh se zobrazí v horní části prohlížeče.

    ![Stránka Train a testování aplikace](./media/luis-how-to-train/train-success.png)

<!-- The following note refers to what might cause the error message "Training failed: FewLabels for model: <ModelName>" -->

>[!NOTE]
>Pokud máte jeden nebo více záměry ve vaší aplikaci, které neobsahují utterances příklad, nelze cvičení vaší aplikace. Přidejte utterances pro všechny vaše záměry. Další informace najdete v tématu [přidat příklad utterances](luis-how-to-add-example-utterances.md).

## <a name="next-steps"></a>Další postup

* [Označení navrhované utterances s LEOŠ](Label-Suggested-Utterances.md) 
* [Funkce lze použít ke zlepšení výkonu aplikace LEOŠ](luis-how-to-add-features.md) 