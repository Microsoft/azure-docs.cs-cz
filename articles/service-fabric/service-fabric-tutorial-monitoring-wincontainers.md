---
title: Monitorování a diagnostika kontejnerů Windows na platformě Service Fabric v Azure | Microsoft Docs
description: V tomto kurzu nakonfigurujete Log Analytics na monitorování a diagnostiku kontejnerů Windows na platformě Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/08/2018
ms.author: dekapur
ms.custom: mvc
ms.openlocfilehash: dfce4adbe986c018ab14e843ee3e8a256a10993f
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/18/2018
ms.locfileid: "49407040"
---
# <a name="tutorial-monitor-windows-containers-on-service-fabric-using-log-analytics"></a>Kurz: Monitorování kontejnerů Windows na platformě Service Fabric pomocí Log Analytics

Toto je třetí část kurzu, která vás provede nastavením Log Analytics pro monitorování kontejnerů Windows orchestrovaných na platformě Service Fabric.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Konfigurace Log Analytics pro cluster Service Fabric
> * Použití pracovního prostoru Log Analytics k zobrazení a dotazování protokolů z kontejnerů a uzlů
> * Konfigurace agenta Log Analytics ke sbírání metrik kontejnerů a uzlů

## <a name="prerequisites"></a>Požadavky

Než začnete s tímto kurzem, musíte mít splněné následující požadavky:

* Máte cluster v Azure. Případně ho můžete [vytvořit pomocí tohoto kurzu](service-fabric-tutorial-create-vnet-and-windows-cluster.md).
* [Nasadili jste do něj kontejnerizovanou aplikaci](service-fabric-host-app-in-a-container.md).

## <a name="setting-up-log-analytics-with-your-cluster-in-the-resource-manager-template"></a>Nastavení Log Analytics pro cluster v šabloně Resource Manageru

V případě, že jste použili [šablonu poskytnutou](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-OMS-UnSecure) v první části tohoto kurzu, měla by v obecné šabloně Azure Resource Manageru pro Service Fabric už zahrnovat následující položky. V případě, že máte vlastní cluster, který chcete nastavit pro monitorování kontejnerů pomocí Log Analytics:

* Proveďte následující změny šablony Resource Manageru.
* Nasaďte ji pomocí PowerShellu a upgradujte tak svůj cluster prostřednictvím [nasazení šablony](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm). Azure Resource Manager rozpozná, že prostředky existují, takže ji zavede jako upgrade.

### <a name="adding-log-analytics-to-your-cluster-template"></a>Přidání Log Analytics do šablony clusteru

Proveďte následující změny v souboru *template.json*.

1. Do části *parameters* (parametry) přidejte umístění a název pracovního prostoru Log Analytics:

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

3. Přidejte agenta Microsoft Monitoring Agent jako rozšíření virtuálního počítače. Vyhledejte prostředek škálovací sady virtuálních počítačů: *resources* > *"apiVersion": "[variables('vmssApiVersion')]"*. V části *properties* > *virtualMachineProfile* > *extensionProfile* > *extensions* přidejte následující popis rozšíření za rozšíření *ServiceFabricNode*: 
    
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

4. Přidejte pracovní prostor Log Analytics jako samostatný prostředek. V části *resources* (prostředky) za prostředek škálovacích sad virtuálních počítačů přidejte následující:

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

[Tady](https://github.com/ChackDan/Service-Fabric/blob/master/ARM%20Templates/Tutorial/azuredeploy.json) je ukázková šablona (použitá v první části tohoto kurzu) obsahující všechny tyto změny, kterou podle potřeby můžete použít jako vodítko. Těmito změnami se přidá pracovní prostor Log Analytics do vaší skupiny prostředků. Pracovní prostor se nakonfiguruje tak, aby sbíral události platformy Service Fabric z tabulek úložiště nakonfigurovaných pomocí agenta [Azure Diagnostics pro Windows](service-fabric-diagnostics-event-aggregation-wad.md). Do každého uzlu v clusteru se také přidal agent Log Analytics (Microsoft Monitoring Agent) jako rozšíření virtuálního počítače – to znamená, že při škálování clusteru se agent automaticky nakonfiguruje na každém počítači a připojí se ke stejnému pracovnímu prostoru.

Nasazením šablony s provedenými změnami upgradujte svůj aktuální cluster. Po dokončení by se ve vaší skupině prostředků měly zobrazit prostředky Log Analytics. Až bude cluster připravený, nasaďte do něj svou kontejnerizovanou aplikaci. V dalším kroku nastavíme monitorování kontejnerů.

## <a name="add-the-container-monitoring-solution-to-your-log-analytics-workspace"></a>Přidání řešení pro monitorování kontejnerů do pracovního prostoru Log Analytics

Pokud chcete ve svém pracovním prostoru nastavit řešení kontejnerů, vyhledejte *řešení pro monitorování kontejnerů* a vytvořte prostředek kontejnerů (v kategorii Monitorování a správa).

![Přidání řešení kontejnerů](./media/service-fabric-tutorial-monitoring-wincontainers/containers-solution.png)

Po zobrazení výzvy k zadání *pracovního prostoru Log Analytics* vyberte pracovní prostor, který se vytvořil ve vaší skupině prostředků, a klikněte na **Vytvořit**. Tím se do vašeho pracovního prostoru přidá *řešení pro monitorování kontejnerů* a agent Log Analytics nasazený šablonou začne automaticky shromažďovat protokoly a statistiky Dockeru. 

Vraťte se do své *skupiny prostředků*, kde by se teď mělo zobrazit nově přidané řešení pro monitorování. Když na něj kliknete, na cílové stránce by se měl zobrazit počet spuštěných imagí kontejnerů.

*Všimněte si, že mám spuštěných 5 instancí kontejneru fabrikam z [druhé části](service-fabric-host-app-in-a-container.md) kurzu.*

![Cílová stránka řešení kontejnerů](./media/service-fabric-tutorial-monitoring-wincontainers/solution-landing.png)

Kliknutím na **Řešení pro monitorování kontejnerů** přejdete na podrobnější řídicí panel, kde můžete procházet několika panely a také spouštět dotazy v Log Analytics.

*Poznámka: Od září 2017 prochází řešení určitými aktualizacemi – protože pracujeme na integraci více orchestrátorů do stejného řešení, ignorujte případné chyby týkající se událostí Kubernetes.*

Vzhledem k tomu, že agent sbírá protokoly Dockeru, ve výchozím nastavení zobrazí výstupy *stdout* a *stderr*. Když se posunete doprava, zobrazí se inventář imagí kontejnerů, jejich stav, metriky a ukázkové dotazy, jejichž spuštěním můžete získat užitečnější data.

![Řídicí panel řešení kontejnerů](./media/service-fabric-tutorial-monitoring-wincontainers/container-metrics.png)

Kliknutím na jakýkoli z těchto panelů přejdete k dotazu Log Analytics, který generuje zobrazenou hodnotu. Změňte dotaz na *\**, aby se zobrazily všechny různé druhy shromažďovaných protokolů. Tady můžete dotazovat nebo filtrovat výkon kontejnerů, protokoly nebo zobrazit události platformy Service Fabric. Vaši agenti také neustále vysílají z každého uzlu prezenční signál, jehož kontrolou se můžete ujistit, že se stále shromažďují data ze všech počítačů, pokud se konfigurace clusteru změní.

![Dotaz na kontejner](./media/service-fabric-tutorial-monitoring-wincontainers/query-sample.png)

## <a name="configure-log-analytics-agent-to-pick-up-performance-counters"></a>Konfigurace agenta Log Analytics pro sbírání čítačů výkonu

Další výhodou používání agenta Log Analytics je možnost změnit čítače výkonu, které chcete sbírat, přes uživatelské rozhraní Log Analytics – nemusíte tak pokaždé konfigurovat agenta diagnostiky Azure ani provádět upgrade na základě šablony Resource Manageru. Provedete to tak, že kliknete na **Pracovní prostor OMS** na cílové stránce vašeho řešení pro monitorování kontejnerů (nebo Service Fabric).

Tím přejdete do svého pracovního prostoru Log Analytics, kde můžete zobrazit svá řešení, vytvářet vlastní řídicí panely a také konfigurovat agenta Log Analytics. 
* Klikněte na **Upřesnit nastavení** a otevřete tak nabídku Upřesnit nastavení.
* Klikněte na **Připojené zdroje** > **Servery Windows** a ověřte, že máte *připojených 5 počítačů s Windows*.
* Klikněte na **Data** > **Čítače výkonu Windows**, abyste mohli vyhledat a přidat nové čítače výkonu. Tady se zobrazí seznam doporučení Log Analytics týkajících se čítačů výkonů, jejichž údaje můžete shromažďovat, a také možnost vyhledat jiné čítače. Ověřte, že se shromažďují informace z čítačů **Procesor(_Celkem)\%Čas procesoru** a **Paměť(*)\Dostupné MB**.

Za několik minut **aktualizujte** řešení pro monitorování kontejnerů. Měla by se vám začít zobrazovat příchozí data o *výkonu počítačů*. Ta vám pomůžou porozumět využití vašich prostředků. Tyto metriky můžete využít také k přijímání patřičných rozhodnutí o škálování clusteru nebo k potvrzení, jestli cluster vyrovnává zatížení podle očekávání.

*Poznámka: Abyste mohli využívat tyto metriky, ujistěte se, že máte správně nastavené filtry času.*

![Čítače výkonu 2](./media/service-fabric-tutorial-monitoring-wincontainers/perf-counters2.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Konfigurace Log Analytics pro cluster Service Fabric
> * Použití pracovního prostoru Log Analytics k zobrazení a dotazování protokolů z kontejnerů a uzlů
> * Konfigurace agenta Log Analytics ke sbírání metrik kontejnerů a uzlů

Teď, když jste nastavili monitorování své kontejnerizované aplikace, vyzkoušejte následující:

* Podle podobných kroků jako výše nastavte Log Analytics pro cluster s Linuxem. Při provádění změn šablony Resource Manageru můžete jako vodítko použít [tuto šablonu](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Linux).
* Nakonfigurujte Log Analytics a nastavte [automatické upozorňování](../log-analytics/log-analytics-alerts.md), které vám pomůže se zjišťováním a diagnostikou.
* Prozkoumejte seznam [doporučených čítačů výkonů](service-fabric-diagnostics-event-generation-perf.md) Service Fabric, které můžete nakonfigurovat pro svůj cluster.
* Seznamte se s funkcemi [prohledávání protokolů a dotazování](../log-analytics/log-analytics-log-searches.md) nabízenými jako součást Log Analytics.