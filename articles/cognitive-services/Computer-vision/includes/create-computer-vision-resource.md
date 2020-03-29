---
title: Podpora kontejnerů
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/07/2019
ms.author: dapine
ms.openlocfilehash: 2cb2cfbdfbac5d496f109d85977b41a050766ab0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "73499100"
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
