---
title: Monitorování výkonu pomocí protokolů Azure Monitor
description: Zjistěte, jak nastavit agenta analýzy protokolů pro monitorování kontejnerů a čítačů výkonu pro clustery Azure Service Fabric.
author: srrengar
ms.topic: conceptual
ms.date: 04/16/2018
ms.author: srrengar
ms.openlocfilehash: c3c1bf511f3313e7408d6ce90b73de60bd1309f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79366734"
---
# <a name="performance-monitoring-with-azure-monitor-logs"></a>Monitorování výkonu pomocí protokolů Azure Monitor

Tento článek popisuje kroky, jak přidat agenta Log Analytics jako rozšíření škálovací sady virtuálních strojů do clusteru a připojit ho k existujícímu pracovnímu prostoru Azure Log Analytics. To umožňuje shromažďování diagnostických dat o kontejnerech, aplikacích a monitorování výkonu. Přidáním jako rozšíření prostředků škálovací sady virtuálních strojů Azure Resource Manager zajišťuje, že se nainstaluje na každém uzlu, a to i při škálování clusteru.

> [!NOTE]
> Tento článek předpokládá, že už máte nastavený pracovní prostor Azure Log Analytics. Pokud tak neučiníte, přejdete k [nastavení protokolů Azure Monitoru.](service-fabric-diagnostics-oms-setup.md)

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="add-the-agent-extension-via-azure-cli"></a>Přidání rozšíření agenta pomocí azure CLI

Nejlepší způsob, jak přidat agenta Log Analytics do clusteru, je prostřednictvím rozhraní API škálovací sady virtuálních strojů, které jsou k dispozici s rozhraním příkazového příkazového příkazu Kasovníka Azure. Pokud ještě nemáte nastavené vázací příkazy k Azure, přejdete na portál Azure a otevřete instanci [Cloud Shellu](../cloud-shell/overview.md) nebo [nainstalujte azure cli](https://docs.microsoft.com/cli/azure/install-azure-cli).

1. Jakmile je vaše Cloud Shell je požadováno, ujistěte se, že pracujete ve stejném předplatném jako váš prostředek. Zkontrolujte `az account show` to s a ujistěte se, že hodnota "název" odpovídá, že předplatného vašeho clusteru.

2. Na portálu přejděte do skupiny prostředků, kde se nachází pracovní prostor Log Analytics. Klikněte do prostředku analýzy protokolů (typ prostředku bude pracovní prostor Log Analytics). Jakmile se dostanete na stránku přehledu zdrojů, klikněte na **Upřesnit nastavení** v části Nastavení v levé nabídce.

    ![Stránka vlastností analýzy protokolů](media/service-fabric-diagnostics-oms-agent/oms-advanced-settings.png)

3. Pokud stojíte v clusteru Windows, klepněte na **servery systému Windows** a **servery Linux,** pokud vytváříte cluster Linux. Na této stránce `workspace ID` se `workspace key` zobrazí vaše a (uvedené jako primární klíč na portálu). Budete potřebovat oba pro další krok.

4. Spuštěním příkazu nainstalujte agenta Log Analytics `vmss extension set` do clusteru pomocí rozhraní API:

    Pro cluster Windows:

    ```azurecli
    az vmss extension set --name MicrosoftMonitoringAgent --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<Log AnalyticsworkspaceId>'}" --protected-settings "{'workspaceKey':'<Log AnalyticsworkspaceKey>'}"
    ```

    Pro linuxový cluster:

    ```azurecli
    az vmss extension set --name OmsAgentForLinux --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<Log AnalyticsworkspaceId>'}" --protected-settings "{'workspaceKey':'<Log AnalyticsworkspaceKey>'}"
    ```

    Tady je příklad agenta Analýzy protokolů, který se přidává do clusteru Windows.

    ![Příkaz cli agenta Log Analytics](media/service-fabric-diagnostics-oms-agent/cli-command.png)

5. To by mělo trvat méně než 15 min úspěšně přidat agenta do uzlů. Můžete ověřit, že agenti byli přidáni pomocí `az vmss extension list` rozhraní API:

    ```azurecli
    az vmss extension list --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType>
    ```

## <a name="add-the-agent-via-the-resource-manager-template"></a>Přidání agenta pomocí šablony Správce prostředků

Ukázkové šablony Správce prostředků, které nasazují pracovní prostor Azure Log Analytics a přidávají agenta do každého z vašich uzlů, jsou dostupné pro [Windows](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-OMS-UnSecure) nebo [Linux](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeType-Secure-OMS).

Tuto šablonu můžete stáhnout a upravit a nasadit cluster, který nejlépe vyhovuje vašim potřebám.

## <a name="view-performance-counters"></a>Zobrazit čítače výkonu

Teď, když jste přidali agenta Log Analytics, přejdete na portál Log Analytics a vyberte, které čítače výkonu chcete shromažďovat.

1. Na webu Azure Portal přejděte do skupiny prostředků, ve které jste vytvořili řešení Service Fabric Analytics. Vyberte **servicefabric\<nameOfLog\>AnalyticsWorkspace**.

2. Klikněte na** Log Analytics**.

3. Klepněte na **položku Upřesnit nastavení**.

4. Klepněte na **položku Data**a potom klepněte na **položku Windows nebo Linux Performance Counters**. K dispozici je seznam výchozích čítačů, které můžete povolit, a můžete také nastavit interval pro shromažďování. Můžete také přidat [další čítače výkonu](service-fabric-diagnostics-event-generation-perf.md) shromažďovat. Správný formát je odkazován v tomto [článku](https://msdn.microsoft.com/library/windows/desktop/aa373193(v=vs.85).aspx).

5. Klepněte na tlačítko **Uložit**a potom klepněte na tlačítko **OK**.

6. Zavřete okno Upřesnit nastavení.

7. V záhlaví Obecné klikněte na **souhrn pracovního prostoru**.

8. Zobrazí se dlaždice ve formě grafu pro každé z povolených řešení, včetně jednoho pro service fabric. Kliknutím na graf **Service Fabric** přenesete k řešení Service Fabric Analytics.

9. Uvidíte několik dlaždic s grafy na provozní kanál a spolehlivé služby události. Grafické znázornění dat, která se pro vybrané čítače přetočí, zobrazí v části Metriky uzlů.

10. Kliknutím na graf Metriky kontejnerů zobrazíte další podrobnosti. Můžete také dotaz na data čítače výkonu podobně jako události clusteru a filtrovat na uzly, název čítače perf a hodnoty pomocí dotazovacího jazyka Kusto.

![Dotaz čítače perf analýzy protokolu](media/service-fabric-diagnostics-event-analysis-oms/oms_node_metrics_table.PNG)

## <a name="next-steps"></a>Další kroky

* Shromážděte příslušné [čítače výkonu](service-fabric-diagnostics-event-generation-perf.md). Chcete-li nakonfigurovat agenta Analýzy protokolů tak, aby shromažďoval konkrétní čítače výkonu, [zkontrolujte konfiguraci zdrojů dat](../azure-monitor/platform/agent-data-sources.md#configuring-data-sources).
* Konfigurace protokolů Azure Monitor u nastavení [automatického upozorňování](../log-analytics/log-analytics-alerts.md) na pomoc při zjišťování a diagnostice
* Jako alternativu můžete shromažďovat čítače výkonu prostřednictvím [rozšíření Diagnostika Azure a odesílat je do Application Insights](service-fabric-diagnostics-event-aggregation-wad.md#add-the-application-insights-sink-to-the-resource-manager-template)
