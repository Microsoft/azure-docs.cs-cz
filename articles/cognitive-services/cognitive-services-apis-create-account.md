---
title: Vytvoření účtu kognitivní rozhraní API služby na portálu Azure | Microsoft Docs
description: Postup vytvoření účtu Microsoft kognitivní rozhraní API služby na portálu Azure.
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
ms.openlocfilehash: 3697dd0628f0028cb486139e92c032f0d757c6ed
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342802"
---
# <a name="create-a-cognitive-services-apis-account-in-the-azure-portal"></a>Vytvoření účtu kognitivní rozhraní API služby na portálu Azure

Pokud chcete používat Microsoft kognitivní rozhraní API služby, musíte nejprve vytvořit účet na portálu Azure.

1. Přihlaste se k [portálu Azure](http://portal.azure.com).

2. Klikněte na tlačítko **+ vytvořit prostředek**.

3. V části Azure Marketplace, vyberte **AI + kognitivní služby** a zjistit seznam dostupných rozhraní API. Klikněte na **zobrazit všechny** celý seznam kognitivní rozhraní API služby. Klikněte na rozhraní API podle vaší volby, aby bylo možné pokračovat.

    ![Vyberte kognitivní služby rozhraní API](media/cognitive-services-apis-create-account/select-cognitive-services-apis.png)

4. Na **vytvořit** stránky, zadejte následující informace:

   - **Název účtu:** název účtu. Doporučujeme použít popisný název, například *AFaceAPIAccount*.

   - **Předplatné:** vyberte jednu z dostupných předplatných Azure, ve kterých je mít minimálně oprávnění přispěvatele.

   - **Typ rozhraní API:** zvolte kognitivní Services API, kterou chcete použít. Další informace o různých kognitivní služby API k dispozici, najdete [kognitivní služby](https://azure.microsoft.com/services/cognitive-services/) lokality.

   - **Cenová úroveň:** náklady na váš účet kognitivní Services závisí na skutečném využití a možnosti, které zvolíte. Další informace o cenách pro každé rozhraní API, najdete v části [ceny stránky](https://azure.microsoft.com/pricing/details/cognitive-services/).

   - **Skupina prostředků:** skupina prostředků je kolekce prostředků, které sdílejí stejný životní cyklus, oprávnění a zásady. Další informace o skupinách prostředků najdete v tématu [Azure spravovat prostředky prostřednictvím portálu](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal).

   - **Umístění skupiny prostředků:** to je potřeba, pouze pokud je rozhraní API vybraná globální (bez vazby umístění). Pokud rozhraní API je globální a není vázaná na umístění, ale musíte zadat umístění pro skupinu prostředků, které se budou metadata spojená s účtem kognitivní rozhraní API služby nachází. Toto umístění nemá žádný vliv na běhovou dostupnost vašeho účtu. Další informace o skupině prostředků najdete v tématu [Azure spravovat prostředky prostřednictvím portálu](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal).

   - **Výslovné potvrzení podmínek Online služby:** Chcete-li vytvořit účet, vlastníky předplatného nebo přispěvatele (podle definice [řízení přístupu](https://docs.microsoft.com/azure/role-based-access-control/overview)) potřeba explicitně potvrdit podmínky, platí pro kognitivní služby [Online podmínek služby](https://www.microsoft.com/en-us/Licensing/product-licensing/products.aspx). 

     Vlastník předplatného můžete zakázat vytváření účtu kognitivní Services pro určité skupiny zdrojů nebo předplatné přes [Azure zásady](../azure-policy/azure-policy-introduction.md) podle článku [portálu Azure pomocí přiřadit a spravovat zásady prostředků](../azure-policy/assign-policy-definition.md) a přiřazení definici zásady "typy prostředků nepovoluje" a zadání **Microsoft.CognitiveServices/accounts** jako cílového typu prostředku.

     Pokud byla zakázaná vytváření účtů, bude zobrazen k následující chybě při vytváření účtu:

     ![Chyba při vytváření účtu](media/cognitive-services-apis-create-account/error-message.png)

5. Chcete-li připnout účet řídicí panel portálu Azure, klikněte na tlačítko **připnout na řídicí panel**.

6. Kliknutím na **Vytvořit** vytvořte účet.

Po účtu kognitivní Services úspěšně nasazení, klikněte na dlaždici na řídicím panelu, chcete-li zobrazit informace o účtu.

Můžete použít **adresu URL koncového bodu** v **přehled** části a klíče v **klíče** části zahájíte vytváření rozhraní API volá ve svých aplikacích.

![Zobrazit informace o účtu](media/cognitive-services-apis-create-account/display-account.png)

![Klíče účtu zobrazení](media/cognitive-services-apis-create-account/account-keys.png)

### <a name="next-steps"></a>Další kroky

Další informace o všech kognitivní službách Microsoftu k dispozici, najdete v části [kognitivní služby](https://azure.microsoft.com/services/cognitive-services/).

Pro rychlý start příručky k používání některé ukázkové kognitivní rozhraní API služby:

 - [Počítač vize C# – elementy QuickStart](/computer-vision/quickstarts/csharp.md)
 - [Analýza textu s Pythonem](/text-analytics/quickstarts/python.md)
 - [Vzhled rozhraní API pomocí jazyka JavaScript](/face/quickstarts/javascript.md)
