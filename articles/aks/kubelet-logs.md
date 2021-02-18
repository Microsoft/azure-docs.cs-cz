---
title: Zobrazení protokolů kubelet ve službě Azure Kubernetes Service (AKS)
description: Přečtěte si, jak zobrazit informace o řešení potíží v protokolech kubelet z uzlů Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 03/05/2019
ms.openlocfilehash: 31605d1b6129c03dcd860d78f937a41ae36502a7
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100578606"
---
# <a name="get-kubelet-logs-from-azure-kubernetes-service-aks-cluster-nodes"></a>Získání protokolů kubelet z uzlů clusteru Azure Kubernetes Service (AKS)

V rámci provozu clusteru AKS možná budete muset zkontrolovat protokoly a vyřešit problém. Integrovaná do Azure Portal je schopnost zobrazit protokoly pro [hlavní komponenty][aks-master-logs] a [kontejnery AKS v clusteru AKS][azure-container-logs]. V některých případech může být potřeba získat protokoly *kubelet* z uzlu AKS pro účely řešení potíží.

V tomto článku se dozvíte, jak můžete pomocí nástroje `journalctl` Zobrazit protokoly *kubelet* v uzlu AKS.

## <a name="before-you-begin"></a>Než začnete

V tomto článku se předpokládá, že máte existující cluster AKS. Pokud potřebujete cluster AKS, přečtěte si rychlý Start AKS a [použijte Azure CLI][aks-quickstart-cli] nebo [Azure Portal][aks-quickstart-portal].

## <a name="create-an-ssh-connection"></a>Vytvoření připojení SSH

Nejdřív vytvořte připojení SSH s uzlem, ke kterému potřebujete zobrazit protokoly *kubelet* . Tato operace je podrobně popsána v dokumentu pro [uzly clusteru Azure Kubernetes Service (AKS)][aks-ssh] .

## <a name="get-kubelet-logs"></a>Získání protokolů kubelet

Jakmile se připojíte k uzlu, spusťte následující příkaz, který načte protokoly *kubelet* :

```console
sudo journalctl -u kubelet -o cat
```

> [!NOTE]
> V případě uzlů Windows se data protokolu nacházejí v `C:\k` a lze je zobrazit pomocí příkazu *more* :
> ```
> more C:\k\kubelet.log
> ```

Následující vzorový výstup zobrazuje data protokolu *kubelet* :

```
I0508 12:26:17.905042    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:26:27.943494    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:26:28.920125    8672 server.go:796] GET /stats/summary: (10.370874ms) 200 [[Ruby] 10.244.0.2:52292]
I0508 12:26:37.964650    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:26:47.996449    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:26:58.019746    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:27:05.107680    8672 server.go:796] GET /stats/summary/: (24.853838ms) 200 [[Go-http-client/1.1] 10.244.0.3:44660]
I0508 12:27:08.041736    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:27:18.068505    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:27:28.094889    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:27:38.121346    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:27:44.015205    8672 server.go:796] GET /stats/summary: (30.236824ms) 200 [[Ruby] 10.244.0.2:52588]
I0508 12:27:48.145640    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:27:58.178534    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:28:05.040375    8672 server.go:796] GET /stats/summary/: (27.78503ms) 200 [[Go-http-client/1.1] 10.244.0.3:44660]
I0508 12:28:08.214158    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:28:18.242160    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:28:28.274408    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:28:38.296074    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:28:48.321952    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:28:58.344656    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
```

## <a name="next-steps"></a>Další kroky

Pokud potřebujete další informace pro řešení potíží z hlavní větve Kubernetes, přečtěte si téma [zobrazení protokolů hlavního uzlu Kubernetes v AKS][aks-master-logs].

<!-- LINKS - internal -->
[aks-ssh]: ssh.md
[aks-master-logs]: view-master-logs.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-master-logs]: view-master-logs.md
[azure-container-logs]: ../azure-monitor/containers/container-insights-overview.md
