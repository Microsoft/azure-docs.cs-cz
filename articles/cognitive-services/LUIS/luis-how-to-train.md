---
title: Vytrénovat aplikaci
titleSuffix: Language Understanding - Azure Cognitive Services
description: Školení je proces vaše verze aplikace Language Understanding (LUIS) ke zlepšení jeho pochopení přirozeného jazyka. Po aktualizacích na model, jako je například přidávání, úpravy, označování popisky a odstranění entit, záměry nebo projevy trénování aplikace LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/23/2019
ms.author: diberry
ms.openlocfilehash: ff1239093f7562bd314305ae3ea8a580fddb8326
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55862271"
---
# <a name="train-your-active-version-of-the-luis-app"></a>Trénování vaše aktivní verzi aplikace LUIS 

Školení je proces Language Understanding (LUIS) aplikace lepší pochopení jeho přirozeného jazyka. Po aktualizacích na model, jako je například přidávání, úpravy, označování popisky a odstranění entit, záměry nebo projevy trénování aplikace LUIS. 

<!--
When you train a LUIS app by example, LUIS generalizes from the examples you have labeled, and it learns to recognize the relevant intents and entities. This teaches LUIS to improve classification accuracy in the future. -->

Školení a [testování](luis-concept-test.md) aplikace je iterativní proces. Po tréninku aplikace LUIS testování s ukázka projevy a zjistěte, jestli jsou správně rozpoznány záměry a entity. Pokud ne, proveďte aktualizace znovu na aplikaci LUIS, trénování a testování. 

Školení se aplikuje na aktivní verze na portálu služby LUIS. 

## <a name="how-to-train-interactively"></a>Interaktivní trénování

Spuštění iterativní proces [LUIS portál](https://www.luis.ai), musíte nejprve trénování aplikace LUIS alespoň jednou. Ujistěte se, že každý záměr má alespoň jednu utterance před školení.

1. Přístup k aplikaci tak, že vyberete jeho název na **Moje aplikace** stránky. 

2. Ve vaší aplikaci, vyberte **Train** v horním panelu. 

3. Po dokončení školení, zobrazí se zelená oznamovací pruh v horní části stránky prohlížeče.

<!-- The following note refers to what might cause the error message "Training failed: FewLabels for model: <ModelName>" -->

>[!NOTE]
>Pokud máte jeden nebo více tříd Intent ve vaší aplikaci, které neobsahují příklad projevy, nelze trénování vaší aplikace. Přidání projevů pro vaše záměry. Další informace najdete v tématu [přidání projevů příklad](luis-how-to-add-example-utterances.md).

## <a name="train-with-all-data"></a>Trénování se všemi daty

Školení používá malé procento negativní vzorkování. Pokud chcete používat místo drobné příklady negativní všechna data, použijte [nastavení verze rozhraní API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) s `UseAllTrainingData` nastaveno na true pro vypnutí této funkce. 

## <a name="unnecessary-training"></a>Zbytečné školení

Nemusíte trénování po každé změně jednoho. Školení by mělo provedeno po skupinu změny se použijí k modelu a je dalším krokem, které chcete provést testování nebo publikovat. Pokud není potřeba testovat nebo publikovat, není nutné školení. 

## <a name="training-with-the-rest-apis"></a>Trénování s využitím rozhraní REST API

Školení na portálu služby LUIS je krokovat stisknutím klávesy **trénování** tlačítko. Trénování s využitím rozhraní REST API je dvoustupňový proces. První je [žádosti školení](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45) pomocí HTTP POST. Vyžádejte [školení stav](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46) pomocí protokolu HTTP Get. 

Pokud chcete zjistit, po dokončení školení, budete muset dotazování stavu, dokud všechny modely jsou úspěšně školení. 

## <a name="next-steps"></a>Další postup

* [Popisek navrhované projevy služba luis](luis-how-to-review-endoint-utt.md) 
* [Použití funkcí pro zvýšení výkonu aplikace LUIS](luis-how-to-add-features.md) 
