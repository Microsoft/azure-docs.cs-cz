---
title: Zásady sítě Azure Kubernetes | Microsoft Docs
description: Přečtěte si o zásadách sítě Kubernetes pro zabezpečení clusteru Kubernetes.
services: virtual-network
documentationcenter: na
author: aanandr
manager: NarayanAnnamalai
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 9/25/2018
ms.author: aanandr
ms.custom: ''
ms.openlocfilehash: 36e5bb33b7d555c3b457b63f94d9032ff390e6cb
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2020
ms.locfileid: "92342310"
---
# <a name="azure-kubernetes-network-policies-overview"></a>Přehled zásad sítě Azure Kubernetes

Zásady sítě poskytují mikrosegmentaci v luskech stejně jako skupiny zabezpečení sítě (skupin zabezpečení sítě) poskytují mikrosegmentaci pro virtuální počítače. Implementace Azure Network Policy Manageru (známá také jako Azure NPM) podporuje standardní specifikaci zásad sítě Kubernetes. Pomocí popisků můžete vybrat skupinu lusků a definovat seznam pravidel příchozího a odchozího přenosu dat pro filtrování provozu do a z těchto lusků. Další informace o zásadách sítě Kubernetes najdete v [dokumentaci k Kubernetes](https://kubernetes.io/docs/concepts/services-networking/network-policies/).

![Přehled zásad sítě Kubernetes](./media/kubernetes-network-policies/kubernetes-network-policies-overview.png)

Implementace Azure NPM funguje ve spojení se službou Azure CNI, která poskytuje integraci virtuální sítě pro kontejnery. NPM se podporuje jenom v systému Linux. Implementace vynutila filtrování přenosů tím, že na základě definovaných zásad konfiguruje pravidla IP pro povolení a odmítnutí v softwaru iptables Linux. Tato pravidla se seskupují v kombinaci s IPSets Linux.

## <a name="planning-security-for-your-kubernetes-cluster"></a>Plánování zabezpečení pro cluster Kubernetes
Při implementaci zabezpečení pro váš cluster použijte skupiny zabezpečení sítě (skupin zabezpečení sítě) k filtrování provozu, který zadává a opouští podsíť clusteru (přenos sever-jih). Využijte Azure NPM k provozu mezi lusky ve vašem clusteru (provoz na východ až severozápad).

## <a name="using-azure-npm"></a>Používání Azure NPM
Azure NPM se dá použít v následujících způsobech, jak poskytnout mikrosegmentaci pro lusky.

### <a name="azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS)
NPM je k dispozici nativně v AKS a je možné ji povolit v době vytváření clusteru. Přečtěte si další informace o [zabezpečení provozu mezi lusky pomocí zásad sítě ve službě Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/use-network-policies).

### <a name="aks-engine"></a>AKS – modul
AKS-Engine je nástroj, který generuje šablonu Azure Resource Manager pro nasazení clusteru Kubernetes v Azure. Konfigurace clusteru se zadává v souboru JSON, který se předá nástroji při generování šablony. Podrobnosti k úplnému výčtu podporovaných nastavení clusteru včetně jejich popisu najdete v tématu Modul služby Microsoft Azure Container Service – definice clusteru.

Pokud chcete povolit zásady pro clustery nasazené pomocí služby ACS-Engine, zadejte hodnotu nastavení networkPolicy v definičním souboru clusteru tak, aby byla "Azure".

#### <a name="example-configuration"></a>Příklad konfigurace

Následující příklad konfigurace JSON vytvoří novou virtuální síť a podsíť a nasadí do ní cluster Kubernetes s využitím Azure CNI. Pro úpravu souboru JSON doporučujeme použít Poznámkový blok. 
```json
{
  "apiVersion": "vlabs",
  "properties": {
    "orchestratorProfile": {
      "orchestratorType": "Kubernetes",
      "kubernetesConfig": {
         "networkPolicy": "azure"
       }
    },
    "masterProfile": {
      "count": 1,
      "dnsPrefix": "<specify a cluster name>",
      "vmSize": "Standard_D2s_v3"
    },
    "agentPoolProfiles": [
      {
        "name": "agentpool",
        "count": 2,
        "vmSize": "Standard_D2s_v3",
        "availabilityProfile": "AvailabilitySet"
      }
    ],
   "linuxProfile": {
      "adminUsername": "<specify admin username>",
      "ssh": {
        "publicKeys": [
          {
            "keyData": "<cut and paste your ssh key here>"
          }
        ]
      }
    },
    "servicePrincipalProfile": {
      "clientId": "<enter the client ID of your service principal here >",
      "secret": "<enter the password of your service principal here>"
    }
  }
}

```
### <a name="do-it-yourself-diy-kubernetes-clusters-in-azure"></a>Udělejte to sami (SVÉPOMOCNÁ) Kubernetes clustery v Azure
 U clusterů SVÉPOMOCNÁ nejdřív nainstalujte modul plug-in CNI a povolte ho na každém virtuálním počítači v clusteru. Podrobné pokyny najdete v tématu o [nasazení modulu plug-in v clusteru Kubernetes, který nasazujete sami](deploy-container-networking.md#deploy-plug-in-for-a-kubernetes-cluster).

Po nasazení clusteru spusťte následující `kubectl` příkaz ke stažení a použití procesu Azure npm *daemon nastaveného* na cluster.

  ```
  kubectl apply -f https://raw.githubusercontent.com/Azure/acs-engine/master/parts/k8s/addons/kubernetesmasteraddons-azure-npm-daemonset.yaml

  ```
Řešení je také open source a kód je k dispozici v [úložišti Azure Container Networking](https://github.com/Azure/azure-container-networking/tree/master/npm).

## <a name="monitor-and-visualize-network-configurations-with-azure-npm"></a>Monitorování a vizualizace konfigurace sítě pomocí Azure NPM
Azure NPM obsahuje informativní Prometheus metriky, které vám umožní monitorovat a lépe pochopit vaše konfigurace. Poskytuje integrované vizualizace v Azure Portal nebo Grafana Labs. Shromažďování těchto metrik můžete zahájit buď pomocí Azure Monitor nebo Prometheus serveru.

### <a name="benefits-of-azure-npm-metrics"></a>Výhody metrik Azure NPM
Uživatelé dřív byli schopni získat informace o konfiguraci sítě jenom pomocí příkazu `iptables -L` spuštěného v uzlu clusteru, který poskytuje podrobný a obtížně porozumět výstupu. Metriky NPM poskytují následující výhody týkající se zásad sítě, pravidel softwaru iptables a IPSets.
- Poskytuje přehled o vztahu mezi třemi a časovou dimenzí pro ladění konfigurace.
- Počet položek ve všech IPSets a každé IPSet.
- Doba, jakou trvalo použití zásady s členitosti úrovně IPTable/IPSet.
 
### <a name="supported-metrics"></a>Podporované metriky
Níže je seznam podporovaných metrik:

|Název metriky |Popis  |Typ metriky Prometheus  |Popisky  |
|---------|---------|---------|---------|
|`npm_num_policies`     |počet zásad sítě          |Měřidlo         |-         |
|`npm_num_iptables_rules`     | počet pravidel softwaru iptables     | Měřidlo        |-         |         
|`npm_num_ipsets`     |počet IPSets         |Měřidlo            |-         |
|`npm_num_ipset_entries`     |počet položek IP adres ve všech IPSets         |Měřidlo         |-         |
|`npm_add_policy_exec_time`     |modul runtime pro přidání zásady sítě         |Souhrn         |Quantile (0,5, 0,9 nebo 0,99)         |
|`npm_add_iptables_rule_exec_time`     |modul runtime pro přidání pravidla softwaru iptables         |Souhrn         |Quantile (0,5, 0,9 nebo 0,99)         |
|`npm_add_ipset_exec_time`     |modul runtime pro přidání IPSet         |Souhrn         |Quantile (0,5, 0,9 nebo 0,99)         |
|`npm_ipset_counts` Upřesnit     |počet položek v rámci jednotlivých IPSet         |GaugeVec         |nastavit název & hodnota hash         |

Různé úrovně Quantile v metrikách "exec_time" vám pomůžou rozlišovat mezi obecnými a nejhoršími případy.

Pro každou "exec_time" souhrnnou metriku jsou k dispozici také metrika "exec_time_count" a "exec_time_sum".

Metriky je možné vyřadit prostřednictvím Azure Monitor pro kontejnery nebo přes Prometheus.

### <a name="setup-for-azure-monitor"></a>Instalační program pro Azure Monitor
Prvním krokem je povolení Azure Monitor pro kontejnery pro cluster Kubernetes. Postup najdete v článku [Azure monitor for Containers Overview](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-overview). Jakmile budete mít Azure Monitor pro kontejnery povolené, nakonfigurujte [Azure monitor kontejnerů ConfigMap](https://aka.ms/container-azm-ms-agentconfig) tak, aby umožňovaly integraci npm a shromažďování METRIK Prometheus npm. Azure monitor pro kontejnery ConfigMap obsahuje ```integrations``` část s nastavením pro shromažďování METRIK npm. Tato nastavení jsou ve výchozím nastavení ve ConfigMap zakázaná. Povolením základního nastavení ```collect_basic_metrics = true``` budou shromažďovány základní metriky npm. Když se povolí rozšířené nastavení, ```collect_advanced_metrics = true``` budou se kromě základních metrik shromažďovat i pokročilé metriky. 

Po úpravě ConfigMap ho uložte místně a použijte ConfigMap pro váš cluster následujícím způsobem.

```kubectl apply -f container-azm-ms-agentconfig.yaml``` Níže je fragment kódu z [Azure monitoru pro kontejnery ConfigMap](https://aka.ms/container-azm-ms-agentconfig), který ukazuje integraci npm s pokročilou kolekcí metrik.
```
integrations: |-
    [integrations.azure_network_policy_manager]
        collect_basic_metrics = false
        collect_advanced_metrics = true
```
Rozšířené metriky jsou volitelné a když je zapnete, budou automaticky zapnuty základní kolekce metrik. Rozšířené metriky aktuálně obsahují pouze `npm_ipset_counts`

Další informace o [nastavení kolekce kontejnerů pro Azure monitor v mapování konfigurace](https://aka.ms/azmon-containers-agent-collection-settings-doc)

### <a name="visualization-options-for-azure-monitor"></a>Možnosti vizualizace pro Azure Monitor
Jakmile je kolekce metriky NPM povolená, můžete zobrazit metriky v Azure Portal pomocí kontejnerových přehledů nebo v Grafana.

#### <a name="viewing-in-azure-portal-under-insights-for-the-cluster"></a>Zobrazení v Azure Portal v části přehledy pro cluster
Otevřete Azure Portal. Jakmile budete mít přehled o clusteru, přejděte do části sešity a otevřete konfiguraci Správce síťových zásad (NPM).

Kromě zobrazení sešitu (obrázky níže) můžete také přímo zadat dotaz na metriky Prometheus v části protokoly v části přehledy. Tento dotaz bude například vracet všechny shromažďované metriky.
| kde TimeGenerated > před (5H) | kde název obsahuje "npm_"

Můžete také zadat dotaz na Log Analytics přímo pro metriky. Další informace o Začínáme s [dotazy Log Analytics](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-log-search) 

#### <a name="viewing-in-grafana-dashboard"></a>Zobrazení v řídicím panelu Grafana
Nastavte server Grafana a nakonfigurujte zdroj dat Log Analytics, jak je popsáno [zde](https://grafana.com/grafana/plugins/grafana-azure-monitor-datasource). Pak naimportujte [řídicí panel Grafana pomocí back-endu Log Analytics](https://grafana.com/grafana/dashboards/10956) do Grafana Labs.

Řídicí panel obsahuje vizuály, které jsou podobné vašemu sešitu Azure. Do grafu můžete přidat panely & Vizualizujte metriky NPM z tabulky InsightsMetrics.

### <a name="setup-for-prometheus-server"></a>Nastavení serveru Prometheus
Někteří uživatelé se můžou rozhodnout shromáždit metriky pomocí serveru Prometheus místo Azure Monitor pro kontejnery. Stačí přidat dvě úlohy do konfigurace likvidace, abyste mohli shromažďovat metriky NPM.

K instalaci jednoduchého serveru Prometheus přidejte toto úložiště Helm na svůj cluster.
```
helm repo add stable https://kubernetes-charts.storage.googleapis.com
helm repo update
```
pak přidejte server
```
helm install prometheus stable/prometheus -n monitoring \
--set pushgateway.enabled=false,alertmanager.enabled=false, \
--set-file extraScrapeConfigs=prometheus-server-scrape-config.yaml
```
kde `prometheus-server-scrape-config.yaml` se skládá z
```
- job_name: "azure-npm-node-metrics"
  metrics_path: /node-metrics
  kubernetes_sd_configs:
  - role: node
  relabel_configs:
  - source_labels: [__address__]
    action: replace
    regex: ([^:]+)(?::\d+)?
    replacement: "$1:10091"
    target_label: __address__
- job_name: "azure-npm-cluster-metrics"
  metrics_path: /cluster-metrics
  kubernetes_sd_configs:
  - role: service
  relabel_configs:
  - source_labels: [__meta_kubernetes_namespace]
    regex: kube-system
    action: keep
  - source_labels: [__meta_kubernetes_service_name]
    regex: npm-metrics-cluster-service
    action: keep
# Comment from here to the end to collect advanced metrics: number of entries for each IPSet
  metric_relabel_configs:
  - source_labels: [__name__]
    regex: npm_ipset_counts
    action: drop
```


Můžete také nahradit `azure-npm-node-metrics` úlohu následujícím obsahem nebo ji začlenit do již existující úlohy pro Kubernetes lusky:
```
- job_name: "azure-npm-node-metrics-from-pod-config"
  metrics_path: /node-metrics
  kubernetes_sd_configs:
  - role: pod
  relabel_configs:
  - source_labels: [__meta_kubernetes_namespace]
    regex: kube-system
    action: keep
  - source_labels: [__meta_kubernetes_pod_annotationpresent_azure_npm_scrapeable]
    action: keep
  - source_labels: [__address__]
    action: replace
    regex: ([^:]+)(?::\d+)?
    replacement: "$1:10091"
    target_label: __address__
```

### <a name="visualization-options-for-prometheus"></a>Možnosti vizualizace pro Prometheus
Při použití Prometheus serveru se podporuje jenom řídicí panel Grafana. 

Pokud jste to ještě neudělali, nastavte Server Grafana a nakonfigurujte zdroj dat Prometheus. Pak naimportujte [řídicí panel Grafana s Prometheus back-endu](https://grafana.com/grafana/dashboards/13000) do vaší Grafana Labs.

Vizuály pro tento řídicí panel se shodují s řídicím panelem, který má kontejner Insights/Log Analytics back-end.

### <a name="sample-dashboards"></a>Ukázkové řídicí panely
Tady je několik ukázkových řídicích panelů pro metriky NPM ve Container Insights (CI) a Grafana.

#### <a name="ci-summary-counts"></a>Počty souhrnů CI
![Počty souhrnů Azure Workbook](media/kubernetes-network-policies/workbook-summary-counts.png)

#### <a name="ci-counts-over-time"></a>Počty CI v průběhu času
[![Počty sešitů Azure v průběhu času](media/kubernetes-network-policies/workbook-counts-over-time.png)](media/kubernetes-network-policies/workbook-counts-over-time.png#lightbox)

#### <a name="ci-ipset-entries"></a>Položky IPSet CI
[![Položky IPSet v sešitu Azure](media/kubernetes-network-policies/workbook-ipset-entries.png)](media/kubernetes-network-policies/workbook-ipset-entries.png#lightbox)

#### <a name="ci-runtime-quantiles"></a>Quantiles modulu CI runtime
![Quantiles Azure Workbook runtime](media/kubernetes-network-policies/workbook-runtime-quantiles.png)

#### <a name="grafana-dashboard-summary-counts"></a>Počty souhrnů řídicího panelu Grafana
![Počty souhrnů řídicího panelu Grafana](media/kubernetes-network-policies/grafana-summary-counts.png)

#### <a name="grafana-dashboard-counts-over-time"></a>Počty řídicích panelů Grafana v průběhu času
[![Počty řídicích panelů Grafana v průběhu času](media/kubernetes-network-policies/grafana-counts-over-time.png)](media/kubernetes-network-policies/grafana-counts-over-time.png#lightbox)

#### <a name="grafana-dashboard-ipset-entries"></a>Položky IPSet řídicího panelu Grafana
[![Položky IPSet řídicího panelu Grafana](media/kubernetes-network-policies/grafana-ipset-entries.png)](media/kubernetes-network-policies/grafana-ipset-entries.png#lightbox)

#### <a name="grafana-dashboard-runtime-quantiles"></a>Quantiles modulu runtime řídicího panelu Grafana
[![Quantiles modulu runtime řídicího panelu Grafana](media/kubernetes-network-policies/grafana-runtime-quantiles.png)](media/kubernetes-network-policies/grafana-runtime-quantiles.png#lightbox)



## <a name="next-steps"></a>Další kroky
- Přečtěte si o [službě Azure Kubernetes](../aks/intro-kubernetes.md).
-  Přečtěte si o [sítích kontejnerů](container-networking-overview.md).
- [Nasaďte modul plug-in](deploy-container-networking.md) pro clustery Kubernetes nebo kontejnery Docker.

    
