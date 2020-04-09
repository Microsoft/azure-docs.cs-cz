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
ms.openlocfilehash: 8dbe36693e551bd03022f4a00044b75b912c834a
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875101"
---
## <a name="create-an-anomaly-detector-resource"></a>Vytvoření prostředku detektoru anomálií

1. Přihlášení k [portálu Azure](https://portal.azure.com)
1. Klikněte na Vytvořit prostředek [ **detektoru anomálií.** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector)
1. Zadejte všechna požadovaná nastavení:

    |Nastavení|Hodnota|
    |--|--|
    |Name (Název)|Požadovaný název (2-64 znaků)|
    |Předplatné|Vybrat příslušné předplatné|
    |Umístění|Vyberte libovolné blízké a dostupné místo|
    |Cenová úroveň|`F0`- minimální cenová úroveň|
    |Skupina prostředků|Výběr dostupné skupiny prostředků|
    |Zaškrtávací políčko Potvrzení náhledu (povinné)|Zda jste si přečetli oznámení o **náhledu**|

1. Klikněte na **Vytvořit** a počkejte na vytvoření prostředku. Po vytvoření přejděte na stránku zdroje.
1. Shromažďujte nakonfigurovaný `endpoint` klíč a klíč rozhraní API:

    |Karta Zdroj na portálu|Nastavení|Hodnota|
    |--|--|--|
    |**Přehled**|Koncový bod|Zkopírujte koncový bod. Vypadá to podobně jako`https://westus2.api.cognitive.microsoft.com/`|
    |**Klíče**|Klíč rozhraní API|Zkopírujte 1 ze dvou klíčů. Jedná se o řetězec 32 alfanumerických znaků `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`bez mezer nebo pomlček .|



