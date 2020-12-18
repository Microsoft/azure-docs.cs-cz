---
title: Podpora kontejnerů
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/10/2020
ms.author: mbullwin
ms.openlocfilehash: 037b9746571678e24e80bd76e7e2ed173ab8eb90
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2020
ms.locfileid: "97677574"
---
## <a name="create-an-anomaly-detector-resource"></a>Vytvoření prostředku detektoru anomálií

1. Přihlaste se k <a href="https://portal.azure.com" target="_blank">Azure Portal <span class="docon docon-navigate-external x-hidden-focus"></span> </a>.
1. Vyberte <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector" target="_blank">vytvořit prostředek detektoru <span class="docon docon-navigate-external x-hidden-focus"></span> anomálií</a> .
1. Zadejte všechna požadovaná nastavení:

    |Nastavení|Hodnota|
    |--|--|
    |Název|Požadovaný název (2-64 znaků)|
    |Předplatné|Vyberte odpovídající předplatné.|
    |Umístění|Výběr libovolného okolí a dostupného umístění|
    |Cenová úroveň|`F0` – 10 volání za sekundu, 20 tisíc transakcí za měsíc. <br> Ani<br> `S0` -80 volání za sekundu|
    |Skupina prostředků|Vyberte dostupnou skupinu prostředků.|

1. Klikněte na **vytvořit** a počkejte na vytvoření prostředku. Po vytvoření přejděte na stránku prostředků.
1. Shromažďovat nakonfigurované `endpoint` a klíč rozhraní API:

    |Karta klíče a koncový bod na portálu|Nastavení|Hodnota|
    |--|--|--|
    |**Přehled**|Koncový bod|Zkopírujte koncový bod. Vypadá podobně jako ` https://<your-resource-name>.cognitiveservices.azure.com/`|
    |**Klíče**|Klíč rozhraní API|Zkopírujte 1 ze dvou klíčů. Je to 32 alfanumerický znak řetězce bez mezer nebo pomlček, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` .|



