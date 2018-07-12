---
title: Shromažďování metrik a protokolování služby Azure Log Analytics | Dokumentace Microsoftu
description: Konfigurace diagnostiky pro prostředky Azure pro zápis protokolů a metrik do Log Analytics.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: 84105740-3697-4109-bc59-2452c1131bfe
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/12/2017
ms.author: magoedte
ms.component: na
ms.openlocfilehash: dec2abdf68b3a38706549b4b655e33bc7abca1a3
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38452667"
---
# <a name="collect-azure-service-logs-and-metrics-for-use-in-log-analytics"></a>Shromažďovat protokoly služby Azure a metriky pro použití v Log Analytics

Existují různé způsoby shromažďování protokolů a metrik pro služby Azure:

1. Diagnostika Azure směrovat do služby Log Analytics (*diagnostiky* v následující tabulce)
2. Diagnostika Azure do služby Azure storage do služby Log Analytics (*úložiště* v následující tabulce)
3. Konektory pro služby Azure (*konektory* v následující tabulce)
4. Skripty, které shromažďovat a následně je publikovat data do Log Analytics (prázdné hodnoty v následující tabulce a pro služby, které nejsou uvedené)


| Služba                 | Typ prostředku                           | Logs        | Metriky     | Řešení |
| --- | --- | --- | --- | --- |
| Application Gateway    | Microsoft.Network/applicationGateways   | Diagnostika | Diagnostika | [Analýzy Azure Application Gateway](log-analytics-azure-networking-analytics.md#azure-application-gateway-analytics-solution-in-log-analytics) |
| Application Insights    |                                         | Spojovací čára   | Spojovací čára   | [Application Insights Connector](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/) (Preview) |
| Účty Automation     | Microsoft.Automation/AutomationAccounts | Diagnostika |             | [Další informace](../automation/automation-manage-send-joblogs-log-analytics.md)|
| Účty Batch          | Microsoft.Batch/batchAccounts           | Diagnostika | Diagnostika | |
| Klasické cloudové služby  |                                         | Úložiště     |             | [Další informace](log-analytics-azure-storage-iis-table.md) |
| Kognitivní služby      | Microsoft.CognitiveServices/accounts    |             | Diagnostika | |
| Data Lake analytics     | Microsoft.DataLakeAnalytics/accounts    | Diagnostika |             | |
| Data Lake store         | Microsoft.DataLakeStore/accounts        | Diagnostika |             | |
| Obor názvů centra událostí     | Microsoft.EventHub/namespaces           | Diagnostika | Diagnostika | |
| IoT Huby                | Microsoft.Devices/IotHubs               |             | Diagnostika | |
| Key Vault               | Microsoft.KeyVault/vaults               | Diagnostika |             | [Analýza služby KeyVault](log-analytics-azure-key-vault.md) |
| Služby vyrovnávání zatížení          | Microsoft.Network/loadBalancers         | Diagnostika |             |  |
| Logic Apps              | Microsoft.Logic/workflows <br> Microsoft.Logic/integrationAccounts | Diagnostika | Diagnostika | |
| Network Security Groups (Skupiny zabezpečení sítě) | Microsoft.Network/networksecuritygroups | Diagnostika |             | [Analýzy skupin zabezpečení sítě Azure](log-analytics-azure-networking-analytics.md#azure-network-security-group-analytics-solution-in-log-analytics) |
| Trezory služby Recovery         | Microsoft.RecoveryServices/vaults       |             |             | [Analýza (Preview) služby Azure Recovery Services](https://github.com/krnese/AzureDeploy/blob/master/OMS/MSOMS/Solutions/recoveryservices/)|
| Služby hledání         | Microsoft.Search/searchServices         | Diagnostika | Diagnostika | |
| Obor názvů služby Service Bus   | Microsoft.ServiceBus/namespaces         | Diagnostika | Diagnostika | [Služba Service Bus Analytics (Náhled)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-servicebus-solution)|
| Service Fabric          |                                         | Úložiště     |             | [Analýza služby Service Fabric (verze Preview)](log-analytics-service-fabric.md) |
| SQL (v12)               | Microsoft.Sql/servers/databases <br> Microsoft.Sql/servers/elasticPools |             | Diagnostika | [Azure SQL Analytics (Náhled)](log-analytics-azure-sql.md) |
| Úložiště                 |                                         |             | Skript      | [Analýza úložiště Azure (Preview)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-azure-storage-analytics-solution) |
| Virtuální počítače        | Microsoft.Compute/virtualMachines       | Linka   | Linka <br> Diagnostika  | |
| Škálovací sady virtuálních počítačů | Microsoft.Compute/virtualMachines <br> Microsoft.Compute/virtualMachineScaleSets/virtualMachines |             | Diagnostika | |
| Farmy webových serverů        | Microsoft.Web/serverfarms               |             | Diagnostika | |
| Weby               | Microsoft.Web/sites <br> Microsoft.Web/sites/slots |             | Diagnostika | [Azure Web Apps Analytics (Náhled)](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-azure-web-apps-analytics) |


> [!NOTE]
> Monitorování virtuálních počítačů Azure (Linux i Windows), doporučujeme nejprve nainstalovat [rozšíření virtuálního počítače Log Analytics](log-analytics-azure-vm-extension.md). Agent poskytuje přehled shromážděných z v rámci vašich virtuálních počítačů. Můžete také použít rozšíření pro škálovací sady virtuálních počítačů.
>
>

## <a name="azure-diagnostics-direct-to-log-analytics"></a>Diagnostika Azure směrovat do služby Log Analytics
Řadu prostředků Azure jsou schopni zapisovat diagnostické protokoly a metriky přímo k Log Analytics, což je upřednostňovaný způsob shromažďování dat pro analýzu. Při použití diagnostiky Azure, data se zapisují okamžitě do Log Analytics a není nutné nejprve zapisovat data do úložiště.

Prostředky Azure, které podporují [Azure monitor](../monitoring-and-diagnostics/monitoring-overview.md) posílat svoje protokoly a metriky přímo ke službě Log Analytics.

> [!NOTE]
> Odesílání vícedimenzionálních metrik do Log Analytics přes nastavení diagnostiky se v současné době nepodporuje. Metriky s dimenzemi se exportují jako ploché jednodimenzionální metriky agregované napříč hodnotami dimenzí.
>
> *Příklad:* Metriku Příchozí zprávy v centru událostí je možné zkoumat a převést na graf na úrovni jednotlivých front. Ale při exportu přes nastavení diagnostiky, které metrika je vyjádřena jako všechny příchozí zprávy ve všech zařadí do fronty událostí centra.
>
>

* Podrobnosti o dostupných metrik, najdete v tématu [podporované metriky ve službě Azure Monitor](../monitoring-and-diagnostics/monitoring-supported-metrics.md).
* Podrobnosti o dostupných protokolů, najdete v tématu [podporované služby a schéma pro diagnostické protokoly](../monitoring-and-diagnostics/monitoring-diagnostic-logs-schema.md).

### <a name="enable-diagnostics-with-powershell"></a>Povolení diagnostiky pomocí PowerShellu
Potřebujete listopadu 2016 (v2.3.0) nebo novější verze [prostředí Azure PowerShell](/powershell/azure/overview).

Následující příklad PowerShell ukazuje, jak používat [Set-AzureRmDiagnosticSetting](/powershell/module/azurerm.insights/set-azurermdiagnosticsetting) povolit diagnostiku na skupinu zabezpečení sítě. Stejný postup funguje u všech podporovaných zdrojů – nastavit `$resourceId` k prostředku, které chcete povolit diagnostiku pro id prostředku.

```powershell
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$resourceId = "/SUBSCRIPTIONS/ec11ca60-1234-491e-5678-0ea07feae25c/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/DEMO"

Set-AzureRmDiagnosticSetting -ResourceId $ResourceId  -WorkspaceId $workspaceId -Enabled $true
```

### <a name="enable-diagnostics-with-resource-manager-templates"></a>Povolení diagnostiky pomocí šablon Resource Manageru

Povolit diagnostiku na prostředku, když se vytvoří a Diagnostika odeslali do pracovního prostoru Log Analytics můžete můžete použít šablonu podobně jako následující. Tento příklad je určený pro účet Automation, ale funguje pro všechny podporované typy prostředků.

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

## <a name="azure-diagnostics-to-storage-then-to-log-analytics"></a>Diagnostika Azure storage a do služby Log Analytics

Pro shromažďování protokolů z v rámci některé prostředky, je možné odeslat protokoly do služby Azure storage a potom nakonfigurujte Log Analytics pro čtení protokolů ze služby storage.

Log Analytics můžete tento postup použijte ke shromažďování diagnostických údajů ze služby Azure storage pro protokoly a následující prostředky:

| Prostředek | Logs |
| --- | --- |
| Service Fabric |ETWEvent <br> Provozních událostí <br> Události Reliable Actors <br> Události Reliable Service |
| Virtuální počítače |Protokolu Syslog v Linuxu <br> Události Windows <br> Protokol IIS <br> Windows ETWEvent |
| Webové role <br> Role pracovního procesu |Protokolu Syslog v Linuxu <br> Události Windows <br> Protokol IIS <br> Windows ETWEvent |

> [!NOTE]
> Účtují se běžné Azure datové sazby za úložiště a transakce při odeslání diagnostiky do účtu úložiště a kdy Log Analytics načítá data z vašeho účtu úložiště.
>
>

Zobrazit [úložiště objektů blob můžete použít pro služby IIS a table storage pro události](log-analytics-azure-storage-iis-table.md) Další informace o tom, jak Log Analytics může shromažďovat protokoly.

## <a name="connectors-for-azure-services"></a>Konektory pro služby Azure

Je konektor pro službu Application Insights, která umožňuje dat shromážděných službou Application Insights k odeslání do Log Analytics.

Další informace o [Application Insights connector](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/).

## <a name="scripts-to-collect-and-post-data-to-log-analytics"></a>Skripty ke shromažďování a odesílání dat do služby Log Analytics

Pro služby Azure, které neposkytují přímý způsob, jak odesílat protokoly a metriky do Log Analytics můžete použít skriptu Azure Automation ke shromažďování protokolů a metrik. Skript potom může data odeslat na pomocí Log Analytics [rozhraní API kolekce dat](log-analytics-data-collector-api.md)

Galerie Azure šablony má [příklady použití Azure Automation](https://azure.microsoft.com/resources/templates/?term=OMS) ke shromažďování dat ze služby a odesílá je do Log Analytics.

## <a name="next-steps"></a>Další postup

* [Použití úložiště objektů blob pro službu IIS a table storage pro události](log-analytics-azure-storage-iis-table.md) číst protokoly pro diagnostiku tohoto zápisu do table storage nebo protokoly IIS zapsána do úložiště objektů blob služby Azure.
* [Povolení řešení](log-analytics-add-solutions.md) k poskytování přehledů o data.
* [Pomocí vyhledávacích dotazů](log-analytics-log-searches.md) chcete analyzovat data.
