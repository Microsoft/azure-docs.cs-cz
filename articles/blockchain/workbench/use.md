---
title: Používání aplikací v Azure Blockchain Workbench
description: Smlouvy o tom, jak používat aplikace v aplikaci Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 10/1/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 4fe6f164882ffce7bf22ec0c0b94107abcf6a20e
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2018
ms.locfileid: "48242405"
---
# <a name="using-applications-in-azure-blockchain-workbench"></a>Používání aplikací v Azure Blockchain Workbench

Blockchain Workbench můžete vytvářet a provádět s nimi akce smluv. Můžete také zobrazit podrobnosti, jako je historie stavu a transakce smlouvy.

## <a name="prerequisites"></a>Požadavky

* Blockchain Workbench umožňuje nasazení. Další informace najdete v tématu [Azure Blockchain Workbench umožňuje nasazení](deploy.md) podrobnosti o nasazení
* Nasazené blockchainové aplikace v Blockchain Workbench. Zobrazit [vytvoření blockchainové aplikace v Azure Blockchain Workbench](create-app.md)

[Otevřete Blockchain Workbench](deploy.md#blockchain-workbench-web-url) v prohlížeči.

![Blockchain Workbench](./media/use/workbench.png)

Musíte se přihlásit jako člen Blockchain Workbench. Pokud nejsou žádné aplikace, uvedená, jste členem Blockchain Workbench, ale není členem žádné aplikace. Správce Blockchain Workbench můžete přiřadit členy do aplikací.

## <a name="create-new-contract"></a>Vytvořit nové smlouvy. 

Pokud chcete vytvořit nové smlouvy, musíte být členem zadané jako kontrakt **iniciátoru**. Definování rolí aplikace a iniciátory pro kontrakt informace najdete v tématu [pracovních postupů v přehledu konfigurace](configuration.md#workflows). Informace o přiřazování členů do aplikačních rolí, najdete v části [přidat člena do aplikace](manage-users.md#add-member-to-application).

1. V části aplikace Blockchain Workbench vyberte dlaždici aplikace, která obsahuje smlouvu, kterou chcete vytvořit. Zobrazí seznam aktivních smluv.

2. Chcete-li vytvořit nové smlouvy, vyberte **nové smlouvy**.

    ![Tlačítko Nová smlouva](./media/use/contract-list.png)

3. **Nové smlouvy** zobrazí podokno. Zadejte parametry počáteční hodnoty. Vyberte **Vytvořit**.

    ![Nové podokno kontraktu](./media/use/new-contract.png)

    Nově vytvořený kontraktu se zobrazí v seznamu s aktivní smluv.

    ![Seznam aktivních smluv](./media/use/active-contracts.png)

## <a name="take-action-on-contract"></a>Provést akci u kontraktu

V závislosti na stavu je kontrakt, členové mohli podniknout kroky k přechodu do dalšího stavu kontraktu. Akce jsou definované jako [přechody](configuration.md#transitions) v rámci [stavu](configuration.md#states). Členy, které patří do povolených aplikací nebo instanci role pro přechod může trvat akce. 

1. V oddílu aplikace Blockchain Workbench vyberte dlaždici aplikace, který obsahuje kontrakt provést akci.
2. V seznamu vyberte kontrakt. Podrobnosti o smlouvě se zobrazují v různých oddílů. 

    ![Podrobnosti o kontraktu](./media/use/contract-details.png)

    | Sekce  | Popis  |
    |---------|---------|
    | Status | Uvádí aktuální průběh v rámci fáze kontraktu |
    | Podrobnosti | Aktuální hodnoty kontraktu |
    | Akce | Podrobnosti o poslední akci. |
    | Aktivita | Historie transakcí kontraktu |
    
3. V **akce** vyberte **provést akci**.

4. V podokně se zobrazí podrobnosti o aktuálním stavu kontraktu. Zvolte akci, kterou budete chtít využít v rozevíracím seznamu. 

    ![Vybrat akci](./media/use/choose-action.png)

5. Vyberte **provést akci** zahájit akci.
6. Pokud jsou vyžadovány pro akci parametry, zadejte hodnoty pro akci.

    ![Provést akci](./media/use/take-action.png)

7. Vyberte **provést akci** k provedení akce.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Řešení potíží s Azure Blockchain Workbench](troubleshooting.md)
