---
title: Azure Service Fabric - s analýzy protokolů pro monitorování výkonu | Microsoft Docs
description: Zjistěte, jak nastavit agenta OMS pro monitorování kontejnery a čítače výkonu pro Azure Service Fabric clusterů.
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
ms.openlocfilehash: a3ce72e51477c1eda99461b3910bfeeac207be55
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="performance-monitoring-with-log-analytics"></a>Pomocí analýzy protokolů pro sledování výkonu

Tento článek popisuje kroky pro přidat agenta analýzy protokolů, také známé jako OMS jako škálování virtuálního počítače nastavit rozšíření do clusteru a připojte ho k vaší existující pracovní prostor analýzy protokolů Azure. To umožňuje shromažďování dat diagnostiky o kontejnery, aplikací a monitorování výkonu. Přidejte jej jako rozšíření do prostředek sady škálování virtuálního počítače Azure Resource Manager zajistí, že ho získá nainstalovat na všech uzlech, i když škálování clusteru.

> [!NOTE]
> Tento článek předpokládá, že máte k pracovnímu prostoru analýzy protokolů Azure už nastavili. Pokud ho použít nechcete, přejděte na [nastavení analýzy protokolů Azure](service-fabric-diagnostics-oms-setup.md)

## <a name="add-the-agent-extension-via-azure-cli"></a>Přidat agenta rozšíření prostřednictvím rozhraní příkazového řádku Azure

Nejlepší způsob, jak přidat agenta OMS ke clusteru prostřednictvím škálování virtuálních počítačů nastavena dostupných rozhraní API pomocí Azure CLI. Pokud jste ještě nastavení rozhraní příkazového řádku Azure, přejděte na portál Azure a otevře [cloudové prostředí](../cloud-shell/overview.md) instanci, nebo [nainstalovat Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).

1. Jakmile se vyžaduje vaše cloudové prostředí, zkontrolujte, zda že pracujete v rámci stejného předplatného jako prostředku. Zkontrolovat to s `az account show` a zajistěte, aby hodnota "název" odpovídá předplatného vašeho clusteru.

2. Na portálu přejděte do skupiny prostředků, kde je umístěn pracovní prostor analýzy protokolů. Klikněte do prostředků analýzy protokolů (typ prostředku bude analýzy protokolů). Jakmile jste na stránce Přehled prostředků, klikněte na **Upřesnit nastavení** v levé nabídce v části nastavení.

    ![Stránka vlastností analýzy protokolů](media/service-fabric-diagnostics-oms-agent/oms-advanced-settings.png)
 
3. Klikněte na **servery Windows** Pokud jsou stálého do clusteru se systémem Windows, a **servery se systémem Linux** při vytváření clusteru s podporou systému Linux. Tato stránka zobrazí vaše `workspace ID` a `workspace key` (uvedené na portálu jako primární klíč). Budete potřebovat pro další krok.

4. Spusťte příkaz pro instalaci agenta OMS na cluster, pomocí `vmss extension set` rozhraní API ve své cloudové prostředí:

    Pro cluster s podporou Windows:
    
    ```sh
    az vmss extension set --name MicrosoftMonitoringAgent --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<OMSworkspaceId>'}" --protected-settings "{'workspaceKey':'<OMSworkspaceKey>'}"
    ```

    Pro cluster s podporou Linux:

    ```sh
    az vmss extension set --name OmsAgentForLinux --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<OMSworkspaceId>'}" --protected-settings "{'workspaceKey':'<OMSworkspaceKey>'}"
    ```

    Tady je příklad OMS agenta, který se přidává do clusteru se systémem Windows.

    ![Příkaz rozhraní příkazového řádku agenta OMS](media/service-fabric-diagnostics-oms-agent/cli-command.png)
 
5. Je třeba vzít méně než 15 minut úspěšně přidat agenta do uzlů. Můžete ověřit, že byly přidány agenty pomocí `az vmss extension list` rozhraní API:

    ```sh
    az vmss extension list --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType>
    ```

## <a name="add-the-agent-via-the-resource-manager-template"></a>Přidat agenta pomocí šablony Resource Manageru

Ukázka Resource Manager šablony, které nasazení k pracovnímu prostoru analýzy protokolů Azure a přidat agenta na každý z uzlů je k dispozici pro [Windows](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Windows) nebo [Linux](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Linux).

Můžete stáhnout a upravit tato šablona nasazení clusteru, který nejlépe vyhovuje vašim potřebám.

## <a name="view-performance-counters-in-the-log-analytics-portal"></a>Čítače výkonu zobrazení na portálu analýzy protokolů

Teď, když jste přidali agenta OMS, head na over na portál analýzy protokolů a zvolte čítače výkonu, které jste chtěli shromažďovat. 

1. Na portálu Azure přejděte do skupiny prostředků, ve které jste vytvořili řešení Service Fabric analýzy. Vyberte **ServiceFabric\<nameOfOMSWorkspace\>**  a přejděte na stránku s jeho Přehled. V horní části klikněte na odkaz přejdete na portál OMS.

2. Jakmile jste na portálu, zobrazí se dlaždice ve formě grafu pro každou z řešení povoleno, včetně jeden pro Service Fabric. Klepněte sem a pokračovat v řešení Service Fabric analýzy. 

3. Nyní se zobrazí několik dlaždic s grafy na provozní kanál a spolehlivé služby události. Na pravé straně klikněte na ikonu zařízení přejděte na stránku nastavení.

    ![Nastavení OMS](media/service-fabric-diagnostics-oms-agent/oms-solutions-settings.png)

4. Na stránce nastavení kliknutím na možnost Data a zvolte Windows nebo Linux čítače výkonu. Existují seznam výchozích šablon, které můžete povolit a můžete nastavit interval pro kolekci také. Můžete také přidat [další čítače výkonu](service-fabric-diagnostics-event-generation-perf.md) ke shromažďování. Správný formát je odkazováno v tomto [článku](https://msdn.microsoft.com/library/windows/desktop/aa373193(v=vs.85).aspx).

Jakmile jsou nakonfigurované vaše čítače, head zpět na stránku řešení a zobrazí se vám brzo dat odesílaných v a zobrazují se v grafy v části **uzlu metriky**. Můžete taky zadat dotaz na data čítače výkonu podobně jako na clusteru události a na uzly, název čítače výkonu a hodnoty pomocí dotazovacího jazyka pro Kusto filtr. 

![Dotaz čítače výkonu OMS](media/service-fabric-diagnostics-oms-agent/oms-perf-counter-query.png)

## <a name="next-steps"></a>Další postup

* Shromažďovat relevantní [čítače výkonu](service-fabric-diagnostics-event-generation-perf.md). Konfigurace agenta OMS ke shromažďování konkrétních čítačích výkonu, zkontrolujte [konfigurace zdroje dat](../log-analytics/log-analytics-data-sources.md#configuring-data-sources).
* Konfigurace analýzy protokolů nastavit [automatizované výstrahy](../log-analytics/log-analytics-alerts.md) které pomáhají při zjišťování a Diagnostika
* Jako alternativu můžete shromáždit čítače výkonu prostřednictvím [rozšíření diagnostiky Azure a odešlete je Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md#add-the-ai-sink-to-the-resource-manager-template)