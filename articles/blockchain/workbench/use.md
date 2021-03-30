---
title: Používání aplikací v Azure blockchain Workbench
description: Kurz týkající se použití kontraktů aplikací v Azure blockchain Workbench Preview
ms.date: 10/14/2019
ms.topic: tutorial
ms.reviewer: brendal
ms.openlocfilehash: 5761bf1294691c2d50e0e389fe69ec286df4a06c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "74324046"
---
# <a name="tutorial-using-applications-in-azure-blockchain-workbench"></a>Kurz: používání aplikací v Azure blockchain Workbench

Blockchain Workbench můžete použít k vytvoření a provedení akcí ve smlouvách. Můžete také zobrazit podrobnosti o kontraktu, například stav a historii transakcí.

Dozvíte se, jak provést tyto akce:

> [!div class="checklist"]
> * Vytvořit nový kontrakt
> * Provedení akce u kontraktu

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

* Nasazení aplikace blockchain Workbench Další informace najdete v tématu [nasazení Azure blockchain Workbench](deploy.md) pro podrobnosti o nasazení.
* Nasazená aplikace blockchain v blockchain Workbench. Další informace najdete [v tématu Vytvoření aplikace v blockchain v Azure blockchain Workbench](create-app.md) .

[Otevřete aplikaci blockchain Workbench](deploy.md#blockchain-workbench-web-url) v prohlížeči.

![Blockchain Workbench](./media/use/workbench.png)

Musíte se přihlásit jako člen aplikace blockchain Workbench. Pokud nejsou uvedené žádné aplikace, jste členem aplikace blockchain Workbench, ale nejste členem žádné aplikace. Správce blockchain Workbench může přiřadit členy k aplikacím.

## <a name="create-new-contract"></a>Vytvořit nový kontrakt

Pokud chcete vytvořit nový kontrakt, musíte být členem, který jste zadali jako **iniciátor** kontraktu. Informace, které definují aplikační role a iniciátory pro kontrakt, najdete [v tématu pracovní postupy v tématu Přehled konfigurace](configuration.md#workflows). Informace o přiřazování členů k aplikačním rolím najdete v tématu [Přidání člena do aplikace](manage-users.md#add-member-to-application).

1. V části aplikace blockchain Workbench vyberte dlaždici aplikace obsahující kontrakt, který chcete vytvořit. Zobrazí se seznam aktivních smluv.

2. Chcete-li vytvořit novou smlouvu, vyberte možnost **Nový kontrakt**.

    ![Tlačítko Nová smlouva](./media/use/contract-list.png)

3. Zobrazí se podokno **nové smlouvy** . Zadejte hodnoty počátečních parametrů. Vyberte **Vytvořit**.

    ![Nové podokno smlouvy](./media/use/new-contract.png)

    Nově vytvořená smlouva se zobrazí v seznamu s ostatními aktivními kontrakty.

    ![Seznam aktivních smluv](./media/use/active-contracts.png)

## <a name="take-action-on-contract"></a>Provést akci se smlouvou

V závislosti na stavu, ve kterém se kontrakt nachází, můžou členové provést akce přechodu do dalšího stavu smlouvy. Akce jsou definovány jako [přechody](configuration.md#transitions) ve [stavu](configuration.md#states). Akce mohou provést členové patřící do povolené role aplikace nebo instance pro přechod. 

1. V části aplikace blockchain Workbench vyberte dlaždici aplikace, která obsahuje kontrakt pro provedení akce.
2. Vyberte kontrakt v seznamu. Podrobnosti o kontraktu se zobrazují v různých oddílech. 

    ![Podrobnosti o kontraktu](./media/use/contract-details.png)

    | Sekce  | Popis  |
    |---------|---------|
    | Status | Zobrazí aktuální průběh v rámci fází smlouvy. |
    | Podrobnosti | Aktuální hodnoty kontraktu |
    | Akce | Podrobnosti o poslední akci |
    | Aktivita | Historie transakcí kontraktu |
    
3. V části **Akce** vyberte **provést akci**.

4. Podrobnosti o aktuálním stavu smlouvy se zobrazí v podokně. V rozevíracím seznamu vyberte akci, kterou chcete provést. 

    ![Zvolit akci](./media/use/choose-action.png)

5. Chcete-li zahájit akci, vyberte **provést akci** .
6. Pokud jsou pro akci vyžadovány parametry, zadejte hodnoty pro akci.

    ![Provést akci](./media/use/take-action.png)

7. Vyberte **provést akci** a akci spusťte.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Správa verzí aplikací v Azure blockchain Workbench](version-app.md)
