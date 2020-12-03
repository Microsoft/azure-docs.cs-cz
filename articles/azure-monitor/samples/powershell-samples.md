---
title: Ukázky Azure Monitor PowerShellu
description: Použijte PowerShell k přístupu k funkcím Azure Monitor, jako je automatické škálování, výstrahy, Webhooky a vyhledávání protokolů aktivit.
ms.subservice: ''
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 2/14/2018
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 4f7ddf94bbd077912cf0d7c2adef2eac548274ca
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96532274"
---
# <a name="azure-monitor-powershell-samples"></a>Ukázky Azure Monitor PowerShellu
Tento článek ukazuje ukázky příkazů PowerShellu, které vám pomůžou při přístupu k funkcím Azure Monitor.

> [!NOTE]
> Azure Monitor je nový název, který se nazývá "Azure Insights" až do září 25.2016. Nicméně obory názvů, takže následující příkazy budou dál obsahovat Word *Insights*.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="set-up-powershell"></a>Nastavení PowerShellu
Pokud jste to ještě neudělali, nastavte PowerShell tak, aby se spouštěl v počítači. Další informace najdete v tématu [instalace a konfigurace PowerShellu](/powershell/azure/).

## <a name="examples-in-this-article"></a>Příklady v tomto článku
Příklady v článku ukazují, jak můžete použít rutiny Azure Monitor. Můžete si také projít celý seznam rutin Azure Monitor PowerShellu na [Azure monitor (Insights) rutiny](/powershell/module/az.applicationinsights).

## <a name="sign-in-and-use-subscriptions"></a>Přihlášení a používání předplatných
Nejdřív se přihlaste ke svému předplatnému Azure.

```powershell
Connect-AzAccount
```

Zobrazí se přihlašovací obrazovka. Po přihlášení účtu se zobrazí TenantID a ID výchozího předplatného. Všechny rutiny Azure fungují v kontextu výchozího předplatného. Chcete-li zobrazit seznam předplatných, ke kterým máte přístup, použijte následující příkaz:

```powershell
Get-AzSubscription
```

Chcete-li zobrazit pracovní kontext (u kterého předplatného jsou spouštěny vaše příkazy), použijte následující příkaz:

```powershell
Get-AzContext
```
Chcete-li změnit svůj pracovní kontext na jiné předplatné, použijte následující příkaz:

```powershell
Set-AzContext -SubscriptionId <subscriptionid>
```


## <a name="retrieve-activity-log"></a>Načíst protokol aktivit
Použijte rutinu [Get-AzLog](/powershell/module/az.monitor/get-azlog) .  Níže jsou uvedeny některé běžné příklady. Protokol aktivit uchovává operace za posledních 90 dnů. Použití dat před tímto časem má za následek chybovou zprávu.  

Podívejte se, jak aktuální datum a čas slouží k ověření, které časy se mají použít v následujících příkazech:
```powershell
Get-Date
```

Získat položky protokolu z tohoto času nebo data k dispozici:

```powershell
Get-AzLog -StartTime 2019-03-01T10:30
```

Získat položky protokolu mezi rozsahem data a času:

```powershell
Get-AzLog -StartTime 2019-01-01T10:30 -EndTime 2015-01-01T11:30
```

Získat položky protokolu z konkrétní skupiny prostředků:

```powershell
Get-AzLog -ResourceGroup 'myrg1'
```

Získat položky protokolu od určitého poskytovatele prostředků mezi rozsahem data a času:

```powershell
Get-AzLog -ResourceProvider 'Microsoft.Web' -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Získat všechny položky protokolu s konkrétním volajícím:

```powershell
Get-AzLog -Caller 'myname@company.com'
```

Následující příkaz načte poslední 1000 události z protokolu aktivit:

```powershell
Get-AzLog -MaxRecord 1000
```

`Get-AzLog` podporuje mnoho dalších parametrů. `Get-AzLog`Další informace najdete v referenčních informacích.

> [!NOTE]
> `Get-AzLog` poskytuje jenom 15 dní historie. S využitím parametru **-MaxRecords** můžete dotazovat posledních N událostí za více než 15 dnů. Pokud chcete získat přístup k událostem starším než 15 dnů, použijte rozhraní REST API nebo sadu SDK (ukázka použití sady SDK v jazyce C#). Pokud nezadáte **počáteční čas**, jeho výchozí hodnota bude **koncový čas** minus jedna hodina. Pokud nezadáte **koncový čas**, jeho výchozí hodnota bude aktuální čas. Všechny časy jsou v UTC.
> 
> 

## <a name="retrieve-alerts-history"></a>Načtení historie výstrah
Chcete-li zobrazit všechny události výstrah, můžete zadat dotaz na protokoly Azure Resource Manager pomocí následujících příkladů.

```powershell
Get-AzLog -Caller "Microsoft.Insights/alertRules" -DetailedOutput -StartTime 2015-03-01
```

Chcete-li zobrazit historii konkrétního pravidla výstrahy, můžete použít `Get-AzAlertHistory` rutinu, která předává ID prostředku pravidla výstrahy.

```powershell
Get-AzAlertHistory -ResourceId /subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/alertrules/myalert -StartTime 2016-03-1 -Status Activated
```

`Get-AzAlertHistory`Rutina podporuje různé parametry. Další informace najdete v tématu [Get-AlertHistory](/previous-versions/azure/mt282453(v=azure.100)).

## <a name="retrieve-information-on-alert-rules"></a>Načíst informace o pravidlech výstrahy
Všechny následující příkazy se chovají ve skupině prostředků s názvem "montest".

Zobrazit všechny vlastnosti pravidla výstrahy:

```powershell
Get-AzAlertRule -Name simpletestCPU -ResourceGroup montest -DetailedOutput
```

Načíst všechny výstrahy ve skupině prostředků:

```powershell
Get-AzAlertRule -ResourceGroup montest
```

Načte všechna pravidla upozornění nastavená pro cílový prostředek. Například všechna pravidla upozornění nastavená na virtuálním počítači.

```powershell
Get-AzAlertRule -ResourceGroup montest -TargetResourceId /subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig
```

`Get-AzAlertRule` podporuje další parametry. Další informace najdete v tématu [Get-AlertRule](/previous-versions/azure/mt282459(v=azure.100)) .

## <a name="create-metric-alerts"></a>Vytvoření upozornění metrik
Pomocí `Add-AlertRule` rutiny můžete vytvořit, aktualizovat nebo zakázat pravidlo výstrahy.

Můžete vytvořit vlastnosti e-mailu a Webhooku pomocí  `New-AzAlertRuleEmail` a v `New-AzAlertRuleWebhook` uvedeném pořadí. V rutině pravidla výstrahy přiřaďte tyto vlastnosti jako akce do vlastnosti **Actions** pravidla výstrahy.

Následující tabulka popisuje parametry a hodnoty používané k vytvoření výstrahy pomocí metriky.

| parameter | value |
| --- | --- |
| Název |simpletestdiskwrite |
| Umístění tohoto pravidla výstrahy |East US |
| ResourceGroup |montest |
| Parametrem targetresourceid |/subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig |
| Metrika vytvořeného upozornění |\PhysicalDisk (_Total) \ zápisu za sekundu. Podívejte se na `Get-MetricDefinitions` rutinu, jak načíst přesné názvy metrik. |
| operátor |GreaterThan |
| Prahová hodnota (počet/s) pro tuto metriku |1 |
| WindowSize (hh: mm: SS formát) |00:05:00 |
| Agregátor (statistika metriky, která v tomto případě používá průměrný počet) |Průměr |
| vlastní e-maily (pole řetězců) |'foo@example.com','bar@example.com' |
| odesílání e-mailů vlastníkům, přispěvatelům a čtenářům |-SendToServiceOwners |

Vytvoření e-mailové akce

```powershell
$actionEmail = New-AzAlertRuleEmail -CustomEmail myname@company.com
```

Vytvoření akce Webhooku

```powershell
$actionWebhook = New-AzAlertRuleWebhook -ServiceUri https://example.com?token=mytoken
```

Vytvoření pravidla výstrahy na procesoru% metriky na klasickém virtuálním počítači

```powershell
Add-AzMetricAlertRule -Name vmcpu_gt_1 -Location "East US" -ResourceGroup myrg1 -TargetResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.ClassicCompute/virtualMachines/my_vm1 -MetricName "Percentage CPU" -Operator GreaterThan -Threshold 1 -WindowSize 00:05:00 -TimeAggregationOperator Average -Action $actionEmail, $actionWebhook -Description "alert on CPU > 1%"
```

Načtení pravidla výstrahy

```powershell
Get-AzAlertRule -Name vmcpu_gt_1 -ResourceGroup myrg1 -DetailedOutput
```

Rutina přidat výstrahu také aktualizuje pravidlo, pokud pravidlo výstrahy již pro dané vlastnosti existuje. Pravidlo výstrahy zakážete tak, že zadáte parametr **-DisableRule**.

## <a name="get-a-list-of-available-metrics-for-alerts"></a>Získat seznam dostupných metrik pro výstrahy
Pomocí `Get-AzMetricDefinition` rutiny můžete zobrazit seznam všech metrik pro konkrétní prostředek.

```powershell
Get-AzMetricDefinition -ResourceId <resource_id>
```

Následující příklad vygeneruje tabulku s názvem metriky a jednotkou pro ni.

```powershell
Get-AzMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Úplný seznam dostupných možností pro `Get-AzMetricDefinition` je k dispozici na adrese [Get-MetricDefinitions](/previous-versions/azure/mt282458(v=azure.100)).

## <a name="create-and-manage-activity-log-alerts"></a>Vytváření a správa výstrah protokolu aktivit
Pomocí `Set-AzActivityLogAlert` rutiny můžete nastavit upozornění protokolu aktivit. Upozornění protokolu aktivit vyžaduje, abyste nejdřív definovali podmínky jako slovník podmínek a pak vytvořili výstrahu, která tyto podmínky používá.

```powershell

$condition1 = New-AzActivityLogAlertCondition -Field 'category' -Equal 'Administrative'
$condition2 = New-AzActivityLogAlertCondition -Field 'operationName' -Equal 'Microsoft.Compute/virtualMachines/write'
$additionalWebhookProperties = New-Object "System.Collections.Generic.Dictionary``2[System.String,System.String]"
$additionalWebhookProperties.Add('customProperty', 'someValue')
$actionGrp1 = New-AzActionGroup -ActionGroupId '/subscriptions/<subid>/providers/Microsoft.Insights/actiongr1' -WebhookProperty $additionalWebhookProperties
Set-AzActivityLogAlert -Location 'Global' -Name 'alert on VM create' -ResourceGroupName 'myResourceGroup' -Scope '/subscriptions/<subid>' -Action $actionGrp1 -Condition $condition1, $condition2

```

Další vlastnosti Webhooku jsou volitelné. Obsah upozornění protokolu aktivit můžete získat zpět pomocí `Get-AzActivityLogAlert` .

## <a name="create-and-manage-autoscale-settings"></a>Vytvoření a Správa nastavení automatického škálování

> [!NOTE] 
> V případě Cloud Services (Microsoft. ClassicCompute) podporuje automatické škálování časové intervaly 5 minut (PT5M). Pro automatické škálování jiných služeb podporuje časové intervaly minimálně 1 minutu (PT1M).

Prostředek (webová aplikace, virtuální počítač, cloudová služba nebo sada škálování virtuálního počítače) může mít nakonfigurované jenom jedno nastavení automatického škálování.
Každé nastavení automatického škálování ale může mít několik profilů. Například jeden pro profil škálování založený na výkonu a druhý pro profil založený na plánu. Pro každý profil může být nakonfigurována více pravidel. Další informace o automatickém škálování najdete v tématu věnovaném [automatickému škálování aplikace](../../cloud-services/cloud-services-how-to-scale-portal.md).

Tady je postup pro použití:

1. Vytvořit pravidla.
2. Vytvořte profily mapování pravidel, která jste předtím vytvořili v profilech.
3. Volitelné: vytvořte oznámení pro automatické škálování konfigurací Webhooku a vlastností e-mailu.
4. Pomocí mapování profilů a oznámení, která jste vytvořili v předchozích krocích, vytvořte nastavení automatického škálování s názvem na cílovém prostředku.

Následující příklady vám ukážou, jak můžete vytvořit nastavení automatického škálování pro sadu škálování virtuálního počítače pro operační systém Windows na základě metriky využití procesoru.

Nejdřív vytvořte pravidlo pro horizontální navýšení kapacity s nárůstem počtu instancí.

```powershell
$rule1 = New-AzAutoscaleRule -MetricName "Percentage CPU" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 60 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Increase -ScaleActionValue 1
```        

Dále vytvořte pravidlo pro horizontální navýšení kapacity s snížením počtu instancí.

```powershell
$rule2 = New-AzAutoscaleRule -MetricName "Percentage CPU" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 30 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Decrease -ScaleActionValue 1
```

Pak vytvořte profil pro pravidla.

```powershell
$profile1 = New-AzAutoscaleProfile -DefaultCapacity 2 -MaximumCapacity 10 -MinimumCapacity 2 -Rules $rule1,$rule2 -Name "My_Profile"
```

Vytvoří vlastnost Webhooku.

```powershell
$webhook_scale = New-AzAutoscaleWebhook -ServiceUri "https://example.com?mytoken=mytokenvalue"
```

Vytvořte vlastnost Notification pro nastavení automatického škálování, včetně e-mailu a Webhooku, který jste vytvořili dříve.

```powershell
$notification1= New-AzAutoscaleNotification -CustomEmails ashwink@microsoft.com -SendEmailToSubscriptionAdministrators SendEmailToSubscriptionCoAdministrators -Webhooks $webhook_scale
```

Nakonec vytvořte nastavení automatického škálování a přidejte profil, který jste vytvořili dříve. 

```powershell
Add-AzAutoscaleSetting -Location "East US" -Name "MyScaleVMSSSetting" -ResourceGroup big2 -TargetResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -AutoscaleProfiles $profile1 -Notifications $notification1
```

Další informace o správě nastavení automatického škálování najdete v tématu [Get-AutoscaleSetting](/previous-versions/azure/mt282461(v=azure.100)).

## <a name="autoscale-history"></a>Historie automatického škálování
Následující příklad ukazuje, jak můžete zobrazit nedávné události automatického škálování a výstrahy. Pomocí prohledávání protokolu aktivit můžete zobrazit historii automatického škálování.

```powershell
Get-AzLog -Caller "Microsoft.Insights/autoscaleSettings" -DetailedOutput -StartTime 2015-03-01
```

`Get-AzAutoScaleHistory`K načtení historie automatického škálování můžete použít rutinu.

```powershell
Get-AzAutoScaleHistory -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/microsoft.insights/autoscalesettings/myScaleSetting -StartTime 2016-03-15 -DetailedOutput
```

Další informace najdete v tématu [Get-AutoscaleHistory](/previous-versions/azure/mt282464(v=azure.100)).

### <a name="view-details-for-an-autoscale-setting"></a>Zobrazit podrobnosti nastavení automatického škálování
Pomocí `Get-Autoscalesetting` rutiny můžete získat další informace o nastavení automatického škálování.

Následující příklad ukazuje podrobnosti o všech nastaveních automatického škálování ve skupině prostředků ' myrg1 '.

```powershell
Get-AzAutoscalesetting -ResourceGroup myrg1 -DetailedOutput
```

Následující příklad zobrazuje podrobnosti o všech nastaveních automatického škálování ve skupině prostředků ' myrg1 ' a konkrétně nastavení automatického škálování s názvem ' MyScaleVMSSSetting '.

```powershell
Get-AzAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting -DetailedOutput
```

### <a name="remove-an-autoscale-setting"></a>Odebrání nastavení automatického škálování
Pomocí `Remove-Autoscalesetting` rutiny můžete odstranit nastavení automatického škálování.

```powershell
Remove-AzAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting
```

## <a name="manage-log-profiles-for-activity-log"></a>Správa profilů protokolů pro protokol aktivit
Můžete vytvořit *Profil protokolu* a exportovat data z protokolu aktivit do účtu úložiště a můžete pro něj nakonfigurovat uchovávání dat. V případě potřeby můžete data streamovat také do centra událostí. Tato funkce je aktuálně ve verzi Preview a můžete vytvořit pouze jeden profil protokolu na jedno předplatné. K vytváření a správě profilů protokolů můžete použít následující rutiny s vaším aktuálním předplatným. Můžete také zvolit konkrétní předplatné. I když je PowerShell výchozí pro aktuální předplatné, můžete ho vždycky změnit pomocí `Set-AzContext` . Protokol aktivit můžete nakonfigurovat tak, aby směroval data do libovolného účtu úložiště nebo centra událostí v rámci daného předplatného. Data jsou zapsána jako soubory objektů BLOB ve formátu JSON.

### <a name="get-a-log-profile"></a>Získat profil protokolu
K načtení stávajících profilů protokolů použijte `Get-AzLogProfile` rutinu.

### <a name="add-a-log-profile-without-data-retention"></a>Přidání profilu protokolu bez uchovávání dat
```powershell
Add-AzLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Location global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia
```

### <a name="remove-a-log-profile"></a>Odebrání profilu protokolu
```powershell
Remove-AzLogProfile -name my_log_profile_s1
```

### <a name="add-a-log-profile-with-data-retention"></a>Přidání profilu protokolu s uchováváním dat
Můžete zadat vlastnost **-RetentionInDays** s počtem dnů jako kladné celé číslo, kde se data uchovávají.

```powershell
Add-AzLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Location global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

### <a name="add-log-profile-with-retention-and-eventhub"></a>Přidat profil protokolu s uchováním a EventHub
Kromě směrování dat do účtu úložiště ho můžete streamovat také do centra událostí. V této verzi Preview je konfigurace účtu úložiště povinná, ale konfigurace centra událostí je volitelná.

```powershell
Add-AzLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Location global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

## <a name="configure-diagnostics-logs"></a>Konfigurace diagnostických protokolů
Řada služeb Azure poskytuje další protokoly a telemetrii, které mohou provádět jednu nebo více následujících akcí: 
 - je nakonfigurované tak, aby ukládala data v účtu Azure Storage.
 - odesláno do Event Hubs
 - odesílá se do Log Analytics pracovního prostoru. 

Operaci lze provést pouze na úrovni prostředků. Účet úložiště nebo centrum událostí by se měly nacházet ve stejné oblasti jako cílový prostředek, ve kterém je nastavení diagnostiky nakonfigurované.

### <a name="get-diagnostic-setting"></a>Získat nastavení diagnostiky
```powershell
Get-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp
```

Zakázat nastavení diagnostiky

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $false
```

Povolit nastavení diagnostiky bez uchování

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true
```

Povolit nastavení diagnostiky s uchováním

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

Povolit nastavení diagnostiky s uchováním pro určitou kategorii protokolu

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/sakteststorage -Categories NetworkSecurityGroupEvent -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

Povolit nastavení diagnostiky pro Event Hubs

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Enable $true
```

Povolit nastavení diagnostiky pro Log Analytics

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -WorkspaceId /subscriptions/s1/resourceGroups/insights-integration/providers/providers/microsoft.operationalinsights/workspaces/myWorkspace -Enabled $true

```

Všimněte si, že vlastnost ID pracovního prostoru přijímá *ID prostředku* pracovního prostoru. ID prostředku pracovního prostoru Log Analytics můžete získat pomocí následujícího příkazu:

```powershell
(Get-AzOperationalInsightsWorkspace).ResourceId

```

Tyto příkazy lze kombinovat pro posílání dat do více cílů.
