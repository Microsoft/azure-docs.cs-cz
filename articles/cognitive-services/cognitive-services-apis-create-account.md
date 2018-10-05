---
title: Na webu Azure Portal vytvořit účet rozhraní API služeb Cognitive Services
titlesuffix: Azure Cognitive Services
description: Jak vytvořit účet Microsoft API služeb Cognitive Services na webu Azure Portal.
services: cognitive-services
author: garyericson
manager: cgronlun
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 02/01/2018
ms.author: garye
ms.openlocfilehash: 9c4aea2493ffed12b90f82113baf81c6c77a0ea2
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2018
ms.locfileid: "48801399"
---
# <a name="quickstart-create-a-cognitive-services-account-in-the-azure-portal"></a>Rychlý start: Vytvoření účtu služeb Cognitive Services na webu Azure Portal

Pokud chcete začít používat Azure Cognitive Services použijte v tomto rychlém startu. Tyto služby jsou reprezentované prostřednictvím Azure [prostředky](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal), které umožňují připojení k nejméně jeden mnoho API služeb Cognitive Services k dispozici.

## <a name="prerequisites"></a>Požadavky

* Platné předplatné Azure. je možné [vytvořit účet](https://azure.microsoft.com/free/) zdarma.

## <a name="create-and-subscribe-to-an-azure-cognitive-services-resource"></a>Vytvoření a přihlášení k odběru k prostředku Azure Cognitive Services

1. Přihlaste se k [webu Azure portal](http://portal.azure.com)a klikněte na tlačítko **+ vytvořit prostředek**.
    
    ![Vyberte rozhraní API služeb Cognitive Services](media/cognitive-services-apis-create-account/azurePortalScreen.png)

2. V části webu Azure Marketplace vyberte **AI a strojové učení**. Pokud nevidíte službu, která vás zajímá, klikněte na **zobrazit všechny** Chcete-li zobrazit celý katalog rozhraní API služeb Cognitive Services.

    ![Vyberte rozhraní API služeb Cognitive Services](media/cognitive-services-apis-create-account/azureMarketplace.png)

3. Na **vytvořit** stránky, zadejte následující informace:

    |    |    |
    |--|--|
    | **Název** | Popisný název pro váš prostředek služeb cognitive services. Doporučujeme použít popisný název, například *MyNameFaceAPIAccount*. |
    | **Předplatné** | Vyberte jednu z dostupných předplatných Azure. |
    | **Umístění** | Umístění vaší instanci služby cognitive Services. Různá umístění mohou zavést latence, ale mít vliv na běhovou dostupnost vašeho prostředku. |
    | **Cenová úroveň** | Náklady na váš účet služeb Cognitive Services závisí na zvolené možnosti a využití. Další informace najdete v tématu rozhraní API [podrobnosti o cenách](https://azure.microsoft.com/pricing/details/cognitive-services/).
    | **Skupina prostředků** | [Skupiny prostředků Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/azure-resource-access#what-is-an-azure-resource-group) , která bude obsahovat váš prostředek služeb Cognitive Services. Můžete vytvořit novou skupinu nebo ho přidat do stávající skupiny. |

    ![Vytvoření obrazovky prostředků](media/cognitive-services-apis-create-account/resource_create_screen.png)

## <a name="access-your-resource"></a>Přístup k prostředku 

> [!NOTE]
> Vlastníci předplatného můžete zakázat vytváření účtů služeb Cognitive Services pro skupiny prostředků a předplatná použitím [Azure policy](https://docs.microsoft.com/en-us/azure/governance/policy/overview#policy-definition)přiřazení definice zásady "není povolené typy prostředků" a určení **Microsoft.CognitiveServices/accounts** jako typ cílového prostředku.

Po vytvoření prostředku můžete přistupovat ji z řídicího panelu Azure Pokud je připnutý. V opačném případě se nachází v **skupiny prostředků**.

V prostředku služeb Cognitive Services, můžete použít adresu URL koncového bodu a klíče **přehled** části a začněte s vytvářením rozhraní API volá ve svých aplikacích.

![Obrazovka prostředky](media/cognitive-services-apis-create-account/resourceScreen.png)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Počítač Vision API C# – tutoriál](https://docs.microsoft.com/azure/cognitive-services/computer-vision/tutorials/csharptutorial)

## <a name="see-also"></a>Další informace najdete v tématech

* [Rychlý start: Extrahujte rukou psaný text z obrázku](https://docs.microsoft.com/azure/cognitive-services/computer-vision/quickstarts/csharp-hand-text)
* [Kurz: Vytvoření aplikace pro zjišťování a rámečku tváří v obrázku](https://docs.microsoft.com/azure/cognitive-services/Face/Tutorials/FaceAPIinCSharpTutorial)
* [Vytvořit webovou stránku pro vlastní vyhledávání](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/tutorials/custom-search-web-page)
* [Integrace s využitím botu pomocí rozhraní Bot Framework Language Understanding (LUIS)](https://docs.microsoft.com/azure/cognitive-services/luis/luis-nodejs-tutorial-build-bot-framework-sample)
