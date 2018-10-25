---
title: Rychlý start – Vytvoření serveru služby Analysis Services pomocí webu Azure Portal | Microsoft Docs
description: Zjistěte, jak v Azure vytvořit instanci serveru služby Analysis Services.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: quickstart
ms.date: 10/18/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: b5aa300d51deef408b8dd2b410db319449360329
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2018
ms.locfileid: "49428823"
---
# <a name="quickstart-create-a-server---portal"></a>Rychlý start: Vytvoření serveru – portál

Tento rychlý start popisuje, jak pomocí portálu vytvořit prostředek serveru služby Analysis Services v předplatném Azure.

## <a name="prerequisites"></a>Požadavky 

* **Předplatné Azure:** Pokud si chcete vytvořit účet, přejděte na stránku [Bezplatný zkušební verze Azure](https://azure.microsoft.com/offers/ms-azr-0044p/).
* **Azure Active Directory:** Vaše předplatné musí být přidružené k tenantovi Azure Active Directory. Zároveň musíte být přihlášeni k Azure pod účtem v této službě Azure Active Directory. Další informace najdete v tématu [Ověřování a uživatelská oprávnění](analysis-services-manage-users.md).

## <a name="log-in-to-the-azure-portal"></a>Přihlášení k portálu Azure Portal 

[Přihlášení k portálu](https://portal.azure.com)


## <a name="create-a-server"></a>Vytvoření serveru

1. Klikněte na **+ Vytvořit prostředek** > **Data a analýzy** > **Analysis Services**.

    ![Portál](./media/analysis-services-create-server/aas-create-server-portal.png)

2. V části **Analysis Services** vyplňte požadovaná pole a pak stiskněte **Vytvořit**.
   
   * **Název serveru:** Zadejte jedinečný název, který slouží jako odkaz na server.
   * **Předplatné:** Vyberte předplatné, ke kterému se tento server přidruží.
   * **Skupina prostředků:** Vytvořte novou skupinu prostředků nebo vyberte jednu z těch, které už máte. Skupiny prostředků jsou navržené tak, aby pomáhaly se správou kolekcí prostředků Azure. Další informace najdete v tématu věnovaném [skupinám prostředků](../azure-resource-manager/resource-group-overview.md).
   * **Umístění:** Toto umístění datacentra Azure je hostitelem serveru. Zvolte umístění co nejblíže vaší největší uživatelské základně.
   * **Cenová úroveň:** Vyberte cenovou úroveň. Pokud provádíte testování a chcete nainstalovat ukázkovou modelovou databázi, vyberte bezplatnou úroveň **D1**. Další informace najdete v tématu [Ceny služby Azure Analysis Services](https://azure.microsoft.com/pricing/details/analysis-services/). 
    * **Správce:** Toto bude ve výchozím nastavení účet, pod kterým jste přihlášeni. Můžete zvolit jiný účet z vaší služby Azure Active Directory.
    * **Nastavení úložiště zálohování:** Volitelné. Pokud už máte [účet úložiště](../storage/common/storage-introduction.md), můžete ho zadat jako výchozí účet úložiště pro zálohování modelové databáze. Později můžete zadat také nastavení [zálohování a obnovení](analysis-services-backup.md).
    * **Vypršení platnosti klíče úložiště:** Volitelné. Zadejte dobu platnosti klíče úložiště.

Vytvoření serveru obvykle trvá necelou minutu. Pokud jste vybrali možnost **Přidat na portál**, přejděte na portál a prohlédněte si nový server. Případně přejděte do části **Všechny služby** > **Analysis Services** a zkontrolujte, jestli je server připravený.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už server nepotřebujete, odstraňte ho. Na stránce **Přehled** vašeho serveru klikněte na **Odstranit**. 

 ![Vyčištění](./media/analysis-services-create-server/aas-create-server-cleanup.png)


## <a name="next-steps"></a>Další kroky
V tomto rychlém startu jste zjistili, jak vytvořit server v předplatném Azure. Když teď máte server, můžete ho zabezpečit nakonfigurováním (volitelné) brány firewall serveru. Na server také můžete přímo z portálu přidat základní ukázkový datový model. Na ukázkovém modelu se naučíte konfigurovat role modelové databáze a testovat připojení klientů. Ve výuce pokračujte kurzem, ve kterém přidáte ukázkový model.

> [!div class="nextstepaction"]
> [Rychlý start: Konfigurace brány firewall serveru – portál](analysis-services-qs-firewall.md)   
> [!div class="nextstepaction"]
> [Kurz: Přidání ukázkového modelu na server](analysis-services-create-sample-model.md)
