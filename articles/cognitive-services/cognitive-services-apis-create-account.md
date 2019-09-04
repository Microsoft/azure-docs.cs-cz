---
title: Vytvoření prostředku Cognitive Services v Azure Portal
titleSuffix: Azure Cognitive Services
description: Začněte s Azure Cognitive Services vytvořením a přihlášením k odběru prostředku v Azure Portal.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 07/16/2019
ms.author: aahi
ms.openlocfilehash: c7db2b4d49e3b9297c32d2e11ffe7c7702c17544
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2019
ms.locfileid: "70274577"
---
# <a name="create-a-cognitive-services-resource-using-the-azure-portal"></a>Vytvoření prostředku Cognitive Services pomocí Azure Portal

Pomocí tohoto rychlého startu vytvoříte prostředek Azure Cognitive Services pomocí Azure Portal. Po úspěšném vytvoření prostředku Cognitive Services obdržíte koncový bod a klíč, který můžete použít k ověření svých aplikací.

## <a name="prerequisites"></a>Požadavky

* Platné předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/).

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="create-a-new-azure-cognitive-services-resource"></a>Vytvořit nový prostředek služby Azure Cognitive Services

Před vytvořením prostředku Cognitive Services musíte mít skupinu prostředků Azure, která má prostředek obsahovat. Při vytváření nového prostředku máte možnost vytvořit novou skupinu prostředků, nebo použít existující. V tomto článku se dozvíte, jak vytvořit novou skupinu prostředků.

1. Přihlaste se k [Azure Portal](https://portal.azure.com)a klikněte na **+ vytvořit prostředek**.

    ![Vyberte rozhraní API služeb Cognitive Services](media/cognitive-services-apis-create-account/azurePortalScreenMulti.png)

2. Jak bylo vysvětleno dříve, můžete vytvořit prostředek Cognitive Services dvěma způsoby – pomocí prostředku s více službami nebo prostředku s jednou službou.

    #### <a name="multi-service-resourcetabmultiservice"></a>[Prostředek s více službami](#tab/multiservice)

    Prostředek s více službami vytvoříte tak, že na panelu hledání zadáte **Cognitive Services** .

    ![Hledat Cognitive Services](media/cognitive-services-apis-create-account/azureCogServSearchMulti.png)

    Na stránce Cognitive Services vyberte **vytvořit**.

    ![Vytvořit účet Cognitive Services](media/cognitive-services-apis-create-account/azurecogservsearchmulti-2.png)

    #### <a name="single-service-resourcetabsingleservice"></a>[Prostředek s jednou službou](#tab/singleservice)

    Pokud chcete zobrazit všechny dostupné služby pro rozpoznávání, vyberte v části **Azure Marketplace** **AI a Machine Learning**. Pokud nevidíte službu, kterou vás zajímá, klikněte na **Zobrazit vše** a posuňte se na **Cognitive Services**. Kliknutím na **Zobrazit další** zobrazíte celý katalog Cognitive Services.

    Jakmile budete u služby, které vás zajímá, klikněte na **vytvořit**.
    
    ![Vyberte rozhraní API služeb Cognitive Services](media/cognitive-services-apis-create-account/azureMarketplace.png)

    ***
3. Na stránce **vytvořit** zadejte následující informace:

    #### <a name="multi-service-resourcetabmultiservice"></a>[Prostředek s více službami](#tab/multiservice)

    |    |    |
    |--|--|
    | **Název** | Popisný název prostředku služby pro rozpoznávání. Například *MyCognitiveServicesResource*. |
    | **Předplatné** | Vyberte jedno z dostupných předplatných Azure. |
    | **Location** | Umístění instance služby vyvnímání. Různá umístění můžou způsobit latenci, ale nemají žádný vliv na dostupnost vašeho prostředku za běhu. Zapamatujte si umístění Azure, jak ho budete možná potřebovat při volání Azure Cognitive Services. |
    | **Cenová úroveň** | Náklady na účet Cognitive Services závisí na možnostech, které zvolíte, a na vašem využití. Další informace najdete v [podrobnostech o cenách](https://azure.microsoft.com/pricing/details/cognitive-services/)rozhraní API.
    | **Skupina prostředků** | Skupina prostředků Azure, která bude obsahovat váš prostředek Cognitive Services. Můžete vytvořit novou skupinu nebo ji přidat do již existující skupiny. |

    ![Obrazovka pro vytvoření prostředku](media/cognitive-services-apis-create-account/resource_create_screen-multi.png)

    Klikněte na možnost **Vytvořit**.

    #### <a name="single-service-resourcetabsingleservice"></a>[Prostředek s jednou službou](#tab/singleservice)

    |    |    |
    |--|--|
    | **Název** | Popisný název prostředku služby pro rozpoznávání. Například *TextAnalyticsResource*. |
    | **Předplatné** | Vyberte jedno z dostupných předplatných Azure. |
    | **Location** | Umístění instance služby vyvnímání. Různá umístění můžou způsobit latenci, ale nemají žádný vliv na dostupnost vašeho prostředku za běhu. Zapamatujte si umístění Azure, jak ho budete možná potřebovat při volání Azure Cognitive Services. |
    | **Cenová úroveň** | Náklady na účet Cognitive Services závisí na možnostech, které zvolíte, a na vašem využití. Další informace najdete v [podrobnostech o cenách](https://azure.microsoft.com/pricing/details/cognitive-services/)rozhraní API.
    | **Skupina prostředků** | Skupina prostředků Azure, která bude obsahovat váš prostředek Cognitive Services. Můžete vytvořit novou skupinu nebo ji přidat do již existující skupiny. |

    ![Obrazovka pro vytvoření prostředku](media/cognitive-services-apis-create-account/resource_create_screen.png)

    Klikněte na možnost **Vytvořit**.

    ***

## <a name="get-the-keys-for-your-resource"></a>Získat klíče pro svůj prostředek

Po úspěšném vytvoření prostředku se v pravém horním rohu obrazovky zobrazí automaticky otevírané oznámení. V oznámení klikněte na **Přejít k prostředku** a zobrazí se prostředek služby pro rozpoznávání, který jste vytvořili. 

![Přejít na prostředek služby rozpoznávání](media/cognitive-services-apis-create-account/cog-serv-go-to-resource.png)

V otevřeném podokně pro rychlé zprovoznění máte přístup ke svému koncovému bodu a klíči.

![Získat klíč a koncový bod](media/cognitive-services-apis-create-account/get-cog-serv-keys.png)

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="pricing-tiers-and-billing"></a>Cenové úrovně a fakturace

Cenové úrovně (a množství, které se vám bude účtovat), vycházejí z počtu transakcí, které odešlete pomocí ověřovacích informací. Každá cenová úroveň určuje:
* maximální počet povolených transakcí za sekundu (TPS).
* funkce služby povolené v rámci cenové úrovně.
* Náklady na předdefinovaný počet transakcí. Po překročení této částky se bude účtovat další poplatek, který je uvedený v [podrobnostech o cenách](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) vaší služby.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné Cognitive Services, můžete prostředek nebo skupinu prostředků odstranit. Odstraněním skupiny prostředků dojde také k odstranění všech dalších prostředků obsažených ve skupině.

Odebrání skupiny prostředků pomocí webu Azure Portal:

1. Na webu Azure Portal rozbalením nabídky na levé straně otevřete nabídku služeb a zvolte **Skupiny prostředků**. Zobrazí se seznam skupin prostředků.
2. Vyhledejte skupinu prostředků, kterou chcete odstranit, a klikněte pravým tlačítkem myši na tlačítko Další (...) na pravé straně seznamu.
3. Vyberte **Odstranit skupinu prostředků** a potvrďte tuto akci.

## <a name="see-also"></a>Viz také:

* [Ověřování požadavků do Azure Cognitive Services](authentication.md)
* [Co je Azure Cognitive Services?](Welcome.md)
* [Podpora přirozeného jazyka](language-support.md)
* [Podpora kontejneru Docker](cognitive-services-container-support.md)
