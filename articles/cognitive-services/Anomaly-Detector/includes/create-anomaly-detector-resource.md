---
title: Podpora kontejnerů
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/10/2020
ms.author: aahi
ms.openlocfilehash: 8f93e0ffe74ade79059fc845788faf36ab5b4f13
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "91024801"
---
## <a name="create-an-anomaly-detector-resource"></a>Vytvoření prostředku detektoru anomálií

1. Přihlaste se k [Azure Portal](https://portal.azure.com)
1. Klikněte na [vytvořit prostředek **detektoru anomálií** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector) .
1. Zadejte všechna požadovaná nastavení:

    |Nastavení|Hodnota|
    |--|--|
    |Název|Požadovaný název (2-64 znaků)|
    |Předplatné|Vyberte odpovídající předplatné.|
    |Umístění|Výběr libovolného okolí a dostupného umístění|
    |Cenová úroveň|`F0` – Minimální cenová úroveň|
    |Skupina prostředků|Vyberte dostupnou skupinu prostředků.|

1. Klikněte na **vytvořit** a počkejte na vytvoření prostředku. Po vytvoření přejděte na stránku prostředků.
1. Shromažďovat nakonfigurované `endpoint` a klíč rozhraní API:

    |Karta prostředek na portálu|Nastavení|Hodnota|
    |--|--|--|
    |**Přehled**|Koncový bod|Zkopírujte koncový bod. Vypadá podobně jako `https://westus2.api.cognitive.microsoft.com/`|
    |**Klíče**|Klíč rozhraní API|Zkopírujte 1 ze dvou klíčů. Je to 32 alfanumerický znak řetězce bez mezer nebo pomlček, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` .|



