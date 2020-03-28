---
title: Používání aplikací v Azure Blockchain Workbench
description: Návod, jak používat kontrakty aplikací ve verzi Azure Blockchain Workbench Preview.
ms.date: 10/14/2019
ms.topic: tutorial
ms.reviewer: brendal
ms.openlocfilehash: 5761bf1294691c2d50e0e389fe69ec286df4a06c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "74324046"
---
# <a name="tutorial-using-applications-in-azure-blockchain-workbench"></a>Kurz: Používání aplikací v Azure Blockchain Workbench

Blockchain Workbench můžete použít k vytváření a akcím na smlouvách. Můžete také zobrazit podrobnosti smlouvy, jako je stav a historie transakcí.

Dozvíte se, jak provést tyto akce:

> [!div class="checklist"]
> * Vytvoření nové smlouvy
> * Provést akci na základě smlouvy

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

* Nasazení blockchainworkbench. Další informace najdete v [tématu Nasazení Azure Blockchain Workbench](deploy.md) podrobnosti o nasazení
* Nasazená blockchainová aplikace v Blockchain Workbench. Viz [Vytvoření blockchainové aplikace v Azure Blockchain Workbench](create-app.md)

[Otevřete blockchainworkbench](deploy.md#blockchain-workbench-web-url) ve svém prohlížeči.

![Blockchain Workbench](./media/use/workbench.png)

Musíte se přihlásit jako člen Blockchain Workbench. Pokud nejsou uvedeny žádné aplikace, jste členem Blockchain Workbench, ale ne jste členem žádné aplikace. Správce Blockchain Workbench může přiřadit členy k aplikacím.

## <a name="create-new-contract"></a>Vytvořit novou smlouvu

Chcete-li vytvořit novou smlouvu, musíte být členem určeným jako **iniciátor**smlouvy . Informace definující role aplikace a iniciátory smlouvy naleznete [v tématu pracovní postupy v přehledu konfigurace](configuration.md#workflows). Informace o přiřazení členů k rolím aplikace naleznete [v tématu Přidání člena do aplikace](manage-users.md#add-member-to-application).

1. V části aplikace Blockchain Workbench vyberte dlaždici aplikace, která obsahuje smlouvu, kterou chcete vytvořit. Zobrazí se seznam aktivních smluv.

2. Chcete-li vytvořit novou smlouvu, vyberte **možnost Nová smlouva**.

    ![Tlačítko Nové smlouvy](./media/use/contract-list.png)

3. Zobrazí se podokno **Nový kontrakt.** Zadejte hodnoty počátečních parametrů. Vyberte **Vytvořit**.

    ![Nové podokno smluv](./media/use/new-contract.png)

    Nově vytvořená smlouva je zobrazena v seznamu s ostatními aktivními smlouvami.

    ![Seznam aktivních smluv](./media/use/active-contracts.png)

## <a name="take-action-on-contract"></a>Přijmout opatření na zakázku

V závislosti na stavu smlouvy, členové mohou provést kroky k přechodu na další stav smlouvy. Akce jsou [definovány](configuration.md#transitions) jako přechody v rámci [stavu](configuration.md#states). Členové, kteří patří k povolené roli aplikace nebo instance pro přechod, mohou provést akci. 

1. V části aplikace Blockchain Workbench vyberte dlaždici aplikace, která obsahuje smlouvu, aby se akce.
2. Vyberte smlouvu v seznamu. Podrobnosti o smlouvě jsou zobrazeny v různých sekcích. 

    ![Podrobnosti o smlouvě](./media/use/contract-details.png)

    | Sekce  | Popis  |
    |---------|---------|
    | Status | Uvádí aktuální pokrok ve fázích smlouvy. |
    | Podrobnosti | Aktuální hodnoty smlouvy |
    | Akce | Podrobnosti o poslední akci |
    | Aktivita | Historie transakcí smlouvy |
    
3. V části **Akce** vyberte **Přijmout akci**.

4. Podrobnosti o aktuálním stavu smlouvy jsou zobrazeny v podokně. V rozevíracím souboru zvolte akci, kterou chcete provést. 

    ![Zvolit akci](./media/use/choose-action.png)

5. Chcete-li akci zahájit, vyberte **provést akci.**
6. Pokud jsou pro akci požadovány parametry, zadejte hodnoty akce.

    ![Přijmout opatření](./media/use/take-action.png)

7. Vyberte **Provést akci** k provedení akce.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Správa verzí aplikací Azure Blockchain Workbench](version-app.md)
