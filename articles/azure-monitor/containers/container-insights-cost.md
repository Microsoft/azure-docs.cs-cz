---
title: Náklady na monitorování pro kontejnerové přehledy | Microsoft Docs
description: Tento článek popisuje náklady na monitorování metriky & inventáře shromažďovaných službou Container Insights, aby zákazníkům pomohla spravovat jejich využití a přidružené náklady.
ms.topic: conceptual
ms.date: 05/29/2020
ms.openlocfilehash: 78387e950d476126d7c2065a530844e44fd59b4f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101728905"
---
# <a name="understand-monitoring-costs-for-container-insights"></a>Porozumění nákladům na monitorování pro službu Container Insights

Tento článek poskytuje cenové poradenství pro službu Container Insights, které vám pomůžou pochopit následující informace:

* Jak odhadnout náklady předem před tím, než povolíte tento přehled

* Jak měřit náklady po povolení služby Container Insights pro jeden nebo více kontejnerů

* Jak řídit shromažďování dat a snížit náklady

Azure Monitor protokoly shromažďuje, indexuje a ukládá data generovaná clusterem Kubernetes. 

Cenový model Azure Monitor je primárně založený na množství dat přijatých v gigabajtech za den do pracovního prostoru Log Analytics. Náklady na Log Analytics pracovní prostor nejsou založené jenom na objemu shromažďovaných dat, závisí taky na vybraném plánu a na tom, jak dlouho jste se rozhodli ukládat data generovaná z vašich clusterů.

>[!NOTE]
>Všechny velikosti a ceny jsou pouze pro vzorové odhady. Nejnovější ceny na základě Azure Monitor Log Analytics cenového modelu a oblasti Azure najdete na stránce s [cenami](https://azure.microsoft.com/pricing/details/monitor/) Azure monitor.

Následuje souhrn typů dat shromažďovaných z clusteru Kubernetes s využitím služby Container Insights, které mají vliv na náklady a které je možné přizpůsobit na základě vašeho využití:

- Stdout, stderr – protokoly kontejneru z každého monitorovaného kontejneru v každém Kubernetes oboru názvů v clusteru

- Proměnné prostředí kontejneru z každého monitorovaného kontejneru v clusteru

- Dokončené Kubernetes úlohy/lusky v clusteru, který nevyžaduje monitorování

- Aktivní likvidace metrik Prometheus

- [Shromažďování protokolů diagnostiky](../../aks/view-control-plane-logs.md) protokolů hlavního uzlu Kubernetes v clusteru AKS pro analýzu dat protokolu generovaných hlavními komponentami, jako jsou *Kube-apiserver* a *Kube-Controller-Manager*.

## <a name="what-is-collected-from-kubernetes-clusters"></a>Co se shromažďuje z clusterů Kubernetes

Container Insights obsahuje předdefinovanou sadu metrik a shromážděných položek inventáře, které se zapisují jako data protokolu v pracovním prostoru Log Analytics. Všechny níže uvedené metriky se ve výchozím nastavení shromažďují každou minutu.

### <a name="node-metrics-collected"></a>Shromážděné metriky uzlů

Následující seznam je 24 metrik na uzlech, které jsou shromažďovány:

- cpuUsageNanoCores
- cpuCapacityNanoCores
- cpuAllocatableNanoCores
- memoryRssBytes
- memoryWorkingSetBytes
- memoryCapacityBytes
- memoryAllocatableBytes
- restartTimeEpoch
- použito (disk)
- zdarma (disk)
- used_percent (disk)
- io_time (diskio)
- zápisy (diskio)
- čtení (diskio)
- write_bytes (diskio)
- write_time (diskio)
- iops_in_progress (diskio)
- read_bytes (diskio)
- read_time (diskio)
- err_in (NET)
- err_out (NET)
- bytes_recv (NET)
- bytes_sent (NET)
- Kubelet_docker_operations (Kubelet)

### <a name="container-metrics"></a>Metriky kontejneru

Následující seznam uvádí osm metrik na kontejner, které jsou shromážděny:

- cpuUsageNanoCores
- cpuRequestNanoCores
- cpuLimitNanoCores
- memoryRssBytes
- memoryWorkingSetBytes
- memoryRequestBytes
- memoryLimitBytes
- restartTimeEpoch

### <a name="cluster-inventory"></a>Inventář clusteru

Následující seznam uvádí data inventáře clusteru shromážděná ve výchozím nastavení:

- KubePodInventory – 1 za minutu na kontejner
- KubeNodeInventory – 1 za uzel za minutu
- KubeServices – 1 za službu za minutu
- ContainerInventory – 1 za kontejner za minutu

## <a name="estimating-costs-to-monitor-your-aks-cluster"></a>Odhad nákladů na monitorování clusteru AKS

Odhad níže vychází z clusteru Azure Kubernetes Service (AKS) s následujícím příkladem určení velikosti. Odhad se také vztahuje pouze na metriky a shromážděná data inventáře. U protokolů kontejnerů (stdout, stderr a proměnných prostředí) se liší v závislosti na velikostech protokolů generovaných úlohou a jejich vyloučení z našeho odhadu.

Pokud jste povolili monitorování clusteru AKS nakonfigurovaného takto,

- Tři uzly
- Dva disky na uzel
- Jedno síťové rozhraní na uzel
- 20 lusků (jeden kontejner v každém pod = 20 kontejnerů celkem)
- Dva obory názvů Kubernetes
- Pět Kubernetes služeb (zahrnuje Kube – systémové lusky, služby a obor názvů)
- Frekvence shromažďování = 60 sekund (výchozí)

V pracovním prostoru přiřazené Log Analytics můžete zobrazit tabulky a objem dat vygenerovaných za hodinu. Další informace o jednotlivých tabulkách najdete v tématu [záznamy kontejneru](container-insights-log-search.md#container-records).

|Tabulka | Odhad velikosti (MB za hodinu) |
|------|---------------|
|Výkon | 12,9 |
|InsightsMetrics | 11.3 |
|KubePodInventory | 1.5 |
|KubeNodeInventory | 0,75 |
|KubeServices | 0.13 |
|ContainerInventory | 3,6 |
|KubeHealth | 0.1 |
|KubeMonAgentEvents |0,005 |

Total = 31 MB/hod = 23,1 GB/měsíc (jeden měsíc = 31 dní)

S využitím výchozích [cen](https://azure.microsoft.com/pricing/details/monitor/) pro Log Analytics, což je model průběžných plateb, můžete odhadnout Azure monitor náklady měsíčně. Po zahrnutí rezervované kapacity by cena byla vyšší za měsíc v závislosti na vybrané rezervaci.

## <a name="controlling-ingestion-to-reduce-cost"></a>Řízení ingestování za účelem snížení nákladů

Vezměte v úvahu scénář, ve kterém má vaše organizace různé obchodní jednotky sdílené Kubernetes infrastruktury a pracovního prostoru Log Analytics. U každé obchodní jednotky oddělené oborem názvů Kubernetes. Pomocí sady Runbook **využití dat** , která je k dispozici v rozevíracím seznamu **Zobrazit sešity** , můžete vizualizovat, kolik dat je v každém pracovním prostoru přijímáno.

[![Zobrazit rozevírací seznam sešitů](media/container-insights-cost/workbooks-dropdown.png)](media/container-insights-cost/workbooks-dropdown.png#lightbox)


Tento sešit vám pomůže vizualizovat zdroj dat, aniž byste museli vytvářet vlastní knihovnu dotazů z toho, co sdílíme v naší dokumentaci. V tomto sešitu jsou grafy, pomocí kterých můžete zobrazit fakturovatelná data z těchto perspektiv jako:

- Celková fakturovatelná data ingestovaná v GB podle řešení
- Fakturovatelné data ingestovaná protokoly kontejnerů (protokoly aplikací)
- Fakturovatelné protokoly kontejneru data ingestovaná podle oboru názvů Kubernetes
- Fakturovatelné protokoly kontejnerů data ingestovaná oddělená názvem clusteru
- Fakturovatelné data protokolu kontejneru podle položky logsource
- Fakturovatelné diagnostická data ingestovaná v protokolech diagnostických hlavních uzlů

[![Sešit využití dat](media/container-insights-cost/data-usage-workbook.png)](media/container-insights-cost/data-usage-workbook.png#lightbox)

Chcete-li získat informace o správě práv a oprávnění k sešitu, zkontrolujte [řízení přístupu](../visualize/workbooks-access-control.md).

Po dokončení analýzy zjistíte, které zdroje nebo zdroje generují nejvíc dat, nebo více dat, která překračují vaše požadavky, můžete shromažďování dat znovu nakonfigurovat. Podrobnosti o konfiguraci kolekce proměnných stdout, stderr a prostředí jsou popsány v článku [Konfigurace nastavení shromažďování dat agenta](container-insights-agent-config.md) .

Níže najdete příklady toho, jaké změny můžete u clusteru použít úpravou souboru ConfigMap, abyste mohli pořídit náklady.

1. V souboru ConfigMap v části všechny obory názvů v clusteru zakažte protokoly STDOUT pomocí následujících úprav:

    ```
    [log_collection_settings]       
       [log_collection_settings.stdout]          
          enabled = false
    ```

2. Zakažte shromažďování protokolů stderr z oboru názvů pro vývoj (například **vývoj a testování**) a pokračujte v shromažďování protokolů stderr z jiných oborů názvů (například z **výroby** a **výchozích**) úpravou následujícího v souboru ConfigMap:

    >[!NOTE]
    >Kolekce protokolu Kube-System je ve výchozím nastavení zakázána. Výchozí nastavení je zachováno, přidání oboru názvů pro **vývoj a testování** do seznamu oborů názvů vyloučení je použito pro shromažďování protokolů stderr.

    ```
    [log_collection_settings.stderr]          
       enabled = true          
          exclude_namespaces = ["kube-system", "dev-test"]
    ```

3. Zakažte shromažďování proměnných prostředí v rámci clusteru úpravou následujícího v souboru ConfigMap. To platí pro všechny kontejnery v každém Kubernetes oboru názvů. 

    ```
    [log_collection_settings.env_var]
        enabled = false
    ```

4. Pokud chcete vyčistit dokončené úlohy, v definici úlohy zadejte zásady čištění, a to úpravou následujícího v souboru ConfigMap:

    ```
    apiVersion: batch/v1
    kind: Job
    metadata:
      name: pi-with-ttl
    spec:
      ttlSecondsAfterFinished: 100
    ```

Po použití jedné nebo několika těchto změn v ConfigMaps najdete informace v tématu [použití aktualizovaných ConfigMap](container-insights-prometheus-integration.md#applying-updated-configmap) pro použití ve vašem clusteru.

### <a name="prometheus-metrics-scraping"></a>Likvidace metrik Prometheus

Pokud využíváte [Prometheus metriky](container-insights-prometheus-integration.md), ujistěte se, že je k omezení počtu metrik shromažďovaných z clusteru potřeba zvážit následující:

- Ujistěte se, že je frekvence vyřazení nastavena optimálně (výchozí hodnota je 60 sekund). I když můžete tuto frekvenci prodloužit na 15 sekund, musíte zajistit, aby metriky, které vydáváte, byly v této frekvenci publikované. V opačném případě bude k dispozici mnoho duplicitních metrik a jejich odeslání do vašeho Log Analytics pracovního prostoru v intervalech přidávání do příjmu dat a nákladů na uchovávání, ale mají nižší hodnotu. 

- Služba Container Insights podporuje seznamy vyloučení & zahrnutí podle názvu metriky. Pokud například obdržíte metriky **kubedns** ve vašem clusteru, může to být stovky, které se ve výchozím nastavení vyřadí, ale máte největší pravděpodobně zájem jenom o určitou podmnožinu. Potvrďte, že jste zadali seznam metrik, které se mají vyřadit, nebo vyloučit jiné, kromě několika málo, než se uloží na svazek pro příjem dat. Je snadné povolit vyřazení a nepoužívat spoustu těchto metrik, které budou do faktury Log Analytics přidávat jenom další poplatky.

- Při vynechání poznámek pod, nezapomeňte filtrovat podle oboru názvů tak, abyste vyloučili vynechání metriky pod obory názvů, které nepoužíváte (například obor názvů pro **vývoj a testování** ).

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak zjistit, jaké náklady budou pravděpodobně založeny na nedávných vzorcích využití z dat shromážděných pomocí služby Container Insights, najdete v tématu [Správa využití a odhad nákladů](../logs/manage-cost-storage.md).