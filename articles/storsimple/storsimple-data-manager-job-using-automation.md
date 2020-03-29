---
title: Spuštění úlohy ve Správci dat StorSimple pomocí Azure Automation
description: Naučte se používat Azure Automation pro spouštění úloh StorSimple Data Manager
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: 034b4996672f0961cf31d342aa6055482f099b9f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76273996"
---
# <a name="use-azure-automation-to-trigger-a-job"></a>Spuštění úlohy pomocí Azure Automation

Tento článek vysvětluje, jak můžete použít funkci transformace dat v rámci služby StorSimple Data Manager k transformaci dat zařízení StorSimple. Úlohu transformace dat můžete spustit dvěma způsoby: 

 - Použití sady .NET SDK
 - Použití runbooku Azure Automation
 
Tento článek podrobně popisuje, jak vytvořit runbook Azure Automation a pak ji použít k zahájení úlohy transformace dat. Další informace o zahájení transformace dat prostřednictvím sady .NET SDK najdete v použití sady [.NET SDK k aktivaci úloh transformace dat](storsimple-data-manager-dotnet-jobs.md).

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že máte:

*   Azure PowerShell nainstalovaný v klientském počítači. [Stáhněte si Azure PowerShell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps).
*   Správně nakonfigurovaná definice úlohy ve službě StorSimple Data Manager v rámci skupiny prostředků.
*   Stáhněte si [`DataTransformationApp.zip`](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/raw/master/Azure%20Automation%20For%20Data%20Manager/DataTransformationApp.zip) soubor z úložiště GitHub. 
*   Stáhněte si [`Trigger-DataTransformation-Job.ps1`](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Azure%20Automation%20For%20Data%20Manager/Trigger-DataTransformation-Job.ps1) skript z úložiště GitHub.

## <a name="step-by-step-procedure"></a>Postup krok za krokem

### <a name="set-up-the-automation-account"></a>Nastavení účtu automatizace

1. Vytvořte účet Azure Run As automation na webu Azure Portal. Chcete-li tak učinit, přejděte na **Azure marketplace > Všechno** a **vyhledejte automatizaci**. Vyberte **automatizační účty**.

    ![Vytvořit spustit jako účet automatizace](./media/storsimple-data-manager-job-using-automation/search-automation-account1.png)

2. Chcete-li přidat nový účet automatizace, klepněte na tlačítko **+ Přidat**.

    ![Vytvořit spustit jako účet automatizace](./media/storsimple-data-manager-job-using-automation/add-automation-account1.png)

3. V přidat **automatizaci**:

   1. Zadejte **název** účtu automatizace.
   2. Vyberte **předplatné** propojené se službou StorSimple Data Manager.
   3. Vytvořte novou skupinu prostředků nebo vyberte z existující skupiny prostředků.
   4. Vyberte **umístění**.
   5. Ponechte vybranou výchozí možnost **Vytvořit spustit jako účet.**
   6. Chcete-li získat odkaz pro rychlý přístup na řídicí panel, zaškrtněte **políčko Připnout na řídicí panel**. Klikněte na **Vytvořit**.

      ![Vytvořit spustit jako účet automatizace](./media/storsimple-data-manager-job-using-automation/create-automation-run-as-account.png)
    
      Po úspěšném vytvoření účtu automatizace budete upozorněni.
    
      ![Oznámení o nasazení účtu automatizace](./media/storsimple-data-manager-job-using-automation/deployment-automation-account-notification1.png)

      Další informace naleznete v najdete [v najdete v souboru Vytvoření účtu Spustit jako](../automation/automation-create-runas-account.md).

3. V nově vytvořeném účtu přejděte na **sdílené prostředky > moduly** a klepněte na tlačítko **+ Přidat modul**.

    ![Importní modul 1](./media/storsimple-data-manager-job-using-automation/import-module-1.png)

4. Přejděte do `DataTransformationApp.zip` umístění souboru z místního počítače a vyberte a otevřete modul. Klepnutím na **tlačítko OK** modul importujete.

    ![Importní modul 2](./media/storsimple-data-manager-job-using-automation/import-module-2.png)

   Když Azure Automation importuje modul do vašeho účtu, extrahuje metadata o modulu. Tato operace může trvat několik minut.

   ![Importní modul 4](./media/storsimple-data-manager-job-using-automation/import-module-4.png)

5. Obdržíte oznámení, že modul je nasazen a další oznámení po dokončení procesu.  Stav **moduly** se změní na **K dispozici**.

    ![Importní modul 5](./media/storsimple-data-manager-job-using-automation/import-module-5.png)

### <a name="import-publish-and-run-automation-runbook"></a>Import, publikování a spuštění runbooku Automation

Chcete-li aktivovat definici úloh, proveďte následující kroky importovat, publikovat a spustit runbook.

1. Na webu Azure Portal otevřete účet Automation. Přejděte na **> runbooky** a klikněte na **+ Přidat runbook**.

    ![Přidat runbook 1](./media/storsimple-data-manager-job-using-automation/add-runbook-1.png)

2. V **části Přidat runbook**klepněte na **položku Importovat existující soubor Runbook**.

3. Přejděte na soubor `Trigger-DataTransformation-Job.ps1` skriptu Azure PowerShell pro soubor **Runbook**. Automaticky se vybere typ knihy Runbook. Zadejte název a volitelný popis runbooku. Klikněte na **Vytvořit**.

    ![Přidat runbook 2](./media/storsimple-data-manager-job-using-automation/add-runbook-2.png)

4. Nový runbook se zobrazí v seznamu runbooků pro účet Automation. Vyberte a klikněte na tento soubor Runbook.

    ![Přidat runbook 3](./media/storsimple-data-manager-job-using-automation/add-runbook-3.png)

5. Upravte runbook a klikněte na **Podokno test.**

    ![Přidat runbook 4](./media/storsimple-data-manager-job-using-automation/add-runbook-4.png)

6. Zadejte parametry, jako je název služby StorSimple Data Manager, přidružená skupina prostředků a název definice úlohy. **Spusťte** test. Sestava je generována po dokončení spuštění. Další informace naleznete v [otestovaném runbooku](../automation/automation-first-runbook-textual-powershell.md#step-3---test-the-runbook).

    ![Přidat runbook 8](./media/storsimple-data-manager-job-using-automation/add-runbook-8.png)    

7. Zkontrolujte výstup z runbooku v testovacím podokně. Pokud je splněna, zavřete podokno. Klikněte na **Publikovat** a po zobrazení výzvy k potvrzení, potvrzení a publikování runbooku.

    ![Přidat runbook 6](./media/storsimple-data-manager-job-using-automation/add-runbook-6.png)

8. Vraťte se do **sady Runbook** a vyberte nově vytvořený runbook.

    ![Přidat runbook 7](./media/storsimple-data-manager-job-using-automation/add-runbook-7.png)

9. **Spusťte** runbook. V **seznamu Start runbook**zadejte všechny parametry. Klepnutím na **tlačítko OK** odešlete a spusťte úlohu transformace dat.

10. Pokud chcete sledovat průběh úlohy na webu Azure Portal, přejděte na **Úlohy** ve službě StorSimple Data Manager. Výběrem a kliknutím na úlohu zobrazíte podrobnosti o úloze.

    ![Přidat runbook 10](./media/storsimple-data-manager-job-using-automation/add-runbook-10.png)

## <a name="next-steps"></a>Další kroky

[Pomocí ui Správce dat StorSimple pro transformaci dat](storsimple-data-manager-ui.md).
