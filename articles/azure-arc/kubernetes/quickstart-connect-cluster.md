---
title: 'Rychlý Start: připojení existujícího clusteru Kubernetes ke službě Azure ARC'
description: V tomto rychlém startu se dozvíte, jak připojit cluster Kubernetes s povoleným ARC Azure.
author: mgoedtel
ms.author: magoedte
ms.service: azure-arc
ms.topic: quickstart
ms.date: 03/03/2021
ms.custom: template-quickstart, references_regions, devx-track-azurecli
keywords: Kubernetes, oblouk, Azure, cluster
ms.openlocfilehash: 21ec5000ed7ef9df1805fa6ec43e20efc0f82182
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481238"
---
# <a name="quickstart-connect-an-existing-kubernetes-cluster-to-azure-arc"></a>Rychlý Start: připojení existujícího clusteru Kubernetes ke službě Azure ARC 

V tomto rychlém startu budeme těžit výhody Kubernetes s podporou ARC Azure a připojíme stávající cluster Kubernetes ke službě Azure ARC. Koncepční postup připojení clusterů do Azure ARC najdete v [článku architektura s povoleným agentem Azure ARC Kubernetes](./conceptual-agent-architecture.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

* Spuštěný cluster Kubernetes. Pokud ho ještě nemáte, můžete cluster vytvořit pomocí jedné z těchto možností:
    * [Kubernetes v Docker (druh)](https://kind.sigs.k8s.io/)
    * Vytvoření clusteru Kubernetes pomocí Docker pro [Mac](https://docs.docker.com/docker-for-mac/#kubernetes) nebo [Windows](https://docs.docker.com/docker-for-windows/#kubernetes)
    * Samostatný cluster Kubernetes s využitím [rozhraní API clusteru](https://cluster-api.sigs.k8s.io/user/quick-start.html)

    >[!NOTE]
    > Cluster musí mít alespoň jeden uzel s operačním systémem a typem architektury `linux/amd64` . Clustery se všemi `linux/arm64` uzly se zatím nepodporují.
    
* `kubeconfig`Soubor a kontext ukazující na váš cluster.
* Oprávnění číst a zapisovat pro typ prostředku Kubernetes s povoleným ARC Azure ( `Microsoft.Kubernetes/connectedClusters` ).

* Nainstalujte [nejnovější verzi Helm 3](https://helm.sh/docs/intro/install).

- [Instalace nebo upgrade rozhraní příkazového řádku Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) na verzi >= 2.16.0
* Nainstalujte `connectedk8s` rozšíření Azure CLI verze >= 1.0.0:
  
  ```azurecli
  az extension add --name connectedk8s
  ```

>[!TIP]
> Pokud `connectedk8s` je rozšíření už nainstalované, aktualizujte ho na nejnovější verzi pomocí následujícího příkazu. `az extension update --name connectedk8s`


>[!NOTE]
>Seznam oblastí, které podporuje Azure ARC Enabled Kubernetes, najdete [tady](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc).

>[!NOTE]
> Pokud chcete v clusteru používat vlastní umístění, použijte Východní USA nebo Západní Evropa oblasti pro připojení clusteru, protože vlastní umístění jsou k dispozici pouze v těchto oblastech. Všechny ostatní funkce Kubernetes s podporou ARC Azure jsou dostupné ve všech oblastech uvedených výše.

## <a name="meet-network-requirements"></a>Splnění požadavků na síť

>[!IMPORTANT]
>Agenti Azure ARC vyžadují, aby následující protokoly/porty/odchozí adresy URL fungovaly:
>* TCP na portu 443: `https://:443`
>* TCP na portu 9418: `git://:9418`
  
| Koncový bod (DNS) | Popis |  
| ----------------- | ------------- |  
| `https://management.azure.com`                                                                                 | Vyžaduje se, aby se agent připojil k Azure a zaregistroval cluster.                                                        |  
| `https://<region>.dp.kubernetesconfiguration.azure.com` | Koncový bod roviny dat pro agenta, aby načetl stav a načetl informace o konfiguraci.                                      |  
| `https://login.microsoftonline.com`                                                                            | Vyžaduje se pro načtení a aktualizaci tokenů Azure Resource Manager.                                                                                    |  
| `https://mcr.microsoft.com`                                                                            | Vyžaduje se pro získání imagí kontejneru pro agenty Azure ARC.                                                                  |  
| `https://eus.his.arc.azure.com`, `https://weu.his.arc.azure.com`, `https://wcus.his.arc.azure.com`, `https://scus.his.arc.azure.com`, `https://sea.his.arc.azure.com`, `https://uks.his.arc.azure.com`, `https://wus2.his.arc.azure.com`, `https://ae.his.arc.azure.com`, `https://eus2.his.arc.azure.com`, `https://ne.his.arc.azure.com` |  Požadováno pro vyžádání certifikátů s přiřazenými systémy Identita spravované služby (MSI).                                                                  |

## <a name="register-the-two-providers-for-azure-arc-enabled-kubernetes"></a>Registrace dvou poskytovatelů pro Kubernetes s povoleným ARC Azure

1. Zadejte následující příkazy:
    ```azurecli
    az provider register --namespace Microsoft.Kubernetes
    az provider register --namespace Microsoft.KubernetesConfiguration
    az provider register --namespace Microsoft.ExtendedLocation
    ```
2. Monitorujte proces registrace. Registrace může trvat až 10 minut.
    ```azurecli
    az provider show -n Microsoft.Kubernetes -o table
    az provider show -n Microsoft.KubernetesConfiguration -o table
    az provider show -n Microsoft.ExtendedLocation -o table
    ```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků:  

```console
az group create --name AzureArcTest -l EastUS -o table
```

```output
Location    Name
----------  ------------
eastus      AzureArcTest
```

## <a name="connect-an-existing-kubernetes-cluster"></a>Připojit existující cluster Kubernetes

1. Připojte svůj cluster Kubernetes ke službě Azure ARC pomocí následujícího příkazu:
    ```console
    az connectedk8s connect --name AzureArcTest1 --resource-group AzureArcTest
    ```

    ```output
    Helm release deployment succeeded

    {
      "aadProfile": {
        "clientAppId": "",
        "serverAppId": "",
        "tenantId": ""
      },
      "agentPublicKeyCertificate": "xxxxxxxxxxxxxxxxxxx",
      "agentVersion": null,
      "connectivityStatus": "Connecting",
      "distribution": "gke",
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1",
      "identity": {
        "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "type": "SystemAssigned"
      },
      "infrastructure": "gcp",
      "kubernetesVersion": null,
      "lastConnectivityTime": null,
      "location": "eastus",
      "managedIdentityCertificateExpirationTime": null,
      "name": "AzureArcTest1",
      "offering": null,
      "provisioningState": "Succeeded",
      "resourceGroup": "AzureArcTest",
      "tags": {},
      "totalCoreCount": null,
      "totalNodeCount": null,
      "type": "Microsoft.Kubernetes/connectedClusters"
    }
    ```

> [!TIP]
> Výše uvedený příkaz bez zadaného parametru Location vytvoří prostředek Kubernetes s povolenou službou Azure ARC ve stejném umístění jako skupina prostředků. Pokud chcete vytvořit prostředek Kubernetes s povolenou službou Azure ARC v jiném umístění, zadejte buď `--location <region>` nebo `-l <region>` při spuštění `az connectedk8s connect` příkazu.

## <a name="verify-cluster-connection"></a>Ověřit připojení clusteru

Seznam připojených clusterů zobrazíte pomocí následujícího příkazu:  

```console
az connectedk8s list -g AzureArcTest -o table
```

```output
Name           Location    ResourceGroup
-------------  ----------  ---------------
AzureArcTest1  eastus      AzureArcTest
```

> [!NOTE]
> Po připojení clusteru trvá přibližně 5 až 10 minut, než se na stránce přehledu v prostředku Kubernetes s povoleným prostředkem Azure ARC v Azure Portal doplní Stránka s přehledem.

## <a name="connect-using-an-outbound-proxy-server"></a>Připojení pomocí odchozí proxy server

Pokud je váš cluster za odchozím proxy server, Azure CLI a agenti Azure ARC s povoleným Kubernetes musí směrovat své žádosti přes odchozí proxy server. 


1. Nastavte proměnné prostředí potřebné pro použití odchozího proxy server v Azure CLI:

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

2. Spusťte příkaz Connect se zadanými parametry proxy serveru:

    ```console
    az connectedk8s connect -n <cluster-name> -g <resource-group> --proxy-https https://<proxy-server-ip-address>:<port> --proxy-http http://<proxy-server-ip-address>:<port> --proxy-skip-range <excludedIP>,<excludedCIDR> --proxy-cert <path-to-cert-file>
    ```

> [!NOTE]
> * V `excludedCIDR` části `--proxy-skip-range` zajistěte, aby komunikace v clusteru nebyla pro agenty poškozená.
> * `--proxy-http`, `--proxy-https` a `--proxy-skip-range` jsou očekávána pro většinu odchozích proxy prostředí. `--proxy-cert` je vyžadován *pouze* v případě, že je nutné vložit důvěryhodné certifikáty, které proxy očekává, do důvěryhodného úložiště certifikátů agenta lusky.

## <a name="view-azure-arc-agents-for-kubernetes"></a>Zobrazit agenty ARC Azure pro Kubernetes

Kubernetes s povoleným obloukem Azure nasadí několik operátorů do `azure-arc` oboru názvů. 

1. Zobrazit tato nasazení a lusky pomocí:

    ```console
    kubectl -n azure-arc get deployments,pods
    ```

1. Ověřte, že všechny lusky jsou ve `Running` stavu.

    ```output
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

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pomocí Azure CLI můžete odstranit prostředek Kubernetes s povolenou službou Azure ARC, všechny přidružené prostředky konfigurace *a* všechny agenty spuštěné v clusteru pomocí rozhraní příkazového řádku Azure:

```azurecli
az connectedk8s delete --name AzureArcTest1 --resource-group AzureArcTest
```

>[!NOTE]
>Odstranění prostředku Kubernetes s povolenou službou Azure ARC pomocí Azure Portal odebere všechny přidružené prostředky konfigurace *, ale* neodebere žádné agenty spuštěné v clusteru. Osvědčeným postupem je odstranit prostředek Kubernetes s povoleným ARC Azure pomocí `az connectedk8s delete` místo Azure Portal.

## <a name="next-steps"></a>Další kroky

V dalším článku se dozvíte, jak nasadit konfigurace do připojeného clusteru Kubernetes pomocí GitOps.
> [!div class="nextstepaction"]
> [Nasazení konfigurací pomocí Gitops](tutorial-use-gitops-connected-cluster.md)
