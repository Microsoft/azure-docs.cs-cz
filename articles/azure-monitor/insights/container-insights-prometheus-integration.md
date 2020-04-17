---
title: Konfigurace Azure Monitor u kontejnerů Prometheus Integrace | Dokumenty společnosti Microsoft
description: Tento článek popisuje, jak můžete nakonfigurovat Azure Monitor pro kontejnery agent a škrábání metriky z Prometheus s clusteru Kubernetes.
ms.topic: conceptual
ms.date: 04/16/2020
ms.openlocfilehash: 7fcf52cceb69834f68f8e4ce7a2674972a6430fd
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537368"
---
# <a name="configure-scraping-of-prometheus-metrics-with-azure-monitor-for-containers"></a>Konfigurace škrábání metrik Prometheus pomocí Azure Monitoru pro kontejnery

[Prometheus](https://prometheus.io/) je populární open source metrické monitorování řešení a je součástí [Cloud Native Compute Foundation](https://www.cncf.io/). Azure Monitor pro kontejnery poskytuje bezproblémové připojení prostředí pro shromažďování metrik Prometheus. Obvykle, chcete-li použít Prometheus, musíte nastavit a spravovat server Prometheus s úložištěm. Díky integraci s Azure Monitor, server Prometheus není vyžadováno. Stačí jen vystavit koncový bod metriky Prometheus prostřednictvím vývozců nebo podů (aplikace) a kontejnerizovaného agenta pro Azure Monitor pro kontejnery můžete scrape metriky pro vás. 

![Architektura monitorování kontejnerů pro Prometheus](./media/container-insights-prometheus-integration/monitoring-kubernetes-architecture.png)

>[!NOTE]
>Minimální verze agenta podporovaná pro škrábání metrikY Prometheus je ciprod07092019 nebo novější a `KubeMonAgentEvents` verze agenta podporovaná pro psaní chyb konfigurace a agentů v tabulce je ciprod10112019. Další informace o verzích agenta a o tom, co je součástí každé verze, naleznete v [poznámkách k verzi agenta](https://github.com/microsoft/Docker-Provider/tree/ci_feature_prod). Chcete-li ověřit verzi agenta, vyberte na kartě **Uzel** uzel a v podokně vlastností hodnotu poznámky **vlastnosti Číslo obrázku agenta.**

Škrábání metrik Prometheus je podporováno clustery Kubernetes hostovanými na:

- Azure Kubernetes Service (AKS)
- Azure Stack nebo místní
- Azure Red Hat OpenShift

>[!NOTE]
>Pro Azure Red Hat OpenShift šablona ConfigMap soubor se vytvoří v oboru názvů *openshift-azure protokolování.* Není nakonfigurován tak, aby aktivně scrape metriky nebo shromažďování dat z agenta.
>

## <a name="azure-red-hat-openshift-prerequisites"></a>Předpoklady Prosazuje Red Hat OpenShift

Než začnete, potvrďte, že jste členem role správce clusteru clusteru zákazníka vašeho clusteru Azure Red Hat OpenShift a nakonfigurujte kontejnerizovaného agenta a nastavení scrapingu Prometheus. Chcete-li ověřit, zda jste členem *skupiny osa-customer-admins,* spusťte následující příkaz:

``` bash
  oc get groups
```

Výstup se bude podobat následujícímu:

``` bash
NAME                  USERS
osa-customer-admins   <your-user-account>@<your-tenant-name>.onmicrosoft.com
```

Pokud jste členem *skupiny osa-customer-admins,* měli byste `container-azm-ms-agentconfig` být schopni uvést ConfigMap pomocí následujícího příkazu:

``` bash
oc get configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

Výstup se bude podobat následujícímu:

``` bash
NAME                           DATA      AGE
container-azm-ms-agentconfig   4         56m
```

### <a name="prometheus-scraping-settings"></a>Nastavení škrábání Prometheus

Aktivní škrábání metrik z Prometheus se provádí z jedné ze dvou perspektiv:

* Adresa URL celého clusteru – adresa URL HTTP a zjišťuje cíle z uvedených koncových bodů služby. Například k8s služby, jako je kube-dns a kube-state-metrics a pod poznámky specifické pro aplikaci. Metriky shromážděné v tomto kontextu budou definovány v části ConfigMap *[Prometheus data_collection_settings.cluster]*.
* Adresa URL pro celý uzel – http a zjišťují cíle z uvedených koncových bodů služby. Metriky shromážděné v tomto kontextu budou definovány v části ConfigMap *[Prometheus_data_collection_settings.node]*.

| Koncový bod | Rozsah | Příklad |
|----------|-------|---------|
| Anotace podu | Celý cluster | Poznámky: <br>`prometheus.io/scrape: "true"` <br>`prometheus.io/path: "/mymetrics"` <br>`prometheus.io/port: "8000"` <br>`prometheus.io/scheme: "http"` |
| Kubernetes servis | Celý cluster | `http://my-service-dns.my-namespace:9100/metrics` <br>`https://metrics-server.kube-system.svc.cluster.local/metrics` |
| adresa URL/koncový bod | Uzel a/nebo celý cluster | `http://myurl:9101/metrics` |

Když je zadána adresa URL Azure Monitor pro kontejnery pouze scrapes koncový bod. Je-li zadána služba Kubernetes, je název služby vyřešen serverem DNS clusteru, aby se získala adresa IP, a pak je vyřešená služba vyřazena.

|Rozsah | Klíč | Datový typ | Hodnota | Popis |
|------|-----|-----------|-------|-------------|
| Celý cluster | | | | Zadejte některou z následujících tří metod pro scrape koncové body pro metriky. |
| | `urls` | Řetězec | Pole oddělené čárkami | HTTP koncový bod (zadána adresa IP nebo platná cesta URL). Například: `urls=[$NODE_IP/metrics]`. ($NODE_IP je specifický parametr Azure Monitor pro kontejnery a lze použít místo IP adresy uzlu. Musí být všechna velká.) |
| | `kubernetes_services` | Řetězec | Pole oddělené čárkami | Pole služeb Kubernetes pro škrábání metriky z kube-state-metriky. Například`kubernetes_services = ["https://metrics-server.kube-system.svc.cluster.local/metrics",http://my-service-dns.my-namespace:9100/metrics]`.|
| | `monitor_kubernetes_pods` | Logická hodnota | true nebo false | Pokud je `true` nastavena na v nastavení celého clusteru, Azure Monitor pro kontejnery agent bude škrábání Kubernetes pods v celém clusteru pro následující poznámky Prometheus:<br> `prometheus.io/scrape:`<br> `prometheus.io/scheme:`<br> `prometheus.io/path:`<br> `prometheus.io/port:` |
| | `prometheus.io/scrape` | Logická hodnota | true nebo false | Umožňuje škrábání modulu. `monitor_kubernetes_pods`musí být `true`nastavena na . |
| | `prometheus.io/scheme` | Řetězec | http nebo https | Výchozí je vyřazení přes protokol HTTP. V případě potřeby `https`nastavte na . | 
| | `prometheus.io/path` | Řetězec | Pole oddělené čárkami | Cesta k prostředku HTTP, na kterém chcete načíst metriky. Pokud cesta metriky `/metrics`není , definujte ji pomocí této poznámky. |
| | `prometheus.io/port` | Řetězec | 9102 | Zadejte port, ze který chcete seškrábat. Pokud port není nastaven, bude výchozí 9102. |
| | `monitor_kubernetes_pods_namespaces` | Řetězec | Pole oddělené čárkami | Seznam povolených oborů názvů pro škrábání metriky z podů Kubernetes.<br> Například `monitor_kubernetes_pods_namespaces = ["default1", "default2", "default3"]`. |
| Celý uzel | `urls` | Řetězec | Pole oddělené čárkami | HTTP koncový bod (zadána adresa IP nebo platná cesta URL). Například: `urls=[$NODE_IP/metrics]`. ($NODE_IP je specifický parametr Azure Monitor pro kontejnery a lze použít místo IP adresy uzlu. Musí být všechna velká.) |
| Uzel široký nebo celý cluster | `interval` | Řetězec | 60s | Výchozí interval kolekce je jedna minuta (60 sekund). Kolekci můžete upravit pro *[prometheus_data_collection_settings.node]* a/nebo *[prometheus_data_collection_settings.cluster]* na časové jednotky, jako jsou s, m, h. |
| Uzel široký nebo celý cluster | `fieldpass`<br> `fielddrop`| Řetězec | Pole oddělené čárkami | Můžete určit určité metriky, které mají být shromažďovány nebo`fieldpass`ne z koncového bodu nastavením povolit ( ) a zakázat (`fielddrop`) výpis. Nejprve je nutné nastavit seznam povolených položek. |

ConfigMaps je globální seznam a může být pouze jeden ConfigMap použít na agenta. Nelze mít jiný ConfigMaps převažují nad kolekce.

## <a name="configure-and-deploy-configmaps"></a>Konfigurace a nasazení map ConfigMaps

Chcete-li nakonfigurovat konfigurační soubor ConfigMap pro clustery Kubernetes, proveďte následující kroky.

1. [Stáhněte si](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/Kubernetes/container-azm-ms-agentconfig.yaml) šablonu ConfigMap yaml soubor a uložte jej jako kontejner-azm-ms-agentconfig.yaml.

   >[!NOTE]
   >Tento krok není vyžadován při práci s Azure Red Hat OpenShift, protože šablona ConfigMap již v clusteru existuje.

2. Upravte soubor Yaml ConfigMap pomocí vlastního nastavení, abyste se škrábali na metrikách Prometheus. Pokud upravujete soubor Yaml ConfigMap pro Azure Red Hat OpenShift, nejprve spusťte příkaz `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` a otevřete soubor v textovém editoru.

    >[!NOTE]
    >Následující poznámky `openshift.io/reconcile-protect: "true"` musí být přidány pod metadata *kontejneru azm-ms-agentconfig* ConfigMap zabránit odsouhlasení. 
    >```
    >metadata:
    >   annotations:
    >       openshift.io/reconcile-protect: "true"
    >```

    - Chcete-li shromažďovat služby Kubernetes v celém clusteru, nakonfigurujte soubor ConfigMap pomocí následujícího příkladu.

        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings
        [prometheus_data_collection_settings.cluster] 
        interval = "1m"  ## Valid time units are s, m, h.
        fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
        fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
        kubernetes_services = ["http://my-service-dns.my-namespace:9102/metrics"]
        ```

    - Chcete-li nakonfigurovat škrábání metrik Prometheus z určité adresy URL v celém clusteru, nakonfigurujte soubor ConfigMap pomocí následujícího příkladu.

        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings
        [prometheus_data_collection_settings.cluster] 
        interval = "1m"  ## Valid time units are s, m, h.
        fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
        fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
        urls = ["http://myurl:9101/metrics"] ## An array of urls to scrape metrics from
        ```

    - Chcete-li nakonfigurovat škrábání metrik Prometheus z daemonset agenta pro každý jednotlivý uzel v clusteru, nakonfigurujte následující v ConfigMap:
    
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
        >$NODE_IP je specifický parametr Azure Monitor pro kontejnery a dá se použít místo IP adresy uzlu. Musí to být velká písmena. 

    - Chcete-li nakonfigurovat škrábání metrik Prometheus zadáním poznámky podu, proveďte následující kroky:

       1. V konfigurační mapě zadejte následující:

            ```
            prometheus-data-collection-settings: |- 
            # Custom Prometheus metrics data collection settings
            [prometheus_data_collection_settings.cluster] 
            interval = "1m"  ## Valid time units are s, m, h
            monitor_kubernetes_pods = true 
            ```

       2. Pro poznámky podu zadejte následující konfiguraci:

           ```
           - prometheus.io/scrape:"true" #Enable scraping for this pod 
           - prometheus.io/scheme:"http:" #If the metrics endpoint is secured then you will need to set this to `https`, if not default ‘http’
           - prometheus.io/path:"/mymetrics" #If the metrics path is not /metrics, define it with this annotation. 
           - prometheus.io/port:"8000" #If port is not 9102 use this annotation
           ```
    
          Pokud chcete omezit monitorování na určité obory názvů pro pody, které mají poznámky, například `monitor_kubernetes_pod` zahrnout `true` pouze pody vyhrazené pro produkční `monitor_kubernetes_pods_namespaces` úlohy, nastavte na v ConfigMap a přidejte filtr oboru názvů určující obory názvů, ze kterých chcete získat škrábání. Například `monitor_kubernetes_pods_namespaces = ["default1", "default2", "default3"]`.

3. Pro jiné clustery než Azure Red Hat OpenShift spusťte následující příkaz kubectl: `kubectl apply -f <configmap_yaml_file.yaml>`.
    
    Příklad: `kubectl apply -f container-azm-ms-agentconfig.yaml`. 

    Pro Azure Red Hat OpenShift uložte změny v editoru.

Změna konfigurace může trvat několik minut před projevem a všechny omsagent pody v clusteru se restartuje. Restartování je postupné restartování pro všechny pody omsagent, ne všechny restartovat současně. Po dokončení restartování se zobrazí zpráva podobná následující a obsahuje výsledek: `configmap "container-azm-ms-agentconfig" created`.

Aktualizovaný configMap pro Azure Red Hat OpenShift můžete `oc describe configmaps container-azm-ms-agentconfig -n openshift-azure-logging`zobrazit spuštěním příkazu . 

## <a name="applying-updated-configmap"></a>Použití aktualizované hospo-

Pokud jste již do clusteru nasadili configmap a chcete ji aktualizovat novější konfigurací, můžete upravit dříve používaný soubor ConfigMap a použít je pomocí stejných příkazů jako dříve.

Pro kubernetes clustery než Azure Red Hat `kubectl apply -f <configmap_yaml_file.yaml`OpenShift spusťte příkaz . 

V clusteru Azure Red Hat OpenShift spusťte příkaz a `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` otevřete soubor ve výchozím editoru, který chcete upravit a pak ho uložit.

Změna konfigurace může trvat několik minut před projevem a všechny omsagent pody v clusteru se restartuje. Restartování je postupné restartování pro všechny pody omsagent, ne všechny restartovat současně. Po dokončení restartování se zobrazí zpráva podobná následující a obsahuje výsledek: `configmap "container-azm-ms-agentconfig" updated`.

## <a name="verify-configuration"></a>Ověřit konfiguraci

Chcete-li ověřit, zda byla konfigurace úspěšně použita v clusteru, zkontrolujte `kubectl logs omsagent-fdf58 -n=kube-system`protokoly z modulu agenta pomocí následujícího příkazu: . 

>[!NOTE]
>Tento příkaz se nevztahuje na cluster Azure Red Hat OpenShift.
> 

Pokud jsou chyby konfigurace z omsagent pods, výstup se zobrazí chyby podobné následující:

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

Chyby související s použitím změn konfigurace jsou také k dispozici ke kontrole. Následující možnosti jsou k dispozici pro provedení dalších řešení potíží se změnami konfigurace a škrábáním metrik Prometheus:

- Z protokolů pod agenta `kubectl logs` pomocí stejného příkazu 
    >[!NOTE]
    >Tento příkaz se nevztahuje na cluster Azure Red Hat OpenShift.
    > 

- Z živých dat (náhled). Protokoly živých dat (náhled) zobrazují chyby podobné následujícímu:

    ```
    2019-07-08T18:55:00Z E! [inputs.prometheus]: Error in plugin: error making HTTP request to http://invalidurl:1010/metrics: Get http://invalidurl:1010/metrics: dial tcp: lookup invalidurl on 10.0.0.10:53: no such host
    ```

- Z tabulky **KubeMonAgentEvents** v pracovním prostoru Analýzy protokolů. Data jsou odesílána každou hodinu s *varovnou* závažností chyby při škrábání a *závažností chyb* pro chyby konfigurace. Pokud nejsou žádné chyby, položka v tabulce bude mít data s *informacemi o*závažnosti , která nehlásí žádné chyby. **Vlastnost Tags** obsahuje další informace o pod a id kontejneru, na kterém došlo k chybě a také první výskyt, poslední výskyt a počet za poslední hodinu.

- Pro Azure Red Hat OpenShift zkontrolujte protokoly omsagent hledáním **tabulka ContainerLog** ověřit, jestli je povolena kolekce protokolu openshift-azure-logging.

Chyby brání omsagent v analýzě souboru, přimět jej restartovat a použít výchozí konfiguraci. Po opravě chyby v ConfigMap na clusterech než Azure Red Hat OpenShift, uložte soubor yaml a `kubectl apply -f <configmap_yaml_file.yaml`použít aktualizované ConfigMaps spuštěním příkazu: . 

V případě Azure Red Hat OpenShift upravte a uložte `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging`aktualizované configmaps spuštěním příkazu: .

## <a name="query-prometheus-metrics-data"></a>Dotaz Prometheus metriky data

Chcete-li zobrazit metriky prometheus seškrábnuté službou Azure Monitor a všechny chyby konfigurace/škrábání hlášené agentem, zkontrolujte [data metrik Query Prometheus](container-insights-log-search.md#query-prometheus-metrics-data) a [chyby konfigurace nebo škrábání dotazu](container-insights-log-search.md#query-config-or-scraping-errors).

## <a name="view-prometheus-metrics-in-grafana"></a>Zobrazit metriky Prometheus v Grafaně

Azure Monitor pro kontejnery podporuje zobrazení metriky uložené v pracovním prostoru Log Analytics v řídicích panelech Grafana. Poskytli jsme šablonu, kterou si můžete stáhnout z [úložiště řídicích panelů](https://grafana.com/grafana/dashboards?dataSource=grafana-azure-monitor-datasource&category=docker) grafany, abyste mohli začít a odkazovat, abyste se naučili, jak zadat další data ze sledovaných clusterů, abyste je mohli vizualizovat ve vlastních řídicích panelech Grafana. 

## <a name="review-prometheus-data-usage"></a>Kontrola využití dat společnosti Prometheus

Chcete-li identifikovat objem ingestování každé velikosti metriky v GB za den, abyste zjistili, zda je vysoká, je k dispozici následující dotaz.

```
InsightsMetrics 
| where Namespace == "prometheus"
| where TimeGenerated > ago(24h)
| summarize VolumeInGB = (sum(_BilledSize) / (1024 * 1024 * 1024)) by Name
| order by VolumeInGB desc
| render barchart
```
Výstup zobrazí výsledky podobné následujícímu:

![Protokolovat výsledky dotazu na svazek při požití dat](./media/container-insights-prometheus-integration/log-query-example-usage-03.png)

Chcete-li odhadnout, jaká je velikost jednotlivých metrik v GB za měsíc, abyste zjistili, zda je objem přijatých dat přijatých v pracovním prostoru vysoký, je k dispozici následující dotaz.

```
InsightsMetrics 
| where Namespace contains "prometheus"
| where TimeGenerated > ago(24h)
| summarize EstimatedGBPer30dayMonth = (sum(_BilledSize) / (1024 * 1024 * 1024)) * 30 by Name
| order by EstimatedGBPer30dayMonth desc
| render barchart
```

Výstup zobrazí výsledky podobné následujícímu:

![Protokolovat výsledky dotazu na svazek při požití dat](./media/container-insights-prometheus-integration/log-query-example-usage-02.png)

Další informace o tom, jak sledovat využití dat a analyzovat náklady, jsou k dispozici v [aplikaci Správa využití a nákladů pomocí protokolů monitorování Azure](../platform/manage-cost-storage.md).

## <a name="next-steps"></a>Další kroky

Další informace o konfiguraci nastavení kolekce agenta pro stdout, stderr a proměnné prostředí z úloh kontejneru [zde](container-insights-agent-config.md). 
