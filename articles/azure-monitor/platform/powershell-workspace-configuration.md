---
title: Použití Powershellu k vytvoření a konfigurace pracovního prostoru Log Analytics | Dokumentace Microsoftu
description: Protokolovat Analytics používá data ze serverů ve vaší místní nebo cloudovou infrastrukturu. Shromažďovat počítačových dat z Azure storage generování diagnostiky Azure.
services: log-analytics
documentationcenter: ''
author: richrundmsft
manager: jochan
editor: ''
ms.assetid: 3b9b7ade-3374-4596-afb1-51b695f481c2
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: powershell
ms.topic: conceptual
ms.date: 11/21/2016
ms.author: richrund
ms.openlocfilehash: 088d8155fda6c370d89cded516bfa6c174c9380a
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/15/2018
ms.locfileid: "53438027"
---
# <a name="manage-log-analytics-using-powershell"></a>Správa služby Log Analytics pomocí PowerShellu
Můžete použít [rutiny Powershellu Log Analytics](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/) k provádění různých funkcí ve službě Log Analytics z příkazového řádku nebo v rámci skriptu.  Příklady úloh, které můžete provést pomocí prostředí PowerShell:

* Vytvoření pracovního prostoru
* Přidat nebo odebrat řešení
* Import a export uložených hledání
* Vytvořit skupinu počítačů
* Povolit shromažďování protokolů služby IIS z počítačů s nainstalovaným agentem Windows
* Shromáždit čítače výkonu z počítačů se systémy Linux a Windows
* Shromažďování událostí z protokolu syslog pro počítače s Linuxem 
* Shromažďovat události z protokolů událostí Windows
* Shromažďovat vlastní protokoly událostí
* Přidat agenta log analytics na virtuálním počítači Azure
* Nakonfigurujte log analytics a index data shromážděná pomocí diagnostiky Azure

Tento článek obsahuje dva příklady, které znázorňují některé z funkcí, které můžete provádět z prostředí PowerShell.  Můžete se podívat do [Reference k rutinám Powershellu Log Analytics](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/) pro jiné funkce.

> [!NOTE]
> Log Analytics se dříve nazývala Operational Insights, což je důvod, proč je název používaný v rutinách.
> 
> 

## <a name="prerequisites"></a>Požadavky
Tyto příklady práce s verzí 2.3.0 nebo později z modulu AzureRm.OperationalInsights.


## <a name="create-and-configure-a-log-analytics-workspace"></a>Vytvoření a konfigurace pracovního prostoru Log Analytics
Následující ukázkový skript ukazuje, jak:

1. Vytvoření pracovního prostoru
2. Seznam dostupných řešení
3. Přidání řešení do pracovního prostoru
4. Importovat uložené hledání
5. Export uložit hledání
6. Vytvořit skupinu počítačů
7. Povolit shromažďování protokolů služby IIS z počítačů s nainstalovaným agentem Windows
8. Shromáždit čítače výkonu logický Disk z počítačů s Linuxem (% volných uzlů Inode; Volné megabajty; % Využitého místa; Přenosy disku/s; Čtení disku/s; Zápis disku/s)
9. Shromažďovat události procesu syslog z počítačů s Linuxem
10. Shromáždit události chyby a upozornění z protokolu událostí aplikace z počítačů s Windows
11. Shromažďovat čítač výkonu paměť v MB k dispozici z počítačů s Windows
12. Shromažďovat vlastní protokol 

```

$ResourceGroup = "oms-example"
$WorkspaceName = "log-analytics-" + (Get-Random -Maximum 99999) # workspace names need to be unique - Get-Random helps with this for the example code
$Location = "westeurope"

# List of solutions to enable
$Solutions = "Security", "Updates", "SQLAssessment"

# Saved Searches to import
$ExportedSearches = @"
[
    {
        "Category":  "My Saved Searches",
        "DisplayName":  "WAD Events (All)",
        "Query":  "Type=Event SourceSystem:AzureStorage ",
        "Version":  1
    },
    {        
        "Category":  "My Saved Searches",
        "DisplayName":  "Current Disk Queue Length",
        "Query":  "Type=Perf ObjectName=LogicalDisk InstanceName=\"C:\" CounterName=\"Current Disk Queue Length\"",
        "Version":  1
    }
]
"@ | ConvertFrom-Json

# Custom Log to collect
$CustomLog = @"
{
    "customLogName": "sampleCustomLog1", 
    "description": "Example custom log datasource", 
    "inputs": [
        { 
            "location": { 
            "fileSystemLocations": { 
                "windowsFileTypeLogPaths": [ "e:\\iis5\\*.log" ], 
                "linuxFileTypeLogPaths": [ "/var/logs" ] 
                } 
            }, 
        "recordDelimiter": { 
            "regexDelimiter": { 
                "pattern": "\\n", 
                "matchIndex": 0, 
                "matchIndexSpecified": true, 
                "numberedGroup": null 
                } 
            } 
        }
    ], 
    "extractions": [
        { 
            "extractionName": "TimeGenerated", 
            "extractionType": "DateTime", 
            "extractionProperties": { 
                "dateTimeExtraction": { 
                    "regex": null, 
                    "joinStringRegex": null 
                    } 
                } 
            }
        ] 
    }
"@

# Create the resource group if needed
try {
    Get-AzureRmResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzureRmResourceGroup -Name $ResourceGroup -Location $Location
}

# Create the workspace
New-AzureRmOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup

# List all solutions and their installation status
Get-AzureRmOperationalInsightsIntelligencePacks -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Add solutions
foreach ($solution in $Solutions) {
    Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -IntelligencePackName $solution -Enabled $true
}

# List enabled solutions
(Get-AzureRmOperationalInsightsIntelligencePacks -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName).Where({($_.enabled -eq $true)})

# Import Saved Searches
foreach ($search in $ExportedSearches) {
    $id = $search.Category + "|" + $search.DisplayName
    New-AzureRmOperationalInsightsSavedSearch -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId $id -DisplayName $search.DisplayName -Category $search.Category -Query $search.Query -Version $search.Version
}

# Export Saved Searches
(Get-AzureRmOperationalInsightsSavedSearch -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName).Value.Properties | ConvertTo-Json 

# Create Computer Group based on a query
New-AzureRmOperationalInsightsComputerGroup -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId "My Web Servers" -DisplayName "Web Servers" -Category "My Saved Searches" -Query "Computer=""web*"" | distinct Computer" -Version 1

# Create a computer group based on names (up to 5000)
$computerGroup = """servername1.contoso.com"",""servername2.contoso.com"",""servername3.contoso.com"",""servername4.contoso.com"""
New-AzureRmOperationalInsightsComputerGroup -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId "My Named Servers" -DisplayName "Named Servers" -Category "My Saved Searches" -Query $computerGroup -Version 1

# Enable IIS Log Collection using agent
Enable-AzureRmOperationalInsightsIISLogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Linux Perf
New-AzureRmOperationalInsightsLinuxPerformanceObjectDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -ObjectName "Logical Disk" -InstanceName "*"  -CounterNames @("% Used Inodes", "Free Megabytes", "% Used Space", "Disk Transfers/sec", "Disk Reads/sec", "Disk Reads/sec", "Disk Writes/sec") -IntervalSeconds 20  -Name "Example Linux Disk Performance Counters"
Enable-AzureRmOperationalInsightsLinuxCustomLogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Linux Syslog
New-AzureRmOperationalInsightsLinuxSyslogDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -Facility "kern" -CollectEmergency -CollectAlert -CollectCritical -CollectError -CollectWarning -Name "Example kernal syslog collection"
Enable-AzureRmOperationalInsightsLinuxSyslogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Windows Event
New-AzureRmOperationalInsightsWindowsEventDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -EventLogName "Application" -CollectErrors -CollectWarnings -Name "Example Application Event Log"

# Windows Perf
New-AzureRmOperationalInsightsWindowsPerformanceCounterDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -ObjectName "Memory" -InstanceName "*" -CounterName "Available MBytes" -IntervalSeconds 20 -Name "Example Windows Performance Counter"

# Custom Logs
New-AzureRmOperationalInsightsCustomLogDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -CustomLogRawJson "$CustomLog" -Name "Example Custom Log Collection"

```

## <a name="configuring-log-analytics-to-index-azure-diagnostics"></a>Konfigurace Log Analytics k indexování diagnostiky Azure
Prostředky pro monitorování bez agentů prostředků Azure, musí mít diagnostiky Azure povolené a nakonfigurované pro zápis do pracovního prostoru Log Analytics. Tento přístup přímo k Log Analytics odesílá data a nevyžaduje, aby data k zápisu do účtu úložiště. Podporované prostředky zahrnují:

| Typ prostředku | Logs | Metriky |
| --- | --- | --- |
| Brány Application Gateway    | Ano | Ano |
| Účty Automation     | Ano | |
| Účty Batch          | Ano | Ano |
| Data Lake analytics     | Ano | | 
| Data Lake store         | Ano | |
| Fond elastické SQL        |     | Ano |
| Obor názvů centra událostí     |     | Ano |
| IoT Huby                |     | Ano |
| Key Vault               | Ano | |
| Nástroje pro vyrovnávání zatížení          | Ano | |
| Logic Apps              | Ano | Ano |
| Network Security Groups (Skupiny zabezpečení sítě) | Ano | |
| Azure Cache for Redis             |     | Ano |
| Služby hledání         | Ano | Ano |
| Obor názvů služby Service Bus   |     | Ano |
| SQL (v12)               |     | Ano |
| Weby               |     | Ano |
| Farmy webových serverů        |     | Ano |

Podrobnosti o dostupných metrik, najdete v tématu [podporované metriky ve službě Azure Monitor](../../azure-monitor/platform/metrics-supported.md).

Podrobnosti o dostupných protokolů, najdete v tématu [podporované služby a schéma pro diagnostické protokoly](../../azure-monitor/platform/tutorial-dashboards.md).

```
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$resourceId = "/SUBSCRIPTIONS/ec11ca60-1234-491e-5678-0ea07feae25c/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/DEMO" 

Set-AzureRmDiagnosticSetting -ResourceId $resourceId -WorkspaceId $workspaceId -Enabled $true
```

Můžete také použít rutinu předchozí shromažďování protokolů z prostředků, které jsou v různých předplatných. Rutina je moct pracovat napříč předplatnými, protože tím id prostředku vytváření protokoly a protokoly jsou odeslána do pracovního prostoru.


## <a name="configuring-log-analytics-to-index-azure-diagnostics-from-storage"></a>Konfigurace Log Analytics k indexování diagnostiky Azure ze služby storage
Shromažďovat data protokolu z v rámci běžící instance cloudovou službou modelu classic nebo clusteru service fabric, budete muset nejprve zapisovat data do služby Azure storage. Log Analytics se pak nakonfiguruje k shromažďovat protokoly z účtu úložiště. Podporované prostředky zahrnují:

* Klasické cloudové služby (webové a pracovní role)
* Clustery Service fabric

Následující příklad ukazuje postup:

1. Seznam existujících účtů úložiště a umístění, která Log Analytics bude indexování dat z
2. Vytvořit konfiguraci, kterou chcete číst z účtu úložiště
3. Aktualizace nově vytvořeného konfigurace na index data z dalších umístěních
4. Odstranit nově vytvořený konfiguraci

```
# validTables = "WADWindowsEventLogsTable", "LinuxsyslogVer2v0", "WADServiceFabric*EventTable", "WADETWEventTable" 
$workspace = (Get-AzureRmOperationalInsightsWorkspace).Where({$_.Name -eq "your workspace name"})

# Update these two lines with the storage account resource ID and the storage account key for the storage account you want to Log Analytics to  
$storageId = "/subscriptions/ec11ca60-1234-491e-5678-0ea07feae25c/resourceGroups/demo/providers/Microsoft.Storage/storageAccounts/wadv2storage"
$key = "abcd=="

# List existing insights
Get-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name

# Create a new insight
New-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" -StorageAccountResourceId $storageId -StorageAccountKey $key -Tables @("WADWindowsEventLogsTable") -Containers @("wad-iis-logfiles")

# Update existing insight
Set-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" -Tables @("WADWindowsEventLogsTable", "WADETWEventTable") -Containers @("wad-iis-logfiles")

# Remove the insight
Remove-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" 

```

Můžete také použít předchozí skript shromažďování protokolů z účtů úložiště v různých předplatných. Skript je moct pracovat napříč předplatnými, protože tím id prostředku účtu úložiště a odpovídající přístupový klíč. Při změně přístupového klíče, je potřeba aktualizovat úložiště přehled o nový klíč.


## <a name="next-steps"></a>Další postup
* [Zkontrolujte rutiny Powershellu Log Analytics](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/) Další informace o použití prostředí PowerShell pro konfiguraci služby Log Analytics.

