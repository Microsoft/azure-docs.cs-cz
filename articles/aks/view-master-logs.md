---
title: Zobrazení protokolů řadičů služby Azure Kubernetes (AKS)
description: Zjistěte, jak povolit a zobrazit protokoly hlavního uzlu Kubernetes ve službě Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 01/03/2019
ms.openlocfilehash: f759f15cf98546cb95ba0adb5890885f85ca6aa1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259380"
---
# <a name="enable-and-review-kubernetes-master-node-logs-in-azure-kubernetes-service-aks"></a>Povolení a kontrola protokolů hlavních uzlů Kubernetes ve službě Azure Kubernetes Service (AKS)

Se službou Azure Kubernetes Service (AKS) jsou hlavní součásti, jako je *kube-apiserver* a *správce kube-controller,* poskytovány jako spravovaná služba. Můžete vytvořit a spravovat uzly, které spouštějí *runa kubelet* a kontejner runtime a nasadit aplikace prostřednictvím spravovaného serveru Rozhraní API Kubernetes. Chcete-li pomoci při řešení potíží s aplikací a službami, bude pravděpodobně nutné zobrazit protokoly generované těmito hlavní součástmi. Tento článek ukazuje, jak používat protokoly Azure Monitor povolit a dotaz protokoly z hlavních součástí Kubernetes.

## <a name="before-you-begin"></a>Než začnete

Tento článek vyžaduje existující cluster AKS spuštěný ve vašem účtu Azure. Pokud ještě nemáte cluster AKS, vytvořte ho pomocí [Azure CLI][cli-quickstart] nebo [portálu Azure][portal-quickstart]. Protokoly Azure Monitor funguje s RBAC i nerbac povoleno Clustery AKS.

## <a name="enable-diagnostics-logs"></a>Povolení protokolů diagnostiky

Protokoly Azure Monitoru poskytují dotazovací jazyk a analytický modul, který poskytuje přehledovně pro vaše prostředí, aby bylo možné shromažďovat a kontrolovat data z více zdrojů. Pracovní prostor se používá ke shromažďování a analýze dat a lze integrovat s dalšími službami Azure, jako jsou Application Insights a Security Center. Chcete-li k analýze protokolů použít jinou platformu, můžete místo toho zvolit odeslání diagnostických protokolů do účtu úložiště Azure nebo centra událostí. Další informace najdete v tématu [Co je protokoly Azure Monitor?][log-analytics-overview].

Protokoly Azure Monitoru jsou povolené a spravované na webu Azure Portal. Chcete-li povolit kolekci protokolů pro hlavní součásti Kubernetes v clusteru AKS, otevřete portál Azure ve webovém prohlížeči a proveďte následující kroky:

1. Vyberte skupinu prostředků pro cluster AKS, například *myResourceGroup*. Nevybírejte skupinu prostředků, která obsahuje jednotlivé prostředky clusteru AKS, například *MC_myResourceGroup_myAKSCluster_eastus*.
1. Na levé straně zvolte **Nastavení diagnostiky**.
1. Vyberte cluster AKS, například *myAKSCluster*, a pak zvolte **přidat diagnostické nastavení**.
1. Zadejte název, například *myAKSClusterLogs*, a vyberte možnost **Odeslat do analýzy protokolů**.
1. Vyberte existující pracovní prostor nebo vytvořte nový. Pokud vytvoříte pracovní prostor, zadejte název pracovního prostoru, skupinu prostředků a umístění.
1. V seznamu dostupných protokolů vyberte protokoly, které chcete povolit. Běžné protokoly zahrnují *kube-apiserver*, *kube-controller-manager*a *kube-scheduler*. Můžete povolit další protokoly, například *kube-audit* a *cluster-autoscaler*. Můžete vrátit a změnit shromážděné protokoly, jakmile jsou povoleny pracovní prostory Analýzy protokolů.
1. Až budete připraveni, vyberte **Uložit,** chcete-li povolit shromažďování vybraných protokolů.

Následující ukázkový snímek obrazovky portálu ukazuje okno *Nastavení diagnostiky:*

![Povolení pracovního prostoru Log Analytics pro protokoly Azure Monitor clusteru AKS](media/view-master-logs/enable-oms-log-analytics.png)

## <a name="schedule-a-test-pod-on-the-aks-cluster"></a>Naplánování testovacího podu v clusteru AKS

Chcete-li generovat některé protokoly, vytvořte nový pod v clusteru AKS. Následující příklad ManifestU YAML lze použít k vytvoření základní instance NGINX. Vytvořte soubor `nginx.yaml` pojmenovaný v editoru podle vašeho výběru a vložte následující obsah:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  - name: mypod
    image: nginx:1.15.5
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    ports:
    - containerPort: 80
```

Vytvořte pod s příkazem [create kubectl][kubectl-create] a určete soubor YAML, jak je znázorněno v následujícím příkladu:

```
$ kubectl create -f nginx.yaml

pod/nginx created
```

## <a name="view-collected-logs"></a>Zobrazit shromážděné protokoly

Může trvat několik minut, než se diagnostické protokoly povolují a zobrazí se v pracovním prostoru Analýzy protokolů. Na webu Azure Portal vyberte skupinu prostředků pro pracovní prostor Analýzy protokolů, například *myResourceGroup*, a pak zvolte prostředek analýzy protokolů, například *myAKSLogs*.

![Vyberte pracovní prostor Log Analytics pro cluster AKS.](media/view-master-logs/select-log-analytics-workspace.png)

Na levé straně zvolte **Protokoly**. Chcete-li zobrazit *kube-apiserver*, zadejte do textového pole následující dotaz:

```
AzureDiagnostics
| where Category == "kube-apiserver"
| project log_s
```

Mnoho protokolů jsou pravděpodobně vráceny pro server rozhraní API. Chcete-li rozsah dolů dotaz zobrazit protokoly o pod NGINX vytvořené v předchozím kroku, přidejte další *where* prohlášení pro vyhledávání *pods/nginx,* jak je znázorněno v následujícím příkladu dotazu:

```
AzureDiagnostics
| where Category == "kube-apiserver"
| where log_s contains "pods/nginx"
| project log_s
```

Zobrazí se konkrétní protokoly pro pod NGINX, jak je znázorněno na následujícím příkladu snímku obrazovky:

![Výsledky dotazu analýzy protokolů pro ukázkový pod NGINX](media/view-master-logs/log-analytics-query-results.png)

Chcete-li zobrazit další protokoly, můžete aktualizovat dotaz na název *kategorie* *kube-controller-manager* nebo *kube-scheduler*, v závislosti na tom, jaké další protokoly povolíte. Další, *kde* příkazy lze použít k upřesnění události, které hledáte.

Další informace o tom, jak dotazovat a filtrovat data protokolu, naleznete v [tématu Zobrazení nebo analýza dat shromážděných pomocí hledání protokolu analytics][analyze-log-analytics].

## <a name="log-event-schema"></a>Schéma události protokolu

Chcete-li analyzovat data protokolu, následující tabulka podrobně popisuje schéma použité pro každou událost:

| Název pole               | Popis |
|--------------------------|-------------|
| *Resourceid*             | Prostředek Azure, který vytvořil protokol |
| *Čas*                   | Časové razítko, kdy byl protokol nahrán |
| *Kategorie*               | Název kontejneru/součásti generující protokol |
| *název_operace*          | Vždy *Microsoft.ContainerService/managedClusters/diagnosticLogs/Read* |
| *properties.log*         | Úplný text protokolu ze komponenty |
| *properties.stream*      | *stderr* nebo *stdout* |
| *properties.pod*         | Název podu, ze kterého protokol pochází |
| *properties.containerID* | ID kontejneru dockeru, ze kterých tento protokol pochází |

## <a name="log-roles"></a>Role protokolu

| Role                     | Popis |
|--------------------------|-------------|
| *aksService*             | Zobrazovaný název v protokolu auditu pro operaci roviny ovládacího prvku (z hcpService) |
| *hlavní klient*           | Zobrazovaný název v protokolu auditu pro MasterClientCertificate, certifikát, který získáte od az aks get-credentials |
| *klient uzlu*             | Zobrazovaný název pro ClientCertificate, který používá uzly agenta |

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli, jak povolit a zkontrolovat protokoly pro hlavní součásti Kubernetes v clusteru AKS. Chcete-li dále sledovat a řešit problémy, můžete také [zobrazit kubeletské protokoly][kubelet-logs] a [povolit přístup k uzlu SSH][aks-ssh].

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create

<!-- LINKS - internal -->
[cli-quickstart]: kubernetes-walkthrough.md
[portal-quickstart]: kubernetes-walkthrough-portal.md
[log-analytics-overview]: ../log-analytics/log-analytics-overview.md
[analyze-log-analytics]: ../azure-monitor/learn/tutorial-viewdata.md
[kubelet-logs]: kubelet-logs.md
[aks-ssh]: ssh.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
