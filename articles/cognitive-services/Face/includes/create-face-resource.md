---
title: Podpora kontejnerů
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/5/2019
ms.author: aahi
ms.openlocfilehash: 0a330c2401412e1e3d5e2e49bf8121a681342808
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878357"
---
## <a name="create-an-face-resource"></a>Vytvoření prostředku plochy

1. Přihlášení k [portálu Azure](https://portal.azure.com)
1. Klikněte na Vytvořit zdroj [ **plochy.** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFace)
1. Zadejte všechna požadovaná nastavení:

    |Nastavení|Hodnota|
    |--|--|
    |Name (Název)|Požadovaný název (2-64 znaků)|
    |Předplatné|Vybrat příslušné předplatné|
    |Umístění|Vyberte libovolné blízké a dostupné místo|
    |Cenová úroveň|`F0`- minimální cenová úroveň|
    |Skupina prostředků|Výběr dostupné skupiny prostředků|

1. Klikněte na **Vytvořit** a počkejte na vytvoření prostředku. Po vytvoření přejděte na stránku zdroje.
1. Shromažďujte nakonfigurovaný `endpoint` klíč a klíč rozhraní API:

    |Karta Zdroj na portálu|Nastavení|Hodnota|
    |--|--|--|
    |**Přehled**|Koncový bod|Zkopírujte koncový bod. Vypadá to podobně jako`https://face.cognitiveservices.azure.com/face/v1.0`|
    |**Klíče**|Klíč rozhraní API|Zkopírujte 1 ze dvou klíčů. Jedná se o řetězec 32 alfanumerických znaků `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`bez mezer nebo pomlček .|
