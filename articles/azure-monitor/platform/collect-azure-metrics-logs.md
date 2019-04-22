---
title: Shromažďování metrik a protokolování služby Azure do pracovního prostoru Log Analytics | Dokumentace Microsoftu
description: Konfigurace diagnostiky pro prostředky Azure pro zápis protokolů a metrik do pracovního prostoru Log Analytics ve službě Azure Monitor.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: 84105740-3697-4109-bc59-2452c1131bfe
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/12/2017
ms.author: magoedte
ms.openlocfilehash: d086b6f844deb06d98edec8d8ec0f5670d84f066
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59006265"
---
# <a name="collect-azure-service-logs-and-metrics-into-log-analytics-workspace-in-azure-monitor"></a>Shromažďování metrik a protokolování služby Azure do pracovního prostoru Log Analytics ve službě Azure Monitor

Existují různé způsoby shromažďování protokolů a metrik pro služby Azure:

1. Diagnostika Azure směrovat do pracovního prostoru Log Analytics ve službě Azure Monitor (*diagnostiky* v následující tabulce)
2. Diagnostika Azure do služby Azure storage k pracovnímu prostoru Log Analytics ve službě Azure Monitor (*úložiště* v následující tabulce)
3. Konektory pro služby Azure (*konektory* v následující tabulce)
4. Skripty, které shromažďovat a následně je publikovat data do pracovního prostoru Log Analytics ve službě Azure Monitor (prázdné hodnoty v následující tabulce a pro služby, které nejsou uvedené)


| Služba                 | Typ prostředku                           | Protokoly        | Metriky     | Řešení |
| --- | --- | --- | --- | --- |
| Application Gateway    | Microsoft.Network/applicationGateways   | Diagnostika | Diagnostika | [Analýzy Azure Application Gateway](../insights/azure-networking-analytics.md#azure-application-gateway-analytics-solution-in-azure-monitor) |
| Application insights    |                                         | Spojovací čára   | Spojovací čára   | [Application Insights Connector](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/) (Preview) |
| Účty Automation     | Microsoft.Automation/AutomationAccounts | Diagnostika |             | [Další informace](../../automation/automation-manage-send-joblogs-log-analytics.md)|
| Účty Batch          | Microsoft.Batch/batchAccounts           | Diagnostika | Diagnostika | |
| Klasické cloudové služby  |                                         | Úložiště     |             | [Další informace](azure-storage-iis-table.md) |
| Kognitivní služby      | Microsoft.CognitiveServices/accounts    |             | Diagnostika | |
| Data Lake analytics     | Microsoft.DataLakeAnalytics/accounts    | Diagnostika |             | |
| Data Lake store         | Microsoft.DataLakeStore/accounts        | Diagnostika |             | |
| Obor názvů centra událostí     | Microsoft.EventHub/namespaces           | Diagnostika | Diagnostika | |
| IoT Huby                | Microsoft.Devices/IotHubs               |             | Diagnostika | |
| Key Vault               | Microsoft.KeyVault/vaults               | Diagnostika |             | [Analýza služby KeyVault](../insights/azure-key-vault.md) |
| Nástroje pro vyrovnávání zatížení          | Microsoft.Network/loadBalancers         | Diagnostika |             |  |
| Logic Apps              | Microsoft.Logic/workflows <br> Microsoft.Logic/integrationAccounts | Diagnostika | Diagnostika | |
| Network Security Groups (Skupiny zabezpečení sítě) | Microsoft.Network/networksecuritygroups | Diagnostika |             | [Analýzy skupin zabezpečení sítě Azure](../insights/azure-networking-analytics.md#azure-network-security-group-analytics-solution-in-azure-monitor) |
| Trezory služby Recovery         | Microsoft.RecoveryServices/vaults       |             |             | [Analýza (Preview) služby Azure Recovery Services](https://github.com/krnese/AzureDeploy/blob/master/OMS/MSOMS/Solutions/recoveryservices/)|
| Služby hledání         | Microsoft.Search/searchServices         | Diagnostika | Diagnostika | |
| Obor názvů služby Service Bus   | Microsoft.ServiceBus/namespaces         | Diagnostika | Diagnostika | [Služba Service Bus Analytics (Náhled)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-servicebus-solution)|
| Service Fabric          |                                         | Úložiště     |             | [Analýza služby Service Fabric (verze Preview)](../../service-fabric/service-fabric-diagnostics-oms-setup.md) |
| SQL (v12)               | Microsoft.Sql/servers/databases <br> Microsoft.Sql/servers/elasticPools |             | Diagnostika | [Azure SQL Analytics (Náhled)](../insights/azure-sql.md) |
| Úložiště                 |                                         |             | Skript      | [Analýza úložiště Azure (Preview)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-azure-storage-analytics-solution) |
| Virtuální počítače        | Microsoft.Compute/virtualMachines       | Linka   | Linka <br> Diagnostika  | |
| Škálovací sady virtuálních počítačů | Microsoft.Compute/virtualMachines <br> Microsoft.Compute/virtualMachineScaleSets/virtualMachines |             | Diagnostika | |
| Farmy webových serverů        | Microsoft.Web/serverfarms               |             | Diagnostika | |
| Weby               | Microsoft.Web/sites <br> Microsoft.Web/sites/slots |             | Diagnostika | [Azure Web Apps Analytics (Náhled)](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-web-apps-analytics) |


> [!NOTE]
> Monitorování virtuálních počítačů Azure (Linux i Windows), doporučujeme nejprve nainstalovat [rozšíření virtuálního počítače Log Analytics](../../azure-monitor/learn/quick-collect-azurevm.md). Agent poskytuje přehled shromážděných z v rámci vašich virtuálních počítačů. Můžete také použít rozšíření pro škálovací sady virtuálních počítačů.
>
>

## <a name="azure-diagnostics-direct-to-log-analytics"></a>Diagnostika Azure směrovat do služby Log Analytics
Řadu prostředků Azure se nemůže zapisovat přímo do pracovního prostoru Log Analytics ve službě Azure Monitor diagnostické protokoly a metriky a toto je upřednostňovaný způsob shromažďování dat pro analýzu. Při použití diagnostiky Azure, data se zapisují okamžitě do pracovního prostoru a není nutné nejprve zapisovat data do úložiště.

Prostředky Azure, které podporují [Azure monitor](../../azure-monitor/overview.md) posílat svoje protokoly a metriky přímo do pracovního prostoru Log Analytics.

> [!NOTE]
> Odesílání vícedimenzionálních metrik pracovní prostor Log Analytics přes nastavení diagnostiky se momentálně nepodporuje. Metriky s dimenzemi se exportují jako ploché jednodimenzionální metriky agregované napříč hodnotami dimenzí.
>
> *Například*: Metrika 'Příchozí zprávy' v Centru událostí můžete prozkoumat a převést na graf úrovni jednotlivých front. Ale při exportu přes nastavení diagnostiky, které metrika je vyjádřena jako všechny příchozí zprávy ve všech zařadí do fronty událostí centra.
>
>

* Podrobnosti o dostupných metrik, najdete v tématu [podporované metriky ve službě Azure Monitor](../../azure-monitor/platform/metrics-supported.md).
* Podrobnosti o dostupných protokolů, najdete v tématu [podporované služby a schéma pro diagnostické protokoly](../../azure-monitor/platform/diagnostic-logs-schema.md).

### <a name="enable-diagnostics-with-powershell"></a>Povolení diagnostiky pomocí PowerShellu

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Následující příklad PowerShell ukazuje, jak používat [Set-AzDiagnosticSetting](/powershell/module/Az.Monitor/Set-AzDiagnosticSetting) povolit diagnostiku na skupinu zabezpečení sítě. Stejný postup funguje u všech podporovaných zdrojů – nastavit `$resourceId` k prostředku, které chcete povolit diagnostiku pro id prostředku.

```powershell
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$resourceId = "/SUBSCRIPTIONS/ec11ca60-1234-491e-5678-0ea07feae25c/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/DEMO"

Set-AzDiagnosticSetting -ResourceId $ResourceId  -WorkspaceId $workspaceId -Enabled $true
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

[!INCLUDE [log-analytics-troubleshoot-azure-diagnostics](../../../includes/log-analytics-troubleshoot-azure-diagnostics.md)]

## <a name="azure-diagnostics-to-storage-then-to-log-analytics"></a>Diagnostika Azure storage a do služby Log Analytics

Pro shromažďování protokolů z v rámci některé prostředky, je možné odeslat protokoly do služby Azure storage a pak nakonfigurujte pracovní prostor Log Analytics pro čtení protokolů z úložiště.

Azure Monitor můžete tento postup použijte ke shromažďování diagnostických údajů ze služby Azure storage pro protokoly a následující prostředky:

| Prostředek | Protokoly |
| --- | --- |
| Service Fabric |ETWEvent <br> Provozních událostí <br> Události Reliable Actors <br> Události Reliable Service |
| Virtuální počítače |Protokolu Syslog v Linuxu <br> Události Windows <br> Protokol IIS <br> Windows ETWEvent |
| Webové role <br> Role pracovního procesu |Protokolu Syslog v Linuxu <br> Události Windows <br> Protokol IIS <br> Windows ETWEvent |

> [!NOTE]
> Azure běžné datové sazby za úložiště a transakce se účtují při odeslání diagnostiky do účtu úložiště a kdy pracovní prostor Log Analytics načítá data z vašeho účtu úložiště.
>
>

Zobrazit [úložiště objektů blob můžete použít pro služby IIS a table storage pro události](azure-storage-iis-table.md) Další informace o tom, jak Azure Monitor může shromažďovat tyto protokoly.

## <a name="connectors-for-azure-services"></a>Konektory pro služby Azure

Je konektor pro službu Application Insights, která umožňuje dat shromážděných službou Application Insights k odeslání do pracovního prostoru Log Analytics.

Další informace o [Application Insights connector](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/).

## <a name="scripts-to-collect-and-post-data-to-log-analytics-workspace"></a>Skripty ke shromažďování a odesílání dat do pracovního prostoru Log Analytics

Pro služby Azure, které neposkytují přímý způsob, jak posílat protokoly a metriky k pracovnímu prostoru Log Analytics můžete použít skriptu Azure Automation ke shromažďování protokolů a metrik. Skript potom může data odeslat na použití pracovního prostoru [rozhraní API kolekce dat](../../azure-monitor/platform/data-collector-api.md)

Galerie Azure šablony má [příklady použití Azure Automation](https://azure.microsoft.com/resources/templates/?term=OMS) pro shromažďování dat ze služeb a jejich odesílání do Azure monitoru.

## <a name="next-steps"></a>Další postup

* [Použití úložiště objektů blob pro službu IIS a table storage pro události](azure-storage-iis-table.md) číst protokoly pro diagnostiku tohoto zápisu do table storage nebo protokoly IIS zapsána do úložiště objektů blob služby Azure.
* [Povolení řešení](../../azure-monitor/insights/solutions.md) k poskytování přehledů o data.
* [Pomocí vyhledávacích dotazů](../../azure-monitor/log-query/log-query-overview.md) chcete analyzovat data.
