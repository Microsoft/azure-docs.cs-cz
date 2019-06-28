---
title: Vytvoření účtu služeb Cognitive Services na webu Azure Portal
titlesuffix: Azure Cognitive Services
description: Jak vytvořit účet Azure API služeb Cognitive Services na webu Azure Portal.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: aahi
ms.openlocfilehash: b857ee0395c447c8699b8f6a812853528812a7bd
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445856"
---
# <a name="create-a-cognitive-services-account-using-the-azure-portal"></a>Vytvoření účtu služeb Cognitive Services pomocí webu Azure portal

V tomto rychlém startu se budete dozvíte, jak se zaregistrovat pro Azure Cognitive Services a vytvořit účet, který má předplatné služby jednoho nebo víc služeb. Tyto služby jsou reprezentované prostřednictvím Azure [prostředky](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal), které umožňují připojení k jedné či více rozhraní API kognitivních služeb Azure.

## <a name="prerequisites"></a>Požadavky

* Platné předplatné Azure. [Vytvoření účtu](https://azure.microsoft.com/free/) zdarma.

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="create-a-new-azure-cognitive-services-resource"></a>Vytvořit nový prostředek služby Azure Cognitive Services

Před vytvořením prostředku, musí mít skupinu prostředků Azure. Každý účet služeb Cognitive Services (a jeho přidružený prostředek Azure), musí patřit do skupiny prostředků Azure. Když vytvoříte účet, máte možnost vytvořit novou skupinu prostředků, nebo použijte již existující. Tento článek ukazuje, jak vytvořit novou skupinu prostředků.

1. Přihlaste se k [webu Azure portal](https://portal.azure.com)a klikněte na tlačítko **+ vytvořit prostředek**.

    ![Vyberte rozhraní API služeb Cognitive Services](media/cognitive-services-apis-create-account/azurePortalScreenMulti.png)

2. Můžete najít dostupné služeb Cognitive Services s následujícími způsoby:
    * Pomocí panelu hledání a zadejte název služby, které se chcete přihlásit k odběru.
        * Chcete-li vytvořit prostředek pro víc služeb předplatné, zadejte **služeb Cognitive Services** ve službě search panelu a vyberte **služeb Cognitive Services** prostředků.

        ![Vyhledejte Cognitive Services](media/cognitive-services-apis-create-account/azureCogServSearchMulti.png)

    * Pokud chcete zobrazit všechny dostupné služby cognitive services, vyberte **AI a strojové učení**v části **Azure Marketplace**. Pokud nevidíte službu, která vás zajímá, klikněte na **zobrazit všechny** a přejděte k položce **služeb Cognitive Services**. Klikněte na tlačítko **Další** Chcete-li zobrazit celý katalog rozhraní API služeb Cognitive Services.
    
        ![Vyberte rozhraní API služeb Cognitive Services](media/cognitive-services-apis-create-account/azureMarketplace.png)

3. Na **vytvořit** stránky, zadejte následující informace:

    > [!IMPORTANT]
    > Nezapomeňte vaše oblast Azure, možná ji budete potřebovat při volání služeb Azure Cognitive Services.

    |    |    |
    |--|--|
    | **Název** | Popisný název pro váš prostředek služeb cognitive services. Doporučujeme použít popisný název, například *MyCognitiveServicesAccount*. |
    | **Předplatné** | Vyberte jednu z dostupných předplatných Azure. |
    | **Location** | Umístění vaší instanci služby cognitive Services. Různá umístění mohou zavést latence, ale mít vliv na běhovou dostupnost vašeho prostředku. |
    | **Cenová úroveň** | Náklady na váš účet služeb Cognitive Services závisí na zvolené možnosti a využití. Další informace najdete v tématu rozhraní API [podrobnosti o cenách](https://azure.microsoft.com/pricing/details/cognitive-services/).
    | **Skupina prostředků** | [Skupiny prostředků Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/governance/resource-consistency/azure-resource-access#what-is-an-azure-resource-group) , která bude obsahovat váš prostředek služeb Cognitive Services. Můžete vytvořit novou skupinu nebo ho přidat do existující skupiny. |

    ![Vytvoření obrazovky prostředků](media/cognitive-services-apis-create-account/resource_create_screen.png)


## <a name="get-the-keys-for-your-subscription"></a>Získání klíčů předplatného

Po vytvoření prostředku můžete přistupovat ji z řídicího panelu Azure Pokud je připnutý. V opačném případě se nachází v **skupiny prostředků**. Po výběru prostředku, můžete získat klíče výběrem **klíče** pod **správy prostředků**.

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné služeb Cognitive Services, můžete odstranit prostředek nebo skupinu prostředků. Odstraněním skupiny prostředků se odstraní také všechny další prostředky spojené s vybranou skupinou prostředků.

Odebrání skupiny prostředků pomocí webu Azure Portal:

1. Na webu Azure Portal rozbalením nabídky na levé straně otevřete nabídku služeb a zvolte **Skupiny prostředků**. Zobrazí se seznam skupin prostředků.
2. Vyhledejte skupinu prostředků, kterou chcete odstranit a klikněte pravým tlačítkem na tlačítko Další (...) na pravé straně seznamu.
3. Vyberte **Odstranit skupinu prostředků** a potvrďte tuto akci.

## <a name="see-also"></a>Další informace najdete v tématech

* [Ověření požadavků ve službě Azure Cognitive Services](authentication.md)
* [Co jsou Azure Cognitive Services?](Welcome.md)
* [Podpora přirozeného jazyka](language-support.md)
* [Podpora kontejnerů dockeru](cognitive-services-container-support.md)
