---
title: Přesunutí účtu Azure Automation do jiného předplatného
description: V tomto článku se dozvíte, jak přesunout účet Automation do jiného předplatného.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/11/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 8454c5a5bb5b44d2a60ae0095a9b82a19ed27c8d
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896642"
---
# <a name="move-your-azure-automation-account-to-another-subscription"></a>Přesunutí účtu Azure Automation do jiného předplatného

Azure Automation umožňuje přesunout některé prostředky do nové skupiny prostředků nebo předplatného. Prostředky můžete přesunout pomocí Azure Portal, PowerShellu, rozhraní příkazového řádku Azure nebo REST API. Další informace o tomto procesu najdete v tématu [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](../../azure-resource-manager/management/move-resource-group-and-subscription.md).

Účet Automation je jedním z prostředků, které můžete přesunout. V tomto článku se dozvíte, jak přesunout účty Automation do jiného prostředku nebo předplatného. Postup při přesunu vašeho účtu Automation je v rámci vysoké úrovně:

1. Zakažte své funkce.
2. Odpojte svůj pracovní prostor.
3. Přesuňte účet Automation.
4. Odstraňte a znovu vytvořte účty Spustit jako.
5. Znovu povolte své funkce.

## <a name="remove-features"></a>Odebrat funkce

Pokud chcete odpojit svůj pracovní prostor od svého účtu Automation, musíte odebrat prostředky funkce ve vašem pracovním prostoru:

- Change Tracking a Inventory
- Update Management
- Spuštění/zastavení virtuálních počítačů mimo špičku

1. Na webu Azure Portal vyhledejte svou skupinu prostředků.
2. Najděte jednotlivé funkce a na stránce **Odstranit prostředky** vyberte **Odstranit** .

    ![Snímek obrazovky odstranění prostředků funkce z Azure Portal](../media/move-account/delete-solutions.png)

Pokud budete chtít, můžete prostředky odstranit pomocí rutiny [Remove-AzResource](/powershell/module/Az.Resources/Remove-AzResource) :

```azurepowershell-interactive
$workspaceName = <myWorkspaceName>
$resourceGroupName = <myResourceGroup>
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM($workspaceName)" -ResourceGroupName $resourceGroupName
```

### <a name="remove-alert-rules-for-startstop-vms-during-off-hours"></a>Odebrat pravidla upozornění pro Start/Stop VMs during off-hours

Pro Start/Stop VMs during off-hours musíte také odebrat pravidla upozornění vytvořená funkcí.

1. V Azure Portal přejdete do skupiny prostředků a vyberte **monitorování**  >  **výstrahy**  >  **Spravovat pravidla výstrah**.

   ![Snímek stránky s výstrahami, která zobrazuje výběr pravidel pro správu pravidel výstrah](../media/move-account/alert-rules.png)

2. Na stránce pravidla by se měl zobrazit seznam výstrah nakonfigurovaných v této skupině prostředků. Tato funkce vytvoří tato pravidla:

    * AutoStop_VM_Child
    * ScheduledStartStop_Parent
    * SequencedStartStop_Parent

3. Vyberte pravidla po jednom a vyberte **Odstranit** a odstraňte je.

    ![Snímek obrazovky se stránkou pravidel, která vyžaduje potvrzení odstranění pro vybraná pravidla](../media/move-account/delete-rules.png)

    > [!NOTE]
    > Pokud nevidíte žádná pravidla výstrahy na stránce pravidla, změňte pole **stav** na **zakázáno** , aby se zobrazily zakázané výstrahy. 

4. Po odebrání pravidel výstrahy je nutné odebrat skupinu akcí vytvořenou pro Start/Stop VMs during off-hours oznámení. V Azure Portal vyberte **monitorování**  >  **výstrahy**  >  **Spravovat skupiny akcí**.

5. Vyberte **StartStop_VM_Notification**. 

6. Na stránce skupina akcí vyberte **Odstranit**.

    ![Snímek stránky skupiny akcí](../media/move-account/delete-action-group.png)

Pokud budete chtít, můžete odstranit skupinu akcí pomocí rutiny [Remove-AzActionGroup](/powershell/module/az.monitor/remove-azactiongroup) :

```azurepowershell-interactive
Remove-AzActionGroup -ResourceGroupName <myResourceGroup> -Name StartStop_VM_Notification
```

## <a name="unlink-your-workspace"></a>Zrušit propojení pracovního prostoru

Nyní můžete zrušit propojení pracovního prostoru:

1. V Azure Portal vyberte   >    >  **pracovní prostor propojené** prostředky v účtu Automation. 

2. Vyberte zrušit **propojení pracovního prostoru** , abyste mohli zrušit propojení pracovního prostoru s vaším účtem Automation.

    ![Snímek obrazovky, který odpojí pracovní prostor od účtu Automation](../media/move-account/unlink-workspace.png)

## <a name="move-your-automation-account"></a>Přesunutí účtu Automation

Nyní můžete přesunout svůj účet Automation a jeho Runbooky. 

1. V Azure Portal přejděte do skupiny prostředků svého účtu Automation. Vyberte **přesunout**  >  **přesunout do jiného předplatného**.

    ![Snímek stránky skupiny prostředků, přejít na jiné předplatné](../media/move-account/move-resources.png)

2. Vyberte prostředky ve vaší skupině prostředků, které chcete přesunout. Ujistěte se, že jste zahrnuli svůj účet Automation, Runbooky a Log Analytics prostředky pracovního prostoru.

## <a name="re-create-run-as-accounts"></a>Opětovné vytvoření účtů spustit jako

[Účty Spustit jako](../manage-runas-account.md) vytvoří instanční objekt v Azure Active Directory k ověřování pomocí prostředků Azure. Když změníte předplatné, účet Automation už nepoužívá existující účet Spustit jako. Postup opětovného vytvoření účtů spustit jako:

1. V rámci nového předplatného vyberte účet Automation a v části **Nastavení účtu** vyberte **účty Spustit jako** . Uvidíte, že účty Spustit jako jsou nyní zobrazeny jako nedokončené.

    ![Snímek obrazovky s účty Spustit jako, zobrazení neúplných](../media/move-account/run-as-accounts.png)

2. Odstraňte účty Spustit jako, a to tak, že na stránce **vlastnosti** vyberete **Odstranit** . 

    > [!NOTE]
    > Pokud nemáte oprávnění k vytvoření nebo zobrazení účtů spustit jako, zobrazí se následující zpráva: další `You do not have permissions to create an Azure Run As account (service principal) and grant the Contributor role to the service principal.` informace najdete v tématu [oprávnění požadovaná ke konfiguraci účtů spustit jako](../manage-runas-account.md#permissions).

3. Po odstranění účtů spustit jako vyberte v části **účet Spustit jako pro Azure** možnost **vytvořit** . 

4. Na stránce Přidat účet Spustit jako pro Azure vyberte **vytvořit** a vytvořte účet Spustit jako a instanční objekt. 

5. Opakujte výše uvedené kroky s účtem spustit jako pro Azure Classic.

## <a name="enable-features"></a>Povolit funkce

Po opětovném vytvoření účtů spustit jako je nutné znovu povolit funkce, které jste odebrali před přesunutím:

1. Pokud chcete zapnout Change Tracking a inventář, vyberte **Change Tracking a inventář** v účtu Automation. Zvolte pracovní prostor Log Analytics, který jste přesunuli, a vyberte **Povolit**.

2. Opakujte krok 1 pro Update Management.

    ![Snímek obrazovky pro opětovné povolení funkcí v přesunutém účtu Automation](../media/move-account/reenable-solutions.png)

3. Počítače, které jsou s vašimi funkcemi povolené, se zobrazí, když se připojíte ke stávajícímu pracovnímu prostoru Log Analytics. Chcete-li zapnout funkci Start/Stop VMs during off-hours, je nutné ji znovu povolit. V části **související prostředky** vyberte **Spustit nebo zastavit virtuální počítače** další  >  **informace o a povolte řešení**  >  **vytvořit** , abyste mohli nasazení spustit.

4. Na stránce Přidat řešení vyberte pracovní prostor Log Analytics a účet Automation.

    ![Snímek obrazovky nabídky Přidat řešení](../media/move-account/add-solution-vm.png)

5. Nakonfigurujte funkci, jak je popsáno v tématu [Start/Stop VMS during off-hours Overview](../automation-solution-vm-management.md).

## <a name="verify-the-move"></a>Ověřit přesunutí

Po dokončení přesunu ověřte, že jsou povolené níže uvedené možnosti. 

|Schopnost|Testy|Řešení potíží|
|---|---|---|
|Runbooky|Sada Runbook může úspěšně běžet a připojovat se k prostředkům Azure.|[Řešení potíží s runbooky](../troubleshoot/runbooks.md)
|Správa zdrojového kódu|Můžete spustit ruční synchronizaci v úložišti správy zdrojového kódu.|[Integrace správy zdrojového kódu](../source-control-integration.md)|
|Change Tracking a Inventory|Ověřte, že se na vašich počítačích zobrazují aktuální data inventáře.|[Řešení potíží se sledováním změn](../troubleshoot/change-tracking.md)|
|Správa aktualizací|Ověřte, zda jsou vaše počítače a zda jsou v pořádku.</br>Spusťte testovací nasazení aktualizace softwaru.|[Řešení potíží se správou aktualizací](../troubleshoot/update-management.md)|
|Sdílené prostředky|Ověřte, že vidíte všechny sdílené prostředky, jako jsou [přihlašovací údaje](../shared-resources/credentials.md) a [proměnné](../shared-resources/variables.md).|

## <a name="next-steps"></a>Další kroky

Další informace o přesouvání prostředků v Azure najdete v tématu [Přesunutí prostředků v Azure](../../azure-resource-manager/management/move-support-resources.md).
