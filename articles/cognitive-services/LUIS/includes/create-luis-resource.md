---
title: Vytvořit prostředek LUIS
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/07/2020
ms.author: aahi
ms.openlocfilehash: ebfe7e80577b163218a7bc501fa4e3e9b206fd62
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879192"
---
<a name="create-luis-resources"></a>

## <a name="create-luis-resources-in-azure-portal"></a>Vytvoření prostředků LUIS na webu Azure Portal

1. Pomocí [tohoto odkazu](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) můžete začít vytvářet prostředky LUIS na webu Azure Portal.

1. Zadejte všechna požadovaná nastavení:

    |Name (Název)|Účel|
    |--|--|
    |Název předplatného| předplatné, které se bude účtovat za prostředek.|
    |Skupina prostředků| Vlastní název skupiny prostředků, který zvolíte nebo vytvoříte. Skupiny prostředků umožňují seskupit prostředky Azure pro přístup a správu.|
    |Name (Název)| Vlastní název, který zvolíte, který se používá jako vlastní subdoména pro dotazy na koncový bod vytváření a předpovědi.|
    |Vytváření umístění|Oblast přidružená k modelu.|
    |Vytváření cenové úrovně|Cenová úroveň určuje maximální transakci za sekundu a měsíc.|
    |Umístění runtime|Oblast přidružená k publikované predikční koncové době runtime.|
    |Cenová úroveň runtime|Cenová úroveň určuje maximální transakci za sekundu a měsíc.|

    > [!div class="mx-imgBorder"]
    > [![Vytvoření prostředku pro porozumění jazykům](../media/luis-how-to-azure-subscription/create-resource-in-azure-small.png)](../media/luis-how-to-azure-subscription/create-resource-in-azure-small.png#lightbox)

1. Klikněte na **Revize + vytvořit** a počkejte na vytvoření zdroje.
1. Po vytvoření obou prostředků, stále na webu Azure portal, vyberte nový prostředek vytváření, pak **Quickstarts** získat vytváření **koncového bodu URL** a **klíč** pro vytváření programově.

> [!TIP]
> Chcete-li použít prostředky, na portálu LUIS [přiřaďte prostředky](../luis-how-to-azure-subscription.md#assign-an-authoring-resource-in-the-luis-portal-for-all-apps).