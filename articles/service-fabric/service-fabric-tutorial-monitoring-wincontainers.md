---
title: Monitorování a diagnostika kontejnerů Windows
description: V tomto kurzu nakonfigurujete protokoly Azure Monitor pro monitorování a diagnostiku kontejnerů Windows v Azure Service Fabric.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc
ms.openlocfilehash: b7689d6e259055137a8d1d3c61552790ab9f28d3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100588242"
---
# <a name="tutorial-monitor-windows-containers-on-service-fabric-using-azure-monitor-logs"></a>Kurz: monitorování kontejnerů Windows na Service Fabric pomocí protokolů Azure Monitor

Toto je třetí část kurzu a provede vás konfigurací protokolů Azure Monitor pro monitorování vašich kontejnerů Windows, které jsou Orchestrované na Service Fabric.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Konfigurace protokolů Azure Monitor pro cluster Service Fabric
> * Použití pracovního prostoru služby Log Analytics k zobrazení a dotazování protokolů z kontejnerů a uzlů
> * Konfigurace agenta Log Analytics ke sbírání metrik kontejnerů a uzlů

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Předpoklady

Než začnete s tímto kurzem, musíte mít splněné následující požadavky:

* Máte cluster v Azure. Případně ho můžete [vytvořit pomocí tohoto kurzu](service-fabric-tutorial-create-vnet-and-windows-cluster.md).
* [Nasadili jste do něj kontejnerizovanou aplikaci](service-fabric-host-app-in-a-container.md).

## <a name="setting-up-azure-monitor-logs-with-your-cluster-in-the-resource-manager-template"></a>Nastavení protokolů Azure Monitor s clusterem v šabloně Správce prostředků

V případě, že jste použili [šablonu poskytnutou](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-OMS-UnSecure) v první části tohoto kurzu, měla by v obecné šabloně Azure Resource Manageru pro Service Fabric už zahrnovat následující položky. V případě, že máte vlastní cluster, který chcete nastavit pro monitorování kontejnerů pomocí protokolů Azure Monitor:

* Proveďte následující změny šablony Resource Manageru.
* Nasaďte ji pomocí PowerShellu a upgradujte tak svůj cluster prostřednictvím [nasazení šablony](./service-fabric-cluster-creation-via-arm.md). Azure Resource Manager rozpozná, že prostředky existují, takže ji zavede jako upgrade.

### <a name="adding-azure-monitor-logs-to-your-cluster-template"></a>Přidání protokolů Azure Monitor do šablony clusteru

Proveďte následující změny v souboru *template.json*.

1. Do části *parameters* (parametry) přidejte umístění a název pracovního prostoru služby Log Analytics:

    ```json
    "omsWorkspacename": {
      "type": "string",
      "defaultValue": "[toLower(concat('sf',uniqueString(resourceGroup().id)))]",
      "metadata": {
        "description": "Name of your Log Analytics Workspace"
      }
    },
    "omsRegion": {
      "type": "string",
      "defaultValue": "East US",
      "allowedValues": [
        "West Europe",
        "East US",
        "Southeast Asia"
      ],
      "metadata": {
        "description": "Specify the Azure Region for your Log Analytics workspace"
      }
    }
    ```

    Pokud chcete některou z těchto hodnot změnit, přidejte stejné parametry do souboru *template.parameters.json* a změňte použité hodnoty tam.

2. Do části *variables* (proměnné) přidejte název řešení a řešení:

    ```json
    "omsSolutionName": "[Concat('ServiceFabric', '(', parameters('omsWorkspacename'), ')')]",
    "omsSolution": "ServiceFabric"
    ```

3. Přidejte agenta Microsoft Monitoring Agent jako rozšíření virtuálního počítače. Hledání prostředku Virtual Machine Scale Sets: *prostředky*  >  *"apiVersion": "[proměnné (' vmssApiVersion ')]"*. V části *vlastnosti*  >    >    >  *rozšíření* extensionProfile virtualMachineProfile přidejte následující popis rozšíření v rámci rozšíření *ServiceFabricNode* : 
    
    ```json
    {
        "name": "[concat(variables('vmNodeType0Name'),'OMS')]",
        "properties": {
            "publisher": "Microsoft.EnterpriseCloud.Monitoring",
            "type": "MicrosoftMonitoringAgent",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "workspaceId": "[reference(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')), '2015-11-01-preview').customerId]"
            },
            "protectedSettings": {
                "workspaceKey": "[listKeys(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')),'2015-11-01-preview').primarySharedKey]"
            }
        }
    },
    ```

4. Přidejte pracovní prostor služby Log Analytics jako samostatný prostředek. V části *resources* (prostředky) za prostředek škálovacích sad virtuálních počítačů přidejte následující:

    ```json
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[parameters('omsWorkspacename')]",
        "type": "Microsoft.OperationalInsights/workspaces",
        "properties": {
            "sku": {
                "name": "Free"
            }
        },
        "resources": [
            {
                "apiVersion": "2015-11-01-preview",
                "name": "[concat(variables('applicationDiagnosticsStorageAccountName'),parameters('omsWorkspacename'))]",
                "type": "storageinsightconfigs",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]",
                    "[concat('Microsoft.Storage/storageAccounts/', variables('applicationDiagnosticsStorageAccountName'))]"
                ],
                "properties": {
                    "containers": [ ],
                    "tables": [
                        "WADServiceFabric*EventTable",
                        "WADWindowsEventLogsTable",
                        "WADETWEventTable"
                    ],
                    "storageAccount": {
                        "id": "[resourceId('Microsoft.Storage/storageaccounts/', variables('applicationDiagnosticsStorageAccountName'))]",
                        "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('applicationDiagnosticsStorageAccountName')),'2015-06-15').key1]"
                    }
                }
            },
            {
                "apiVersion": "2015-11-01-preview",
                "name": "System",
                "type": "datasources",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
                ],
                "kind": "WindowsEvent",
                "properties": {
                    "eventLogName": "System",
                    "eventTypes": [
                        {
                            "eventType": "Error"
                        },
                        {
                            "eventType": "Warning"
                        },
                        {
                            "eventType": "Information"
                        }
                    ]
                }
            }
        ]
    },
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[variables('omsSolutionName')]",
        "type": "Microsoft.OperationsManagement/solutions",
        "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('OMSWorkspacename'))]"
        ],
        "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
        },
        "plan": {
            "name": "[variables('omsSolutionName')]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('omsSolution'))]",
            "promotionCode": ""
        }
    },
    ```

[Tady](https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/d2ffa318581fc23ac7f1b0ab2b52db1a0d7b4ba7/5-VM-Windows-OMS-UnSecure/sfclusteroms.json) je ukázková šablona (použitá v první části tohoto kurzu) obsahující všechny tyto změny, kterou podle potřeby můžete použít jako vodítko. Těmito změnami se přidá pracovní prostor služby Log Analytics do vaší skupiny prostředků. Pracovní prostor se nakonfiguruje tak, aby sbíral události platformy Service Fabric z tabulek úložiště nakonfigurovaných pomocí agenta [Azure Diagnostics pro Windows](service-fabric-diagnostics-event-aggregation-wad.md). Do každého uzlu v clusteru se také přidal agent Log Analytics (Microsoft Monitoring Agent) jako rozšíření virtuálního počítače – to znamená, že při škálování clusteru se agent automaticky nakonfiguruje na každém počítači a připojí se ke stejnému pracovnímu prostoru.

Nasazením šablony s provedenými změnami upgradujte svůj aktuální cluster. Po dokončení by se ve vaší skupině prostředků měly zobrazit prostředky Log Analytics. Až bude cluster připravený, nasaďte do něj svou kontejnerizovanou aplikaci. V dalším kroku nastavíme monitorování kontejnerů.

## <a name="add-the-container-monitoring-solution-to-your-log-analytics-workspace"></a>Přidání řešení pro monitorování kontejnerů do pracovního prostoru služby Log Analytics

Pokud chcete ve svém pracovním prostoru nastavit řešení kontejnerů, vyhledejte *řešení pro monitorování kontejnerů* a vytvořte prostředek kontejnerů (v kategorii Monitorování a správa).

![Přidání řešení kontejnerů](./media/service-fabric-tutorial-monitoring-wincontainers/containers-solution.png)

Po zobrazení výzvy k zadání *pracovního prostoru Log Analytics* vyberte pracovní prostor, který se vytvořil ve vaší skupině prostředků, a vyberte **vytvořit**. Tím do svého pracovního prostoru přidáte *řešení pro monitorování kontejnerů* a inicializujete agenta Log Analytics nasazeného šablonou a začnete shromažďovat protokoly a statistiky Docker.

Vraťte se do své *skupiny prostředků*, kde by se teď mělo zobrazit nově přidané řešení pro monitorování. Pokud ho vyberete, cílová stránka by měla zobrazit počet imagí kontejneru, které jste spustili.

*Všimněte si, že jsme v [druhé části](service-fabric-host-app-in-a-container.md) kurzu spustili pět instancí kontejneru Fabrikam.*

![Cílová stránka řešení kontejnerů](./media/service-fabric-tutorial-monitoring-wincontainers/solution-landing.png)

Výběrem **řešení pro monitorování kontejnerů** přejdete k podrobnějšímu řídicímu panelu, který vám umožní procházet více panelů a také spouštět dotazy v protokolech Azure monitor.

Vzhledem k tomu, že agent sbírá protokoly Dockeru, ve výchozím nastavení zobrazí výstupy *stdout* a *stderr*. Pokud se posunete vodorovně, uvidíte metriky inventáře, stavu, metrik a ukázkových dotazů, které můžete spustit, abyste získali užitečnější data.

![Řídicí panel řešení kontejnerů](./media/service-fabric-tutorial-monitoring-wincontainers/container-metrics.png)

Kliknutím na kterýkoli z těchto panelů přejdete do dotazu Kusto, který generuje zobrazenou hodnotu. Změňte dotaz tak, aby *\** se zobrazily všechny různé druhy protokolů, které jsou vyzvednuty. Tady můžete dotazovat nebo filtrovat výkon kontejnerů, protokoly nebo zobrazit události platformy Service Fabric. Vaši agenti také neustále vysílají z každého uzlu prezenční signál, jehož kontrolou se můžete ujistit, že se stále shromažďují data ze všech počítačů, pokud se konfigurace clusteru změní.

![Dotaz na kontejner](./media/service-fabric-tutorial-monitoring-wincontainers/query-sample.png)

## <a name="configure-log-analytics-agent-to-pick-up-performance-counters"></a>Konfigurace agenta Log Analytics pro sbírání čítačů výkonu

Další výhodou použití agenta Log Analytics je schopnost měnit čítače výkonu, které chcete prozradit prostřednictvím uživatelského rozhraní Log Analytics, a nemusíte konfigurovat agenta diagnostiky Azure a pokaždé provádět upgrade na základě Správce prostředků šablon. Pokud to chcete provést, vyberte v **pracovním prostoru OMS** na cílové stránce řešení pro monitorování kontejnerů (nebo Service Fabric).

Tím přejdete do svého pracovního prostoru služby Log Analytics, kde můžete zobrazit svá řešení, vytvářet vlastní řídicí panely a také konfigurovat agenta Log Analytics. 
* Výběrem **Možnosti Upřesnit nastavení** otevřete nabídku Upřesnit nastavení.
* Vyberte **připojené zdroje**  >  **Windows servery** , abyste ověřili, že máte *připojené 5 počítačů s Windows*.
* Vyberte   >  **čítače výkonu data systému Windows** , které chcete vyhledat, a přidejte nové čítače výkonu. Tady se zobrazí seznam doporučení z protokolů Azure Monitor pro čítače výkonu, které můžete shromažďovat, a také možnost vyhledat jiné čítače. Ověřte, že se shromažďují informace z čítačů **Procesor(_Celkem)\%Čas procesoru** a **Paměť(*)\Dostupné MB**.

Za několik minut **aktualizujte** řešení pro monitorování kontejnerů. Měla by se vám začít zobrazovat příchozí data o *výkonu počítačů*. Ta vám pomůžou porozumět využití vašich prostředků. Tyto metriky můžete využít také k přijímání patřičných rozhodnutí o škálování clusteru nebo k potvrzení, jestli cluster vyrovnává zatížení podle očekávání.

*Poznámka: Abyste mohli využívat tyto metriky, ujistěte se, že máte správně nastavené filtry času.*

![Čítače výkonu 2](./media/service-fabric-tutorial-monitoring-wincontainers/perf-counters2.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Konfigurace protokolů Azure Monitor pro cluster Service Fabric
> * Použití pracovního prostoru služby Log Analytics k zobrazení a dotazování protokolů z kontejnerů a uzlů
> * Konfigurace agenta Log Analytics ke sbírání metrik kontejnerů a uzlů

Teď, když jste nakonfigurovali monitorování pro svou kontejnerovou aplikaci, zkuste:

* Konfigurace protokolů Azure Monitor pro clustery se systémem Linux, podobně jako v tomto kurzu. Při provádění změn šablony Resource Manageru můžete jako vodítko použít [tuto šablonu](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeType-Secure-OMS).
* Nakonfigurujte protokoly Azure Monitor a nastavte [Automatické upozorňování](../azure-monitor/alerts/alerts-overview.md) , které pomáhají při zjišťování a diagnostice.
* Prozkoumejte seznam [doporučených čítačů výkonů](service-fabric-diagnostics-event-generation-perf.md) Service Fabric, které můžete nakonfigurovat pro svůj cluster.
* Seznamte se s funkcemi [prohledávání protokolů a dotazování](../azure-monitor/logs/log-query-overview.md) , které nabízí jako součást protokolů Azure monitor.
