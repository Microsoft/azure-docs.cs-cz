---
title: Vytvořit prostředek Cognitive Services Analýza textu
titleSuffix: Azure Cognitive Services
description: Naučte se vytvořit prostředek Cognitive Services Analýza textu.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 6a9363472e1d5770232ca9a0b151fb309d9c4c75
ms.sourcegitcommit: b49431b29a53efaa5b82f9be0f8a714f668c38ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/22/2019
ms.locfileid: "68377421"
---
## <a name="create-a-cognitive-services-text-analytics-resource"></a>Vytvořit prostředek Cognitive Services Analýza textu

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Vyberte **vytvořit prostředek**a pak přejít na **AI + Machine Learning** > **Analýza textu**.
   Případně můžete přejít na [vytvořit analýza textu](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics).
1. Zadejte všechna požadovaná nastavení:

    |Nastavení|Value|
    |--|--|
    |Name|Zadejte název (2-64 znaků).|
    |Subscription|Vyberte příslušné předplatné.|
    |Location|Vyberte okolní umístění.|
    |Cenová úroveň| Zadejte **S**, cenová úroveň Standard.|
    |Resource group|Vyberte dostupnou skupinu prostředků.|

1. Vyberte **vytvořit**a počkejte na vytvoření prostředku. Prohlížeč automaticky přesměruje na stránku nově vytvořeného prostředku.
1. Shromáždění nakonfigurovaného `endpoint` a klíč rozhraní API:

    |Karta prostředek na portálu|Nastavení|Hodnota|
    |--|--|--|
    |**Přehled**|Koncový bod|Zkopírujte koncový bod. Vypadá to jako `https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0`.|
    |**Klíče**|Klíč rozhraní API|Zkopírujte jeden ze dvou klíčů. Jedná se o alfanumerický řetězec s 32 znaky bez mezer nebo pomlček: <`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`>.|
