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
ms.openlocfilehash: 3c42bf2b2acc2472741bd603ea9d653a314ecc40
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73522618"
---
## <a name="create-a-speech-resource"></a>Vytvoření prostředku pro rozpoznávání řeči

1. Přihlaste se k [Azure Portal](https://portal.azure.com)
1. Klikněte na [vytvořit prostředek **řeči** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices) .
1. Zadejte všechna požadovaná nastavení:

    |Nastavení|Hodnota|
    |--|--|
    |Name (Název)|Požadovaný název (2-64 znaků)|
    |Předplatné|Vyberte odpovídající předplatné.|
    |Umístění|Výběr libovolného okolí a dostupného umístění|
    |Cenová úroveň|`F0` – minimální cenová úroveň|
    |Skupina prostředků|Vyberte dostupnou skupinu prostředků.|

1. Klikněte na **vytvořit** a počkejte na vytvoření prostředku. Po vytvoření přejděte na stránku prostředků.
1. Shromažďovat nakonfigurované `endpoint` a klíč rozhraní API:

    |Karta prostředek na portálu|Nastavení|Hodnota|
    |--|--|--|
    |**Přehled**|Koncový bod|Zkopírujte koncový bod. Vypadá podobně jako `https://speech.cognitiveservices.azure.com/sts/v1.0/issuetoken`|
    |**Klíče**|Klíč rozhraní API|Zkopírujte 1 ze dvou klíčů. Je to 32 řetězec alfanumerického znaku, který neobsahuje mezery ani pomlčky, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|
