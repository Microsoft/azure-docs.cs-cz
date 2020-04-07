---
title: Vytvořit prostředek LUIS
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/06/2020
ms.author: dapine
ms.openlocfilehash: 88289686e5b091ef3ec309ee9b54ee0f895c8c22
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80754362"
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