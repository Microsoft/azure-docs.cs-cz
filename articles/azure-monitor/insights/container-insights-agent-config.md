---
title: Konfigurace Azure Monitor pro shromažďování dat agenta kontejnerů | Microsoft Docs
description: Tento článek popisuje, jak můžete nakonfigurovat agenta Azure Monitor for Containers pro řízení kolekce protokolů stdout/stderr a proměnných prostředí.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/08/2019
ms.author: magoedte
ms.openlocfilehash: dfa823955cccba4ac7ec6859894a4562f0810d76
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2019
ms.locfileid: "72248756"
---
# <a name="configure-agent-data-collection-for-azure-monitor-for-containers"></a>Konfigurace shromažďování dat agenta pro Azure Monitor pro kontejnery

Azure Monitor pro kontejnery shromažďují z kontejneru kontejnerů stdout, stderr a proměnné prostředí z úloh kontejneru nasazených do spravovaných clusterů Kubernetes hostovaných ve službě Azure Kubernetes Service (AKS). Tento agent může také shromažďovat data časových řad (označované také jako metriky) z Prometheus pomocí kontejnerového agenta bez nutnosti nastavovat a spravovat server Prometheus a databázi. Nastavení shromažďování dat agenta můžete nakonfigurovat vytvořením vlastního ConfigMaps Kubernetes pro řízení tohoto prostředí. 

Tento článek ukazuje, jak vytvořit ConfigMap a nakonfigurovat shromažďování dat podle vašich požadavků.

>[!NOTE]
>Podpora pro Prometheus je ve verzi Public Preview v současnosti funkce.
>

## <a name="configmap-file-settings-overview"></a>Přehled nastavení souboru ConfigMap

K dispozici je soubor šablony ConfigMap, který umožňuje snadnou úpravu pomocí vlastního nastavení bez nutnosti vytvářet zcela nové. Než začnete, měli byste si projít dokumentaci k Kubernetes o [ConfigMaps](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/) a seznámit se s tím, jak vytvářet, konfigurovat a nasazovat ConfigMaps. To vám umožní filtrovat stderr a STDOUT na obor názvů nebo napříč celým clusterem a proměnné prostředí pro libovolný kontejner běžící v rámci všech lusků nebo uzlů v clusteru.

>[!IMPORTANT]
>Minimální verze agenta podporovaná pro shromažďování stdout, stderr a proměnných prostředí z úloh kontejneru je ciprod06142019 nebo novější. Minimální verze agenta podporovaná metrikami Prometheus je ciprod07092019 nebo novější. Chcete-li ověřit verzi agenta, na kartě **uzel** vyberte uzel a v podokně vlastností hodnotu poznámky pro vlastnost **značka image agenta** .  

### <a name="data-collection-settings"></a>Nastavení shromažďování dat

Níže jsou uvedené nastavení, které lze nakonfigurovat pro řízení shromažďování dat.

|Klíč |Data type |Hodnota |Popis |
|----|----------|------|------------|
|`schema-version` |Řetězec (rozlišuje velká a malá písmena) |V1 |Toto je verze schématu používaná agentem při analýze tohoto ConfigMap. Aktuálně podporovaná verze schématu je v1. Změna této hodnoty není podporována a při vyhodnocování ConfigMap bude odmítnuta.|
|`config-version` |Řetězec | | Podporuje schopnost sledovat tuto verzi konfiguračního souboru v systému správy zdrojů nebo v úložišti. Maximální povolený počet znaků je 10 a všechny ostatní znaky jsou zkráceny. |
|`[log_collection_settings.stdout] enabled =` |Logická hodnota | true nebo false | Tento ovládací prvek určuje, zda je povoleno shromažďování protokolů kontejnerů STDOUT. Pokud je nastavená hodnota `true` a pro kolekci protokolů stdout (nastavení `log_collection_settings.stdout.exclude_namespaces` níže nejsou žádné obory názvů), budou se protokoly stdout shromažďovat ze všech kontejnerů v rámci všech lusků nebo uzlů v clusteru. Pokud není zadán v ConfigMaps, výchozí hodnota je `enabled = true`. |
|`[log_collection_settings.stdout] exclude_namespaces =`|Řetězec | Pole oddělené čárkami |Pole oborů názvů Kubernetes, pro které se protokoly stdout nebudou shromažďovat Toto nastavení platí pouze v případě, že je parametr `log_collection_settings.stdout.enabled` nastaven na hodnotu `true`. Pokud není zadán v ConfigMap, výchozí hodnota je `exclude_namespaces = ["kube-system"]`.|
|`[log_collection_settings.stderr] enabled =` |Logická hodnota | true nebo false |Tyto ovládací prvky, pokud je povoleno shromažďování protokolů kontejneru stderr. Pokud je nastavena hodnota `true` a žádné obory názvů nejsou vyloučeny pro shromažďování protokolů stdout (nastavení `log_collection_settings.stderr.exclude_namespaces`), budou protokoly stderr shromažďovány ze všech kontejnerů v rámci všech lusků nebo uzlů v clusteru. Pokud není zadán v ConfigMaps, výchozí hodnota je `enabled = true`. |
|`[log_collection_settings.stderr] exclude_namespaces =` |Řetězec |Pole oddělené čárkami |Pole oborů názvů Kubernetes, pro které nebudou shromažďovány protokoly stderr Toto nastavení platí pouze v případě, že je parametr `log_collection_settings.stdout.enabled` nastaven na hodnotu `true`. Pokud není zadán v ConfigMap, výchozí hodnota je `exclude_namespaces = ["kube-system"]`. |
| `[log_collection_settings.env_var] enabled =` |Logická hodnota | true nebo false | Toto nastavení řídí kolekci proměnných prostředí ve všech luskech/uzlech v clusteru a ve výchozím nastavení `enabled = true`, pokud není zadáno v ConfigMaps. Pokud je kolekce proměnných prostředí globálně povolená, můžete ji pro konkrétní kontejner zakázat nastavením proměnné prostředí `AZMON_COLLECT_ENV` na **hodnotu false** buď pomocí nastavení souboru Dockerfile, nebo v [konfiguračním souboru pro](https://kubernetes.io/docs/tasks/inject-data-application/define-environment-variable-container/) příkaz pod **. ENV:** oddíl Pokud je kolekce proměnných prostředí globálně zakázaná, nemůžete povolit shromažďování pro konkrétní kontejner (to znamená, že jediné přepsání, které může být použito na úrovni kontejneru, je zakázat shromažďování, pokud je již povoleno globálně). |

### <a name="prometheus-scraping-settings"></a>Nastavení pro likvidační Prometheus

![Architektura monitorování kontejnerů pro Prometheus](./media/container-insights-agent-config/monitoring-kubernetes-architecture.png)

Azure Monitor for Containers poskytuje bezproblémové prostředí pro povolení shromažďování metrik Prometheus pomocí vícenásobného vyřazení pomocí následujících mechanismů, jak je znázorněno v následující tabulce. Metriky se shromažďují pomocí sady nastavení zadané v jednom souboru ConfigMap, což je stejný soubor, který se používá ke konfiguraci kolekce proměnných stdout, stderr a prostředí z úloh kontejneru. 

Aktivní likvidace metrik z Prometheus se provádí z jednoho ze dvou perspektiv:

* Adresa URL pro clustery v rámci clusteru a zjišťování cílů z uvedených koncových bodů služby, k8s služeb, jako jsou Kube-DNS a Kube – metriky a pod, poznámky specifické pro aplikaci. Metriky shromážděné v tomto kontextu budou definovány v části ConfigMap *[Prometheus data_collection_settings. cluster]* .
* Adresa URL v rámci uzlu-HTTP a zjišťují se cíle z uvedených koncových bodů služby. Metriky shromážděné v tomto kontextu budou definovány v části ConfigMap *[Prometheus_data_collection_settings. Node]* .

| Koncový bod | Rozsah | Příklad: |
|----------|-------|---------|
| Pod – Poznámka | Napříč clustery | anotac <br>`prometheus.io/scrape: "true"` <br>`prometheus.io/path: "/mymetrics"` <br>`prometheus.io/port: "8000"` <br>`prometheus.io/scheme: "http"` |
| Služba Kubernetes | Napříč clustery | `http://my-service-dns.my-namespace:9100/metrics` <br>`https://metrics-server.kube-system.svc.cluster.local/metrics` |
| Adresa URL/koncový bod | Pro jednotlivé uzly nebo pro clustery v rámci clusteru | `http://myurl:9101/metrics` |

Pokud je zadána adresa URL, Azure Monitor pro kontejnery vyřadí pouze koncový bod. Při zadání služby Kubernetes se název služby vyřeší se serverem DNS clusteru, aby získal IP adresu, a pak se vyhodnocená služba vyřadí.

|Rozsah | Klíč | Data type | Hodnota | Popis |
|------|-----|-----------|-------|-------------|
| Napříč clustery | | | | Zadejte jednu z následujících tří metod pro vyřazení koncových bodů pro metriky. |
| | `urls` | Řetězec | Pole oddělené čárkami | Koncový bod HTTP (buď zadaná IP adresa, nebo platná cesta URL) Například: `urls=[$NODE_IP/metrics]`. ($NODE _IP je specifický parametr Azure Monitor for Containers a dá se použít místo IP adresy uzlu. Musí být všechna velká.) |
| | `kubernetes_services` | Řetězec | Pole oddělené čárkami | Pole služeb Kubernetes pro vyřazení metrik z Kube-State-Metrics. Například `kubernetes_services = ["https://metrics-server.kube-system.svc.cluster.local/metrics", http://my-service-dns.my-namespace:9100/metrics]`.|
| | `monitor_kubernetes_pods` | Logická hodnota | true nebo false | Když nastavíte `true` v nastaveních na úrovni celého clusteru, Azure Monitor pro agenta kontejnerů vyřadí Kubernetes do celého clusteru pro následující poznámky Prometheus:<br> `prometheus.io/scrape:`<br> `prometheus.io/scheme:`<br> `prometheus.io/path:`<br> `prometheus.io/port:` |
| | `prometheus.io/scrape` | Logická hodnota | true nebo false | Povoluje vyřazení pod. `monitor_kubernetes_pods` musí být nastavené na `true`. |
| | `prometheus.io/scheme` | Řetězec | http nebo https | Výchozím nastavením je vyřazení přes protokol HTTP. V případě potřeby nastavte na `https`. | 
| | `prometheus.io/path` | Řetězec | Pole oddělené čárkami | Cesta prostředku HTTP, ze které se mají načíst metriky Pokud cesta k metrikám není `/metrics`, definujte ji pomocí této poznámky. |
| | `prometheus.io/port` | Řetězec | 9102 | Zadejte port, ze kterého se má vyřadit. Pokud není Port nastavený, použije se výchozí hodnota 9102. |
| Napříč uzly | `urls` | Řetězec | Pole oddělené čárkami | Koncový bod HTTP (buď zadaná IP adresa, nebo platná cesta URL) Například: `urls=[$NODE_IP/metrics]`. ($NODE _IP je specifický parametr Azure Monitor for Containers a dá se použít místo IP adresy uzlu. Musí být všechna velká.) |
| V rozsáhlých uzlech nebo v clusteru | `interval` | Řetězec | 60 s | Výchozí interval shromažďování je jedna minuta (60 sekund). Můžete upravit kolekci pro *[prometheus_data_collection_settings. Node]* a/nebo *[prometheus_data_collection_settings. cluster]* na časové jednotky, například s, m, h. |
| V rozsáhlých uzlech nebo v clusteru | `fieldpass`<br> `fielddrop`| Řetězec | Pole oddělené čárkami | Nastavením seznamu Povolit (`fieldpass`) a zakázat (`fielddrop`) můžete určit určité metriky, které mají být shromažďovány nebo nikoli z koncového bodu. Nejprve musíte nastavit seznam povolených. |

ConfigMaps je globální seznam a v agentovi může být použit pouze jeden ConfigMap. Nemůžete mít k dispozici další ConfigMaps pro kolekce.

## <a name="configure-and-deploy-configmaps"></a>Konfigurace a nasazení ConfigMaps

Provedením následujících kroků nakonfigurujete a nasadíte konfigurační soubor ConfigMap do clusteru.

1. [Stáhněte](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/Kubernetes/container-azm-ms-agentconfig.yaml) si soubor Template ConfigMap YAML a uložte ho jako Container-AZM-MS-agentconfig. yaml.  

2. Upravte soubor ConfigMap YAML s vlastními nastaveními pro shromažďování proměnných prostředí stdout, stderr a/nebo.

    - Pokud chcete vyloučit konkrétní obory názvů pro shromažďování protokolů stdout, nakonfigurujte klíč nebo hodnotu pomocí následujícího příkladu: `[log_collection_settings.stdout] enabled = true exclude_namespaces = ["my-namespace-1", "my-namespace-2"]`.
    
    - Chcete-li zakázat shromažďování proměnných prostředí pro určitý kontejner, nastavte klíč/hodnotu `[log_collection_settings.env_var] enabled = true` pro globální povolení kolekce proměnných a pak postupujte podle pokynů [zde](container-insights-manage-agent.md#how-to-disable-environment-variable-collection-on-a-container) , a dokončete konfiguraci pro konkrétní kontejner.
    
    - Pokud chcete zakázat shromažďování protokolů protokolu stderr na úrovni clusteru, nakonfigurujte klíč/hodnotu pomocí následujícího příkladu: `[log_collection_settings.stderr] enabled = false`.
    
3. Chcete-li konfigurovat shromažďování služby Kubernetes Services v clusteru, nakonfigurujte soubor ConfigMap pomocí následujícího příkladu.

    ```
    prometheus-data-collection-settings: |- 
    # Custom Prometheus metrics data collection settings
    [prometheus_data_collection_settings.cluster] 
    interval = "1m"  ## Valid time units are s, m, h.
    fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
    fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
    kubernetes_services = ["http://my-service-dns.my-namespace:9102/metrics"]
    ```

4. Pokud chcete nakonfigurovat vyřazení metrik Prometheus z konkrétní adresy URL v clusteru, nakonfigurujte soubor ConfigMap pomocí následujícího příkladu.

    ```
    prometheus-data-collection-settings: |- 
    # Custom Prometheus metrics data collection settings
    [prometheus_data_collection_settings.cluster] 
    interval = "1m"  ## Valid time units are s, m, h.
    fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
    fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
    urls = ["http://myurl:9101/metrics"] ## An array of urls to scrape metrics from
    ```

5. Pokud chcete nakonfigurovat vyřazení metrik Prometheus z DaemonSet agenta pro každý jednotlivý uzel v clusteru, nakonfigurujte následující v ConfigMap:
    
    ```
    prometheus-data-collection-settings: |- 
    # Custom Prometheus metrics data collection settings 
    [prometheus_data_collection_settings.node] 
    interval = "1m"  ## Valid time units are s, m, h. 
    urls = ["http://$NODE_IP:9103/metrics"] 
    fieldpass = ["metric_to_pass1", "metric_to_pass2"] 
    fielddrop = ["metric_to_drop"] 
    ```

    >[!NOTE]
    >$NODE _IP je konkrétní Azure Monitor parametr Containers a dá se použít místo IP adresy uzlu. Musí být všechna velká. 

6. Chcete-li nakonfigurovat vyřazení metrik Prometheus zadáním poznámky pod, proveďte následující kroky:

    1. V části ConfigMap zadejte následující:

        ```
         prometheus-data-collection-settings: |- 
         # Custom Prometheus metrics data collection settings
         [prometheus_data_collection_settings.cluster] 
         interval = "1m"  ## Valid time units are s, m, h
         monitor_kubernetes_pods = true 
        ```

    2. Pro poznámky pod zadejte následující konfiguraci:

        ```
         - prometheus.io/scrape:"true" #Enable scraping for this pod 
         - prometheus.io/scheme:"http:" #If the metrics endpoint is secured then you will need to set this to `https`, if not default ‘http’
         - prometheus.io/path:"/mymetrics" #If the metrics path is not /metrics, define it with this annotation. 
         - prometheus.io/port:"8000" #If port is not 9102 use this annotation
        ```

7. Vytvořte ConfigMap spuštěním následujícího příkazu kubectl: `kubectl apply -f <configmap_yaml_file.yaml>`.
    
    Příklad: `kubectl apply -f container-azm-ms-agentconfig.yaml`. 
    
    Dokončení změny konfigurace může trvat několik minut, než se projeví, a všechny omsagent v clusteru se restartují. Restartování je postupné restartování pro všechny omsagent lusky, ne pro všechna restartování ve stejnou dobu. Po dokončení restartů se zobrazí zpráva podobná následujícímu příkladu a obsahuje výsledek: `configmap "container-azm-ms-agentconfig" created`.

Chcete-li ověřit, zda byla konfigurace úspěšně použita, pomocí následujícího příkazu zkontrolujte protokoly z agenta pod: `kubectl logs omsagent-fdf58 -n=kube-system`. Pokud dojde k chybám konfigurace z omsagent lusků, ve výstupu se zobrazí chyby podobné následujícímu:

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

K dispozici jsou také chyby související s aplikováním změn konfigurace pro Prometheus.  Buď z protokolů z agenta pod, pomocí stejného příkazu `kubectl logs` nebo z živých protokolů. Live logs zobrazuje chyby podobné následujícímu:

```
2019-07-08T18:55:00Z E! [inputs.prometheus]: Error in plugin: error making HTTP request to http://invalidurl:1010/metrics: Get http://invalidurl:1010/metrics: dial tcp: lookup invalidurl on 10.0.0.10:53: no such host
```

Chyby zabraňují omsagent analýze souboru, což způsobí, že se restartuje a použije se výchozí konfigurace. Po opravě chyb v ConfigMap uložte soubor YAML a použijte aktualizovaný ConfigMaps spuštěním příkazu: `kubectl apply -f <configmap_yaml_file.yaml`.

## <a name="applying-updated-configmap"></a>Použití aktualizovaných ConfigMap

Pokud jste už nasadili ConfigMap do clusteru a chcete ji aktualizovat pomocí novější konfigurace, můžete upravit soubor ConfigMap, který jste dřív použili, a pak použít stejný příkaz jako předtím, `kubectl apply -f <configmap_yaml_file.yaml`.

Dokončení změny konfigurace může trvat několik minut, než se projeví, a všechny omsagent v clusteru se restartují. Restartování je postupné restartování pro všechny omsagent lusky, ne pro všechna restartování ve stejnou dobu. Po dokončení restartů se zobrazí zpráva podobná následujícímu příkladu a obsahuje výsledek: `configmap "container-azm-ms-agentconfig" updated`.

## <a name="verifying-schema-version"></a>Ověřuje se verze schématu.

Podporované verze schématu konfigurace jsou k dispozici jako Poznámka (verze schématu) na omsagent pod. Můžete je zobrazit pomocí následujícího příkazu kubectl: `kubectl describe pod omsagent-fdf58 -n=kube-system`

Ve výstupu se zobrazí zpráva podobná následující se schématy poznámky – verze:

```
    Name:           omsagent-fdf58
    Namespace:      kube-system
    Node:           aks-agentpool-95673144-0/10.240.0.4
    Start Time:     Mon, 10 Jun 2019 15:01:03 -0700
    Labels:         controller-revision-hash=589cc7785d
                    dsName=omsagent-ds
                    pod-template-generation=1
    Annotations:    agentVersion=1.10.0.1
                  dockerProviderVersion=5.0.0-0
                    schema-versions=v1 
```

## <a name="query-prometheus-metrics-data"></a>Data metrik Prometheus dotazů

Pokud chcete zobrazit Prometheus metriky, které jsou vyřazeny Azure Monitor, zadejte jako obor názvů "Prometheus". Tady je ukázkový dotaz pro zobrazení metrik Prometheus z oboru názvů Kubernetes `default`.

```
InsightsMetrics 
| where Namespace == "prometheus"
| extend tags=parse_json(Tags)
| summarize count() by Name
```

Data Prometheus lze také přímo dotazovat podle názvu.

```
InsightsMetrics 
| where Namespace == "prometheus"
| where Name contains "some_prometheus_metric"
```

## <a name="review-prometheus-data-usage"></a>Kontrola využití dat Prometheus

Pokud chcete zjistit objem příjmu každé metriky v GB za den, abyste zjistili, jestli je vysoká, je k dispozici následující dotaz.

```
InsightsMetrics 
| where Namespace == "prometheus"
| where TimeGenerated > ago(24h)
| summarize VolumeInGB = (sum(_BilledSize) / (1024 * 1024 * 1024)) by Name
| order by VolumeInGB desc
| render barchart
```
Ve výstupu se zobrazí výsledky podobné následujícímu:

![Výsledky dotazu do protokolu pro objem příjmu dat](./media/container-insights-agent-config/log-query-example-usage-03.png)

Pokud chcete odhadnout, co je velikost každé metriky v GB, je každý měsíc, abyste zjistili, jestli je objem dat přijatých v pracovním prostoru vysoký, je k dispozici následující dotaz.

```
InsightsMetrics 
| where Namespace contains "prometheus"
| where TimeGenerated > ago(24h)
| summarize EstimatedGBPer30dayMonth = (sum(_BilledSize) / (1024 * 1024 * 1024)) * 30 by Name
| order by EstimatedGBPer30dayMonth desc
| render barchart
```

Ve výstupu se zobrazí výsledky podobné následujícímu:

![Výsledky dotazu do protokolu pro objem příjmu dat](./media/container-insights-agent-config/log-query-example-usage-02.png)

Další informace o tom, jak monitorovat využití dat a analyzovat náklady, najdete v tématu [Správa využití a nákladů pomocí protokolů Azure monitor](../platform/manage-cost-storage.md).

## <a name="next-steps"></a>Další kroky

Azure Monitor pro kontejnery neobsahují předdefinovanou sadu výstrah. Přečtěte si téma [vytvoření výstrah výkonu s Azure monitor pro kontejnery](container-insights-alerts.md) , kde se dozvíte, jak vytvořit Doporučené výstrahy pro zajištění vysokého využití procesoru a paměti, aby podporovaly vaše DevOps nebo provozní procesy a postupy.

- Pokud chcete pokračovat v učení, jak používat Azure Monitor a monitorovat další aspekty clusteru AKS, přečtěte si téma [zobrazení stavu služby Azure Kubernetes](container-insights-analyze.md).

- Podívejte se na [příklady dotazů protokolu](container-insights-log-search.md#search-logs-to-analyze-data) , kde najdete předdefinované dotazy a příklady pro vyhodnocení nebo přizpůsobení výstrah, vizualizace a analýzy clusterů.
