---
title: Trénování služby LUIS aplikace – Azure | Dokumentace Microsoftu
description: Language Understanding (LUIS) použijete k natrénování modelu.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/14/2018
ms.author: v-geberr
ms.openlocfilehash: 8ec731ee2110b21a35d76d53fae4a1a6756fb5b8
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2018
ms.locfileid: "37951810"
---
# <a name="train-your-luis-app"></a>Trénování aplikace LUIS

Školení je proces Language Understanding (LUIS) aplikace lepší pochopení jeho přirozeného jazyka. Po aktualizacích na model, jako je například přidávání, úpravy, označování popisky a odstranění entit, záměry nebo projevy trénování aplikace LUIS. 

<!--
When you train a LUIS app by example, LUIS generalizes from the examples you have labeled, and it learns to recognize the relevant intents and entities. This teaches LUIS to improve classification accuracy in the future. -->

Školení a [testování](luis-concept-test.md) aplikace je iterativní proces. Po tréninku aplikace LUIS testování s ukázka projevy a zjistěte, jestli jsou správně rozpoznány záměry a entity. Pokud ne, proveďte aktualizace znovu na aplikaci LUIS, trénování a testování. 

## <a name="train-your-app"></a>Trénování vaší aplikace
Pokud chcete začít iterativní proces, musíte nejprve trénování aplikace LUIS alespoň jednou. Ujistěte se, že každý záměr má alespoň jednu utterance před školení.

1. Přístup k aplikaci tak, že vyberete jeho název na **Moje aplikace** stránky. 

2. Ve vaší aplikaci, vyberte **Train** v horním panelu. 

    ![Tlačítko trénování](./media/luis-how-to-train/train-button.png)

3. Po dokončení školení, zobrazí se zelená oznamovací pruh v horní části stránky prohlížeče.

    ![Aplikace Test pro trénování a stránky](./media/luis-how-to-train/train-success.png)

<!-- The following note refers to what might cause the error message "Training failed: FewLabels for model: <ModelName>" -->

>[!NOTE]
>Pokud máte jeden nebo více tříd Intent ve vaší aplikaci, které neobsahují příklad projevy, nelze trénování vaší aplikace. Přidání projevů pro vaše záměry. Další informace najdete v tématu [přidání projevů příklad](luis-how-to-add-example-utterances.md).

## <a name="next-steps"></a>Další postup

* [Popisek navrhované projevy služba luis](luis-how-to-review-endoint-utt.md) 
* [Použití funkcí pro zvýšení výkonu aplikace LUIS](luis-how-to-add-features.md) 