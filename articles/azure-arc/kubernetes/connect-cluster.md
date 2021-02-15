---
title: Připojení clusteru Kubernetes s povoleným ARC Azure (Preview)
services: azure-arc
ms.service: azure-arc
ms.date: 02/09/2021
ms.topic: article
author: mlearned
ms.author: mlearned
description: Připojení clusteru Kubernetes s povoleným ARC Azure pomocí ARC Azure
keywords: Kubernetes, oblouk, Azure, K8s, Containers
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: e68eccf998592aa7d1ebfea51e4ca66d577b3c7f
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100390551"
---
# <a name="connect-an-azure-arc-enabled-kubernetes-cluster-preview"></a>Připojení clusteru Kubernetes s povoleným ARC Azure (Preview)

V tomto článku se dozvíte, jak připojit jakýkoliv cluster s certifikací CNCF (Cloud Native Computing Foundation), jako je AKS Engine v Azure, AKS modul v Azure Stack hub, GKE, EKS a VMware vSphere cluster do Azure ARC.

## <a name="before-you-begin"></a>Než začnete

Ověřte, že jste připravili následující požadavky:

* Spuštěný cluster Kubernetes. Pokud nemáte existující cluster Kubernetes, můžete k vytvoření testovacího clusteru použít jedno z následujících pokynů:
  * Vytvořte cluster Kubernetes pomocí [Kubernetes v Docker (druh)](https://kind.sigs.k8s.io/).
  * Vytvořte cluster Kubernetes pomocí Docker pro [Mac](https://docs.docker.com/docker-for-mac/#kubernetes) nebo [Windows](https://docs.docker.com/docker-for-windows/#kubernetes).
* Soubor kubeconfig pro přístup ke clusteru a roli Správce clusteru v clusteru pro nasazení agentů Kubernetes s povoleným ARC.
* Uživatel nebo instanční objekt použitý s `az login` příkazy a `az connectedk8s connect` musí mít oprávnění číst a zapsat pro typ prostředku Microsoft. Kubernetes/connectedclusters. Role "cluster Kubernetes-Azure ARC" má tato oprávnění a lze ji použít k přiřazení rolí pro uživatele nebo instanční objekt.
* Helm 3 pro registraci clusteru pomocí rozšíření connectedk8s. Pro splnění tohoto požadavku [nainstalujte nejnovější verzi Helm 3](https://helm.sh/docs/intro/install) .
* Azure CLI verze 2.15 + pro instalaci rozšíření CLI s povoleným rozhraním Azure ARC Kubernetes. Nainstalujte rozhraní příkazového [řádku Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) nebo ho aktualizujte na nejnovější verzi.
* Instalace rozšíření Kubernetes CLI s povoleným ARC:
  
  * Nainstalujte `connectedk8s` rozšíření, které vám pomůže připojit clustery Kubernetes do Azure:
  
  ```azurecli
  az extension add --name connectedk8s
  ```
  
   * Nainstalovat `k8sconfiguration` rozšíření:
  
  ```azurecli
  az extension add --name k8sconfiguration
  ```

  * Pokud chcete tato rozšíření později aktualizovat, spusťte následující příkazy:
  
  ```azurecli
  az extension update --name connectedk8s
  az extension update --name k8sconfiguration
  ```

## <a name="supported-regions"></a>Podporované oblasti

* East US
* West Europe

## <a name="network-requirements"></a>Požadavky sítě

Agenti Azure ARC vyžadují, aby následující protokoly/porty/odchozí adresy URL fungovaly:

* TCP na portu 443: `https://:443`
* TCP na portu 9418: `git://:9418`

| Koncový bod (DNS)                                                                                               | Description                                                                                                                 |
| ------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------- |
| `https://management.azure.com`                                                                                 | Vyžaduje se, aby se agent připojil k Azure a zaregistroval cluster.                                                        |
| `https://eastus.dp.kubernetesconfiguration.azure.com`, `https://westeurope.dp.kubernetesconfiguration.azure.com` | Koncový bod roviny dat pro agenta, aby načetl stav a načetl informace o konfiguraci.                                      |
| `https://login.microsoftonline.com`                                                                            | Vyžaduje se pro načtení a aktualizaci tokenů Azure Resource Manager.                                                                                    |
| `https://mcr.microsoft.com`                                                                            | Vyžaduje se pro získání imagí kontejneru pro agenty Azure ARC.                                                                  |
| `https://eus.his.arc.azure.com`, `https://weu.his.arc.azure.com`                                                                            |  Vyžaduje se pro získání certifikátů spravovaných identitě přiřazených systémem.                                                                  |

## <a name="register-the-two-providers-for-azure-arc-enabled-kubernetes"></a>Zaregistrujte dva poskytovatele pro Kubernetes s povoleným ARC Azure:

```console
az provider register --namespace Microsoft.Kubernetes

az provider register --namespace Microsoft.KubernetesConfiguration
```

Registrace je asynchronní proces a může trvat přibližně 10 minut. Proces registrace můžete monitorovat pomocí následujících příkazů:

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

Dále připojíme náš cluster Kubernetes k Azure pomocí `az connectedk8s connect` :

1. Ověřte připojení ke clusteru Kubernetes pomocí jednoho z následujících postupů:
   * `KUBECONFIG`
   * `~/.kube/config`
   * `--kube-config`
1. Nasaďte agenty Azure ARC pro Kubernetes s využitím Helm 3 do `azure-arc` oboru názvů:

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

K vypsání připojených clusterů použijte následující příkaz:

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

Tento prostředek můžete zobrazit také na [Azure Portal](https://portal.azure.com/). Otevřete portál v prohlížeči a přejděte do skupiny prostředků a prostředku Kubernetes s povoleným obloukem Azure na základě názvů prostředků a názvů skupin prostředků, které se použily dříve v `az connectedk8s connect` příkazu.  
> [!NOTE]
> Po připojení clusteru trvá přibližně 5 až 10 minut, než se na stránce přehledu v prostředku Kubernetes s povoleným prostředkem Azure ARC v Azure Portal doplní Stránka s přehledem.

## <a name="connect-using-an-outbound-proxy-server"></a>Připojení pomocí odchozí proxy server

Pokud je váš cluster za odchozím proxy server, Azure CLI a agenti Kubernetes s povoleným obloukem musí směrovat své žádosti přes odchozí proxy server:

1. Ověřte verzi `connectedk8s` rozšíření nainstalovaného na vašem počítači:

    ```console
    az -v
    ```

    Abyste mohli `connectedk8s` nastavit agenty s odchozím proxy serverem, potřebujete rozšíření verze 0.2.5 +. Pokud máte na svém počítači verzi 0.2.3 nebo starší, použijte [Postup aktualizace](#before-you-begin) a získejte na svém počítači nejnovější verzi rozšíření.

2. Nastavte proměnné prostředí potřebné pro použití odchozího proxy server v Azure CLI:

    * Pokud používáte bash, spusťte následující příkaz s příslušnými hodnotami:

        ```bash
        export HTTP_PROXY=<proxy-server-ip-address>:<port>
        export HTTPS_PROXY=<proxy-server-ip-address>:<port>
        export NO_PROXY=<cluster-apiserver-ip-address>:<port>
        ```

    * Pokud používáte PowerShell, spusťte následující příkaz s příslušnými hodnotami:

        ```powershell
        $Env:HTTP_PROXY = "<proxy-server-ip-address>:<port>"
        $Env:HTTPS_PROXY = "<proxy-server-ip-address>:<port>"
        $Env:NO_PROXY = "<cluster-apiserver-ip-address>:<port>"
        ```

3. Spusťte příkaz Connect se zadanými parametry proxy serveru:

    ```console
    az connectedk8s connect -n <cluster-name> -g <resource-group> --proxy-https https://<proxy-server-ip-address>:<port> --proxy-http http://<proxy-server-ip-address>:<port> --proxy-skip-range <excludedIP>,<excludedCIDR> --proxy-cert <path-to-cert-file>
    ```

> [!NOTE]
> * Zadání `excludedCIDR` v části `--proxy-skip-range` je důležité, aby se zajistilo, že komunikace v clusteru není pro agenty poškozená.
> * I když se `--proxy-http` `--proxy-https` `--proxy-skip-range` u většiny prostředí odchozího proxy serveru očekává, že se musí do `--proxy-cert` důvěryhodného úložiště certifikátů v části agenti vložit jenom důvěryhodné certifikáty ze serveru proxy.
> * Výše uvedená specifikace proxy serveru se v tuto chvíli používá jenom pro agenty ARC a ne pro tok lusků používaných v sourceControlConfiguration. Kubernetes tým s povoleným obloukem aktivně pracuje na této funkci a brzy bude k dispozici.

## <a name="azure-arc-agents-for-kubernetes"></a>Agenti Azure ARC pro Kubernetes

Kubernetes s povoleným obloukem Azure nasadí několik operátorů do `azure-arc` oboru názvů. Tato nasazení a lusky můžete zobrazit pomocí:

```console
kubectl -n azure-arc get deployments,pods
```

**Výkonem**

```console
NAME                                        READY      UP-TO-DATE  AVAILABLE  AGE
deployment.apps/cluster-metadata-operator     1/1             1        1      16h
deployment.apps/clusteridentityoperator       1/1             1        1      16h
deployment.apps/config-agent                  1/1             1        1      16h
deployment.apps/controller-manager            1/1             1        1      16h
deployment.apps/flux-logs-agent               1/1             1        1      16h
deployment.apps/metrics-agent                 1/1             1        1      16h
deployment.apps/resource-sync-agent           1/1             1        1      16h

NAME                                           READY    STATUS   RESTART AGE
pod/cluster-metadata-operator-7fb54d9986-g785b  2/2     Running  0       16h
pod/clusteridentityoperator-6d6678ffd4-tx8hr    3/3     Running  0       16h
pod/config-agent-544c4669f9-4th92               3/3     Running  0       16h
pod/controller-manager-fddf5c766-ftd96          3/3     Running  0       16h
pod/flux-logs-agent-7c489f57f4-mwqqv            2/2     Running  0       16h
pod/metrics-agent-58b765c8db-n5l7k              2/2     Running  0       16h
pod/resource-sync-agent-5cf85976c7-522p5        3/3     Running  0       16h
```

Kubernetes s povoleným ARC Azure se skládá z několika agentů (operátorů), které běží v clusteru nasazeném do `azure-arc` oboru názvů.

| Agenti (operátoři)                                                                                               | Description                                                                                                                 |
| ------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------- |
| `deployment.apps/config-agent`                                                                                 | Sleduje připojený cluster pro prostředky konfigurace správy zdrojového kódu použité v clusteru a aktualizuje stav kompatibility.                                                        |
| `deployment.apps/controller-manager` | Operátor obsluhy, který orchestruje interakce mezi komponentami ARC Azure.                                      |
| `deployment.apps/metrics-agent`                                                                            | Shromažďuje metriku výkonu jiných agentů ARC.                                                                                    |
| `deployment.apps/cluster-metadata-operator`                                                                            | Shromažďuje metadata clusteru, jako je verze clusteru, počet uzlů a verze agenta Azure ARC.                                                                  |
| `deployment.apps/resource-sync-agent`                                                                            |  Synchronizuje výše uvedená metadata clusteru do Azure.                                                                  |
| `deployment.apps/clusteridentityoperator`                                                                            |  Kubernetes s povoleným ARC Azure aktuálně podporuje identitu přiřazenou systémem. `clusteridentityoperator` udržuje certifikát MSI (Managed Service identity), který používají jiní agenti pro komunikaci s Azure.                                                                  |
| `deployment.apps/flux-logs-agent`                                                                            |  Shromažďuje protokoly z operátorů toku nasazených jako součást konfigurace správy zdrojového kódu.                                                                  |

## <a name="delete-a-connected-cluster"></a>Odstranění připojeného clusteru

Prostředek můžete odstranit `Microsoft.Kubernetes/connectedcluster` pomocí rozhraní příkazového řádku Azure CLI nebo Azure Portal.


* **Odstranění pomocí Azure CLI**: pomocí následujícího příkazu rozhraní příkazového řádku Azure zahajte odstranění prostředku Kubernetes s povoleným ARC Azure.
  ```console
  az connectedk8s delete --name AzureArcTest1 --resource-group AzureArcTest
  ```
  Tento příkaz odebere `Microsoft.Kubernetes/connectedCluster` prostředek a všechny přidružené `sourcecontrolconfiguration` prostředky v Azure. Rozhraní příkazového řádku Azure CLI `helm uninstall` také odebírá agenty spuštěné v clusteru.

* **Odstranění při Azure Portal**: odstranění prostředku Kubernetes s povoleným ARC Azure na Azure Portal odstraní `Microsoft.Kubernetes/connectedcluster` prostředek a všechny přidružené `sourcecontrolconfiguration` prostředky v Azure, ale neodebere  agenty spuštěné v clusteru. 

  Chcete-li odebrat agenty spuštěné v clusteru, spusťte následující příkaz:

  ```console
  az connectedk8s delete --name AzureArcTest1 --resource-group AzureArcTest
  ```

## <a name="next-steps"></a>Další kroky

* [Použití GitOps v připojeném clusteru](./use-gitops-connected-cluster.md)
* [Řízení konfigurace clusteru pomocí Azure Policy](./use-azure-policy.md)
