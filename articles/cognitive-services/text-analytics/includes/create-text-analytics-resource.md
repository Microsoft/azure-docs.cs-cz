---
title: Vytvoření prostředku analýzy textu služeb cognitive services
titleSuffix: Azure Cognitive Services
description: Přečtěte si, jak vytvořit prostředek analýzy textu služeb Cognitive Services.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: d3b65174f3d161e0b8780a890f297d9d1c812b44
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "74383478"
---
## <a name="create-a-cognitive-services-text-analytics-resource"></a>Vytvoření prostředku analýzy textu služeb cognitive services

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. Vyberte **Vytvořit zdroj**a přejděte na **AI + Machine Learning** > **Text Analytics**.
   Nebo přejděte na [vytvořit analýzu textu](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics).
1. Zadejte všechna požadovaná nastavení:

    |Nastavení|Hodnota|
    |--|--|
    |Name (Název)|Zadejte název (2-64 znaků).|
    |Předplatné|Vyberte příslušné předplatné.|
    |Umístění|Vyberte blízké místo.|
    |Cenová úroveň| Zadejte **S**, standardní cenovou úroveň.|
    |Skupina prostředků|Vyberte dostupnou skupinu prostředků.|

1. Vyberte **Vytvořit**a počkejte, až bude zdroj vytvořen. Prohlížeč automaticky přesměruje na nově vytvořenou stránku zdroje.
1. Shromážděte nakonfigurovaný `endpoint` klíč a klíč rozhraní API:

    |Karta Zdroj na portálu|Nastavení|Hodnota|
    |--|--|--|
    |**Přehled**|Koncový bod|Zkopírujte koncový bod. Zdá se, `https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0`že podobné .|
    |**Klíče**|Klíč rozhraní API|Zkopírujte jeden ze dvou klíčů. Jedná se o alfanumerický řetězec o 32 znacích bez mezer nebo pomlček: <`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`>.|
