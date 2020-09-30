---
title: Nasazení grafů Helm pomocí GitOps v clusteru Kubernetes s povoleným ARC (Preview)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Použití GitOps s Helm pro konfiguraci clusteru s podporou ARC Azure (Preview)
keywords: GitOps, Kubernetes, K8s, Azure, Helm, ARC, AKS, Azure Kubernetes Service, Containers
ms.openlocfilehash: eea81d458ac6631c4a023134b3198e4cdb04526e
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91541607"
---
# <a name="deploy-helm-charts-using-gitops-on-arc-enabled-kubernetes-cluster-preview"></a>Nasazení grafů Helm pomocí GitOps v clusteru Kubernetes s povoleným ARC (Preview)

Helm je open source nástroj pro balení, který vám pomůže s instalací a správou životního cyklu aplikací Kubernetes. Podobně jako správci balíčků pro Linux, jako je APT a Yumu, se Helm používá ke správě Kubernetes grafů, což jsou balíčky předem nakonfigurovaných prostředků Kubernetes.

V tomto článku se dozvíte, jak nakonfigurovat a používat Helm s povoleným Kubernetes ARC Azure.

## <a name="before-you-begin"></a>Než začnete

V tomto článku se předpokládá, že máte existující cluster Kubernetes s povoleným připojením k Arc Azure. Pokud potřebujete připojený cluster, přečtěte si [rychlý Start pro připojení clusteru](./connect-cluster.md).

## <a name="overview-of-using-gitops-and-helm-with-azure-arc-enabled-kubernetes"></a>Přehled použití GitOps a Helm s povoleným Kubernetes ARC Azure

 Operátor Helm poskytuje rozšíření pro tok, který automatizuje vydávání grafů Helm. Vydaná verze grafu je popsána prostřednictvím vlastního prostředku Kubernetes s názvem HelmRelease. Tok synchronizuje Tyto prostředky z Gitu do clusteru a operátor Helm zajišťuje, aby byly grafy Helm vydávány tak, jak jsou uvedeny v prostředcích.

 [Ukázkové úložiště](https://github.com/Azure/arc-helm-demo) použité v tomto dokumentu je strukturované následujícím způsobem:

```console
├── charts
│   └── azure-arc-sample
│       ├── Chart.yaml
│       ├── templates
│       │   ├── NOTES.txt
│       │   ├── deployment.yaml
│       │   └── service.yaml
│       └── values.yaml
└── releases
    └── app.yaml
```

V úložišti Git máme dva adresáře, jeden obsahující Helmový graf a druhý, který obsahuje konfiguraci releases. V `releases` adresáři `app.yaml` obsahuje konfiguraci HelmRelease, která je uvedená níže:

```yaml
apiVersion: helm.fluxcd.io/v1
kind: HelmRelease
metadata:
  name: azure-arc-sample
  namespace: arc-k8s-demo
spec:
  releaseName: arc-k8s-demo
  chart:
    git: https://github.com/Azure/arc-helm-demo
    ref: master
    path: charts/azure-arc-sample
  values:
    serviceName: arc-k8s-demo
```

Konfigurace verze Helm obsahuje následující pole:

- `metadata.name` je povinný a musí dodržovat zásady vytváření názvů Kubernetes.
- `metadata.namespace` je volitelný a určuje, kde je vydaná verze vytvořena.
- `spec.releaseName` je volitelné, a pokud není k dispozici, bude název verze $namespace-$name
- `spec.chart.path` je adresář obsahující graf vzhledem k kořenu úložiště.
- `spec.values` vlastní hodnoty parametrů ze samotného grafu vlastní uživatel

Možnosti zadané v HelmRelease spec. Values přepíše možnosti zadané v hodnotách. yaml ze zdroje grafu.

Další informace o HelmRelease najdete v dokumentaci k oficiálnímu [operátorovi Helm](https://docs.fluxcd.io/projects/helm-operator/en/stable/) .

## <a name="create-a-configuration"></a>Vytvořit konfiguraci

Pomocí rozšíření Azure CLI pro `k8sconfiguration` , pojďme propojit náš připojený cluster s ukázkovým úložištěm Git. Této konfiguraci přiřadíme název `azure-arc-sample` a nasadíme operátor toku do `arc-k8s-demo` oboru názvů.

```console
az k8sconfiguration create --name azure-arc-sample --cluster-name AzureArcTest1 --resource-group AzureArcTest --operator-instance-name flux --operator-namespace arc-k8s-demo --operator-params='--git-readonly --git-path=releases' --enable-helm-operator --helm-operator-version='0.6.0' --helm-operator-params='--set helm.versions=v3' --repository-url https://github.com/Azure/arc-helm-demo.git --scope namespace --cluster-type connectedClusters
```

### <a name="configuration-parameters"></a>Parametry konfigurace

Pokud chcete vytvořit konfiguraci, [Přečtěte si informace o dalších parametrech, které můžete použít](./use-gitops-connected-cluster.md#additional-parameters).

## <a name="validate-the-configuration"></a>Ověření konfigurace

Pomocí Azure CLI ověřte, že se `sourceControlConfiguration` úspěšně vytvořil.

```console
az k8sconfiguration show --name azure-arc-sample --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

`sourceControlConfiguration`Prostředek se aktualizuje se stavem dodržování předpisů, zprávami a informacemi o ladění.

**Výkonem**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
{
  "complianceStatus": {
    "complianceState": "Installed",
    "lastConfigApplied": "2019-12-05T05:34:41.481000",
    "message": "{\"OperatorMessage\":null,\"ClusterState\":null}",
    "messageLevel": "3"
  },
  "enableHelmOperator": "True",
  "helmOperatorProperties": {
    "chartValues": "--set helm.versions=v3",
    "chartVersion": "0.6.0"
  },
  "id": "/subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1/providers/Microsoft.KubernetesConfiguration/sourceControlConfigurations/azure-arc-sample",
  "name": "azure-arc-sample",
  "operatorInstanceName": "flux",
  "operatorNamespace": "arc-k8s-demo",
  "operatorParams": "--git-readonly --git-path=releases",
  "operatorScope": "namespace",
  "operatorType": "Flux",
  "provisioningState": "Succeeded",
  "repositoryPublicKey": "",
  "repositoryUrl": "https://github.com/Azure/arc-helm-demo.git",
  "resourceGroup": "AzureArcTest",
  "type": "Microsoft.KubernetesConfiguration/sourceControlConfigurations"
}
```

## <a name="validate-application"></a>Ověřit aplikaci

Spusťte následující příkaz a přejděte do `localhost:8080` prohlížeče, abyste ověřili, že je aplikace spuštěná.

```console
kubectl port-forward -n arc-k8s-demo svc/arc-k8s-demo 8080:8080
```

## <a name="next-steps"></a>Další kroky

- [Řízení konfigurace clusteru pomocí Azure Policy](./use-azure-policy.md)
