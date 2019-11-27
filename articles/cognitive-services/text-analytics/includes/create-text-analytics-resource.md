---
title: Vytvořit prostředek Cognitive Services Analýza textu
titleSuffix: Azure Cognitive Services
description: Naučte se vytvořit prostředek Cognitive Services Analýza textu.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: d3b65174f3d161e0b8780a890f297d9d1c812b44
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383478"
---
## <a name="create-a-cognitive-services-text-analytics-resource"></a>Vytvořit prostředek Cognitive Services Analýza textu

1. Přihlaste se na web [Azure Portal ](https://portal.azure.com).
1. Vyberte **vytvořit prostředek**a pak > **Analýza textu**přejít na **AI + Machine Learning** .
   Případně můžete přejít na [vytvořit analýza textu](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics).
1. Zadejte všechna požadovaná nastavení:

    |Nastavení|Hodnota|
    |--|--|
    |Název|Zadejte název (2-64 znaků).|
    |Předplatné|Vyberte příslušné předplatné.|
    |Umístění|Vyberte okolní umístění.|
    |Cenová úroveň| Zadejte **S**, cenová úroveň Standard.|
    |Skupina prostředků|Vyberte dostupnou skupinu prostředků.|

1. Vyberte **vytvořit**a počkejte na vytvoření prostředku. Prohlížeč automaticky přesměruje na stránku nově vytvořeného prostředku.
1. Shromážděte nakonfigurované `endpoint` a klíč rozhraní API:

    |Karta prostředek na portálu|Nastavení|Hodnota|
    |--|--|--|
    |**Přehled**|Koncový bod|Zkopírujte koncový bod. Vypadá to jako `https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0`.|
    |**Klíče**|Klíč rozhraní API|Zkopírujte jeden ze dvou klíčů. Je to znak alfanumerického znaku 32 a nesmí obsahovat mezery ani pomlčky: <`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`>.|
