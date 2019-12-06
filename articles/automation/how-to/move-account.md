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
ms.openlocfilehash: 2d1c747a52a1e8dedd0b5ba411b673eee463a2b6
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849577"
---
# <a name="move-your-azure-automation-account-to-another-subscription"></a>Přesunutí účtu Azure Automation do jiného předplatného

Azure poskytuje možnost přesunout některé prostředky do nové skupiny prostředků nebo předplatného. Prostředky můžete přesunout pomocí Azure Portal, PowerShellu, rozhraní příkazového řádku Azure nebo REST API. Další informace o tomto procesu najdete v tématu [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](../../azure-resource-manager/resource-group-move-resources.md).

Účty Azure Automation jsou jedním z prostředků, které se dají přesunout. V tomto článku se seznámíte s postupem přesunutí účtů Automation do jiného prostředku nebo předplatného.

Mezi kroky vysoké úrovně přesunutí účtu Automation patří:

1. Odeberte svá řešení.
2. Odpojte svůj pracovní prostor.
3. Přesuňte účet Automation.
4. Odstraňte a znovu vytvořte účty Spustit jako.
5. Znovu povolte vaše řešení.

## <a name="remove-solutions"></a>Odebrat řešení

Pokud chcete odpojit svůj pracovní prostor od svého účtu Automation, musíte tato řešení odebrat z vašeho pracovního prostoru:
- **Change Tracking a inventář**
- **Správa aktualizací**
- **Spouštění/zastavování virtuálních počítačů v době mimo špičku**

Ve vaší skupině prostředků Najděte každé řešení a vyberte **Odstranit**. Na stránce **Odstranit prostředky** potvrďte prostředky, které se mají odebrat, a vyberte **Odstranit**.

![Odstranit řešení z Azure Portal](../media/move-account/delete-solutions.png)

Stejnou úlohu můžete provést pomocí rutiny [Remove-AzureRmResource](/powershell/module/azurerm.resources/remove-azurermresource) , jak je znázorněno v následujícím příkladu:

```azurepowershell-interactive
$workspaceName = <myWorkspaceName>
$resourceGroupName = <myResourceGroup>
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM($workspaceName)" -ResourceGroupName $resourceGroupName
```

### <a name="additional-steps-for-startstop-vms"></a>Další kroky pro spouštění a zastavování virtuálních počítačů

Pro řešení **spouštění a zastavování virtuálních počítačů** je také potřeba odebrat pravidla upozornění vytvořená řešením.

V Azure Portal přejdete do skupiny prostředků a vyberte **monitorování** > **výstrahy** > **Spravovat pravidla výstrah**.

![Stránka s výstrahami ukazující výběr pravidel pro správu pravidel výstrah](../media/move-account/alert-rules.png)

Na stránce **pravidla** by se měl zobrazit seznam výstrah nakonfigurovaných v této skupině prostředků. Řešení **spouštění a zastavování virtuálních počítačů** vytvoří tři pravidla upozornění:

* AutoStop_VM_Child
* ScheduledStartStop_Parent
* SequencedStartStop_Parent

Vyberte tato tři pravidla upozornění a pak vyberte **Odstranit**. Tato akce odstraní tato pravidla upozornění.

![Stránka pravidla požadující potvrzení odstranění pro vybraná pravidla](../media/move-account/delete-rules.png)

> [!NOTE]
> Pokud nevidíte žádná pravidla výstrahy na stránce **pravidla** , změňte **stav** tak, aby zobrazoval výstrahy **zakázané** , protože je možné, že jste je zakázali.

Po odebrání pravidel upozornění odeberte skupinu akcí vytvořenou pro oznámení řešení **Spustit nebo zastavit virtuální počítače** .

V Azure Portal vyberte **sledovat** > **výstrahy** > **Spravovat skupiny akcí**.

V seznamu vyberte **StartStop_VM_Notification** . Na stránce skupina akcí vyberte **Odstranit**.

![Stránka skupiny akcí, vyberte Odstranit](../media/move-account/delete-action-group.png)

Podobně můžete odstranit skupinu akcí pomocí prostředí PowerShell pomocí rutiny [Remove-AzureRmActionGroup](/powershell/module/azurerm.insights/remove-azurermactiongroup) , jak je vidět v následujícím příkladu:

```azurepowershell-interactive
Remove-AzureRmActionGroup -ResourceGroupName <myResourceGroup> -Name StartStop_VM_Notification
```

## <a name="unlink-your-workspace"></a>Zrušit propojení pracovního prostoru

V Azure Portal vyberte **účet Automation** > **související prostředky** > **propojený pracovní prostor**. Vyberte zrušit **propojení pracovního prostoru** , abyste mohli zrušit propojení pracovního prostoru s vaším účtem Automation.

![Zrušení propojení pracovního prostoru s účtem Automation](../media/move-account/unlink-workspace.png)

## <a name="move-your-automation-account"></a>Přesunutí účtu Automation

Po odebrání předchozích položek můžete i nadále odebírat svůj účet Automation a jeho Runbooky. V Azure Portal přejděte do skupiny prostředků svého účtu Automation. Vyberte **přesunout** > **přesunout do jiného předplatného**.

![Stránka skupiny prostředků, přejít na jiné předplatné](../media/move-account/move-resources.png)

Vyberte prostředky ve vaší skupině prostředků, které chcete přesunout. Ujistěte se, že jste zahrnuli prostředky **účtu Automation**, **Runbook**a **Log Analytics pracovního prostoru** .

Po dokončení přesunu je potřeba provést další kroky, aby všechno fungovalo.

## <a name="re-create-run-as-accounts"></a>Opětovné vytvoření účtů spustit jako

[Účty Spustit jako](../manage-runas-account.md) vytvoří instanční objekt v Azure Active Directory k ověřování pomocí prostředků Azure. Když změníte předplatné, účet Automation už nepoužívá existující účet Spustit jako.

V rámci nového předplatného vyberte účet Automation a v části **Nastavení účtu**vyberte **účty Spustit jako** . Uvidíte, že účty Spustit jako jsou nyní zobrazeny jako nedokončené.

![Účty Spustit jako jsou neúplné](../media/move-account/run-as-accounts.png)

Vyberte každý účet Spustit jako. Na stránce **vlastnosti** vyberte **Odstranit** a odstraňte účet Spustit jako.

> [!NOTE]
> Pokud nemáte oprávnění k vytvoření nebo zobrazení účtů spustit jako, zobrazí se následující zpráva: `You do not have permissions to create an Azure Run As account (service principal) and grant the Contributor role to the service principal.` Chcete-li získat informace o oprávněních potřebných ke konfiguraci účtu Spustit jako, přečtěte si téma [oprávnění vyžadovaná ke konfiguraci účtů spustit jako](../manage-runas-account.md#permissions).

Po odstranění účtů spustit jako vyberte **vytvořit** v části **účet Spustit jako pro Azure**. Na stránce **Přidat účet Spustit jako pro Azure** vyberte **vytvořit** a vytvořte účet Spustit jako a instanční objekt. Předchozí kroky opakujte s **účtem spustit jako pro Azure Classic**.

## <a name="enable-solutions"></a>Povolení řešení

Po opětovném vytvoření účtů spustit jako znovu povolíte řešení, která jste odebrali před přesunutím. Pokud chcete zapnout **Change Tracking a inventář** a **Update Management**, vyberte příslušnou funkci v účtu Automation. Zvolte pracovní prostor Log Analytics, který jste přesunuli, a vyberte **Povolit**.

![Opětovné povolení řešení na přesunutém účtu Automation](../media/move-account/reenable-solutions.png)

Počítače, které jsou připojené s vašimi řešeními, se budou zobrazovat, až budete připojeni k existujícímu pracovnímu prostoru Log Analytics.

Pokud chcete zapnout **a vypnout virtuální počítače** v době mimo špičku, budete muset řešení znovu nasadit. V části **související prostředky**vyberte **Spustit nebo zastavit virtuální počítače** > další **informace o a povolení řešení** > **Vytvoření** pro spuštění nasazení.

Na stránce **Přidat řešení** vyberte pracovní prostor Log Analytics a účet Automation.

![Přidat nabídku řešení](../media/move-account/add-solution-vm.png)

Podrobné pokyny týkající se konfigurace řešení najdete v tématu [Start/Stop VMS during off-hours řešení v Azure Automation](../automation-solution-vm-management.md).

## <a name="post-move-verification"></a>Ověření po přesunutí

Po dokončení přesunu se podívejte na následující seznam úloh, které se mají ověřit:

|Schopnost|Testy|Odkaz pro řešení potíží|
|---|---|---|
|Runbooky|Sada Runbook může úspěšně běžet a připojovat se k prostředkům Azure.|[Řešení potíží s runbooky](../troubleshoot/runbooks.md)
|Správa zdrojového kódu|V úložišti správy zdrojových kódů můžete spustit ruční synchronizaci.|[Integrace správy zdrojového kódu](../source-control-integration.md)|
|Sledování změn a inventáře|Ověřte, že se na vašich počítačích zobrazují aktuální data inventáře.|[Řešení potíží se sledováním změn](../troubleshoot/change-tracking.md)|
|Správa aktualizací|Ověřte, že jsou vaše počítače v pořádku.</br>Spusťte testovací nasazení aktualizace softwaru.|[Řešení potíží se správou aktualizací](../troubleshoot/update-management.md)|
|Sdílené prostředky|Ověřte, že vidíte všechny sdílené prostředky, jako jsou například [přihlašovací údaje](../shared-resources/credentials.md), [proměnné](../shared-resources/variables.md)atd.|

## <a name="next-steps"></a>Další kroky

Další informace o přesouvání prostředků v Azure najdete v tématu [Přesunutí prostředků v Azure](../../azure-resource-manager/move-support-resources.md).
