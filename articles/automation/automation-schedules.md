---
title: Plánů ve službě Azure Automation
description: Plány služeb automatizace se používají k plánování sady runbook ve službě Azure Automation na automatické spouštění. Popisuje, jak vytvořit a spravovat plánu v tak, aby může automaticky spustit sadu runbook v určitém čase nebo podle plánu opakování.
services: automation
ms.service: automation
ms.component: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 05/08/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 6c7bd4d4249d304ee7c1df4ae4b8fc0af476b99c
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="scheduling-a-runbook-in-azure-automation"></a>Naplánování runbooku v Azure Automation

Pokud chcete naplánovat spuštění runbooku ve službě Azure Automation spustit v zadanou dobu, můžete ho propojit s jedním nebo víc plány. Plán můžete nakonfigurovat pro spuštění jednou nebo na nadále hodinové nebo denní plán pro sady runbook na portálu Azure. Můžete také naplánovat je na týdně, měsíčně, konkrétní dny v týdnu nebo dny v měsíci nebo určitý den v měsíci. Sady runbook mohou být spojeny s víc plány a plán může mít víc runbooků.

> [!NOTE]
> Plány konfigurace Azure Automation DSC aktuálně nepodporují.

## <a name="windows-powershell-cmdlets"></a>Rutiny prostředí Windows PowerShell

Rutiny v následující tabulce se používají k vytváření a správě plánů v prostředí Windows PowerShell ve službě Azure Automation. Se dodávají jako součást [modul Azure PowerShell](/powershell/azure/overview).

| Rutiny | Popis |
|:--- |:--- |
| [Get-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/get-azurermautomationschedule) |Načte plán. |
| [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) |Vytvoří nový plán. |
| [Remove-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/remove-azurermautomationschedule) |Odebere plánu. |
| [Set-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/set-azurermautomationschedule) |Nastaví vlastnosti pro existující plán. |
| [Get-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/set-azurermautomationscheduledrunbook) |Načte naplánovat sady runbook. |
| [Register-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/register-azurermautomationscheduledrunbook) |Přidruží sady runbook s plánem. |
| [Unregister-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/unregister-azurermautomationscheduledrunbook) |Dissociates sady runbook z plánu. |

## <a name="creating-a-schedule"></a>Vytvoření plánu

Můžete vytvořit nový plán pro sady runbook na portálu Azure nebo pomocí prostředí Windows PowerShell.

> [!NOTE]
> Při spuštění novou naplánovanou úlohu automatizace Azure používá nejnovější modulů ve vašem účtu Automation.  Nechcete-li vaše sady runbook a procesy, které budou automatizovat, které mají vliv, byste měli nejprve otestovat všechny sady runbook, které jste propojili plány s účet Automation, který je vyhrazený pro testování.  Ověří se vaše naplánované sady runbook i nadále fungovat správně a pokud ne, můžete další řešení a použít všechny změny požadované před migrací verze aktualizované sady runbook do produkčního prostředí.
> Váš účet Automation nezíská automaticky nové verze modulů Pokud budete mít aktualizovat ručně tak, že vyberete [moduly Azure aktualizace](automation-update-azure-modules.md) možnost z **moduly**.

### <a name="to-create-a-new-schedule-in-the-azure-portal"></a>Chcete-li vytvořit nový plán na portálu Azure

1. Na portálu Azure z vašeho účtu automation vyberte **plány** části **sdílené prostředky** na levé straně.
1. Klikněte na tlačítko **přidat plán** v horní části stránky.
1. Na **nový plán** podokně zadejte **název** a volitelně **popis** pro nový plán.
1. Vyberte jestli plán se spustí jednorázově nebo podle plánu opakovaném výběrem **jednou** nebo **opakování**. Pokud vyberete **jednou** zadejte **počáteční čas**a potom klikněte na **vytvořit**. Pokud vyberete **opakování**, zadejte **počáteční čas** a jak často chcete runbooku opakovat - nástrojem frekvenci **hodinu**, **den**, **týden**, nebo pomocí **měsíc**. Pokud vyberete **týden** nebo **měsíc** z rozevíracího seznamu **opakování možnost** se zobrazí v podokně a při výběru, **opakování možnost** podokně se zobrazí a den v týdnu můžete vybrat, pokud jste vybrali **týden**. Pokud jste vybrali **měsíc**, můžete **dny v týdnu** nebo konkrétní dny v měsíci v kalendáři a nakonec chcete spustit poslední den v měsíci, nebo Ne, a potom klikněte na **OK**.

### <a name="to-create-a-new-schedule-with-windows-powershell"></a>K vytvoření nového plánu pomocí prostředí Windows PowerShell

Můžete použít [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) vytvořte plány. Je-li zadat čas zahájení pro plán a četnosti, který se má spustit.

Následující vzorové příkazy ukazují, jak vytvořit plán pro 15. dne a 30 v každém měsíci rutinou služby Správce prostředků Azure.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
New-AzureRMAutomationSchedule –AutomationAccountName $automationAccountName –Name `
$scheduleName -StartTime "7/01/2016 15:30:00" -MonthInterval 1 `
-DaysOfMonth Fifteenth,Thirtieth -ResourceGroupName "ResourceGroup01"
```

## <a name="linking-a-schedule-to-a-runbook"></a>Propojování plánu k sadě runbook

Sady runbook mohou být spojeny s víc plány a plán může mít víc runbooků. Pokud runbook obsahuje parametry, můžete zadat hodnoty pro ně. Zadejte hodnoty všech povinných parametrů a může poskytnout hodnoty pro všechny volitelné parametry. Tyto hodnoty se používají při každém spuštění runbooku podle tohoto plánu. Můžete přiřadit stejné sady runbook jiný plán a zadejte jiné hodnoty parametru.

### <a name="to-link-a-schedule-to-a-runbook-with-the-azure-portal"></a>Pro připojení plánu k sadě runbook pomocí portálu Azure

1. Na portálu Azure z vašeho účtu automation vyberte **Runbooky** části **automatizace procesu** na levé straně.
1. Klikněte na název sady runbook k plánu.
1. Pokud sada runbook není aktuálně propojena k plánu, pak vám bude nabídnuta možnost vytvořit nový plán nebo odkaz na existující plán.
1. Pokud má runbook parametry, můžete vybrat možnost **upravit nastavení spouštění (výchozí: Azure)** a **parametry** podokně se zobrazí, kde můžete zadat informace odpovídajícím způsobem.

### <a name="to-link-a-schedule-to-a-runbook-with-windows-powershell"></a>Pro připojení plánu k sadě runbook pomocí prostředí Windows PowerShell

Můžete použít [Register-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/register-azurermautomationscheduledrunbook) rutina pro připojení plánu. S parametrem parametrů můžete zadat hodnoty pro parametry runbooku. Další informace o zadání hodnot parametrů najdete v tématu [spuštění sady Runbook ve službě Azure Automation](automation-starting-a-runbook.md).
Následující vzorové příkazy ukazují, jak pro připojení plánu k sadě runbook s parametry rutinou služby Správce prostředků Azure.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$runbookName = "Test-Runbook"
$scheduleName = "Sample-DailySchedule"
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Register-AzureRmAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
–Name $runbookName –ScheduleName $scheduleName –Parameters $params `
-ResourceGroupName "ResourceGroup01"
```

## <a name="scheduling-runbooks-more-frequently"></a>Plánování častěji sady runbook

Nejčastěji se vyskytující interval plánu ve službě Azure Automation lze nakonfigurovat pro je jedna hodina. Pokud budete potřebovat plány provést častěji, než který, existují dvě možnosti:

* Vytvoření [webhooku](automation-webhooks.md) pro sadu runbook a použití [Azure Scheduler](../scheduler/scheduler-get-started-portal.md) volat webhook. Azure Scheduler poskytuje podrobnějšího členitosti při definování plánu.

* Vytvořte čtyři plány všechny spouštění do 15 minut od sebe navzájem spuštění jednou za hodinu. Tento scénář umožňuje spuštění každých 15 minut se různé plány sady runbook.

## <a name="disabling-a-schedule"></a>Zakázání plánu

Při zakázání plánu všechny sady runbook propojena k němu už běží na tento plán. Můžete ručně zakázání plánu nebo můžete nastavit dobu vypršení platnosti plány s frekvencí při jejich vytváření. Když je dosaženo času vypršení platnosti, plán je zakázán.

### <a name="to-disable-a-schedule-from-the-azure-portal"></a>Zakázání plánu z portálu Azure

1. Na portálu Azure z vašeho účtu Automation vyberte **plány** části **sdílené prostředky** na levé straně.
1. Klikněte na název plán, který chcete otevřít v podokně podrobností.
1. Změna **povoleno** k **ne**.

### <a name="to-disable-a-schedule-with-windows-powershell"></a>Zakázání plánu pomocí prostředí Windows PowerShell

Můžete použít [Set-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/set-azurermautomationschedule) rutiny změnit vlastnosti existující plán. Pokud chcete zakázat plán, zadejte **false** pro **hodnotu IsEnabled** parametr.

Následující vzorové příkazy ukazují, jak zakázat plán pro sady runbook pomocí rutiny Azure Resource Manager.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Set-AzureRmAutomationSchedule –AutomationAccountName $automationAccountName `
–Name $scheduleName –IsEnabled $false -ResourceGroupName "ResourceGroup01"
```

## <a name="next-steps"></a>Další postup

* Chcete-li začít pracovat se sadami runbook ve službě Azure Automation, přečtěte si téma [spuštění sady Runbook ve službě Azure Automation](automation-starting-a-runbook.md)