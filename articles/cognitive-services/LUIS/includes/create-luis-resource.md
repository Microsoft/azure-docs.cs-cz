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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "80879192"
---
<a name="create-luis-resources"></a>

## <a name="create-luis-resources-in-azure-portal"></a>Vytvoření prostředků LUIS v Azure Portal

1. Pomocí [tohoto odkazu](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) můžete začít vytvářet Luis prostředky v Azure Portal.

1. Zadejte všechna požadovaná nastavení:

    |Name|Účel|
    |--|--|
    |Název předplatného| předplatné, které se bude fakturovat za prostředek.|
    |Skupina prostředků| Vlastní název skupiny prostředků, který zvolíte nebo vytvoříte. Skupiny prostředků umožňují seskupit prostředky Azure pro přístup a správu.|
    |Name| Vlastní název, který zvolíte, se používá jako vlastní subdoména pro dotazy na koncový bod vytváření a předpovědi.|
    |Umístění pro vytváření obsahu|Oblast přidružená k vašemu modelu|
    |Vytváření cenové úrovně|Cenová úroveň určuje maximální transakci za sekundu a měsíc.|
    |Umístění modulu runtime|Oblast přidružená k publikovanému běhovému koncovému bodu předpovědi|
    |Cenová úroveň za běhu|Cenová úroveň určuje maximální transakci za sekundu a měsíc.|

    > [!div class="mx-imgBorder"]
    > [![Vytvoření prostředku pro porozumění jazyku](../media/luis-how-to-azure-subscription/create-resource-in-azure-small.png)](../media/luis-how-to-azure-subscription/create-resource-in-azure-small.png#lightbox)

1. Klikněte na tlačítko **zkontrolovat + vytvořit** a počkejte na vytvoření prostředku.
1. Po vytvoření obou prostředků ještě v Azure Portal vyberte nový prostředek pro vytváření obsahu a pak **rychlý Start** k získání **adresy URL koncového bodu** pro vytváření obsahu a **klíč** pro vytváření obsahu prostřednictvím kódu programu.

> [!TIP]
> Pokud chcete prostředky používat, na portálu LUIS [přiřaďte prostředky](../luis-how-to-azure-subscription.md#assign-an-authoring-resource-in-the-luis-portal-for-all-apps).