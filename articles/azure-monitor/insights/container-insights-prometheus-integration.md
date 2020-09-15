---
title: Konfigurace Azure Monitor pro kontejnery integrace Prometheus | Microsoft Docs
description: Tento článek popisuje, jak můžete nakonfigurovat agenta Azure Monitor for Containers, aby vyodpadí metriky z Prometheus s clusterem Kubernetes.
ms.topic: conceptual
ms.date: 04/22/2020
ms.openlocfilehash: 8c83d962a31150b31f5883150a2f7bd8d4b49183
ms.sourcegitcommit: 1fe5127fb5c3f43761f479078251242ae5688386
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2020
ms.locfileid: "90069420"
---
# <a name="configure-scraping-of-prometheus-metrics-with-azure-monitor-for-containers"></a>Konfigurace získávání metrik Prometheus pomocí služby Azure Monitor pro kontejnery

[Prometheus](https://prometheus.io/) je oblíbený open source řešení pro monitorování metrik a je součástí [cloudového nativního výpočetního základu](https://www.cncf.io/). Azure Monitor for Containers poskytuje bezproblémové prostředí pro připojování ke shromažďování metrik Prometheus. Aby bylo možné používat Prometheus, je obvykle potřeba nastavit a spravovat server Prometheus s úložištěm. Integrací s Azure Monitor není třeba server Prometheus. Potřebujete jenom vystavit koncový bod Prometheus metriky prostřednictvím vývozců nebo lusků (aplikace) a kontejnerový Agent pro Azure Monitor pro kontejnery může metriky vyřadit. 

![Architektura monitorování kontejnerů pro Prometheus](./media/container-insights-prometheus-integration/monitoring-kubernetes-architecture.png)

>[!NOTE]
>Minimální verze agenta podporovaná metrikami Prometheus je ciprod07092019 nebo novější a verze agenta podporovaná pro psaní konfigurace a chyb agenta v `KubeMonAgentEvents` tabulce je ciprod10112019. Pro Azure Red Hat OpenShift a Red Hat OpenShift v4, verze agenta ciprod04162020 nebo vyšší. 
>
>Další informace o verzích agenta a o tom, co je součástí každé vydané verze, najdete v tématu [poznámky k verzi agenta](https://github.com/microsoft/Docker-Provider/tree/ci_feature_prod). 
>Chcete-li ověřit verzi agenta, na kartě **uzel** vyberte uzel a v podokně vlastností hodnotu poznámky pro vlastnost **značka image agenta** .

Vyřazení metrik Prometheus se podporuje u clusterů Kubernetes hostovaných na:

- Azure Kubernetes Service (AKS)
- Azure Stack nebo místní
- Azure Red Hat OpenShift verze 3. x
- Azure Red Hat OpenShift a Red Hat OpenShift verze 4. x

### <a name="prometheus-scraping-settings"></a>Nastavení pro likvidační Prometheus

Aktivní likvidace metrik z Prometheus se provádí z jednoho ze dvou perspektiv:

* Adresa URL pro clustery v rámci clusteru a zjišťování cílů z uvedených koncových bodů služby. Například služby k8s Services, jako jsou Kube-DNS a Kube – metriky a pod, jsou specifické pro aplikaci. Metriky shromážděné v tomto kontextu budou definovány v části ConfigMap *[Prometheus data_collection_settings. cluster]*.
* Adresa URL v rámci uzlu-HTTP a zjišťují se cíle z uvedených koncových bodů služby. Metriky shromážděné v tomto kontextu budou definovány v části ConfigMap *[Prometheus_data_collection_settings. Node]*.

| Koncový bod | Obor | Příklad |
|----------|-------|---------|
| Pod – Poznámka | Napříč clustery | anotac <br>`prometheus.io/scrape: "true"` <br>`prometheus.io/path: "/mymetrics"` <br>`prometheus.io/port: "8000"` <br>`prometheus.io/scheme: "http"` |
| Služba Kubernetes | Napříč clustery | `http://my-service-dns.my-namespace:9100/metrics` <br>`https://metrics-server.kube-system.svc.cluster.local/metrics` |
| Adresa URL/koncový bod | Pro jednotlivé uzly nebo pro clustery v rámci clusteru | `http://myurl:9101/metrics` |

Pokud je zadána adresa URL, Azure Monitor pro kontejnery vyřadí pouze koncový bod. Při zadání služby Kubernetes se název služby vyřeší se serverem DNS clusteru, aby získal IP adresu, a pak se vyhodnocená služba vyřadí.

|Obor | Klíč | Datový typ | Hodnota | Popis |
|------|-----|-----------|-------|-------------|
| Napříč clustery | | | | Zadejte jednu z následujících tří metod pro vyřazení koncových bodů pro metriky. |
| | `urls` | Řetězec | Pole oddělené čárkami | Koncový bod HTTP (buď zadaná IP adresa, nebo platná cesta URL) Například: `urls=[$NODE_IP/metrics]`. ($NODE _IP je konkrétní Azure Monitor pro parametr Containers a dá se použít místo IP adresy uzlu. Musí být všechna velká.) |
| | `kubernetes_services` | Řetězec | Pole oddělené čárkami | Pole služeb Kubernetes pro vyřazení metrik z Kube-State-Metrics. Příklad: `kubernetes_services = ["https://metrics-server.kube-system.svc.cluster.local/metrics",http://my-service-dns.my-namespace:9100/metrics]`.|
| | `monitor_kubernetes_pods` | Logická hodnota | true nebo false | Když se nastaví na `true` nastavení v rámci celého clusteru, Azure monitor pro agenty kontejnerů vyřadí v celém clusteru Kubernetes lusky pro následující poznámky Prometheus:<br> `prometheus.io/scrape:`<br> `prometheus.io/scheme:`<br> `prometheus.io/path:`<br> `prometheus.io/port:` |
| | `prometheus.io/scrape` | Logická hodnota | true nebo false | Povoluje vyřazení pod. `monitor_kubernetes_pods` musí být nastaven na hodnotu `true` . |
| | `prometheus.io/scheme` | Řetězec | http nebo https | Výchozím nastavením je vyřazení přes protokol HTTP. V případě potřeby nastavte na `https` . | 
| | `prometheus.io/path` | Řetězec | Pole oddělené čárkami | Cesta prostředku HTTP, ze které se mají načíst metriky Pokud cesta metriky není `/metrics` , definujte ji pomocí této poznámky. |
| | `prometheus.io/port` | Řetězec | 9102 | Zadejte port, ze kterého se má vyřadit. Pokud není Port nastavený, použije se výchozí hodnota 9102. |
| | `monitor_kubernetes_pods_namespaces` | Řetězec | Pole oddělené čárkami | Seznam povolených oborů názvů, ze kterých se mají vyřadit metriky z Kubernetes lusků<br> Například `monitor_kubernetes_pods_namespaces = ["default1", "default2", "default3"]`. |
| Napříč uzly | `urls` | Řetězec | Pole oddělené čárkami | Koncový bod HTTP (buď zadaná IP adresa, nebo platná cesta URL) Například: `urls=[$NODE_IP/metrics]`. ($NODE _IP je konkrétní Azure Monitor pro parametr Containers a dá se použít místo IP adresy uzlu. Musí být všechna velká.) |
| V rozsáhlých uzlech nebo v clusteru | `interval` | Řetězec | 60 s | Výchozí interval shromažďování je jedna minuta (60 sekund). Můžete upravit kolekci pro *[prometheus_data_collection_settings. Node]* a/nebo *[prometheus_data_collection_settings. cluster]* na časové jednotky, například s, m, h. |
| V rozsáhlých uzlech nebo v clusteru | `fieldpass`<br> `fielddrop`| Řetězec | Pole oddělené čárkami | Nastavením seznamu Povolit ( `fieldpass` ) a zakázat () můžete určit určité metriky, které mají být shromažďovány nebo nikoli z koncového bodu `fielddrop` . Nejprve musíte nastavit seznam povolených. |

ConfigMaps je globální seznam a v agentovi může být použit pouze jeden ConfigMap. Nemůžete mít k dispozici další ConfigMaps pro kolekce.

## <a name="configure-and-deploy-configmaps"></a>Konfigurace a nasazení ConfigMaps

Proveďte následující kroky ke konfiguraci konfiguračního souboru ConfigMap pro následující clustery:

* Azure Kubernetes Service (AKS)
* Azure Stack nebo místní
* Azure Red Hat OpenShift verze 4. x a Red Hat OpenShift verze 4. x

1. [Stáhněte](https://aka.ms/container-azm-ms-agentconfig) si soubor Template ConfigMap YAML a uložte ho jako Container-AZM-MS-agentconfig. yaml.

   >[!NOTE]
   >Tento krok není nutný při práci s Azure Red Hat OpenShift, protože šablona ConfigMap už v clusteru existuje.

2. Upravte soubor ConfigMap YAML s vlastními úpravami, abyste mohli vyřadit metriky Prometheus.

    >[!NOTE]
    >Pokud upravujete soubor ConfigMap YAML pro Azure Red Hat OpenShift, nejprve spusťte příkaz `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` a otevřete soubor v textovém editoru.

    >[!NOTE]
    >`openshift.io/reconcile-protect: "true"`Aby se zabránilo odsouhlasení, je nutné přidat následující poznámku pod metadata *kontejneru-AZM-MS-agentconfig* ConfigMap. 
    >```
    >metadata:
    >   annotations:
    >       openshift.io/reconcile-protect: "true"
    >```

    - Pokud chcete shromáždit cluster Kubernetes Services v rámci clusteru, nakonfigurujte soubor ConfigMap pomocí následujícího příkladu.

        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings
        [prometheus_data_collection_settings.cluster] 
        interval = "1m"  ## Valid time units are s, m, h.
        fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
        fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
        kubernetes_services = ["http://my-service-dns.my-namespace:9102/metrics"]
        ```

    - Pokud chcete nakonfigurovat vyřazení metrik Prometheus z konkrétní adresy URL v clusteru, nakonfigurujte soubor ConfigMap pomocí následujícího příkladu.

        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings
        [prometheus_data_collection_settings.cluster] 
        interval = "1m"  ## Valid time units are s, m, h.
        fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
        fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
        urls = ["http://myurl:9101/metrics"] ## An array of urls to scrape metrics from
        ```

    - Pokud chcete nakonfigurovat vyřazení metrik Prometheus z DaemonSet agenta pro každý jednotlivý uzel v clusteru, nakonfigurujte následující v ConfigMap:
    
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
        >$NODE _IP je konkrétní Azure Monitor pro parametr Containers a dá se použít místo IP adresy uzlu. Musí se jednat o všechna velká písmena. 

    - Chcete-li nakonfigurovat vyřazení metrik Prometheus zadáním poznámky pod, proveďte následující kroky:

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
           - prometheus.io/scheme:"http" #If the metrics endpoint is secured then you will need to set this to `https`, if not default ‘http’
           - prometheus.io/path:"/mymetrics" #If the metrics path is not /metrics, define it with this annotation. 
           - prometheus.io/port:"8000" #If port is not 9102 use this annotation
           ```
    
          Chcete-li omezit monitorování na konkrétní obory názvů pro lusky, které mají poznámky, například pouze v případě produkčních úloh, které jsou vyhrazeny pro produkční úlohy, nastavte na hodnotu `monitor_kubernetes_pod` `true` v ConfigMap a přidejte filtr oboru názvů `monitor_kubernetes_pods_namespaces` určující obory názvů, ze kterých se mají vyřadit. Například `monitor_kubernetes_pods_namespaces = ["default1", "default2", "default3"]`.

3. Spusťte následující příkaz kubectl: `kubectl apply -f <configmap_yaml_file.yaml>` .
    
    Příklad: `kubectl apply -f container-azm-ms-agentconfig.yaml`. 

Dokončení změny konfigurace může trvat několik minut, než se projeví, a všechny omsagent v clusteru se restartují. Restartování je postupné restartování pro všechny omsagent lusky, ne pro všechna restartování ve stejnou dobu. Po dokončení restartů se zobrazí zpráva podobná následujícímu příkladu a obsahuje výsledek: `configmap "container-azm-ms-agentconfig" created` .

## <a name="configure-and-deploy-configmaps---azure-red-hat-openshift-v3"></a>Konfigurace a nasazení ConfigMaps – Azure Red Hat OpenShift V3

Tato část obsahuje požadavky a kroky pro úspěšnou konfiguraci konfiguračního souboru ConfigMap pro cluster Azure Red Hat OpenShift v3. x.

>[!NOTE]
>V případě Azure Red Hat OpenShift v3. x se vytvoří soubor šablony ConfigMap v oboru názvů *OpenShift-Azure-Logging* . Není nastavené na aktivní vyřazení metrik nebo shromažďování dat z agenta.

### <a name="prerequisites"></a>Předpoklady

Než začnete, potvrďte, že jste členem role Správce clusteru pro zákazníky v clusteru Azure Red Hat OpenShift ke konfiguraci kontejnerového nastavení s kontejnery a Prometheus. Pokud chcete ověřit, že jste členem skupiny *osa-Customer-Admins* , spusťte následující příkaz:

``` bash
  oc get groups
```

Výstup bude vypadat přibližně takto:

``` bash
NAME                  USERS
osa-customer-admins   <your-user-account>@<your-tenant-name>.onmicrosoft.com
```

Pokud jste členem skupiny *osa-Customers-Admins* , měli byste být schopni zobrazit seznam `container-azm-ms-agentconfig` ConfigMap pomocí následujícího příkazu:

``` bash
oc get configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

Výstup bude vypadat přibližně takto:

``` bash
NAME                           DATA      AGE
container-azm-ms-agentconfig   4         56m
```

### <a name="enable-monitoring"></a>Povolení monitorování

Pomocí následujících kroků proveďte konfiguraci konfiguračního souboru ConfigMap pro cluster Azure Red Hat OpenShift v3. x.

1. Upravte soubor ConfigMap YAML s vlastními úpravami, abyste mohli vyřadit metriky Prometheus. Šablona ConfigMap již existuje v clusteru Red Hat OpenShift v3. Spuštěním příkazu `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` otevřete soubor v textovém editoru.

    >[!NOTE]
    >`openshift.io/reconcile-protect: "true"`Aby se zabránilo odsouhlasení, je nutné přidat následující poznámku pod metadata *kontejneru-AZM-MS-agentconfig* ConfigMap. 
    >```
    >metadata:
    >   annotations:
    >       openshift.io/reconcile-protect: "true"
    >```

    - Pokud chcete shromáždit cluster Kubernetes Services v rámci clusteru, nakonfigurujte soubor ConfigMap pomocí následujícího příkladu.

        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings
        [prometheus_data_collection_settings.cluster] 
        interval = "1m"  ## Valid time units are s, m, h.
        fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
        fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
        kubernetes_services = ["http://my-service-dns.my-namespace:9102/metrics"]
        ```

    - Pokud chcete nakonfigurovat vyřazení metrik Prometheus z konkrétní adresy URL v clusteru, nakonfigurujte soubor ConfigMap pomocí následujícího příkladu.

        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings
        [prometheus_data_collection_settings.cluster] 
        interval = "1m"  ## Valid time units are s, m, h.
        fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
        fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
        urls = ["http://myurl:9101/metrics"] ## An array of urls to scrape metrics from
        ```

    - Pokud chcete nakonfigurovat vyřazení metrik Prometheus z DaemonSet agenta pro každý jednotlivý uzel v clusteru, nakonfigurujte následující v ConfigMap:
    
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
        >$NODE _IP je konkrétní Azure Monitor pro parametr Containers a dá se použít místo IP adresy uzlu. Musí se jednat o všechna velká písmena. 

    - Chcete-li nakonfigurovat vyřazení metrik Prometheus zadáním poznámky pod, proveďte následující kroky:

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
           - prometheus.io/scheme:"http" #If the metrics endpoint is secured then you will need to set this to `https`, if not default ‘http’
           - prometheus.io/path:"/mymetrics" #If the metrics path is not /metrics, define it with this annotation. 
           - prometheus.io/port:"8000" #If port is not 9102 use this annotation
           ```
    
          Chcete-li omezit monitorování na konkrétní obory názvů pro lusky, které mají poznámky, například pouze v případě produkčních úloh, které jsou vyhrazeny pro produkční úlohy, nastavte na hodnotu `monitor_kubernetes_pod` `true` v ConfigMap a přidejte filtr oboru názvů `monitor_kubernetes_pods_namespaces` určující obory názvů, ze kterých se mají vyřadit. Například `monitor_kubernetes_pods_namespaces = ["default1", "default2", "default3"]`.

2. Uložte změny v editoru.

Dokončení změny konfigurace může trvat několik minut, než se projeví, a všechny omsagent v clusteru se restartují. Restartování je postupné restartování pro všechny omsagent lusky, ne pro všechna restartování ve stejnou dobu. Po dokončení restartů se zobrazí zpráva podobná následujícímu příkladu a obsahuje výsledek: `configmap "container-azm-ms-agentconfig" created` .

Aktualizované ConfigMap můžete zobrazit spuštěním příkazu `oc describe configmaps container-azm-ms-agentconfig -n openshift-azure-logging` . 

## <a name="applying-updated-configmap"></a>Použití aktualizovaných ConfigMap

Pokud jste už nasadili ConfigMap do clusteru a chcete ji aktualizovat pomocí novější konfigurace, můžete upravit soubor ConfigMap, který jste předtím použili, a pak použít stejné příkazy jako předtím.

Pro následující prostředí Kubernetes:

- Azure Kubernetes Service (AKS)
- Azure Stack nebo místní
- Azure Red Hat OpenShift a Red Hat OpenShift verze 4. x

Spusťte příkaz `kubectl apply -f <configmap_yaml_file.yaml` . 

Pro cluster Azure Red Hat OpenShift v3. x spusťte příkaz a `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` otevřete soubor ve výchozím editoru, který chcete upravit, a pak ho uložte.

Dokončení změny konfigurace může trvat několik minut, než se projeví, a všechny omsagent v clusteru se restartují. Restartování je postupné restartování pro všechny omsagent lusky, ne pro všechna restartování ve stejnou dobu. Po dokončení restartů se zobrazí zpráva podobná následujícímu příkladu a obsahuje výsledek: `configmap "container-azm-ms-agentconfig" updated` .

## <a name="verify-configuration"></a>Ověřit konfiguraci

Chcete-li ověřit, zda byla konfigurace úspěšně aplikována na cluster, použijte následující příkaz ke kontrole protokolů z agenta pod: `kubectl logs omsagent-fdf58 -n=kube-system` . 

>[!NOTE]
>Tento příkaz se nedá použít pro cluster Azure Red Hat OpenShift v3. x.
> 

Pokud dojde k chybám konfigurace z omsagent lusků, ve výstupu se zobrazí chyby podobné následujícímu:

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

Chyby související s použitím změn konfigurace jsou k dispozici také ke kontrole. K dispozici jsou následující možnosti, které umožňují další řešení potíží se změnami konfigurace a vyřazení Prometheus metriky:

- Od agenta pod protokoly pomocí stejného `kubectl logs` příkazu 
    >[!NOTE]
    >Tento příkaz se nedá použít pro cluster Azure Red Hat OpenShift.
    > 

- Z živých dat (Preview). V protokolech Live data (Preview) se zobrazují chyby podobné následujícímu:

    ```
    2019-07-08T18:55:00Z E! [inputs.prometheus]: Error in plugin: error making HTTP request to http://invalidurl:1010/metrics: Get http://invalidurl:1010/metrics: dial tcp: lookup invalidurl on 10.0.0.10:53: no such host
    ```

- Z tabulky **KubeMonAgentEvents** v pracovním prostoru Log Analytics. Data se odesílají každou hodinu se závažností *Upozornění* pro chyby vyřazení a závažnost *chyby* pro chyby konfigurace. Pokud nedochází k žádným chybám, bude mít položka v tabulce údaje *o*závažnosti, které hlásí žádné chyby. Vlastnost **tagss** obsahuje další informace o ID pod a kontejneru, na kterém došlo k chybě, a také o prvním výskytu, posledním výskytu a počtu za poslední hodinu.

- V případě Azure Red Hat OpenShift v3. x a v4. x zkontrolujte protokoly omsagent hledáním v tabulce **ContainerLog** a ověřte, jestli je povolená kolekce protokolů OpenShift-Azure-Logging.

Chyby zabraňují omsagent analýze souboru, což způsobí, že se restartuje a použije se výchozí konfigurace. Po opravě chyb v ConfigMap na jiných clusterech než Azure Red Hat OpenShift v3. x uložte soubor YAML a použijte aktualizovaný ConfigMaps spuštěním příkazu: `kubectl apply -f <configmap_yaml_file.yaml` . 

V případě Azure Red Hat OpenShift v3. x upravte a uložte aktualizované ConfigMaps spuštěním příkazu: `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` .

## <a name="query-prometheus-metrics-data"></a>Data metrik Prometheus dotazů

Chcete-li zobrazit metriky Prometheus Azure Monitor a všechny chyby konfigurace/vyřazení hlášené agentem, zkontrolujte [data metrik dotazů Prometheus](container-insights-log-search.md#query-prometheus-metrics-data) a konfiguraci [dotazů nebo chyby](container-insights-log-search.md#query-config-or-scraping-errors)při vyřazení.

## <a name="view-prometheus-metrics-in-grafana"></a>Zobrazení metrik Prometheus v Grafana

Azure Monitor for Containers podporuje zobrazování metrik uložených v pracovním prostoru Log Analytics v řídicích panelech Grafana. K dispozici je šablona, kterou si můžete stáhnout z [úložiště řídicích panelů](https://grafana.com/grafana/dashboards?dataSource=grafana-azure-monitor-datasource&category=docker) Grafana, abyste mohli začít s odkazem na pomoc s postupem, jak se dotazovat na další data z monitorovaných clusterů, aby je bylo možné vizualizovat ve vlastních řídicích panelech Grafana. 

## <a name="review-prometheus-data-usage"></a>Kontrola využití dat Prometheus

Pokud chcete zjistit objem příjmu každé metriky v GB za den, abyste zjistili, jestli je vysoká, je k dispozici následující dotaz.

```
InsightsMetrics
| where Namespace contains "prometheus"
| where TimeGenerated > ago(24h)
| summarize VolumeInGB = (sum(_BilledSize) / (1024 * 1024 * 1024)) by Name
| order by VolumeInGB desc
| render barchart
```

Ve výstupu se zobrazí výsledky podobné následujícímu:

![Výsledky dotazu do protokolu pro objem příjmu dat](./media/container-insights-prometheus-integration/log-query-example-usage-03.png)

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

![Výsledky dotazu do protokolu pro objem příjmu dat](./media/container-insights-prometheus-integration/log-query-example-usage-02.png)

Další informace o tom, jak monitorovat využití dat a analyzovat náklady, najdete v tématu [Správa využití a nákladů pomocí protokolů Azure monitor](../platform/manage-cost-storage.md).

## <a name="next-steps"></a>Další kroky

Další informace o konfiguraci nastavení kolekce agentů pro stdout, stderr a proměnné prostředí z úloh kontejnerů [najdete tady](container-insights-agent-config.md). 
