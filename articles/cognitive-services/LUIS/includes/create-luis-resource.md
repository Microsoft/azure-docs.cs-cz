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
ms.openlocfilehash: a077d255648ff07cc88b43dece889a221c46c11f
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94561504"
---
<a name="create-luis-resources"></a>

## <a name="create-luis-resources-in-the-azure-portal"></a>Vytvoření prostředků LUIS v Azure Portal

1. Pomocí [tohoto odkazu](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) můžete začít vytvářet Luis prostředky v Azure Portal.

1. Zadejte všechna požadovaná nastavení:

    |Název|Účel|
    |--|--|
    |Předplatné | Předplatné, které se bude fakturovat za prostředek.|
    |Skupina prostředků| Název vlastní skupiny prostředků, kterou zvolíte nebo vytvoříte. Skupiny prostředků umožňují seskupit prostředky Azure pro přístup a správu.|
    |Název| Vlastní název, který zvolíte. Používá se jako vaše vlastní subdoména pro dotazy na koncový bod vytváření a předpovědi.|
    |Umístění pro vytváření obsahu|Oblast přidružená k vašemu modelu|
    |Vytváření cenové úrovně|Určuje maximální počet transakcí za sekundu a měsíc.|
    |Umístění předpovědi|Oblast přidružená k publikovanému běhovému koncovému bodu předpovědi|
    |Cenová úroveň předpovědi|Určuje maximální počet transakcí za sekundu a měsíc.|

    > [!div class="mx-imgBorder"]
    > [![Snímek obrazovky, který zobrazuje kartu základy v části vytvořit.](../media/luis-how-to-azure-subscription/create-resource-in-azure-small.png)](../media/luis-how-to-azure-subscription/create-resource-in-azure-small.png#lightbox)

1. Vyberte **zkontrolovat + vytvořit** a počkejte na vytvoření prostředku.
1. Po vytvoření obou prostředků ještě v Azure Portal vyberte nový prostředek pro vytváření obsahu. Pak vyberte **rychlé starty** a získejte **adresu URL koncového bodu** a **klíč** pro vytváření obsahu prostřednictvím kódu programu.

> [!TIP]
> Pokud chcete prostředky používat, na portálu LUIS [přiřaďte prostředky](../luis-how-to-azure-subscription.md#assign-an-authoring-resource-in-the-luis-portal-for-all-apps).
