---
title: Plány v Azure Automation
description: Plány automatizace se používají k plánování automatického spouštění Runbooků v Azure Automation. Popisuje, jak vytvořit a spravovat plán v nástroji, abyste mohli automaticky spustit sadu Runbook v určitou dobu nebo podle plánu opakování.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: c4898ba62abdc42d95b77b9a77387bfe71fb4771
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/25/2020
ms.locfileid: "77592199"
---
# <a name="scheduling-a-runbook-in-azure-automation"></a>Naplánování runbooku v Azure Automation

Pokud chcete naplánovat spuštění Runbooku v Azure Automation v zadanou dobu, můžete ho propojit s jedním nebo více plány. Plán se dá nakonfigurovat tak, aby se spouštěl buď jednou, nebo na základě hodinových nebo denních plánů pro Runbooky v Azure Portal. Můžete je také naplánovat na týdenní, měsíční, konkrétní dny v týdnu nebo dny v měsíci nebo určitý den v měsíci. Runbook můžete spojit s víc plány a k jednomu plánu může být připojených víc Runbooků.

> [!NOTE]
> Plány v současné době nepodporují Azure Automation konfigurací DSC.

## <a name="powershell-cmdlets"></a>Rutiny prostředí PowerShell

Rutiny v následující tabulce se používají k vytváření a správě plánů pomocí prostředí PowerShell v Azure Automation. Dodávají se jako součást [modulu Azure PowerShell](/powershell/azure/overview).

| Rutiny | Popis |
|:--- |:--- |
| [Get-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/get-azurermautomationschedule) |Načte plán. |
| [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) |Vytvoří nový plán. |
| [Remove-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/remove-azurermautomationschedule) |Odebere plán. |
| [Set-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/set-azurermautomationschedule) |Nastaví vlastnosti pro existující plán. |
| [Get-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/get-azurermautomationscheduledrunbook) |Načte naplánované Runbooky. |
| [Registrovat – AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/register-azurermautomationscheduledrunbook) |Přidruží sadu Runbook k plánu. |
| [Zrušit registraci – AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/unregister-azurermautomationscheduledrunbook) |Dissociates sadu Runbook z plánu. |

## <a name="creating-a-schedule"></a>Vytvoření plánu

Můžete vytvořit nový plán pro Runbooky v Azure Portal nebo pomocí PowerShellu.

> [!NOTE]
> Azure Automation používá nejnovější moduly v účtu Automation při spuštění nové naplánované úlohy.  Chcete-li se vyhnout vlivu na vaše Runbooky a procesy, které automatizují, měli byste nejprve otestovat všechny Runbooky, které mají propojené plány, pomocí účtu Automation vyhrazeného pro testování.  Tím se ověří, že vaše naplánované Runbooky budou dál fungovat správně, a pokud ne, můžete pokračovat v řešení potíží a použít všechny změny, které se vyžadují před migrací aktualizované verze Runbooku do produkčního prostředí.
> Váš účet Automation automaticky nezíská žádné nové verze modulů, pokud jste je neaktualizovali ručně, a to tak, že v **modulech**vyberete možnost [aktualizovat moduly Azure](../automation-update-azure-modules.md) .

### <a name="to-create-a-new-schedule-in-the-azure-portal"></a>Vytvoření nového plánu v Azure Portal

1. V Azure Portal v účtu Automation na levé straně v části **sdílené prostředky** vyberte **plány** .
2. V horní části stránky klikněte na **Přidat plán** .
3. V podokně **Nový plán** zadejte **název** a volitelně také **Popis** nového plánu.
4. Vyberte, jestli se plán spouští jednou, nebo na základě plánu **opakování**, a to tak, že vyberete **jednou** nebo znovu. Pokud vyberete možnost **zadat** **čas zahájení**a pak klikněte na tlačítko **vytvořit**. Pokud vyberete možnost **opakovat, zadejte** **čas zahájení** a **opakování každého**, vyberte frekvenci, s jakou se má sada Runbook opakovat – podle **hodin**, **dnů**, **týdnů**nebo po **měsících**.
    1. Pokud vyberete **týden**, zobrazí se seznam dnů v týdnu, ze kterých si můžete vybrat. Vyberte tolik dní, kolik chcete. První spuštění vašeho plánu nastane první den, který se vybere po počátečním čase. Chcete-li například zvolit víkendový plán, vyberte možnost **sobota** a **neděle**.

       ![Nastavování opakovaného plánu na víkend](../media/schedules/week-end-weekly-recurrence.png)

    2. Pokud vyberete možnost **month (měsíc**), budete mít k disdílně různé možnosti. Pro možnost **měsíčních výskytů** vyberte buď **dny v měsíci** nebo **dny v týdnu**. Pokud zvolíte **dny v měsíci**, zobrazí se kalendář, který vám umožní zvolit tolik dní, kolik chcete. Pokud zvolíte datum, například 31, ke kterému nedochází v aktuálním měsíci, plán se nespustí. Pokud chcete, aby plán běžel za poslední den, v části **Spustit za poslední den v měsíci**vyberte **Ano** . Pokud zvolíte **dny v týdnu**, zobrazí se **všechny možnosti opakování** . Vyberte **první**, **druhý**, **třetí**, **čtvrtý**nebo **Poslední**. Nakonec vyberte den, kdy se má opakovat.

       ![Měsíční plán na prvních, patnáct a poslední den v měsíci](../media/schedules/monthly-first-fifteenth-last.png)

5. Po dokončení klikněte na **vytvořit**.

### <a name="to-create-a-new-schedule-with-powershell"></a>Vytvoření nového plánu pomocí PowerShellu

Pomocí rutiny [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) vytvoříte plány. Zadejte čas spuštění plánu a frekvenci, kterou má spustit. Následující příklady ukazují, jak vytvořit mnoho různých scénářů plánování.

#### <a name="create-a-one-time-schedule"></a>Vytvořit jednorázový plán

Následující vzorové příkazy vytvoří jeden časový plán.

```azurepowershell-interactive
$TimeZone = ([System.TimeZoneInfo]::Local).Id
New-AzureRmAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule01" -StartTime "23:00" -OneTime -ResourceGroupName "ResourceGroup01" -TimeZone $TimeZone
```

#### <a name="create-a-recurring-schedule"></a>Vytvoření plánu opakování

Následující vzorové příkazy ukazují, jak vytvořit opakovaný plán, který se spouští každý den v intervalu 1:13:00 po dobu roku.

```azurepowershell-interactive
$StartTime = Get-Date "13:00:00"
$EndTime = $StartTime.AddYears(1)
New-AzureRmAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule02" -StartTime $StartTime -ExpiryTime $EndTime -DayInterval 1 -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule"></a>Vytvoření týdenního opakovaného plánu

Následující vzorové příkazy ukazují, jak vytvořit týdenní plán, který se spouští jenom v pracovních dnech.

```azurepowershell-interactive
$StartTime = (Get-Date "13:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekDays = @([System.DayOfWeek]::Monday..[System.DayOfWeek]::Friday)
New-AzureRmAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule03" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule-for-weekends"></a>Vytvoření týdenního opakovaného plánu pro víkendy

Následující vzorové příkazy ukazují, jak vytvořit týdenní plán, který se spouští jenom na víkendech.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekendDays = @([System.DayOfWeek]::Saturday,[System.DayOfWeek]::Sunday)
New-AzureRmAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Weekends 6PM" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekendDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-recurring-schedule-for-first-15th-and-last-days-of-the-month"></a>Vytvořit plán opakování pro první, 15 a poslední den v měsíci

Následující vzorové příkazy ukazují, jak vytvořit plán opakování, který se spouští na 1, 15 a poslední den v měsíci.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
New-AzureRmAutomationSchedule -AutomationAccountName "TestAzureAuto" -Name "1st, 15th and Last" -StartTime $StartTime -DaysOfMonth @("One", "Fifteenth", "Last") -ResourceGroupName "TestAzureAuto" -MonthInterval 1
```

## <a name="linking-a-schedule-to-a-runbook"></a>Propojení plánu k sadě Runbook

Runbook můžete spojit s víc plány a k jednomu plánu může být připojených víc Runbooků. Pokud sada Runbook obsahuje parametry, můžete pro ně zadat jejich hodnoty. Je nutné zadat hodnoty pro všechny povinné parametry a může poskytnout hodnoty pro všechny volitelné parametry. Tyto hodnoty se používají při každém spuštění sady Runbook tímto plánem. Stejný Runbook můžete připojit k jinému plánu a zadat jiné hodnoty parametrů.

### <a name="to-link-a-schedule-to-a-runbook-with-the-azure-portal"></a>Postup připojení plánu k Runbooku pomocí Azure Portal

1. V Azure Portal na svém účtu Automation v části **Automatizace procesu** na levé straně vyberte **Runbooky** .
2. Klikněte na název Runbooku, který chcete naplánovat.
3. Pokud Runbook není aktuálně propojený s plánem, nabídne se vám možnost vytvořit nový plán nebo propojit s existujícím plánem.
4. Pokud má Runbook parametry, můžete vybrat možnost **Upravit nastavení spouštění (výchozí: Azure)** a podokno **parametry** , kde můžete zadat informace.

### <a name="to-link-a-schedule-to-a-runbook-with-powershell"></a>Připojení plánu k Runbooku pomocí PowerShellu

K propojení plánu můžete použít rutinu [Register-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/register-azurermautomationscheduledrunbook) . Parametry Runbooku můžete zadat pomocí parametru Parametry. Další informace o zadávání hodnot parametrů naleznete v tématu [Starting a Runbook in Azure Automation](../automation-starting-a-runbook.md).
Následující vzorové příkazy znázorňují postup připojení plánu k Runbooku pomocí rutiny Azure Resource Manager s parametry.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$runbookName = "Test-Runbook"
$scheduleName = "Sample-DailySchedule"
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Register-AzureRmAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
–Name $runbookName –ScheduleName $scheduleName –Parameters $params `
-ResourceGroupName "ResourceGroup01"
```

## <a name="scheduling-runbooks-more-frequently"></a>Častější plánování runbooků

Nejčastější interval, po který je možné naplánovat Azure Automation, je jedna hodina. Pokud požadujete, aby se plány spouštěly častěji než to, existují dvě možnosti:

* Vytvořte [Webhook](../automation-webhooks.md) pro Runbook a použijte [Azure Logic Apps](../../logic-apps/logic-apps-overview.md) pro volání Webhooku. Azure Logic Apps poskytuje přesnější členitost při definování plánu.

* Vytvořte čtyři plány, které začínají během 15 minut od každé jiné spuštění každou hodinu. Tento scénář umožňuje spuštění sady Runbook každých 15 minut s různými plány.

## <a name="disabling-a-schedule"></a>Zakázání plánu

Když plán zakážete, na tento plán se už nespustí žádná sada Runbook, na kterou se odkazuje. Můžete ručně zakázat plán nebo nastavit čas vypršení platnosti plánů s frekvencí při jejich vytváření. Po dosažení doby platnosti je plán zakázán.

### <a name="to-disable-a-schedule-from-the-azure-portal"></a>Postup zakázání plánu z Azure Portal

1. V Azure Portal v účtu Automation na levé straně v části **sdílené prostředky** vyberte **plány** .
2. Kliknutím na název plánu otevřete podokno podrobností.
3. Změňte možnost **povoleno** na **ne**.

> [!NOTE]
> Pokud chcete zakázat plán, který má čas začátku v minulosti, je nutné před uložením změnit počáteční datum na čas v budoucnosti.

### <a name="to-disable-a-schedule-with-powershell"></a>Postup zakázání plánu pomocí prostředí PowerShell

Pomocí rutiny [set-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/set-azurermautomationschedule) můžete změnit vlastnosti stávajícího plánu. Chcete-li zakázat plán, zadejte **hodnotu false** pro parametr **Enable** .

Následující vzorové příkazy ukazují, jak zakázat plán pro sadu Runbook pomocí rutiny Azure Resource Manager.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Set-AzureRmAutomationSchedule –AutomationAccountName $automationAccountName `
–Name $scheduleName –IsEnabled $false -ResourceGroupName "ResourceGroup01"
```

## <a name="next-steps"></a>Další kroky

* Chcete-li začít se sadami Runbook v nástroji Azure Automation, přečtěte si téma [spuštění sady Runbook v Azure Automation](../automation-starting-a-runbook.md)

