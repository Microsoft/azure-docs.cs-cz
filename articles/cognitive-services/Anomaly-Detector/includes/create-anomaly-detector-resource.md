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
ms.openlocfilehash: b40f1833f08074cb0a8d45fe3afc6bac7cbac7f0
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2019
ms.locfileid: "67717100"
---
## <a name="create-an-anomaly-detector-resource"></a>Vytvořte prostředek detekce anomálií

1. Přihlaste se [webu Azure portal](https://portal.azure.com)
1. Klikněte na tlačítko [vytvořit **detekce anomálií** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector) prostředků
1. Zadejte všechna požadovaná nastavení:

    |Nastavení|Value|
    |--|--|
    |Name|Požadovaný název (2-64 znaků)|
    |Subscription|Vyberte odpovídající předplatné|
    |Location|Výběrem kteréhokoli umístění okolí a k dispozici|
    |Cenová úroveň|`F0` -minimální cenová úroveň|
    |Skupina prostředků|Vyberte skupinu prostředků k dispozici|
    |Zaškrtávací políčko potvrzení ve verzi Preview (povinné)|Určuje, jestli jste si přečetli **ve verzi Preview** Všimněte si, že|

1. Klikněte na tlačítko **vytvořit** a počkat na prostředek, který má být vytvořen. Po jeho vytvoření, přejděte na stránku prostředků
1. Shromažďování nakonfigurované `endpoint` a klíč rozhraní API:

    |Karta prostředků na portálu|Nastavení|Hodnota|
    |--|--|--|
    |**Přehled**|Koncový bod|Zkopírujte koncový bod. Vypadá podobně jako `https://westus2.api.cognitive.microsoft.com/`|
    |**klíče**|Klíč rozhraní API|Zkopírujte 1 dva klíče. Jedná se o 32 alfanumerický znak řetězec bez mezery, pomlčky, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|



