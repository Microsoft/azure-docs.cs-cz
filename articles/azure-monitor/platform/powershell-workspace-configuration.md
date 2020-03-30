---
title: Vytvoření & konfigurace analýzy protokolů pomocí PowerShellu
description: Pracovní prostory Log Analytics ve službě Azure Monitor ukládají data ze serverů v místní nebo cloudové infrastruktuře. Počítačová data můžete shromažďovat z úložiště Azure, když je generuje diagnostika Azure.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/19/2019
ms.openlocfilehash: 2584cedceab1386cbab9c72bb4b510eebe2122bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80054695"
---
# <a name="manage-log-analytics-workspace-in-azure-monitor-using-powershell"></a>Správa pracovního prostoru Log Analytics v Azure Monitoru pomocí PowerShellu

[Rutiny Prostředí PowerShell log Analytics](https://docs.microsoft.com/powershell/module/az.operationalinsights/) můžete použít k provádění různých funkcí v pracovním prostoru Log Analytics v Azure Monitoru z příkazového řádku nebo jako součást skriptu.  Příklady úloh, které můžete provádět s Prostředím PowerShell:

* Vytvoření pracovního prostoru
* Přidání nebo odebrání řešení
* Import a export uložených hledání
* Vytvoření skupiny počítačů
* Povolení shromažďování protokolů služby IIS z počítačů s nainstalovaným agentem systému Windows
* Sběr čítačů výkonu z počítačů S Linuxem a Windows
* Shromažďování událostí ze syslogu na počítačích s Linuxem
* Shromažďování událostí z protokolů událostí systému Windows
* Sbírat protokoly vlastních událostí
* Přidání agenta analýzy protokolů do virtuálního počítače Azure
* Konfigurace analýzy protokolů pro indexování dat shromážděných pomocí diagnostiky Azure

Tento článek obsahuje dvě ukázky kódu, které ilustrují některé funkce, které můžete provádět z prostředí PowerShell.  Můžete odkazovat na [odkaz rutiny rutiny Prostředí PowerShell log Analytics](https://docs.microsoft.com/powershell/module/az.operationalinsights/) pro další funkce.

> [!NOTE]
> Log Analytics byl dříve nazýván Statistiky provozu, což je důvod, proč je název používaný v rutinách.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky
Tyto příklady pracují s verzí 1.0.0 nebo novějším modulu Az.OperationalInsights.


## <a name="create-and-configure-a-log-analytics-workspace"></a>Vytvoření a konfigurace pracovního prostoru Analýzy protokolů
Následující ukázka skriptu ukazuje, jak:

1. Vytvoření pracovního prostoru
2. Seznam dostupných řešení
3. Přidání řešení do pracovního prostoru
4. Import uložených hledání
5. Exportu uložených hledání
6. Vytvoření skupiny počítačů
7. Povolení shromažďování protokolů služby IIS z počítačů s nainstalovaným agentem systému Windows
8. Sbírat čítače perf logického disku z počítačů se systémem Linux (% použité inody; Megabajty zdarma; % využitémísto; Přenosy disků/s; Čtení disku/s; Zápisy na disk/s)
9. Shromažďování událostí syslogu z počítačů s Linuxem
10. Shromažďování událostí chyb a upozornění z protokolu událostí aplikace z počítačů se systémem Windows
11. Čítač výkonu Shromáždit dostupné paměti Mbytes z počítačů se systémem Windows
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
New-AzOperationalInsightsLinuxPerformanceObjectDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -ObjectName "Logical Disk" -InstanceName "*"  -CounterNames @("% Used Inodes", "Free Megabytes", "% Used Space", "Disk Transfers/sec", "Disk Reads/sec", "Disk Writes/sec") -IntervalSeconds 20  -Name "Example Linux Disk Performance Counters"
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

> [!NOTE]
> Formát parametru **CustomLogRawJson,** který definuje konfiguraci vlastního protokolu, může být složitý. Pomocí [get-azOperationalInsightsDataSource](https://docs.microsoft.com/powershell/module/az.operationalinsights/get-azoperationalinsightsdatasource?view=azps-3.2.0) načíst konfiguraci pro existující vlastní protokol. Vlastnost **Vlastnostje** konfigurace požadovaná pro parametr **CustomLogRawJson.**

Ve výše uvedeném příkladu regexDelimiter byl definován jako "n"\\pro newline. Oddělovač protokolu může být také časové razítko.  Jedná se o podporované formáty:

| Formát | Formát Json RegEx \\ používá dva pro každý \ ve standardním RegEx, takže pokud testování v aplikaci RegEx snížit \\ na \ | | |
| --- | --- | --- | --- |
| `YYYY-MM-DD HH:MM:SS` | `((\\d{2})|(\\d{4}))-([0-1]\\d)-(([0-3]\\d)|(\\d))\\s((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9]` | | |
| `M/D/YYYY HH:MM:SS AM/PM` | `(([0-1]\\d)|[0-9])/(([0-3]\\d)|(\\d))/((\\d{2})|(\\d{4}))\\s((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9]\\s(AM|PM|am|pm)` | | |
| `dd/MMM/yyyy HH:MM:SS` | `(([0-2][1-9]|[3][0-1])\\/(Jan|Feb|Mar|May|Apr|Jul|Jun|Aug|Oct|Sep|Nov|Dec|jan|feb|mar|may|apr|jul|jun|aug|oct|sep|nov|dec)\\/((19|20)[0-9][0-9]))\\s((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9])` |
| `MMM dd yyyy HH:MM:SS` | `(((?:Jan(?:uary)?|Feb(?:ruary)?|Mar(?:ch)?|Apr(?:il)?|May|Jun(?:e)?|Jul(?:y)?|Aug(?:ust)?|Sep(?:tember)?|Sept|Oct(?:ober)?|Nov(?:ember)?|Dec(?:ember)?)).*?((?:(?:[0-2]?\\d{1})|(?:[3][01]{1})))(?![\\d]).*?((?:(?:[1]{1}\\d{1}\\d{1}\\d{1})|(?:[2]{1}\\d{3})))(?![\\d]).*?((?:(?:[0-1][0-9])|(?:[2][0-3])|(?:[0-9])):(?:[0-5][0-9])(?::[0-5][0-9])?(?:\\s?(?:am|AM|pm|PM))?))` | | |
| `yyMMdd HH:mm:ss` | `([0-9]{2}([0][1-9]|[1][0-2])([0-2][0-9]|[3][0-1])\\s\\s?([0-1]?[0-9]|[2][0-3]):[0-5][0-9]:[0-5][0-9])` | | |
| `ddMMyy HH:mm:ss` | `(([0-2][0-9]|[3][0-1])([0][1-9]|[1][0-2])[0-9]{2}\\s\\s?([0-1]?[0-9]|[2][0-3]):[0-5][0-9]:[0-5][0-9])` | | |
| `MMM d HH:mm:ss` | `(Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)\\s\\s?([0]?[1-9]|[1-2][0-9]|[3][0-1])\\s([0-1]?[0-9]|[2][0-3]):([0-5][0-9]):([0-5][0-9])` | | |
| `MMM  d HH:mm:ss` <br> dvě mezery po MMM | `(Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)\\s\\s([0]?[1-9]|[1-2][0-9]|[3][0-1])\\s([0][0-9]|[1][0-2]):([0-5][0-9]):([0-5][0-9])` | | |
| `MMM d HH:mm:ss` | `(Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)\\s([0]?[1-9]|[1-2][0-9]|[3][0-1])\\s([0][0-9]|[1][0-2]):([0-5][0-9]):([0-5][0-9])` | | |
| `dd/MMM/yyyy:HH:mm:ss +zzzz` <br> kde + je + nebo - <br> kde zzzz čas posun | `(([0-2][1-9]|[3][0-1])\\/(Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)\\/((19|20)[0-9][0-9]):([0][0-9]|[1][0-2]):([0-5][0-9]):([0-5][0-9])\\s[\\+|\\-][0-9]{4})` | | |
| `yyyy-MM-ddTHH:mm:ss` <br> T je doslovné písmeno T | `((\\d{2})|(\\d{4}))-([0-1]\\d)-(([0-3]\\d)|(\\d))T((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9]` | | |

## <a name="configuring-log-analytics-to-send-azure-diagnostics"></a>Konfigurace analýzy protokolů pro odesílání diagnostiky Azure
Pro monitorování prostředků Azure bez agentů musí mít prostředky azure diagnostiku povolenou a nakonfigurovanou pro zápis do pracovního prostoru Log Analytics. Tento přístup odesílá data přímo do pracovního prostoru a nevyžaduje, aby byla data zapsána do účtu úložiště. Mezi podporované zdroje patří:

| Typ prostředku | Protokoly | Metriky |
| --- | --- | --- |
| Brány Application Gateway    | Ano | Ano |
| Účty Automation     | Ano | |
| Účty Batch          | Ano | Ano |
| Analýza datového jezera     | Ano | |
| Úložiště datových jezer         | Ano | |
| Elastický fond SQL        |     | Ano |
| Obor názvů centra událostí     |     | Ano |
| IoT Hubs                |     | Ano |
| Key Vault               | Ano | |
| Nástroje pro vyrovnávání zatížení          | Ano | |
| Logic Apps              | Ano | Ano |
| Network Security Groups (Skupiny zabezpečení sítě) | Ano | |
| Azure Cache for Redis             |     | Ano |
| Služby hledání         | Ano | Ano |
| Obor názvů služby Service Bus   |     | Ano |
| SQL (v12)               |     | Ano |
| Webové stránky               |     | Ano |
| Farmy webového serveru        |     | Ano |

Podrobnosti o dostupných metrikách najdete v [části podporované metriky s Azure Monitorem](../../azure-monitor/platform/metrics-supported.md).

Podrobnosti o dostupných protokolech naleznete v [části Podporované služby a schéma protokolů prostředků](../../azure-monitor/platform/diagnostic-logs-schema.md).

```powershell
$workspaceId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$resourceId = "/SUBSCRIPTIONS/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/DEMO"

Set-AzDiagnosticSetting -ResourceId $resourceId -WorkspaceId $workspaceId -Enabled $true
```

Pomocí předchozí rutiny můžete také shromažďovat protokoly z prostředků, které jsou v různých předplatných. Rutina je schopna pracovat napříč předplatnými, protože poskytujete ID protokolů vytvářejících prostředky i pracovního prostoru, do které jsou protokoly odesílány.


## <a name="configuring-log-analytics-workspace-to-collect-azure-diagnostics-from-storage"></a>Konfigurace pracovního prostoru Log Analytics pro shromažďování diagnostiky Azure z úložiště
Chcete-li shromažďovat data protokolu z v rámci spuštěné instance klasické cloudové služby nebo clusteru prostředků infrastruktury služeb, musíte nejprve zapsat data do úložiště Azure. Pracovní prostor Analýzy protokolů je pak nakonfigurován tak, aby shromažďoval protokoly z účtu úložiště. Mezi podporované zdroje patří:

* Klasické cloudové služby (webové a pracovní role)
* Clustery prostředků infrastruktury služeb

Následující příklad ukazuje, jak:

1. Seznam existujících účtů úložiště a umístění, ze kterých bude pracovní prostor indexovat data
2. Vytvoření konfigurace pro čtení z účtu úložiště
3. Aktualizace nově vytvořené konfigurace na indexovaná data z dalších umístění
4. Odstranit nově vytvořenou konfiguraci

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

Předchozí skript můžete také použít ke shromažďování protokolů z účtů úložiště v různých předplatných. Skript je schopen pracovat napříč předplatnými, protože poskytujete ID prostředku účtu úložiště a odpovídající přístupový klíč. Když změníte přístupový klíč, budete muset aktualizovat přehled úložiště, abyste měli nový klíč.


## <a name="next-steps"></a>Další kroky
* [Zkontrolujte rutiny prostředí PowerShell log Analytics, kde](https://docs.microsoft.com/powershell/module/az.operationalinsights/) nazíredalší informace o použití prostředí PowerShell pro konfiguraci analýzy protokolů.

