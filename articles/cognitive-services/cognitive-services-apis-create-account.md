---
title: Vytvoření prostředku Cognitive Services v Azure Portal
titleSuffix: Azure Cognitive Services
description: Začněte s Azure Cognitive Services vytvořením a přihlášením k odběru prostředku v Azure Portal.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: aahi
ms.openlocfilehash: 14157ed22ea40a2157f696e91c59af9b58110bf2
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2019
ms.locfileid: "72819940"
---
# <a name="create-a-cognitive-services-resource-using-the-azure-portal"></a>Vytvoření prostředku Cognitive Services pomocí Azure Portal

V tomto rychlém startu můžete začít používat Azure Cognitive Services. Po vytvoření prostředku služby vnímání v Azure Portal získáte koncový bod a klíč pro ověřování vašich aplikací.


[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="prerequisites"></a>Předpoklady

* Platné předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/).

## <a name="create-a-new-azure-cognitive-services-resource"></a>Vytvořit nový prostředek služby Azure Cognitive Services

1. Vytvořte prostředek.

    #### <a name="multi-service-resourcetabmultiservice"></a>[Prostředek s více službami](#tab/multiservice)
    
    Prostředek s více službami se jmenuje **Cognitive Services** na portálu. [Vytvořte prostředek Cognitive Services](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne).
    
    Prostředek s více službami v současnosti umožňuje přístup k následujícím Cognitive Services:
    
    |                  |                                                      |                    |                               |                  |
    |------------------|------------------------------------------------------|--------------------|-------------------------------|------------------|
    | Computer Vision  | Content Moderator                                    | Tvář               | Language Understanding (LUIS) | Analýza textu   |
    | Translator Text  | Vyhledávání Bingu v7 <br>(Web, obrázek, novinky, video, vizuál) | Vlastní vyhledávání Bingu | Vyhledávání entit Bingu            | Automatické návrhy Bingu |
    | Kontrola pravopisu Bingu |                                                      |                    |                               |                  |
    
    #### <a name="single-service-resourcetabsingleservice"></a>[Prostředek s jednou službou](#tab/singleservice)

    K vytvoření prostředku pro dostupné Cognitive Services použijte odkazy níže:

    | Vidění                      | Řeč                  | Jazyk                          | Rozhodnutí             | Hledat                 |
    |-----------------------------|-------------------------|-----------------------------------|----------------------|------------------------|
    | [Počítačové zpracování obrazu](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision)         | [Hlasové služby](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices)     | [Moderní čtečka](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesImmersiveReader)              | [Detektor anomálií](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector) | [Vyhledávání Bingu rozhraní API v7](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7) |
    | [Služba Custom Vision Service](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesCustomVision) | [rozpoznávání mluvčího](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeakerRecognition) | [Language Understanding (LUIS)](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) | [Content Moderator](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator) | [Vlastní vyhledávání Bingu](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingCustomSearch) |
    | [Plochy](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFace)                    |                         | [QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)                     | [Přizpůsobení](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer)     | [Vyhledávání entit Bingu](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingEntitySearch) |
    | [Rozpoznávání rukopisu](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesInkRecognizer)        |                         | [Analýza textu](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)                |                      | [Kontrola pravopisu Bingu](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingSpellCheck-v7)   |
    |           |                         | [Translator Text](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation)               |                      | [Automatické návrhy Bingu](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingAutosuggest-v7)                       |
    ***

3. Na stránce **vytvořit** zadejte následující informace:

    #### <a name="multi-service-resourcetabmultiservice"></a>[Prostředek s více službami](#tab/multiservice)

    |    |    |
    |--|--|
    | **Název** | Popisný název prostředku služby pro rozpoznávání. Například *MyCognitiveServicesResource*. |
    | **Předplatné** | Vyberte jedno z dostupných předplatných Azure. |
    | **Umístění** | Umístění instance služby vyvnímání. Různá umístění můžou způsobit latenci, ale nemají žádný vliv na dostupnost vašeho prostředku za běhu. Zapamatujte si umístění Azure, jak ho budete možná potřebovat při volání Azure Cognitive Services. |
    | **Cenová úroveň** | Náklady na účet Cognitive Services závisí na možnostech, které zvolíte, a na vašem využití. Další informace najdete v [podrobnostech o cenách](https://azure.microsoft.com/pricing/details/cognitive-services/)rozhraní API.
    | **Skupina prostředků** | Skupina prostředků Azure, která bude obsahovat váš prostředek Cognitive Services. Můžete vytvořit novou skupinu nebo ji přidat do již existující skupiny. |

    ![Obrazovka pro vytvoření prostředku](media/cognitive-services-apis-create-account/resource_create_screen-multi.png)

    Klikněte na **Vytvořit**.

    #### <a name="single-service-resourcetabsingleservice"></a>[Prostředek s jednou službou](#tab/singleservice)

    |    |    |
    |--|--|
    | **Název** | Popisný název prostředku služby pro rozpoznávání. Například *TextAnalyticsResource*. |
    | **Předplatné** | Vyberte jedno z dostupných předplatných Azure. |
    | **Umístění** | Umístění instance služby vyvnímání. Různá umístění můžou způsobit latenci, ale nemají žádný vliv na dostupnost vašeho prostředku za běhu. Zapamatujte si umístění Azure, jak ho budete možná potřebovat při volání Azure Cognitive Services. |
    | **Cenová úroveň** | Náklady na účet Cognitive Services závisí na možnostech, které zvolíte, a na vašem využití. Další informace najdete v [podrobnostech o cenách](https://azure.microsoft.com/pricing/details/cognitive-services/)rozhraní API.
    | **Skupina prostředků** | Skupina prostředků Azure, která bude obsahovat váš prostředek Cognitive Services. Můžete vytvořit novou skupinu nebo ji přidat do již existující skupiny. |

    ![Obrazovka pro vytvoření prostředku](media/cognitive-services-apis-create-account/resource_create_screen.png)

    Klikněte na **Vytvořit**.

    ***

## <a name="get-the-keys-for-your-resource"></a>Získat klíče pro svůj prostředek

1. Po úspěšném nasazení prostředku klikněte v části **Další kroky**na **Přejít k prostředku** .

    ![Hledat Cognitive Services](media/cognitive-services-apis-create-account/resource-next-steps.png)

2. V otevřeném podokně pro rychlé zprovoznění máte přístup ke svému klíči a koncovému bodu.

    ![Získat klíč a koncový bod](media/cognitive-services-apis-create-account/get-cog-serv-keys.png)

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné Cognitive Services, můžete prostředek nebo skupinu prostředků odstranit. Odstraněním skupiny prostředků dojde také k odstranění všech dalších prostředků obsažených ve skupině.

1. Na webu Azure Portal rozbalením nabídky na levé straně otevřete nabídku služeb a zvolte **Skupiny prostředků**. Zobrazí se seznam skupin prostředků.
2. Vyhledejte skupinu prostředků obsahující prostředek, který chcete odstranit.
3. Klikněte pravým tlačítkem na výpis skupiny prostředků. Vyberte **Odstranit skupinu prostředků** a potvrďte tuto akci.

## <a name="see-also"></a>Další informace najdete v tématech

* [Ověřování požadavků do Azure Cognitive Services](authentication.md)
* [Co je Azure Cognitive Services?](Welcome.md)
* [Podpora přirozeného jazyka](language-support.md)
* [Podpora kontejneru Docker](cognitive-services-container-support.md)
