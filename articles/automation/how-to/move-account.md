---
title: Přesunutí účtu Azure Automation na jiné předplatné
description: Tento článek popisuje, jak přesunout účet Automatizace do jiného předplatného.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/11/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2dbe7dc171b6e0ec81c99a460a4f997eeb9e27a5
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681891"
---
# <a name="move-your-azure-automation-account-to-another-subscription"></a>Přesunutí účtu Azure Automation na jiné předplatné

Azure Automation umožňuje přesunout některé prostředky do nové skupiny prostředků nebo předplatného. Prostředky můžete přesunout přes portál Azure, PowerShell, rozhraní API Azure nebo rozhraní REST API. Další informace o procesu najdete v tématu [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](../../azure-resource-manager/management/move-resource-group-and-subscription.md).

Účet Azure Automation je jedním z prostředků, které můžete přesunout. V tomto článku se naučíte přesunout účty automatizace do jiného prostředku nebo předplatného. Kroky na vysoké úrovni pro přesunutí účtu Automation jsou:

1. Odeberte svá řešení.
2. Odpojte pracovní prostor.
3. Přesuňte účet automatizace.
4. Odstraňte a znovu vytvořte účty Spustit jako.
5. Znovu povolte svá řešení.

>[!NOTE]
>Tento článek je aktualizovaný a využívá nový modul Az Azure PowerShellu. Můžete dál využívat modul AzureRM, který bude dostávat opravy chyb nejméně do prosince 2020. Další informace o kompatibilitě nového modulu Az a modulu AzureRM najdete v tématu [Seznámení s novým modulem Az Azure PowerShellu](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pokyny k instalaci modulu AZ na pracovníka hybridní sady Runbook najdete [v tématu Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). U vašeho účtu Automation můžete aktualizovat moduly na nejnovější verzi pomocí [funkce Jak aktualizovat moduly Azure PowerShellu v Azure Automation](../automation-update-azure-modules.md).

## <a name="remove-solutions"></a>Odebrání řešení

Chcete-li zrušit propojení pracovního prostoru od účtu Automation, je nutné odebrat tato řešení z pracovního prostoru:

- Change Tracking a Inventory
- Update Management
- Spuštění/zastavení virtuálních počítačů mimo špičku

1. Na webu Azure Portal vyhledejte svou skupinu prostředků.
2. Najděte každé řešení a na stránce Odstranit prostředky klikněte na **Odstranit.**

    ![Odstranění řešení z webu Azure Portal](../media/move-account/delete-solutions.png)

    Pokud chcete, můžete odstranit řešení pomocí rutiny [Remove-AzResource:](https://docs.microsoft.com/powershell/module/Az.Resources/Remove-AzResource?view=azps-3.7.0)

    ```azurepowershell-interactive
    $workspaceName = <myWorkspaceName>
    $resourceGroupName = <myResourceGroup>
    Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking($workspaceName)" -ResourceGroupName $resourceGroupName
    Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates($workspaceName)" -ResourceGroupName $resourceGroupName
    Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM($workspaceName)" -ResourceGroupName $resourceGroupName
    ```

### <a name="remove-alert-rules-for-the-startstop-vms-during-off-hours-solution"></a>Odebrání pravidel výstrah pro řešení Spuštění a zastavení virtuálních her mimo pracovní dobu

Pro řešení Spuštění a zastavení virtuálních her mimo pracovní dobu je také nutné odebrat pravidla výstrah vytvořená řešením.

1. Na webu Azure Portal přejděte do skupiny prostředků a vyberte **Sledování** > **výstrah** > **Správa pravidel výstrah**.

![Stránka Upozornění zobrazující výběr pravidel správy výstrah](../media/move-account/alert-rules.png)

2. Na stránce Pravidla byste měli vidět seznam výstrah nakonfigurovaných v této skupině prostředků. Řešení vytváří tato pravidla:

    * AutoStop_VM_Child
    * ScheduledStartStop_Parent
    * SequencedStartStop_Parent

3. Vyberte pravidla po jednom a kliknutím na **Odstranit** je odeberte.

    ![Stránka pravidel požadující potvrzení o odstranění pro vybraná pravidla](../media/move-account/delete-rules.png)

    > [!NOTE]
    > Pokud na stránce Pravidla nevidíte žádná pravidla výstrah, změňte pole **Stav** na Zakázáno, aby se zobrazovala zakázaná upozornění, protože jste je pravděpodobně zakázali.

4. Po odebrání pravidel výstrah je nutné odebrat skupinu akcí vytvořenou pro virtuální chod y Start/Stop během oznámení o řešení mimo pracovní dobu. Na webu Azure Portal vyberte **Monitor** > **Alerts** > **Manage skupinakcí**.

5. Vyberte **StartStop_VM_Notification**. 

6. Na stránce skupiny akcí vyberte **Odstranit**.

    ![Stránka skupiny akcí](../media/move-account/delete-action-group.png)

    Pokud chcete, můžete skupinu akcí odstranit pomocí rutiny [Odebrat azactiongroup:](https://docs.microsoft.com/powershell/module/az.monitor/remove-azactiongroup?view=azps-3.7.0)

    ```azurepowershell-interactive
    Remove-AzActionGroup -ResourceGroupName <myResourceGroup> -Name StartStop_VM_Notification
    ```

## <a name="unlink-your-workspace"></a>Odpojení pracovního prostoru

Nyní můžete pracovní prostor odpojit:

1. Na portálu Azure vyberte pracovní**prostor propojený****s účtem** >  **automation účtu** > . 

2. Výběrem **možnosti Odpojítit pracovní prostor** odpojíte pracovní prostor od účtu automatizace.

    ![Odpojení pracovního prostoru od účtu automatizace](../media/move-account/unlink-workspace.png)

## <a name="move-your-automation-account"></a>Přesunutí účtu Automation

Nyní můžete přesunout účet Automation a jeho runbooky. 

1. Na webu Azure Portal přejděte na skupinu prostředků vašeho účtu Automation. Vyberte **Přesunout** > **přesunout do jiného předplatného**.

    ![Stránka skupiny prostředků, přechod na jiné předplatné](../media/move-account/move-resources.png)

2. Vyberte prostředky ve skupině prostředků, které chcete přesunout. Ujistěte se, že zahrnete svůj účet Automation, sady Runbook a prostředky pracovního prostoru Log Analytics.

## <a name="recreate-run-as-accounts"></a>Znovu vytvořit účty Spustit jako

[Spustit jako účty](../manage-runas-account.md) vytvořit instanční objekt ve službě Azure Active Directory k ověření pomocí prostředků Azure. Když změníte odběry, účet automatizace již používá existující účet Spustit jako. Opětovné vytvoření účtů Spustit jako:

1. V novém předplatném přejděte na svůj účet Automation a v části **Nastavení účtu**vyberte Spustit **jako účty** . Uvidíte, že účty Spustit jako nyní jsou neúplné.

    ![Spustit jako účty jsou neúplné](../media/move-account/run-as-accounts.png)

2. Odstraňte účty Spustit jako jeden po druhém pomocí tlačítka **Odstranit** na stránce Vlastnosti. 

    > [!NOTE]
    > Pokud nemáte oprávnění k vytvoření nebo zobrazení účtů Spustit jako, zobrazí `You do not have permissions to create an Azure Run As account (service principal) and grant the Contributor role to the service principal.` se následující zpráva: Informace o oprávněních potřebných ke konfiguraci účtu Spustit jako naleznete v [tématu Oprávnění potřebná ke konfiguraci účtů Spustit jako](../manage-runas-account.md#permissions).

3. Po odstranění účtů Spustit jako vyberte **Vytvořit** v části **Účet Azure Spustit jako**. 

4. Na stránce Přidat účet Azure Run As vyberte **Vytvořit** a vytvořte účet Spustit jako a instanční objekt služby. 

5. Opakujte výše uvedené kroky s účtem Azure Classic Run As.

## <a name="enable-solutions"></a>Povolení řešení

Po opětovném vytvoření účtů Spustit jako je nutné znovu povolit řešení, která jste odebrali před přesunutím: 

1. Pokud chcete zapnout řešení Sledování změn a zásob, vúčtu Automation vyberte Sledování změn a Inventář. Zvolte pracovní prostor Log Analytics, který jste přesunuli, a vyberte **Povolit**.

2. Opakujte krok 1 pro řešení správy aktualizací.

    ![Opětovné povolení řešení v přesunutém účtu Automation](../media/move-account/reenable-solutions.png)

3. Počítače, které jsou součástí s vašimi řešeními jsou viditelné, když jste připojili existující pracovní prostor Log Analytics. Chcete-li zapnout řešení Start/Stop virtuálních počítačích během mimo pracovní dobu, je nutné znovu nasadit řešení. V části **Související prostředky**vyberte Možnost Spuštění a **zastavení virtuálních počítače** > **Další informace o a povolit řešení** > **Vytvořit** pro spuštění nasazení.

4. Na stránce Přidat řešení zvolte pracovní prostor analýzy protokolů a účet Automatizace.

    ![Nabídka Přidat řešení](../media/move-account/add-solution-vm.png)

5. Nakonfigurujte řešení, jak je popsáno v [řešení Start/Stop VM mimo pracovní dobu v Azure Automation](../automation-solution-vm-management.md).

## <a name="verify-the-move"></a>Ověření přesunu

Po dokončení přesunu ověřte, zda jsou povoleny níže uvedené funkce. 

|Schopnost|Testy|Poradce při potížích|
|---|---|---|
|Runbooky|Runbook můžete úspěšně spustit a připojit se k prostředkům Azure.|[Řešení potíží s runbooky](../troubleshoot/runbooks.md)
|Správa zdrojového kódu|Ruční synchronizaci můžete spustit v úložišti správy zdrojového kódu.|[Integrace správy zdrojového kódu](../source-control-integration.md)|
|Sledování změn a zásoby|Ověřte, zda se zobrazují aktuální data zásob z vašich počítačů.|[Poradce při potížích se sledováním změn](../troubleshoot/change-tracking.md)|
|Správa aktualizací|Ověřte, zda se vaše počítače zobrazují a zda jsou v pořádku.</br>Spusťte nasazení aktualizace testovacího softwaru.|[Poradce při potížích se správou aktualizací](../troubleshoot/update-management.md)|
|Sdílené prostředky|Ověřte, zda se zobrazí všechny sdílené prostředky, například [přihlašovací údaje](../shared-resources/credentials.md), [proměnné](../shared-resources/variables.md)a podobně.|

## <a name="next-steps"></a>Další kroky

Další informace o přesouvání prostředků v Azure najdete v tématu [Přesun prostředků v Azure](../../azure-resource-manager/management/move-support-resources.md).
