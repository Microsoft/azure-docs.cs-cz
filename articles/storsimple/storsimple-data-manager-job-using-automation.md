---
title: Pro spuštění úlohy StorSimple Data Manager použijte Azure Automation | Microsoft Docs
description: Naučte se používat Azure Automation pro spuštění úlohy Data Manager zařízení StorSimple
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
ms.openlocfilehash: 1e5fcbee664271058ac1c7fa80bb285e09b8579a
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2018
ms.locfileid: "27862192"
---
# <a name="use-azure-automation-to-trigger-a-job"></a>Použít k aktivaci úlohy automatizace Azure.

Tento článek vysvětluje, jak můžete použít funkci transformaci dat v rámci služby StorSimple Manager dat k transformaci dat zařízení StorSimple. Můžete spustit úlohu transformace datového dvěma způsoby: 

 - Použití sady .NET SDK
 - Použít runbook automatizace Azure.
 
Tento článek podrobné informace o tom, jak vytvořit runbook služby automatizace Azure a použít ho k zahájení úlohy transformace datového. Další informace o tom, jak zahájit transformaci dat pomocí .NET SDK, přejděte na [pomocí .NET SDK k aktivační události úlohy transformace datového](storsimple-data-manager-dotnet-jobs.md).

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, zda máte:

*   Azure PowerShell v klientském počítači nainstalována. [Stáhnout prostředí Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps).
*   Definice správně nakonfigurována úlohy ve službě StorSimple Manager dat ve skupině prostředků.
*   Stáhněte si [ `DataTransformationApp.zip` ](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/raw/master/Azure%20Automation%20For%20Data%20Manager/DataTransformationApp.zip) soubor z úložiště Githubu. 
*   Stáhněte si [ `Trigger-DataTransformation-Job.ps1` ](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Azure%20Automation%20For%20Data%20Manager/Trigger-DataTransformation-Job.ps1) skript z úložiště Githubu.

## <a name="step-by-step-procedure"></a>Podrobný postup

### <a name="set-up-the-automation-account"></a>Nastavit účet Automation

1. Vytvoření účtu automation spustit v Azure jako na portálu Azure. Chcete-li to provést, přejděte na **Azure marketplace > všechno, co** a poté vyhledejte **automatizace**. Vyberte **účty Automation**.

    ![Vytvořit účet automation spustit jako](./media/storsimple-data-manager-job-using-automation/search-automation-account1.png)

2. Chcete-li přidat nový účet automation, klikněte na tlačítko **+ přidat**.

    ![Vytvořit účet automation spustit jako](./media/storsimple-data-manager-job-using-automation/add-automation-account1.png)

3. V **přidat automatizace**:

    1. Zadejte **název** účtu automation.
    2. Vyberte **předplatné** propojené s služby StorSimple Data Manager.
    3. Vytvořte novou skupinu prostředků nebo vyberte existující skupinu prostředků.
    4. Vyberte **Umístění**.
    5. Ponechte výchozí nastavení **vytvořit účet Spustit jako** možnost.
    6. Chcete-li získat odkaz pro rychlý přístup na řídicím panelu, zkontrolujte **připnout na řídicí panel**. Klikněte na možnost **Vytvořit**.

    ![Vytvořit účet automation spustit jako](./media/storsimple-data-manager-job-using-automation/create-automation-run-as-account.png)
    
    Po úspěšném vytvoření účtu automation, budete upozorněni.
    
    ![Oznámení pro nasazení účtu automation](./media/storsimple-data-manager-job-using-automation/deployment-automation-account-notification1.png)

    Další informace, přejděte na [vytvořit účet Spustit jako](../automation/automation-create-runas-account.md).

3. V nově vytvořený účet, přejděte do **sdílené prostředky > moduly** a klikněte na tlačítko **+ přidat modul**.

    ![Import modulu 1](./media/storsimple-data-manager-job-using-automation/import-module-1.png)

4. Přejděte do umístění `DataTransformationApp.zip` souboru z místního počítače a vyberte a otevřete modul. Klikněte na tlačítko **OK** Import modulu.

    ![Import modulu 2](./media/storsimple-data-manager-job-using-automation/import-module-2.png)

   Jakmile Azure Automation importuje modul ke svému účtu, extrahuje metadata o modulu. Tato operace může trvat několik minut.

   ![Import modulu 4](./media/storsimple-data-manager-job-using-automation/import-module-4.png)

5. Po dokončení procesu obdržíte oznámení, který je nasazován modul a jiné oznámení.  Tento stav v **moduly** změny **dostupné**.

    ![Import modulu 5](./media/storsimple-data-manager-job-using-automation/import-module-5.png)

### <a name="import-publish-and-run-automation-runbook"></a>Import, publikování a spuštění sady runbook automatizace

Proveďte následující kroky pro import, publikování a spuštění sady runbook k aktivaci definice úlohy.

1. Na webu Azure Portal otevřete účet Automation. Přejděte na **automatizace procesu > sady Runbook** a klikněte na tlačítko **+ přidat runbook**.

    ![Přidat runbook 1](./media/storsimple-data-manager-job-using-automation/add-runbook-1.png)

2. V **přidat runbook**, klikněte na tlačítko **importovat stávající runbook**.

3. Přejděte na soubor skriptu Azure Powershellu `Trigger-DataTransformation-Job.ps1` pro **soubor sady Runbook**. Typ runbooku je automaticky vybrán. Zadejte název a volitelný popis pro sadu runbook. Klikněte na možnost **Vytvořit**.

    ![Přidat runbook 2](./media/storsimple-data-manager-job-using-automation/add-runbook-2.png)

4. Nový runbook se zobrazí v seznamu sad runbook pro účet služby Automation. Vyberte a klikněte na tuto sadu runbook.

    ![Přidat runbook 3](./media/storsimple-data-manager-job-using-automation/add-runbook-3.png)

5. Upravte sadu runbook a klikněte na tlačítko **Test** podokně.

    ![Přidat sady runbook 4](./media/storsimple-data-manager-job-using-automation/add-runbook-4.png)

6. Zadejte parametry, jako je název služby StorSimple Data Manager, skupiny přidružené Gateway a název definice úlohy. **Spustit** test. Kdy je sestava vygenerována po dokončení spuštění. Další informace, přejděte na postup [otestování sady runbook](../automation/automation-first-runbook-textual-powershell.md#step-3---test-the-runbook).

    ![Přidat runbook 8](./media/storsimple-data-manager-job-using-automation/add-runbook-8.png)    

7. Zkontrolujte výstup z runbooku v testovací podokno. Pokud budete s informacemi spokojeni, zavřete podokno. Klikněte na tlačítko **publikovat** a po zobrazení výzvy k potvrzení, potvrďte a publikovat sadu runbook.

    ![Přidat runbook 6](./media/storsimple-data-manager-job-using-automation/add-runbook-6.png)

8. Přejděte zpět na **Runbooky** a vyberte nově vytvořenou sadu runbook.

    ![Přidat runbook 7](./media/storsimple-data-manager-job-using-automation/add-runbook-7.png)

9. **Spustit** sady runbook. V **spuštění sady runbook**, zadejte všechny parametry. Klikněte na tlačítko **OK** k odeslání a spustit úlohu transformace data.

10. Chcete-li sledovat průběh úlohy na portálu Azure, přejděte na **úlohy** ve službě StorSimple Data Manager. Vyberte a klikněte na úlohu chcete-li zobrazit podrobnosti úlohy.

    ![Přidat sady runbook 10](./media/storsimple-data-manager-job-using-automation/add-runbook-10.png)

## <a name="next-steps"></a>Další postup

[Data Manager zařízení StorSimple pomocí uživatelského rozhraní pro transformaci dat](storsimple-data-manager-ui.md).