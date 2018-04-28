---
title: Shromažďovat protokoly služby Azure a metriky pro analýzy protokolů | Microsoft Docs
description: Konfigurace diagnostiky na prostředky pro zápis protokoly a metriky k analýze protokolů Azure.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: 84105740-3697-4109-bc59-2452c1131bfe
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: magoedte
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a748cb0e2a08ed5e8ada5db171d5ef12b2fe121e
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="collect-azure-service-logs-and-metrics-for-use-in-log-analytics"></a>Shromažďovat protokoly služby Azure a metriky pro použití v analýzy protokolů

Existují čtyři různé způsoby shromažďování protokolů a metriky pro služby Azure:

1. Azure diagnostics přímo k Log Analytics (*diagnostiky* v následující tabulce)
2. Azure diagnostics do úložiště Azure k analýze protokolů (*úložiště* v následující tabulce)
3. Konektory pro služby Azure (*konektory* v následující tabulce)
4. Skripty ke sběru a následně je publikovat data do analýzy protokolů (prázdné buňky v následující tabulce a pro služby, které nejsou uvedené)


| Služba                 | Typ prostředku                           | Logs        | Metriky     | Řešení |
| --- | --- | --- | --- | --- |
| Application Gateway    | Microsoft.Network/applicationGateways   | Diagnostika | Diagnostika | [Analýza brány Azure aplikace](log-analytics-azure-networking-analytics.md#azure-application-gateway-analytics-solution-in-log-analytics) |
| Application Insights    |                                         | Konektor   | Konektor   | [Konektor služby Statistika aplikace](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/) (Preview) |
| Účty Automation     | Microsoft.Automation/AutomationAccounts | Diagnostika |             | [Další informace](../automation/automation-manage-send-joblogs-log-analytics.md)|
| Účty Batch          | Microsoft.Batch/batchAccounts           | Diagnostika | Diagnostika | |
| Classic cloudové služby  |                                         | Úložiště     |             | [Další informace](log-analytics-azure-storage-iis-table.md) |
| Kognitivní služby      | Microsoft.CognitiveServices/accounts    |             | Diagnostika | |
| Data Lake analytics     | Microsoft.DataLakeAnalytics/accounts    | Diagnostika |             | |
| Úložiště data Lake store         | Microsoft.DataLakeStore/accounts        | Diagnostika |             | |
| Obor názvů centra událostí     | Microsoft.EventHub/namespaces           | Diagnostika | Diagnostika | |
| IoT Huby                | Microsoft.Devices/IotHubs               |             | Diagnostika | |
| Key Vault               | Microsoft.KeyVault/vaults               | Diagnostika |             | [KeyVault Analytics](log-analytics-azure-key-vault.md) |
| Služby vyrovnávání zatížení          | Microsoft.Network/loadBalancers         | Diagnostika |             |  |
| Logic Apps              | Microsoft.Logic/workflows <br> Microsoft.Logic/integrationAccounts | Diagnostika | Diagnostika | |
| Network Security Groups (Skupiny zabezpečení sítě) | Microsoft.Network/networksecuritygroups | Diagnostika |             | [Skupina zabezpečení sítě Azure Analytics](log-analytics-azure-networking-analytics.md#azure-network-security-group-analytics-solution-in-log-analytics) |
| Trezory zotavení         | Microsoft.RecoveryServices/vaults       |             |             | [Azure Recovery Services Analytics (Preview)](https://github.com/krnese/AzureDeploy/blob/master/OMS/MSOMS/Solutions/recoveryservices/)|
| Služby hledání         | Microsoft.Search/searchServices         | Diagnostika | Diagnostika | |
| Obor názvů služby Service Bus   | Microsoft.ServiceBus/namespaces         | Diagnostika | Diagnostika | [Service Bus Analytics (Preview)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-servicebus-solution)|
| Service Fabric          |                                         | Úložiště     |             | [Služba Fabric Analytics (Preview)](log-analytics-service-fabric.md) |
| SQL (v12)               | Microsoft.Sql/servers/databases <br> Microsoft.Sql/servers/elasticPools |             | Diagnostika | [Analýza Azure SQL (Preview)](log-analytics-azure-sql.md) |
| Úložiště                 |                                         |             | Skript      | [Azure Storage Analytics (Preview)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-azure-storage-analytics-solution) |
| Virtuální počítače        | Microsoft.Compute/virtualMachines       | Linka   | Linka <br> Diagnostika  | |
| Sady škálování virtuálních počítačů | Microsoft.Compute/virtualMachines <br> Microsoft.Compute/virtualMachineScaleSets/virtualMachines |             | Diagnostika | |
| Webové serverové farmy        | Microsoft.Web/serverfarms               |             | Diagnostika | |
| Weby               | Microsoft.Web/sites <br> Microsoft.Web/sites/slots |             | Diagnostika | [Službě Azure Web Apps Analytics (Preview)](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureWebAppsAnalyticsOMS?tab=Overview) |


> [!NOTE]
> U monitorování na virtuálních počítačích Azure (Linux a Windows), doporučujeme nainstalovat [rozšíření virtuálního počítače Log Analytics](log-analytics-azure-vm-extension.md). Agent poskytuje přehled shromážděných z virtuálních počítačů. Můžete taky rozšíření pro sady škálování virtuálního počítače.
>
>

## <a name="azure-diagnostics-direct-to-log-analytics"></a>Azure diagnostics přímo k Log Analytics
Mnoho prostředků Azure jsou schopni zapisovat diagnostické protokoly a metriky přímo na analýzy protokolů a toto je upřednostňovaný způsob shromažďování dat pro analýzu. Pokud používáte Azure diagnostics, data se zapisují okamžitě k analýze protokolů a není nutné nejprve zapsat data do úložiště.

Prostředky Azure, které podporují [Azure monitorování](../monitoring-and-diagnostics/monitoring-overview.md) může poslat jejich protokoly a metriky přímo k Log Analytics.

> [!NOTE]
> Odesílání vícerozměrných metriky k analýze protokolů prostřednictvím nastavení diagnostiky se aktuálně nepodporuje. Metriky s dimenzemi se exportují jako ploché jednodimenzionální metriky agregované napříč hodnotami dimenzí.
>
> *Příklad:* Metriku Příchozí zprávy v centru událostí je možné zkoumat a převést na graf na úrovni jednotlivých front. Ale při exportu prostřednictvím nastavení pro diagnostiku metrika je reprezentována jako všechny příchozí zprávy napříč všemi fronty události rozbočovače.
>
>

* Podrobnosti k dispozici metrik [podporované metriky s Azure monitorování](../monitoring-and-diagnostics/monitoring-supported-metrics.md).
* Podrobnosti o dostupných protokolů, najdete v části [podporované služby a schématu pro diagnostické protokoly](../monitoring-and-diagnostics/monitoring-diagnostic-logs-schema.md).

### <a name="enable-diagnostics-with-powershell"></a>Povolení diagnostiky pomocí PowerShellu
Potřebujete listopadu 2016 (v2.3.0) nebo novější vydání [prostředí Azure PowerShell](/powershell/azure/overview).

Následující příklad PowerShell ukazuje, jak používat [Set-AzureRmDiagnosticSetting](/powershell/module/azurerm.insights/set-azurermdiagnosticsetting) povolí se Diagnostika na skupinu zabezpečení sítě. Ve stejný přístup se dá použít pro všechny podporované prostředky – nastavit `$resourceId` k prostředku, které chcete povolit diagnostiku pro id prostředku.

```powershell
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$resourceId = "/SUBSCRIPTIONS/ec11ca60-1234-491e-5678-0ea07feae25c/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/DEMO"

Set-AzureRmDiagnosticSetting -ResourceId $ResourceId  -WorkspaceId $workspaceId -Enabled $true
```

### <a name="enable-diagnostics-with-resource-manager-templates"></a>Povolte diagnostiku pomocí šablony Resource Manageru

K povolení diagnostiky pro prostředek, kdy je vytvořen a Diagnostika odeslali do pracovního prostoru analýzy protokolů můžete použít podobná té následující šablonu. V tomto příkladu je pro účet Automation, ale funguje pro všechny typy podporovaných prostředků.

```json
        {
            "type": "Microsoft.Automation/automationAccounts/providers/diagnosticSettings",
            "name": "[concat(parameters('omsAutomationAccountName'), '/', 'Microsoft.Insights/service')]",
            "apiVersion": "2015-07-01",
            "dependsOn": [
                "[concat('Microsoft.Automation/automationAccounts/', parameters('omsAutomationAccountName'))]",
                "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspaceName'))]"
            ],
            "properties": {
                "workspaceId": "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('omsWorkspaceName'))]",
                "logs": [
                    {
                        "category": "JobLogs",
                        "enabled": true
                    },
                    {
                        "category": "JobStreams",
                        "enabled": true
                    }
                ]
            }
        }
```

[!INCLUDE [log-analytics-troubleshoot-azure-diagnostics](../../includes/log-analytics-troubleshoot-azure-diagnostics.md)]

## <a name="azure-diagnostics-to-storage-then-to-log-analytics"></a>Azure diagnostics do úložiště a k analýze protokolů

Pro shromažďování protokolů z v rámci některé prostředky, je možné odeslat protokoly do úložiště Azure a pak nakonfigurujte analýzy protokolů číst protokoly z úložiště.

Analýzy protokolů můžete použít tento přístup ke shromažďování diagnostiky z úložiště Azure pro protokoly a následující prostředky:

| Prostředek | Logs |
| --- | --- |
| Service Fabric |ETWEvent <br> Provozních událostí <br> Událost spolehlivé objektu Actor <br> Spolehlivé služby událostí |
| Virtuální počítače |Linux Syslog <br> Události systému Windows <br> Protokol IIS <br> Windows ETWEvent |
| Webové role <br> Role pracovního procesu |Linux Syslog <br> Události systému Windows <br> Protokol IIS <br> Windows ETWEvent |

> [!NOTE]
> Budou se účtovat normální Azure datové sazby za úložiště a transakce při odeslání diagnostiky do účtu úložiště a při analýze protokolů čte data z vašeho účtu úložiště.
>
>

V tématu [pomocí úložiště objektů blob pro službu IIS a tabulka úložiště pro události](log-analytics-azure-storage-iis-table.md) Další informace o jak analýzy protokolů shromáždit tyto protokoly.

## <a name="connectors-for-azure-services"></a>Konektory pro služby Azure

Je konektor pro službu Application Insights, což umožňuje data shromážděná pomocí Application Insights k odeslání do analýzy protokolů.

Další informace o [Application Insights konektor](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/).

## <a name="scripts-to-collect-and-post-data-to-log-analytics"></a>Skripty ke shromažďování a odesílání dat k analýze protokolů

Pro služby Azure, které neposkytuje přímý způsob, jak odeslat protokoly a metriky k analýze protokolů můžete použít skript Azure Automation ke shromažďování protokolů a metriky. Skript může pak posílat data pomocí analýzy protokolů [kolekcí dat rozhraní API](log-analytics-data-collector-api.md)

Galerie šablony Azure má [příklady použití Azure Automation](https://azure.microsoft.com/resources/templates/?term=OMS) shromažďovat data ze služby a odesláním k analýze protokolů.

## <a name="next-steps"></a>Další postup

* [Používání úložiště blob pro službu IIS a tabulka úložiště pro události](log-analytics-azure-storage-iis-table.md) číst protokoly pro tuto diagnostiku zápisu do table storage nebo protokoly služby IIS zapisovat do úložiště objektů blob služby Azure.
* [Povolit řešení](log-analytics-add-solutions.md) zajistit přehled o data.
* [Použijte vyhledávací dotazy](log-analytics-log-searches.md) analyzovat data.
