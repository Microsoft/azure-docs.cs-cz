---
title: Zobrazit protokoly řadiče AKS (Azure Kubernetes Service)
description: Naučte se, jak povolit a zobrazit protokoly pro hlavní uzel Kubernetes ve službě Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 10/14/2020
ms.openlocfilehash: 5f0a01adfabe59542fa999af3103a9394f4dd77b
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/21/2021
ms.locfileid: "98664074"
---
# <a name="enable-and-review-kubernetes-master-node-logs-in-azure-kubernetes-service-aks"></a>Povolení a kontrola protokolů hlavních uzlů Kubernetes ve službě Azure Kubernetes Service (AKS)

Pomocí služby Azure Kubernetes Service (AKS) jsou hlavní součásti, jako je například *Kube-apiserver* a *Kube-Controller-Manager* , poskytovány jako spravovaná služba. Můžete vytvářet a spravovat uzly, které spouštějí *kubelet* a modul runtime kontejnerů, a nasazovat aplikace prostřednictvím spravovaného serveru rozhraní Kubernetes API. Chcete-li pomoct s řešením vaší aplikace a služeb, bude pravděpodobně nutné zobrazit protokoly generované těmito hlavními komponentami. V tomto článku se dozvíte, jak používat protokoly Azure Monitor k povolení a dotazování protokolů z hlavních komponent Kubernetes.

## <a name="before-you-begin"></a>Než začnete

Tento článek vyžaduje existující cluster AKS spuštěný ve vašem účtu Azure. Pokud ještě nemáte cluster AKS, vytvořte ho pomocí [Azure CLI][cli-quickstart] nebo [Azure Portal][portal-quickstart]. Protokoly Azure Monitor fungují s clustery AKS s povoleným Kubernetes RBAC, Azure RBAC i bez RBAC.

## <a name="enable-resource-logs"></a>Povolení protokolů prostředků

Aby bylo možné získat a zkontrolovat data z více zdrojů, Azure Monitor protokolů poskytuje dotazovací jazyk a analytický modul, který poskytuje přehledy pro vaše prostředí. Pracovní prostor se používá k kompletování a analýze dat a může se integrovat s dalšími službami Azure, jako jsou Application Insights a Security Center. Pokud chcete k analýze protokolů použít jinou platformu, můžete místo toho Odeslat protokoly prostředků do účtu služby Azure Storage nebo centra událostí. Další informace najdete v tématu [co je Azure monitor protokolů?][log-analytics-overview].

Protokoly Azure Monitor jsou v Azure Portal povolené a spravované. Chcete-li povolit shromažďování protokolů pro hlavní komponenty Kubernetes v clusteru AKS, otevřete Azure Portal ve webovém prohlížeči a proveďte následující kroky:

1. Vyberte skupinu prostředků pro cluster AKS, například *myResourceGroup*. Nevybírejte skupinu prostředků, která obsahuje vaše jednotlivé prostředky clusteru AKS, například *MC_myResourceGroup_myAKSCluster_eastus*.
1. Na levé straně vyberte **nastavení diagnostiky**.
1. Vyberte cluster AKS, jako je například *myAKSCluster*, a pak zvolte **Přidání nastavení diagnostiky**.
1. Zadejte název, třeba *myAKSClusterLogs*, a pak vyberte možnost **odeslání do Log Analytics**.
1. Vyberte existující pracovní prostor nebo vytvořte nový. Pokud vytváříte pracovní prostor, zadejte název pracovního prostoru, skupinu prostředků a umístění.
1. V seznamu dostupných protokolů vyberte protokoly, které chcete povolit. V tomto příkladu povolte protokoly *Kube-audit* a *Kube-audit-admin* . Mezi běžné protokoly patří *Kube-apiserver*, *Kube-Controller-Manager* a *Kube-Scheduler*. Shromážděné protokoly můžete vrátit a změnit, jakmile jsou povolené pracovní prostory Log Analytics.
1. Až budete připraveni, vyberte **Uložit** a povolte shromažďování vybraných protokolů.

## <a name="log-categories"></a>Kategorie protokolů

Kromě záznamů zapsaných v Kubernetes mají protokoly auditu vašeho projektu také záznamy z AKS.

Protokoly auditu se zaznamenávají do tří kategorií: *Kube-audit*, *Kube-audit-admin* a *Guard*.

- Kategorie *Kube-audit* obsahuje všechna data protokolu auditu pro každou událost auditu, včetně *Get*, *list*, *Create*, *Update*, *Delete*, *patch* a *post*.
- Kategorie *Kube-audit-admin* je podmnožinou kategorie protokolu *Kube-audit* . *Kube – audit – správce* zkracuje počet protokolů významně tím, že z protokolu vyloučí události *Get* a *list* audit.
- Kategorie *Guard* je spravovaná pro audity Azure AD a Azure RBAC. Pro spravovanou službu Azure AD: token v, informace o uživateli. Pro Azure RBAC: kontroly přístupu a kontrolují se.

## <a name="schedule-a-test-pod-on-the-aks-cluster"></a>Naplánování testu pod v clusteru AKS

Pokud chcete vygenerovat některé protokoly, vytvořte v clusteru AKS nový. Následující příklad manifestu YAML lze použít k vytvoření základní instance NGINX. Vytvořte soubor s názvem `nginx.yaml` v libovolném editoru a vložte následující obsah:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  nodeSelector:
    "beta.kubernetes.io/os": linux
  containers:
  - name: mypod
    image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
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

Povolení a zobrazení diagnostických protokolů může trvat až 10 minut.

> [!NOTE]
> Pokud potřebujete všechna data protokolu auditu pro dodržování předpisů nebo jiné účely, shromážděte je a uložte do levného úložiště, jako je BLOB Storage. Ke shromáždění a uložení smysluplné sady dat protokolu auditu pro účely monitorování a upozorňování použijte kategorii protokolu *Kube-audit-admin* .

V Azure Portal přejděte na svůj cluster AKS a na levé straně vyberte **protokoly** . Pokud se zobrazí okno *příklady dotazů* , zavřete ho.

Na levé straně vyberte **protokoly**. Chcete-li zobrazit protokoly *Kube-audit* , zadejte do textového pole následující dotaz:

```
AzureDiagnostics
| where Category == "kube-audit"
| project log_s
```

Je nejspíš vráceno mnoho protokolů. Chcete-li určit rozsah dotazu pro zobrazení protokolů NGINX pod vytvořením v předchozím kroku, přidejte další příkaz *WHERE* pro hledání *Nginx* , jak je znázorněno v následujícím příkladu dotazu:

```
AzureDiagnostics
| where Category == "kube-audit"
| where log_s contains "nginx"
| project log_s
```

Pokud chcete zobrazit protokoly *Kube-audit-admin* , zadejte do textového pole tento dotaz:

```
AzureDiagnostics
| where Category == "kube-audit-admin"
| project log_s
```

V tomto příkladu se v dotazu zobrazí všechny úlohy vytváření v *Kube-audit-admin*. Bylo vráceno příliš mnoho výsledků. Chcete-li zobrazit protokoly o NGINX pod vytvořením v předchozím kroku, přidejte do rozsahu dotazu další příkaz *WHERE* *, jak je* znázorněno v následujícím příkladu dotazu.

```
AzureDiagnostics
| where Category == "kube-audit-admin"
| where log_s contains "nginx"
| project log_s
```


Další informace o tom, jak zadávat dotazy a filtrovat data protokolu, najdete v tématu [zobrazení nebo analýza dat shromážděných pomocí prohledávání protokolů Log Analytics][analyze-log-analytics].

## <a name="log-event-schema"></a>Schéma událostí protokolu

AKS zaznamená následující události:

* [AzureActivity][log-schema-azureactivity]
* [AzureDiagnostics][log-schema-azurediagnostics]
* [AzureMetrics][log-schema-azuremetrics]
* [ContainerImageInventory][log-schema-containerimageinventory]
* [ContainerInventory][log-schema-containerinventory]
* [ContainerLog][log-schema-containerlog]
* [ContainerNodeInventory][log-schema-containernodeinventory]
* [ContainerServiceLog][log-schema-containerservicelog]
* [Tep][log-schema-heartbeat]
* [InsightsMetrics][log-schema-insightsmetrics]
* [KubeEvents][log-schema-kubeevents]
* [KubeHealth][log-schema-kubehealth]
* [KubeMonAgentEvents][log-schema-kubemonagentevents]
* [KubeNodeInventory][log-schema-kubenodeinventory]
* [KubePodInventory][log-schema-kubepodinventory]
* [KubeServices][log-schema-kubeservices]
* [Výkon][log-schema-perf]

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
[analyze-log-analytics]: ../azure-monitor/log-query/log-analytics-tutorial.md
[kubelet-logs]: kubelet-logs.md
[aks-ssh]: ssh.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[log-schema-azureactivity]: /azure/azure-monitor/reference/tables/azureactivity
[log-schema-azurediagnostics]: /azure/azure-monitor/reference/tables/azurediagnostics
[log-schema-azuremetrics]: /azure/azure-monitor/reference/tables/azuremetrics
[log-schema-containerimageinventory]: /azure/azure-monitor/reference/tables/containerimageinventory
[log-schema-containerinventory]: /azure/azure-monitor/reference/tables/containerinventory
[log-schema-containerlog]: /azure/azure-monitor/reference/tables/containerlog
[log-schema-containernodeinventory]: /azure/azure-monitor/reference/tables/containernodeinventory
[log-schema-containerservicelog]: /azure/azure-monitor/reference/tables/containerservicelog
[log-schema-heartbeat]: /azure/azure-monitor/reference/tables/heartbeat
[log-schema-insightsmetrics]: /azure/azure-monitor/reference/tables/insightsmetrics
[log-schema-kubeevents]: /azure/azure-monitor/reference/tables/kubeevents
[log-schema-kubehealth]: /azure/azure-monitor/reference/tables/kubehealth
[log-schema-kubemonagentevents]: /azure/azure-monitor/reference/tables/kubemonagentevents
[log-schema-kubenodeinventory]: /azure/azure-monitor/reference/tables/kubenodeinventory
[log-schema-kubepodinventory]: /azure/azure-monitor/reference/tables/kubepodinventory
[log-schema-kubeservices]: /azure/azure-monitor/reference/tables/kubeservices
[log-schema-perf]: /azure/azure-monitor/reference/tables/perf