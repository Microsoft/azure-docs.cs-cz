---
title: Azure Monitor – ukázky rychlého zprovoznění s využitím PowerShellu
description: Pomocí PowerShellu můžete přistupovat k funkcím Azure Monitoru, jako jsou automatické škálování, výstrahy, webhooky a prohledávání protokolů aktivit.
ms.subservice: ''
ms.topic: conceptual
ms.date: 2/14/2018
ms.openlocfilehash: 9f039f71954998ef561d1efd1e559318740c86ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274317"
---
# <a name="azure-monitor-powershell-quick-start-samples"></a>Azure Monitor – ukázky rychlého zprovoznění s využitím PowerShellu
Tento článek ukazuje ukázkové příkazy PowerShellu, které vám pomůžou získat přístup k funkcím Azure Monitoru.

> [!NOTE]
> Azure Monitor je nový název pro to, co se nazývalo "Azure Insights" až do 25.9.2016. Obory názvů a tedy následující příkazy však stále obsahují slovo "insights".

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="set-up-powershell"></a>Nastavení PowerShellu
Pokud jste tak ještě neučinili, nastavte PowerShell pro spuštění v počítači. Další informace naleznete v tématu [Jak nainstalovat a nakonfigurovat prostředí PowerShell](/powershell/azure/overview).

## <a name="examples-in-this-article"></a>Příklady v tomto článku
Příklady v článku ilustrují, jak můžete použít rutiny Azure Monitor. Můžete také zkontrolovat celý seznam rutin Azure Monitor PowerShell na [Rutiny Azure Monitor (Insights).](https://docs.microsoft.com/powershell/module/az.applicationinsights)

## <a name="sign-in-and-use-subscriptions"></a>Přihlášení a používání předplatných
Nejprve se přihlaste k předplatnému Azure.

```powershell
Connect-AzAccount
```

Zobrazí se přihlašovací obrazovka. Po přihlášení se zobrazí Id tenanta a výchozí ID předplatného. Všechny rutiny Azure fungují v kontextu výchozího předplatného. Chcete-li zobrazit seznam předplatných, ke které máte přístup, použijte následující příkaz:

```powershell
Get-AzSubscription
```

Chcete-li zobrazit pracovní kontext (proti kterému předplatnému jsou vaše příkazy spuštěny), použijte následující příkaz:

```powershell
Get-AzContext
```
Chcete-li změnit pracovní kontext na jiné předplatné, použijte následující příkaz:

```powershell
Set-AzContext -SubscriptionId <subscriptionid>
```


## <a name="retrieve-activity-log-for-a-subscription"></a>Načíst protokol aktivit pro předplatné
Použijte rutinu [Get-AzLog.](https://docs.microsoft.com/powershell/module/az.monitor/get-azlog)  Níže jsou uvedeny některé běžné příklady. Protokol aktivit obsahuje posledních 90 dní operací. Použití dat před touto dobou má za následek chybovou zprávu.  

Podívejte se, jaké je aktuální datum a čas pro ověření toho, jaké časy použít v následujících příkazech:
```powershell
Get-Date
```

Získejte položky protokolu od tohoto času/ data až po současnost:

```powershell
Get-AzLog -StartTime 2019-03-01T10:30
```

Získejte položky protokolu mezi časem a rozsahem dat:

```powershell
Get-AzLog -StartTime 2019-01-01T10:30 -EndTime 2015-01-01T11:30
```

Získejte položky protokolu z určité skupiny prostředků:

```powershell
Get-AzLog -ResourceGroup 'myrg1'
```

Získejte položky protokolu od určitého poskytovatele prostředků mezi časem a rozsahem dat:

```powershell
Get-AzLog -ResourceProvider 'Microsoft.Web' -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Získejte všechny položky protokolu s konkrétním volajícím:

```powershell
Get-AzLog -Caller 'myname@company.com'
```

Následující příkaz načte posledních 1000 událostí z protokolu aktivit:

```powershell
Get-AzLog -MaxRecord 10
```

`Get-AzLog`podporuje mnoho dalších parametrů. Další `Get-AzLog` informace naleznete v odkazu.

> [!NOTE]
> `Get-AzLog`poskytuje pouze 15 dní historie. Pomocí parametru **-MaxRecords** můžete zadat dotaz na poslední N události za 15 dní. Chcete-li získat přístup k událostem starším než 15 dní, použijte rozhraní REST API nebo sadu SDK (ukázka jazyka C# pomocí sady SDK). Pokud nezahrnete **starttime**, pak je výchozí hodnota **EndTime** mínus jedna hodina. Pokud nezahrnete **EndTime**, bude výchozí hodnota aktuální čas. Všechny časy jsou v UTC.
> 
> 

## <a name="retrieve-alerts-history"></a>Načtení historie výstrah
Chcete-li zobrazit všechny události výstrahy, můžete dotaz protokoly Správce prostředků Azure pomocí následujících příkladů.

```powershell
Get-AzLog -Caller "Microsoft.Insights/alertRules" -DetailedOutput -StartTime 2015-03-01
```

Chcete-li zobrazit historii pro konkrétní pravidlo `Get-AzAlertHistory` výstrahy, můžete použít rutinu, předávání ID prostředku pravidla výstrahy.

```powershell
Get-AzAlertHistory -ResourceId /subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/alertrules/myalert -StartTime 2016-03-1 -Status Activated
```

Rutina `Get-AzAlertHistory` podporuje různé parametry. Další informace naleznete v [tématu Get-AlertHistory](https://msdn.microsoft.com/library/mt282453.aspx).

## <a name="retrieve-information-on-alert-rules"></a>Načtení informací o pravidlech výstrah
Všechny následující příkazy působí na skupinu prostředků s názvem "montest".

Zobrazení všech vlastností pravidla výstrahy:

```powershell
Get-AzAlertRule -Name simpletestCPU -ResourceGroup montest -DetailedOutput
```

Načíst všechny výstrahy ve skupině prostředků:

```powershell
Get-AzAlertRule -ResourceGroup montest
```

Načtěte všechna pravidla výstrah nastavená pro cílový prostředek. Například všechna pravidla výstrah nastavená na virtuálním počítači.

```powershell
Get-AzAlertRule -ResourceGroup montest -TargetResourceId /subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig
```

`Get-AzAlertRule`podporuje další parametry. Další informace naleznete v [tématu Get-AlertRule.](https://msdn.microsoft.com/library/mt282459.aspx)

## <a name="create-metric-alerts"></a>Vytvoření upozornění metrik
Pomocí rutiny `Add-AlertRule` můžete vytvořit, aktualizovat nebo zakázat pravidlo výstrahy.

Můžete vytvořit e-mail a `New-AzAlertRuleEmail` `New-AzAlertRuleWebhook`webhookvlastnosti pomocí a , resp. V rutině pravidla výstrahy přiřaďte tyto vlastnosti jako akce vlastnosti **Akce** pravidla výstrahy.

Následující tabulka popisuje parametry a hodnoty použité k vytvoření výstrahy pomocí metriky.

| parametr | value |
| --- | --- |
| Name (Název) |simpletestdiskwrite |
| Umístění tohoto pravidla výstrahy |USA – východ |
| ResourceGroup |montest |
| Id cílového prostředku |/subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig |
| Název metrická výstrahy, která je vytvořena |\PhysicalDisk(_Total)\Zápisy na disku/s. Podívejte `Get-MetricDefinitions` se na rutinu o tom, jak načíst přesné názvy metrik |
|  – operátor |GreaterThan |
| Prahová hodnota (počet/s v pro tuto metriku) |1 |
| WindowSize (hh:mm:ss formát) |00:05:00 |
| agregátor (statistika metriky, která používá průměrný počet, v tomto případě) |Průměr |
| vlastní e-maily (pole řetězců) |'foo@example.com','bar@example.com' |
| posílat e-maily vlastníkům, přispěvatelům a čtenářům |-SendToServiceOwners |

Vytvoření akce E-mailu

```powershell
$actionEmail = New-AzAlertRuleEmail -CustomEmail myname@company.com
```

Vytvoření akce Webhooku

```powershell
$actionWebhook = New-AzAlertRuleWebhook -ServiceUri https://example.com?token=mytoken
```

Vytvoření pravidla výstrahy pro metriku % procesoru na klasickém virtuálním počítači

```powershell
Add-AzMetricAlertRule -Name vmcpu_gt_1 -Location "East US" -ResourceGroup myrg1 -TargetResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.ClassicCompute/virtualMachines/my_vm1 -MetricName "Percentage CPU" -Operator GreaterThan -Threshold 1 -WindowSize 00:05:00 -TimeAggregationOperator Average -Action $actionEmail, $actionWebhook -Description "alert on CPU > 1%"
```

Načtení pravidla výstrahy

```powershell
Get-AzAlertRule -Name vmcpu_gt_1 -ResourceGroup myrg1 -DetailedOutput
```

Rutina Přidat výstrahu také aktualizuje pravidlo, pokud pravidlo výstrahy již existuje pro dané vlastnosti. Chcete-li zakázat pravidlo výstrahy, zahrňte parametr **-DisableRule**.

## <a name="get-a-list-of-available-metrics-for-alerts"></a>Získání seznamu dostupných metrik pro výstrahy
Pomocí rutiny `Get-AzMetricDefinition` můžete zobrazit seznam všech metrik pro konkrétní prostředek.

```powershell
Get-AzMetricDefinition -ResourceId <resource_id>
```

Následující příklad generuje tabulku s metrikou Název a jednotkou pro ni.

```powershell
Get-AzMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Úplný seznam dostupných `Get-AzMetricDefinition` možností je k dispozici na [Get-MetricDefinitions](https://msdn.microsoft.com/library/mt282458.aspx).

## <a name="create-and-manage-activity-log-alerts"></a>Vytváření a správa výstrah protokolu aktivit
Pomocí rutiny `Set-AzActivityLogAlert` můžete nastavit výstrahu protokolu aktivit. Výstraha protokolu aktivit vyžaduje, abyste nejprve definovali podmínky jako slovník podmínek a potom vytvořili výstrahu, která tyto podmínky používá.

```powershell

$condition1 = New-AzActivityLogAlertCondition -Field 'category' -Equal 'Administrative'
$condition2 = New-AzActivityLogAlertCondition -Field 'operationName' -Equal 'Microsoft.Compute/virtualMachines/write'
$additionalWebhookProperties = New-Object "System.Collections.Generic.Dictionary``2[System.String,System.String]"
$additionalWebhookProperties.Add('customProperty', 'someValue')
$actionGrp1 = New-AzActionGroup -ActionGroupId '/subscriptions/<subid>/providers/Microsoft.Insights/actiongr1' -WebhookProperty $additionalWebhookProperties
Set-AzActivityLogAlert -Location 'Global' -Name 'alert on VM create' -ResourceGroupName 'myResourceGroup' -Scope '/subscriptions/<subid>' -Action $actionGrp1 -Condition $condition1, $condition2

```

Další vlastnosti webhooku jsou volitelné. Obsah výstrahy protokolu aktivit můžete získat `Get-AzActivityLogAlert`zpět pomocí aplikace .

## <a name="create-and-manage-autoscale-settings"></a>Vytvoření a správa nastavení automatického škálování
Prostředek (webová aplikace, virtuální počítač, cloudová služba nebo škálovací sada virtuálního počítače) může mít nakonfigurované jenom jedno nastavení automatického škálování.
Každé nastavení automatického škálování však může mít více profilů. Například jeden pro profil škálování založené na výkonu a druhý pro profil založený na plánu. Každý profil může mít nakonfigurované více pravidel. Další informace o automatickém škálování naleznete v [tématu Automatické škálování aplikace](../../cloud-services/cloud-services-how-to-scale-portal.md).

Zde jsou kroky, které je třeba použít:

1. Vytvořit pravidla.
2. Vytvořte profily mapující pravidla, která jste vytvořili dříve, na profily.
3. Volitelné: Vytvořte oznámení pro automatické škálování konfigurací vlastností webhooku a e-mailu.
4. Mapováním profilů a oznámení, která jste vytvořili v předchozích krocích, vytvořte nastavení automatického škálování s názvem cílového prostředku.

Následující příklady ukazují, jak můžete vytvořit nastavení automatického škálování pro škálovací sadu virtuálních strojů pro operační systém Windows na základě metriky využití procesoru.

Nejprve vytvořte pravidlo pro horizontální navýšení kapacity, s nárůstem počtu instancí.

```powershell
$rule1 = New-AzAutoscaleRule -MetricName "Percentage CPU" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 60 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Increase -ScaleActionValue 1
```        

Dále vytvořte pravidlo pro škálování, s poklesem počtu instancí.

```powershell
$rule2 = New-AzAutoscaleRule -MetricName "Percentage CPU" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 30 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Decrease -ScaleActionValue 1
```

Potom vytvořte profil pro pravidla.

```powershell
$profile1 = New-AzAutoscaleProfile -DefaultCapacity 2 -MaximumCapacity 10 -MinimumCapacity 2 -Rules $rule1,$rule2 -Name "My_Profile"
```

Vytvořte vlastnost webhooku.

```powershell
$webhook_scale = New-AzAutoscaleWebhook -ServiceUri "https://example.com?mytoken=mytokenvalue"
```

Vytvořte vlastnost oznámení pro nastavení automatického škálování, včetně e-mailu a webhooku, který jste vytvořili dříve.

```powershell
$notification1= New-AzAutoscaleNotification -CustomEmails ashwink@microsoft.com -SendEmailToSubscriptionAdministrators SendEmailToSubscriptionCoAdministrators -Webhooks $webhook_scale
```

Nakonec vytvořte nastavení automatického škálování a přidejte profil, který jste vytvořili dříve. 

```powershell
Add-AzAutoscaleSetting -Location "East US" -Name "MyScaleVMSSSetting" -ResourceGroup big2 -TargetResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -AutoscaleProfiles $profile1 -Notifications $notification1
```

Další informace o správě nastavení automatického škálování naleznete v [tématu Get-AutoscaleSetting](https://msdn.microsoft.com/library/mt282461.aspx).

## <a name="autoscale-history"></a>Historie automatického škálování
Následující příklad ukazuje, jak můžete zobrazit nedávné události automatického škálování a výstrahy. Pomocí vyhledávání protokolu aktivit můžete zobrazit historii automatického škálování.

```powershell
Get-AzLog -Caller "Microsoft.Insights/autoscaleSettings" -DetailedOutput -StartTime 2015-03-01
```

Rutinu `Get-AzAutoScaleHistory` můžete použít k načtení historie automatického škálování.

```powershell
Get-AzAutoScaleHistory -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/microsoft.insights/autoscalesettings/myScaleSetting -StartTime 2016-03-15 -DetailedOutput
```

Další informace naleznete v [tématu Get-AutoscaleHistory](https://msdn.microsoft.com/library/mt282464.aspx).

### <a name="view-details-for-an-autoscale-setting"></a>Zobrazení podrobností pro nastavení automatického škálování
Pomocí rutiny `Get-Autoscalesetting` můžete načíst další informace o nastavení automatického škálování.

Následující příklad ukazuje podrobnosti o všech nastavení automatického škálování ve skupině prostředků "myrg1".

```powershell
Get-AzAutoscalesetting -ResourceGroup myrg1 -DetailedOutput
```

Následující příklad ukazuje podrobnosti o všech nastavení automatického škálování ve skupině prostředků "myrg1" a konkrétně nastavení automatického škálování s názvem MyScaleVMSSSetting.

```powershell
Get-AzAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting -DetailedOutput
```

### <a name="remove-an-autoscale-setting"></a>Odebrání nastavení automatického škálování
Pomocí rutiny `Remove-Autoscalesetting` můžete odstranit nastavení automatického škálování.

```powershell
Remove-AzAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting
```

## <a name="manage-log-profiles-for-activity-log"></a>Správa profilů protokolu pro protokol aktivit
Můžete vytvořit *profil protokolu* a exportovat data z protokolu aktivit do účtu úložiště a můžete pro něj nakonfigurovat uchovávání dat. Volitelně můžete také streamovat data do centra událostí. Tato funkce je aktuálně ve verzi Preview a můžete vytvořit pouze jeden profil protokolu na jedno předplatné. Pomocí následujících rutin s aktuálním předplatným můžete vytvářet a spravovat profily protokolu. Můžete také zvolit konkrétní předplatné. Přestože prostředí PowerShell výchozí aktuální předplatné, můžete `Set-AzContext`vždy změnit, že pomocí . Protokol aktivit můžete nakonfigurovat tak, aby směroval data do libovolného účtu úložiště nebo centra událostí v rámci tohoto předplatného. Data jsou zapsána jako soubory objektů blob ve formátu JSON.

### <a name="get-a-log-profile"></a>Získání profilu protokolu
Chcete-li načíst existující `Get-AzLogProfile` profily protokolu, použijte rutinu.

### <a name="add-a-log-profile-without-data-retention"></a>Přidání profilu protokolu bez uchovávání dat
```powershell
Add-AzLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Location global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia
```

### <a name="remove-a-log-profile"></a>Odebrání profilu protokolu
```powershell
Remove-AzLogProfile -name my_log_profile_s1
```

### <a name="add-a-log-profile-with-data-retention"></a>Přidání profilu protokolu s uchováváním dat
Můžete zadat **vlastnost -RetentionInDays** s počtem dní jako kladné celé číslo, kde jsou data zachována.

```powershell
Add-AzLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Location global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

### <a name="add-log-profile-with-retention-and-eventhub"></a>Přidání profilu protokolu s uchováváním informací a EventHub
Kromě směrování dat do účtu úložiště, můžete také streamovat do centra událostí. V této verzi preview je konfigurace účtu úložiště povinná, ale konfigurace centra událostí je volitelná.

```powershell
Add-AzLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Location global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

## <a name="configure-diagnostics-logs"></a>Konfigurace protokolů diagnostiky
Mnoho služeb Azure poskytuje další protokoly a telemetrii, které můžou udělat jednu nebo víc z následujících akcí: 
 - nakonfigurovaný pro ukládání dat do účtu Azure Storage
 - odesláno do centra událostí
 - odeslána do pracovního prostoru Analýzy protokolů. 

Operaci lze provést pouze na úrovni prostředků. Účet úložiště nebo centrum událostí by měly být k dispozici ve stejné oblasti jako cílový prostředek, kde je nakonfigurováno nastavení diagnostiky.

### <a name="get-diagnostic-setting"></a>Nastavení diagnostiky
```powershell
Get-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp
```

Zakázat nastavení diagnostiky

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $false
```

Povolit diagnostické nastavení bez uchování

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true
```

Povolit diagnostické nastavení s uchováváním informací

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

Povolit diagnostické nastavení s uchováváním informací pro určitou kategorii protokolu

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/sakteststorage -Categories NetworkSecurityGroupEvent -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

Povolit diagnostické nastavení pro centra událostí

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Enable $true
```

Povolit diagnostické nastavení pro analýzu protokolů

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -WorkspaceId /subscriptions/s1/resourceGroups/insights-integration/providers/providers/microsoft.operationalinsights/workspaces/myWorkspace -Enabled $true

```

Všimněte si, že vlastnost WorkspaceId přebírá *ID prostředku* pracovního prostoru. ID prostředku pracovního prostoru Log Analytics můžete získat pomocí následujícího příkazu:

```powershell
(Get-AzOperationalInsightsWorkspace).ResourceId

```

Tyto příkazy lze kombinovat pro odesílání dat do více cílů.

