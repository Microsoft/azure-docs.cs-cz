---
title: Trénování služby LUIS verzi
titleSuffix: Azure Cognitive Services
description: Školení je proces vaše verze aplikace Language Understanding (LUIS) ke zlepšení jeho pochopení přirozeného jazyka. Po aktualizacích na model, jako je například přidávání, úpravy, označování popisky a odstranění entit, záměry nebo projevy trénování aplikace LUIS.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/24/2018
ms.author: diberry
ms.openlocfilehash: f27716cc416b162a5b2df5542d709058f3b3e903
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2018
ms.locfileid: "47182027"
---
# <a name="train-your-luis-app-version"></a>Trénování vaše verze aplikace LUIS

Školení je proces Language Understanding (LUIS) aplikace lepší pochopení jeho přirozeného jazyka. Po aktualizacích na model, jako je například přidávání, úpravy, označování popisky a odstranění entit, záměry nebo projevy trénování aplikace LUIS. 

<!--
When you train a LUIS app by example, LUIS generalizes from the examples you have labeled, and it learns to recognize the relevant intents and entities. This teaches LUIS to improve classification accuracy in the future. -->

Školení a [testování](luis-concept-test.md) aplikace je iterativní proces. Po tréninku aplikace LUIS testování s ukázka projevy a zjistěte, jestli jsou správně rozpoznány záměry a entity. Pokud ne, proveďte aktualizace znovu na aplikaci LUIS, trénování a testování. 

## <a name="how-to-train"></a>Trénování
Pokud chcete začít iterativní proces, musíte nejprve trénování aplikace LUIS alespoň jednou. Ujistěte se, že každý záměr má alespoň jednu utterance před školení.

1. Přístup k aplikaci tak, že vyberete jeho název na **Moje aplikace** stránky. 

2. Ve vaší aplikaci, vyberte **Train** v horním panelu. 

3. Po dokončení školení, zobrazí se zelená oznamovací pruh v horní části stránky prohlížeče.

<!-- The following note refers to what might cause the error message "Training failed: FewLabels for model: <ModelName>" -->

>[!NOTE]
>Pokud máte jeden nebo více tříd Intent ve vaší aplikaci, které neobsahují příklad projevy, nelze trénování vaší aplikace. Přidání projevů pro vaše záměry. Další informace najdete v tématu [přidání projevů příklad](luis-how-to-add-example-utterances.md).

## <a name="train-with-all-data"></a>Trénování se všemi daty
Školení používá malé procento negativní vzorkování. Pokud chcete používat místo drobné příklady negativní všechna data, použijte [nastavení verze rozhraní API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) s `UseAllTrainingData` nastavenou na hodnotu true, chcete-li tuto funkci vypnout. 

## <a name="next-steps"></a>Další postup

* [Popisek navrhované projevy služba luis](luis-how-to-review-endoint-utt.md) 
* [Použití funkcí pro zvýšení výkonu aplikace LUIS](luis-how-to-add-features.md) 