---
title: Podpora kontejnerů
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 2647c604bebc2885f5c8516eb997792b65751e88
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876068"
---
## <a name="create-an-computer-vision-resource"></a>Vytvoření prostředku počítačového vidění

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. Klikněte na Vytvořit zdroj [ **počítačového vidění.** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision)
1. Zadejte všechna požadovaná nastavení:

    |Nastavení|Hodnota|
    |--|--|
    |Name (Název)|Požadovaný název (2-64 znaků)|
    |Předplatné|Vybrat příslušné předplatné|
    |Umístění|Vyberte libovolné blízké a dostupné místo|
    |Cenová úroveň|`F0`- minimální cenová úroveň|
    |Skupina prostředků|Výběr dostupné skupiny prostředků|

1. Klikněte na **Vytvořit** a počkejte na vytvoření prostředku. Po vytvoření přejděte na stránku prostředků.
1. Shromážděte nakonfigurované `{ENDPOINT_URI}` a `{API_KEY}`, viz shromažďování [požadovaných parametrů](../computer-vision-how-to-install-containers.md#gathering-required-parameters) podrobnosti.
