---
title: Rychlý start Azure – Vytvoření účtu Azure Automation | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit účet Azure Automation a spustit runbook.
services: automation
ms.date: 04/04/2019
ms.topic: quickstart
ms.subservice: process-automation
ms.custom: mvc
ms.openlocfilehash: 7704f080b7c1878f2fa2b079a1f242c8c2cc87a9
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "81536977"
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

1. Jakmile se `Running`stav úlohy zobrazí, klikněte na **výstup** nebo **všechny protokoly** a zobrazte výstup úlohy Runbooku. V případě tohoto cvičného runbooku je výstupem seznam vašich prostředků Azure.

## <a name="next-steps"></a>Další kroky

V rámci tohoto rychlého startu jste nasadili účet Automation, spustili úlohu runbooku a zobrazili výsledky úlohy. Další informace o službě Azure Automation najdete v rychlém startu k vytvoření prvního runbooku.

> [!div class="nextstepaction"]
> [Rychlý start se službou Automation – Vytvoření runbooku](./automation-quickstart-create-runbook.md)

