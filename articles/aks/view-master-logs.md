---
title: Zobrazit protokoly kontroleru Azure Kubernetes Service (AKS)
description: Další informace o povolení a zobrazení protokolů pro hlavní uzel Kubernetes ve službě Azure Kubernetes Service (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 01/03/2019
ms.author: mlearned
ms.openlocfilehash: ef77b991461c5d9640cbab9d53f8393540f47c9b
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2019
ms.locfileid: "67613923"
---
# <a name="enable-and-review-kubernetes-master-node-logs-in-azure-kubernetes-service-aks"></a>Povolit a zkontrolovat Kubernetes hlavní uzel protokolů ve službě Azure Kubernetes Service (AKS)

S Azure Kubernetes Service (AKS), hlavní součásti, jako *kube apiserver* a *správce kontroléru kube* jsou k dispozici jako spravovaná služba. Vytvořit a spravovat uzly, které běží *kubelet* a kontejner modulu runtime a nasadit vaše aplikace prostřednictvím spravovaného serveru Kubernetes API. K řešení potíží se vaše aplikace a služby, můžete zobrazit protokoly generované tyto hlavní součásti. Tento článek popisuje, jak používat Azure Monitor protokoly k povolení a dotazování protokolů z hlavní součásti Kubernetes.

## <a name="before-you-begin"></a>Před zahájením

Tento článek vyžaduje existující cluster AKS spuštěné v účtu Azure. Pokud již nemáte AKS cluster, vytvořte ji pomocí [rozhraní příkazového řádku Azure][cli-quickstart] or [Azure portal][portal-quickstart]. Azure Monitor protokoly funguje s oběma RBAC a není RBAC povolené AKS clustery.

## <a name="enable-diagnostics-logs"></a>Povolení diagnostických protokolů

Ke shromáždění a data kontroly z více zdrojů protokoly Azure monitoru poskytuje dotazovací jazyk a analytický modul, který poskytuje přehledy pro vaše prostředí. Pracovní prostor se používá k porovnání a analyzovat data a můžete integrovat s dalšími službami Azure, jako je například služba Application Insights a Security Center. Pokud chcete použít k analýze protokolů různé platformy, místo toho můžete odesílání diagnostických protokolů do služby Azure storage účet nebo event hub. Další informace najdete v tématu [co je Azure Monitor protokoly?][log-analytics-overview].

Protokoly služby Azure Monitor jsou povolit a spravovat na webu Azure Portal. Pokud chcete povolit shromažďování protokolů pro Kubernetes hlavní součásti v clusteru AKS, otevřete ve webovém prohlížeči na webu Azure portal a proveďte následující kroky:

1. Vyberte skupinu prostředků pro váš cluster AKS, jako je například *myResourceGroup*. Nevybírejte skupinu prostředků, která obsahuje vaše vybrané prostředky clusteru AKS, jako například *MC_myResourceGroup_myAKSCluster_eastus*.
1. Na levé straně zvolte **nastavení diagnostiky**.
1. Vyberte váš cluster AKS, jako je například *myAKSCluster*, pak se rozhodnout **přidejte nastavení diagnostiky**.
1. Zadejte název, jako například *myAKSClusterLogs*, vyberte možnost **odesílat do Log Analytics**.
1. Vyberte existující pracovní prostor nebo vytvořte novou. Pokud vytváříte pracovní prostor, zadejte název pracovního prostoru, skupinu prostředků a umístění.
1. V seznamu dostupných protokolů vyberte protokoly, které si přejete povolit. Běžné protokoly obsahují *kube apiserver*, *správce kontroléru kube*, a *kube Plánovač*. Můžete povolit dodatečné protokolování, *kube auditu* a *clusteru bylo*. Můžete vrátit a změnit shromažďovat protokoly jsou povolené pracovních prostorů Log Analytics.
1. Až to budete mít, vyberte **Uložit** povolení kolekce vybraných protokolů.

> [!NOTE]
> AKS zaznamená pouze protokoly auditu pro clustery, které jsou vytvořeny nebo upgradovat po povolení příznak funkce v rámci předplatného. K registraci *AKSAuditLog* příznak funkce, použijte [az funkce register][az-feature-register] příkaz, jak je znázorněno v následujícím příkladu:
>
> `az feature register --name AKSAuditLog --namespace Microsoft.ContainerService`
>
> Počkejte na zobrazení stavu *registrované*. Vy můžete zkontrolovat stav registrace pomocí [seznam funkcí az][az-feature-list] příkaz:
>
> `az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSAuditLog')].{Name:name,State:properties.state}"`
>
> Až to budete mít, aktualizovat registraci poskytovatele prostředků AKS pomocí [az provider register][az-provider-register] příkaz:
>
> `az provider register --namespace Microsoft.ContainerService`

Následující příklad ukazuje snímek obrazovky portálu *nastavení diagnostiky* okno:

![Povolit pracovní prostor Log Analytics pro Azure Monitor protokoly clusteru AKS](media/view-master-logs/enable-oms-log-analytics.png)

## <a name="schedule-a-test-pod-on-the-aks-cluster"></a>Plánování testů pod v clusteru AKS

Generovat některé protokoly, vytvořte nový pod ve vašem clusteru AKS. Následující příklad YAML manifestu slouží k vytvoření základní instance NGINX. Vytvořte soubor s názvem `nginx.yaml` v editoru podle vašeho výběru a vložte následující obsah:

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

Vytvořte pod s [kubectl vytvořit][kubectl-create] příkaz a zadejte svůj soubor YAML, jak je znázorněno v následujícím příkladu:

```
$ kubectl create -f nginx.yaml

pod/nginx created
```

## <a name="view-collected-logs"></a>Zobrazení shromážděných protokolů

Může trvat několik minut, než se diagnostické protokoly povolena a zobrazí v pracovním prostoru Log Analytics. Na webu Azure Portal, vyberte skupinu prostředků pro váš pracovní prostor Log Analytics, jako je například *myResourceGroup*, klikněte na tlačítko prostředku log analytics, jako například *myAKSLogs*.

![Vyberte pracovní prostor Log Analytics pro váš cluster AKS](media/view-master-logs/select-log-analytics-workspace.png)

Na levé straně zvolte **protokoly**. Chcete-li zobrazit *kube apiserver*, v textovém poli zadejte následující dotaz:

```
AzureDiagnostics
| where Category == "kube-apiserver"
| project log_s
```

Mnoho protokolů jsou pravděpodobně vrátil serveru rozhraní API. K určení oboru dolů dotazu k zobrazení protokolů o pod NGINX vytvořili v předchozím kroku, přidat další *kde* příkazu k vyhledání *podů/nginx* jak ukazuje následující příklad dotazu:

```
AzureDiagnostics
| where Category == "kube-apiserver"
| where log_s contains "pods/nginx"
| project log_s
```

Jsou zobrazeny konkrétní protokoly pro podu NGINX, jak je znázorněno v následujícím ukázkovém snímku obrazovky:

![Výsledky dotazu log analytics pro ukázkové NGINX pod](media/view-master-logs/log-analytics-query-results.png)

Chcete-li zobrazit další protokoly, můžete aktualizovat dotaz pro *kategorie* název pro *kube správce kontroléru* nebo *kube Plánovač*, v závislosti na tom, jaké další protokoly můžete Povolte. Další *kde* příkazy lze pak použít pro upřesnění události, které hledáte.

Další informace o tom, jak dotazování a filtrování dat protokolu najdete v části [zobrazení nebo analýza shromážděných dat pomocí prohledávání protokolu log analytics][analyze-log-analytics].

## <a name="log-event-schema"></a>Schéma událostí protokolu

A pomáhá tak analyzovat data protokolu, následující tabulka obsahuje podrobnosti o schéma používané pro každou jednotlivou událost:

| Název pole               | Popis |
|--------------------------|-------------|
| *ID prostředku*             | Prostředek Azure, který vytváří protokolu |
| *čas*                   | Pokud byl nahrán do protokolu časové razítko |
| *Kategorie*               | Název kontejneru nebo komponenty generování protokolu |
| *OperationName*          | Vždy *Microsoft.ContainerService/managedClusters/diagnosticLogs/Read* |
| *properties.log*         | Úplný text protokolu z komponenty |
| *properties.stream*      | *STDERR* nebo *stdout* |
| *properties.pod*         | Pod názvem, která v protokolu pochází z |
| *properties.containerID* | ID, které tento protokol pochází z kontejneru dockeru |

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak povolit a zkontrolovat protokoly pro Kubernetes hlavní součásti v clusteru AKS. Ke sledování a řešení potíží s další, můžete také [zobrazení protokolů Kubelet][kubelet-logs] and [enable SSH node access][aks-ssh].

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
