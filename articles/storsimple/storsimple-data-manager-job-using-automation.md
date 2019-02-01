---
title: Pomocí Azure Automation ke spuštění úlohy správce dat StorSimple | Dokumentace Microsoftu
description: Další informace o použití Azure Automation. pro spuštění úlohy správce dat StorSimple
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: 6e62e923b6e983dfff8eb72c9526708a3b360930
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55494744"
---
# <a name="use-azure-automation-to-trigger-a-job"></a>Pomocí Azure Automation k aktivaci úlohy

Tento článek vysvětluje, jak můžete pomocí funkce transformace dat ve službě StorSimple Data Manageru k transformaci dat zařízení StorSimple. Můžete spustit úlohu transformace dat dvěma způsoby: 

 - Použití sady .NET SDK
 - Použití runbooku Azure Automation
 
Tento článek podrobně popisuje, jak vytvořit runbook Azure Automation a použít jej k zahájení úlohu transformace dat. Další informace o tom, jak inicializovat transformace dat pomocí sady .NET SDK, přejděte na [použití sady .NET SDK k aktivaci úloh transformace dat](storsimple-data-manager-dotnet-jobs.md).

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že máte:

*   Azure PowerShell v klientském počítači nainstalována. [Stáhněte si prostředí Azure PowerShell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps).
*   Definice správně nakonfigurovanou úlohy ve službě StorSimple Data Manageru v rámci skupiny prostředků.
*   Stáhněte si [ `DataTransformationApp.zip` ](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/raw/master/Azure%20Automation%20For%20Data%20Manager/DataTransformationApp.zip) soubor z úložiště Githubu. 
*   Stáhněte si [ `Trigger-DataTransformation-Job.ps1` ](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Azure%20Automation%20For%20Data%20Manager/Trigger-DataTransformation-Job.ps1) skript z úložiště GitHub.

## <a name="step-by-step-procedure"></a>Podrobný postup

### <a name="set-up-the-automation-account"></a>Při vytváření účtu Automation

1. Vytvoření účtu automation spustit jako pro Azure na webu Azure Portal. Uděláte to tak, přejděte na **Azure marketplace > vše, co** a vyhledejte **automatizace**. Vyberte **účty Automation**.

    ![Vytvořit účet automation spustit jako](./media/storsimple-data-manager-job-using-automation/search-automation-account1.png)

2. Chcete-li přidat nový účet automation, klikněte na tlačítko **+ přidat**.

    ![Vytvořit účet automation spustit jako](./media/storsimple-data-manager-job-using-automation/add-automation-account1.png)

3. V **přidání automatizace**:

    1. Zadat **název** vašeho účtu automation.
    2. Vyberte **předplatné** propojenému s vaší službou StorSimple Data Manageru.
    3. Vytvořit novou skupinu prostředků nebo vyberte existující skupinu prostředků.
    4. Vyberte **Umístění**.
    5. Ponechte výchozí nastavení **vytvořit účet Spustit jako** zaškrtnutou možnost.
    6. Chcete-li získat odkaz na rychlý přístup na řídicím panelu, zkontrolujte **připnout na řídicí panel**. Klikněte na možnost **Vytvořit**.

    ![Vytvořit účet automation spustit jako](./media/storsimple-data-manager-job-using-automation/create-automation-run-as-account.png)
    
    Po úspěšném vytvoření účtu automation, budete upozorněni.
    
    ![Oznámení pro nasazení účtu automation](./media/storsimple-data-manager-job-using-automation/deployment-automation-account-notification1.png)

    Další informace najdete v části [vytvořit účet Spustit jako](../automation/automation-create-runas-account.md).

3. V nově vytvořeného účtu, přejděte na **sdílené prostředky > moduly** a klikněte na tlačítko **+ přidat modul**.

    ![Import modulu 1](./media/storsimple-data-manager-job-using-automation/import-module-1.png)

4. Přejděte do umístění `DataTransformationApp.zip` souboru ze svého místního počítače a vyberte a otevřete modul. Klikněte na tlačítko **OK** importujte modul.

    ![Importovat modul 2](./media/storsimple-data-manager-job-using-automation/import-module-2.png)

   Azure Automation importuje modul ke svému účtu, extrahuje metadata modulu. Tato operace může trvat několik minut.

   ![Importovat modul 4](./media/storsimple-data-manager-job-using-automation/import-module-4.png)

5. Po dokončení tohoto procesu se zobrazí oznámení, že se nasazuje modulu a další oznámení.  Stav v **moduly** změny **dostupné**.

    ![Import modulu 5](./media/storsimple-data-manager-job-using-automation/import-module-5.png)

### <a name="import-publish-and-run-automation-runbook"></a>Import, publikování a spuštění runbooku Automation

Postupujte následovně chcete importovat, publikování a spuštění sady runbook k aktivaci definice úlohy.

1. Na webu Azure Portal otevřete účet Automation. Přejděte na **automatizace procesů > sady Runbook** a klikněte na tlačítko **+ přidat runbook**.

    ![Přidat runbook 1](./media/storsimple-data-manager-job-using-automation/add-runbook-1.png)

2. V **přidat runbook**, klikněte na tlačítko **importovat existující runbook**.

3. Přejděte na soubor skriptu Azure Powershellu `Trigger-DataTransformation-Job.ps1` pro **soubor sady Runbook**. Typ runbooku se vybere automaticky. Zadejte název a volitelný popis pro sadu runbook. Klikněte na možnost **Vytvořit**.

    ![Přidat runbook 2](./media/storsimple-data-manager-job-using-automation/add-runbook-2.png)

4. Nová sada runbook se zobrazí v seznamu sad runbook pro účet Automation. Vyberte a klikněte na tuto sadu runbook.

    ![Přidat runbook 3](./media/storsimple-data-manager-job-using-automation/add-runbook-3.png)

5. Upravit sadu runbook a klikněte na tlačítko **Test** podokně.

    ![Přidat runbook 4](./media/storsimple-data-manager-job-using-automation/add-runbook-4.png)

6. Zadejte parametry, jako je například název vaší služby StorSimple Data Manager, v přidružené skupině prostředků a název definice úlohy. **Spustit** testu. Po dokončení spuštění se generuje sestava. Další informace najdete v části Jak [otestování sady runbook](../automation/automation-first-runbook-textual-powershell.md#step-3---test-the-runbook).

    ![Přidat runbook 8](./media/storsimple-data-manager-job-using-automation/add-runbook-8.png)    

7. Zkontrolujte výstup z runbooku v testovací podokno. Při splnění zavřete podokno. Klikněte na tlačítko **publikovat** a po zobrazení výzvy k potvrzení, potvrzení a publikovat sadu runbook.

    ![Přidat runbook 6](./media/storsimple-data-manager-job-using-automation/add-runbook-6.png)

8. Přejděte zpět na **sady Runbook** a vyberte nově vytvořenou sadu runbook.

    ![Přidat runbook 7](./media/storsimple-data-manager-job-using-automation/add-runbook-7.png)

9. **Spustit** sady runbook. V **spustit sadu runbook**, zadejte všechny parametry. Klikněte na tlačítko **OK** odeslat a spustit úlohu transformace data.

10. Pokud chcete monitorovat průběh úlohy na webu Azure portal, přejděte na **úlohy** ve službě StorSimple Data Manageru. Vyberte a klikněte na úlohu chcete-li zobrazit podrobnosti o úloze.

    ![Přidat runbook 10](./media/storsimple-data-manager-job-using-automation/add-runbook-10.png)

## <a name="next-steps"></a>Další postup

[Použití StorSimple Data Manager uživatelského rozhraní pro transformaci dat](storsimple-data-manager-ui.md).