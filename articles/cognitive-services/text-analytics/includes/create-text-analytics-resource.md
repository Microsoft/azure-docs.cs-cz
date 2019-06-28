---
title: Podpora kontejnerů
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak vytvořit prostředek služeb cognitive services text analytics.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 022ffcd806d4d4f89f8a8cf256a541518ea12602
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67455091"
---
## <a name="create-a-cognitive-services-text-analytics-resource"></a>Vytvoření prostředku Cognitive Services pro analýzu textu

1. Přihlaste se [webu Azure portal](https://portal.azure.com)
1. Vyberte **+ vytvořit prostředek**, přejděte na **AI a strojové učení > rozhraní Text Analytics**, nebo klikněte na tlačítko [vytvořit **pro analýzu textu**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)
1. Zadejte všechna požadovaná nastavení:

    |Nastavení|Hodnota|
    |--|--|
    |Název|Požadovaný název (2-64 znaků)|
    |Předplatné|Vyberte odpovídající předplatné|
    |Location|Vyberte nejbližší umístění|
    |Cenová úroveň|`S` -cenovou úroveň standard|
    |Skupina prostředků|Vyberte skupinu prostředků k dispozici|

1. Klikněte na tlačítko **vytvořit** a počkat na prostředek, který má být vytvořen. Po jeho vytvoření, váš prohlížeč automaticky přesměruje na stránku pro nově vytvořený prostředek
1. Shromažďování nakonfigurované `endpoint` a klíč rozhraní API:

    |Karta prostředků na portálu|Nastavení|Hodnota|
    |--|--|--|
    |**Přehled**|Koncový bod|Zkopírujte koncový bod. Vypadá podobně jako `https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0`|
    |**klíče**|Klíč rozhraní API|Zkopírujte 1 dva klíče. Jedná se o 32 alfanumerický znak řetězec bez mezery, pomlčky, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|