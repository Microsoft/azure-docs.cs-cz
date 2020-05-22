---
title: Připojení clusteru Kubernetes s povoleným ARC Azure (Preview)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Připojení clusteru Kubernetes s povoleným ARC Azure pomocí ARC Azure
keywords: Kubernetes, oblouk, Azure, K8s, Containers
ms.openlocfilehash: 690955f0e7d18f9a784b4c9a2fa1733442cb70dc
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/22/2020
ms.locfileid: "83780023"
---
# <a name="connect-an-azure-arc-enabled-kubernetes-cluster-preview"></a>Připojení clusteru Kubernetes s povoleným ARC Azure (Preview)

Připojte cluster Kubernetes ke službě Azure ARC. 

## <a name="before-you-begin"></a>Před zahájením

Ověřte, že máte připravené tyto požadavky:

* Cluster Kubernetes, který je v provozu
* Budete potřebovat přístup s kubeconfig a přístupem správce clusteru. 
* Uživatel nebo instanční objekt použitý s `az login` příkazy a `az connectedk8s connect` musí mít oprávnění číst a zapsat pro typ prostředku Microsoft. Kubernetes/connectedclusters. Roli "připojení Azure ARC pro Kubernetes s těmito oprávněními" lze použít pro přiřazení rolí pro uživatele nebo instanční objekt používaný s Azure CLI pro registraci.
* Nejnovější verze rozšíření *connectedk8s* a *k8sconfiguration*

## <a name="supported-regions"></a>Podporované oblasti

* USA – východ
* Západní Evropa

## <a name="network-requirements"></a>Požadavky sítě

Agenti Azure ARC vyžadují, aby následující protokoly/porty/odchozí adresy URL fungovaly.

* TCP na portu 443 – >`https://:443`
* TCP na portu 9418 – >`git://:9418`

| Koncový bod (DNS)                                                                                               | Popis                                                                                                                 |
| ------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------- |
| `https://management.azure.com`                                                                                 | Vyžaduje se, aby se agent připojil k Azure a zaregistroval cluster.                                                        |
| `https://eastus.dp.kubernetesconfiguration.azure.com`, `https://westeurope.dp.kubernetesconfiguration.azure.com` | Koncový bod roviny dat pro agenta, aby načetl stav a načetl informace o konfiguraci                                      |
| `https://docker.io`                                                                                            | Požadováno pro načtení imagí kontejneru                                                                                         |
| `https://github.com`, git://github.com                                                                         | Příklady úložišť GitOps jsou hostované na GitHubu. Agent konfigurace vyžaduje připojení k libovolnému koncovému bodu Git, který zadáte. |
| `https://login.microsoftonline.com`                                                                            | Požadováno pro načtení a aktualizaci tokenů Azure Resource Manager                                                                                    |
| `https://azurearcfork8s.azurecr.io`                                                                            | Požadováno pro získání imagí kontejneru pro agenty Azure ARC                                                                  |

## <a name="register-the-two-providers-for-azure-arc-enabled-kubernetes"></a>Zaregistrujte dva poskytovatele pro Kubernetes s povoleným ARC Azure:

```console
az provider register --namespace Microsoft.Kubernetes
Registering is still on-going. You can monitor using 'az provider show -n Microsoft.Kubernetes'

az provider register --namespace Microsoft.KubernetesConfiguration
Registering is still on-going. You can monitor using 'az provider show -n Microsoft.KubernetesConfiguration'
```

Registrace je asynchronní proces. Registrace může trvat přibližně 10 minut. Proces registrace můžete monitorovat pomocí následujících příkazů:

```console
az provider show -n Microsoft.Kubernetes -o table
```

```console
az provider show -n Microsoft.KubernetesConfiguration -o table
```

## <a name="install-azure-cli-extensions"></a>Nainstalovat rozšíření Azure CLI

Nainstalujte `connectedk8s` rozšíření, které vám pomůže připojit clustery Kubernetes do Azure:

```console
az extension add --name connectedk8s
```

Nainstalovat `k8sconfiguration` rozšíření:

```console
az extension add --name k8sconfiguration
```

Spuštěním následujících příkazů aktualizujte rozšíření na nejnovější verze.

```console
az extension update --name connectedk8s
az extension update --name k8sconfiguration
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Pomocí skupiny prostředků můžete ukládat metadata pro svůj cluster.

Nejdřív vytvořte skupinu prostředků, která bude obsahovat prostředek připojeného clusteru.

```console
az group create --name AzureArcTest -l EastUS -o table
```

**Výkonem**

```console
Location    Name
----------  ------------
eastus      AzureArcTest
```

## <a name="connect-a-cluster"></a>Připojení clusteru

Dále připojíme náš cluster Kubernetes k Azure. Pracovní postup pro `az connectedk8s connect` je následující:

1. Ověřte připojení ke clusteru Kubernetes: prostřednictvím `KUBECONFIG` , `~/.kube/config` nebo`--kube-config`
1. Nasazení agentů Azure ARC pro Kubernetes pomocí Helm 3 do `azure-arc` oboru názvů

```console
az connectedk8s connect --name AzureArcTest1 --resource-group AzureArcTest
```

**Výkonem**

```console
Command group 'connectedk8s' is in preview. It may be changed/removed in a future release.
Helm release deployment succeeded

{
  "aadProfile": {
    "clientAppId": "",
    "serverAppId": "",
    "tenantId": ""
  },
  "agentPublicKeyCertificate": "...",
  "agentVersion": "0.1.0",
  "id": "/subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1",
  "identity": {
    "principalId": null,
    "tenantId": null,
    "type": "None"
  },
  "kubernetesVersion": "v1.15.0",
  "location": "eastus",
  "name": "AzureArcTest1",
  "resourceGroup": "AzureArcTest",
  "tags": {},
  "totalNodeCount": 1,
  "type": "Microsoft.Kubernetes/connectedClusters"
}
```

## <a name="verify-connected-cluster"></a>Ověřit připojený cluster

Uveďte připojené clustery:

```console
az connectedk8s list -g AzureArcTest
```

**Výkonem**

```console
Command group 'connectedk8s' is in preview. It may be changed/removed in a future release.
Name           Location    ResourceGroup
-------------  ----------  ---------------
AzureArcTest1  eastus      AzureArcTest
```

Kubernetes s povoleným obloukem Azure nasadí několik operátorů do `azure-arc` oboru názvů. Tato nasazení a lusky můžete zobrazit tady:

```console
kubectl -n azure-arc get deployments,pods
```

**Výkonem**

```console
NAME                                        READY   UP-TO-DATE AVAILABLE AGE
deployment.apps/cluster-metadata-operator   1/1     1           1        16h
deployment.apps/clusteridentityoperator     1/1     1           1        16h
deployment.apps/config-agent                1/1     1           1        16h
deployment.apps/controller-manager          1/1     1           1        16h
deployment.apps/flux-logs-agent             1/1     1           1        16h
deployment.apps/metrics-agent               1/1     1           1        16h
deployment.apps/resource-sync-agent         1/1     1           1        16h

NAME                                            READY   STATUS   RESTART AGE
pod/cluster-metadata-operator-7fb54d9986-g785b  2/2     Running  0       16h
pod/clusteridentityoperator-6d6678ffd4-tx8hr    3/3     Running  0       16h
pod/config-agent-544c4669f9-4th92               3/3     Running  0       16h
pod/controller-manager-fddf5c766-ftd96          3/3     Running  0       16h
pod/flux-logs-agent-7c489f57f4-mwqqv            2/2     Running  0       16h
pod/metrics-agent-58b765c8db-n5l7k              2/2     Running  0       16h
pod/resource-sync-agent-5cf85976c7-522p5        3/3     Running  0       16h
```

## <a name="azure-arc-agents-for-kubernetes"></a>Agenti Azure ARC pro Kubernetes

Kubernetes s povoleným ARC Azure se skládá z několika agentů (operátorů), které běží v clusteru nasazeném do `azure-arc` oboru názvů.

* `deployment.apps/config-agent`: sleduje připojený cluster pro prostředky konfigurace správy zdrojového kódu použité v clusteru a aktualizuje stav dodržování předpisů.
* `deployment.apps/controller-manager`: je operátor operátorů a orchestruje interakce mezi komponentami ARC Azure.
* `deployment.apps/metrics-agent`: shromažďuje metriky jiných agentů ARC, aby se zajistilo, že tito agenti vykazují optimální výkon.
* `deployment.apps/cluster-metadata-operator`: shromažďuje metadata clusteru – verze clusteru, počet uzlů a verzi agenta ARC.
* `deployment.apps/resource-sync-agent`: synchronizuje výše uvedená metadata clusteru do Azure.
* `deployment.apps/clusteridentityoperator`: udržuje certifikát MSI (Managed Service identity), který používají jiní agenti pro komunikaci s Azure.
* `deployment.apps/flux-logs-agent`: shromažďuje protokoly z operátorů toku nasazených jako součást konfigurace správy zdrojového kódu.

## <a name="delete-a-connected-cluster"></a>Odstranění připojeného clusteru

Prostředek můžete odstranit `Microsoft.Kubernetes/connectedcluster` pomocí rozhraní příkazového řádku Azure CLI nebo Azure Portal.

Příkaz rozhraní příkazového řádku Azure `az connectedk8s delete` odebere `Microsoft.Kubernetes/connectedCluster` prostředek v Azure. Azure CLI odstraní všechny přidružené `sourcecontrolconfiguration` prostředky v Azure. Rozhraní příkazového řádku Azure využívá odinstalaci Helm k odebrání agentů v clusteru.

Azure Portal odstraní `Microsoft.Kubernetes/connectedcluster` prostředek v Azure a odstraní všechny přidružené `sourcecontrolconfiguration` prostředky v Azure.

Chcete-li odebrat agenty v clusteru, které je třeba spustit, `az connectedk8s delete` nebo `helm uninstall azurearcfork8s` .

## <a name="next-steps"></a>Další kroky

* [Použití GitOps v připojeném clusteru](./use-gitops-connected-cluster.md)
* [Řízení konfigurace clusteru pomocí Azure Policy](./use-azure-policy.md)