---
title: Podpora kontejnerů
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: 52d8e1355558b197b193a50c7cde571799541268
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2019
ms.locfileid: "67717163"
---
## <a name="create-a-luis-resource"></a>Vytvořit prostředek služby LUIS

1. Přihlaste se [webu Azure portal](https://portal.azure.com)
1. Klikněte na tlačítko [vytvořit **Language Understanding**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUIS)
1. Zadejte všechna požadovaná nastavení:

    |Nastavení|Hodnota|
    |--|--|
    |Name|Požadovaný název (2-64 znaků)|
    |Subscription|Vyberte odpovídající předplatné|
    |Location|Výběrem kteréhokoli umístění okolí a k dispozici|
    |Cenová úroveň|`F0` -minimální cenová úroveň|
    |Skupina prostředků|Vyberte skupinu prostředků k dispozici|

1. Klikněte na tlačítko **vytvořit** a počkat na prostředek, který má být vytvořen. Po jeho vytvoření, přejděte na stránku prostředků
1. Shromažďování nakonfigurované `endpoint` a klíč rozhraní API:

    |Karta prostředků na portálu|Nastavení|Value|
    |--|--|--|
    |**Přehled**|Koncový bod|Zkopírujte koncový bod. Vypadá podobně jako `https://luis.cognitiveservices.azure.com/luis/v2.0`|
    |**klíče**|Klíč rozhraní API|Zkopírujte 1 dva klíče. Jedná se o 32 alfanumerický znak řetězec bez mezery, pomlčky, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|
