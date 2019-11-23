---
title: Create a Cognitive Services resource in the Azure portal
titleSuffix: Azure Cognitive Services
description: Get started with Azure Cognitive Services by creating and subscribing to a resource in the Azure portal.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: aahi
ms.openlocfilehash: d2555ed61697e102e9f481f1e4521afd1480da3d
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74326660"
---
# <a name="create-a-cognitive-services-resource-using-the-azure-portal"></a>Create a Cognitive Services resource using the Azure portal

Use this quickstart to start using Azure Cognitive Services. After creating a Cognitive Service resource in the Azure portal, you'll get an endpoint and a key for authenticating your applications.


[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="prerequisites"></a>Předpoklady

* A valid Azure subscription - [Create one for free](https://azure.microsoft.com/free/).

## <a name="create-a-new-azure-cognitive-services-resource"></a>Create a new Azure Cognitive Services resource

1. Create a resource.

    #### <a name="multi-service-resourcetabmultiservice"></a>[Multi-service resource](#tab/multiservice)
    
    The multi-service resource is named **Cognitive Services** in the portal. [Create a Cognitive Services resource](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne).
    
    At this time, the multi-service resource enables access to the following Cognitive Services:
    
    |                  |                                                      |                    |                               |                  |
    |------------------|------------------------------------------------------|--------------------|-------------------------------|------------------|
    | Computer Vision  | Content Moderator                                    | Tvář               | Language Understanding (LUIS) | Analýza textu   |
    | Translator Text  | Bing Search v7 <br>(Web, Image, News, Video, Visual) | Vlastní vyhledávání Bingu | Vyhledávání entit Bingu            | Automatické návrhy Bingu |
    | Kontrola pravopisu Bingu |                                                      |                    |                               |                  |
    
    #### <a name="single-service-resourcetabsingleservice"></a>[Single-service resource](#tab/singleservice)

    Use the below links to create a resource for the available Cognitive Services:

    | Vidění                      | Řeč                  | Jazyk                          | Rozhodnutí             | Hledat                 |
    |-----------------------------|-------------------------|-----------------------------------|----------------------|------------------------|
    | [Počítačové zpracování obrazu](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision)         | [Hlasové služby](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices)     | [Immersive reader](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesImmersiveReader)              | [Anomaly Detector](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector) | [Bing Search API V7](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7) |
    | [Custom vision service](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesCustomVision) | [Speaker Recognition](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeakerRecognition) | [Language Understanding (LUIS)](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) | [Content Moderator](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator) | [Vlastní vyhledávání Bingu](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingCustomSearch) |
    | [Face](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFace)                    |                         | [QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)                     | [Personalizer](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer)     | [Vyhledávání entit Bingu](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingEntitySearch) |
    | [Ink Recognizer](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesInkRecognizer)        |                         | [Analýza textu](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)                |                      | [Kontrola pravopisu Bingu](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingSpellCheck-v7)   |
    |           |                         | [Translator Text](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation)               |                      | [Automatické návrhy Bingu](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingAutosuggest-v7)                       |
    ***

3. On the **Create** page, provide the following information:

    #### <a name="multi-service-resourcetabmultiservice"></a>[Multi-service resource](#tab/multiservice)

    |    |    |
    |--|--|
    | **Název** | A descriptive name for your cognitive services resource. For example, *MyCognitiveServicesResource*. |
    | **Předplatné** | Select one of your available Azure subscriptions. |
    | **Umístění** | The location of your cognitive service instance. Different locations may introduce latency, but have no impact on the runtime availability of your resource. |
    | **Cenová úroveň** | The cost of your Cognitive Services account depends on the options you choose and your usage. For more information, see the API [pricing details](https://azure.microsoft.com/pricing/details/cognitive-services/).
    | **Skupina prostředků** | The Azure resource group that will contain your Cognitive Services resource. You can create a new group or add it to a pre-existing group. |

    ![Resource creation screen](media/cognitive-services-apis-create-account/resource_create_screen-multi.png)

    Klikněte na **Vytvořit**.

    #### <a name="single-service-resourcetabsingleservice"></a>[Single-service resource](#tab/singleservice)

    |    |    |
    |--|--|
    | **Název** | A descriptive name for your cognitive services resource. For example, *TextAnalyticsResource*. |
    | **Předplatné** | Select one of your available Azure subscriptions. |
    | **Umístění** | The location of your cognitive service instance. Different locations may introduce latency, but have no impact on the runtime availability of your resource. |
    | **Cenová úroveň** | The cost of your Cognitive Services account depends on the options you choose and your usage. For more information, see the API [pricing details](https://azure.microsoft.com/pricing/details/cognitive-services/).
    | **Skupina prostředků** | The Azure resource group that will contain your Cognitive Services resource. You can create a new group or add it to a pre-existing group. |

    ![Resource creation screen](media/cognitive-services-apis-create-account/resource_create_screen.png)

    Klikněte na **Vytvořit**.

    ***

## <a name="get-the-keys-for-your-resource"></a>Get the keys for your resource

1. After your resource is successfully deployed, click on **Go to resource** under **Next Steps**.

    ![Search for Cognitive Services](media/cognitive-services-apis-create-account/resource-next-steps.png)

2. From the quickstart pane that opens, you can access your key and endpoint.

    ![Get key and endpoint](media/cognitive-services-apis-create-account/get-cog-serv-keys.png)

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

If you want to clean up and remove a Cognitive Services subscription, you can delete the resource or resource group. Deleting the resource group also deletes any other resources contained in the group.

1. Na webu Azure Portal rozbalením nabídky na levé straně otevřete nabídku služeb a zvolte **Skupiny prostředků**. Zobrazí se seznam skupin prostředků.
2. Locate the resource group containing the resource to be deleted
3. Right-click on the resource group listing. Vyberte **Odstranit skupinu prostředků** a potvrďte tuto akci.

## <a name="see-also"></a>Další informace najdete v tématech

* [Authenticate requests to Azure Cognitive Services](authentication.md)
* [What is Azure Cognitive Services?](Welcome.md)
* [Natural language support](language-support.md)
* [Docker container support](cognitive-services-container-support.md)
