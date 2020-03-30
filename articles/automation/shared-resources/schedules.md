---
title: Plány v Azure Automation
description: Plány automatizace se používají k plánování runbooků v Azure Automation, aby se automaticky spouštěla. Popisuje, jak vytvořit a spravovat plán, takže můžete automaticky spustit runbook v určitém čase nebo podle opakovaného plánu.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: c4898ba62abdc42d95b77b9a77387bfe71fb4771
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252659"
---
# <a name="scheduling-a-runbook-in-azure-automation"></a>Naplánování runbooku v Azure Automation

Chcete-li naplánovat spuštění sady Runbook v Azure Automation v určeném čase, propojte ho s jedním nebo více plány. Plán lze nakonfigurovat tak, aby se spouštěl jednou nebo podle opakovaného hodinového nebo denního plánu pro runbooky na webu Azure Portal. Můžete je také naplánovat na týdenní, měsíční, konkrétní dny v týdnu nebo dny v měsíci nebo na konkrétní den v měsíci. Runbook můžete spojit s víc plány a k jednomu plánu může být připojených víc Runbooků.

> [!NOTE]
> Plány aktuálně nepodporují konfigurace DSC azure automatizace.

## <a name="powershell-cmdlets"></a>Rutiny prostředí PowerShell

Rutiny v následující tabulce se používají k vytváření a správě plánů pomocí Prostředí PowerShell v Azure Automation. Dodávají se jako součást [modulu Azure PowerShell](/powershell/azure/overview).

| Rutiny | Popis |
|:--- |:--- |
| [Get-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/get-azurermautomationschedule) |Načte plán. |
| [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) |Vytvoří nový plán. |
| [Remove-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/remove-azurermautomationschedule) |Odebere plán. |
| [Set-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/set-azurermautomationschedule) |Nastaví vlastnosti existujícího plánu. |
| [Get-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/get-azurermautomationscheduledrunbook) |Načte naplánované sady Runbook. |
| [Register-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/register-azurermautomationscheduledrunbook) |Přidruží runbook k plánu. |
| [Unregister-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/unregister-azurermautomationscheduledrunbook) |Disociace runbook z plánu. |

## <a name="creating-a-schedule"></a>Vytvoření plánu

Nový plán pro runbooky můžete vytvořit na webu Azure Portal nebo s PowerShellem.

> [!NOTE]
> Azure Automation používá nejnovější moduly ve vašem účtu Automation při spuštění nové naplánované úlohy.  Chcete-li se vyhnout dopadu na vaše sady Runbook a procesy, které automatizují, měli byste nejprve otestovat všechny sady Runbook, které propojili plány s účtem Automation vyhrazeným pro testování.  Tím ověříte, že naplánované sady Runbook nadále fungují správně, a pokud ne, můžete dále řešit potíže a použít všechny změny potřebné před migrací aktualizované verze sady Runbook do produkčního prostředí.
> Váš účet Automatizace nezíská automaticky žádné nové verze modulů, pokud jste je aktualizovali ručně výběrem [možnosti Aktualizovat moduly Azure](../automation-update-azure-modules.md) z **modulů**.

### <a name="to-create-a-new-schedule-in-the-azure-portal"></a>Vytvoření nového plánu na webu Azure Portal

1. Na webu Azure Portal z účtu automatizace vyberte **plány** v části **Sdílené prostředky** na levé straně.
2. V horní části stránky klikněte na **Přidat plán.**
3. V podokně **Nový plán** zadejte **název** a případně **popis** nového plánu.
4. Vyberte, zda se plán spustí jednou nebo podle opakovaného plánu, a to výběrem **možnosti Jednou** nebo **Opakované**. Pokud vyberete **Jednou** určíte **čas zahájení**a potom klepnete na **tlačítko Vytvořit**. Pokud vyberete **Možnost Opakovat**, zadejte **čas zahájení** a pro opakování **každý ,** vyberte frekvenci, jak často se má runbook opakovat - podle **hodiny**, **dne**, **týdne**nebo **měsíce**.
    1. Pokud vyberete **týden**, zobrazí se seznam dnů v týdnu, ze kterých si můžete vybrat. Vyberte tolik dní, kolik chcete. První spuštění plánu proběhne první den vybraný po počátečním čase. Chcete-li například zvolit plán víkendu, zvolte **sobota** a **neděle**.

       ![Nastavení víkendového periodického plánu](../media/schedules/week-end-weekly-recurrence.png)

    2. Pokud vyberete **měsíc**, budete mít různé možnosti. Pro možnost **Měsíční výskyty** vyberte buď **dny měsíce,** nebo **Dny týdne**. Pokud zvolíte **Měsíce –** zobrazí se kalendář, který vám umožní vybrat si libovolný počet dní. Pokud zvolíte datum, jako je například 31. Pokud chcete, aby byl plán spuštěn poslední den, zvolte **Ano** v části **Spustit poslední den v měsíci**. Pokud zvolíte **Dny v týdnu**, zobrazí se **možnost Opakovat každou** možnost. Zvolte **První**, **Druhý,** **Třetí,** **Čtvrtý**nebo **Poslední**. Nakonec si vyberte den, který chcete opakovat.

       ![Měsíční plán na první, patnáctý a poslední den v měsíci](../media/schedules/monthly-first-fifteenth-last.png)

5. Po dokončení klepněte na **tlačítko Vytvořit**.

### <a name="to-create-a-new-schedule-with-powershell"></a>Vytvoření nového plánu pomocí PowerShellu

Rutinu [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) použijete k vytvoření plánů. Zadáte čas zahájení plánu a frekvenci, kterou má spustit. Následující příklady ukazují, jak vytvořit mnoho různých scénářů plánu.

#### <a name="create-a-one-time-schedule"></a>Vytvoření jednorázového plánu

Následující ukázkové příkazy vytvoří jednorázový plán.

```azurepowershell-interactive
$TimeZone = ([System.TimeZoneInfo]::Local).Id
New-AzureRmAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule01" -StartTime "23:00" -OneTime -ResourceGroupName "ResourceGroup01" -TimeZone $TimeZone
```

#### <a name="create-a-recurring-schedule"></a>Vytvoření opakovaného plánu

Následující ukázkové příkazy ukazují, jak vytvořit opakovaný plán, který se spouští každý den v 13:00 po dobu jednoho roku.

```azurepowershell-interactive
$StartTime = Get-Date "13:00:00"
$EndTime = $StartTime.AddYears(1)
New-AzureRmAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule02" -StartTime $StartTime -ExpiryTime $EndTime -DayInterval 1 -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule"></a>Vytvoření týdenního opakovaného plánu

Následující ukázkové příkazy ukazují, jak vytvořit týdenní plán, který běží pouze ve všední dny.

```azurepowershell-interactive
$StartTime = (Get-Date "13:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekDays = @([System.DayOfWeek]::Monday..[System.DayOfWeek]::Friday)
New-AzureRmAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule03" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule-for-weekends"></a>Vytvoření týdenního opakovaného plánu pro víkendy

Následující ukázkové příkazy ukazují, jak vytvořit týdenní plán, který běží pouze o víkendech.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekendDays = @([System.DayOfWeek]::Saturday,[System.DayOfWeek]::Sunday)
New-AzureRmAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Weekends 6PM" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekendDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-recurring-schedule-for-first-15th-and-last-days-of-the-month"></a>Vytvoření opakovaného plánu pro první, patnáctý a poslední dny v měsíci

Následující ukázkové příkazy ukazují, jak vytvořit opakovaný plán, který se spustí první, patnáctý a poslední den v měsíci.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
New-AzureRmAutomationSchedule -AutomationAccountName "TestAzureAuto" -Name "1st, 15th and Last" -StartTime $StartTime -DaysOfMonth @("One", "Fifteenth", "Last") -ResourceGroupName "TestAzureAuto" -MonthInterval 1
```

## <a name="linking-a-schedule-to-a-runbook"></a>Propojení plánu se knihou Runbook

Runbook můžete spojit s víc plány a k jednomu plánu může být připojených víc Runbooků. Pokud má runbook parametry, můžete pro ně zadat hodnoty. Musíte zadat hodnoty pro všechny povinné parametry a může poskytnout hodnoty pro všechny volitelné parametry. Tyto hodnoty se používají při každém spuštění runbooku tímto plánem. Můžete připojit stejnou runbook k jinému plánu a zadat různé hodnoty parametrů.

### <a name="to-link-a-schedule-to-a-runbook-with-the-azure-portal"></a>Propojení plánu s runbookem s portálem Azure

1. Na portálu Azure z vašeho účtu automatizace vyberte **Runbooky** v části **Automatizace procesů** na levé straně.
2. Klikněte na název Runbooku, který chcete naplánovat.
3. Pokud sada Runbook není aktuálně propojena s plánem, bude vám nabídnuta možnost vytvořit nový plán nebo propojit existující plán.
4. Pokud sada Runbook má parametry, můžete vybrat možnost **Změnit nastavení spuštění (Default:Azure)** a zobrazí se podokno **Parametry,** kde můžete zadat informace.

### <a name="to-link-a-schedule-to-a-runbook-with-powershell"></a>Propojení plánu s runbookem s PowerShellem

Rutina [Register-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/register-azurermautomationscheduledrunbook) můžete použít k propojení plánu. Parametry Runbooku můžete zadat pomocí parametru Parametry . Další informace o určení hodnot parametrů najdete v [tématu Spuštění sady Runbook v Azure Automation](../automation-starting-a-runbook.md).
Následující ukázkové příkazy ukazují, jak propojit plán s runbookpomocí rutiny Správce prostředků Azure s parametry.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$runbookName = "Test-Runbook"
$scheduleName = "Sample-DailySchedule"
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Register-AzureRmAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
–Name $runbookName –ScheduleName $scheduleName –Parameters $params `
-ResourceGroupName "ResourceGroup01"
```

## <a name="scheduling-runbooks-more-frequently"></a>Častější plánování sad runbooků

Nejčastější interval, pro který lze nakonfigurovat plán v Azure Automation, je jedna hodina. Pokud požadujete plány pro provádění častěji než to, existují dvě možnosti:

* Vytvořte [webhookpro](../automation-webhooks.md) runbook a pomocí [Azure Logic Apps](../../logic-apps/logic-apps-overview.md) volat webhooku. Azure Logic Apps poskytuje více jemně odstupňované rozlišovací schopnost při definování plánu.

* Vytvořte čtyři plány, které začínají do 15 minut od sebe, které se spouštějí jednou za hodinu. Tento scénář umožňuje spuštění runbooku každých 15 minut s různými plány.

## <a name="disabling-a-schedule"></a>Zakázání plánu

Když zakážete plán, všechny runbook s ním spojené již běží na tomto plánu. Můžete ručně zakázat plán nebo nastavit čas vypršení platnosti pro plány s frekvencí při jejich vytváření. Po dosažení doby vypršení platnosti je plán zakázán.

### <a name="to-disable-a-schedule-from-the-azure-portal"></a>Zakázání plánu z webu Azure Portal

1. Na webu Azure Portal z účtu Automation vyberte **plány** v části **Sdílené prostředky** na levé straně.
2. Kliknutím na název plánu otevřete podokno podrobností.
3. Změnit **povoleno** na **ne**.

> [!NOTE]
> Chcete-li zakázat plán, který má čas zahájení v minulosti, je nutné změnit počáteční datum na čas v budoucnu před uložením.

### <a name="to-disable-a-schedule-with-powershell"></a>Zakázání plánu pomocí Prostředí PowerShell

Rutinu [Set-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/set-azurermautomationschedule) můžete použít ke změně vlastností existujícího plánu. Chcete-li zakázat plán, zadejte **false** pro **isenabled** parametr.

Následující ukázkové příkazy ukazují, jak zakázat plán pro runbook pomocí rutiny Správce prostředků Azure.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Set-AzureRmAutomationSchedule –AutomationAccountName $automationAccountName `
–Name $scheduleName –IsEnabled $false -ResourceGroupName "ResourceGroup01"
```

## <a name="next-steps"></a>Další kroky

* Pokud chcete začít s runbooky v Azure Automation, přečtěte si témat [u tématu Spuštění Runbooku v Azure Automation](../automation-starting-a-runbook.md)

