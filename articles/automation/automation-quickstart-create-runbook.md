---
title: Rychlý start Azure – Vytvoření runbooku Azure Automation | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit runbook Azure Automation.
services: automation
ms.date: 02/05/2019
ms.topic: quickstart
ms.subservice: process-automation
ms.custom: mvc
ms.openlocfilehash: 250f51c9f028dd55d8327259e35b82b0c392c1f6
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "81536994"
---
# <a name="create-an-azure-automation-runbook"></a>Vytvoření runbooku Azure Automation

Runbooky Azure Automation je možné vytvářet přes Azure. Tato metoda poskytuje uživatelské rozhraní v prohlížeči, pomocí kterého můžete vytvářet runbooky Automation. V tomto rychlém startu si projdete vytvořením, úpravou, testováním a publikováním powershellového runbooku Automation.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k https://portal.azure.comAzure na adrese.

## <a name="create-the-runbook"></a>Vytvoření Runbooku

Nejprve vytvořte runbook. Ukázkový runbook vytvářený v tomto rychlém startu ve výchozím nastavení vypisuje `Hello World`.

1. Otevřete váš účet služby Automation.

1. V části **Automatizace procesu**klikněte na **Runbooky** . Zobrazí se seznam runbooků.

1. V horní části seznamu klikněte na **vytvořit Runbook** .

1. Do `Hello-World` pole **název** zadejte název Runbooku a v poli **typ Runbooku** vyberte **PowerShell** . 

   ![Zadání informací o runbooku Automation na stránce](./media/automation-quickstart-create-runbook/automation-create-runbook-configure.png)

1. Klikněte na **Vytvořit**. Runbook se vytvoří a otevře se stránka Upravit powershellový runbook.

    ![Vytvoření skriptu PowerShellu v editoru runbooků](./media/automation-quickstart-create-runbook/automation-edit-runbook-empty.png)

1. Zadejte nebo zkopírujte a vložte následující kód do podokna úprav. Vytvoří volitelný vstupní parametr s názvem `Name` s výchozí hodnotou `World`a vypíše řetězec, který používá tuto vstupní hodnotu:

   ```powershell-interactive
   param
   (
       [Parameter(Mandatory=$false)]
       [String] $Name = "World"
   )

   "Hello $Name!"
   ```

1. Kliknutím na **Uložit** uložte pracovní kopii Runbooku.

    ![Vytvoření skriptu PowerShellu v editoru runbooků](./media/automation-quickstart-create-runbook/automation-edit-runbook.png)

## <a name="test-the-runbook"></a>Otestování runbooku

Po vytvoření sady Runbook je nutné sadu Runbook otestovat, aby bylo možné ověřit, zda funguje.

1. Kliknutím na **Testovací podokno** otevřete testovací podokno.

1. Zadejte nějakou hodnotu parametru **Name** (Název) a klikněte na **Spustit**. Spustí se testovací úloha a zobrazí se stav úlohy a výstup.

    ![Testovací úloha Runbooku](./media/automation-quickstart-create-runbook/automation-test-runbook.png)

1. Kliknutím na **X** v pravém horním rohu zavřete testovací podokno. V automaticky otevřeném okně vyberte **OK**.

1. Na stránce Upravit powershellový runbook klikněte na **Publikovat** a publikujte runbook jako oficiální verzi runbooku v tomto účtu.

   ![Testovací úloha Runbooku](./media/automation-quickstart-create-runbook/automation-hello-world-runbook-job.png)

## <a name="run-the-runbook"></a>Spuštění runbooku

Po publikování runbooku se zobrazí stránka s přehledem.

1. Na stránce s přehledem runbooku kliknutím na **Spustit** otevřete stránku konfigurace Spustit runbook pro tento runbook.

   ![Testovací úloha Runbooku](./media/automation-quickstart-create-runbook/automation-hello-world-runbook-start.png)

1. Parametr **Name** (Název) nechte prázdný, aby se použila výchozí hodnota, a klikněte na **OK**. Odešle se úloha Runbooku a zobrazí se stránka úloha.

   ![Testovací úloha Runbooku](./media/automation-quickstart-create-runbook/automation-job-page.png)

1. Když je `Running` stav úlohy nebo `Completed`, kliknutím na **výstup** otevřete podokno výstup a zobrazí se výstup Runbooku.

   ![Testovací úloha Runbooku](./media/automation-quickstart-create-runbook/automation-hello-world-runbook-job-output.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už runbook nepotřebujete, odstraňte ho. Provedete to tak, že v seznamu runbooků vyberete příslušný runbook a kliknete na **Odstranit**.

## <a name="next-steps"></a>Další kroky

V rámci tohoto rychlého startu jste vytvořili, upravili, otestovali a publikovali runbook a spustili úlohu runbooku. Další informace o runboocích Automation najdete v článku věnovaném různým typům runbooků, které můžete vytvářet a používat ve službě Automation.

> [!div class="nextstepaction"]
> [Návod pro službu Automation – Typy runbooků](./automation-runbook-types.md)
