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
ms.openlocfilehash: 44803338a27fc492f4dc896a0edb398b2ce486ea
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/07/2020
ms.locfileid: "87926123"
---
# <a name="deploy-helm-charts-using-gitops-on-arc-enabled-kubernetes-cluster-preview"></a>Nasazení grafů Helm pomocí GitOps v clusteru Kubernetes s povoleným ARC (Preview)

Helm je open source nástroj pro balení, který vám pomůže s instalací a správou životního cyklu aplikací Kubernetes. Podobně jako správci balíčků pro Linux, jako je APT a Yumu, se Helm používá ke správě Kubernetes grafů, což jsou balíčky předkonfigurovaných prostředků Kubernetes.

V tomto článku se dozvíte, jak nakonfigurovat a používat Helm s povoleným Kubernetes ARC Azure.

## <a name="before-you-begin"></a>Než začnete

V tomto článku se předpokládá, že máte existující cluster Kubernetes s povoleným připojením k Arc Azure. Pokud potřebujete připojený cluster, přečtěte si [rychlý Start pro připojení clusteru](./connect-cluster.md).

Nejdřív nanastavíme proměnné prostředí, které se použijí v celém tomto kurzu. Pro připojený cluster budete potřebovat název skupiny prostředků a název clusteru.

```bash
export RESOURCE_GROUP=<Resource_Group_Name>
export CLUSTER_NAME=<ClusterName>
```

## <a name="verify-your-cluster-is-enabled-with-arc"></a>Ověření, jestli je cluster povolený pomocí ARC

```bash
az connectedk8s list -g $RESOURCE_GROUP -o table
```

Výstup:
```bash
Name           Location    ResourceGroup
-------------  ----------  ---------------
arc-helm-demo  eastus      k8s-clusters
```

## <a name="overview-of-using-gitops-and-helm-with-azure-arc-enabled-kubernetes"></a>Přehled použití GitOps a Helm s povoleným Kubernetes ARC Azure

 Operátor Helm poskytuje rozšíření pro tok, který automatizuje vydávání grafů Helm. Vydaná verze grafu je popsána prostřednictvím vlastního prostředku Kubernetes s názvem HelmRelease. Tok synchronizuje Tyto prostředky z Gitu do clusteru a operátor Helm zajišťuje, aby byly grafy Helm vydávány tak, jak jsou uvedeny v prostředcích.

 Níže je uvedená ukázková struktura úložiště Git, kterou budeme používat v tomto kurzu:

```bash
├── charts
│   └── azure-vote
│       ├── Chart.yaml
│       ├── templates
│       │   ├── NOTES.txt
│       │   ├── deployment.yaml
│       │   └── service.yaml
│       └── values.yaml
└── releases
    └── vote-app.yaml
```

V úložišti Git máme dva adresáře, jeden obsahující Helmový graf a druhý, který obsahuje konfiguraci releases. V `releases` adresáři `vote-app.yaml` obsahuje konfiguraci HelmRelease, která je uvedená níže:

```bash
apiVersion: helm.fluxcd.io/v1
kind: HelmRelease
metadata:
  name: vote-app
  namespace: arc-k8s-demo
spec:
  releaseName: arc-k8s-demo
  chart:
    git: https://github.com/Azure/arc-helm-demo
    ref: master
    path: charts/azure-vote
  values:
    frontendServiceName: arc-k8s-demo-vote-front
```

Konfigurace verze Helm obsahuje následující pole:

- `metadata.name`je povinný a musí dodržovat zásady vytváření názvů Kubernetes.
- `metadata.namespace`je volitelný a určuje, kde je vydaná verze vytvořena.
- `spec.releaseName`je volitelné, a pokud není k dispozici, bude název verze $namespace-$name
- `spec.chart.path`je adresář obsahující graf vzhledem k kořenu úložiště.
- `spec.values`vlastní hodnoty parametrů ze samotného grafu vlastní uživatel

Možnosti zadané v HelmRelease spec. Values přepíše možnosti zadané v hodnotách. yaml ze zdroje grafu.

Další informace o HelmRelease najdete v dokumentaci k oficiálnímu [operátorovi Helm](https://docs.fluxcd.io/projects/helm-operator/en/stable/) .

## <a name="create-a-configuration"></a>Vytvořit konfiguraci

Pomocí rozšíření Azure CLI pro `k8sconfiguration` , pojďme propojit náš připojený cluster s ukázkovým úložištěm Git. Této konfiguraci přiřadíme název `azure-voting-app` a nasadíme operátor toku do `arc-k8s-demo` oboru názvů.

```bash
az k8sconfiguration create --name azure-voting-app \
  --resource-group $RESOURCE_GROUP --cluster-name $CLUSTER_NAME \
  --operator-instance-name flux --operator-namespace arc-k8s-demo \
  --operator-params='--git-readonly --git-path=releases' \
  --enable-helm-operator --helm-operator-version='0.6.0' \
  --helm-operator-params='--set helm.versions=v3' \
  --repository-url https://github.com/Azure/arc-helm-demo.git  \
  --scope namespace --cluster-type connectedClusters
```

### <a name="configuration-parameters"></a>Parametry konfigurace

Pokud chcete vytvořit konfiguraci, [Přečtěte si informace o dalších parametrech, které můžete použít](./use-gitops-connected-cluster.md#additional-parameters).

## <a name="validate-the-configuration"></a>Ověření konfigurace

Pomocí Azure CLI ověřte, že se `sourceControlConfiguration` úspěšně vytvořil.

```console
az k8sconfiguration show --resource-group $RESOURCE_GROUP --name azure-voting-app --cluster-name $CLUSTER_NAME --cluster-type connectedClusters
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
  "id": "/subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1/providers/Microsoft.KubernetesConfiguration/sourceControlConfigurations/azure-voting-app",
  "name": "azure-voting-app",
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

Spusťte následující příkaz a přejděte do `localhost:3000` prohlížeče, abyste ověřili, že je aplikace spuštěná.

```bash
kubectl port-forward -n arc-k8s-demo svc/arc-k8s-demo-vote-front 3000:80
```

## <a name="next-steps"></a>Další kroky

- [Řízení konfigurace clusteru pomocí Azure Policy](./use-azure-policy.md)
