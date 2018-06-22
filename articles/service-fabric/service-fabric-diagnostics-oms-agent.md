---
title: Azure Service Fabric - s analýzy protokolů pro monitorování výkonu | Microsoft Docs
description: Zjistěte, jak nastavit agenta analýzy protokolů pro monitorování kontejnery a čítače výkonu pro Azure Service Fabric clusterů.
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
ms.openlocfilehash: a31fe62f2e81a0e39e4c314fc736e91e72bf7517
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/21/2018
ms.locfileid: "36301549"
---
# <a name="performance-monitoring-with-log-analytics"></a>Pomocí analýzy protokolů pro sledování výkonu

Tento článek popisuje kroky pro přidat agenta analýzy protokolů jako škálování virtuálního počítače nastavit rozšíření do clusteru a připojte ho k vaší existující pracovní prostor analýzy protokolů Azure. To umožňuje shromažďování dat diagnostiky o kontejnery, aplikací a monitorování výkonu. Přidejte jej jako rozšíření do prostředek sady škálování virtuálního počítače Azure Resource Manager zajistí, že ho získá nainstalovat na všech uzlech, i když škálování clusteru.

> [!NOTE]
> Tento článek předpokládá, že máte k pracovnímu prostoru analýzy protokolů Azure už nastavili. Pokud ho použít nechcete, přejděte na [nastavení analýzy protokolů Azure](service-fabric-diagnostics-oms-setup.md)

## <a name="add-the-agent-extension-via-azure-cli"></a>Přidat agenta rozšíření prostřednictvím rozhraní příkazového řádku Azure

Nejlepší způsob, jak přidat agenta Analytics protokolu ke clusteru prostřednictvím škálování virtuálních počítačů nastavena dostupných rozhraní API pomocí Azure CLI. Pokud jste ještě nastavení rozhraní příkazového řádku Azure, přejděte na portál Azure a otevře [cloudové prostředí](../cloud-shell/overview.md) instanci, nebo [nainstalovat Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).

1. Jakmile se vyžaduje vaše cloudové prostředí, zkontrolujte, zda že pracujete v rámci stejného předplatného jako prostředku. Zkontrolovat to s `az account show` a zajistěte, aby hodnota "název" odpovídá předplatného vašeho clusteru.

2. Na portálu přejděte do skupiny prostředků, kde je umístěn pracovní prostor analýzy protokolů. Klikněte do prostředků analýzy protokolů (typ prostředku bude analýzy protokolů). Jakmile jste na stránce Přehled prostředků, klikněte na **Upřesnit nastavení** v levé nabídce v části nastavení.

    ![Stránka vlastností analýzy protokolů](media/service-fabric-diagnostics-oms-agent/oms-advanced-settings.png)
 
3. Klikněte na **servery Windows** Pokud jsou stálého do clusteru se systémem Windows, a **servery se systémem Linux** při vytváření clusteru s podporou systému Linux. Tato stránka zobrazí vaše `workspace ID` a `workspace key` (uvedené na portálu jako primární klíč). Budete potřebovat pro další krok.

4. Spusťte příkaz pro instalaci agenta analýzy protokolů do clusteru, pomocí `vmss extension set` rozhraní API ve své cloudové prostředí:

    Pro cluster s podporou Windows:
    
    ```sh
    az vmss extension set --name MicrosoftMonitoringAgent --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<Log AnalyticsworkspaceId>'}" --protected-settings "{'workspaceKey':'<Log AnalyticsworkspaceKey>'}"
    ```

    Pro cluster s podporou Linux:

    ```sh
    az vmss extension set --name Log AnalyticsAgentForLinux --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<Log AnalyticsworkspaceId>'}" --protected-settings "{'workspaceKey':'<Log AnalyticsworkspaceKey>'}"
    ```

    Tady je příklad Agent analýzy protokolů, který se přidává do clusteru se systémem Windows.

    ![Přihlaste se analýza agenta rozhraní příkazového řádku příkaz](media/service-fabric-diagnostics-oms-agent/cli-command.png)
 
5. Je třeba vzít méně než 15 minut úspěšně přidat agenta do uzlů. Můžete ověřit, že byly přidány agenty pomocí `az vmss extension list` rozhraní API:

    ```sh
    az vmss extension list --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType>
    ```

## <a name="add-the-agent-via-the-resource-manager-template"></a>Přidat agenta pomocí šablony Resource Manageru

Ukázka Resource Manager šablony, které nasazení k pracovnímu prostoru analýzy protokolů Azure a přidat agenta na každý z uzlů je k dispozici pro [Windows](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Windows) nebo [Linux](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Linux).

Můžete stáhnout a upravit tato šablona nasazení clusteru, který nejlépe vyhovuje vašim potřebám.

## <a name="view-performance-counters"></a>Čítače výkonu zobrazení

Teď, když jste přidali agenta analýzy protokolů, head na over na portál analýzy protokolů a zvolte čítače výkonu, které jste chtěli shromažďovat. 

1. Na portálu Azure přejděte do skupiny prostředků, ve které jste vytvořili řešení Service Fabric analýzy. Vyberte **ServiceFabric\<nameOfLog AnalyticsWorkspace\>**.

2. Klikněte na **Log Analytics**.

3. Klikněte na tlačítko **upřesňující nastavení**.

4. Klikněte na tlačítko **Data**, pak klikněte na tlačítko **Windows nebo Linux čítače výkonu**. Je seznam Výchozí čítače, které můžete povolit a můžete nastavit interval pro kolekci také. Můžete také přidat [další čítače výkonu](service-fabric-diagnostics-event-generation-perf.md) ke shromažďování. Správný formát je odkazováno v tomto [článku](https://msdn.microsoft.com/library/windows/desktop/aa373193(v=vs.85).aspx).

5. Klikněte na tlačítko **Uložit**, pak klikněte na tlačítko **OK**.

6. Zavřete okno Upřesnit nastavení.

7. V části Obecné záhlaví, klikněte na **přehled**.

8. Zobrazí se dlaždice ve formě grafu pro každou z řešení povoleno, včetně jeden pro Service Fabric. Klikněte **Service Fabric** grafu nadále řešení Service Fabric analýzy.

9. Zobrazí se několik dlaždic s grafy na provozní kanál a spolehlivé služby události. Grafické znázornění dat odesílaných v pro čítače, které jste vybrali se zobrazí v uzlu metriky. 

10. Klikněte na graf kontejneru metrika zobrazíte další podrobnosti. Můžete taky zadat dotaz na data čítače výkonu podobně jako na clusteru události a na uzly, název čítače výkonu a hodnoty pomocí dotazovacího jazyka pro Kusto filtr.

![Dotaz čítače výkonu protokolu analýzy](media/service-fabric-diagnostics-event-analysis-oms/oms_node_metrics_table.PNG)

## <a name="next-steps"></a>Další postup

* Shromažďovat relevantní [čítače výkonu](service-fabric-diagnostics-event-generation-perf.md). Konfigurace agenta analýzy protokolů ke shromažďování konkrétních čítačích výkonu, zkontrolujte [konfigurace zdroje dat](../log-analytics/log-analytics-data-sources.md#configuring-data-sources).
* Konfigurace analýzy protokolů nastavit [automatizované výstrahy](../log-analytics/log-analytics-alerts.md) které pomáhají při zjišťování a Diagnostika
* Jako alternativu můžete shromáždit čítače výkonu prostřednictvím [rozšíření diagnostiky Azure a odešlete je Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md#add-the-ai-sink-to-the-resource-manager-template)
