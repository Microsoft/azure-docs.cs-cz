---
title: 'Rychlý Start: nasazení clusteru AKS pomocí Azure CLI'
description: Naučte se rychle vytvořit cluster Kubernetes, nasadit aplikaci a monitorovat výkon ve službě Azure Kubernetes Service (AKS) pomocí Azure CLI.
services: container-service
ms.topic: quickstart
ms.date: 02/26/2021
ms.custom:
- H1Hack27Feb2017
- mvc
- devcenter
- seo-javascript-september2019
- seo-javascript-october2019
- seo-python-october2019
- devx-track-azurecli
- contperf-fy21q1
ms.openlocfilehash: b3d6c7695f74c048cb03e3f4e7ae822005c81c06
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2021
ms.locfileid: "103492876"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-cluster-using-the-azure-cli"></a>Rychlý Start: nasazení clusteru služby Azure Kubernetes pomocí Azure CLI

Služba Azure Kubernetes Service (AKS) je spravovaná služba Kubernetes, která umožňuje rychle nasadit a spravovat clustery. V tomto rychlém startu budete:
* Nasaďte cluster AKS pomocí Azure CLI. 
* Spusťte aplikaci s více kontejnery s webovým front-end a instancí Redis v clusteru. 
* Monitorujte stav clusteru a lusky, které spouští vaši aplikaci.

  ![Hlasovací aplikace nasazená ve službě Azure Kubernetes](./media/container-service-kubernetes-walkthrough/voting-app-deployed-in-azure-kubernetes-service.png)

Tento rychlý start předpokládá základní znalosti konceptů Kubernetes. Další informace najdete v tématu [základní koncepty Kubernetes pro Azure Kubernetes Service (AKS)][kubernetes-concepts].

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Další informace o vytváření fondu uzlů Windows serveru najdete v tématu [Vytvoření clusteru AKS, který podporuje kontejnery Windows serveru][windows-container-cli].

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Tento článek vyžaduje rozhraní příkazového řádku Azure (2.0.64) nebo vyšší verze. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.

> [!NOTE]
> Spusťte příkazy jako správce, pokud máte v plánu spustit příkazy v tomto rychlém startu místo v Azure Cloud Shell.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

[Skupina prostředků Azure](../azure-resource-manager/management/overview.md) je logická skupina, ve které se nasazují a spravují prostředky Azure. Při vytváření skupiny prostředků se zobrazí výzva k zadání umístění. Toto umístění je: 
* Umístění úložiště metadat skupiny prostředků.
* Kde se vaše prostředky spustí v Azure, pokud při vytváření prostředků nezadáte jinou oblast. 

Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*.

Vytvořte skupinu prostředků pomocí příkazu [AZ Group Create][az-group-create] .


```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Výstup pro úspěšně vytvořenou skupinu prostředků:

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup",
  "location": "eastus",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="enable-cluster-monitoring"></a>Povolit monitorování clusteru

1. Ověřte, že se ve vašem předplatném zaregistrovali *Microsoft. OperationsManagement* a *Microsoft. OperationalInsights* . Postup kontroly stavu registrace:

    ```azurecli
    az provider show -n Microsoft.OperationsManagement -o table
    az provider show -n Microsoft.OperationalInsights -o table
    ```
 
    Pokud nejsou registrovány, zaregistrujte *Microsoft. OperationsManagement* a *Microsoft. OperationalInsights* pomocí:
 
    ```azurecli
    az provider register --namespace Microsoft.OperationsManagement
    az provider register --namespace Microsoft.OperationalInsights
    ```

2. Povolte [Azure monitor pro kontejnery][azure-monitor-containers] pomocí parametru *--Enable-addons monitoring* . 

## <a name="create-aks-cluster"></a>Vytvoření clusteru AKS

Vytvořte cluster AKS pomocí příkazu [AZ AKS Create][az-aks-create] . Následující příklad vytvoří cluster s názvem *myAKSCluster* s jedním uzlem: 

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster --node-count 1 --enable-addons monitoring --generate-ssh-keys
```

Po několika minutách se příkaz dokončí a vrátí informace o clusteru ve formátu JSON.

> [!NOTE]
> Při vytváření clusteru AKS se automaticky vytvoří druhá skupina prostředků pro ukládání prostředků AKS. Další informace najdete v tématu [Proč jsou dvě skupiny prostředků vytvořené pomocí AKS?](./faq.md#why-are-two-resource-groups-created-with-aks)

## <a name="connect-to-the-cluster"></a>Připojení ke clusteru

Ke správě clusteru Kubernetes použijte klienta příkazového řádku Kubernetes [kubectl][kubectl]. `kubectl` je již nainstalován, pokud používáte Azure Cloud Shell. 

1. `kubectl`Místně nainstalujte pomocí příkazu [AZ AKS Install-CLI][az-aks-install-cli] :

    ```azurecli
    az aks install-cli
    ```

2. Nakonfigurujte `kubectl` pro připojení ke clusteru Kubernetes pomocí příkazu [AZ AKS Get-Credentials][az-aks-get-credentials] . Následující příkaz:  
      * Stáhne přihlašovací údaje a nakonfiguruje rozhraní příkazového řádku Kubernetes pro jejich použití.
      * Používá `~/.kube/config` výchozí umístění [konfiguračního souboru Kubernetes][kubeconfig-file]. Zadejte jiné umístění konfiguračního souboru Kubernetes pomocí *--File*.


    ```azurecli-interactive
    az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
    ```

3. Pomocí příkazu [kubectl Get][kubectl-get] ověřte připojení ke svému clusteru. Tento příkaz vrátí seznam uzlů clusteru.

    ```azurecli-interactive
    kubectl get nodes
    ```

    Výstup zobrazuje jeden uzel vytvořený v předchozích krocích. Přesvědčte se, zda je stav uzlu *připraveno*:

    ```output
    NAME                       STATUS   ROLES   AGE     VERSION
    aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.12.8
    ```

## <a name="run-the-application"></a>Spuštění aplikace

[Soubor manifestu Kubernetes][kubernetes-deployment] definuje požadovaný stav clusteru, například které image kontejnerů se mají spustit. 

V tomto rychlém startu použijete manifest k vytvoření všech objektů potřebných ke spuštění [aplikace hlasování Azure][azure-vote-app]. Tento manifest obsahuje dvě [Kubernetes nasazení][kubernetes-deployment]:
* Ukázková aplikace Pythonu pro Azure
* Instance Redis 

Vytvoří se také dvě [služby Kubernetes][kubernetes-service] :
* Interní služba pro instanci Redis
* Externí služba pro přístup k aplikaci hlasování Azure z Internetu

1. Vytvořte soubor s názvem `azure-vote.yaml`.
    * Pokud použijete Azure Cloud Shell, tento soubor se dá vytvořit pomocí `code` , `vi` nebo `nano` jako při práci na virtuálním nebo fyzickém systému.
1. Zkopírujte následující definici YAML:

    ```yaml
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: azure-vote-back
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: azure-vote-back
      template:
        metadata:
          labels:
            app: azure-vote-back
        spec:
          nodeSelector:
            "beta.kubernetes.io/os": linux
          containers:
          - name: azure-vote-back
            image: mcr.microsoft.com/oss/bitnami/redis:6.0.8
            env:
            - name: ALLOW_EMPTY_PASSWORD
              value: "yes"
            resources:
              requests:
                cpu: 100m
                memory: 128Mi
              limits:
                cpu: 250m
                memory: 256Mi
            ports:
            - containerPort: 6379
              name: redis
    ---
    apiVersion: v1
    kind: Service
    metadata:
      name: azure-vote-back
    spec:
      ports:
      - port: 6379
      selector:
        app: azure-vote-back
    ---
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: azure-vote-front
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: azure-vote-front
      template:
        metadata:
          labels:
            app: azure-vote-front
        spec:
          nodeSelector:
            "beta.kubernetes.io/os": linux
          containers:
          - name: azure-vote-front
            image: mcr.microsoft.com/azuredocs/azure-vote-front:v1
            resources:
              requests:
                cpu: 100m
                memory: 128Mi
              limits:
                cpu: 250m
                memory: 256Mi
            ports:
            - containerPort: 80
            env:
            - name: REDIS
              value: "azure-vote-back"
    ---
    apiVersion: v1
    kind: Service
    metadata:
      name: azure-vote-front
    spec:
      type: LoadBalancer
      ports:
      - port: 80
      selector:
        app: azure-vote-front
    ```

1. Nasaďte aplikaci pomocí příkazu [kubectl Apply][kubectl-apply] a zadejte název manifestu YAML:

    ```console
    kubectl apply -f azure-vote.yaml
    ```

    Výstup zobrazuje úspěšně vytvořená nasazení a služby:

    ```output
    deployment "azure-vote-back" created
    service "azure-vote-back" created
    deployment "azure-vote-front" created
    service "azure-vote-front" created
    ```

## <a name="test-the-application"></a>Testování aplikace

Když je aplikace spuštěná, služba Kubernetes zpřístupňuje front-end aplikace na internetu. Dokončení tohoto procesu může trvat několik minut.

Sledujte průběh pomocí příkazu [kubectl Get Service][kubectl-get] s `--watch` argumentem.

```azurecli-interactive
kubectl get service azure-vote-front --watch
```

Výstup **externích IP adres** pro `azure-vote-front` službu se zpočátku zobrazí jako *čeká na vyřízení*.

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Jakmile se adresa **External-IP** změní ze *stavu čeká* na skutečnou veřejnou IP adresu, použijte `CTRL-C` k zastavení `kubectl` procesu sledování. Následující příklad výstupu ukazuje platnou veřejnou IP adresu přiřazenou ke službě:

```output
azure-vote-front   LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Pokud chcete zobrazit hlasovou aplikaci Azure v akci, otevřete webový prohlížeč na externí IP adresu vaší služby.

![Hlasovací aplikace nasazená ve službě Azure Kubernetes](./media/container-service-kubernetes-walkthrough/voting-app-deployed-in-azure-kubernetes-service.png)

Zobrazuje metriky stavu uzlů clusteru a lusky zachycené [Azure monitor pro kontejnery][azure-monitor-containers] v Azure Portal. 

## <a name="delete-the-cluster"></a>Odstranění clusteru

Pokud se chcete vyhnout poplatkům za Azure, vyčistěte nepotřebné prostředky. Pomocí příkazu [az group delete][az-group-delete] odeberete skupinu prostředků, službu kontejneru a všechny související prostředky.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> Při odstranění clusteru se neodebere instanční objekt služby Azure Active Directory používaný clusterem AKS. Postup odebrání instančního objektu najdete v tématu věnovaném [aspektům instančního objektu AKS a jeho odstranění][sp-delete].
> 
> Pokud jste použili spravovanou identitu, tato identita je spravovaná platformou a nevyžaduje odebrání.

## <a name="get-the-code"></a>Získání kódu

V tomto rychlém startu se použily již existující image kontejnerů k vytvoření nasazení Kubernetes. Související soubor manifestu Application Code, souboru Dockerfile a Kubernetes jsou [k dispozici na GitHubu.][azure-vote-app]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste nasadili cluster Kubernetes a potom do něj nasadili aplikaci s více kontejnery. [Přístup k webovému řídicímu panelu Kubernetes][kubernetes-dashboard] pro váš cluster AKS.

Další informace o službě AKS a podrobné vysvětlení kompletního příkladu od kódu až po nasazení najdete v kurzu clusteru Kubernetes.

> [!div class="nextstepaction"]
> [Kurz AKS][aks-tutorial]

<!-- LINKS - external -->
[azure-vote-app]: https://github.com/Azure-Samples/azure-voting-app-redis.git
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubeconfig-file]: https://kubernetes.io/docs/concepts/configuration/organize-cluster-access-kubeconfig/

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[aks-monitor]: ../azure-monitor/containers/container-insights-onboard.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-browse]: /cli/azure/aks#az-aks-browse
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-install-cli]: /cli/azure/aks#az-aks-install-cli
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[azure-cli-install]: /cli/azure/install-azure-cli
[azure-monitor-containers]: ../azure-monitor/containers/container-insights-overview.md
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[azure-portal]: https://portal.azure.com
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[kubernetes-dashboard]: kubernetes-dashboard.md
[windows-container-cli]: windows-container-cli.md