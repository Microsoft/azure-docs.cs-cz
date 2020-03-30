---
title: Přesunutí účtu Azure Automation na jiné předplatné
description: Tento článek popisuje, jak přesunout účet Automation do jiného předplatného
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/11/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 1aa759a2984764169eb28935e095d0f7c0f90c08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75969828"
---
# <a name="move-your-azure-automation-account-to-another-subscription"></a>Přesunutí účtu Azure Automation na jiné předplatné

Azure poskytuje možnost přesunout některé prostředky do nové skupiny prostředků nebo předplatného. Prostředky můžete přesunout přes portál Azure, PowerShell, rozhraní API Azure nebo rozhraní REST API. Další informace o procesu najdete v tématu [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](../../azure-resource-manager/management/move-resource-group-and-subscription.md).

Účty Azure Automation jsou jedním z prostředků, které lze přesunout. V tomto článku se dozvíte, jak přesunout účty automatizace do jiného prostředku nebo předplatného.

Kroky na vysoké úrovni k přesunutí účtu Automation jsou:

1. Odeberte svá řešení.
2. Odpojte pracovní prostor.
3. Přesuňte účet automatizace.
4. Odstraňte a znovu vytvořte účty Spustit jako.
5. Znovu povolte svá řešení.

## <a name="remove-solutions"></a>Odebrání řešení

Chcete-li odpojit pracovní prostor od účtu Automation, je nutné z pracovního prostoru odebrat tato řešení:
- **Change Tracking a Inventory**
- **Update Management**
- **Spuštění/zastavení virtuálních discích mimo pracovní dobu**

Ve skupině prostředků vyhledejte každé řešení a vyberte **odstranit**. Na stránce **Odstranit prostředky** potvrďte prostředky, které mají být odebrány, a vyberte **Odstranit**.

![Odstranění řešení z webu Azure Portal](../media/move-account/delete-solutions.png)

Můžete provést stejný úkol s [Odebrat-AzureRmResource](/powershell/module/azurerm.resources/remove-azurermresource) rutina, jak je znázorněno v následujícím příkladu:

```azurepowershell-interactive
$workspaceName = <myWorkspaceName>
$resourceGroupName = <myResourceGroup>
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM($workspaceName)" -ResourceGroupName $resourceGroupName
```

### <a name="additional-steps-for-startstop-vms"></a>Další kroky pro virtuální chod start/stop

Pro **řešení Start/Stop virtuálních her** je také nutné odebrat pravidla výstrah vytvořených řešením.

Na webu Azure Portal přejděte do skupiny prostředků a vyberte **Sledování** > **výstrah** > **Správa pravidel výstrah**.

![Stránka Upozornění zobrazující výběr pravidel správy výstrah](../media/move-account/alert-rules.png)

Na stránce **Pravidla** byste měli vidět seznam výstrah nakonfigurovaných v této skupině prostředků. Řešení **Start/Stop virtuálních her** vytvoří tři pravidla výstrah:

* AutoStop_VM_Child
* ScheduledStartStop_Parent
* SequencedStartStop_Parent

Vyberte tato tři pravidla výstrah a pak vyberte **Odstranit**. Tato akce odebere tato pravidla výstrah.

![Stránka pravidel požadující potvrzení o odstranění pro vybraná pravidla](../media/move-account/delete-rules.png)

> [!NOTE]
> Pokud na stránce **Pravidla** nevidíte žádná pravidla výstrah, změňte **stav** tak, aby **zobrazoval zakázané** výstrahy, protože jste je mohli zakázat.

Po odebrání pravidel výstrah odeberte skupinu akcí, která byla vytvořena pro oznámení řešení **řešení Start/Stop.**

Na webu Azure Portal vyberte **Monitor** > **Alerts** > **Manage skupinakcí**.

Vyberte **StartStop_VM_Notification** ze seznamu. Na stránce skupiny akcí vyberte **Odstranit**.

![Stránka skupiny akcí, vyberte odstranit](../media/move-account/delete-action-group.png)

Podobně můžete odstranit skupinu akcí pomocí prostředí PowerShell s rutinou [Remove-AzureRmActionGroup,](/powershell/module/azurerm.insights/remove-azurermactiongroup) jak je vidět v následujícím příkladu:

```azurepowershell-interactive
Remove-AzureRmActionGroup -ResourceGroupName <myResourceGroup> -Name StartStop_VM_Notification
```

## <a name="unlink-your-workspace"></a>Odpojení pracovního prostoru

Na portálu Azure vyberte pracovní**prostor propojený****s účtem** >  **automation účtu** > . Výběrem **možnosti Odpojítit pracovní prostor** odpojíte pracovní prostor od účtu automatizace.

![Odpojení pracovního prostoru od účtu automatizace](../media/move-account/unlink-workspace.png)

## <a name="move-your-automation-account"></a>Přesunutí účtu Automation

Po odebrání předchozích položek můžete pokračovat v odebrání účtu Automation a jeho runbooků. Na webu Azure Portal přejděte na skupinu prostředků vašeho účtu Automation. Vyberte **Přesunout** > **přesunout do jiného předplatného**.

![Stránka skupiny prostředků, přechod na jiné předplatné](../media/move-account/move-resources.png)

Vyberte prostředky ve skupině prostředků, které chcete přesunout. Ujistěte se, že jste **zahrnuli svůj účet Automation**, **runbook**a prostředky **pracovního prostoru Log Analytics.**

Po dokončení přesunu jsou nutné další kroky, aby vše fungovalo.

## <a name="re-create-run-as-accounts"></a>Znovu vytvořit účty Spustit jako

[Spustit jako účty](../manage-runas-account.md) vytvořit instanční objekt ve službě Azure Active Directory k ověření pomocí prostředků Azure. Když změníte odběry, účet automatizace již používá existující účet Spustit jako.

V novém předplatném přejděte na svůj účet Automation a v části **Nastavení účtu**vyberte Spustit **jako účty** . Uvidíte, že účty Spustit jako nyní jsou neúplné.

![Spustit jako účty jsou neúplné](../media/move-account/run-as-accounts.png)

Vyberte každý účet Spustit jako. Na stránce **Vlastnosti** vyberte **Odstranit,** chcete-li odstranit účet Spustit jako.

> [!NOTE]
> Pokud nemáte oprávnění k vytvoření nebo zobrazení účtů Spustit jako, zobrazí `You do not have permissions to create an Azure Run As account (service principal) and grant the Contributor role to the service principal.` se následující zpráva: Informace o oprávněních potřebných ke konfiguraci účtu Spustit jako naleznete v [tématu Oprávnění potřebná ke konfiguraci účtů Spustit jako](../manage-runas-account.md#permissions).

Po odstranění účtů Spustit jako vyberte **vytvořit** v části **Účet Azure Spustit jako**. Na stránce **Přidat účet Azure Run As** vyberte **Vytvořit** a vytvořte účet Spustit jako a instanční objekt služby. Opakujte předchozí kroky s **účtem Azure Classic Run As**.

## <a name="enable-solutions"></a>Povolení řešení

Po opětovném vytvoření účtů Spustit jako znovu povolíte řešení, která jste odebrali před přesunutím. Chcete-li zapnout **funkci Sledování změn a Správu inventáře** a **aktualizací**, vyberte příslušnou funkci v účtu automatizace. Zvolte pracovní prostor Log Analytics, přes který jste se přesunuli, a vyberte **Povolit**.

![Opětovné povolení řešení v přesunutém účtu Automation](../media/move-account/reenable-solutions.png)

Počítače, které jsou součástí s vašimi řešeními, budou viditelné, když jste připojili stávající pracovní prostor Log Analytics.

Chcete-li zapnout **spuštění a zastavení virtuálních počítačů** během mimo pracovní dobu řešení, budete muset znovu nasadit řešení. V části **Související prostředky**vyberte Možnost Spuštění a **zastavení virtuálních počítače** > **Další informace o a povolit řešení** > **Vytvořit** pro spuštění nasazení.

Na stránce **Přidat řešení** zvolte svůj účet pracovního prostoru analýzy protokolů a automatizace.

![Nabídka Přidat řešení](../media/move-account/add-solution-vm.png)

Podrobné pokyny ke konfiguraci řešení najdete v tématu [Spuštění a zastavení virtuálních počítačů během mimopracovní doby řešení v Azure Automation](../automation-solution-vm-management.md).

## <a name="post-move-verification"></a>Ověření po přesunutí

Po dokončení přesunu zkontrolujte následující seznam úkolů, které by měly být ověřeny:

|Schopnost|Testy|Odkaz pro řešení potíží|
|---|---|---|
|Runbooky|Runbook můžete úspěšně spustit a připojit se k prostředkům Azure.|[Poradce při potížích se sadkami Runbook](../troubleshoot/runbooks.md)
|Správa zdrojového kódu|Ruční synchronizaci můžete spustit v repo správy zdrojového kódu.|[Integrace správy zdrojového kódu](../source-control-integration.md)|
|Sledování změn a zásoby|Ověřte, zda se zobrazují aktuální data zásob z vašich počítačů.|[Poradce při potížích se sledováním změn](../troubleshoot/change-tracking.md)|
|Správa aktualizací|Ověřte, zda vidíte své počítače a jsou zdravé.</br>Spusťte nasazení aktualizace testovacího softwaru.|[Poradce při potížích se správou aktualizací](../troubleshoot/update-management.md)|
|Sdílené prostředky|Ověřte, zda se zobrazí všechny sdílené prostředky, například [přihlašovací údaje](../shared-resources/credentials.md), [proměnné](../shared-resources/variables.md)atd.|

## <a name="next-steps"></a>Další kroky

Další informace o přesouvání prostředků v Azure najdete v tématu [Přesun prostředků v Azure](../../azure-resource-manager/management/move-support-resources.md).
