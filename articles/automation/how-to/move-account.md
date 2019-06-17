---
title: Přesun účtu služby Azure Automation do jiného předplatného
description: Tento článek popisuje, jak přesunout svůj účet Automation do jiného předplatného
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/11/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 59d433bfb888eaa41cc8f66bdf3ad28c16efbe5c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "61302503"
---
# <a name="move-your-azure-automation-account-to-another-subscription"></a>Přesun účtu služby Azure Automation do jiného předplatného

Azure poskytuje možnost některé prostředky přesunout do nové skupiny prostředků nebo předplatného. Můžete přesunout prostředky prostřednictvím webu Azure portal, Powershellu, rozhraní příkazového řádku Azure nebo rozhraní REST API. Další informace o procesu najdete v tématu [přesunutí prostředků do nové skupiny prostředků nebo předplatného](../../azure-resource-manager/resource-group-move-resources.md). 

Účty Azure Automation se jeden z prostředků, které je možné přesunout. V tomto článku se dozvíte, kroky pro účty Automation přesunout do jiného prostředku nebo předplatnému.

Jsou základní kroky pro přesun účtu služby Automation:

1. Odeberte řešení.
2. Zrušit propojení pracovního prostoru.
3. Přesun účtu služby Automation.
4. Odstranit a znovu vytvořit účty spustit jako.
5. Znovu povolte řešení.

## <a name="remove-solutions"></a>Odebrat řešení

Se zrušit propojení pracovního prostoru ve svém účtu Automation, musí být tato řešení odebrat z pracovního prostoru:
- **Change Tracking a Inventory**
- **Správa aktualizací** 
- **Spuštění/zastavení virtuálních počítačů mimo špičku** 

Ve vaší skupině prostředků, vyhledejte každé řešení a vyberte **odstranit**. Na **odstranit prostředky** stránky, zkontrolujte prostředky, které chcete odebrat, vyberte **odstranit**.

![Odstraňte řešení na webu Azure Portal](../media/move-account/delete-solutions.png)

Můžete provést stejný úkol s [Remove-AzureRmResource](/powershell/module/azurerm.resources/remove-azurermresource) rutiny, jak je znázorněno v následujícím příkladu:

```azurepowershell-interactive
$workspaceName = <myWorkspaceName>
$resourceGroupName = <myResourceGroup>
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM($workspaceName)" -ResourceGroupName $resourceGroupName
```

### <a name="additional-steps-for-startstop-vms"></a>Další kroky pro spouštění/zastavování virtuálních počítačů

Pro **spouštění/zastavování virtuálních počítačů** řešení, musíte také odebrat pravidla upozornění vytvořená tímto řešením.

Na webu Azure Portal, přejděte do vaší skupiny prostředků a vyberte **monitorování** > **výstrahy** > **spravovat pravidla výstrah**.

![Upozorní stránky zobrazující výběr pravidel Správa výstrah](../media/move-account/alert-rules.png)

Na **pravidla** stránky, zobrazí se seznam výstrah nakonfigurované v této skupině prostředků. **Spouštění/zastavování virtuálních počítačů** řešení vytvoří tři pravidel upozornění:

* AutoStop_VM_Child
* ScheduledStartStop_Parent
* SequencedStartStop_Parent

Vyberte tyto tři pravidla upozornění a pak vyberte **odstranit**. Tato akce odebere těchto pravidel upozornění.

![Stránka pravidla vyžádání potvrzení odstranění pro vybraná pravidla](../media/move-account/delete-rules.png)

> [!NOTE]
> Pokud se nezobrazí všechna pravidla upozornění na **pravidla** stránce, změnit **stav** zobrazíte **zakázané** výstrahy, protože vám může mít zakázáno je.

Při odebrání pravidel upozornění odstranit skupinu akcí, pro kterou byla vytvořena **spouštění/zastavování virtuálních počítačů** řešení oznámení.

Na webu Azure Portal, vyberte **monitorování** > **výstrahy** > **spravovat skupiny akcí**.

Vyberte **StartStop_VM_Notification** ze seznamu. Na stránce skupiny akcí vyberte **odstranit**.

![Stránka akce skupiny, vyberte možnost odstranit](../media/move-account/delete-action-group.png)

Podobně můžete odstranit vaší skupiny akcí s použitím prostředí PowerShell se službou [Remove-AzureRmActionGroup](/powershell/module/azurerm.insights/remove-azurermactiongroup) rutiny, jak je znázorněno v následujícím příkladu:

```azurepowershell-interactive
Remove-AzureRmActionGroup -ResourceGroupName <myResourceGroup> -Name StartStop_VM_Notification
```

## <a name="unlink-your-workspace"></a>Zrušit propojení pracovního prostoru

Na webu Azure Portal, vyberte **účtu Automation** > **související prostředky** > **pracovní prostor propojený**. Vyberte **zrušit propojení pracovního prostoru** zrušit propojení pracovního prostoru ve svém účtu Automation.

![Zrušit propojení pracovního prostoru z účtu služby Automation](../media/move-account/unlink-workspace.png)

## <a name="move-your-automation-account"></a>Přesun účtu služby Automation

Po odebrání předchozí položky, můžete pokračovat k odebrání účtu Automation a jeho sady runbook. Na webu Azure Portal přejděte do skupiny prostředků účtu Automation. Vyberte **přesunout** > **přesunout do jiného předplatného**.

![Stránce skupiny prostředků, přesunout do jiného předplatného](../media/move-account/move-resources.png)

Vyberte prostředky ve skupině prostředků, kterou chcete přesunout. Zajištění zahrnete vaše **účtu Automation**, **Runbook**, a **pracovní prostor Log Analytics** prostředky.

Po dokončení přesunutí existují další kroky potřebné k vytvoření všech položek práce.

## <a name="re-create-run-as-accounts"></a>Znovu vytvořit účty spustit jako

[Účty spustit jako](../manage-runas-account.md) vytvoření instančního objektu v Azure Active Directory k ověření pomocí prostředků Azure. Při změně předplatných, účet Automation už používá existující účet Spustit jako.

Přejděte do vašeho účtu Automation v rámci nového předplatného a vyberte **účty spustit jako** pod **nastavení účtu**. Uvidíte, že zobrazit účty spustit jako nyní jako nedokončené.

![Účty spustit jako jsou neúplné](../media/move-account/run-as-accounts.png)

Vyberte jednotlivé účty spustit jako. Na **vlastnosti** stránce **odstranit** odstranění účtu spustit jako.

> [!NOTE]
> Pokud nemáte oprávnění k vytváření a zobrazení účtů spustit jako, se zobrazí následující zpráva: `You do not have permissions to create an Azure Run As account (service principal) and grant the Contributor role to the service principal.` Další informace o oprávnění potřebná ke konfiguraci účtu spustit jako najdete v tématu [oprávnění vyžadované ke konfiguraci účtů spustit jako](../manage-runas-account.md#permissions).

Když dojde k odstranění účtu spustit jako, vyberte **vytvořit** pod **účet Spustit jako pro Azure**. Na **přidání účtu Azure spustit jako** stránce **vytvořit** vytvořit instanční objekt účtu spustit jako a služby. Opakujte předchozí kroky, **účet Spustit jako pro Azure Classic**.

## <a name="enable-solutions"></a>Povolení řešení

Když znovu vytvoříte účty spustit jako, bude znovu povolíte řešení, které jste odebrali před přesunem. Chcete-li **Change Tracking a Inventory** a **Update Management**, vyberte odpovídající možnost ve vašem účtu Automation. Zvolte pracovní prostor Log Analytics jste přesunuli přes a vyberte **povolit**.

![Opětovné povolení řešení ve vašem přesunutý účtu Automation](../media/move-account/reenable-solutions.png)

Počítače, které se připojí pomocí vašeho řešení se budou viditelné, když jste se připojili existující pracovní prostor Log Analytics.

Chcete-li **spouštění/zastavování virtuálních počítačů** špičku, budete potřebovat k opětovnému nasazení řešení. V části **související prostředky**vyberte **spouštění/zastavování virtuálních počítačů** > **Další informace o a povolte řešení** > **vytvořit** ke spuštění nasazení.

Na **přidat řešení** zvolte váš pracovní prostor Log Analytics a účet Automation.  

![Přidání nabídky řešení](../media/move-account/add-solution-vm.png)

Podrobné pokyny ke konfiguraci řešení, najdete v části [spouštění/zastavování virtuálních počítačů špičku ve službě Azure Automation](../automation-solution-vm-management.md).

## <a name="post-move-verification"></a>Po přesunutí ověření

Po dokončení přesunutí najdete v následujícím seznamu úkolů, které by se měly ověřit:

|Schopnost|Testy|Řešení potíží s odkaz|
|---|---|---|
|Runbooky|Sady runbook můžete úspěšně spustit a připojte se k prostředkům Azure.|[Řešení potíží s runbooky](../troubleshoot/runbooks.md)
| Správy zdrojového kódu|Ruční synchronizaci můžete spustit na vaše úložiště správy zdrojového kódu.|[Integrace správy zdrojového kódu](../source-control-integration.md)|
|Change tracking a inventory|Ověřte, že vidíte aktuální data inventáře z vašich počítačů.|[Řešení potíží s řešení change tracking](../troubleshoot/change-tracking.md)|
|Správa aktualizací|Ověření se zobrazí vaše počítače a že jsou v pořádku.</br>Spusťte test nasazení aktualizací softwaru.|[Řešení potíží s update managementem](../troubleshoot/update-management.md)|

## <a name="next-steps"></a>Další postup

Další informace o přesun prostředků v Azure najdete v tématu [přesunutí prostředků v Azure](../../azure-resource-manager/move-support-resources.md).
