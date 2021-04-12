---
title: Zobrazení metrik clusteru pro službu Azure Kubernetes (AKS)
description: Zobrazte metriky clusteru pro Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 03/30/2021
ms.openlocfilehash: 32d8745e0ea780b5f86588fabdc25b244cf2cd8e
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105969226"
---
# <a name="view-cluster-metrics-for-azure-kubernetes-service-aks"></a>Zobrazení metrik clusteru pro službu Azure Kubernetes (AKS)

AKS poskytuje sadu metrik pro plochu ovládacího prvku, včetně serveru rozhraní API a automatického škálování clusteru a uzlů clusteru. Tyto metriky vám umožní monitorovat stav clusteru a řešit problémy. Metriky pro svůj cluster můžete zobrazit pomocí Azure Portal.

> [!NOTE]
> Tyto metriky clusterů AKS se překrývají s podmnožinou metrik, kterou [poskytuje Kubernetes][kubernetes-metrics].

## <a name="view-metrics-for-your-aks-cluster-using-the-azure-portal"></a>Zobrazte metriky pro cluster AKS pomocí Azure Portal

Chcete-li zobrazit metriky pro cluster AKS:

1. Přihlaste se k [Azure Portal][azure-portal] a přejděte do svého clusteru AKS.
1. Na levé straně v části *monitorování* vyberte *metriky*.
1. Vytvořte graf pro metriky, které chcete zobrazit. Vytvořte například graf:
    1. V *oblasti obor* vyberte svůj cluster.
    1. V případě *oboru názvů metriky* vyberte *Standardní metriky služby kontejneru (spravované)*.
    1. U *metriky* v části *lusky* vyberte *počet lusků podle fáze*.
    1. V možnosti *agregace* vyberte *střední*.

:::image type="content" source="media/metrics/metrics-chart.png" alt-text="{alt-text}":::

Výše uvedený příklad ukazuje metriky pro průměrný počet lusků pro *myAKSCluster*.

## <a name="available-metrics"></a>Dostupné metriky

K dispozici jsou následující metriky clusteru:

| Name | Group (Skupina) | ID | Description |
| --- | --- | --- | ---- |
| Žádosti o inletu | Server API (Preview) |apiserver_current_inflight_requests | Maximální počet aktuálně aktivních požadavků na inletu na serveru rozhraní API na typ žádosti. |
| Stav clusteru | Automatické škálování clusteru (Preview) | cluster_autoscaler_cluster_safe_to_autoscale | Určuje, jestli má cluster pro automatické škálování v clusteru provést akci. |
| Horizontální navýšení kapacity cooldown | Automatické škálování clusteru (Preview) | cluster_autoscaler_scale_down_in_cooldown | Určuje, zda je horizontální snížení kapacity v cooldown – během tohoto časového období nebudou žádné uzly odebrány. |
| Nepotřebné uzly | Automatické škálování clusteru (Preview) | cluster_autoscaler_unneeded_nodes_count | Cluster auotscaler označí tyto uzly jako kandidáty na odstranění a nakonec se odstraní. |
| Unschedulable lusky | Automatické škálování clusteru (Preview) | cluster_autoscaler_unschedulable_pods_count | Počet lusků, které jsou v clusteru aktuálně unschedulable. |
| Celkový počet dostupných jader procesoru ve spravovaném clusteru | Uzly | kube_node_status_allocatable_cpu_cores | Celkový počet dostupných jader procesoru ve spravovaném clusteru. |
| Celková velikost dostupné paměti ve spravovaném clusteru | Uzly | kube_node_status_allocatable_memory_bytes | Celková velikost dostupné paměti ve spravovaném clusteru. |
| Stavy pro různé podmínky uzlu | Uzly | kube_node_status_condition | Stavy pro různé podmínky uzlu |
| Využití CPU Millicores | Uzly (Preview) | node_cpu_usage_millicores | Agregované měření využití CPU v millicores napříč clusterem. |
| Procento využití procesoru | Uzly (Preview) | node_cpu_usage_percentage | Celkové průměrné využití procesoru měřené v procentech v rámci clusteru. |
| Paměť RSS – bajty | Uzly (Preview) | node_memory_rss_bytes | Využitá paměť RSS kontejneru v bajtech |
| Procento paměti RSS | Uzly (Preview) | node_memory_rss_percentage | Paměť RSS kontejneru využitá v procentech |
| Bajty pracovní sady paměti | Uzly (Preview) | node_memory_working_set_bytes | Paměť pracovní sady kontejneru využitá v bajtech. |
| Procento pracovní sady paměti | Uzly (Preview) | node_memory_working_set_percentage | Paměť pracovní sady kontejneru využitá v procentech |
| Využité bajty disku | Uzly (Preview) | node_disk_usage_bytes | Místo na disku využité v bajtech podle zařízení. |
| Procento využitého disku | Uzly (Preview) | node_disk_usage_percentage | Místo na disku využité v procentech podle zařízení. |
| Síť v bajtech | Uzly (Preview) | node_network_in_bytes | Počet přijatých bajtů sítě. |
| Výstupní bajty sítě | Uzly (Preview) | node_network_out_bytes | Přenesené bajty sítě. |
| Počet lusků ve stavu připraveno | Podů | kube_pod_status_ready | Počet lusků ve stavu *připraveno* . |
| Počet lusků podle fáze | Podů | kube_pod_status_phase | Počet lusků podle fáze |

> [!IMPORTANT]
> Metriky ve verzi Preview je možné aktualizovat nebo změnit, včetně jejich názvů a popisů ve verzi Preview.

## <a name="next-steps"></a>Další kroky

Kromě metriky clusteru pro AKS můžete také použít Azure Monitor s clusterem AKS. Další informace o použití Azure Monitor s AKS najdete v tématu [Azure monitor for Containers][aks-azure-monitory].

[aks-azure-monitory]: ../azure-monitor/containers/container-insights-overview.md
[azure-portal]: https://portal.azure.com/
[kubernetes-metrics]: https://kubernetes.io/docs/concepts/cluster-administration/system-metrics/