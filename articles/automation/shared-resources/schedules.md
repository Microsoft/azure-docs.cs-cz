---
title: Spravovat plány v Azure Automation
description: Plány automatizace se používají k plánování automatického spouštění Runbooků v Azure Automation. Popisuje, jak vytvořit a spravovat plán v nástroji, abyste mohli automaticky spustit sadu Runbook v určitou dobu nebo podle plánu opakování.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 17d46ddb738abc812ebfc458e25c745b84a29c2a
ms.sourcegitcommit: 1ed0230c48656d0e5c72a502bfb4f53b8a774ef1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2020
ms.locfileid: "82136596"
---
# <a name="manage-schedules-in-azure-automation"></a>Spravovat plány v Azure Automation

Pokud chcete naplánovat spuštění Runbooku v Azure Automation v zadanou dobu, můžete ho propojit s jedním nebo více plány. Plán se dá nakonfigurovat tak, aby se spouštěl buď jednou, nebo na základě hodinových nebo denních plánů pro Runbooky v Azure Portal. Můžete je také naplánovat na týdenní, měsíční, konkrétní dny v týdnu nebo dny v měsíci nebo určitý den v měsíci. Runbook můžete spojit s víc plány a k jednomu plánu může být připojených víc Runbooků.

> [!NOTE]
> Plány v současné době nepodporují Azure Automation konfigurací DSC.

>[!NOTE]
>Tento článek je aktualizovaný a využívá nový modul Az Azure PowerShellu. Můžete dál využívat modul AzureRM, který bude dostávat opravy chyb nejméně do prosince 2020. Další informace o kompatibilitě nového modulu Az a modulu AzureRM najdete v tématu [Seznámení s novým modulem Az Azure PowerShellu](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pokyny k instalaci nástroje AZ Module Hybrid Runbook Worker najdete v tématu [Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Pro váš účet Automation můžete aktualizovat moduly na nejnovější verzi pomocí [postupu aktualizace modulů Azure PowerShell v Azure Automation](../automation-update-azure-modules.md).

## <a name="powershell-cmdlets"></a>Rutiny prostředí PowerShell

Rutiny v následující tabulce se používají k vytváření a správě plánů pomocí prostředí PowerShell v Azure Automation. Dodávají se jako součást [modulu Azure PowerShell](/powershell/azure/overview).

| Rutiny | Popis |
|:--- |:--- |
| [Get-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationSchedule?view=azps-3.7.0) |Načte plán. |
| [Get-AzAutomationScheduledRunbook](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationscheduledrunbook?view=azps-3.7.0) |Načte naplánované Runbooky. |
| [New-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSchedule?view=azps-3.7.0) |Vytvoří nový plán. |
| [Registrovat – AzAutomationScheduledRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Register-AzAutomationScheduledRunbook?view=azps-3.7.0) |Přidruží sadu Runbook k plánu. |
| [Remove-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationSchedule?view=azps-3.7.0) |Odebere plán. |
| [Set-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationSchedule?view=azps-3.7.0) |Nastaví vlastnosti pro existující plán. |
| [Zrušit registraci – AzAutomationScheduledRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Unregister-AzAutomationScheduledRunbook?view=azps-3.7.0) |Dissociates sadu Runbook z plánu. |

## <a name="creating-a-schedule"></a>Vytvoření plánu

Můžete vytvořit nový plán pro Runbooky v Azure Portal nebo pomocí PowerShellu.

> [!NOTE]
> Azure Automation používá nejnovější moduly v účtu Automation při spuštění nové naplánované úlohy.  Chcete-li se vyhnout vlivu na vaše Runbooky a procesy, které automatizují, měli byste nejprve otestovat všechny Runbooky, které mají propojené plány, pomocí účtu Automation vyhrazeného pro testování.  Tím se ověří, že vaše naplánované Runbooky budou dál fungovat správně, a pokud ne, můžete pokračovat v řešení potíží a použít všechny změny, které se vyžadují před migrací aktualizované verze Runbooku do produkčního prostředí.
> Váš účet Automation automaticky nezíská žádné nové verze modulů, pokud jste je neaktualizovali ručně, a to tak, že v **modulech**vyberete možnost [aktualizovat moduly Azure](../automation-update-azure-modules.md) .

### <a name="create-a-new-schedule-in-the-azure-portal"></a>Vytvořit nový plán v Azure Portal

1. V Azure Portal v účtu Automation na levé straně v části **sdílené prostředky** vyberte **plány** .
2. V horní části stránky klikněte na **Přidat plán** .
3. V podokně nový plán zadejte název a volitelně také popis nového plánu.
4. Vyberte, jestli se plán spouští jednou, nebo na základě plánu **opakování**, a to tak, že vyberete **jednou** nebo znovu. Pokud vyberete jednu z **nich, zadejte**čas spuštění a pak klikněte na **vytvořit**. Pokud vyberete možnost **opakovaná**, zadejte čas spuštění. U **každého opakování**vyberte, jak často se má sada Runbook opakovat – podle hodin, dnů, týdnů nebo měsíců.
    1. Pokud vyberete možnost **týden**, zobrazí se dny v týdnu, ze kterých si můžete vybrat. Vyberte tolik dní, kolik chcete. První spuštění vašeho plánu nastane první den, který se vybere po počátečním čase. Chcete-li například vybrat víkendový plán, vyberte možnost sobota a neděle. 
    
       ![Nastavování opakovaného plánu na víkend](../media/schedules/week-end-weekly-recurrence.png)

    2. Pokud vyberete možnost **month (měsíc**), budete mít k disdílně různé možnosti. Pro možnost **měsíčních výskytů** vyberte buď **dny v měsíci** nebo **dny v týdnu**. Pokud zvolíte **dny v měsíci**, zobrazí se kalendář, který vám umožní zvolit tolik dní, kolik chcete. Pokud zvolíte datum, například 31, ke kterému nedochází v aktuálním měsíci, plán se nespustí. Pokud chcete, aby plán běžel za poslední den, v části **Spustit za poslední den v měsíci**vyberte **Ano** . Pokud zvolíte **dny v týdnu**, zobrazí se **všechny možnosti opakování** . Vyberte **první**, **druhý**, **třetí**, **čtvrtý**nebo **Poslední**. Nakonec vyberte den, kdy se má opakovat.

       ![Měsíční plán na prvních, patnáct a poslední den v měsíci](../media/schedules/monthly-first-fifteenth-last.png)

5. Po dokončení klikněte na **vytvořit**.

### <a name="create-a-new-schedule-with-powershell"></a>Vytvoření nového plánu pomocí PowerShellu

K vytvoření plánů použijte rutinu [New-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSchedule?view=azps-3.7.0) . Zadejte čas spuštění plánu a frekvenci, kterou má spustit. Následující příklady ukazují, jak vytvořit mnoho různých scénářů plánování.

#### <a name="create-a-one-time-schedule"></a>Vytvoření jednorázového plánu

Následující vzorové příkazy vytvoří jeden časový plán.

```azurepowershell-interactive
$TimeZone = ([System.TimeZoneInfo]::Local).Id
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule01" -StartTime "23:00" -OneTime -ResourceGroupName "ResourceGroup01" -TimeZone $TimeZone
```

#### <a name="create-a-recurring-schedule"></a>Vytvoření plánu opakování

Následující příklad ukazuje, jak vytvořit opakovaný plán, který se spouští každý den v intervalu 1:13:00 po dobu roku.

```azurepowershell-interactive
$StartTime = Get-Date "13:00:00"
$EndTime = $StartTime.AddYears(1)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule02" -StartTime $StartTime -ExpiryTime $EndTime -DayInterval 1 -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule"></a>Vytvoření týdenního opakovaného plánu

Následující příklad ukazuje, jak vytvořit týdenní plán, který se spouští pouze v pracovních dnech.

```azurepowershell-interactive
$StartTime = (Get-Date "13:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekDays = @([System.DayOfWeek]::Monday..[System.DayOfWeek]::Friday)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule03" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule-for-weekends"></a>Vytvoření týdenního opakovaného plánu pro víkendy

Následující vzorové příkazy ukazují, jak vytvořit týdenní plán, který se spouští jenom na víkendech.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekendDays = @([System.DayOfWeek]::Saturday,[System.DayOfWeek]::Sunday)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Weekends 6PM" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekendDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-recurring-schedule-for-first-15th-and-last-days-of-the-month"></a>Vytvořit plán opakování pro první, 15 a poslední den v měsíci

Následující příklad ukazuje, jak vytvořit opakovaný plán, který běží na 1, 15 a poslední den v měsíci.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
New-AzAutomationSchedule -AutomationAccountName "TestAzureAuto" -Name "1st, 15th and Last" -StartTime $StartTime -DaysOfMonth @("One", "Fifteenth", "Last") -ResourceGroupName "TestAzureAuto" -MonthInterval 1
```

## <a name="linking-a-schedule-to-a-runbook"></a>Propojení plánu k sadě Runbook

Runbook můžete spojit s víc plány a k jednomu plánu může být připojených víc Runbooků. Pokud sada Runbook obsahuje parametry, můžete pro ně zadat jejich hodnoty. Je nutné zadat hodnoty pro všechny povinné parametry a může poskytnout hodnoty pro všechny volitelné parametry. Tyto hodnoty se používají při každém spuštění sady Runbook tímto plánem. Stejný Runbook můžete připojit k jinému plánu a zadat jiné hodnoty parametrů.

### <a name="link-a-schedule-to-a-runbook-with-the-azure-portal"></a>Připojení plánu k sadě Runbook pomocí Azure Portal

1. V Azure Portal na svém účtu Automation v části **Automatizace procesu**vyberte **Runbooky** .
2. Klikněte na název Runbooku, který chcete naplánovat.
3. Pokud Runbook není aktuálně propojený s plánem, nabídne se vám možnost vytvořit nový plán nebo propojit s existujícím plánem.
4. Pokud má Runbook parametry, můžete vybrat možnost **Upravit nastavení spouštění (výchozí: Azure)** a zobrazí se podokno parametry. Sem můžete zadat informace o parametrech.

### <a name="link-a-schedule-to-a-runbook-with-powershell"></a>Připojení plánu k Runbooku pomocí PowerShellu

K propojení plánu použijte rutinu [Register-AzAutomationScheduledRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Register-AzAutomationScheduledRunbook?view=azps-3.7.0) . Parametry Runbooku můžete zadat pomocí parametru Parametry . Další informace o zadávání hodnot parametrů naleznete v tématu [Starting a Runbook in Azure Automation](../automation-starting-a-runbook.md).
Následující příklad ukazuje, jak propojit plán k sadě Runbook pomocí rutiny Azure Resource Manager s parametry.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$runbookName = "Test-Runbook"
$scheduleName = "Sample-DailySchedule"
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Register-AzAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
–Name $runbookName –ScheduleName $scheduleName –Parameters $params `
-ResourceGroupName "ResourceGroup01"
```

## <a name="scheduling-runbooks-to-run-more-frequently"></a>Plánování spouštění Runbooků častěji

Nejčastější interval, po který je možné naplánovat Azure Automation, je jedna hodina. Pokud požadujete, aby se plány spouštěly častěji než to, existují dvě možnosti:

* Vytvořte [Webhook](../automation-webhooks.md) pro Runbook a použijte [Azure Logic Apps](../../logic-apps/logic-apps-overview.md) pro volání Webhooku. Azure Logic Apps poskytuje přesnější členitost při definování plánu.

* Vytvořte čtyři plány, které začínají během 15 minut od každé jiné spuštění každou hodinu. Tento scénář umožňuje spuštění sady Runbook každých 15 minut s různými plány.

## <a name="disabling-a-schedule"></a>Zakázání plánu

Když plán zakážete, na tento plán se už nespustí žádná sada Runbook, na kterou se odkazuje. Můžete ručně zakázat plán nebo nastavit čas vypršení platnosti plánů s frekvencí při jejich vytváření. Po dosažení doby platnosti je plán zakázán.

### <a name="disable-a-schedule-from-the-azure-portal"></a>Zakázat plán z Azure Portal

1. Ve svém účtu Automation v části **sdílené prostředky**vyberte **plány** .
2. Kliknutím na název plánu otevřete podokno podrobností.
3. Změňte možnost **povoleno** na **ne**.

> [!NOTE]
> Pokud chcete zakázat plán, který má čas začátku v minulosti, je nutné před uložením změnit počáteční datum na čas v budoucnosti.

### <a name="disable-a-schedule-with-powershell"></a>Zakázání plánu pomocí PowerShellu

Pomocí rutiny [set-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationSchedule?view=azps-3.7.0) můžete změnit vlastnosti stávajícího plánu. Pokud chcete plán zakázat, zadejte pro `IsEnabled` parametr hodnotu false.

Následující příklad ukazuje, jak zakázat plán pro sadu Runbook pomocí rutiny Azure Resource Manager.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Set-AzAutomationSchedule –AutomationAccountName $automationAccountName `
–Name $scheduleName –IsEnabled $false -ResourceGroupName "ResourceGroup01"
```

## <a name="removing-a-schedule"></a>Odebírá se plán.

Až budete připraveni k odebrání plánů, můžete použít Azure Portal nebo `Remove-AzureRmAutomationSchedule` rutinu. Mějte na paměti, že můžete odebrat jenom plán, který je zakázaný, jak je popsané v předchozí části.

### <a name="remove-a-schedule-using-the-azure-portal"></a>Odebrání plánu pomocí Azure Portal

1. Ve svém účtu Automation v části **sdílené prostředky**vyberte **plány** .
2. Kliknutím na název plánu otevřete podokno podrobností.
3. Klikněte na **Odstranit**.

### <a name="remove-a-schedule-with-powershell"></a>Odebrání plánu pomocí PowerShellu

Pomocí rutiny [Remove-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationSchedule?view=azps-3.7.0) můžete odstranit stávající plán. 

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Remove-AzAutomationSchedule -AutomationAccountName $automationAccountName `
-Name $scheduleName -ResourceGroupName "ResourceGroup01"
```

## <a name="next-steps"></a>Další kroky

* Chcete-li začít se sadami Runbook v nástroji Azure Automation, přečtěte si téma [spuštění sady Runbook v Azure Automation](../automation-starting-a-runbook.md).