---
title: Sledování výkonu pomocí protokolů Azure Monitor
description: Naučte se, jak nastavit agenta Log Analytics pro monitorování kontejnerů a čítačů výkonu pro clustery Azure Service Fabric.
ms.topic: conceptual
ms.date: 04/16/2018
ms.openlocfilehash: 9bb89dc2eebe584a0a9f81a6707c0a2e4fa2fc30
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105626669"
---
# <a name="performance-monitoring-with-azure-monitor-logs"></a>Sledování výkonu pomocí protokolů Azure Monitor

Tento článek popisuje postup přidání agenta Log Analytics jako rozšíření sady škálování virtuálního počítače do clusteru a jeho připojení ke stávajícímu pracovnímu prostoru Azure Log Analytics. To umožňuje shromažďovat diagnostická data o kontejnerech, aplikacích a monitorování výkonu. Když ho přidáte jako rozšíření prostředku sady škálování virtuálních počítačů, Azure Resource Manager zajistí, že se nainstalují na každý uzel, a to i při škálování clusteru.

> [!NOTE]
> V tomto článku se předpokládá, že už máte nastavený pracovní prostor Azure Log Analytics. Pokud to neuděláte, přejděte k [nastavení protokolů Azure monitor](service-fabric-diagnostics-oms-setup.md) .

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="add-the-agent-extension-via-azure-cli"></a>Přidání rozšíření agenta přes rozhraní příkazového řádku Azure

Nejlepším způsobem, jak přidat agenta Log Analytics do vašeho clusteru, je prostřednictvím rozhraní API sady škálování pro virtuální počítače, která jsou k dispozici v rozhraní příkazového řádku Azure CLI. Pokud ještě nemáte Azure CLI nastavené, přejděte na Azure Portal a otevřete [Cloud Shell](../cloud-shell/overview.md) instanci nebo [nainstalujte rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli).

1. Po vyžádání Cloud Shell se ujistěte, že pracujete ve stejném předplatném jako váš prostředek. Zkontrolujte to pomocí `az account show` a ujistěte se, že hodnota "název" odpovídá vašemu předplatnému vašeho clusteru.

2. Na portálu přejděte do skupiny prostředků, ve které se nachází váš Log Analytics pracovní prostor. Klikněte na prostředek Log Analytics (typ prostředku bude Log Analytics pracovní prostor). Až budete na stránce Přehled prostředků, klikněte na **Upřesnit nastavení** v části nastavení v nabídce vlevo.

    ![Stránka vlastností Log Analytics](media/service-fabric-diagnostics-oms-agent/oms-advanced-settings.png)

3. Pokud při vytváření clusteru se systémem Linux zadáte cluster se systémem Windows a **servery Linux** , klikněte na **servery Windows** . Na této stránce se zobrazí vaše `workspace ID` a `workspace key` (na portálu je uvedený jako primární klíč). Pro další krok budete potřebovat obě.

4. Spuštěním příkazu nainstalujte agenta Log Analytics do svého clusteru pomocí `vmss extension set` rozhraní API:

    Pro cluster Windows:

    ```azurecli
    az vmss extension set --name MicrosoftMonitoringAgent --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<Log AnalyticsworkspaceId>'}" --protected-settings "{'workspaceKey':'<Log AnalyticsworkspaceKey>'}"
    ```

    Pro cluster se systémem Linux:

    ```azurecli
    az vmss extension set --name OmsAgentForLinux --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<Log AnalyticsworkspaceId>'}" --protected-settings "{'workspaceKey':'<Log AnalyticsworkspaceKey>'}"
    ```

    Tady je příklad přidávaného agenta Log Analytics do clusteru se systémem Windows.

    ![Příkaz Log Analytics agenta CLI](media/service-fabric-diagnostics-oms-agent/cli-command.png)

5. K úspěšnému přidání agenta do vašich uzlů byste měli mít méně než 15 minut. Pomocí rozhraní API můžete ověřit, že se agenti přidali `az vmss extension list` :

    ```azurecli
    az vmss extension list --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType>
    ```

## <a name="add-the-agent-via-the-resource-manager-template"></a>Přidání agenta prostřednictvím šablony Správce prostředků

Ukázka Správce prostředků šablon, které nasazují pracovní prostor Azure Log Analytics a přidání agenta do každého uzlu, je k dispozici pro [Windows](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-OMS-UnSecure) nebo [Linux](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeType-Secure-OMS).

Tuto šablonu můžete stáhnout a upravit, abyste mohli nasadit cluster, který nejlépe vyhovuje vašim potřebám.

## <a name="view-performance-counters"></a>Zobrazit čítače výkonu

Teď, když jste přidali agenta Log Analytics, přecházíte na portál Log Analytics a zvolíte si čítače výkonu, které chcete shromažďovat.

1. V Azure Portal přejdete do skupiny prostředků, ve které jste vytvořili řešení Service Fabric Analytics. Vyberte **ServiceFabric \<nameOfLog AnalyticsWorkspace\>**.

2. Klikněte na **Log Analytics**.

3. Klikněte na **Upřesnit nastavení**.

4. Klikněte na **data** a pak na **čítače výkonu Windows nebo Linux**. K dispozici je seznam výchozích čítačů, které můžete povolit, a můžete nastavit interval pro shromažďování dat. Můžete také přidat [Další čítače výkonu](service-fabric-diagnostics-event-generation-perf.md) ke shromáždění. Správný formát je odkazován v tomto [článku](/windows/win32/perfctrs/specifying-a-counter-path).

5. Klikněte na **Uložit** a pak na **OK**.

6. Zavřete okno Upřesnit nastavení.

7. V záhlaví Obecné klikněte na možnost **Souhrn pracovního prostoru**.

8. Zobrazí se dlaždice ve formě grafu pro každé povolené řešení, včetně jednoho pro Service Fabric. Kliknutím na graf **Service Fabric** pokračujte v řešení Service Fabric Analytics.

9. Zobrazí se několik dlaždic s grafy pro provozní kanál a spolehlivé služby. Grafická reprezentace toku dat pro čítače, které jste vybrali, se zobrazí v části metriky uzlů.

10. Kliknutím na graf metriky kontejnerů zobrazíte další podrobnosti. Můžete také dotazovat se na data čítače výkonu podobně jako u událostí clusteru a filtrovat uzly, název čítače výkonu a hodnoty pomocí dotazovacího jazyka Kusto.

![Log Analytics dotaz na čítač výkonu](media/service-fabric-diagnostics-event-analysis-oms/oms_node_metrics_table.PNG)

## <a name="next-steps"></a>Další kroky

* Shromážděte relevantní [čítače výkonu](service-fabric-diagnostics-event-generation-perf.md). Chcete-li nakonfigurovat agenta Log Analytics pro shromažďování konkrétních čítačů výkonu, přečtěte si téma [Konfigurace zdrojů dat](../azure-monitor/agents/agent-data-sources.md#configuring-data-sources).
* Konfigurace protokolů Azure Monitor pro nastavení [automatizovaného upozorňování](../azure-monitor/alerts/alerts-overview.md) na pomoc při zjišťování a diagnostice
* Jako alternativu můžete shromažďovat čítače výkonu prostřednictvím [rozšíření Azure Diagnostics a posílat je Application Insights](service-fabric-diagnostics-event-aggregation-wad.md#add-the-application-insights-sink-to-the-resource-manager-template)
