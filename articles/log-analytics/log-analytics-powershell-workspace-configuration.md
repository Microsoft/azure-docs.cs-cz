---
title: Pomocí prostředí PowerShell vytvořit a nakonfigurovat pracovní prostor Log Analytics | Microsoft Docs
description: Protokolovat Analytics používá data ze serverů v místní nebo cloudové infrastruktury. Můžete shromáždit data počítače z úložiště Azure generování Azure diagnostics.
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
ms.component: na
ms.openlocfilehash: 375ae9a82af4a1f8a86b529b597ed479388e66dc
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37129332"
---
# <a name="manage-log-analytics-using-powershell"></a>Správa služby Log Analytics pomocí PowerShellu
Můžete použít [rutiny prostředí PowerShell Log Analytics](https://msdn.microsoft.com/library/mt188224\(v=azure.300\).aspx) k provádění různých funkcí v analýzy protokolů z příkazového řádku nebo v rámci skriptu.  Příklady úlohy, které můžete provést pomocí prostředí PowerShell:

* Vytvoření pracovního prostoru
* Přidat nebo odebrat řešení
* Import a export uložených hledání
* Vytvořit skupinu počítačů
* Povolit shromažďování protokolů služby IIS z počítačů s nainstalovaným agentem systému Windows
* Shromáždit čítače výkonu z počítačů se systémy Linux a Windows
* Shromažďování událostí z syslog počítačů se systémem Linux 
* Shromažďování událostí z protokolů událostí systému Windows
* Shromažďovat vlastní protokoly událostí
* Přidat agenta analýzy protokolů pro virtuální počítač Azure
* Konfigurace analýzy protokolů pro data indexu shromažďována pomocí Azure diagnostics

Tento článek obsahuje dvě ukázky kódu, které ilustrovat některé z funkcí, které lze provádět z prostředí PowerShell.  Můžete se podívat do [odkazu na rutiny Powershellu Log Analytics](https://msdn.microsoft.com/library/mt188224\(v=azure.300\).aspx) pro další funkce.

> [!NOTE]
> Analýzy protokolů volala dřív Operational Insights, proto je název používaný v rutiny.
> 
> 

## <a name="prerequisites"></a>Požadavky
Tyto příklady fungovat s verzí 2.3.0 nebo později AzureRm.OperationalInsights modulu.


## <a name="create-and-configure-a-log-analytics-workspace"></a>Vytvořit a nakonfigurovat pracovní prostor Log Analytics
Znázorňuje následující ukázka skriptu postup:

1. Vytvoření pracovního prostoru
2. Seznam dostupných řešení
3. Přidat řešení do pracovního prostoru
4. Importovat uložené hledání
5. Export uložené hledání
6. Vytvořit skupinu počítačů
7. Povolit shromažďování protokolů služby IIS z počítačů s nainstalovaným agentem systému Windows
8. Z počítače se systémem Linux shromáždit čítače výkonu logický Disk (% použitých uzlů; Volné megabajty; % Využitého místa; Přenosy disku/s; Čtení disku/s; Zápis disku/s)
9. Shromažďovat události procesu syslog z počítače se systémem Linux
10. Shromažďování událostí chyb a upozornění z protokolu událostí aplikace z počítače se systémem Windows
11. Shromažďovat čítač výkonu paměť v MB k dispozici z počítače se systémem Windows
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

## <a name="configuring-log-analytics-to-index-azure-diagnostics"></a>Konfigurace analýzy protokolů do indexu Azure diagnostics
Pro monitorování bez agentů prostředků Azure, prostředky musí být Azure diagnostics povolené a nakonfigurované k zápisu do pracovního prostoru analýzy protokolů. Tento přístup přímo k Log Analytics odesílá data a nevyžaduje data k zápisu do účtu úložiště. Podporované prostředky zahrnují:

| Typ prostředku | Logs | Metriky |
| --- | --- | --- |
| Brány Application Gateway    | Ano | Ano |
| Účty Automation     | Ano | |
| Účty Batch          | Ano | Ano |
| Data Lake analytics     | Ano | | 
| Úložiště data Lake store         | Ano | |
| Fond elastické SQL        |     | Ano |
| Obor názvů centra událostí     |     | Ano |
| IoT Huby                |     | Ano |
| Key Vault               | Ano | |
| Služby vyrovnávání zatížení          | Ano | |
| Logic Apps              | Ano | Ano |
| Network Security Groups (Skupiny zabezpečení sítě) | Ano | |
| Redis Cache             |     | Ano |
| Služby hledání         | Ano | Ano |
| Obor názvů služby Service Bus   |     | Ano |
| SQL (v12)               |     | Ano |
| Weby               |     | Ano |
| Webové serverové farmy        |     | Ano |

Podrobnosti k dispozici metrik [podporované metriky s Azure monitorování](../monitoring-and-diagnostics/monitoring-supported-metrics.md).

Podrobnosti o dostupných protokolů, najdete v části [podporované služby a schématu pro diagnostické protokoly](../monitoring-and-diagnostics/monitoring-diagnostic-logs-schema.md).

```
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$resourceId = "/SUBSCRIPTIONS/ec11ca60-1234-491e-5678-0ea07feae25c/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/DEMO" 

Set-AzureRmDiagnosticSetting -ResourceId $resourceId -WorkspaceId $workspaceId -Enabled $true
```

Můžete také použít rutinu předchozí ke shromažďování protokolů z prostředků, které se nacházejí v různých předplatných. Rutina je možné pracovat ve předplatných vzhledem k tomu, že zadáte id prostředku vytváření protokoly a protokoly jsou odeslána do pracovního prostoru.


## <a name="configuring-log-analytics-to-index-azure-diagnostics-from-storage"></a>Konfigurace analýzy protokolů do indexu Azure diagnostics z úložiště
Shromažďování dat protokolu z v rámci spuštěnou instanci classic cloudové služby nebo service fabric cluster, musíte nejprve zapíše data do úložiště Azure. Analýzy protokolů je nakonfigurovaný pro shromažďování protokolů z účtu úložiště. Podporované prostředky zahrnují:

* Classic cloudových služeb (webové a pracovní role)
* Clustery služby infrastruktury

Následující příklad ukazuje postup:

1. Seznam existující účty úložiště a umístění, které bude analýzy protokolů indexu dat z
2. Vytvořit konfiguraci, kterou chcete číst z účtu úložiště
3. Aktualizace se nově vytvořená konfigurace data indexu z další umístění
4. Odstranit nově vytvořenou konfiguraci

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

Můžete taky uvedený skript ke shromažďování protokolů z účty úložiště v různých předplatných. Skript je možné pracovat ve předplatných vzhledem k tomu, že zadáváte id prostředků účtu úložiště a odpovídající přístupový klíč. Když změníte přístupový klíč, je potřeba aktualizovat náhled úložiště tak, aby měl nový klíč.


## <a name="next-steps"></a>Další postup
* [Zkontrolujte rutiny prostředí PowerShell Log Analytics](https://msdn.microsoft.com/library/mt188224\(v=azure.300\).aspx) Další informace o použití prostředí PowerShell pro konfiguraci analýzy protokolů.

