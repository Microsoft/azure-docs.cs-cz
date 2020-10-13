---
title: Výuka aplikace – LUIS
titleSuffix: Azure Cognitive Services
description: Školení je proces výuky vaší verze aplikace Language Understanding (LUIS), která vylepšuje svůj přirozený jazyk. LUIS aplikaci po aktualizacích modelu, jako je přidání, úpravy, označení nebo odstranění entit, záměrů nebo projevy.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 11/15/2019
ms.openlocfilehash: a53857061110d5a77ac3d166277e7076f4f7f9c6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91541369"
---
# <a name="train-your-active-version-of-the-luis-app"></a>Výuka aktivní verze aplikace LUIS

Školení je proces výuky vaší aplikace Language Understanding (LUIS), která vylepšuje svůj přirozený jazyk. LUIS aplikaci po aktualizacích modelu, jako je přidání, úpravy, označení nebo odstranění entit, záměrů nebo projevy.

Školení a [testování](luis-concept-test.md) aplikace je iterativní proces. Po natrénování aplikace LUIS ji můžete otestovat s ukázkovými promluvami, abyste zjistili, jestli se správně rozpoznávají záměry a entity. Pokud nejsou, proveďte aktualizace aplikace LUIS, školení a testování znovu.

Školení se aplikuje na aktivní verzi na portálu LUIS.

## <a name="how-to-train-interactively"></a>Jak interaktivně proškolit

Chcete-li spustit iterativní proces na [portálu Luis](https://www.luis.ai), musíte nejprve vytvořit aplikaci Luis alespoň jednou. Ujistěte se, že každý záměr má před školením aspoň jeden utterance.

1. Přístup k aplikaci získáte tak, že na stránce **Moje aplikace** vyberete její název.

1. V aplikaci vyberte v horním panelu možnost **vlak** .

1. Po dokončení školení se v horní části prohlížeče zobrazí oznámení.

## <a name="training-date-and-time"></a>Datum a čas školení

Datum a čas školení jsou GMT + 2.

## <a name="train-with-all-data"></a>Naučit se všemi daty

Školení používá malé procento negativního vzorkování. Pokud chcete použít všechna data namísto malého negativního vzorkování, použijte [rozhraní API](#version-settings-api-use-of-usealltrainingdata).

### <a name="version-settings-api-use-of-usealltrainingdata"></a>Nastavení verze API použití UseAllTrainingData

Tuto funkci můžete vypnout pomocí [rozhraní API pro nastavení verze](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) s `UseAllTrainingData` nastavením na hodnotu true.

## <a name="unnecessary-training"></a>Zbytečné školení

Nemusíte vlakovat po každé jedné změně. Školení je potřeba provést po použití skupiny změn v modelu a dalšího kroku, který chcete udělat, je otestovat nebo publikovat. Pokud nepotřebujete testovat nebo publikovat, školení není nutné.

## <a name="training-with-the-rest-apis"></a>Školení s rozhraními REST API

Školení na portálu LUIS je jedním krokem při stisknutí tlačítka **vlak** . Školení s rozhraními REST API je proces se dvěma kroky. První je požadavek na [školení](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45) pomocí HTTP POST. Pak vyžádejte [stav školení](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46) pomocí HTTP GET.

Aby bylo možné zjistit, kdy školení bylo dokončeno, musíte se dotazovat na stav, dokud nebudou všechny modely úspěšně vyškolené.

## <a name="next-steps"></a>Další kroky

* [Interaktivní testování](luis-interactive-test.md)
* [Dávkové testování](luis-how-to-batch-test.md)
