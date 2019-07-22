---
title: Vytvoření prostředku Cognitive Services v Azure Portal
titlesuffix: Azure Cognitive Services
description: Začněte s Azure Cognitive Services vytvořením a přihlášením k odběru prostředku v Azure Portal.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 07/16/2019
ms.author: aahi
ms.openlocfilehash: e9616f3014288e4b2580f474c49c646928db5a08
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/19/2019
ms.locfileid: "68334244"
---
# <a name="create-a-cognitive-services-resource-using-the-azure-portal"></a>Vytvoření prostředku Cognitive Services pomocí Azure Portal

V tomto rychlém startu můžete začít s Azure Cognitive Services pomocí Azure Portal. Cognitive Services jsou reprezentovány [prostředky](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal) Azure, které vytvoříte ve svém předplatném Azure. Po vytvoření prostředku použijte k ověření vašich aplikací klíče a koncový bod, který jste vygenerovali. 

## <a name="prerequisites"></a>Požadavky

* Platné předplatné Azure – [můžete ho vytvořit zdarma](https://azure.microsoft.com/free/) .

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="create-a-new-azure-cognitive-services-resource"></a>Vytvořit nový prostředek služby Azure Cognitive Services

Před vytvořením prostředku Cognitive Services musíte mít skupinu prostředků Azure, která má prostředek obsahovat. Při vytváření nového prostředku máte možnost vytvořit novou skupinu prostředků, nebo použít existující. V tomto článku se dozvíte, jak vytvořit novou skupinu prostředků.

1. Přihlaste se k [Azure Portal](https://portal.azure.com)a klikněte na **+ vytvořit prostředek**.

    ![Vyberte rozhraní API služeb Cognitive Services](media/cognitive-services-apis-create-account/azurePortalScreenMulti.png)

2. Dostupné Cognitive Services můžete najít následujícími způsoby:
    * Použijte panel hledání a zadejte název služby, ke které se chcete přihlásit.
        * Prostředek s více službami vytvoříte tak, že na panelu hledání zadáte **Cognitive Services** a vyberete prostředek **Cognitive Services** .

        ![Hledat Cognitive Services](media/cognitive-services-apis-create-account/azureCogServSearchMulti.png)

    * Pokud chcete zobrazit všechny dostupné služby pro rozpoznávání, vyberte v části **Azure Marketplace** **AI a Machine Learning**. Pokud nevidíte službu, kterou vás zajímá, klikněte na **Zobrazit vše** a posuňte se na **Cognitive Services**. Kliknutím na **Další** zobrazíte celý katalog rozhraní API služeb Cognitive Services.
    
        ![Vyberte rozhraní API služeb Cognitive Services](media/cognitive-services-apis-create-account/azureMarketplace.png)

3. Na stránce **vytvořit** zadejte následující informace:

    > [!IMPORTANT]
    > Zapamatujte si umístění Azure, jak ho budete možná potřebovat při volání Azure Cognitive Services.

    |    |    |
    |--|--|
    | **Název** | Popisný název prostředku služby pro rozpoznávání. Například *MyCognitiveServicesAccount*. |
    | **Předplatné** | Vyberte jedno z dostupných předplatných Azure. |
    | **Location** | Umístění instance služby vyvnímání. Různá umístění můžou způsobit latenci, ale nemají žádný vliv na dostupnost vašeho prostředku za běhu. |
    | **Cenová úroveň** | Náklady na účet Cognitive Services závisí na možnostech, které zvolíte, a na vašem využití. Další informace najdete v [podrobnostech o cenách](https://azure.microsoft.com/pricing/details/cognitive-services/)rozhraní API.
    | **Skupina prostředků** | [Skupina prostředků Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/governance/resource-consistency/azure-resource-access#what-is-an-azure-resource-group) , která bude obsahovat váš prostředek Cognitive Services. Můžete vytvořit novou skupinu nebo ji přidat do již existující skupiny. |

    ![Obrazovka pro vytvoření prostředku](media/cognitive-services-apis-create-account/resource_create_screen.png)


## <a name="get-the-keys-for-your-resource"></a>Získat klíče pro svůj prostředek

Po vytvoření prostředku můžete k němu získat přístup z řídicího panelu Azure, pokud jste ho připnuli. V opačném případě ji můžete najít ve **skupinách prostředků**. Po výběru prostředku můžete klíče získat výběrem **klíče** v části **Správa prostředků**.

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

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
