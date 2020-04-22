---
title: Správa plánů v Azure Automation
description: Plány automatizace se používají k plánování runbooků v Azure Automation, aby se automaticky spouštěla. Popisuje, jak vytvořit a spravovat plán, takže můžete automaticky spustit runbook v určitém čase nebo podle opakovaného plánu.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 3603e76186ce30fb491d829d3a804837f4ac2e6d
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732791"
---
# <a name="manage-schedules-in-azure-automation"></a>Správa plánů v Azure Automation

Chcete-li naplánovat spuštění sady Runbook v Azure Automation v určeném čase, propojte ho s jedním nebo více plány. Plán lze nakonfigurovat tak, aby se spouštěl jednou nebo podle opakovaného hodinového nebo denního plánu pro runbooky na webu Azure Portal. Můžete je také naplánovat na týdenní, měsíční, konkrétní dny v týdnu nebo dny v měsíci nebo na konkrétní den v měsíci. Runbook můžete spojit s víc plány a k jednomu plánu může být připojených víc Runbooků.

> [!NOTE]
> Plány aktuálně nepodporují konfigurace DSC azure automatizace.

>[!NOTE]
>Tento článek je aktualizovaný a využívá nový modul Az Azure PowerShellu. Můžete dál využívat modul AzureRM, který bude dostávat opravy chyb nejméně do prosince 2020. Další informace o kompatibilitě nového modulu Az a modulu AzureRM najdete v tématu [Seznámení s novým modulem Az Azure PowerShellu](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pokyny k instalaci modulu AZ na pracovníka hybridní sady Runbook najdete [v tématu Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). U vašeho účtu Automation můžete aktualizovat moduly na nejnovější verzi pomocí [funkce Jak aktualizovat moduly Azure PowerShellu v Azure Automation](../automation-update-azure-modules.md).

## <a name="powershell-cmdlets"></a>Rutiny prostředí PowerShell

Rutiny v následující tabulce se používají k vytváření a správě plánů pomocí Prostředí PowerShell v Azure Automation. Dodávají se jako součást [modulu Azure PowerShell](/powershell/azure/overview).

| Rutiny | Popis |
|:--- |:--- |
| [Get-AzAutomationPlán](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationSchedule?view=azps-3.7.0) |Načte plán. |
| [Get-AzAutomationScheduledRunbook](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationscheduledrunbook?view=azps-3.7.0) |Načte naplánované sady Runbook. |
| [Nový plán AzAutomation](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSchedule?view=azps-3.7.0) |Vytvoří nový plán. |
| [Register-AzAutomationScheduledRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Register-AzAutomationScheduledRunbook?view=azps-3.7.0) |Přidruží runbook k plánu. |
| [Odebrat azautomationschedule](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationSchedule?view=azps-3.7.0) |Odebere plán. |
| [Set-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationSchedule?view=azps-3.7.0) |Nastaví vlastnosti existujícího plánu. |
| [Zrušit registraci-AzAutomationScheduledRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Unregister-AzAutomationScheduledRunbook?view=azps-3.7.0) |Disociace runbook z plánu. |

## <a name="creating-a-schedule"></a>Vytvoření plánu

Nový plán pro runbooky můžete vytvořit na webu Azure Portal nebo s PowerShellem.

> [!NOTE]
> Azure Automation používá nejnovější moduly ve vašem účtu Automation při spuštění nové naplánované úlohy.  Chcete-li se vyhnout dopadu na vaše sady Runbook a procesy, které automatizují, měli byste nejprve otestovat všechny sady Runbook, které propojili plány s účtem Automation vyhrazeným pro testování.  Tím ověříte, že naplánované sady Runbook nadále fungují správně, a pokud ne, můžete dále řešit potíže a použít všechny změny potřebné před migrací aktualizované verze sady Runbook do produkčního prostředí.
> Váš účet Automatizace nezíská automaticky žádné nové verze modulů, pokud jste je aktualizovali ručně výběrem [možnosti Aktualizovat moduly Azure](../automation-update-azure-modules.md) z **modulů**.

### <a name="create-a-new-schedule-in-the-azure-portal"></a>Vytvoření nového plánu na webu Azure Portal

1. Na webu Azure Portal z účtu automatizace vyberte **plány** v části **Sdílené prostředky** na levé straně.
2. V horní části stránky klikněte na **Přidat plán.**
3. V podokně Nový plán zadejte název a případně popis nového plánu.
4. Vyberte, zda se plán spustí jednou nebo podle opakovaného plánu, a to výběrem **možnosti Jednou** nebo **Opakované**. Pokud vyberete **Jednou**, zadejte čas zahájení a klepněte na tlačítko **Vytvořit**. Pokud vyberete **Možnost Opakované**, zadejte čas zahájení. V **poli Opakovat každý**, vyberte, jak často se má runbook opakovat – podle hodiny, dne, týdne nebo měsíce.
    1. Pokud vyberete **týden**, zobrazí se dny v týdnu, ze kterých si můžete vybrat. Vyberte tolik dní, kolik chcete. První spuštění plánu proběhne první den vybraný po počátečním čase. Chcete-li například vybrat plán víkendu, vyberte sobotu a neděli. 
    
       ![Nastavení víkendového periodického plánu](../media/schedules/week-end-weekly-recurrence.png)

    2. Pokud vyberete **měsíc**, budete mít různé možnosti. Pro možnost **Měsíční výskyty** vyberte buď **dny měsíce,** nebo **Dny týdne**. Pokud zvolíte **Měsíce –** zobrazí se kalendář, který vám umožní vybrat si libovolný počet dní. Pokud zvolíte datum, jako je například 31. Pokud chcete, aby byl plán spuštěn poslední den, zvolte **Ano** v části **Spustit poslední den v měsíci**. Pokud zvolíte **Dny v týdnu**, zobrazí se **možnost Opakovat každou** možnost. Zvolte **První**, **Druhý,** **Třetí,** **Čtvrtý**nebo **Poslední**. Nakonec si vyberte den, který chcete opakovat.

       ![Měsíční plán na první, patnáctý a poslední den v měsíci](../media/schedules/monthly-first-fifteenth-last.png)

5. Po dokončení klepněte na **tlačítko Vytvořit**.

### <a name="create-a-new-schedule-with-powershell"></a>Vytvoření nového plánu pomocí PowerShellu

Rutina [New-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSchedule?view=azps-3.7.0) slouží k vytváření plánů. Zadáte čas zahájení plánu a frekvenci, kterou má spustit. Následující příklady ukazují, jak vytvořit mnoho různých scénářů plánu.

#### <a name="create-a-one-time-schedule"></a>Vytvoření jednorázového plánu

Následující ukázkové příkazy vytvoří jednorázový plán.

```azurepowershell-interactive
$TimeZone = ([System.TimeZoneInfo]::Local).Id
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule01" -StartTime "23:00" -OneTime -ResourceGroupName "ResourceGroup01" -TimeZone $TimeZone
```

#### <a name="create-a-recurring-schedule"></a>Vytvoření opakovaného plánu

Následující příklad ukazuje, jak vytvořit opakovaný plán, který běží každý den v 1:00 PM po dobu jednoho roku.

```azurepowershell-interactive
$StartTime = Get-Date "13:00:00"
$EndTime = $StartTime.AddYears(1)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule02" -StartTime $StartTime -ExpiryTime $EndTime -DayInterval 1 -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule"></a>Vytvoření týdenního opakovaného plánu

Následující příklad ukazuje, jak vytvořit týdenní plán, který běží pouze ve všední dny.

```azurepowershell-interactive
$StartTime = (Get-Date "13:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekDays = @([System.DayOfWeek]::Monday..[System.DayOfWeek]::Friday)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule03" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule-for-weekends"></a>Vytvoření týdenního opakovaného plánu pro víkendy

Následující ukázkové příkazy ukazují, jak vytvořit týdenní plán, který běží pouze o víkendech.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekendDays = @([System.DayOfWeek]::Saturday,[System.DayOfWeek]::Sunday)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Weekends 6PM" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekendDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-recurring-schedule-for-first-15th-and-last-days-of-the-month"></a>Vytvoření opakovaného plánu pro první, patnáctý a poslední dny v měsíci

Následující příklad ukazuje, jak vytvořit opakovaný plán, který běží na 1st, 15th a poslední den v měsíci.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
New-AzAutomationSchedule -AutomationAccountName "TestAzureAuto" -Name "1st, 15th and Last" -StartTime $StartTime -DaysOfMonth @("One", "Fifteenth", "Last") -ResourceGroupName "TestAzureAuto" -MonthInterval 1
```

## <a name="linking-a-schedule-to-a-runbook"></a>Propojení plánu se knihou Runbook

Runbook můžete spojit s víc plány a k jednomu plánu může být připojených víc Runbooků. Pokud má runbook parametry, můžete pro ně zadat hodnoty. Musíte zadat hodnoty pro všechny povinné parametry a může poskytnout hodnoty pro všechny volitelné parametry. Tyto hodnoty se používají při každém spuštění runbooku tímto plánem. Můžete připojit stejnou runbook k jinému plánu a zadat různé hodnoty parametrů.

### <a name="link-a-schedule-to-a-runbook-with-the-azure-portal"></a>Propojení plánu s runbookem s portálem Azure

1. Na portálu Azure z účtu automatizace vyberte **Runbook v** části **Automatizace procesů**.
2. Klikněte na název Runbooku, který chcete naplánovat.
3. Pokud sada Runbook není aktuálně propojena s plánem, bude vám nabídnuta možnost vytvořit nový plán nebo propojit existující plán.
4. Pokud sada Runbook má parametry, můžete vybrat možnost **Změnit nastavení spuštění (Default:Azure)** a zobrazí se podokno Parametry. Zde můžete zadat informace o parametrech.

### <a name="link-a-schedule-to-a-runbook-with-powershell"></a>Propojení plánu s runbookem s PowerShellem

Pomocí rutiny [Register-AzAutomationScheduledRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Register-AzAutomationScheduledRunbook?view=azps-3.7.0) propojte plán. Parametry Runbooku můžete zadat pomocí parametru Parametry . Další informace o určení hodnot parametrů najdete v [tématu Spuštění sady Runbook v Azure Automation](../automation-starting-a-runbook.md).
Následující příklad ukazuje, jak propojit plán s runbook pomocí rutiny Správce prostředků Azure s parametry.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$runbookName = "Test-Runbook"
$scheduleName = "Sample-DailySchedule"
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Register-AzAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
–Name $runbookName –ScheduleName $scheduleName –Parameters $params `
-ResourceGroupName "ResourceGroup01"
```

## <a name="scheduling-runbooks-to-run-more-frequently"></a>Plánování runbooků pro častější spouštění

Nejčastější interval, pro který lze nakonfigurovat plán v Azure Automation, je jedna hodina. Pokud požadujete plány pro provádění častěji než to, existují dvě možnosti:

* Vytvořte [webhookpro](../automation-webhooks.md) runbook a pomocí [Azure Logic Apps](../../logic-apps/logic-apps-overview.md) volat webhooku. Azure Logic Apps poskytuje více jemně odstupňované rozlišovací schopnost při definování plánu.

* Vytvořte čtyři plány, které začínají do 15 minut od sebe, které se spouštějí jednou za hodinu. Tento scénář umožňuje spuštění runbooku každých 15 minut s různými plány.

## <a name="disabling-a-schedule"></a>Zakázání plánu

Když zakážete plán, všechny runbook s ním spojené již běží na tomto plánu. Můžete ručně zakázat plán nebo nastavit čas vypršení platnosti pro plány s frekvencí při jejich vytváření. Po dosažení doby vypršení platnosti je plán zakázán.

### <a name="disable-a-schedule-from-the-azure-portal"></a>Zakázání plánu z webu Azure Portal

1. V účtu Automation vyberte **plány v** části **Sdílené prostředky**.
2. Kliknutím na název plánu otevřete podokno podrobností.
3. Změnit **povoleno** na **ne**.

> [!NOTE]
> Chcete-li zakázat plán, který má čas zahájení v minulosti, je nutné změnit počáteční datum na čas v budoucnu před uložením.

### <a name="disable-a-schedule-with-powershell"></a>Zakázání plánu pomocí PowerShellu

Pomocí rutiny [Set-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationSchedule?view=azps-3.7.0) můžete změnit vlastnosti existujícího plánu. Chcete-li zakázat plán, `IsEnabled` zadejte false pro parametr.

Následující příklad ukazuje, jak zakázat plán pro runbook pomocí rutiny Správce prostředků Azure.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Set-AzAutomationSchedule –AutomationAccountName $automationAccountName `
–Name $scheduleName –IsEnabled $false -ResourceGroupName "ResourceGroup01"
```

## <a name="next-steps"></a>Další kroky

* Pokud chcete začít s runbooky v Azure Automation, [přečtěte si informace o spuštění runbooku v Azure Automation](../automation-starting-a-runbook.md).