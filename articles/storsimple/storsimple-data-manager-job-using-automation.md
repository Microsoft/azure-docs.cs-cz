---
title: Spuštění úlohy v StorSimple Data Manager pomocí Azure Automation
description: Naučte se používat Azure Automation pro aktivaci StorSimple Data Manager úloh.
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: f13e402344111dea28514ed7b0d381b46ff73064
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91743603"
---
# <a name="use-azure-automation-to-trigger-a-job"></a>Aktivace úlohy s využitím služby Azure Automation

Tento článek vysvětluje, jak můžete pomocí funkce transformace dat v rámci služby StorSimple Data Manager transformovat data zařízení StorSimple. Úlohu transformace dat můžete spustit dvěma způsoby: 

 - Použití sady .NET SDK
 - Použití Azure Automation Runbook
 
Tento článek podrobně popisuje, jak vytvořit sadu Azure Automation Runbook a pak ji použít k zahájení úlohy transformace dat. Další informace o tom, jak iniciovat transformaci dat pomocí sady .NET SDK, najdete v tématu [použití sady .NET SDK pro aktivaci úloh transformace dat](storsimple-data-manager-dotnet-jobs.md).

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že máte následující:

*   Azure PowerShell nainstalované v klientském počítači. [Stáhněte si Azure PowerShell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps).
*   Správně nakonfigurovaná definice úlohy ve StorSimple Data Manager službě v rámci skupiny prostředků.
*   Stáhněte si  [`DataTransformationApp.zip`](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/raw/master/Azure%20Automation%20For%20Data%20Manager/DataTransformationApp.zip) soubor z úložiště GitHub. 
*   Stáhněte si [`Trigger-DataTransformation-Job.ps1`](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Azure%20Automation%20For%20Data%20Manager/Trigger-DataTransformation-Job.ps1) skript z úložiště GitHub.

## <a name="step-by-step-procedure"></a>Podrobný postup

### <a name="set-up-the-automation-account"></a>Nastavení účtu Automation

1. Vytvořte v Azure Portal účet služby Automation spustit jako pro Azure. Provedete to tak, že přejdete na **Azure marketplace > vše** a pak vyhledáte **Automation**. Vyberte **účty Automation**.

    ![Vytvořit účet Spustit jako Automation](./media/storsimple-data-manager-job-using-automation/search-automation-account1.png)

2. Pokud chcete přidat nový účet Automation, klikněte na **+ Přidat**.

    ![Vytvořit účet Spustit jako Automation 2](./media/storsimple-data-manager-job-using-automation/add-automation-account1.png)

3. V okně **Přidat automatizaci**:

   1. Zadejte **název** svého účtu Automation.
   2. Vyberte **předplatné** propojené s vaší službou StorSimple data Manager.
   3. Vytvořte novou skupinu prostředků nebo vyberte některou z existujících skupin prostředků.
   4. Vyberte **umístění**.
   5. Ponechte vybranou možnost výchozí **účet Spustit jako** .
   6. Odkaz pro rychlý přístup na řídicím panelu získáte tak, že zkontrolujete **Připnout na řídicí panel**. Klikněte na **Vytvořit**.

      ![Vytvoření účtu Spustit jako služby Automation 3](./media/storsimple-data-manager-job-using-automation/create-automation-run-as-account.png)
    
      Po úspěšném vytvoření účtu Automation budete upozorněni.
    
      ![Oznámení pro nasazení účtu Automation](./media/storsimple-data-manager-job-using-automation/deployment-automation-account-notification1.png)

      Další informace získáte, když přejdete na [vytvořit účet Spustit jako](../automation/automation-create-runas-account.md).

3. V nově vytvořeném účtu přejděte na **sdílené prostředky > moduly** a klikněte na **+ Přidat modul**.

    ![Importovat modul 1](./media/storsimple-data-manager-job-using-automation/import-module-1.png)

4. Přejděte do umístění `DataTransformationApp.zip` souboru z místního počítače a vyberte a otevřete modul. Kliknutím na tlačítko **OK** Importujte modul.

    ![Importovat modul 2](./media/storsimple-data-manager-job-using-automation/import-module-2.png)

   Když Azure Automation importuje modul do svého účtu, extrahuje metadata o modulu. Tato operace může trvat několik minut.

   ![Importovat modul 4](./media/storsimple-data-manager-job-using-automation/import-module-4.png)

5. Obdržíte oznámení o tom, že je modul nasazený, a další oznámení po dokončení procesu.  Stav v **modulech** se změní na **k dispozici**.

    ![Importovat modul 5](./media/storsimple-data-manager-job-using-automation/import-module-5.png)

### <a name="import-publish-and-run-automation-runbook"></a>Import, publikování a spuštění Runbooku služby Automation

Proveďte následující kroky pro import, publikování a spuštění sady Runbook pro aktivaci definice úlohy.

1. Na webu Azure Portal otevřete účet Automation. Přejděte na **proces automatizace automatizace > Runbooky** a klikněte na **+ Přidat Runbook**.

    ![Přidat Runbook 1](./media/storsimple-data-manager-job-using-automation/add-runbook-1.png)

2. V nabídce **Přidat Runbook**klikněte na **importovat existující Runbook**.

3. Přejděte na soubor skriptu Azure PowerShell `Trigger-DataTransformation-Job.ps1` pro **soubor sady Runbook**. Typ Runbooku se vybere automaticky. Zadejte název a nepovinný popis Runbooku. Klikněte na **Vytvořit**.

    ![Přidat Runbook 2](./media/storsimple-data-manager-job-using-automation/add-runbook-2.png)

4. Nová sada Runbook se zobrazí v seznamu sad Runbook pro účet Automation. Vyberte tuto sadu Runbook a klikněte na ni.

    ![Přidat Runbook 3](./media/storsimple-data-manager-job-using-automation/add-runbook-3.png)

5. Upravte Runbook a klikněte na **testovací** podokno.

    ![Přidat sadu Runbook 4](./media/storsimple-data-manager-job-using-automation/add-runbook-4.png)

6. Zadejte parametry, jako je název služby StorSimple Data Manager, přidružená skupina prostředků a název definice úlohy. **Spusťte** test. Sestava je generována po dokončení spuštění. Další informace najdete v tématu Postup [testování Runbooku](../automation/automation-first-runbook-textual-powershell.md#step-3---test-the-runbook).

    ![Přidat Runbook 8](./media/storsimple-data-manager-job-using-automation/add-runbook-8.png)    

7. Zkontrolujte výstup Runbooku v podokně test. V případě splnění zavřete podokno. Klikněte na **publikovat** a po zobrazení výzvy k potvrzení, potvrzení a publikování Runbooku.

    ![Přidat Runbook 6](./media/storsimple-data-manager-job-using-automation/add-runbook-6.png)

8. Vraťte se na **Runbooky** a vyberte nově vytvořenou sadu Runbook.

    ![Přidat Runbook 7](./media/storsimple-data-manager-job-using-automation/add-runbook-7.png)

9. **Spusťte** Runbook. V části **Spustit Runbook**zadejte všechny parametry. Kliknutím na tlačítko **OK** odešlete a spusťte úlohu transformace dat.

10. Pokud chcete monitorovat průběh úloh v Azure Portal, Projděte si **úlohy** ve službě StorSimple data Manager. Pokud chcete zobrazit podrobnosti úlohy, vyberte úlohu a klikněte na ni.

    ![Přidat sadu Runbook 10](./media/storsimple-data-manager-job-using-automation/add-runbook-10.png)

## <a name="next-steps"></a>Další kroky

[K transformaci dat použijte StorSimple data Manager uživatelské rozhraní](storsimple-data-manager-ui.md).
