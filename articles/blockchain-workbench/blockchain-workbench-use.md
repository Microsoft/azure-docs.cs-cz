---
title: Použití aplikace v Azure Blockchain Workbench
description: Smlouvy o tom, jak použít aplikaci v Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/16/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: b15d4980875b805b49f7dc1cdb941e607232f3cb
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2018
ms.locfileid: "34258035"
---
# <a name="using-applications-in-azure-blockchain-workbench"></a>Použití aplikace v Azure Blockchain Workbench

Můžete vytvořit a provést akce v kontraktech Blockchain Workbench. Můžete také zobrazit podrobnosti, například historii stavu a transakce smlouvy.

## <a name="prerequisites"></a>Požadavky

* Nasazení Blockchain Workbench. Další informace najdete v tématu [nasazení Azure Blockchain Workbench](blockchain-workbench-deploy.md) podrobnosti o nasazení
* Blockchain nasazené aplikace v Blockchain Workbench. V tématu [vytvoření blockchain aplikace v Azure Blockchain Workbench]()

[Otevřete Blockchain Workbench](blockchain-workbench-deploy.md#blockchain-workbench-web-url) v prohlížeči.

![Blockchain Workbench](media/blockchain-workbench-use/workbench.png)

Musíte se přihlásit jako člen skupiny Blockchain Workbench. Pokud nejsou uvedené žádné aplikace, jste členem Blockchain Workbench, ale není členem žádné aplikace. Správce Blockchain Workbench můžete přiřadit členy aplikace.

## <a name="create-new-contract"></a>Vytvoření nové smlouvy. 

Pokud chcete vytvořit nové smlouvy, musíte být členem zadané jako kontrakt **iniciátor**. Definování aplikační role a iniciátory pro kontrakt informace najdete v tématu [pracovních postupů v Přehled konfigurace](blockchain-workbench-configuration-overview.md#workflows). Informace o přiřazování členy aplikačních rolí najdete v tématu [přidat člena do aplikace](blockchain-workbench-manage-users.md#add-member-to-application).

1. V oddílu aplikací Blockchain Workbench vyberte dlaždici aplikace, která obsahuje smlouvu, kterou chcete vytvořit. Zobrazí se seznam aktivních smluv.

2. Chcete-li vytvořit nové smlouvy, vyberte **nové smlouvy**.

    ![Tlačítko Nový kontraktu](media/blockchain-workbench-use/contract-list.png)

3. **Nové smlouvy** podokně se zobrazí. Zadejte parametry počáteční hodnoty. Vyberte **Vytvořit**.

    ![Nové podokno kontraktu](media/blockchain-workbench-use/new-contract.png)

    Nově vytvořený kontrakt se zobrazí v seznamu s aktivních smluv.

    ![Seznam aktivních smluv](media/blockchain-workbench-use/active-contracts.png)

## <a name="take-action-on-contract"></a>Provést akci pro kontraktu

V závislosti na stavu je kontrakt, členy jde provádět akce pro přechod na další stav kontraktu. Akce jsou definovány jako [přechody](blockchain-workbench-configuration-overview.md#transitions) v rámci [stavu](blockchain-workbench-configuration-overview.md#states). Členové, které patří do povolené aplikace nebo instanci role pro přechod může trvat akce. 

1. V oddílu aplikací Blockchain Workbench vyberte dlaždici aplikace, která obsahuje kontrakt provést akci.
2. V seznamu vyberte kontrakt. Podrobnosti o smlouvě se zobrazují v různých oddílů. 

    ![Podrobnosti o kontraktu](media/blockchain-workbench-use/contract-details.png)

    | Sekce  | Popis  |
    |---------|---------|
    | Status | Zobrazí aktuální průběh v rámci fáze kontraktu |
    | Podrobnosti | Aktuální hodnoty kontraktu |
    | Akce | Podrobnosti o poslední akci. |
    | Aktivita | Historie transakce kontraktu |
    
3. V **akce** vyberte **akce**.

4. V podokně se zobrazí podrobnosti o aktuálním stavu kontrakt. Vyberte akci, kterou budete chtít využít v rozevíracím seznamu. 

    ![Vyberte akci](media/blockchain-workbench-use/choose-action.png)

5. Vyberte **akce** zahájíte akci.
6. Pokud jsou vyžadovány pro akci parametry, zadejte hodnoty pro akci.

    ![Provést akci](media/blockchain-workbench-use/take-action.png)

7. Vyberte **akce** k provedení akce.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Řešení potíží s Azure Blockchain Workbench](blockchain-workbench-troubleshooting.md)