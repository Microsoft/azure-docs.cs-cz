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
ms.custom: references_regions
ms.openlocfilehash: 2c5e697f3dd67087582118fb6a6e083feecf549f
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87050097"
---
# <a name="connect-an-azure-arc-enabled-kubernetes-cluster-preview"></a>Připojení clusteru Kubernetes s povoleným ARC Azure (Preview)

Tento dokument popisuje proces propojení libovolného clusteru Kubernetes s certifikací Cloud Computing Foundation (CNCF), jako je AKS Engine v Azure, AKS Engine v Azure Stack hub, GKE, EKS a VMware vSphere cluster do Azure ARC.

## <a name="before-you-begin"></a>Než začnete

Ověřte, že máte připravené tyto požadavky:

* Cluster Kubernetes, který je v provozu. Pokud nemáte existující cluster Kubernetes, můžete k vytvoření testovacího clusteru použít jedno z následujících pokynů:
  * Vytvoření clusteru Kubernetes pomocí [Kubernetes v Docker (druh)](https://kind.sigs.k8s.io/)
  * Vytvoření clusteru Kubernetes pomocí Docker pro [Mac](https://docs.docker.com/docker-for-mac/#kubernetes) nebo [Windows](https://docs.docker.com/docker-for-windows/#kubernetes)
* Pro nasazení agentů Kubernetes s podporou ARC budete potřebovat soubor kubeconfig pro přístup ke clusteru a roli Správce clusteru v clusteru.
* Uživatel nebo instanční objekt použitý s `az login` příkazy a `az connectedk8s connect` musí mít oprávnění číst a zapsat pro typ prostředku Microsoft. Kubernetes/connectedclusters. Role "cluster Kubernetes-Azure ARC" má tato oprávnění a lze ji použít k přiřazení rolí pro uživatele nebo instanční objekt.
* K registraci clusteru pomocí rozšíření connectedk8s se vyžaduje Helm 3. Pro splnění tohoto požadavku [nainstalujte nejnovější verzi Helm 3](https://helm.sh/docs/intro/install) .
* Pro instalaci rozšíření CLI s povoleným Kubernetes rozhraním Azure se vyžaduje Azure CLI verze 2.3 + +. Nainstalujte rozhraní příkazového [řádku Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) nebo aktualizujte na nejnovější verzi, abyste měli jistotu, že máte Azure CLI verze 2.3 +.
* Instalace rozšíření Kubernetes CLI s povoleným ARC:
  
  Nainstalujte `connectedk8s` rozšíření, které vám pomůže připojit clustery Kubernetes do Azure:
  
  ```console
  az extension add --name connectedk8s
  ```
  
  Nainstalovat `k8sconfiguration` rozšíření:
  
  ```console
  az extension add --name k8sconfiguration
  ```
  
  Pokud chcete tato rozšíření později aktualizovat, spusťte následující příkazy:
  
  ```console
  az extension update --name connectedk8s
  az extension update --name k8sconfiguration
  ```

## <a name="supported-regions"></a>Podporované oblasti

* East US
* West Europe

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

az provider register --namespace Microsoft.KubernetesConfiguration
```

Registrace je asynchronní proces. Registrace může trvat přibližně 10 minut. Proces registrace můžete monitorovat pomocí následujících příkazů:

```console
az provider show -n Microsoft.Kubernetes -o table
```

```console
az provider show -n Microsoft.KubernetesConfiguration -o table
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
az connectedk8s list -g AzureArcTest -o table
```

**Výkonem**

```console
Command group 'connectedk8s' is in preview. It may be changed/removed in a future release.
Name           Location    ResourceGroup
-------------  ----------  ---------------
AzureArcTest1  eastus      AzureArcTest
```

Tento prostředek můžete zobrazit také na [Azure Portal](https://portal.azure.com/). Jakmile máte portál otevřený v prohlížeči, přejděte do skupiny prostředků a prostředku Kubernetes s povoleným ARC Azure na základě názvů prostředků a názvů skupin prostředků, které se použily dříve v `az connectedk8s connect` příkazu.

> [!NOTE]
> Po připojení clusteru trvá přibližně 5 až 10 minut, než se na stránce Přehled v prostředku Kubernetes s povoleným Azure Portal prostředkem Azure ARC na ploše zobrazí stránka s přehledem.

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
* `deployment.apps/cluster-metadata-operator`: shromažďuje metadata clusteru – verze clusteru, počet uzlů a verzi agenta Azure ARC.
* `deployment.apps/resource-sync-agent`: synchronizuje výše uvedená metadata clusteru do Azure.
* `deployment.apps/clusteridentityoperator`: Kubernetes s podporou Azure ARC aktuálně podporuje identitu přiřazenou systémem. clusteridentityoperator udržuje certifikát MSI (Managed Service identity), který používají jiní agenti pro komunikaci s Azure.
* `deployment.apps/flux-logs-agent`: shromažďuje protokoly z operátorů toku nasazených jako součást konfigurace správy zdrojového kódu.

## <a name="delete-a-connected-cluster"></a>Odstranění připojeného clusteru

Prostředek můžete odstranit `Microsoft.Kubernetes/connectedcluster` pomocí rozhraní příkazového řádku Azure CLI nebo Azure Portal.


* **Odstranění pomocí Azure CLI: pomocí**následujícího příkazu rozhraní PŘÍKAZového řádku Azure můžete iniciovat odstranění prostředku Kubernetes s povoleným ARC Azure.
  ```console
  az connectedk8s delete --name AzureArcTest1 --resource-group AzureArcTest
  ```
  Tento příkaz odebere `Microsoft.Kubernetes/connectedCluster` prostředek a všechny přidružené `sourcecontrolconfiguration` prostředky v Azure. Rozhraní příkazového řádku Azure používá k odebrání agentů spuštěných v clusteru také Helm Uninstall.

* **Odstranění při Azure Portal**: odstranění prostředku Kubernetes s povoleným ARC Azure na Azure Portal odstraní `Microsoft.Kubernetes/connectedcluster` prostředek a všechny přidružené `sourcecontrolconfiguration` prostředky v Azure, ale neodstraní agenty spuštěné v clusteru. Chcete-li odstranit agenty spuštěné v clusteru, spusťte následující příkaz.

  ```console
  az connectedk8s delete --name AzureArcTest1 --resource-group AzureArcTest
  ```

## <a name="next-steps"></a>Další kroky

* [Použití GitOps v připojeném clusteru](./use-gitops-connected-cluster.md)
* [Řízení konfigurace clusteru pomocí Azure Policy](./use-azure-policy.md)
