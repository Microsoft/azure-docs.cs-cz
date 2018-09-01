---
title: Plány ve službě Azure Automation
description: Plány služeb automatizace se používají k plánování runbooků ve službě Azure Automation, aby se spouštěla automaticky. Popisuje, jak vytvářet a spravovat plán v tak, aby může automaticky spustit runbook v určitou dobu nebo podle plánu opakování.
services: automation
ms.service: automation
ms.component: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 05/08/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 211d79f387697ce850ac645ef65338c216e2bd76
ms.sourcegitcommit: a3a0f42a166e2e71fa2ffe081f38a8bd8b1aeb7b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/01/2018
ms.locfileid: "43382191"
---
# <a name="scheduling-a-runbook-in-azure-automation"></a>Naplánování runbooku v Azure Automation

Plánování runbooku ve službě Azure Automation ke spuštění v určenou dobu, můžete ho propojit s jedním nebo víc plány. Plán můžete nakonfigurovat na spuštění jednou nebo vyskytovat nadále hodinové nebo denní plán pro sady runbook na portálu Azure portal. Můžete také naplánovat je pro každý týden, měsíc, konkrétní dny v týdnu nebo dny v měsíci nebo konkrétní den v měsíci. Sady runbook lze propojit s více plánů a plán může mít víc runbooků k němu.

> [!NOTE]
> Plány Azure Automation DSC konfigurace aktuálně nepodporují.

## <a name="windows-powershell-cmdlets"></a>Rutiny Windows Powershellu

Rutiny v následující tabulce se používají k vytváření a správě plány pomocí Windows Powershellu ve službě Azure Automation. Se dodávají jako součást [modulu Azure PowerShell](/powershell/azure/overview).

| Rutiny | Popis |
|:--- |:--- |
| [Get-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/get-azurermautomationschedule) |Načte plán. |
| [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) |Vytvoří nový plán. |
| [Remove-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/remove-azurermautomationschedule) |Odstraní plán. |
| [Set-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/set-azurermautomationschedule) |Nastaví vlastnosti pro existující plán. |
| [Get-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/get-azurermautomationscheduledrunbook) |Načte naplánované runbooky. |
| [Register-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/register-azurermautomationscheduledrunbook) |Přidruží sady runbook k plánu. |
| [Unregister-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/unregister-azurermautomationscheduledrunbook) |Dissociates sady runbook z plánu. |

## <a name="creating-a-schedule"></a>Vytvoření plánu

Nový plán pro sady runbook můžete vytvořit na webu Azure Portal nebo pomocí Windows Powershellu.

> [!NOTE]
> Při spuštění novou naplánovanou úlohu Azure Automation používá nejnovější moduly ve vašem účtu Automation.  Aby se zabránilo dopadu na vaše sady runbook a procesy, které jsou automatizaci, měli byste otestovat nejprve všechny runbooky, který mají propojené plány se účet Automation, který je vyhrazený pro testování.  Tato operace ověří vaše naplánované runbooky dál fungují správně, a pokud ne, můžete další řešení potíží a použít požadované změny před migrací verze aktualizované sady runbook do produkčního prostředí.
> Svůj účet Automation nezíská automaticky všem novým verzím modulů, pokud jste je aktualizovat ručně tak, že vyberete [aktualizace modulů Azure](automation-update-azure-modules.md) možnost **moduly**.

### <a name="to-create-a-new-schedule-in-the-azure-portal"></a>Chcete-li vytvořit nový plán na webu Azure Portal

1. Na portálu Azure ve svém účtu automation vyberte **plány** části **sdílené prostředky** na levé straně.
1. Klikněte na tlačítko **přidat plán** v horní části stránky.
1. Na **nový plán** podokně zadejte **název** a volitelně **popis** nového plánu.
1. Vyberte, jestli plán se spustí jednorázově nebo podle plánu opakované tak, že vyberete **jednou** nebo **opakování**. Pokud vyberete **jednou** zadat **počáteční čas**a potom klikněte na tlačítko **vytvořit**. Pokud vyberete **opakování**, zadejte **počáteční čas** a jak často chcete runbooku opakovat - podle frekvence **hodinu**, **den**, **týden**, nebo **měsíc**. Pokud vyberete **týden** nebo **měsíc** z rozevíracího seznamu **možnost opakování** se zobrazí v podokně a po výběru **možnost opakování** podokně se zobrazí a můžete vybrat den v týdnu, pokud jste vybrali **týden**. Pokud jste vybrali **měsíc**, můžete použít podle **dny v týdnu** nebo konkrétní dny v měsíci v kalendáři a nakonec chcete spustit poslední den v měsíci, nebo Ne a potom klikněte na **OK**.

### <a name="to-create-a-new-schedule-with-windows-powershell"></a>K vytvoření nového plánu pomocí prostředí Windows PowerShell

Můžete použít [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) rutina pro vytvoření plány. Můžete zadat čas zahájení pro plán a frekvenci, který se má spustit.

Následující vzorové příkazy ukazují, jak vytvořit plán pro 15. dne a 30 v měsíci pomocí rutiny Azure Resource Manageru.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
New-AzureRMAutomationSchedule –AutomationAccountName $automationAccountName –Name `
$scheduleName -StartTime "7/01/2016 15:30:00" -MonthInterval 1 `
-DaysOfMonth Fifteenth,Thirtieth -ResourceGroupName "ResourceGroup01"
```

## <a name="linking-a-schedule-to-a-runbook"></a>Připojení plánu k runbooku

Sady runbook lze propojit s více plánů a plán může mít víc runbooků k němu. Pokud runbook obsahuje parametry, můžete zadat hodnoty pro ně. Musíte zadat hodnoty všech povinných parametrů a může poskytnout hodnoty pro všechny volitelné parametry. Tyto hodnoty se používají při každém spuštění runbooku pomocí tohoto plánu. Můžete připojit stejnou sadu runbook na jiný plán a zadejte jiné hodnoty parametru.

### <a name="to-link-a-schedule-to-a-runbook-with-the-azure-portal"></a>Připojení plánu k runbooku pomocí portálu Azure portal

1. Na portálu Azure ve svém účtu automation vyberte **sady Runbook** části **automatizace procesů** na levé straně.
1. Klikněte na název runbooku, naplánování.
1. Pokud sada runbook není aktuálně propojený s plánu, jsou nabízí možnost vytvořit nový plán nebo odkaz k existujícímu plánu.
1. Pokud má runbook parametry, můžete vybrat možnost **upravit nastavení spouštění (výchozí: Azure)** a **parametry** podokně se zobrazí, ve kterém můžete zadat informace odpovídajícím způsobem.

### <a name="to-link-a-schedule-to-a-runbook-with-windows-powershell"></a>Připojení plánu k runbooku pomocí prostředí Windows PowerShell

Můžete použít [Register-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/register-azurermautomationscheduledrunbook) rutiny pro připojení plánu. Hodnoty pro parametry runbooku můžete zadat pomocí parametru parametry. Další informace o zadání hodnot parametrů najdete v tématu [spuštění Runbooku ve službě Azure Automation](automation-starting-a-runbook.md).
Následující vzorové příkazy znázorňují postup připojení plánu k runbooku pomocí rutiny Azure Resource Manageru s parametry.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$runbookName = "Test-Runbook"
$scheduleName = "Sample-DailySchedule"
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Register-AzureRmAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
–Name $runbookName –ScheduleName $scheduleName –Parameters $params `
-ResourceGroupName "ResourceGroup01"
```

## <a name="scheduling-runbooks-more-frequently"></a>Plánování runbooků častěji

Interval nejčastější plánu ve službě Azure Automation lze nakonfigurovat pro je jedna hodina. Pokud budete potřebovat plány, které se spouští častěji než, existují dvě možnosti:

* Vytvoření [webhooku](automation-webhooks.md) pro sadu runbook a použití [Azure Scheduleru](../scheduler/scheduler-get-started-portal.md) pro volání webhooku. Azure Scheduler poskytuje další detailnější členitosti při definování plánu.

* Vytvořte čtyři plány všechna spuštění do 15 minut od sebe navzájem spuštění jednou za hodinu. Tento scénář umožňuje spuštění každých 15 minut pomocí různých plánů sady runbook.

## <a name="disabling-a-schedule"></a>Zakázání plánu

Při zakázání plánu libovolné sady runbook je již propojen se spouští podle tohoto plánu. Můžete ručně zakázat plán nebo nastavit čas vypršení platnosti pro plány s frekvencí při jejich vytváření. Když je dosaženo času vypršení platnosti, plán zakázán.

### <a name="to-disable-a-schedule-from-the-azure-portal"></a>Zakázání plánu z portálu Azure portal

1. Na portálu Azure ve svém účtu Automation vyberte **plány** části **sdílené prostředky** na levé straně.
1. Klikněte na název plánu a otevřete tak podokno Podrobnosti.
1. Změna **povolené** k **ne**.

### <a name="to-disable-a-schedule-with-windows-powershell"></a>Zakázání plánu pomocí prostředí Windows PowerShell

Můžete použít [Set-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/set-azurermautomationschedule) rutiny, chcete-li změnit vlastnosti existujícího plánu. Chcete-li zakázat plán, zadejte **false** pro **IsEnabled** parametru.

Následující vzorové příkazy ukazují, jak zakázat plán pro sady runbook pomocí rutiny Azure Resource Manageru.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Set-AzureRmAutomationSchedule –AutomationAccountName $automationAccountName `
–Name $scheduleName –IsEnabled $false -ResourceGroupName "ResourceGroup01"
```

## <a name="next-steps"></a>Další postup

* Začínáme se sadami runbook ve službě Azure Automation najdete v článku [spuštění Runbooku ve službě Azure Automation](automation-starting-a-runbook.md)
