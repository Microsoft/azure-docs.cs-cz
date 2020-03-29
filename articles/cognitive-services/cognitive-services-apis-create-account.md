---
title: Vytvoření prostředku služeb Cognitive Services na webu Azure Portal
titleSuffix: Azure Cognitive Services
description: S Azure Cognitive Services můžete začít tím, že na webu Azure Portal vytvoříte a přihlásíte se k odběru prostředků.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: aahi
ms.openlocfilehash: dd4444bf42bcc8dda95f8fa37b42a365538efa85
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219475"
---
# <a name="create-a-cognitive-services-resource-using-the-azure-portal"></a>Vytvoření prostředku služeb Cognitive Services pomocí portálu Azure

Tento rychlý start můžete začít používat služby Azure Cognitive Services. Po vytvoření prostředku kognitivní služby na webu Azure Portal získáte koncový bod a klíč pro ověřování vašich aplikací.


[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="prerequisites"></a>Požadavky

* Platné předplatné Azure – [vytvořte si ho zdarma](https://azure.microsoft.com/free/).

## <a name="create-a-new-azure-cognitive-services-resource"></a>Vytvoření nového prostředku Azure Cognitive Services

1. Vytvořit prostředek.

    #### <a name="multi-service-resource"></a>[Prostředek pro více služeb](#tab/multiservice)
    
    Prostředek s více službami se nazývá **Cognitive Services** na portálu. [Vytvořte prostředek služeb Cognitive Services](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne).
    
    V tuto chvíli prostředek více služeb umožňuje přístup k následujícím službám Cognitive Services:
    
    |                  |                                                      |                    |                               |                  |
    |------------------|------------------------------------------------------|--------------------|-------------------------------|------------------|
    | Počítačové zpracování obrazu  | Content Moderator                                    | Tvář               | Language Understanding (LUIS) | Analýza textu   |
    | Translator Text  | Hledání Bingu v7 <br>(Web, Obrázek, Zprávy, Video, Vizuální) | Vlastní vyhledávání Bingu | Vyhledávání entit Bingu            | Automatické návrhy Bingu |
    | Kontrola pravopisu Bingu |                                                      |                    |                               |                  |
    
    #### <a name="single-service-resource"></a>[Prostředek jedné služby](#tab/singleservice)

    Pomocí následujících odkazů vytvořte zdroj pro dostupné služby Cognitive Services:

    | Obraz                      | Řeč                  | Jazyk                          | Rozhodnutí             | Search                 |
    |-----------------------------|-------------------------|-----------------------------------|----------------------|------------------------|
    | [Počítačové vidění](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision)         | [Hlasové služby](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices)     | [Pohlcující čtečka](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesImmersiveReader)              | [Detektor anomálií](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector) | [Rozhraní API pro vyhledávání Bingu V7](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7) |
    | [Vlastní vize služby](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesCustomVision) | [Rozpoznávání mluvčího](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeakerRecognition) | [Language Understanding (LUIS)](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) | [Content Moderator](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator) | [Vlastní vyhledávání Bingu](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingCustomSearch) |
    | [Tvář](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFace)                    |                         | [QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)                     | [Personalizace](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer)     | [Vyhledávání entit Bingu](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingEntitySearch) |
    | [Rozpoznávání rukopisu](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesInkRecognizer)        |                         | [Analýza textu](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)                |                      | [Kontrola pravopisu Bingu](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingSpellCheck-v7)   |
    |           |                         | [Translator Text](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation)               |                      | [Automatické návrhy Bingu](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingAutosuggest-v7)                       |
    ***

3. Na stránce **Vytvořit** zadejte následující informace:

    #### <a name="multi-service-resource"></a>[Prostředek pro více služeb](#tab/multiservice)

    |    |    |
    |--|--|
    | **Název** | Popisný název pro prostředek kognitivních služeb. Například *MyCognitiveServicesResource*. |
    | **Předplatné** | Vyberte jedno z dostupných předplatných Azure. |
    | **Umístění** | Umístění instance kognitivní služby. Různá umístění mohou zavést latenci, ale nemají žádný vliv na dostupnost za běhu vašeho prostředku. |
    | **Cenová úroveň** | Náklady na váš účet služeb Cognitive Services závisí na možnostech, které zvolíte, a na vašem využití. Další informace naleznete v [podrobnostech o cenách](https://azure.microsoft.com/pricing/details/cognitive-services/)rozhraní API .
    | **Skupina prostředků** | Skupina prostředků Azure, která bude obsahovat prostředek služeb Cognitive Services. Můžete vytvořit novou skupinu nebo ji přidat do již existující skupiny. |

    ![Obrazovka pro vytvoření zdroje](media/cognitive-services-apis-create-account/resource_create_screen-multi.png)

    Klikněte na **Vytvořit**.

    #### <a name="single-service-resource"></a>[Prostředek jedné služby](#tab/singleservice)

    |    |    |
    |--|--|
    | **Název** | Popisný název pro prostředek kognitivních služeb. Například *TextAnalyticsResource*. |
    | **Předplatné** | Vyberte jedno z dostupných předplatných Azure. |
    | **Umístění** | Umístění instance kognitivní služby. Různá umístění mohou zavést latenci, ale nemají žádný vliv na dostupnost za běhu vašeho prostředku. |
    | **Cenová úroveň** | Náklady na váš účet služeb Cognitive Services závisí na možnostech, které zvolíte, a na vašem využití. Další informace naleznete v [podrobnostech o cenách](https://azure.microsoft.com/pricing/details/cognitive-services/)rozhraní API .
    | **Skupina prostředků** | Skupina prostředků Azure, která bude obsahovat prostředek služeb Cognitive Services. Můžete vytvořit novou skupinu nebo ji přidat do již existující skupiny. |

    ![Obrazovka pro vytvoření zdroje](media/cognitive-services-apis-create-account/resource_create_screen.png)

    Klikněte na **Vytvořit**.

    ***


## <a name="get-the-keys-for-your-resource"></a>Získání klíčů pro váš zdroj

1. Po úspěšném nasazení prostředku klikněte v části **Další kroky**na **Přejít na prostředek** .

    ![Hledání služeb Cognitive Services](media/cognitive-services-apis-create-account/resource-next-steps.png)

2. Z podokna rychlého startu, které se otevře, můžete získat přístup ke klíči a koncovému bodu.

    ![Získání klíče a koncového bodu](media/cognitive-services-apis-create-account/get-cog-serv-keys.png)

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné služeb Cognitive Services, můžete odstranit prostředek nebo skupinu prostředků. Odstraněním skupiny prostředků se odstraní také všechny ostatní prostředky obsažené ve skupině.

1. Na webu Azure Portal rozbalením nabídky na levé straně otevřete nabídku služeb a zvolte **Skupiny prostředků**. Zobrazí se seznam skupin prostředků.
2. Vyhledejte skupinu prostředků obsahující prostředek, který má být odstraněn.
3. Klikněte pravým tlačítkem myši na výpis skupiny prostředků. Vyberte **Odstranit skupinu prostředků** a potvrďte tuto akci.

## <a name="see-also"></a>Viz také

* [Ověřování požadavků na služby Azure Cognitive Services](authentication.md)
* [Co je Azure Cognitive Services?](Welcome.md)
* [Podpora přirozeného jazyka](language-support.md)
* [Podpora kontejnerů Dockeru](cognitive-services-container-support.md)
