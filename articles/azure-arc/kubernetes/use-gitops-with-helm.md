---
title: Nasazení grafů Helm pomocí GitOps v clusteru Kubernetes s povoleným ARC
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: article
author: mlearned
ms.author: mlearned
description: Použití GitOps s Helm pro konfiguraci clusteru s povoleným obloukem Azure
keywords: GitOps, Kubernetes, K8s, Azure, Helm, ARC, AKS, Azure Kubernetes Service, Containers
ms.openlocfilehash: 75e2fcb25680817fc3e2bddabbbdd9c52b7dd059
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102121401"
---
# <a name="deploy-helm-charts-using-gitops-on-an-arc-enabled-kubernetes-cluster"></a>Nasazení grafů Helm pomocí GitOps u clusteru Kubernetes s povoleným obloukem

Helm je opensourcový nástroj pro správu balíčků, který vám pomůže s instalací a správou životního cyklu aplikací Kubernetes. Podobně jako správci balíčků pro Linux, jako je APT a Yumu, se Helm používá ke správě Kubernetes grafů, což jsou balíčky předem nakonfigurovaných prostředků Kubernetes.

V tomto článku se dozvíte, jak nakonfigurovat a používat Helm s povoleným Kubernetes ARC Azure.

## <a name="before-you-begin"></a>Než začnete

Ověřte, že máte existující cluster Kubernetes s povoleným připojením k Azure ARC. Pokud potřebujete připojený cluster, přečtěte si [rychlý Start clusteru Kubernetes s povoleným připojením Azure ARC](./quickstart-connect-cluster.md).

## <a name="overview-of-using-gitops-and-helm-with-azure-arc-enabled-kubernetes"></a>Přehled použití GitOps a Helm s povoleným Kubernetes ARC Azure

 Operátor Helm poskytuje rozšíření pro tok, který automatizuje vydávání grafů Helm. Vydaná verze grafu Helm je popsaná prostřednictvím vlastního prostředku Kubernetes s názvem HelmRelease. Tok synchronizuje Tyto prostředky z Gitu do clusteru, zatímco operátor Helm zajišťuje, aby byly v prostředcích vydávány grafy Helm, jak je uvedeno v těchto zdrojích.

 [Ukázkové úložiště](https://github.com/Azure/arc-helm-demo) , které se používá v tomto článku, je strukturované následujícím způsobem:

```console
├── charts
│   └── azure-arc-sample
│       ├── Chart.yaml
│       ├── templates
│       │   ├── NOTES.txt
│       │   ├── deployment.yaml
│       │   └── service.yaml
│       └── values.yaml
└── releases
    └── app.yaml
```

V úložišti Git máme dva adresáře: jeden obsahující graf Helm a druhý obsahující konfiguraci releases. V `releases` adresáři `app.yaml` obsahuje konfiguraci HelmRelease, která je zobrazena níže:

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

| Pole | Popis |
| ------------- | ------------- | 
| `metadata.name` | Povinné pole Musí dodržovat zásady vytváření názvů Kubernetes. |
| `metadata.namespace` | Volitelné pole. Určuje, kde je vydaná verze vytvořena. |
| `spec.releaseName` | Volitelné pole. Pokud není zadán, bude název verze `$namespace-$name` . |
| `spec.chart.path` | Adresář obsahující graf (relativní vzhledem k kořenovému adresáři úložiště) |
| `spec.values` | Uživatelské přizpůsobení výchozích hodnot parametrů ze samotného grafu |

Možnosti zadané ve HelmRelease `spec.values` přepíšou možnosti zadané v `values.yaml` ze zdroje grafu.

Další informace o HelmRelease najdete v dokumentaci k oficiálnímu [operátorovi Helm](https://docs.fluxcd.io/projects/helm-operator/en/stable/).

## <a name="create-a-configuration"></a>Vytvořit konfiguraci

Pomocí rozšíření Azure CLI pro proveďte `k8s-configuration` propojení připojeného clusteru s ukázkovým úložištěm Git. Dejte této konfiguraci název `azure-arc-sample` a nasaďte operátor toku do `arc-k8s-demo` oboru názvů.

```console
az k8s-configuration create --name azure-arc-sample --cluster-name AzureArcTest1 --resource-group AzureArcTest --operator-instance-name flux --operator-namespace arc-k8s-demo --operator-params='--git-readonly --git-path=releases' --enable-helm-operator --helm-operator-chart-version='1.2.0' --helm-operator-params='--set helm.versions=v3' --repository-url https://github.com/Azure/arc-helm-demo.git --scope namespace --cluster-type connectedClusters
```

### <a name="configuration-parameters"></a>Parametry konfigurace

Chcete-li přizpůsobit vytvoření konfigurace, [Přečtěte si o dalších parametrech](./tutorial-use-gitops-connected-cluster.md#additional-parameters).

## <a name="validate-the-configuration"></a>Ověření konfigurace

Pomocí Azure CLI ověřte, že se konfigurace úspěšně vytvořila.

```console
az k8s-configuration show --name azure-arc-sample --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

Prostředek konfigurace se aktualizuje se stavem dodržování předpisů, zprávami a informacemi o ladění.

```output
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
    "chartVersion": "1.2.0"
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

Použijte konfiguraci clusteru ve velkém měřítku pomocí [Azure Policy](./use-azure-policy.md).
