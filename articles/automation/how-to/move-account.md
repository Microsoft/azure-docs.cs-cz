---
title: Přesunutí účtu Azure Automation do jiného předplatného
description: Tento článek popisuje, jak přesunout účet Automation do jiného předplatného.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/11/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2dbe7dc171b6e0ec81c99a460a4f997eeb9e27a5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81681891"
---
# <a name="move-your-azure-automation-account-to-another-subscription"></a>Přesunutí účtu Azure Automation do jiného předplatného

Azure Automation umožňuje přesunout některé prostředky do nové skupiny prostředků nebo předplatného. Prostředky můžete přesunout pomocí Azure Portal, PowerShellu, rozhraní příkazového řádku Azure nebo REST API. Další informace o tomto procesu najdete v tématu [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](../../azure-resource-manager/management/move-resource-group-and-subscription.md).

Azure Automation účet je jedním z prostředků, které můžete přesunout. V tomto článku se dozvíte, jak přesunout účty Automation do jiného prostředku nebo předplatného. Postup při přesunu vašeho účtu Automation je v rámci vysoké úrovně:

1. Odeberte svá řešení.
2. Odpojte svůj pracovní prostor.
3. Přesuňte účet Automation.
4. Odstraňte a znovu vytvořte účty Spustit jako.
5. Znovu povolte vaše řešení.

>[!NOTE]
>Tento článek je aktualizovaný a využívá nový modul Az Azure PowerShellu. Můžete dál využívat modul AzureRM, který bude dostávat opravy chyb nejméně do prosince 2020. Další informace o kompatibilitě nového modulu Az a modulu AzureRM najdete v tématu [Seznámení s novým modulem Az Azure PowerShellu](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pokyny k instalaci nástroje AZ Module Hybrid Runbook Worker najdete v tématu [Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Pro váš účet Automation můžete aktualizovat moduly na nejnovější verzi pomocí [postupu aktualizace modulů Azure PowerShell v Azure Automation](../automation-update-azure-modules.md).

## <a name="remove-solutions"></a>Odebrat řešení

Pokud chcete odpojit svůj pracovní prostor od svého účtu Automation, musíte tato řešení odebrat z pracovního prostoru:

- Change Tracking a Inventory
- Update Management
- Spuštění/zastavení virtuálních počítačů mimo špičku

1. Na webu Azure Portal vyhledejte svou skupinu prostředků.
2. Najděte každé řešení a na stránce Odstranit prostředky klikněte na **Odstranit** .

    ![Odstranit řešení z Azure Portal](../media/move-account/delete-solutions.png)

    Pokud budete chtít, můžete řešení odstranit pomocí rutiny [Remove-AzResource](https://docs.microsoft.com/powershell/module/Az.Resources/Remove-AzResource?view=azps-3.7.0) :

    ```azurepowershell-interactive
    $workspaceName = <myWorkspaceName>
    $resourceGroupName = <myResourceGroup>
    Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking($workspaceName)" -ResourceGroupName $resourceGroupName
    Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates($workspaceName)" -ResourceGroupName $resourceGroupName
    Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM($workspaceName)" -ResourceGroupName $resourceGroupName
    ```

### <a name="remove-alert-rules-for-the-startstop-vms-during-off-hours-solution"></a>Odebrat pravidla upozornění pro virtuální počítače spustit/zastavit během nepracovních řešení

U virtuálních počítačů spustit/zastavit v době mimo špičku je také potřeba odebrat pravidla upozornění vytvořená řešením.

1. V Azure Portal přejdete do skupiny prostředků a vyberte **monitorování** > **výstrahy** > **Spravovat pravidla výstrah**.

![Stránka s výstrahami ukazující výběr pravidel pro správu pravidel výstrah](../media/move-account/alert-rules.png)

2. Na stránce pravidla by se měl zobrazit seznam výstrah nakonfigurovaných v této skupině prostředků. Řešení vytváří tato pravidla:

    * AutoStop_VM_Child
    * ScheduledStartStop_Parent
    * SequencedStartStop_Parent

3. Vyberte pravidla po jednom a kliknutím na **Odstranit** je odeberte.

    ![Stránka pravidla požadující potvrzení odstranění pro vybraná pravidla](../media/move-account/delete-rules.png)

    > [!NOTE]
    > Pokud nevidíte žádná pravidla výstrahy na stránce pravidla, změňte pole **stav** na zakázáno, aby se zobrazily zakázané výstrahy, protože jste je mohli zablokovat.

4. Po odebrání pravidel upozornění musíte odebrat skupinu akcí vytvořenou pro spouštění a zastavování virtuálních počítačů v době mimo špičku řešení. V Azure Portal vyberte **monitorování** > **výstrahy** > **Spravovat skupiny akcí**.

5. Vyberte **StartStop_VM_Notification**. 

6. Na stránce skupina akcí vyberte **Odstranit**.

    ![Stránka skupiny akcí](../media/move-account/delete-action-group.png)

    Pokud budete chtít, můžete odstranit skupinu akcí pomocí rutiny [Remove-AzActionGroup](https://docs.microsoft.com/powershell/module/az.monitor/remove-azactiongroup?view=azps-3.7.0) :

    ```azurepowershell-interactive
    Remove-AzActionGroup -ResourceGroupName <myResourceGroup> -Name StartStop_VM_Notification
    ```

## <a name="unlink-your-workspace"></a>Zrušit propojení pracovního prostoru

Nyní můžete zrušit propojení pracovního prostoru:

1. V Azure Portal vyberte**pracovní prostor propojené** > **prostředky** > v **účtu Automation**. 

2. Vyberte zrušit **propojení pracovního prostoru** , abyste mohli zrušit propojení pracovního prostoru s vaším účtem Automation.

    ![Zrušení propojení pracovního prostoru s účtem Automation](../media/move-account/unlink-workspace.png)

## <a name="move-your-automation-account"></a>Přesunutí účtu Automation

Nyní můžete přesunout svůj účet Automation a jeho Runbooky. 

1. V Azure Portal přejděte do skupiny prostředků svého účtu Automation. Vyberte **přesunout** > **přesunout do jiného předplatného**.

    ![Stránka skupiny prostředků, přejít na jiné předplatné](../media/move-account/move-resources.png)

2. Vyberte prostředky ve vaší skupině prostředků, které chcete přesunout. Ujistěte se, že jste zahrnuli svůj účet Automation, Runbooky a Log Analytics prostředky pracovního prostoru.

## <a name="recreate-run-as-accounts"></a>Opětovné vytvoření účtů spustit jako

[Účty Spustit jako](../manage-runas-account.md) vytvoří instanční objekt v Azure Active Directory k ověřování pomocí prostředků Azure. Když změníte předplatné, účet Automation už nepoužívá existující účet Spustit jako. Postup opětovného vytvoření účtů spustit jako:

1. V rámci nového předplatného vyberte účet Automation a v části **Nastavení účtu**vyberte **účty Spustit jako** . Uvidíte, že účty Spustit jako jsou nyní zobrazeny jako nedokončené.

    ![Účty Spustit jako jsou neúplné](../media/move-account/run-as-accounts.png)

2. Odstraňte účty Spustit jako jeden po druhém pomocí tlačítka **Odstranit** na stránce Vlastnosti. 

    > [!NOTE]
    > Pokud nemáte oprávnění k vytvoření nebo zobrazení účtů spustit jako, zobrazí se následující zpráva: `You do not have permissions to create an Azure Run As account (service principal) and grant the Contributor role to the service principal.` informace o oprávněních potřebných ke konfiguraci účtu Spustit jako najdete v tématu [oprávnění požadovaná ke konfiguraci účtů spustit jako](../manage-runas-account.md#permissions).

3. Po odstranění účtů spustit jako vyberte v části **účet Spustit jako pro Azure**možnost **vytvořit** . 

4. Na stránce Přidat účet Spustit jako pro Azure vyberte **vytvořit** a vytvořte účet Spustit jako a instanční objekt. 

5. Opakujte výše uvedené kroky s účtem spustit jako pro Azure Classic.

## <a name="enable-solutions"></a>Povolení řešení

Po opětovném vytvoření účtů spustit jako je nutné znovu povolit řešení, která jste odebrali před přesunutím: 

1. Pokud chcete zapnout řešení Change Tracking a inventáře, vyberte Change Tracking a inventář v účtu Automation. Zvolte pracovní prostor Log Analytics, který jste přesunuli, a vyberte **Povolit**.

2. Opakujte krok 1 pro řešení Update Management.

    ![Opětovné povolení řešení na přesunutém účtu Automation](../media/move-account/reenable-solutions.png)

3. Počítače, které jsou připojené k vašim řešením, se zobrazí, když jste připojili existující Log Analytics pracovní prostor. Pokud chcete zapnout a vypnout virtuální počítače v době mimo špičku, musíte řešení znovu nasadit. V části **související prostředky**vyberte **Spustit nebo zastavit virtuální počítače** > další**informace o a povolte řešení** > **vytvořit** , abyste mohli nasazení spustit.

4. Na stránce Přidat řešení vyberte pracovní prostor Log Analytics a účet Automation.

    ![Přidat nabídku řešení](../media/move-account/add-solution-vm.png)

5. Nakonfigurujte řešení, jak je popsáno v tématu [spuštění/zastavení virtuálních počítačů v době mimo špičku v Azure Automation](../automation-solution-vm-management.md).

## <a name="verify-the-move"></a>Ověřit přesunutí

Po dokončení přesunu ověřte, že jsou povolené níže uvedené možnosti. 

|Schopnost|Testy|Řešení potíží|
|---|---|---|
|Runbooky|Sada Runbook může úspěšně běžet a připojovat se k prostředkům Azure.|[Řešení potíží s runbooky](../troubleshoot/runbooks.md)
|Správa zdrojového kódu|Můžete spustit ruční synchronizaci v úložišti správy zdrojového kódu.|[Integrace správy zdrojového kódu](../source-control-integration.md)|
|Sledování změn a inventáře|Ověřte, že se na vašich počítačích zobrazují aktuální data inventáře.|[Řešení potíží se sledováním změn](../troubleshoot/change-tracking.md)|
|Správa aktualizací|Ověřte, zda jsou vaše počítače a zda jsou v pořádku.</br>Spusťte testovací nasazení aktualizace softwaru.|[Řešení potíží se správou aktualizací](../troubleshoot/update-management.md)|
|Sdílené prostředky|Ověřte, že vidíte všechny sdílené prostředky, jako jsou například [přihlašovací údaje](../shared-resources/credentials.md), [proměnné](../shared-resources/variables.md)a jako.|

## <a name="next-steps"></a>Další kroky

Další informace o přesouvání prostředků v Azure najdete v tématu [Přesunutí prostředků v Azure](../../azure-resource-manager/management/move-support-resources.md).
