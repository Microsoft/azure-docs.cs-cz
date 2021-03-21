---
title: Vytvoření prostředku Cognitive Services v Azure Portal
titleSuffix: Azure Cognitive Services
description: Začněte s Azure Cognitive Services vytvořením a přihlášením k odběru prostředku v Azure Portal.
services: cognitive-services
author: aahill
manager: nitinme
keywords: služby pro rozpoznávání, rozpoznávání, rozpoznávání, rozpoznávání, služby AI
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 03/15/2021
ms.author: aahi
ms.openlocfilehash: 115457180efab719b406c4e1e021234fa99736ab
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104670496"
---
# <a name="quickstart-create-a-cognitive-services-resource-using-the-azure-portal"></a>Rychlý Start: vytvoření prostředku Cognitive Services pomocí Azure Portal

V tomto rychlém startu můžete začít používat Azure Cognitive Services. Po vytvoření prostředku služby vnímání v Azure Portal získáte koncový bod a klíč pro ověřování vašich aplikací.

Azure Cognitive Services jsou cloudové služby s rozhraními REST API a sady SDK pro klientské knihovny, které vývojářům umožňují sestavovat rozpoznávání informací do aplikací, aniž by museli mít přímý přístup k datům (AI) nebo dovednosti v oblasti datových věd. Azure Cognitive Services umožňuje vývojářům snadno přidat funkce rozpoznávání do svých aplikací s rozpoznáváním řešení, která mohou vidět, slyšet, mluvit, pochopit a dokonce i začít.

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="prerequisites"></a>Předpoklady

* Platné předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/cognitive-services/).

## <a name="create-a-new-azure-cognitive-services-resource"></a>Vytvořit nový prostředek služby Azure Cognitive Services

1. Vytvořit prostředek.

### <a name="multi-service-resource"></a>[Prostředek s více službami](#tab/multiservice)

Prostředek s více službami se jmenuje **Cognitive Services** na portálu. [Vytvořte prostředek Cognitive Services](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne).

Prostředek s více službami v současnosti umožňuje přístup k následujícím Cognitive Services:

* **Vision** – Počítačové zpracování obrazu, Custom Vision, rozpoznávání formulářů, tvář
* **Řeč** – řeč
* **Language** -Language UNDERSTANDING (Luis), analýza textu, Translator
* **Rozhodnutí** – přizpůsobení Content moderator

### <a name="single-service-resource"></a>[Prostředek s jednou službou](#tab/singleservice)

K vytvoření prostředku pro dostupné Cognitive Services použijte odkazy níže:

| Obraz                      | Řeč                  | Jazyk                          | Rozhodnutí             |
|-----------------------------|-------------------------|-----------------------------------|----------------------|
| [Počítačové zpracování obrazu](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision)         | [Služby Speech](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices)     | [Moderní čtečka](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesImmersiveReader)              | [Detektor anomálií](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector) | 
| [Služba Custom Vision Service](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesCustomVision) |  | [Language Understanding (LUIS)](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) | [Content Moderator](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator) | 
| [Rozpoznávání tváře](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFace)                    |                         | [QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)                     | [Personalizace](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer)     |
| [Rozpoznávání formulářů](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer)        |                         | [Analýza textu](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)                |  [Metrics Advisor](https://go.microsoft.com/fwlink/?linkid=2142156)                    |
| | | [Translator](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation) | |

---

2. Na stránce **vytvořit** zadejte následující informace:
<!-- markdownlint-disable MD024 -->

### <a name="multi-service-resource"></a>[Prostředek s více službami](#tab/multiservice)

|Podrobnosti o projektu| Popis   |
|--|--|
| **Předplatné** | Vyberte jedno z dostupných předplatných Azure. |
| **Skupina prostředků** | Skupina prostředků Azure, která bude obsahovat váš prostředek Cognitive Services. Můžete vytvořit novou skupinu nebo ji přidat do již existující skupiny. |
| **Oblast** | Umístění instance služby vyvnímání. Různá umístění můžou způsobit latenci, ale nemají žádný vliv na dostupnost vašeho prostředku za běhu. |
| **Název** | Popisný název prostředku služby pro rozpoznávání. Například *MyCognitiveServicesResource*. |
| **Cenová úroveň** | Náklady na účet Cognitive Services závisí na možnostech, které zvolíte, a na vašem využití. Další informace najdete v [podrobnostech o cenách](https://azure.microsoft.com/pricing/details/cognitive-services/)rozhraní API.

<!--![Multi-service resource creation screen](media/cognitive-services-apis-create-account/resource_create_screen-multi.png)-->
:::image type="content" source="media/cognitive-services-apis-create-account/resource_create_screen-multi.png" alt-text="Obrazovka pro vytváření prostředků s více službami":::

Přečtěte si a přijměte podmínky (podle potřeby) a pak vyberte **zkontrolovat + vytvořit**.

### <a name="single-service-resource"></a>[Prostředek s jednou službou](#tab/singleservice)

|Podrobnosti o projektu| Popis   |
|--|--|
| **Předplatné** | Vyberte jedno z dostupných předplatných Azure. |
| **Skupina prostředků** | Skupina prostředků Azure, která bude obsahovat váš prostředek Cognitive Services. Můžete vytvořit novou skupinu nebo ji přidat do již existující skupiny. |
| **Oblast** | Umístění instance služby vyvnímání. Různá umístění můžou způsobit latenci, ale nemají žádný vliv na dostupnost vašeho prostředku za běhu. |
| **Název** | Popisný název prostředku služby pro rozpoznávání. Například *MyCognitiveServicesResource*. |
| **Cenová úroveň** | Náklady na účet Cognitive Services závisí na možnostech, které zvolíte, a na vašem využití. Další informace najdete v [podrobnostech o cenách](https://azure.microsoft.com/pricing/details/cognitive-services/)rozhraní API.

<!--![Single-service resource creation screen](media/cognitive-services-apis-create-account/resource_create_screen.png)-->
:::image type="content" source="media/cognitive-services-apis-create-account/resource_create_screen.png" alt-text="Obrazovka pro vytváření prostředků s jednou službou":::

Vyberte **Další: Virtual Network** a zvolte typ síťového přístupu, který chcete pro svůj prostředek použít, a pak vyberte **zkontrolovat + vytvořit**.

---

[!INCLUDE [Register Azure resource for subscription](./includes/register-resource-subscription.md)]

## <a name="get-the-keys-for-your-resource"></a>Získat klíče pro svůj prostředek

1. Po úspěšném nasazení prostředku klikněte v části **Další kroky** na **Přejít k prostředku** .

    ![Hledat Cognitive Services](media/cognitive-services-apis-create-account/resource-next-steps.png)

2. V otevřeném podokně pro rychlé zprovoznění máte přístup ke svému klíči a koncovému bodu.

    ![Získat klíč a koncový bod](media/cognitive-services-apis-create-account/get-cog-serv-keys.png)

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné Cognitive Services, můžete prostředek nebo skupinu prostředků odstranit. Odstraněním skupiny prostředků dojde také k odstranění všech dalších prostředků obsažených ve skupině.

1. Na webu Azure Portal rozbalením nabídky na levé straně otevřete nabídku služeb a zvolte **Skupiny prostředků**. Zobrazí se seznam skupin prostředků.
2. Vyhledejte skupinu prostředků obsahující prostředek, který chcete odstranit.
3. Klikněte pravým tlačítkem na výpis skupiny prostředků. Vyberte **Odstranit skupinu prostředků** a potvrďte tuto akci.

## <a name="see-also"></a>Viz také

* [Ověřování požadavků do Azure Cognitive Services](authentication.md)
* [Co je Azure Cognitive Services?](./what-are-cognitive-services.md)
* [Vytvoření nového prostředku pomocí klientské knihovny pro správu Azure](.\cognitive-services-apis-create-account-client-library.md)
* [Podpora přirozeného jazyka](language-support.md)
* [Podpora kontejneru Docker](cognitive-services-container-support.md)
