---
title: Vytvoření serveru služby Analysis Services v Azure | Microsoft Docs
description: Naučte se vytvořit instanci služby Analysis Services serveru v Azure.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: c49e886ee5b980e8fd059d72eb2e4a3f0dc895c4
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="create-an-analysis-services-server-in-azure-portal"></a>Vytvoření serveru služby Analysis Services v portálu Azure
Tento článek vás provede procesem vytvoření prostředek serveru služby Analysis Services ve vašem předplatném Azure.

Než začnete, musíte provést tyto akce: 

* **Předplatné Azure:** Pokud si chcete vytvořit účet, přejděte na stránku [Bezplatný zkušební verze Azure](https://azure.microsoft.com/offers/ms-azr-0044p/).
* **Azure Active Directory**: vaše předplatné musí být přidružen klienta služby Azure Active Directory. A, musíte být přihlášeni do Azure pomocí účtu v této službě Azure Active Directory. Další informace najdete v tématu [Ověřování a uživatelská oprávnění](analysis-services-manage-users.md).

## <a name="log-in-to-the-azure-portal"></a>Přihlášení k portálu Azure Portal 

Přihlaste se k portálu [Azure Portal](https://portal.azure.com).


## <a name="create-a-server"></a>Vytvoření serveru

1. Klikněte na tlačítko **+ vytvořit prostředek** > **Data + analýzy** > **služby Analysis Services**.

    ![Portál](./media/analysis-services-create-server/aas-create-server-portal.png)

2. V **služby Analysis Services**, vyplňte požadovaná pole a stiskněte klávesu **vytvořit**.
   
    ![Vytvoření serveru](./media/analysis-services-create-server/aas-create-server-blade.png)
   
   * **Název serveru**: Zadejte jedinečný název slouží k odkazování na server.
   * **Předplatné**: Vyberte předplatné, tento server bude přidružen.
   * **Skupina prostředků**: Vytvořte novou skupinu prostředků nebo vyberte jeden už máte. Skupiny prostředků slouží ke správě kolekce prostředků Azure. Další informace najdete v tématu [skupiny prostředků](../azure-resource-manager/resource-group-overview.md).
   * **Umístění**: Tento Azure datacenter umístění hostitelem serveru. Vyberte umístění pro nejbližší největší uživatelskou základnu.
   * **Cenová úroveň**: Vyberte cenovou úroveň. Pokud testujete a chcete instalace ukázkové databáze modelu, vyberte bezplatnou **D1** vrstvy. Další informace najdete v tématu [ceny služby Azure Analysis Services](https://azure.microsoft.com/pricing/details/analysis-services/). 
    * **Správce**: ve výchozím nastavení, bude jednat o účet, který jste se přihlásili. Jiný účet můžete ze služby Azure Active Directory.
    * **Nastavení úložiště zálohování**: volitelné. Pokud již máte [účet úložiště](../storage/common/storage-introduction.md), můžete je zadat jako výchozí pro model zálohování databáze. Můžete také zadat [zálohování a obnovení](analysis-services-backup.md) nastavení později.
    * **Vypršení platnosti klíče úložiště**: volitelné. Zadejte dobu vypršení platnosti klíče úložiště.
3. Klikněte na možnost **Vytvořit**.

Vytvoření trvá obvykle za minutu. Pokud jste vybrali **přidávat na portál**, přejděte na portál zobrazíte nový server. Nebo přejděte na **všechny služby** > **služby Analysis Services** chcete zobrazit, pokud váš server je připraven.

## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud již nepotřebujete, odstraňte váš server. Na vašem serveru **přehled**, klikněte na tlačítko **odstranit**. 

 ![Vyčištění](./media/analysis-services-create-server/aas-create-server-cleanup.png)


## <a name="next-steps"></a>Další postup

[Přidat datový model ukázka](analysis-services-create-sample-model.md) na váš server.  
[Nainstalujte bránu dat místní](analysis-services-gateway-install.md) Pokud datového modelu se připojí k místní datové zdroje.  
[Nasazení projektu tabulkový model](analysis-services-deploy.md) ze sady Visual Studio.   


