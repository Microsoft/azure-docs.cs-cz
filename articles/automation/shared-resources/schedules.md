---
title: Spravovat plány v Azure Automation
description: V tomto článku se dozvíte, jak vytvořit a pracovat s plánem v Azure Automation.
services: automation
ms.subservice: shared-capabilities
ms.date: 09/10/2020
ms.topic: conceptual
ms.openlocfilehash: f40e3d555d6e1472b9d2368a114ee27d588f6383
ms.sourcegitcommit: 6776f0a27e2000fb1acb34a8dddc67af01ac14ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2021
ms.locfileid: "103149473"
---
# <a name="manage-schedules-in-azure-automation"></a>Spravovat plány v Azure Automation

Pokud chcete naplánovat spuštění Runbooku v Azure Automation v zadanou dobu, můžete ho propojit s jedním nebo více plány. Plán se dá nakonfigurovat tak, aby se spouštěl buď jednou, nebo podle opakovaného hodinového nebo denního plánu pro Runbooky v Azure Portal. Můžete je také naplánovat na týdenní, měsíční, konkrétní dny v týdnu nebo dny v měsíci nebo určitý den v měsíci. Runbook můžete spojit s víc plány a k jednomu plánu může být připojených víc Runbooků.

> [!NOTE]
> Azure Automation podporuje letní čas a plánuje je vhodným způsobem pro operace automatizace.

> [!NOTE]
> V plánech aktuálně nejsou povoleny Azure Automation konfigurací DSC.

## <a name="powershell-cmdlets-used-to-access-schedules"></a>Rutiny PowerShellu používané pro přístup k plánům

Rutiny v následující tabulce vytvářejí a spravují plány automatizace pomocí PowerShellu. Dodávají se jako součást [AZ moduls](modules.md#az-modules).

| Rutiny | Description |
|:--- |:--- |
| [Get-AzAutomationSchedule](/powershell/module/Az.Automation/Get-AzAutomationSchedule) |Načte plán. |
| [Get-AzAutomationScheduledRunbook](/powershell/module/az.automation/get-azautomationscheduledrunbook) |Načte naplánované Runbooky. |
| [New-AzAutomationSchedule](/powershell/module/Az.Automation/New-AzAutomationSchedule) |Vytvoří nový plán. |
| [Registrovat – AzAutomationScheduledRunbook](/powershell/module/Az.Automation/Register-AzAutomationScheduledRunbook) |Přidruží sadu Runbook k plánu. |
| [Remove-AzAutomationSchedule](/powershell/module/Az.Automation/Remove-AzAutomationSchedule) |Odebere plán. |
| [Set-AzAutomationSchedule](/powershell/module/Az.Automation/Set-AzAutomationSchedule) |Nastaví vlastnosti pro existující plán. |
| [Zrušit registraci – AzAutomationScheduledRunbook](/powershell/module/Az.Automation/Unregister-AzAutomationScheduledRunbook) |Dissociates sadu Runbook z plánu. |

## <a name="create-a-schedule"></a>Vytvořit plán

Můžete vytvořit nový plán pro Runbooky v Azure Portal nebo pomocí PowerShellu. Abyste se vyhnuli vlivu na vaše Runbooky a procesy, které automatizují, měli byste nejprve otestovat všechny Runbooky s propojenými plány s účtem služby Automation vyhrazeným pro testování. Test ověří, zda budou naplánované Runbooky nadále fungovat správně. Pokud se zobrazí problém, můžete řešit problémy a před migrací aktualizované verze Runbooku do produkčního prostředí provést nějaké změny.

> [!NOTE]
> Váš účet Automation automaticky nezíská žádné nové verze modulů, pokud jste je neaktualizovali ručně, a to tak, že vyberete možnost [aktualizovat moduly Azure](../automation-update-azure-modules.md) z **modulů**. Azure Automation používá nejnovější moduly v účtu Automation při spuštění nové naplánované úlohy. 

### <a name="create-a-new-schedule-in-the-azure-portal"></a>Vytvořit nový plán v Azure Portal

1. Z účtu Automation v levém podokně vyberte **plány** v části **sdílené prostředky**.
2. Na stránce **plány** vyberte **Přidat plán**.
3. Na stránce **Nový plán** zadejte název a volitelně zadejte popis nového plánu.

    >[!NOTE]
    >Plány automatizace v současné době nepodporují používání speciálních znaků v názvu plánu.
    >

4. Vyberte, jestli se plán spouští jednou nebo v plánu **opakování**, a to tak, že vyberete **jednou** nebo znovu. Pokud vyberete jednu z **nich, zadejte** čas spuštění a pak vyberte **vytvořit**. Pokud vyberete možnost **opakovaná**, zadejte čas spuštění. U **každého opakování** vyberte, jak často se má sada Runbook opakovat. Vyberte podle hodin, dnů, týdnů nebo měsíců.

    * Pokud vyberete možnost **týden**, zobrazí se dny v týdnu, ze kterých si můžete vybrat. Vyberte tolik dní, kolik chcete. První spuštění vašeho plánu nastane první den, který se vybere po počátečním čase. Chcete-li například vybrat víkendový plán, vyberte možnost sobota a neděle.

    ![Nastavování opakovaného plánu na víkend](../media/schedules/week-end-weekly-recurrence.png)

    * Pokud vyberete možnost **month (měsíc**), budete mít k disdílně různé možnosti. Pro možnost **měsíčních výskytů** vyberte buď **dny v měsíci** nebo **dny v týdnu**. Pokud vyberete **dny v měsíci**, zobrazí se kalendář, abyste si mohli vybrat tolik dní, kolik chcete. Pokud zvolíte datum, například 31, ke kterému nedochází v aktuálním měsíci, plán se nespustí. Pokud chcete, aby plán běžel za poslední den, vyberte v části **Spustit poslední den v měsíci** možnost **Ano** . Pokud vyberete **dny v týdnu**, zobrazí se **všechny možnosti opakování** . Vyberte **první**, **druhý**, **třetí**, **čtvrtý** nebo **Poslední**. Nakonec vyberte den, kdy se má opakovat.

    ![Měsíční plán na prvních, patnáct a poslední den v měsíci](../media/schedules/monthly-first-fifteenth-last.png)

5. Až budete hotovi, vyberte **vytvořit**.

### <a name="create-a-new-schedule-with-powershell"></a>Vytvoření nového plánu pomocí PowerShellu

K vytvoření plánů použijte rutinu [New-AzAutomationSchedule](/powershell/module/Az.Automation/New-AzAutomationSchedule) . Zadejte čas spuštění plánu a frekvenci, kterou má spustit. Následující příklady ukazují, jak vytvořit mnoho různých scénářů plánování.

>[!NOTE]
>Plány automatizace v současné době nepodporují používání speciálních znaků v názvu plánu.
>

#### <a name="create-a-one-time-schedule"></a>Vytvoření jednorázového plánu

Následující příklad vytvoří Jednorázový plán.

```azurepowershell-interactive
$TimeZone = ([System.TimeZoneInfo]::Local).Id
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule01" -StartTime "23:00" -OneTime -ResourceGroupName "ResourceGroup01" -TimeZone $TimeZone
```

#### <a name="create-a-recurring-schedule"></a>Vytvoření plánu opakování

Následující příklad ukazuje, jak vytvořit opakovaný plán, který se spouští každý den v 1:00./odp. za rok.

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

Následující příklad ukazuje, jak vytvořit týdenní plán, který běží pouze na víkendech.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekendDays = @([System.DayOfWeek]::Saturday,[System.DayOfWeek]::Sunday)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Weekends 6PM" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekendDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-recurring-schedule-for-the-first-fifteenth-and-last-days-of-the-month"></a>Vytvoření opakovaného plánu pro první, patnáctý a poslední den v měsíci

Následující příklad ukazuje, jak vytvořit opakovaný plán, který běží na prvním, patnáctém a posledním dni v měsíci.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
New-AzAutomationSchedule -AutomationAccountName "TestAzureAuto" -Name "1st, 15th and Last" -StartTime $StartTime -DaysOfMonth @("One", "Fifteenth", "Last") -ResourceGroupName "TestAzureAuto" -MonthInterval 1
```

## <a name="create-a-schedule-with-a-resource-manager-template"></a>Vytvoření plánu pomocí šablony Správce prostředků

V tomto příkladu používáme šablonu automatizace Správce prostředků (ARM), která vytvoří nový plán úlohy. Obecné informace o této šabloně pro správu plánů úloh služby Automation najdete v tématu Referenční dokumentace k [šablonám Microsoft. Automation automationAccounts/jobSchedules](/templates/microsoft.automation/automationaccounts/jobschedules#quickstart-templates).

Zkopírujte tento soubor šablony do textového editoru:

```json
{
  "name": "5d5f3a05-111d-4892-8dcc-9064fa591b96",
  "type": "Microsoft.Automation/automationAccounts/jobSchedules",
  "apiVersion": "2015-10-31",
  "properties": {
    "schedule": {
      "name": "scheduleName"
    },
    "runbook": {
      "name": "runbookName"
    },
    "runOn": "hybridWorkerGroup",
    "parameters": {}
  }
}
```

Upravte následující hodnoty parametrů a uložte šablonu jako soubor JSON:

* Název objektu plánu úlohy: jako název objektu plánu úlohy se používá identifikátor GUID (globálně jedinečný identifikátor).

   >[!IMPORTANT]
   > Pro každý plán úlohy nasazený pomocí šablony ARM musí být identifikátor GUID jedinečný. I v případě, že přeplánujete stávající plán, budete muset změnit identifikátor GUID. To platí i v případě, že jste dříve odstranili existující plán úlohy, který byl vytvořen se stejnou šablonou. Výsledkem opětovného použití stejného identifikátoru GUID je nasazení, které selhalo.</br></br>
   > K dispozici jsou služby online, které mohou vygenerovat nový identifikátor GUID, například tento [bezplatný generátor GUID online](https://guidgenerator.com/).

* Název plánu: představuje název plánu úlohy služby Automation, který bude propojený s určenou sadou Runbook.
* Název Runbooku: představuje název Runbooku služby Automation, ke kterému se má plán úlohy přidružit.

Po uložení souboru můžete plán úlohy Runbooku vytvořit pomocí následujícího příkazu PowerShellu. Příkaz používá `TemplateFile` parametr k zadání cesty a názvu souboru šablony.

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "ContosoEngineering" -TemplateFile "<path>\RunbookJobSchedule.json"
```

## <a name="link-a-schedule-to-a-runbook"></a>Propojení plánu k sadě Runbook

Runbook můžete spojit s víc plány a k jednomu plánu může být připojených víc Runbooků. Pokud sada Runbook obsahuje parametry, můžete pro ně zadat jejich hodnoty. Je nutné zadat hodnoty pro všechny povinné parametry a také můžete zadat hodnoty pro všechny volitelné parametry. Tyto hodnoty se používají při každém spuštění sady Runbook tímto plánem. Stejný Runbook můžete připojit k jinému plánu a zadat jiné hodnoty parametrů.

### <a name="link-a-schedule-to-a-runbook-with-the-azure-portal"></a>Připojení plánu k sadě Runbook pomocí Azure Portal

1. V Azure Portal na svém účtu Automation v části **Automatizace procesu** vyberte **Runbooky** .
1. Vyberte název Runbooku, který chcete naplánovat.
1. Pokud sada Runbook není aktuálně propojená s plánem, nabídne se vám možnost vytvořit nový plán nebo propojit s existujícím plánem.
1. Pokud má Runbook parametry, můžete vybrat možnost **Upravit nastavení spouštění (výchozí: Azure)** a zobrazí se podokno **parametry** . Sem můžete zadat informace o parametrech.

### <a name="link-a-schedule-to-a-runbook-with-powershell"></a>Připojení plánu k Runbooku pomocí PowerShellu

K propojení plánu použijte rutinu [Register-AzAutomationScheduledRunbook](/powershell/module/Az.Automation/Register-AzAutomationScheduledRunbook) . Parametry Runbooku můžete zadat pomocí parametru Parametry . Další informace o tom, jak zadat hodnoty parametrů, najdete [v tématu Spuštění Runbooku v Azure Automation](../start-runbooks.md).
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

## <a name="schedule-runbooks-to-run-more-frequently"></a>Plánování spouštění Runbooků častěji

Častý interval, pro který je možné nakonfigurovat plán ve Azure Automation, je jedna hodina. Pokud vyžadujete, aby se plány spouštěly častěji než to, máte dvě možnosti:

* Vytvořte [Webhook](../automation-webhooks.md) pro Runbook a použijte [Azure Logic Apps](../../logic-apps/logic-apps-overview.md) pro volání Webhooku. Azure Logic Apps poskytuje přesnější členitost k definování plánu.

* Vytvořte čtyři plány, které začínají během 15 minut od sebe, a každý den se spustí jednou za hodinu. Tento scénář umožňuje spuštění sady Runbook každých 15 minut s různými plány.

## <a name="disable-a-schedule"></a>Zakázat plán

Když plán zakážete, na tento plán se už nespustí žádná sada Runbook, na kterou se odkazuje. Můžete ručně zakázat plán nebo nastavit čas vypršení platnosti plánů s frekvencí při jejich vytváření. Po dosažení doby vypršení platnosti je plán zakázán.

### <a name="disable-a-schedule-from-the-azure-portal"></a>Zakázat plán z Azure Portal

1. V účtu Automation v levém podokně vyberte **plány** v části **sdílené prostředky**.
1. Vyberte název plánu a otevřete tak podokno podrobností.
1. Změňte možnost **povoleno** na **ne**.

> [!NOTE]
> Pokud chcete zakázat plán, který má čas začátku v minulosti, musíte změnit počáteční datum na čas v budoucnosti před tím, než ho uložíte.

### <a name="disable-a-schedule-with-powershell"></a>Zakázání plánu pomocí PowerShellu

Pomocí rutiny [set-AzAutomationSchedule](/powershell/module/Az.Automation/Set-AzAutomationSchedule) můžete změnit vlastnosti stávajícího plánu. Pokud chcete plán zakázat, zadejte pro parametr hodnotu false `IsEnabled` .

Následující příklad ukazuje, jak zakázat plán pro sadu Runbook pomocí rutiny Azure Resource Manager.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Set-AzAutomationSchedule –AutomationAccountName $automationAccountName `
–Name $scheduleName –IsEnabled $false -ResourceGroupName "ResourceGroup01"
```

## <a name="remove-a-schedule"></a>Odebrat plán

Až budete připraveni k odebrání plánů, můžete použít Azure Portal nebo PowerShell. Mějte na paměti, že můžete odebrat jenom plán, který je zakázaný, jak je popsané v předchozí části.

### <a name="remove-a-schedule-using-the-azure-portal"></a>Odebrání plánu pomocí Azure Portal

1. V účtu Automation v levém podokně vyberte **plány** v části **sdílené prostředky**.
2. Vyberte název plánu a otevřete tak podokno podrobností.
3. Klikněte na **Odstranit**.

### <a name="remove-a-schedule-with-powershell"></a>Odebrání plánu pomocí PowerShellu

Pomocí `Remove-AzAutomationSchedule` rutiny, jak je znázorněno níže, můžete odstranit stávající plán.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Remove-AzAutomationSchedule -AutomationAccountName $automationAccountName `
-Name $scheduleName -ResourceGroupName "ResourceGroup01"
```

## <a name="next-steps"></a>Další kroky

* Další informace o rutinách, které se používají pro přístup k plánům, najdete v tématu [Správa modulů v Azure Automation](modules.md).
* Obecné informace o sadách Runbook naleznete [v tématu Spuštění Runbooku v Azure Automation](../automation-runbook-execution.md).