---
title: Výuka aplikace – LUIS
titleSuffix: Azure Cognitive Services
description: Školení je proces vaše verze aplikace Language Understanding (LUIS) ke zlepšení jeho pochopení přirozeného jazyka. Po aktualizacích na model, jako je například přidávání, úpravy, označování popisky a odstranění entit, záměry nebo projevy trénování aplikace LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: diberry
ms.openlocfilehash: 1da8ab3015730c6b3e1962301a34b1ad43b1aad6
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2019
ms.locfileid: "74143689"
---
# <a name="train-your-active-version-of-the-luis-app"></a>Výuka aktivní verze aplikace LUIS 

Školení je proces Language Understanding (LUIS) aplikace lepší pochopení jeho přirozeného jazyka. Po aktualizacích na model, jako je například přidávání, úpravy, označování popisky a odstranění entit, záměry nebo projevy trénování aplikace LUIS. 

Školení a [testování](luis-concept-test.md) aplikace je iterativní proces. Po tréninku aplikace LUIS testování s ukázka projevy a zjistěte, jestli jsou správně rozpoznány záměry a entity. Pokud ne, proveďte aktualizace znovu na aplikaci LUIS, trénování a testování. 

Školení se aplikuje na aktivní verze na portálu služby LUIS. 

## <a name="how-to-train-interactively"></a>Interaktivní trénování

Spuštění iterativní proces [LUIS portál](https://www.luis.ai), musíte nejprve trénování aplikace LUIS alespoň jednou. Ujistěte se, že každý záměr má alespoň jednu utterance před školení.

1. Přístup k aplikaci tak, že vyberete jeho název na **Moje aplikace** stránky. 

1. Ve vaší aplikaci, vyberte **Train** v horním panelu. 

1. Po dokončení školení se v horní části prohlížeče zobrazí oznámení.

## <a name="training-date-and-time"></a>Datum a čas školení

Datum a čas školení jsou GMT + 2. 

## <a name="train-with-all-data"></a>Trénování se všemi daty

Školení používá malé procento negativní vzorkování. Pokud chcete použít všechna data namísto malého negativního vzorkování, použijte [rozhraní API](#version-settings-api-use-of-usealltrainingdata).

### <a name="version-settings-api-use-of-usealltrainingdata"></a>Nastavení verze API použití UseAllTrainingData

Tuto funkci můžete vypnout pomocí [rozhraní API pro nastavení verze](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) s `UseAllTrainingData` nastavenou na hodnotu true. 

## <a name="unnecessary-training"></a>Zbytečné školení

Nemusíte trénování po každé změně jednoho. Školení by mělo provedeno po skupinu změny se použijí k modelu a je dalším krokem, které chcete provést testování nebo publikovat. Pokud není potřeba testovat nebo publikovat, není nutné školení. 

## <a name="training-with-the-rest-apis"></a>Trénování s využitím rozhraní REST API

Školení na portálu služby LUIS je krokovat stisknutím klávesy **trénování** tlačítko. Trénování s využitím rozhraní REST API je dvoustupňový proces. První je [žádosti školení](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45) pomocí HTTP POST. Vyžádejte [školení stav](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46) pomocí protokolu HTTP Get. 

Pokud chcete zjistit, po dokončení školení, budete muset dotazování stavu, dokud všechny modely jsou úspěšně školení. 

## <a name="next-steps"></a>Další kroky

* [Interaktivní testování](luis-interactive-test.md)
* [Dávkové testování](luis-how-to-batch-test.md)
