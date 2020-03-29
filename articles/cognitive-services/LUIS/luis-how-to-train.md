---
title: Vlaková aplikace – LUIS
titleSuffix: Azure Cognitive Services
description: Školení je proces výuky verze aplikace pro porozumění jazykům (LUIS) ke zlepšení jejího porozumění přirozenému jazyku. Trénování aplikace LUIS po aktualizacích modelu, jako je například přidávání, úpravy, popisování nebo odstranění entit, záměrů nebo projevy.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219860"
---
# <a name="train-your-active-version-of-the-luis-app"></a>Trénování aktivní verze aplikace LUIS 

Školení je proces výuky aplikace Luis (Language Understanding) ke zlepšení jejího porozumění přirozenému jazyku. Trénování aplikace LUIS po aktualizacích modelu, jako je například přidávání, úpravy, popisování nebo odstranění entit, záměrů nebo projevy. 

Školení a [testování](luis-concept-test.md) aplikace je iterativní proces. Po trénování aplikace LUIS, otestovat s ukázkové projevy, abyste zjistili, zda záměry a entity jsou rozpoznány správně. Pokud nejsou, proveďte aktualizace aplikace LUIS, trénování a testování znovu. 

Školení se použije na aktivní verzi na portálu LUIS. 

## <a name="how-to-train-interactively"></a>Jak interaktivně trénovat

Chcete-li spustit iterativní proces na [portálu LUIS](https://www.luis.ai), musíte nejprve trénovat aplikaci LUIS alespoň jednou. Ujistěte se, že každý záměr má alespoň jeden utterance před školení.

1. Přístup k aplikaci vyberete její název na stránce **Moje aplikace.** 

1. V aplikaci vhorní části panelu vyberte **Trénovat.** 

1. Po dokončení školení se v horní části prohlížeče zobrazí oznámení.

## <a name="training-date-and-time"></a>Datum a čas školení

Datum a čas tréninku jsou GMT + 2. 

## <a name="train-with-all-data"></a>Vlak se všemi daty

Školení používá malé procento negativního odběru vzorků. Pokud chcete použít všechna data namísto malé negativní vzorkování, použijte [rozhraní API](#version-settings-api-use-of-usealltrainingdata).

### <a name="version-settings-api-use-of-usealltrainingdata"></a>Použití rozhraní API pro nastavení verze useAllTrainingData

Chcete-li tuto funkci `UseAllTrainingData` vypnout, použijte rozhraní API pro nastavení [verze](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) s nastavenou na hodnotu true. 

## <a name="unnecessary-training"></a>Zbytečné školení

Nemusíte trénovat po každé změně. Školení by mělo být provedeno po skupině změn, které jsou použity na model a dalším krokem, který chcete udělat, je otestovat nebo publikovat. Pokud nepotřebujete testovat nebo publikovat, školení není nutné. 

## <a name="training-with-the-rest-apis"></a>Školení s REST API

Školení na portálu LUIS je jedním krokem stisknutí tlačítka **Vlak.** Školení s REST API je dvoustupňový proces. Prvním z toho je [požádat o školení](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45) s HTTP POST. Pak požádejte o [stav školení](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46) pomocí http get. 

Chcete-li vědět, kdy je školení dokončeno, musíte dotazovat stav, dokud nebudou všechny modely úspěšně vyškoleny. 

## <a name="next-steps"></a>Další kroky

* [Interaktivní testování](luis-interactive-test.md)
* [Dávkové testování](luis-how-to-batch-test.md)
