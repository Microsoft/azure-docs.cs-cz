---
title: Azure Service Fabric – sledování výkonu pomocí Log Analytics | Dokumentace Microsoftu
description: Zjistěte, jak nastavit agenta Log Analytics pro monitorování kontejnerů a čítače výkonu pro své clustery Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/16/2018
ms.author: srrengar
ms.openlocfilehash: cc051e1ba0bbea68bb1e888168883351bf4f730f
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/18/2018
ms.locfileid: "49406207"
---
# <a name="performance-monitoring-with-log-analytics"></a>Sledování výkonu pomocí Log Analytics

Tento článek popisuje kroky pro přidání agenta Log Analytics jako virtuálního počítače škálovací sady rozšíření do clusteru a připojte ho k existující pracovní prostor Azure Log Analytics. To umožňuje shromažďování diagnostická data o kontejnerech, aplikací a monitorování výkonu. Tak, že přidáte jako rozšíření k prostředku virtuálního počítače škálovací sady, Azure Resource Manageru se zajistí, že se nainstaluje na všech uzlech i při škálování clusteru.

> [!NOTE]
> Tento článek předpokládá, že máte pracovní prostor služby Azure Log Analytics, už nastavili. Pokud tak neučiníte, přejděte na [nastavení Azure Log Analytics](service-fabric-diagnostics-oms-setup.md)

## <a name="add-the-agent-extension-via-azure-cli"></a>Přidat rozšíření agenta pomocí příkazového řádku Azure

Nejlepší způsob, jak přidat agenta Log Analytics ke svému clusteru je prostřednictvím škálování virtuálního počítače nastavte API pomocí Azure CLI. Pokud nemáte ještě nastavení rozhraní příkazového řádku Azure, jděte na webu Azure portal a otevřete [Cloud Shell](../cloud-shell/overview.md) instanci, nebo [instalace rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

1. Jakmile vaší služby Cloud Shell je požadováno, ujistěte se, že pracujete ve stejném předplatném jako prostředek. Zkontrolujte to s `az account show` a ujistěte se, že hodnota "name" odpovídá předplatné vašeho clusteru.

2. Na portálu přejděte do skupiny prostředků, ve kterém se nachází váš pracovní prostor Log Analytics. Klikněte na prostředek Log Analytics (typ prostředku bude Log Analytics). Jakmile budete na stránce Přehled zdrojů, klikněte na **Upřesnit nastavení** v nabídce vlevo v části nastavení.

    ![Stránka Vlastnosti log Analytics](media/service-fabric-diagnostics-oms-agent/oms-advanced-settings.png)
 
3. Klikněte na **servery Windows** Pokud jsou připraveni cluster Windows, a **servery s Linuxem** při vytváření clusteru s Linuxem. Tato stránka zobrazí vaše `workspace ID` a `workspace key` (uvedené jako primárního klíče na portálu). Budete potřebovat i na další krok.

4. Spusťte příkaz k instalaci agenta Log Analytics do clusteru, pomocí `vmss extension set` rozhraní API ve službě Cloud Shell:

    Pro Windows cluster:
    
    ```sh
    az vmss extension set --name MicrosoftMonitoringAgent --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<Log AnalyticsworkspaceId>'}" --protected-settings "{'workspaceKey':'<Log AnalyticsworkspaceKey>'}"
    ```

    Pro cluster s Linuxem:

    ```sh
    az vmss extension set --name OmsAgentForLinux --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<Log AnalyticsworkspaceId>'}" --protected-settings "{'workspaceKey':'<Log AnalyticsworkspaceKey>'}"
    ```

    Tady je příklad agenta Log Analytics přidávají do clusteru Windows.

    ![Protokolovat příkaz rozhraní příkazového řádku pro agenta Analytics](media/service-fabric-diagnostics-oms-agent/cli-command.png)
 
5. To by měla trvat méně než 15 minut úspěšně přidat agenta na svých uzlech. Můžete ověřit, že byly přidány pomocí agentů `az vmss extension list` rozhraní API:

    ```sh
    az vmss extension list --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType>
    ```

## <a name="add-the-agent-via-the-resource-manager-template"></a>Přidat agenta pomocí šablony Resource Manageru

Je k dispozici pro ukázková Resource Manageru šablony, které nasazení pracovnímu prostoru Azure Log Analytics a přidání agenta na jednotlivé uzly [Windows](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-OMS-UnSecure) nebo [Linux](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Linux).

Můžete stáhnout a upravit této šablony můžete nasadit do clusteru, který nejlépe vyhovuje vašim potřebám.

## <a name="view-performance-counters"></a>Zobrazit čítače výkonu

Teď, když jste přidali agenta Log Analytics, přejděte na přes portál Log Analytics k výběru, které čítače výkonu je chcete shromáždit. 

1. Na webu Azure Portal přejděte do skupiny prostředků, ve které jste vytvořili řešení analýza služby Service Fabric. Vyberte **ServiceFabric\<nameOfLog AnalyticsWorkspace\>**.

2. Klikněte na **Log Analytics**.

3. Klikněte na tlačítko **upřesňující nastavení**.

4. Klikněte na tlačítko **Data**, pak klikněte na tlačítko **čítačů výkonu systému Linux nebo Windows**. Zde je seznam Výchozí čítače, které můžete povolit a interval pro shromažďování lze nastavit příliš. Můžete také přidat [další čítače výkonu](service-fabric-diagnostics-event-generation-perf.md) ke shromažďování. Správný formát je odkazováno v tomto [článku](https://msdn.microsoft.com/library/windows/desktop/aa373193(v=vs.85).aspx).

5. Klikněte na tlačítko **Uložit**, pak klikněte na tlačítko **OK**.

6. Zavřete okno Upřesnit nastavení.

7. V části Obecné, klikněte na tlačítko **shrnutí pracovního prostoru**.

8. Zobrazí se dlaždice ve formě grafu pro každou z řešení povolené, včetně pro Service Fabric. Klikněte na tlačítko **Service Fabric** grafu a pokračujte v Service Fabric analytického řešení.

9. Zobrazí se na několik dlaždic s grafy na provozní kanál a události reliable services. Grafické znázornění dat odesílaných v čítačů, které jste vybrali se zobrazí pod uzlem metriky. 

10. Klikněte na graf kontejneru metriky zobrazíte další podrobnosti. Můžete také zadávat dotazy na data čítače výkonu podobně k události clusteru a filtrováním podle uzlů, název čítače výkonu a hodnoty v jazyce dotaz Kusto.

![Dotazu čítače výkonu log Analytics](media/service-fabric-diagnostics-event-analysis-oms/oms_node_metrics_table.PNG)

## <a name="next-steps"></a>Další postup

* Shromažďovat relevantní [čítače výkonu](service-fabric-diagnostics-event-generation-perf.md). Konfigurace agenta Log Analytics ke shromažďování konkrétních čítačích výkonu najdete v tématu [konfigurace zdroje dat](../log-analytics/log-analytics-data-sources.md#configuring-data-sources).
* Nakonfigurujte Log Analytics a nastavit [automatické upozorňování](../log-analytics/log-analytics-alerts.md) které vám pomůže se zjišťováním a diagnostikou
* Jako alternativu můžete shromáždit čítače výkonu prostřednictvím [rozšíření Azure Diagnostics a posílat do Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md#add-the-ai-sink-to-the-resource-manager-template)
