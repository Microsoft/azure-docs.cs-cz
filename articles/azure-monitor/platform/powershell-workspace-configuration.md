---
title: Použití PowerShellu k vytvoření a konfiguraci pracovního prostoru Log Analytics | Microsoft Docs
description: Log Analytics pracovní prostory v Azure Monitor ukládají data ze serverů ve vaší místní nebo cloudové infrastruktuře. Při generování diagnostikou Azure můžete shromažďovat data počítače z úložiště Azure.
services: log-analytics
author: bwren
ms.service: log-analytics
ms.devlang: powershell
ms.topic: conceptual
ms.date: 05/19/2019
ms.author: bwren
ms.openlocfilehash: 16cad34290ecc518e95ec1a0ce0950722cfe0780
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2019
ms.locfileid: "71836148"
---
# <a name="manage-log-analytics-workspace-in-azure-monitor-using-powershell"></a>Správa pracovního prostoru Log Analytics v Azure Monitor pomocí prostředí PowerShell

[Rutiny Log Analytics PowerShellu](https://docs.microsoft.com/powershell/module/az.operationalinsights/) můžete použít k provádění různých funkcí v pracovním prostoru Log Analytics v Azure monitor z příkazového řádku nebo jako součást skriptu.  Mezi příklady úloh, které můžete provádět pomocí PowerShellu, patří:

* Vytvořit pracovní prostor
* Přidání nebo odebrání řešení
* Import a export uložených hledání
* Vytvoření skupiny počítačů
* Povolit shromažďování protokolů IIS z počítačů s nainstalovaným agentem Windows
* Shromažďování čítačů výkonu z počítačů se systémy Linux a Windows
* Shromažďování událostí z protokolu syslog v počítačích se systémem Linux
* Shromažďovat události z protokolů událostí systému Windows
* Shromažďování vlastních protokolů událostí
* Přidání agenta Log Analytics do virtuálního počítače Azure
* Konfigurace Log Analytics na indexovaná data shromážděná pomocí diagnostiky Azure

Tento článek poskytuje dva ukázky kódu, které ilustrují některé z funkcí, které můžete provádět z PowerShellu.  Další funkce najdete v [referenčních informacích k rutině prostředí PowerShell pro Log Analytics](https://docs.microsoft.com/powershell/module/az.operationalinsights/) .

> [!NOTE]
> Log Analytics se dřív volala Operational Insights, což znamená, proč se jedná o název, který se používá v rutinách.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky
Tyto příklady pracují s verzí 1.0.0 nebo novějším v modulu AZ. OperationalInsights.


## <a name="create-and-configure-a-log-analytics-workspace"></a>Vytvoření a konfigurace pracovního prostoru Log Analytics
Následující ukázka skriptu ukazuje, jak:

1. Vytvořit pracovní prostor
2. Seznam dostupných řešení
3. Přidání řešení do pracovního prostoru
4. Import uložených hledání
5. Export uložených hledání
6. Vytvoření skupiny počítačů
7. Povolit shromažďování protokolů IIS z počítačů s nainstalovaným agentem Windows
8. Shromáždit čítače výkonu logických disků z počítačů se systémem Linux (% využitých uzlů inode; Volné megabajty; % Využitého místa; Přenosy disku/s; Čtení z disku/s; Zápisy na disk/s)
9. Shromažďovat události syslogu z počítačů se systémem Linux
10. Shromažďování událostí chyb a upozornění z protokolu událostí aplikace z počítačů se systémem Windows
11. Shromažďovat čítač výkonu MB dostupné paměti z počítačů s Windows
12. Shromáždění vlastního protokolu

```powershell

$ResourceGroup = "oms-example"
$WorkspaceName = "log-analytics-" + (Get-Random -Maximum 99999) # workspace names need to be unique across all Azure subscriptions - Get-Random helps with this for the example code
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
        "Query":  "Perf | where ObjectName == "LogicalDisk" and CounterName == "Current Disk Queue Length" and InstanceName == "C:"",
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
    Get-AzResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzResourceGroup -Name $ResourceGroup -Location $Location
}

# Create the workspace
New-AzOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup

# List all solutions and their installation status
Get-AzOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Add solutions
foreach ($solution in $Solutions) {
    Set-AzOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -IntelligencePackName $solution -Enabled $true
}

# List enabled solutions
(Get-AzOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName).Where({($_.enabled -eq $true)})

# Import Saved Searches
foreach ($search in $ExportedSearches) {
    $id = $search.Category + "|" + $search.DisplayName
    New-AzOperationalInsightsSavedSearch -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId $id -DisplayName $search.DisplayName -Category $search.Category -Query $search.Query -Version $search.Version
}

# Export Saved Searches
(Get-AzOperationalInsightsSavedSearch -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName).Value.Properties | ConvertTo-Json

# Create Computer Group based on a query
New-AzOperationalInsightsComputerGroup -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId "My Web Servers" -DisplayName "Web Servers" -Category "My Saved Searches" -Query "Computer=""web*"" | distinct Computer" -Version 1

# Enable IIS Log Collection using agent
Enable-AzOperationalInsightsIISLogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Linux Perf
New-AzOperationalInsightsLinuxPerformanceObjectDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -ObjectName "Logical Disk" -InstanceName "*"  -CounterNames @("% Used Inodes", "Free Megabytes", "% Used Space", "Disk Transfers/sec", "Disk Reads/sec", "Disk Reads/sec", "Disk Writes/sec") -IntervalSeconds 20  -Name "Example Linux Disk Performance Counters"
Enable-AzOperationalInsightsLinuxPerformanceCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Linux Syslog
New-AzOperationalInsightsLinuxSyslogDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -Facility "kern" -CollectEmergency -CollectAlert -CollectCritical -CollectError -CollectWarning -Name "Example kernel syslog collection"
Enable-AzOperationalInsightsLinuxSyslogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Windows Event
New-AzOperationalInsightsWindowsEventDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -EventLogName "Application" -CollectErrors -CollectWarnings -Name "Example Application Event Log"

# Windows Perf
New-AzOperationalInsightsWindowsPerformanceCounterDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -ObjectName "Memory" -InstanceName "*" -CounterName "Available MBytes" -IntervalSeconds 20 -Name "Example Windows Performance Counter"

# Custom Logs

New-AzOperationalInsightsCustomLogDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -CustomLogRawJson "$CustomLog" -Name "Example Custom Log Collection"

```
Ve výše uvedeném příkladu byl regexDelimiter definován jako "\\n" pro nový řádek. Oddělovač protokolu může být také časovým razítkem.  Podporované formáty:

| Formát | Formát regulárního výrazu JSON používá ke každému regulárnímu regulárnímu výrazu dva \\, takže pokud testování v aplikaci RegEx snižuje \\ na \ | | |
| --- | --- | --- | --- |
| `YYYY-MM-DD HH:MM:SS` | `((\\d{2})|(\\d{4}))-([0-1]\\d)-(([0-3]\\d)|(\\d))\\s((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9]` | | |
| `M/D/YYYY HH:MM:SS AM/PM` | `(([0-1]\\d)|[0-9])/(([0-3]\\d)|(\\d))/((\\d{2})|(\\d{4}))\\s((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9]\\s(AM|PM|am|pm)` | | |
| `dd/MMM/yyyy HH:MM:SS` | `(([0-2][1-9]|[3][0-1])\\/(Jan|Feb|Mar|May|Apr|Jul|Jun|Aug|Oct|Sep|Nov|Dec|jan|feb|mar|may|apr|jul|jun|aug|oct|sep|nov|dec)\\/((19|20)[0-9][0-9]))\\s((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9])` |
| `MMM dd yyyy HH:MM:SS` | `(((?:Jan(?:uary)?|Feb(?:ruary)?|Mar(?:ch)?|Apr(?:il)?|May|Jun(?:e)?|Jul(?:y)?|Aug(?:ust)?|Sep(?:tember)?|Sept|Oct(?:ober)?|Nov(?:ember)?|Dec(?:ember)?)).*?((?:(?:[0-2]?\\d{1})|(?:[3][01]{1})))(?![\\d]).*?((?:(?:[1]{1}\\d{1}\\d{1}\\d{1})|(?:[2]{1}\\d{3})))(?![\\d]).*?((?:(?:[0-1][0-9])|(?:[2][0-3])|(?:[0-9])):(?:[0-5][0-9])(?::[0-5][0-9])?(?:\\s?(?:am|AM|pm|PM))?))` | | |
| `yyMMdd HH:mm:ss` | `([0-9]{2}([0][1-9]|[1][0-2])([0-2][0-9]|[3][0-1])\\s\\s?([0-1]?[0-9]|[2][0-3]):[0-5][0-9]:[0-5][0-9])` | | |
| `ddMMyy HH:mm:ss` | `(([0-2][0-9]|[3][0-1])([0][1-9]|[1][0-2])[0-9]{2}\\s\\s?([0-1]?[0-9]|[2][0-3]):[0-5][0-9]:[0-5][0-9])` | | |
| `MMM d HH:mm:ss` | `(Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)\\s\\s?([0]?[1-9]|[1-2][0-9]|[3][0-1])\\s([0-1]?[0-9]|[2][0-3]):([0-5][0-9]):([0-5][0-9])` | | |
| `MMM  d HH:mm:ss` <br> dva mezery po MMM | `(Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)\\s\\s([0]?[1-9]|[1-2][0-9]|[3][0-1])\\s([0][0-9]|[1][0-2]):([0-5][0-9]):([0-5][0-9])` | | |
| `MMM d HH:mm:ss` | `(Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)\\s([0]?[1-9]|[1-2][0-9]|[3][0-1])\\s([0][0-9]|[1][0-2]):([0-5][0-9]):([0-5][0-9])` | | |
| `dd/MMM/yyyy:HH:mm:ss +zzzz` <br> kde + je + nebo a- <br> kde ZZZZ časový posun | `(([0-2][1-9]|[3][0-1])\\/(Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)\\/((19|20)[0-9][0-9]):([0][0-9]|[1][0-2]):([0-5][0-9]):([0-5][0-9])\\s[\\+|\\-][0-9]{4})` | | |
| `yyyy-MM-ddTHH:mm:ss` <br> T je literální písmeno T. | `((\\d{2})|(\\d{4}))-([0-1]\\d)-(([0-3]\\d)|(\\d))T((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9]` | | |

## <a name="configuring-log-analytics-to-send-azure-diagnostics"></a>Konfigurace Log Analytics pro odeslání diagnostiky Azure
Pro monitorování prostředků Azure bez agentů musí mít prostředky povolenu diagnostiku Azure a nakonfigurované pro zápis do Log Analyticsho pracovního prostoru. Tento přístup odesílá data přímo do pracovního prostoru a nevyžaduje zápis dat do účtu úložiště. Mezi podporované prostředky patří:

| Typ prostředku | Protokolování | Metriky |
| --- | --- | --- |
| Aplikační brány    | Ano | Ano |
| Účty služby Automation     | Ano | |
| Účty Batch          | Ano | Ano |
| Analýzy Data Lake     | Ano | |
| Data Lake Store         | Ano | |
| Elastický fond SQL        |     | Ano |
| Obor názvů centra událostí     |     | Ano |
| Centra IoT                |     | Ano |
| Key Vault               | Ano | |
| Nástroje pro vyrovnávání zatížení          | Ano | |
| Logic Apps              | Ano | Ano |
| Skupiny zabezpečení sítě | Ano | |
| Mezipaměť Azure pro Redis             |     | Ano |
| Hledat služby         | Ano | Ano |
| Obor názvů Service Bus   |     | Ano |
| SQL (V12)               |     | Ano |
| Weby               |     | Ano |
| Webové serverové farmy        |     | Ano |

Podrobnosti o dostupných metrikách najdete v tématu [podporované metriky s Azure monitor](../../azure-monitor/platform/metrics-supported.md).

Podrobnosti o dostupných protokolech najdete v tématu [podporované služby a schéma pro diagnostické protokoly](../../azure-monitor/platform/diagnostic-logs-schema.md).

```powershell
$workspaceId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$resourceId = "/SUBSCRIPTIONS/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/DEMO"

Set-AzDiagnosticSetting -ResourceId $resourceId -WorkspaceId $workspaceId -Enabled $true
```

Pomocí předchozí rutiny můžete také shromažďovat protokoly z prostředků, které jsou v různých předplatných. Rutina může pracovat napříč předplatnými, protože poskytujete ID pro vytváření protokolů a pracovní prostor, do kterého jsou protokoly odesílány.


## <a name="configuring-log-analytics-workspace-to-collect-azure-diagnostics-from-storage"></a>Konfigurace pracovního prostoru Log Analytics ke shromáždění diagnostiky Azure z úložiště
Abyste mohli shromažďovat data protokolu z běžící instance klasické cloudové služby nebo clusteru Service Fabric, musíte nejdřív zapsat data do služby Azure Storage. Log Analytics pracovní prostor je pak nakonfigurovaný tak, aby shromáždil protokoly z účtu úložiště. Mezi podporované prostředky patří:

* Klasické cloudové služby (webové a pracovní role)
* Clustery Service Fabric

Následující příklad ukazuje, jak:

1. Zobrazí seznam existujících účtů a umístění úložiště, ze kterých bude pracovní prostor indexovat data.
2. Vytvoření konfigurace pro čtení z účtu úložiště
3. Aktualizovat nově vytvořenou konfiguraci na indexovaná data z dalších umístění
4. Odstraní nově vytvořenou konfiguraci.

```powershell
# validTables = "WADWindowsEventLogsTable", "LinuxsyslogVer2v0", "WADServiceFabric*EventTable", "WADETWEventTable"
$workspace = (Get-AzOperationalInsightsWorkspace).Where({$_.Name -eq "your workspace name"})

# Update these two lines with the storage account resource ID and the storage account key for the storage account you want the workspace to index
$storageId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/demo/providers/Microsoft.Storage/storageAccounts/wadv2storage"
$key = "abcd=="

# List existing insights
Get-AzOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name

# Create a new insight
New-AzOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" -StorageAccountResourceId $storageId -StorageAccountKey $key -Tables @("WADWindowsEventLogsTable") -Containers @("wad-iis-logfiles")

# Update existing insight
Set-AzOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" -Tables @("WADWindowsEventLogsTable", "WADETWEventTable") -Containers @("wad-iis-logfiles")

# Remove the insight
Remove-AzOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight"

```

Pomocí předchozího skriptu můžete také shromažďovat protokoly z účtů úložiště v různých předplatných. Skript může pracovat mezi předplatnými, protože poskytujete ID prostředku účtu úložiště a odpovídající přístupový klíč. Když změníte přístupovou klávesu, musíte aktualizovat Přehled úložiště, aby měl nový klíč.


## <a name="next-steps"></a>Další kroky
* Další informace o používání PowerShellu pro konfiguraci Log Analytics [najdete v Log Analytics rutinách PowerShellu](https://docs.microsoft.com/powershell/module/az.operationalinsights/) .

