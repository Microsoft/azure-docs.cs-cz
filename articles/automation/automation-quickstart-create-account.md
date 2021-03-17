---
title: Rychlý start Azure – Vytvoření účtu Azure Automation | Dokumentace Microsoftu
description: Tento článek vám pomůže začít vytvářet účet Azure Automation a spouštět Runbook.
services: automation
ms.date: 04/04/2019
ms.topic: quickstart
ms.subservice: process-automation
ms.custom: mvc
ms.openlocfilehash: a07c6ac524aa213519ace1ae204ac2d76db802aa
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "83836698"
---
# <a name="create-an-azure-automation-account"></a>Vytvoření účtu Azure Automation

Účet Azure Automation můžete vytvořit prostřednictvím Azure pomocí Azure Portal, což je uživatelské rozhraní založené na prohlížeči, které umožňuje přístup k několika prostředkům. Jeden účet Automation může spravovat prostředky ve všech oblastech a předplatných pro daného tenanta. 

Tento rychlý Start vás provede vytvořením účtu Automation a spuštěním Runbooku v účtu. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

[Přihlaste se k Azure](https://portal.azure.com).

## <a name="create-automation-account"></a>Vytvoření účtu Automation

1. Vyberte název svého účtu Azure. Názvy účtů Automation jsou jedinečné pro jednotlivé oblasti a skupiny prostředků. Názvy účtů Automation, které byly odstraněny, nemusí být okamžitě k dispozici.

    > [!NOTE]
    > Po zadání v uživatelském rozhraní už název účtu nemůžete změnit. 

2. Klikněte na tlačítko **vytvořit prostředek** v levém horním rohu Azure Portal.

3. Vyberte **& nástroje pro správu**a pak vyberte **Automation**.

4. Zadejte informace o účtu, včetně vybraného názvu účtu. U možnosti **Vytvořit účet Spustit v Azure jako** zvolte **Ano**, aby se automaticky povolily artefakty, které zjednoduší ověřování do Azure. Po dokončení informací kliknutím na **vytvořit** spusťte nasazení účtu Automation.

    ![Zadání informací o účtu Automation na stránce](./media/automation-quickstart-create-account/create-automation-account-portal-blade.png)  

    > [!NOTE]
    > Aktualizovaný seznam umístění, do kterých můžete nasadit účet Automation, najdete v tématu [Dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/?products=automation&regions=all).

5. Po dokončení nasazení klikněte na **všechny služby**.

6. Vyberte **účty Automation** a potom vyberte účet Automation, který jste vytvořili.

    ![Přehled účtu Automation](./media/automation-quickstart-create-account/automation-account-overview.png)

## <a name="run-a-runbook"></a>Spuštění runbooku

Spusťte některý z runbooků pro tento kurz.

1. V části **Automatizace procesu**klikněte na **Runbooky** . Zobrazí se seznam runbooků. Ve výchozím nastavení jsou v účtu povoleny různé Runbooky kurzů.

    ![Seznam runbooků v účtu Automation](./media/automation-quickstart-create-account/automation-runbooks-overview.png)

1. Vyberte runbook **AzureAutomationTutorialScript**. Tato akce otevře stránku s přehledem runbooku.

    ![Přehled runbooku](./media/automation-quickstart-create-account/automation-tutorial-script-runbook-overview.png)

1. Klikněte na **Spustit**, na stránce Spustit Runbook klikněte na **OK** a spusťte runbook.

    ![Stránka úlohy runbooku](./media/automation-quickstart-create-account/automation-tutorial-script-job.png)

1. Jakmile se stav úlohy `Running` zobrazí, klikněte na **výstup** nebo **všechny protokoly** a zobrazte výstup úlohy Runbooku. V případě tohoto cvičného runbooku je výstupem seznam vašich prostředků Azure.

## <a name="next-steps"></a>Další kroky

V rámci tohoto rychlého startu jste nasadili účet Automation, spustili úlohu runbooku a zobrazili výsledky úlohy. Další informace o službě Azure Automation najdete v rychlém startu k vytvoření prvního runbooku.

> [!div class="nextstepaction"]
> [Rychlý Start pro automatizaci – vytvoření sady Runbook Azure Automation](./automation-quickstart-create-runbook.md)

