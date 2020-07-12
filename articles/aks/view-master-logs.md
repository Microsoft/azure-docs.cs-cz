---
title: Zobrazit protokoly řadiče AKS (Azure Kubernetes Service)
description: Naučte se, jak povolit a zobrazit protokoly pro hlavní uzel Kubernetes ve službě Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 01/03/2019
ms.openlocfilehash: 76ded781d4eae48db04f54a4f88a80cc700d0ad9
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2020
ms.locfileid: "86250732"
---
# <a name="enable-and-review-kubernetes-master-node-logs-in-azure-kubernetes-service-aks"></a>Povolení a kontrola protokolů hlavních uzlů Kubernetes ve službě Azure Kubernetes Service (AKS)

Pomocí služby Azure Kubernetes Service (AKS) jsou hlavní součásti, jako je například *Kube-apiserver* a *Kube-Controller-Manager* , poskytovány jako spravovaná služba. Můžete vytvářet a spravovat uzly, které spouštějí *kubelet* a modul runtime kontejnerů, a nasazovat aplikace prostřednictvím spravovaného serveru rozhraní Kubernetes API. Chcete-li pomoct s řešením vaší aplikace a služeb, bude pravděpodobně nutné zobrazit protokoly generované těmito hlavními komponentami. V tomto článku se dozvíte, jak používat protokoly Azure Monitor k povolení a dotazování protokolů z hlavních komponent Kubernetes.

## <a name="before-you-begin"></a>Než začnete

Tento článek vyžaduje existující cluster AKS spuštěný ve vašem účtu Azure. Pokud ještě nemáte cluster AKS, vytvořte ho pomocí [Azure CLI][cli-quickstart] nebo [Azure Portal][portal-quickstart]. Protokoly Azure Monitor fungují s clustery AKS s povolenou RBAC i bez RBAC.

## <a name="enable-resource-logs"></a>Povolení protokolů prostředků

Aby bylo možné získat a zkontrolovat data z více zdrojů, Azure Monitor protokolů poskytuje dotazovací jazyk a analytický modul, který poskytuje přehledy pro vaše prostředí. Pracovní prostor se používá k kompletování a analýze dat a může se integrovat s dalšími službami Azure, jako jsou Application Insights a Security Center. Pokud chcete k analýze protokolů použít jinou platformu, můžete místo toho Odeslat protokoly prostředků do účtu služby Azure Storage nebo centra událostí. Další informace najdete v tématu [co je Azure monitor protokolů?][log-analytics-overview].

Protokoly Azure Monitor jsou v Azure Portal povolené a spravované. Chcete-li povolit shromažďování protokolů pro hlavní komponenty Kubernetes v clusteru AKS, otevřete Azure Portal ve webovém prohlížeči a proveďte následující kroky:

1. Vyberte skupinu prostředků pro cluster AKS, například *myResourceGroup*. Nevybírejte skupinu prostředků, která obsahuje vaše jednotlivé prostředky clusteru AKS, například *MC_myResourceGroup_myAKSCluster_eastus*.
1. Na levé straně vyberte **nastavení diagnostiky**.
1. Vyberte cluster AKS, jako je například *myAKSCluster*, a pak zvolte **Přidání nastavení diagnostiky**.
1. Zadejte název, třeba *myAKSClusterLogs*, a pak vyberte možnost **odeslání do Log Analytics**.
1. Vyberte existující pracovní prostor nebo vytvořte nový. Pokud vytváříte pracovní prostor, zadejte název pracovního prostoru, skupinu prostředků a umístění.
1. V seznamu dostupných protokolů vyberte protokoly, které chcete povolit. Mezi běžné protokoly patří *Kube-apiserver*, *Kube-Controller-Manager*a *Kube-Scheduler*. Můžete povolit další protokoly, jako je například *Kube-audit* a *cluster-AutoScale*. Shromážděné protokoly můžete vrátit a změnit, jakmile jsou povolené pracovní prostory Log Analytics.
1. Až budete připraveni, vyberte **Uložit** a povolte shromažďování vybraných protokolů.

Následující příklad snímku obrazovky portálu ukazuje okno *nastavení diagnostiky* :

![Povolit Log Analytics pracovní prostor pro Azure Monitor protokoly clusteru AKS](media/view-master-logs/enable-oms-log-analytics.png)

## <a name="schedule-a-test-pod-on-the-aks-cluster"></a>Naplánování testu pod v clusteru AKS

Pokud chcete vygenerovat některé protokoly, vytvořte v clusteru AKS nový. Následující příklad manifestu YAML lze použít k vytvoření základní instance NGINX. Vytvořte soubor s názvem `nginx.yaml` v libovolném editoru a vložte následující obsah:

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

Vytvořte pod příkazem [kubectl Create][kubectl-create] a zadejte svůj soubor YAML, jak je znázorněno v následujícím příkladu:

```
$ kubectl create -f nginx.yaml

pod/nginx created
```

## <a name="view-collected-logs"></a>Zobrazit shromážděné protokoly

Povolení a zobrazení diagnostických protokolů v pracovním prostoru Log Analytics může trvat několik minut. V Azure Portal vyberte skupinu prostředků pro pracovní prostor Log Analytics, například *myResourceGroup*, a pak zvolte svůj prostředek Log Analytics, například *myAKSLogs*.

![Výběr pracovního prostoru Log Analytics pro cluster AKS](media/view-master-logs/select-log-analytics-workspace.png)

Na levé straně vyberte **protokoly**. Pokud chcete zobrazit *Kube-apiserver*, zadejte do textového pole tento dotaz:

```
AzureDiagnostics
| where Category == "kube-apiserver"
| project log_s
```

Pro Server rozhraní API je nejspíš vráceno mnoho protokolů. Chcete-li určit rozsah dotazu pro zobrazení protokolů NGINX pod vytvořením v předchozím kroku, přidejte další příkaz *WHERE* pro hledání *lusků/Nginx* , jak je znázorněno v následujícím příkladu dotazu:

```
AzureDiagnostics
| where Category == "kube-apiserver"
| where log_s contains "pods/nginx"
| project log_s
```

Zobrazí se konkrétní protokoly pro váš NGINX pod, jak je znázorněno v následujícím ukázkovém snímku obrazovky:

![Výsledky dotazu Log Analytics pro Sample NGINX pod](media/view-master-logs/log-analytics-query-results.png)

Pokud chcete zobrazit další protokoly, můžete aktualizovat dotaz pro název *kategorie* na *Kube-Controller-Manager* nebo *Kube-Scheduler*v závislosti na tom, jaké další protokoly jste povolili. Další příkazy *WHERE* lze použít k upřesnění událostí, které hledáte.

Další informace o tom, jak zadávat dotazy a filtrovat data protokolu, najdete v tématu [zobrazení nebo analýza dat shromážděných pomocí prohledávání protokolů Log Analytics][analyze-log-analytics].

## <a name="log-event-schema"></a>Schéma událostí protokolu

Následující tabulka podrobně popisuje schéma používané pro každou událost, aby bylo možné analyzovat data protokolu:

| Název pole               | Popis |
|--------------------------|-------------|
| *Prostředku*             | Prostředek Azure, který vytvořil protokol |
| *interval*                   | Časové razítko odeslání protokolu |
| *kategorií*               | Název kontejneru nebo komponenty generující protokol |
| *operationName*          | Vždy *Microsoft. ContainerService/managedClusters/diagnosticLogs/Read* |
| *vlastnosti. log*         | Úplný text protokolu z komponenty |
| *vlastnosti. Stream*      | *stderr* nebo *stdout* |
| *vlastnosti. pod*         | Pod názvem, ze kterého pochází protokol |
| *Properties. containerID* | ID kontejneru Docker, ze kterého pochází tento protokol |

## <a name="log-roles"></a>Role protokolu

| Role                     | Popis |
|--------------------------|-------------|
| *aksService*             | Zobrazovaný název v protokolu auditu pro operaci správy roviny (z hcpService) |
| *masterclient*           | Zobrazovaný název v protokolu auditu pro MasterClientCertificate, certifikát, ze kterého získáte AZ AKS Get-Credentials |
| *nodeclient*             | Zobrazovaný název pro ClientCertificate, který se používá v uzlech agentů |

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak povolit a zkontrolovat protokoly pro hlavní komponenty Kubernetes v clusteru AKS. K dalšímu sledování a řešení potíží můžete také [Zobrazit protokoly Kubelet][kubelet-logs] a [Povolit přístup k uzlu SSH][aks-ssh].

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create

<!-- LINKS - internal -->
[cli-quickstart]: kubernetes-walkthrough.md
[portal-quickstart]: kubernetes-walkthrough-portal.md
[log-analytics-overview]: ../azure-monitor/log-query/log-query-overview.md
[analyze-log-analytics]: ../azure-monitor/log-query/get-started-portal.md
[kubelet-logs]: kubelet-logs.md
[aks-ssh]: ssh.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
