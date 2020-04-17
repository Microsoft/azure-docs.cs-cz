---
title: Rychlý start Azure – Vytvoření účtu Azure Automation | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit účet Azure Automation a spustit runbook.
services: automation
ms.date: 04/04/2019
ms.topic: quickstart
ms.subservice: process-automation
ms.custom: mvc
ms.openlocfilehash: 7704f080b7c1878f2fa2b079a1f242c8c2cc87a9
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536977"
---
# <a name="create-an-azure-automation-account"></a>Vytvoření účtu Azure Automation

Účet Azure Automation můžete vytvořit prostřednictvím Azure pomocí portálu Azure, uživatelského rozhraní založeného na prohlížeči, které umožňuje přístup k řadě prostředků. Jeden účet automatizace můžete spravovat prostředky ve všech oblastech a předplatná pro daného klienta. 

Tento rychlý start vás provede vytvořením účtu Automation a spuštěním runbooku v účtu. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

[Přihlaste se k Azure](https://portal.azure.com).

## <a name="create-automation-account"></a>Vytvoření účtu Automation

1. Zvolte název svého účtu Azure. Názvy účtů automatizace jsou jedinečné pro oblast a skupinu prostředků. Názvy účtů automatizace, které byly odstraněny, nemusí být okamžitě k dispozici.

    > [!NOTE]
    > Po zadání do uživatelského rozhraní nelze název účtu změnit. 

2. Klikněte na tlačítko **Vytvořit prostředek,** které se nachází v levém horním rohu portálu Azure.

3. Vyberte **nástroje pro správu it &** a pak vyberte **automatizace**.

4. Zadejte informace o účtu včetně vybraného názvu účtu. U možnosti **Vytvořit účet Spustit v Azure jako** zvolte **Ano**, aby se automaticky povolily artefakty, které zjednoduší ověřování do Azure. Po dokončení informací klikněte na **vytvořit** a spusťte nasazení účtu automatizace.

    ![Zadání informací o účtu Automation na stránce](./media/automation-quickstart-create-account/create-automation-account-portal-blade.png)  

    > [!NOTE]
    > Aktualizovaný seznam umístění, do kterých můžete nasadit účet automatizace, naleznete v [tématu Produkty dostupné podle oblasti](https://azure.microsoft.com/global-infrastructure/services/?products=automation&regions=all).

5. Po dokončení nasazení klepněte na položku **Všechny služby**.

6. Vyberte **Účty automatizace** a pak zvolte účet automatizace, který jste vytvořili.

    ![Přehled účtu Automation](./media/automation-quickstart-create-account/automation-account-overview.png)

## <a name="run-a-runbook"></a>Spuštění runbooku

Spusťte některý z runbooků pro tento kurz.

1. V části **Automatizace procesů**klepněte na **položku Runbook** . Zobrazí se seznam runbooků. Ve výchozím nastavení je v účtu povoleno několik knih runbooků.

    ![Seznam runbooků v účtu Automation](./media/automation-quickstart-create-account/automation-runbooks-overview.png)

1. Vyberte runbook **AzureAutomationTutorialScript**. Tato akce otevře stránku s přehledem runbooku.

    ![Přehled runbooku](./media/automation-quickstart-create-account/automation-tutorial-script-runbook-overview.png)

1. Klikněte na **Spustit**, na stránce Spustit Runbook klikněte na **OK** a spusťte runbook.

    ![Stránka úlohy runbooku](./media/automation-quickstart-create-account/automation-tutorial-script-job.png)

1. Po stavu úlohy klikněte `Running`na **Výstup** nebo Všechny protokoly a zobrazte výstup **úlohy** runbooku. V případě tohoto cvičného runbooku je výstupem seznam vašich prostředků Azure.

## <a name="next-steps"></a>Další kroky

V rámci tohoto rychlého startu jste nasadili účet Automation, spustili úlohu runbooku a zobrazili výsledky úlohy. Další informace o službě Azure Automation najdete v rychlém startu k vytvoření prvního runbooku.

> [!div class="nextstepaction"]
> [Rychlý start se službou Automation – Vytvoření runbooku](./automation-quickstart-create-runbook.md)

