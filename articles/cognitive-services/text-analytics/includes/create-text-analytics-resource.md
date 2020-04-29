---
title: Vytvořit prostředek Cognitive Services Analýza textu
titleSuffix: Azure Cognitive Services
description: Naučte se vytvořit prostředek Cognitive Services Analýza textu.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 6cd653909e26dc5e0484ca289a1d2ab47e20457f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "80876398"
---
## <a name="create-a-cognitive-services-text-analytics-resource"></a>Vytvořit prostředek Cognitive Services Analýza textu

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Vyberte **vytvořit prostředek**a pak přejít na **AI + Machine Learning** > **Analýza textu**.
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
1. Shromáždění nakonfigurovaného `endpoint` a klíč rozhraní API:

    |Karta prostředek na portálu|Nastavení|Hodnota|
    |--|--|--|
    |**Přehled**|Koncový bod|Zkopírujte koncový bod. Vypadá to jako `https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0`.|
    |**Klíče**|Klíč rozhraní API|Zkopírujte jeden ze dvou klíčů. Jedná se o alfanumerický řetězec s 32 znaky bez mezer nebo pomlček: <`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`>.|
