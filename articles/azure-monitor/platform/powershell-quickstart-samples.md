---
title: Úvodní ukázky v Azure Powershellu pro monitorování
description: Pomocí prostředí PowerShell pro přístup k funkcím Azure Monitor, jako je například automatické škálování, výstrahy, webhooky a prohledávání protokolů aktivit.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 2/14/2018
ms.author: robb
ms.component: ''
ms.openlocfilehash: 72c50871f0d339d5d8f173a50e03aefc72fdcb36
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2018
ms.locfileid: "53585256"
---
# <a name="azure-monitor-powershell-quick-start-samples"></a>Úvodní ukázky v Azure Powershellu pro monitorování
Tento článek ukazuje ukázkové příkazy prostředí PowerShell můžete získat přístup k funkcím Azure Monitor.

> [!NOTE]
> Azure Monitor je nový název pro co byla volána "Azure Insights" do 25. září 2016. Ale obory názvů a proto následující příkazy stále obsahovat slovo "Přehled".
> 
> 

## <a name="set-up-powershell"></a>Nastavení prostředí PowerShell
Pokud jste tak dosud neučinili, nastavení prostředí PowerShell pro spuštění ve vašem počítači. Další informace najdete v tématu [instalace a konfigurace Powershellu](/powershell/azure/overview).

## <a name="examples-in-this-article"></a>Příklady v tomto článku
Příklady v tomto článku ukazují, jak pomocí rutin Azure Monitor. Můžete také zkontrolovat celý seznam rutin Powershellu pro monitorování Azure v [rutiny Azure Monitor (přehled)](https://docs.microsoft.com/powershell/module/azurerm.insights).

## <a name="sign-in-and-use-subscriptions"></a>Přihlaste se a využívat předplatná
Nejdřív přihlaste ke svému předplatnému Azure.

```PowerShell
Connect-AzureRmAccount
```

Zobrazí přihlašovací obrazovka. Jednou přihlásíte ve vašem účtu, TenantID, a jsou zobrazeny výchozí ID předplatného. Všechny rutiny služby Azure fungují v rámci výchozího předplatného. Chcete-li zobrazit seznam předplatných, ke kterým máte přístup, použijte následující příkaz:

```PowerShell
Get-AzureRmSubscription
```

Chcete-li změnit pracovního kontextu do jiného předplatného, použijte následující příkaz:

```PowerShell
Set-AzureRmContext -SubscriptionId <subscriptionid>
```


## <a name="retrieve-activity-log-for-a-subscription"></a>Získat informace o protokolu aktivit pro odběr
Použití `Get-AzureRmLog` rutiny.  Následují některé běžné příklady.

Získání položky protokolu z tohoto času a data, prezentovat:

```PowerShell
Get-AzureRmLog -StartTime 2016-03-01T10:30
```

Získáte položky protokolu mezi rozsahem čas/datum:

```PowerShell
Get-AzureRmLog -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Získáte položky protokolu v konkrétní skupině prostředků:

```PowerShell
Get-AzureRmLog -ResourceGroup 'myrg1'
```

Získáte položky protokolu ze zprostředkovatele konkrétní prostředek rozsahu času a data:

```PowerShell
Get-AzureRmLog -ResourceProvider 'Microsoft.Web' -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Získáte všechny položky protokolu s konkrétním volající:

```PowerShell
Get-AzureRmLog -Caller 'myname@company.com'
```

Následující příkaz načte poslední 1 000 událostí z protokolu aktivit:

```PowerShell
Get-AzureRmLog -MaxEvents 1000
```

`Get-AzureRmLog` podporuje mnoho dalších parametrů. Zobrazit `Get-AzureRmLog` odkaz Další informace.

> [!NOTE]
> `Get-AzureRmLog` poskytuje pouze 15 dní historie. Použití **- MaxEvents** parametr umožňuje dotazování poslední události N se po uplynutí 15 dnů. Pro přístup k události starší než 15 dnů pomocí rozhraní REST API nebo sady SDK (ukázka C# pomocí sady SDK). Pokud není zadána **StartTime**, pak je výchozí hodnota **EndTime** mínus jedna hodina. Pokud není zadána **EndTime**, pak výchozí hodnotou je aktuální čas. Všechny časy jsou ve formátu UTC.
> 
> 

## <a name="retrieve-alerts-history"></a>Načíst historii výstrah
Chcete-li zobrazit všechny výstrahy událostí, můžete zadávat dotazy protokolů Azure Resource Manageru pomocí následující příklady.

```PowerShell
Get-AzureRmLog -Caller "Microsoft.Insights/alertRules" -DetailedOutput -StartTime 2015-03-01
```

Chcete-li zobrazit historii pro konkrétní pravidlo výstrahy, můžete použít `Get-AzureRmAlertHistory` rutiny, předejte ID prostředku pravidla výstrahy.

```PowerShell
Get-AzureRmAlertHistory -ResourceId /subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/alertrules/myalert -StartTime 2016-03-1 -Status Activated
```

`Get-AzureRmAlertHistory` Rutina podporuje různé parametry. Další informace najdete v tématu [Get-AlertHistory](https://msdn.microsoft.com/library/mt282453.aspx).

## <a name="retrieve-information-on-alert-rules"></a>Načíst informace o pravidla upozornění
Všechny následující příkazy fungovat na skupinu prostředků s názvem "montest".

Zobrazte všechny vlastnosti pravidla výstrahy:

```PowerShell
Get-AzureRmAlertRule -Name simpletestCPU -ResourceGroup montest -DetailedOutput
```

Načtěte všechny výstrahy na skupinu prostředků:

```PowerShell
Get-AzureRmAlertRule -ResourceGroup montest
```

Načtěte všechna pravidla výstrah pro cílový prostředek. Všechna pravidla výstrah je třeba nastavit na virtuálním počítači.

```PowerShell
Get-AzureRmAlertRule -ResourceGroup montest -TargetResourceId /subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig
```

`Get-AzureRmAlertRule` podporuje další parametry. Zobrazit [Get-AlertRule](https://msdn.microsoft.com/library/mt282459.aspx) Další informace.

## <a name="create-metric-alerts"></a>Vytvoření upozornění na metriku
Můžete použít `Add-AlertRule` rutina pro vytvoření, aktualizace nebo zakázání pravidla upozornění.

Můžete vytvořit e-mailu a webhook vlastností pomocí `New-AzureRmAlertRuleEmail` a `New-AzureRmAlertRuleWebhook`v uvedeném pořadí. V rutině pravidlo upozornění přiřadit jako akce, které tyto vlastnosti **akce** vlastnost pravidla výstrahy.

Následující tabulka popisuje parametry a hodnoty použité k vytvoření upozornění použitím metrik.

| parametr | hodnota |
| --- | --- |
| Název |simpletestdiskwrite |
| Umístění tohoto upozornění pravidla |USA – východ |
| ResourceGroup |montest |
| TargetResourceId |/subscriptions/S1/resourceGroups/montest/providers/Microsoft.COMPUTE/virtualMachines/testconfig |
| MetricName výstrahy, která je vytvořena |\PhysicalDisk (využití _celkem) \Disk zápisů za sekundu. Zobrazit `Get-MetricDefinitions` rutiny o tom, jak získat přesné názvy metrik |
| – Operátor |GreaterThan |
| Prahová hodnota (počet za sekundu za pro tuto metriku) |1 |
| Velikost_okna (ve formátu hh: mm:) |00:05:00 |
| agregátoru (Statistika metriky, které v tomto případě používá průměrný počet) |Průměr |
| vlastní e-mailů (pole řetězců) |'foo@example.com','bar@example.com' |
| poslat vlastníci, přispěvatelé a čtenáři e-mailu |-SendToServiceOwners |

Vytvořit e-mailové akce

```PowerShell
$actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
```

Vytvoření akce Webhooku

```PowerShell
$actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://example.com?token=mytoken
```

Vytvořit pravidlo upozornění na metriku % využití procesoru na klasickém virtuálním počítači

```PowerShell
Add-AzureRmMetricAlertRule -Name vmcpu_gt_1 -Location "East US" -ResourceGroup myrg1 -TargetResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.ClassicCompute/virtualMachines/my_vm1 -MetricName "Percentage CPU" -Operator GreaterThan -Threshold 1 -WindowSize 00:05:00 -TimeAggregationOperator Average -Actions $actionEmail, $actionWebhook -Description "alert on CPU > 1%"
```

Načíst pravidlo upozornění.

```PowerShell
Get-AzureRmAlertRule -Name vmcpu_gt_1 -ResourceGroup myrg1 -DetailedOutput
```

Rutiny výstrah přidat také aktualizuje pravidlo, pokud se pravidlo upozornění pro dané vlastnosti již existuje. Pokud chcete zakázat pravidlo upozornění, přidejte parametr **- DisableRule**.

## <a name="get-a-list-of-available-metrics-for-alerts"></a>Získání seznamu dostupných metrik pro výstrahy
Můžete použít `Get-AzureRmMetricDefinition` rutiny zobrazíte seznam všech metrik pro určitý prostředek.

```PowerShell
Get-AzureRmMetricDefinition -ResourceId <resource_id>
```

Následující příklad vytvoří tabulku s názvem metrikou a jednotky pro něj.

```PowerShell
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Úplný seznam dostupných možností pro `Get-AzureRmMetricDefinition` je k dispozici na [Get-MetricDefinitions](https://msdn.microsoft.com/library/mt282458.aspx).

## <a name="create-and-manage-activity-log-alerts"></a>Vytvoření a Správa výstrah protokolu aktivit
Můžete použít `Set-AzureRmActivityLogAlert` rutiny k nastavení upozornění protokolu aktivit. Upozornění protokolu aktivit vyžaduje, že nejprve definovat podmínky jako slovník podmínky, a potom vytvořit výstrahu, která používá tyto podmínky.

```PowerShell

$condition1 = New-AzureRmActivityLogAlertCondition -Field 'category' -Equal 'Administrative'
$condition2 = New-AzureRmActivityLogAlertCondition -Field 'operationName' -Equal 'Microsoft.Compute/virtualMachines/write'
$additionalWebhookProperties = New-Object "System.Collections.Generic.Dictionary``2[System.String,System.String]"
$additionalWebhookProperties.Add('customProperty', 'someValue')
$actionGrp1 = New-AzureRmActionGroup -ActionGroupId '/subscriptions/<subid>/providers/Microsoft.Insights/actiongr1' -WebhookProperty $additionalWebhookProperties
Set-AzureRmActivityLogAlert -Location 'Global' -Name 'alert on VM create' -ResourceGroupName 'myResourceGroup' -Scope '/subscriptions/<subid>' -Action $actionGrp1 -Condition $condition1, $condition2

```

Webhook další vlastnosti jsou volitelné. Obsah pomocí upozornění protokolu aktivit můžete vrátit `Get-AzureRmActivityLogAlert`.

## <a name="create-and-manage-autoscale-settings"></a>Vytvořit a spravovat nastavení automatického škálování
Prostředek (webové aplikace, virtuální počítač, Cloudovou službu nebo Škálovací sady virtuálních počítačů) může mít pouze jeden nakonfigurované pro něj zadané nastavení automatického škálování.
Každé nastavení automatického škálování však může mít několik profilů. Například jeden profil škálování na základě výkonu a druhý pro profil na základě plánu. Každý profil může mít víc pravidel, které jsou nakonfigurované na něm. Další informace o automatické škálování najdete v tématu [postupy automatického škálování aplikace](../../cloud-services/cloud-services-how-to-scale-portal.md).

Tady je postup používání:

1. Vytvoření pravidel.
2. Vytvoření profilů mapování pravidla, která jste vytvořili dříve na profily.
3. Volitelné: Konfigurace webhooků a e-mailu vlastností vytvořte oznámení pro automatické škálování.
4. Vytvoření nastavení automatického škálování s názvem na cílový prostředek namapováním profily a oznámení, které jste vytvořili v předchozích krocích.

Následující příklady ukazují, jak můžete vytvořit nastavení automatického škálování pro sadu škálování virtuálního počítače pro operační systém Windows pomocí metrik využití procesoru.

Nejprve vytvořte pravidlo pro horizontální navýšení kapacity, větší počet instancí.

```PowerShell
$rule1 = New-AzureRmAutoscaleRule -MetricName "Percentage CPU" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 60 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Increase -ScaleActionValue 1
```        

Dále vytvořte pravidlo pro škálování, snížení počtu instanci.

```PowerShell
$rule2 = New-AzureRmAutoscaleRule -MetricName "Percentage CPU" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 30 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Decrease -ScaleActionValue 1
```

Vytvořte profil pro pravidla.

```PowerShell
$profile1 = New-AzureRmAutoscaleProfile -DefaultCapacity 2 -MaximumCapacity 10 -MinimumCapacity 2 -Rules $rule1,$rule2 -Name "My_Profile"
```

Vytvoření vlastnosti webhooku.

```PowerShell
$webhook_scale = New-AzureRmAutoscaleWebhook -ServiceUri "https://example.com?mytoken=mytokenvalue"
```

Vytvořte oznámení vlastnost pro nastavení automatického škálování, včetně e-mailu a webhook, který jste vytvořili dříve.

```PowerShell
$notification1= New-AzureRmAutoscaleNotification -CustomEmails ashwink@microsoft.com -SendEmailToSubscriptionAdministrators SendEmailToSubscriptionCoAdministrators -Webhooks $webhook_scale
```

Nakonec vytvořte nastavení automatického škálování pro přidání profilu, který jste vytvořili dříve. 

```PowerShell
Add-AzureRmAutoscaleSetting -Location "East US" -Name "MyScaleVMSSSetting" -ResourceGroup big2 -TargetResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -AutoscaleProfiles $profile1 -Notifications $notification1
```

Další informace o správě nastavení automatického škálování najdete v tématu [Get-AutoscaleSetting](https://msdn.microsoft.com/library/mt282461.aspx).

## <a name="autoscale-history"></a>Historie automatického škálování
Následující příklad ukazuje, jak zobrazit nedávné automatického škálování a události oznámení. Chcete-li zobrazit historii automatického škálování pomocí prohledávání protokolů aktivit.

```PowerShell
Get-AzureRmLog -Caller "Microsoft.Insights/autoscaleSettings" -DetailedOutput -StartTime 2015-03-01
```

Můžete použít `Get-AzureRmAutoScaleHistory` rutina pro načtení historie automatického škálování.

```PowerShell
Get-AzureRmAutoScaleHistory -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/microsoft.insights/autoscalesettings/myScaleSetting -StartTime 2016-03-15 -DetailedOutput
```

Další informace najdete v tématu [Get-AutoscaleHistory](https://msdn.microsoft.com/library/mt282464.aspx).

### <a name="view-details-for-an-autoscale-setting"></a>Zobrazit podrobnosti pro nastavení automatického škálování
Můžete použít `Get-Autoscalesetting` rutina pro načtení Další informace o nastavení automatického škálování.

Následující příklad ukazuje podrobnosti o všech nastaveních automatického škálování v prostředku skupiny "myrg1".

```PowerShell
Get-AzureRmAutoscalesetting -ResourceGroup myrg1 -DetailedOutput
```

Následující příklad ukazuje podrobnosti o všech nastavení automatického škálování v prostředku skupiny "myrg1" a konkrétně nastavení automatického škálování s názvem "MyScaleVMSSSetting".

```PowerShell
Get-AzureRmAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting -DetailedOutput
```

### <a name="remove-an-autoscale-setting"></a>Odebrat nastavení automatického škálování
Můžete použít `Remove-Autoscalesetting` rutina pro odstranění nastavení automatického škálování.

```PowerShell
Remove-AzureRmAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting
```

## <a name="manage-log-profiles-for-activity-log"></a>Spravovat profily protokolů pro protokol aktivit
Můžete vytvořit *profil protokolu* a export dat z vašeho protokolu aktivit do účtu úložiště a vy můžete nakonfigurovat uchovávání dat pro ni. Volitelně můžete také Streamovat data do vašeho centra událostí. Tato funkce je aktuálně ve verzi Preview a může vytvořit pouze jeden profil protokolu na jedno předplatné. Následující rutiny můžete použít s aktuálním předplatném můžete vytvořit a spravovat profily protokolů. Můžete také ke konkrétnímu předplatnému. I když PowerShell výchozí hodnota je aktuální předplatné, můžete vždycky změnit, že při použití `Set-AzureRmContext`. Můžete nakonfigurovat protokolu aktivit do data trasy pro všechny účty úložiště nebo centra událostí v rámci tohoto předplatného. Data se zapisují jako soubory objektů blob ve formátu JSON.

### <a name="get-a-log-profile"></a>Získat profil protokolu
Chcete-li načíst vaše stávající profily protokolů, použijte `Get-AzureRmLogProfile` rutiny.

### <a name="add-a-log-profile-without-data-retention"></a>Přidat profil protokolu bez uchovávání dat
```PowerShell
Add-AzureRmLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia
```

### <a name="remove-a-log-profile"></a>Odebrání profilu protokolu
```PowerShell
Remove-AzureRmLogProfile -name my_log_profile_s1
```

### <a name="add-a-log-profile-with-data-retention"></a>Přidat profil protokolu s uchováním dat
Můžete zadat **- RetentionInDays** vlastnost s počtem dní jako kladné celé číslo, ve kterém se zachovává data.

```PowerShell
Add-AzureRmLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

### <a name="add-log-profile-with-retention-and-eventhub"></a>Přidat profil protokolu s uchování a centra událostí
Kromě směrování dat do účtu úložiště, můžete také Streamovat je do centra událostí. V této verzi preview konfigurací účtu úložiště je povinný, ale konfigurace centra událostí je volitelná.

```PowerShell
Add-AzureRmLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

## <a name="configure-diagnostics-logs"></a>Konfigurace diagnostických protokolů
Mnoho služeb Azure poskytují další protokoly a telemetrii, které můžete provést jeden nebo více z následujících akcí: 
 - nakonfigurovat k uložení dat v účtu Azure Storage
 - odeslání do Event Hubs
 - odešle do pracovního prostoru Log Analytics. 

Operaci lze provést pouze na úrovni prostředků. Úložiště účtu nebo event hub musí existovat ve stejné oblasti jako cílový prostředek, ve kterém se nakonfiguruje nastavení diagnostiky.

### <a name="get-diagnostic-setting"></a>Získá nastavení diagnostiky.
```PowerShell
Get-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp
```

Zakázání nastavení diagnostiky

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $false
```

Povolení diagnostických nastavení bez uchování

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true
```

Povolení diagnostických nastavení s uchováváním informací.

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

Povolení diagnostických nastavení s uchováním pro konkrétní kategorie

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/sakteststorage -Categories NetworkSecurityGroupEvent -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

Povolení diagnostických nastavení pro službu Event Hubs

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Enable $true
```

Povolení nastavení diagnostiky pro Log Analytics

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -WorkspaceId /subscriptions/s1/resourceGroups/insights-integration/providers/providers/microsoft.operationalinsights/workspaces/myWorkspace -Enabled $true

```

Všimněte si, že má vlastnost ID pracovního prostoru *ID prostředku* pracovního prostoru. Můžete získat ID prostředku pracovního prostoru Log Analytics pomocí následujícího příkazu:

```PowerShell
(Get-AzureRmOperationalInsightsWorkspace).ResourceId

```

Tyto příkazy mohou být kombinovány pro odesílání dat do více cílů.
