---
title: Vytvoření serveru služby Analysis Services v Azure | Microsoft Docs
description: Naučte se vytvořit instanci služby Analysis Services serveru v Azure.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: c910416524f149c785aae299d576ca5c521abc6d
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="create-an-azure-analysis-services-server-in-azure-portal"></a>Vytvoření serveru Azure Analysis Services na portálu Azure
Tento článek vás provede procesem vytvoření prostředek serveru služby Analysis Services ve vašem předplatném Azure.

## <a name="before-you-begin"></a>Než začnete
K dokončení tohoto rychlého startu je potřeba:

* **Předplatné Azure:** Pokud si chcete vytvořit účet, přejděte na stránku [Bezplatný zkušební verze Azure](https://azure.microsoft.com/offers/ms-azr-0044p/).
* **Azure Active Directory**: vaše předplatné musí být přidružen klienta služby Azure Active Directory. A, musíte být přihlášeni do Azure pomocí účtu v této službě Azure Active Directory. Účty Microsoft nejsou podporovány. Další informace najdete v tématu [Ověřování a uživatelská oprávnění](analysis-services-manage-users.md).
* **Skupina prostředků**: už máte skupinu prostředků nebo [vytvořte novou](../azure-resource-manager/resource-group-overview.md).

> [!NOTE]
> Vytvoření serveru může znamenat, že se vám začne fakturovat nová služba. Další informace najdete v tématu [Ceny služby Azure Analysis Services](https://azure.microsoft.com/pricing/details/analysis-services/).
> 
> 

## <a name="to-create-a-server-in-the-azure-portal"></a>Vytvoření serveru na portálu Azure
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).  
2. Klikněte na tlačítko **+ nový** > **Data + analýzy** > **služby Analysis Services**.
3. V **služby Analysis Services** okně vyplňte požadovaná pole a stiskněte klávesu **vytvořit**.
   
    ![Vytvoření serveru](./media/analysis-services-create-server/aas-create-server-blade.png)
   
   * **Název serveru**: Zadejte jedinečný název slouží k odkazování na server.
   * **Předplatné**: Vyberte předplatné, tento server bills k.
   * **Skupina prostředků**: Tyto kontejnery slouží ke správě kolekce prostředků Azure. Další informace najdete v tématu [skupiny prostředků](../azure-resource-manager/resource-group-overview.md).
   * **Umístění**: Tento Azure datacenter umístění hostitelem serveru. Vyberte umístění pro nejbližší největší uživatelskou základnu.
   * **Cenová úroveň**: Vyberte cenovou úroveň. Tabulkové modely až 400 GB jsou podporovány. Další informace najdete v tématu [ceny služby Azure Analysis Services](https://azure.microsoft.com/pricing/details/analysis-services/).
4. Klikněte na možnost **Vytvořit**.

Vytvoření trvá obvykle pod minutu; často jenom pár sekund. Pokud jste vybrali **přidávat na portál**, přejděte na portál zobrazíte nový server. Nebo přejděte na **všechny služby** > **služby Analysis Services** chcete zobrazit, pokud váš server je připraven.

 ![Řídicí panel](./media/analysis-services-create-server/aas-create-server-dashboard.png)


## <a name="next-steps"></a>Další postup
Po vytvoření serveru, můžete [nasadit model](analysis-services-deploy.md) k němu pomocí rozšíření SSDT nebo pomocí SSMS.

Pokud model nasadit na server se připojí k místním zdrojům dat, je potřeba nainstalovat [místní brána dat](analysis-services-gateway.md) na počítači v síti.

