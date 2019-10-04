---
title: Rychlý Start Azure – vytvoření účtu Azure Automation | Microsoft Docs
description: Naučte se vytvořit účet Azure Automation a spustit Runbook.
services: automation
author: csand-msft
ms.author: csand
ms.date: 04/04/2019
ms.topic: quickstart
ms.service: automation
ms.subservice: process-automation
ms.custom: mvc
ms.openlocfilehash: 80a175aad3c692a5dfd04c7399211c58134c5164
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2019
ms.locfileid: "71937167"
---
# <a name="create-an-azure-automation-account"></a>Vytvoření účtu Azure Automation

Účty Azure Automation se dají vytvářet pomocí Azure. Tato metoda poskytuje uživatelské rozhraní založené na prohlížeči pro vytváření a konfiguraci účtů a souvisejících prostředků služby Automation. Tento rychlý Start prochází kroky vytvoření účtu Automation a spuštění Runbooku v účtu.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="sign-in-to-azure"></a>Přihlaste se k Azure

Přihlaste se k Azure na adrese https://portal.azure.com

## <a name="create-automation-account"></a>Vytvořit účet Automation

1. Klikněte na tlačítko **vytvořit prostředek** , které najdete v levém horním rohu Azure.

1. Vyberte **& nástroje pro správu**a pak vyberte **Automation**.

1. Zadejte informace o účtu. U možnosti **vytvořit účet Spustit v Azure jako**vyberte **Ano** , aby se automaticky povolily artefakty, které zjednodušují ověřování v Azure. Je důležité si uvědomit, že při vytváření účtu Automation po jeho výběru se název nedá změnit. *Názvy účtů Automation jsou jedinečné pro jednotlivé oblasti a skupiny prostředků. Názvy účtů Automation, které byly odstraněny, nemusí být okamžitě k dispozici.* Jeden účet Automation může spravovat prostředky ve všech oblastech a předplatných pro daného tenanta. Až budete hotovi, kliknutím na **vytvořit**spusťte nasazení účtu Automation.

    ![Zadání informací o účtu Automation na stránce](./media/automation-quickstart-create-account/create-automation-account-portal-blade.png)  

    > [!NOTE]
    > Aktualizovaný seznam umístění, do kterých můžete nasadit účet Automation, abyste viděli, [Dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/?products=automation&regions=all).

1. Až se nasazení dokončí, klikněte na **všechny služby**, vyberte **účty Automation** a vyberte účet Automation, který jste vytvořili.

    ![Přehled účtu Automation](./media/automation-quickstart-create-account/automation-account-overview.png)

## <a name="run-a-runbook"></a>Spuštění runbooku

Spusťte jednu z kurzů Runbook.

1. V části **Automatizace procesu**klikněte na **Runbooky** . Zobrazí se seznam runbooků. Ve výchozím nastavení jsou v účtu povoleny některé Runbooky kurzů.

    ![Seznam runbooků pro účet Automation](./media/automation-quickstart-create-account/automation-runbooks-overview.png)

1. Vyberte Runbook **AzureAutomationTutorialScript** . Tato akce otevře stránku s přehledem Runbooku.

    ![Přehled Runbooku](./media/automation-quickstart-create-account/automation-tutorial-script-runbook-overview.png)

1. Klikněte na tlačítko **Start**a na stránce **Spustit Runbook** spusťte sadu Runbook kliknutím na tlačítko **OK** .

    ![Stránka úlohy Runbooku](./media/automation-quickstart-create-account/automation-tutorial-script-job.png)

1. Jakmile se **stav úlohy** **spustí**, klikněte na **výstup** nebo **všechny protokoly** a zobrazte výstup úlohy Runbooku. Pro účely tohoto kurzu je výstupem seznam vašich prostředků Azure.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste nasadili účet Automation, spustili úlohu Runbooku a zobrazili jste výsledky úlohy. Pokud se chcete dozvědět víc o Azure Automation, pokračujte k rychlému startu při vytváření prvního Runbooku.

> [!div class="nextstepaction"]
> [Rychlý Start automatizace – vytvoření Runbooku](./automation-quickstart-create-runbook.md)

