---
title: Použití GitOps s Helm pro konfiguraci clusteru s podporou ARC Azure (Preview)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Použití GitOps s Helm pro konfiguraci clusteru s podporou ARC Azure (Preview)
keywords: GitOps, Kubernetes, K8s, Azure, Helm, ARC, AKS, Azure Kubernetes Service, Containers
ms.openlocfilehash: 677c5f2b27794ebea9d38e470b5e1a5ba12bff7e
ms.sourcegitcommit: 9b5c20fb5e904684dc6dd9059d62429b52cb39bc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85857230"
---
# <a name="use-gitops-with-helm-for-an-azure-arc-enabled-cluster-configuration-preview"></a>Použití GitOps s Helm pro konfiguraci clusteru s podporou ARC Azure (Preview)

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

## <a name="overview-of-using-helm-with-azure-arc-enabled-kubernetes"></a>Přehled použití Helm s povoleným Kubernetes ARC Azure

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
    └── prod
        └── azure-vote-app.yaml
```

V úložišti Git máme dva adresáře, jeden obsahující Helmový graf a druhý, který obsahuje konfiguraci releases. V `releases/prod` adresáři `azure-vote-app.yaml` obsahuje konfiguraci HelmRelease, která je uvedená níže:

```bash
 apiVersion: helm.fluxcd.io/v1
 kind: HelmRelease
 metadata:
   name: azure-vote-app
   namespace: prod
   annotations:
 spec:
   releaseName: azure-vote-app
   chart:
     git: https://github.com/Azure/arc-helm-demo
     path: charts/azure-vote
     ref: master
   values:
     image:
       repository: dstrebel/azurevote
       tag: v1
     replicaCount: 3
```

Konfigurace verze Helm obsahuje následující pole:

- `metadata.name`je povinný a musí dodržovat zásady vytváření názvů Kubernetes.
- `metadata.namespace`je volitelný a určuje, kde je vydaná verze vytvořena.
- `spec.releaseName`je volitelné, a pokud není k dispozici, bude název verze $namespace-$name
- `spec.chart.path`je adresář obsahující graf vzhledem k kořenu úložiště.
- `spec.values`vlastní hodnoty parametrů ze samotného grafu vlastní uživatel

Možnosti zadané v HelmRelease spec. Values přepíše možnosti zadané v hodnotách. yaml ze zdroje grafu.

Další informace o HelmRelease najdete v dokumentaci k oficiálnímu [operátorovi Helm](https://docs.fluxcd.io/projects/helm-operator/en/1.0.0-rc9/references/helmrelease-custom-resource.html) .

## <a name="create-a-configuration"></a>Vytvořit konfiguraci

Pomocí rozšíření Azure CLI pro `k8sconfiguration` , pojďme propojit náš připojený cluster s ukázkovým úložištěm Git. Této konfiguraci přiřadíme název `azure-voting-app` a nasadíme operátor toku do `prod` oboru názvů.

```bash
az k8sconfiguration create --name azure-voting-app --resource-group  $RESOURCE_GROUP --cluster-name $CLUSTER_NAME --operator-instance-name azure-voting-app --operator-namespace prod --enable-helm-operator --helm-operator-version='0.6.0' --helm-operator-params='--set helm.versions=v3' --repository-url https://github.com/Azure/arc-helm-demo.git --operator-params='--git-readonly --git-path=releases/prod' --scope namespace --cluster-type connectedClusters
```

### <a name="configuration-parameters"></a>Parametry konfigurace

Pokud chcete vytvořit konfiguraci, [Přečtěte si informace o dalších parametrech, které můžete použít](./use-gitops-connected-cluster.md#additional-parameters).


## <a name="validate-the-configuration"></a>Ověření konfigurace

Pomocí Azure CLI ověřte, že se `sourceControlConfiguration` úspěšně vytvořil.

```console
az k8sconfiguration show --resource-group $RESOURCE_GROUP --name azure-voting-app --cluster-name $CLUSTER_NAME --cluster-type connectedClusters
```

Všimněte si, že `sourceControlConfiguration` prostředek je aktualizovaný se stavem dodržování předpisů, zprávami a informacemi o ladění.

**Výkonem**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
{
  "complianceStatus": {
    "complianceState": "Installed",
    "lastConfigApplied": "2019-12-05T05:34:41.481000",
    "message": "...",
    "messageLevel": "3"
  },
  "id": "/subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1/providers/Microsoft.KubernetesConfiguration/sourceControlConfigurations/cluster-config",
  "name": "azure-vote-app",
  "operatorInstanceName": "cluster-config",
  "operatorNamespace": "prod",
  "operatorParams": "--git-readonly --git-path=releases/prod",
  "operatorScope": "namespace",
  "operatorType": "Flux",
  "provisioningState": "Succeeded",
  "repositoryPublicKey": "...",
  "repositoryUrl": "git://github.com/Azure/arc-helm-demo.git",
  "resourceGroup": "AzureArcTest",
  "type": "Microsoft.KubernetesConfiguration/sourceControlConfigurations"
}
```

## <a name="validate-application"></a>Ověřit aplikaci

Nyní zkontrolujeme, jestli je aplikace spuštěná, a to získáním IP adresy služby.

```bash
kubectl get svc/azure-vote-front -n prod
```

**Výkonem**

```bash
NAME               TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.14.161   52.186.160.216   80:30372/TCP   4d22h
```

Vyhledejte externí IP adresu z výstupu výše a otevřete ji v prohlížeči.

## <a name="next-steps"></a>Další kroky

- [Řízení konfigurace clusteru pomocí Azure Policy](./use-azure-policy.md)
