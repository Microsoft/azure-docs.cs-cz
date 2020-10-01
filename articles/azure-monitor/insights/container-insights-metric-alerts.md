---
title: Výstrahy metrik z Azure Monitor pro kontejnery
description: Tento článek kontroluje Doporučené výstrahy metriky, které jsou dostupné z Azure Monitor pro kontejnery ve verzi Public Preview.
ms.topic: conceptual
ms.date: 09/24/2020
ms.openlocfilehash: 83394faf3d7296522151b815bddd910d47e45d24
ms.sourcegitcommit: 4bebbf664e69361f13cfe83020b2e87ed4dc8fa2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2020
ms.locfileid: "91619946"
---
# <a name="recommended-metric-alerts-preview-from-azure-monitor-for-containers"></a>Doporučené výstrahy metriky (Preview) z Azure Monitor pro kontejnery

Pokud chcete upozornit na problémy systémových prostředků, když se setkáváte na vyžádání a běží blízko kapacity, Azure Monitor u kontejnerů byste vytvořili upozornění protokolu na základě údajů o výkonu uložených v protokolech Azure Monitor. Azure Monitor for Containers nyní zahrnuje předem konfigurovaná pravidla upozornění na metriky pro cluster AKS a Kubernetes s podporou ARC Azure, který je ve verzi Public Preview.

Tento článek popisuje prostředí a poskytuje pokyny ke konfiguraci a správě těchto pravidel výstrah.

Pokud nejste obeznámeni s výstrahami Azure Monitor, přečtěte si téma [Přehled výstrah v Microsoft Azure](../platform/alerts-overview.md) před tím, než začnete. Další informace o výstrahách metrik najdete [v tématu výstrahy metrik v Azure monitor](../platform/alerts-metric-overview.md).

## <a name="prerequisites"></a>Požadavky

Než začnete, zkontrolujte následující:

* Vlastní metriky jsou dostupné jenom v podmnožině oblastí Azure. Seznam podporovaných oblastí je popsán v části [podporované oblasti](../platform/metrics-custom-overview.md#supported-regions).

* Aby bylo možné podporovat výstrahy metrik a zavedení dalších metrik, je minimální požadovaná verze agenta **Microsoft/OMS: ciprod05262020** for AKS a **Microsoft/OMS: Ciprod09252020** pro cluster Azure ARC s podporou Kubernetes.

    Pokud chcete ověřit, že cluster používá novější verzi agenta, můžete:

    * Spusťte příkaz: `kubectl describe <omsagent-pod-name> --namespace=kube-system` . Ve vráceném stavu si všimněte hodnoty v části **Image** pro omsagent v oddílu *Containers* výstupu. 
    * Na kartě **uzly** vyberte uzel clusteru a v podokně **vlastnosti** napravo si poznamenejte hodnotu v části **značka image agenta**.

    Hodnota zobrazená pro AKS by měla být verze **ciprod05262020** nebo novější. Hodnota zobrazená pro cluster Kubernetes s povoleným ARC Azure by měla být verze **ciprod09252020** nebo novější. Pokud má cluster starší verzi, přečtěte si téma [Postup upgradu agenta Azure monitor for Containers](container-insights-manage-agent.md#upgrade-agent-on-aks-cluster) , kde najdete pokyny k získání nejnovější verze.

    Další informace týkající se vydání agenta najdete v tématu [Historie vydání verze agenta](https://github.com/microsoft/docker-provider/tree/ci_feature_prod). Pokud chcete ověřit metriky, můžete použít Azure Monitor Průzkumníku metrik a ověřit z **oboru názvů metriky** , na které je **Přehled** uvedený. Pokud je, můžete pokračovat a začít nastavovat výstrahy. Pokud se nezobrazí žádná metrika, v instančním objektu nebo ve službě MSI chybí potřebná oprávnění. Pokud chcete ověřit, jestli je hlavní název služby (SPN) nebo MSI členem role **vydavatele metrik monitorování** , použijte postup popsaný v části [upgrade na cluster pomocí Azure CLI](container-insights-update-metrics.md#upgrade-per-cluster-using-azure-cli) a potvrďte a nastavte přiřazení role.

## <a name="alert-rules-overview"></a>Přehled pravidel výstrah

Pokud chcete upozornit na to, co Azure Monitor kontejnerů, zahrnuje následující výstrahy metriky pro clustery Kubernetes a Azure ARC s povoleným AKS:

|Název| Popis |Výchozí prahová hodnota |
|----|-------------|------------------|
|Průměrný procesor kontejneru% |Vypočítá průměrný procesor využívaný na jeden kontejner.|V případě, že průměrné využití procesoru na kontejner je větší než 95%.| 
|Průměrná paměť pracovní sady v kontejneru% |Vypočítá průměrnou paměť pracovní sady použitou na kontejner.|V případě, že průměrná spotřeba paměti pracovní sady na kontejner je větší než 95%. |
|Average CPU % (průměrné procento využití procesoru) |Vypočítá průměrný počet využitých PROCESORů na uzel. |Když je využití procesoru na středních uzlech větší než 80% |
|Průměrné využití disku% |Vypočítá průměrné využití disku pro uzel.|Když je využití disku pro uzel větší než 80%. |
|Průměrná paměť pracovní sady% |Vypočítá průměrnou paměť pracovní sady pro uzel. |Průměrná velikost pracovní sady pro uzel je větší než 80%. |
|Restartování počtu kontejnerů |Vypočítá počet restartování kontejnerů. | Pokud je restartování kontejneru větší než 0. |
|Počet neúspěšných pod |Vypočítá, zda je některý z nich ve stavu selhání.|Pokud je počet lusků ve stavu selhání větší než 0. |
|Stav nadschodišťového uzlu |Vypočítá, zda je některý uzel ve stavu.|V případě, že je počet uzlů ve stavu-běhoun větší než 0. |
|OOM ukončených kontejnerů |Vypočítá počet OOM ukončených kontejnerů. |V případě, že počet ukončených kontejnerů OOM je větší než 0. |
|Příprava pro lusky |Vypočítá průměrný stav připravenosti lusků. |Když je stav připravenosti lusků menší než 80%.|
|Počet dokončených úloh |Vypočítá počet úloh, které byly dokončeny před více než šesti hodinami. |V případě, že počet zastaralých úloh, které jsou starší než 6 hodin, je větší než 0.|

V rámci všech těchto pravidel výstrah existují společné vlastnosti:

* Všechna pravidla výstrah jsou založena na metrikě.

* Všechna pravidla výstrahy jsou ve výchozím nastavení zakázána.

* Všechna pravidla výstrahy se vyhodnocují jednou za minutu a prohledají se zpátky za posledních 5 minut dat.

* Pravidla upozornění nemají ve výchozím nastavení přiřazenou skupinu akcí. Do této výstrahy můžete přidat [skupinu akcí](../platform/action-groups.md) buď výběrem existující skupiny akcí, nebo vytvořením nové skupiny akcí při úpravě pravidla výstrahy.

* Prahovou hodnotu pro pravidla výstrahy můžete upravit přímo jejich úpravou. Před úpravou prahové hodnoty ale Přečtěte si pokyny, které jsou uvedené v jednotlivých pravidlech upozornění.

Následující metriky založené na výstrahách mají v porovnání s ostatními metrikami jedinečné charakteristiky chování:

* Metrika *completedJobsCount* se odesílá pouze v případě, že jsou dokončeny úlohy před více než šesti hodinami.

* Metrika *containerRestartCount* se posílá pouze v případě, že dojde k restartování kontejnerů.

* Metrika *oomKilledContainerCount* se posílá pouze v případě, že existují OOM ukončené kontejnery.

* metriky *cpuExceededPercentage*, *memoryRssExceededPercentage*a *memoryWorkingSetExceededPercentage* se odesílají v případě, že hodnoty pracovní sady procesoru, paměti RSS a paměti překročí nakonfigurovanou prahovou hodnotu (výchozí prahová hodnota je 95%). Tyto prahové hodnoty jsou výhradně pro mezní hodnotu podmínky upozornění zadanou pro příslušné pravidlo výstrahy. To znamená, že pokud chcete shromáždit tyto metriky a analyzovat je z [Průzkumníka metrik](../platform/metrics-getting-started.md), doporučujeme nastavit prahovou hodnotu na nižší hodnotu, než je prahová hodnota pro výstrahu. Konfigurace související s nastavením kolekce pro prahové hodnoty využití prostředků kontejneru se dá přepsat v souboru ConfigMaps v části `[alertable_metrics_configuration_settings.container_resource_utilization_thresholds]` . Podrobnosti týkající se konfigurace konfiguračního souboru ConfigMap najdete v části [Konfigurace metrik s výstrahou ConfigMaps](#configure-alertable-metrics-in-configmaps) .

## <a name="metrics-collected"></a>Shromážděné metriky

Následující metriky jsou povolené a shromážděné, pokud není uvedeno jinak, jako součást této funkce:

|Obor názvů metriky |Metrika |Popis |
|---------|----|------------|
|Insights. Container/Nodes |cpuUsageMillicores |Využití CPU v millicores podle hostitele.|
|Insights. Container/Nodes |cpuUsagePercentage |Procento využití procesoru podle uzlu|
|Insights. Container/Nodes |memoryRssBytes |Využití kanálu RSS v bajtech podle hostitele|
|Insights. Container/Nodes |memoryRssPercentage |Procento využití paměti RSS podle hostitele|
|Insights. Container/Nodes |memoryWorkingSetBytes |Využití paměti pracovní sady v bajtech podle hostitele.|
|Insights. Container/Nodes |memoryWorkingSetPercentage |Procento využití paměti pracovní sady podle hostitele.|
|Insights. Container/Nodes |nodesCount |Počet uzlů podle stavu|
|Insights. Container/Nodes |diskUsedPercentage |Procento disku použitého v uzlu podle zařízení.|
|Přehledy. kontejner/lusky |podCount |Počet lusků podle řadiče, oboru názvů, uzlu a fáze|
|Přehledy. kontejner/lusky |completedJobsCount |Počet dokončených úloh: počet starších uživatelsky konfigurovatelné prahové hodnoty (výchozí nastavení je 6 hodin) podle kontroléru Kubernetes obor názvů. |
|Přehledy. kontejner/lusky |restartingContainerCount |Počet restartování kontejneru podle kontroléru Kubernetes obor názvů.|
|Přehledy. kontejner/lusky |oomKilledContainerCount |Počet kontejnerů OOMkilled podle kontroléru, obor názvů Kubernetes.|
|Přehledy. kontejner/lusky |podReadyPercentage |Procento lusků ve stavu připraveno podle kontroleru, Kubernetes obor názvů.|
|Insights. Container/Containers |cpuExceededPercentage |Procento využití procesoru u kontejnerů, které překračují mezní hodnotu uživatelsky konfigurovatelné (výchozí hodnota je 95,0) podle názvu kontejneru, názvu kontroleru, oboru názvů Kubernetes, pod názvem.<br> Sběru  |
|Insights. Container/Containers |memoryRssExceededPercentage |Procento paměti RSS pro kontejnery překračující prahovou hodnotu uživatelsky konfigurovatelné (výchozí hodnota je 95,0) podle názvu kontejneru, názvu kontroleru, oboru názvů Kubernetes pod názvem.|
|Insights. Container/Containers |memoryWorkingSetExceededPercentage |Procento pracovní sady paměti pro kontejnery překračující prahovou hodnotu uživatele (výchozí je 95,0) podle názvu kontejneru, názvu kontroleru, oboru názvů Kubernetes pod názvem.|

## <a name="enable-alert-rules"></a>Povolit pravidla upozornění

Pomocí těchto kroků povolíte výstrahy metrik v Azure Monitor z Azure Portal. Pokud chcete povolit použití šablony Správce prostředků, přečtěte si téma [povolení pomocí šablony Správce prostředků](#enable-with-a-resource-manager-template).

### <a name="from-the-azure-portal"></a>Pomocí webu Azure Portal

Tato část vás provede povolením výstrahy metriky Azure Monitor pro kontejnery (Preview) z Azure Portal.

1. Přihlaste se na web [Azure Portal](https://portal.azure.com/).

2. Přístup k funkci upozornění na metriku Azure Monitor for Containers (Preview) je k dispozici přímo z clusteru AKS, a to tak, že v levém podokně v Azure Portal vyberete **přehledy** .

3. Na panelu příkazů vyberte **Doporučené výstrahy**.

    ![Možnost Doporučené výstrahy v Azure Monitor pro kontejnery](./media/container-insights-metric-alerts/command-bar-recommended-alerts.png)

4. Podokno vlastností **Doporučené výstrahy** se automaticky zobrazuje na pravé straně stránky. Ve výchozím nastavení jsou všechna pravidla výstrah v seznamu zakázaná. Po výběru možnosti **Povolit**se vytvoří pravidlo upozornění a název pravidla se aktualizuje tak, aby zahrnoval odkaz na prostředek výstrahy.

    ![Podokno vlastností Doporučené výstrahy](./media/container-insights-metric-alerts/recommended-alerts-pane.png)

    Po výběru možnosti **Povolit/zakázat** pro povolení výstrahy se vytvoří pravidlo upozornění a název pravidla se aktualizuje tak, aby zahrnoval odkaz na skutečný prostředek výstrahy.

    ![Povolení pravidla upozornění](./media/container-insights-metric-alerts/recommended-alerts-pane-enable.png)

5. Pravidla upozornění nejsou přidružena ke [skupině akcí](../platform/action-groups.md) , aby bylo možné upozornit uživatele na aktivaci výstrahy. Vyberte **možnost** přidat nebo **vytvořit**skupinu akcí a na stránce **skupiny akcí** vyberte možnost **Přidat** nebo vytvořit skupinu akcí.

    ![Vyberte skupinu akcí.](./media/container-insights-metric-alerts/select-action-group.png)

### <a name="enable-with-a-resource-manager-template"></a>Povolit s Správce prostředků šablonou

Pomocí šablony Azure Resource Manager a souboru parametrů můžete vytvořit zahrnuté výstrahy metriky v Azure Monitor.

Základní postup je následující:

1. Stáhněte jednu nebo všechny dostupné šablony, které popisují, jak vytvořit výstrahu z [GitHubu](https://github.com/microsoft/Docker-Provider/tree/ci_dev/alerts/recommended_alerts_ARM).

2. Vytvořte a použijte [soubor parametrů](../../azure-resource-manager/templates/parameter-files.md) jako JSON pro nastavení hodnot požadovaných k vytvoření pravidla výstrahy.

3. Nasaďte šablonu z Azure Portal, PowerShellu nebo rozhraní příkazového řádku Azure CLI.

#### <a name="deploy-through-azure-portal"></a>Nasazení prostřednictvím Azure Portal

1. Stáhněte a uložte do místní složky, Azure Resource Manager šablony a souboru parametrů, a vytvořte tak pravidlo výstrahy pomocí následujících příkazů:

2. Chcete-li nasadit přizpůsobenou šablonu prostřednictvím portálu, vyberte možnost **vytvořit prostředek** z [Azure Portal](https://portal.azure.com).

3. Vyhledejte **šablonu**a pak vyberte **template Deployment**.

4. Vyberte **Vytvořit**.

5. Zobrazí se několik možností pro vytvoření šablony. Vyberte **vytvořit vlastní šablonu v editoru**.

6. Na **stránce Upravit šablonu**vyberte **načíst soubor** a pak vyberte soubor šablony.

7. Na stránce **Upravit šablonu** vyberte Save ( **Uložit**).

8. Na stránce **vlastní nasazení** zadejte následující a potom po dokončení vyberte **koupit** pro nasazení šablony a vytvoření pravidla výstrahy.

    * Skupina prostředků
    * Umístění
    * Název výstrahy
    * ID prostředku clusteru

#### <a name="deploy-with-azure-powershell-or-cli"></a>Nasazení pomocí Azure PowerShell nebo rozhraní příkazového řádku

1. Stáhněte a uložte do místní složky, Azure Resource Manager šablony a souboru parametrů, a vytvořte tak pravidlo výstrahy pomocí následujících příkazů:

2. Pomocí PowerShellu nebo rozhraní příkazového řádku Azure můžete vytvořit výstrahu metriky pomocí souboru šablony a parametrů.

    Použití Azure Powershell

    ```powershell
    Connect-AzAccount

    Select-AzSubscription -SubscriptionName <yourSubscriptionName>
    New-AzResourceGroupDeployment -Name CIMetricAlertDeployment -ResourceGroupName ResourceGroupofTargetResource `
    -TemplateFile templateFilename.json -TemplateParameterFile templateParameterFilename.parameters.json
    ```

    Použití Azure CLI

    ```azurecli
    az login

    az group deployment create \
    --name AlertDeployment \
    --resource-group ResourceGroupofTargetResource \
    --template-file templateFileName.json \
    --parameters @templateParameterFilename.parameters.json
    ```

    >[!NOTE]
    >I když se upozornění na metriku dá vytvořit v jiné skupině prostředků jako cílový prostředek, doporučujeme použít stejnou skupinu prostředků jako cílový prostředek.

## <a name="edit-alert-rules"></a>Upravit pravidla upozornění

Můžete zobrazit a spravovat pravidla výstrah Azure Monitor pro kontejnery, upravit její prahovou hodnotu nebo nakonfigurovat [skupinu akcí](../platform/action-groups.md) pro cluster AKS. I když můžete provádět tyto akce z Azure Portal a Azure CLI, můžete je taky dělat přímo z clusteru AKS v Azure Monitor for Containers.

1. Na panelu příkazů vyberte **Doporučené výstrahy**.

2. Chcete-li změnit prahovou hodnotu, vyberte v podokně **Doporučené výstrahy** povolenou výstrahu. V poli **Upravit pravidlo**vyberte **kritéria výstrahy** , která chcete upravit.

    * Pokud chcete změnit prahovou hodnotu pravidla upozornění, vyberte **podmínku**.
    * Pokud chcete zadat existující nebo vytvořit skupinu akcí, vyberte **Přidat** nebo **vytvořit** v části **Skupina akcí** .

Chcete-li zobrazit výstrahy vytvořené pro povolená pravidla, vyberte v podokně **Doporučené výstrahy** možnost **Zobrazit v upozorněních**. Budete přesměrováni do nabídky výstrahy pro cluster AKS, kde můžete zobrazit všechny výstrahy, které jsou aktuálně vytvořeny pro váš cluster.

## <a name="configure-alertable-metrics-in-configmaps"></a>Konfigurace metrik s výstrahou v ConfigMaps

Proveďte následující kroky ke konfiguraci konfiguračního souboru ConfigMap pro přepsání výchozích prahových hodnot využití prostředků kontejneru. Tyto kroky jsou použitelné pouze pro následující metriky s možností upozorňování.

* *cpuExceededPercentage*
* *memoryRssExceededPercentage*
* *memoryWorkingSetExceededPercentage*

1. Upravte soubor ConfigMap YAML v části `[alertable_metrics_configuration_settings.container_resource_utilization_thresholds]` .

2. Chcete-li změnit prahovou hodnotu *cpuExceededPercentage* na 90% a spustit shromažďování této metriky, když je tato prahová hodnota splněna a překročena, nakonfigurujte soubor ConfigMap pomocí následujícího příkladu.

    ```
    container_cpu_threshold_percentage = 90.0
    # Threshold for container memoryRss, metric will be sent only when memory rss exceeds or becomes equal to the following percentage
    container_memory_rss_threshold_percentage = 95.0
    # Threshold for container memoryWorkingSet, metric will be sent only when memory working set exceeds or becomes equal to the following percentage
    container_memory_working_set_threshold_percentage = 95.0
    ```

3. Spusťte následující příkaz kubectl: `kubectl apply -f <configmap_yaml_file.yaml>` .

    Příklad: `kubectl apply -f container-azm-ms-agentconfig.yaml`.

Dokončení změny konfigurace může trvat několik minut, než se projeví, a všechny omsagent v clusteru se restartují. Restartování je postupné restartování pro všechny omsagent lusky, ne pro všechna restartování ve stejnou dobu. Po dokončení restartů se zobrazí zpráva podobná následujícímu příkladu a obsahuje výsledek: `configmap "container-azm-ms-agentconfig" created` .

## <a name="next-steps"></a>Další kroky

- Podívejte se na [příklady dotazů protokolu](container-insights-log-search.md#search-logs-to-analyze-data) , kde najdete předdefinované dotazy a příklady pro vyhodnocení nebo přizpůsobení výstrah, vizualizace a analýzy clusterů.

- Další informace o Azure Monitor a o tom, jak monitorovat další aspekty clusteru Kubernetes, najdete v tématu [zobrazení výkonu clusteru Kubernetes](container-insights-analyze.md).
