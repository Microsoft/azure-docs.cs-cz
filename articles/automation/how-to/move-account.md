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
ms.openlocfilehash: 01f3995a80375f2deada13c36c48600243a17623
ms.sourcegitcommit: 1902adaa68c660bdaac46878ce2dec5473d29275
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2019
ms.locfileid: "57733180"
---
# <a name="move-your-automation-account-to-another-subscription"></a>Přesun účtu služby Automation do jiného předplatného

Azure poskytuje možnost některé prostředky přesunout do nové skupiny prostředků nebo předplatného pomocí stejného tenanta nativně prostřednictvím webu Azure portal, Powershellu, rozhraní příkazového řádku Azure nebo rozhraní REST API. Další informace o procesu najdete v tématu [přesunutí prostředků do nové skupiny prostředků nebo předplatného](../../azure-resource-manager/resource-group-move-resources.md). Účty Automation jsou jedním z prostředků, které je možné přesunout, ale existují speciální kroky potřebné při přesunu účtu Automation.

Jsou základní kroky pro přesun účtu služby Automation na:

* Odebrat řešení
* Zrušit propojení pracovního prostoru
* Přesun účtu služby Automation
* Odstraňte a vytvořte ho znovu jako účty
* Opětovné povolení řešení

## <a name="remove-solutions"></a>Odebrat řešení

Se zrušit propojení pracovního prostoru z vašeho účtu Automation, změn a inventáře, musí být Správa aktualizací a spuštění/zastavení virtuálních počítačů během vypnutí hodin řešení odebrány z pracovního prostoru.

Ve vaší skupině prostředků, vyberte jednotlivé **řešení** a klikněte na tlačítko **odstranit**. Na **odstranit prostředky** stránky, zkontrolujte prostředky, které chcete odebrat, klikněte na tlačítko **odstranit**.

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

Pro **spuštění/zastavení virtuálních počítačů** řešení, musíte také odebrat pravidla upozornění vytvořená tímto řešením.

Na webu Azure Portal, přejděte do vaší skupiny prostředků a vyberte **výstrahy** pod **monitorování**. Na **výstrahy** stránce **spravovat pravidla výstrah**

![Stránka zobrazující, že kliknete na spravovat upozornění pravidla výstrahy](../media/move-account/alert-rules.png)

Na **pravidla** stránky, měli byste vidět seznam všech výstrah nakonfigurované v této skupině prostředků. **Spouštění/zastavování virtuálních počítačů** řešení vytvoří 3 pravidla upozornění

* AutoStop_VM_Child
* ScheduledStartStop_Parent
* SequencedStartStop_Parent

Vyberte tyto 3 pravidla upozornění a klikněte na tlačítko **odstranit**. Tato akce odebere těchto pravidel upozornění.

![Pravidla stránky s pravidly vybrané a se odstranil](../media/move-account/delete-rules.png)

> [!NOTE]
> Pokud se nezobrazí všechna pravidla upozornění na **pravidla** stránce, změnit **stav** zobrazíte **zakázané** výstrahy podle možná jste zakázali je.

Po odebrání pravidel upozornění, budete muset odebrat skupinu akcí, který byl vytvořen pro oznámení pro řešení spuštění/zastavení virtuálních počítačů.

Na webu Azure Portal přejděte do **monitorování**vyberte **výstrahy**a klikněte na tlačítko **spravovat skupiny akcí**.

Vyberte skupinu akcí v seznamu, bude mít název **StartStop_VM_Notification**. Na stránce skupiny akcí klikněte na tlačítko **odstranit**

![Stránka akce skupiny, že kliknete na Odstranit](../media/move-account/delete-action-group.png)

Podobně můžete odstranit vaší skupiny akcí s využitím Powershellu. Tato akce se provádí pomocí [Remove-AzureRmActionGroup](/powershell/module/azurerm.insights/remove-azurermactiongroup) rutiny, jak je znázorněno v následujícím příkladu:

```azurepowershell-interactive
Remove-AzureRmActionGroup -ResourceGroupName <myResourceGroup> -Name StartStop_VM_Notification
```

## <a name="unlink-your-workspace"></a>Zrušit propojení pracovního prostoru

Na webu Azure Portal, přejděte k vaší **účtu Automation**. V části **související prostředky**, klikněte na tlačítko **pracovní prostor propojený**. Klikněte na tlačítko **zrušit propojení pracovního prostoru** zrušit propojení pracovního prostoru ve svém účtu Automation.

![Rušení propojení pracovního prostoru z účtu služby Automation](../media/move-account/unlink-workspace.png)

## <a name="move-your-automation-account"></a>Přesun účtu služby Automation

Po byly odebrány všechny předchozí položky, můžete pokračovat k odebrání účtu Automation a jeho sady runbook. Na webu Azure Portal přejděte do skupiny prostředků účtu Automation. Vyberte **přesunout** a potom **přesunout do jiného předplatného**.

![Výběr přesunout do jiného předplatného stránce skupiny prostředků](../media/move-account/move-resources.png)

Vyberte prostředky ve skupině prostředků, kterou chcete přesunout. Zajištění zahrnete vaše **účtu Automation**, **Runbook**, a **pracovní prostor Log Analytics** prostředky.

Po dokončení přesunutí existují další kroky, které musí být potřebný k získání všechno funguje.

## <a name="recreate-run-as-accounts"></a>Znovu spustit jako účty

[Účty spustit jako](../manage-runas-account.md) vytvoření instančního objektu v Azure Active Directory k ověření pomocí prostředků Azure. Při změně předplatných existující účet Spustit jako již není použitelný pro účet služby Automation.

Přejděte do účtu Automation v rámci nového předplatného a vyberte **účty spustit jako** pod **nastavení účtu**. Uvidíte, že zobrazit účty spustit jako nyní jako nedokončené.

![Účty spustit jako zobrazuje jako nekompletní](../media/move-account/run-as-accounts.png)

Klikněte na každý spustit jako účtu a na **vlastnosti** klikněte na **odstranit** odstranění účtu spustit jako.

> ! [POZNÁMKA] Pokud nemáte oprávnění k vytváření a zobrazit tato zpráva zobrazí účty spustit jako `You do not have permissions to create an Azure Run As account (service principal) and grant the Contributor role to the service principal.`. Další informace o oprávnění potřebná ke konfiguraci účtu spustit jako najdete v tématu [oprávnění vyžadované ke konfiguraci účtů spustit jako](../manage-runas-account.md#permissions).

Jakmile dojde k odstranění účtu spustit jako, klikněte na tlačítko **vytvořit** na **účet Spustit jako**. Na **přidat účet Spustit jako** klikněte na **vytvořit** k vytvoření účtu spustit jako a instanční objekt služby. Opakujte předchozí kroky, **klasický účet Spustit jako**.

## <a name="enable-solutions"></a>Povolení řešení

Poté, co byly znovu vytvořeny účty spustit jako, bude znovu povolíte řešení, která je odebrat před přesunem. Povolit **Change Tracking a Inventory** a **Update Management**, vyberte odpovídající možnost ve vašem účtu Automation. Zvolte pracovní prostor Log Analytics jste přesunuli přes a klikněte na tlačítko **povolit**.

![Opětovné povolení řešení ve vašem přesunutý účtu Automation](../media/move-account/reenable-solutions.png)

Svým počítačům, které se připojit s řešení se zobrazí znovu, protože se připojujete existující pracovní prostor Log Analytics.

Opětovné povolení spouštění/zastavování virtuálních počítačů špičku, budete potřebovat k opětovnému nasazení řešení. V části **související prostředky**vyberte **spuštění/zastavení virtuálního počítače**. Klikněte na tlačítko **Další informace o a povolte řešení** a klikněte na tlačítko **vytvořit** ke spuštění nasazení.

Na **přidat řešení** zvolte pracovní prostor Log Analytics a účet Automation.  

![Účty spustit jako zobrazuje jako nekompletní](../media/move-account/add-solution-vm.png)

Podrobné pokyny ke konfiguraci řešení, najdete v části [spouštění/zastavování virtuálních počítačů špičku ve službě Azure Automation](../automation-solution-vm-management.md)

## <a name="post-move-verification"></a>Ověření přesunutí příspěvku

Po dokončení přesunutí nezapomeňte ověřit různé scénáře ve vašem účtu Automation a ujistěte se, že vše funguje podle očekávání. Následující tabulka uvádí seznam úkolů, které by se měly ověřit po dokončení přesunu:

|Schopnost|Testy|Řešení potíží s odkaz|
|---|---|---|
|Runbooky|Sady Runbook můžete úspěšně spustit a připojte se k prostředkům Azure.|[Řešení potíží s runbooky](../troubleshoot/runbooks.md)
| Správa zdrojového kódu|Ruční synchronizaci můžete spustit na vaše úložiště správy zdrojového kódu.|[Integrace správy zdrojového kódu](../source-control-integration.md)|
|Change Tracking a Inventory|Ověřte, že vidíte aktuální data inventáře z vašich počítačů.|[Řešení Change Tracking](../troubleshoot/change-tracking.md)|
|Update Management|Ověření se zobrazí vaše počítače a že jsou v dobrém stavu</br>Spusťte test nasazení aktualizací softwaru.|[Řešení potíží s Update managementem](../troubleshoot/update-management.md)|

## <a name="next-steps"></a>Další postup

Další informace o přesun prostředků v Azure najdete v tématu [přesunutí prostředků v Azure](../../azure-resource-manager/move-support-resources.md)
