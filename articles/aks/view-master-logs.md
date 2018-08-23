---
title: Zobrazit protokoly kontroleru Azure Kubernetes Service (AKS)
description: Další informace o povolení a zobrazení protokolů pro hlavní uzel Kubernetes ve službě Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 07/26/2018
ms.author: iainfou
ms.openlocfilehash: 04afd71183bcb8001d017b0027f29338b8d67ddb
ms.sourcegitcommit: fab878ff9aaf4efb3eaff6b7656184b0bafba13b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2018
ms.locfileid: "42442363"
---
# <a name="enable-and-review-kubernetes-master-node-logs-in-azure-kubernetes-service-aks"></a>Povolit a zkontrolovat Kubernetes hlavní uzel protokolů ve službě Azure Kubernetes Service (AKS)

S Azure Kubernetes Service (AKS), hlavní součásti, jako *kube apiserver* a *správce kontroléru kube* jsou k dispozici jako spravovaná služba. Vytvořit a spravovat uzly, které běží *kubelet* a kontejner modulu runtime a nasadit vaše aplikace prostřednictvím spravovaného serveru Kubernetes API. K řešení potíží se vaše aplikace a služby, můžete zobrazit protokoly generované tyto hlavní součásti. V tomto článku se dozvíte, jak povolit a dotazování protokolů z hlavní součásti Kubernetes pomocí Azure Log Analytics.

## <a name="before-you-begin"></a>Než začnete

Tento článek vyžaduje existující cluster AKS spuštěné v účtu Azure. Pokud již nemáte AKS cluster, vytvořte ji pomocí [rozhraní příkazového řádku Azure] [ cli-quickstart] nebo [webu Azure portal][portal-quickstart]. Log Analytics spolupracuje s oběma RBAC a není RBAC povolené AKS clustery.

## <a name="enable-diagnostics-logs"></a>Povolení diagnostických protokolů

Abychom shromáždit a zkontrolovat data z víc zdrojů, Log Analytics poskytuje dotazovací jazyk a analytický modul, který poskytuje přehledy pro vaše prostředí. Pracovní prostor se používá k porovnání a analyzovat data a můžete integrovat s dalšími službami Azure, jako je například služba Application Insights a Security Center. Pokud chcete použít k analýze protokolů různé platformy, místo toho můžete odesílání diagnostických protokolů do služby Azure storage účet nebo event hub. Další informace najdete v tématu [co je Azure Log Analytics?] [log-analytics-overview].

Log Analytics je povolit a spravovat na webu Azure Portal. Pokud chcete povolit shromažďování protokolů pro Kubernetes hlavní součásti v clusteru AKS, otevřete ve webovém prohlížeči na webu Azure portal a proveďte následující kroky:

1. Vyberte skupinu prostředků pro váš cluster AKS, jako je například *myResourceGroup*. Nevybírejte skupinu prostředků, která obsahuje vaše vybrané prostředky clusteru AKS, jako například *MC_myResourceGroup_myAKSCluster_eastus*.
1. Na levé straně zvolte **nastavení diagnostiky**.
1. Vyberte váš cluster AKS, jako je například *myAKSCluster*, pak se rozhodnout **zapnout diagnostiku**.
1. Zadejte název, jako například *myAKSLogs*, vyberte možnost **odesílat do Log Analytics**.
    * Zvolit *konfigurovat* Log Analytics, vyberte existující pracovní prostor nebo **vytvořit nový pracovní prostor**.
    * Pokud je potřeba vytvořit pracovní prostor, zadejte název, skupinu prostředků a umístění.
1. V seznamu dostupných protokolů, vyberte protokoly, které chcete povolit, jako například *kube apiserver*, *správce kontroléru kube*, a *kube Plánovač*. Můžete vrátit a změnit shromažďovat protokoly, jakmile budou povolené Log Analytics.
1. Až to budete mít, vyberte **Uložit** povolení kolekce vybraných protokolů.

Následující příklad ukazuje snímek obrazovky portálu *nastavení diagnostiky* časového intervalu a poté na možnost vytvořit pracovní prostor OMS:

![Povolit pracovní prostor OMS pro cluster Log Analytics AKS](media/view-master-logs/enable-oms-log-analytics.png)

## <a name="schedule-a-test-pod-on-the-aks-cluster"></a>Plánování testů pod v clusteru AKS

Generovat některé protokoly, vytvořte nový pod ve vašem clusteru AKS. Následující příklad YAML manifestu slouží k vytvoření základní instance NGINX. Vytvořte soubor s názvem `nginx.yaml` v editoru podle vašeho výběru a vložte následující obsah:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  - name: myfrontend
    image: nginx
    ports:
    - containerPort: 80
```

Vytvořte pod s [kubectl vytvořit] [ kubectl-create] příkaz a zadejte svůj soubor YAML, jak je znázorněno v následujícím příkladu:

```
$ kubectl create -f nginx.yaml

pod/nginx created
```

## <a name="view-collected-logs"></a>Zobrazení shromážděných protokolů

Může trvat několik minut, než se diagnostické protokoly povolena a zobrazí v pracovním prostoru OMS. Na webu Azure Portal, vyberte skupinu prostředků pro váš pracovní prostor Log Analytics, jako je například *myResourceGroup*, klikněte na tlačítko prostředku Log Analytics, jako například *myAKSLogs*.

![Vyberte pracovní prostor Log Analytics pro váš cluster AKS](media/view-master-logs/select-log-analytics-workspace.png)

Na levé straně zvolte **prohledávání protokolů**. Chcete-li zobrazit *kube apiserver*, v textovém poli zadejte následující dotaz:

```
search *
| where Type == "AzureDiagnostics"
| where Category == "kube-apiserver"
| project log_s
```

Mnoho protokolů jsou pravděpodobně vrátil serveru rozhraní API. K určení oboru dolů dotazu k zobrazení protokolů o pod NGINX vytvořili v předchozím kroku, přidat další *kde* příkazu k vyhledání *podů/nginx* jak ukazuje následující příklad dotazu:

```
search *
| where Type == "AzureDiagnostics"
| where Category == "kube-apiserver"
| where log_s contains "pods/nginx"
| project log_s
```

Jsou zobrazeny konkrétní protokoly pro podu NGINX, jak je znázorněno v následujícím ukázkovém snímku obrazovky:

![Výsledky pro pod NGINX ukázka dotazu log Analytics](media/view-master-logs/log-analytics-query-results.png)

Chcete-li zobrazit další protokoly, můžete aktualizovat dotaz pro *kategorie* název pro *kube správce kontroléru* nebo *kube Plánovač*, v závislosti na tom, jaké další protokoly můžete Povolte. Další *kde* příkazy lze pak použít pro upřesnění události, které hledáte.

Další informace o tom, jak dotazování a filtrování dat protokolu najdete v části [zobrazení nebo analýza shromážděných dat pomocí prohledávání protokolu log Analytics][analyze-log-analytics].

## <a name="log-event-schema"></a>Schéma událostí protokolu

A pomáhá tak analyzovat data protokolu, následující tabulka obsahuje podrobnosti o schéma používané pro každou jednotlivou událost:

| Název pole               | Popis |
|--------------------------|-------------|
| *ID prostředku*             | Prostředek Azure, který vytváří protokolu |
| *čas*                   | Pokud byl nahrán do protokolu časové razítko |
| *Kategorie*               | Název kontejneru nebo komponenty generování protokolu |
| *OperationName*          | Vždy *Microsoft.ContainerService/managedClusters/diagnositicLogs/Read* |
| *Properties.log*         | Úplný text protokolu z komponenty |
| *Properties.Stream*      | *STDERR* nebo *stdout* |
| *Properties.pod*         | Pod názvem, která v protokolu pochází z |
| *properties.containerID* | ID, které tento protokol pochází z kontejneru dockeru |

## <a name="next-steps"></a>Další postup

V tomto článku jste zjistili, jak povolit a zkontrolovat protokoly pro Kubernetes hlavní součásti v clusteru AKS. Ke sledování a řešení potíží s další, můžete také [zobrazení protokolů Kubelet] [ kubelet-logs] a [povolit přístup k uzlu SSH][aks-ssh].

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create

<!-- LINKS - internal -->
[cli-quickstart]: kubernetes-walkthrough.md
[portal-quickstart]: kubernetes-walkthrough-portal.md
[log-analytics-overview]: ../log-analytics/log-analytics-overview.md
[analyze-log-analytics]: ../log-analytics/log-analytics-tutorial-viewdata.md
[kubelet-logs]: kubelet-logs.md
[aks-ssh]: ssh.md