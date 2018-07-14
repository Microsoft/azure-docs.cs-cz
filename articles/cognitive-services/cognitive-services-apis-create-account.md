---
title: Na webu Azure Portal vytvořit účet rozhraní API služeb Cognitive Services | Dokumentace Microsoftu
description: Jak vytvořit účet Microsoft API služeb Cognitive Services na webu Azure Portal.
services: cognitive-services
documentationcenter: ''
author: garyericson
manager: cgronlun
editor: ''
ms.assetid: b6176bb2-3bb6-4ebf-84d1-3598ee6e01c6
ms.service: cognitive-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/01/2018
ms.author: garye
ms.reviewer: gibattag
ms.openlocfilehash: ed5f19b23375ecb83e19274c7405e9a1208a7985
ms.sourcegitcommit: 04fc1781fe897ed1c21765865b73f941287e222f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/13/2018
ms.locfileid: "39036148"
---
# <a name="create-a-cognitive-services-apis-account-in-the-azure-portal"></a>Na webu Azure Portal vytvořit účet rozhraní API služeb Cognitive Services

Pokud chcete používat rozhraní API služeb Cognitive Microsoftu, musíte nejprve vytvořit účet na webu Azure Portal.

1. Přihlaste se k [portálu Azure](http://portal.azure.com).

2. Klikněte na tlačítko **+ vytvořit prostředek**.

3. V části webu Azure Marketplace vyberte **AI a Cognitive Services** a seznam dostupných rozhraní API. Klikněte na **zobrazit všechny** zobrazíte úplný seznam sady rozhraní API služeb Cognitive Services. Klikněte na rozhraní API podle vašeho výběru, aby bylo možné pokračovat.

    ![Vyberte rozhraní API služeb Cognitive Services](media/cognitive-services-apis-create-account/select-cognitive-services-apis.png)

4. Na **vytvořit** stránky, zadejte následující informace:

   - **Název účtu:** název účtu. Doporučujeme použít popisný název, například *AFaceAPIAccount*.

   - **Předplatné:** vyberte jednu z dostupných předplatných Azure, ve kterých máte alespoň oprávnění přispěvatele.

   - **Typ rozhraní API:** zvolte Cognitive Services API, kterou chcete použít. Další informace o různých Cognitive Services API k dispozici, najdete [služeb Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) lokality.

   - **Cenová úroveň:** náklady na váš účet služeb Cognitive Services závisí na skutečném využití a možností, můžete zvolit. Další informace o cenách pro každé rozhraní API najdete [stránkách s cenami](https://azure.microsoft.com/pricing/details/cognitive-services/).

   - **Skupina prostředků:** skupina prostředků je kolekce prostředků, které sdílejí stejný životní cyklus, oprávnění a zásady. Další informace o skupinách prostředků najdete v tématu [Správa prostředků Azure prostřednictvím portálu](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal).

   - **Umístění skupiny prostředků:** to je nutné pouze v případě, že je rozhraní API vybraná globální (bez vazby na umístění). Pokud rozhraní API je globální a není vázaná na místo, ale musíte zadat umístění pro skupinu prostředků, ve kterém se budou metadata spojená s účtem rozhraní API služeb Cognitive Services nachází. Toto umístění nemá žádný vliv na běhovou dostupnost vašeho účtu. Další informace o skupině prostředků najdete v tématu [Správa prostředků Azure prostřednictvím portálu](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal).

   - **Výslovné potvrzení Online podmínek použití služby:** Chcete-li vytvořit účet, předplatné vlastníky nebo přispěvateli (podle definice [řízení přístupu](https://docs.microsoft.com/azure/role-based-access-control/overview)) nutné explicitně potvrdit podmínky, které použít na služby Cognitive Services v [Online podmínek použití služby](https://www.microsoft.com/en-us/Licensing/product-licensing/products.aspx). 

     Vlastník předplatného můžete zakázat vytvoření účtu služeb Cognitive Services pro konkrétní skupině prostředků nebo předplatného prostřednictvím [zásady Azure](../azure-policy/azure-policy-introduction.md) podle článku [pomocí Azure portal a přiřazování a správu zásady prostředků](../azure-policy/assign-policy-definition.md) a přiřazení definice zásady "není povolené typy prostředků" a určení **Microsoft.CognitiveServices/accounts** jako typ cílového prostředku.

     Když vytvoření účtu byla zakázaná, by v době vytvoření účtu zobrazit následující chyba:

     ![Chyba při vytváření účtu](media/cognitive-services-apis-create-account/error-message.png)

5. Chcete-li připnout účet na řídicím panelu portálu Azure, klikněte na tlačítko **připnout na řídicí panel**.

6. Kliknutím na **Vytvořit** vytvořte účet.

Po úspěšném nasazení účtu služeb Cognitive Services, klikněte na dlaždici na řídicím panelu zobrazíte informace o účtu.

Můžete použít **adresu URL koncového bodu** v **přehled** oddílu a klíče **klíče** části a začněte s vytvářením rozhraní API volá ve svých aplikacích.

![Zobrazit informace o účtu](media/cognitive-services-apis-create-account/display-account.png)

![Zobrazení klíče účtu](media/cognitive-services-apis-create-account/account-keys.png)

### <a name="next-steps"></a>Další kroky

Další informace o všech Microsoft Cognitive Services k dispozici, najdete v části [služeb Cognitive Services](https://azure.microsoft.com/services/cognitive-services/).

Pro příručky rychlý úvod k používání některé ukázkové rozhraní API služeb Cognitive Services:

 - [Rychlé starty počítače pro zpracování obrazu C#](computer-vision/quickstarts/csharp.md)
 - [Analýza textu s využitím Pythonu](text-analytics/quickstarts/python.md)
 - [Rozhraní face API s použitím jazyka JavaScript](face/quickstarts/javascript.md)
