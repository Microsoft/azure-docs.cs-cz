---
title: Rychlý Start – vytvoření clusteru služby Azure Kubernetes (AKS)
description: Naučte se rychle vytvořit cluster Kubernetes pomocí šablony Azure Resource Manager a nasadit aplikaci ve službě Azure Kubernetes Service (AKS).
services: container-service
ms.topic: quickstart
ms.date: 03/15/2021
ms.custom: mvc,subject-armqs, devx-track-azurecli
ms.openlocfilehash: e88c56f050f2f6d1183eef23a844f5eaf1f671c2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "103492927"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-using-an-arm-template"></a>Rychlý Start: nasazení clusteru služby Azure Kubernetes (AKS) pomocí šablony ARM

Služba Azure Kubernetes Service (AKS) je spravovaná služba Kubernetes, která umožňuje rychle nasadit a spravovat clustery. V tomto rychlém startu budete:
* Nasaďte cluster AKS pomocí šablony Azure Resource Manager. 
* Spusťte aplikaci s více kontejnery s webovým front-end a instancí Redis v clusteru. 

![Obrázek přechodu na aplikaci Azure Vote](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Tento rychlý start předpokládá základní znalosti konceptů Kubernetes. Další informace najdete v tématu [základní koncepty Kubernetes pro Azure Kubernetes Service (AKS)][kubernetes-concepts].

Pokud vaše prostředí splňuje požadavky a jste obeznámeni s používáním šablon ARM, vyberte tlačítko **Nasazení do Azure**. Šablona se otevře v prostředí Azure Portal.

[![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-aks%2Fazuredeploy.json)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Tento článek vyžaduje verzi rozhraní příkazového řádku Azure 2.0.61 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.

- Pokud chcete vytvořit cluster AKS pomocí šablony Správce prostředků, zadáte veřejný klíč SSH. Pokud potřebujete tento prostředek, přečtěte si následující část. v opačném případě přejděte k části [Kontrola šablony](#review-the-template) .

### <a name="create-an-ssh-key-pair"></a>Vytvoření páru klíčů SSH

Chcete-li získat přístup k uzlům AKS, připojte se pomocí páru klíčů SSH (Public a Private), který vygenerujete pomocí `ssh-keygen` příkazu. Ve výchozím nastavení se tyto soubory vytvoří v adresáři *~/.ssh* . Spuštěním `ssh-keygen` příkazu se přepíše všechny páry klíčů ssh se stejným názvem už existujícím v daném umístění.

1. [https://shell.azure.com](https://shell.azure.com)V prohlížeči otevřete Cloud Shell.

1. Spusťte příkaz `ssh-keygen`. Následující příklad vytvoří pár klíčů SSH pomocí šifrování RSA a bitovou délku 2048:

    ```console
    ssh-keygen -t rsa -b 2048
    ```

Další informace o vytváření klíčů SSH najdete v tématu [vytváření a Správa klíčů ssh pro ověřování v Azure][ssh-keys].

## <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je jednou z [šablon pro rychlý start Azure](https://azure.microsoft.com/resources/templates/101-aks/).

:::code language="json" source="~/quickstart-templates/101-aks/azuredeploy.json":::

Další ukázky AKS najdete na webu [šablony pro rychlý Start AKS][aks-quickstart-templates] .

## <a name="deploy-the-template"></a>Nasazení šablony

1. Vyberte následující tlačítko, abyste se přihlásili do Azure a otevřeli šablonu.

    [![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-aks%2Fazuredeploy.json)

2. Vyberte nebo zadejte následující hodnoty.

    V tomto rychlém startu ponechte výchozí hodnoty pro *velikost disku operačního systému GB*, *počet agentů*, *Velikost virtuálního počítače agenta*, *typ operačního systému* a *verzi Kubernetes*. Zadejte vlastní hodnoty pro následující parametry šablony:

    * **Předplatné**: vyberte předplatné Azure.
    * **Skupina prostředků**: vyberte **vytvořit novou**. Zadejte jedinečný název pro skupinu prostředků, třeba *myResourceGroup*, a pak zvolte **OK**.
    * **Umístění**: vyberte umístění, například **východní USA**.
    * **Název clusteru**: Zadejte jedinečný název pro cluster AKS, jako je například *myAKSCluster*.
    * **Předpona DNS**: Zadejte jedinečnou předponu DNS pro váš cluster, například *myakscluster*.
    * **Uživatelské jméno správce systému Linux**: zadejte uživatelské jméno pro připojení pomocí protokolu SSH, například *azureuser*.
    * **Veřejný klíč SSH RSA**: Zkopírujte a vložte *veřejnou* část páru klíčů ssh (ve výchozím nastavení obsah *~/.ssh/id_rsa. pub*).

    ![Správce prostředků šablonu pro vytvoření clusteru služby Azure Kubernetes na portálu](./media/kubernetes-walkthrough-rm-template/create-aks-cluster-using-template-portal.png)

3. Vyberte **Zkontrolovat a vytvořit**.

Vytvoření clusteru AKS bude trvat několik minut. Než přejdete k dalšímu kroku, počkejte, než se cluster úspěšně nasadí.

## <a name="validate-the-deployment"></a>Ověření nasazení

### <a name="connect-to-the-cluster"></a>Připojení ke clusteru

Ke správě clusteru Kubernetes použijte klienta příkazového řádku Kubernetes [kubectl][kubectl]. `kubectl` je již nainstalován, pokud používáte Azure Cloud Shell. 

1. `kubectl`Místně nainstalujte pomocí příkazu [AZ AKS Install-CLI][az-aks-install-cli] :

    ```azurecli
    az aks install-cli
    ```

2. Nakonfigurujte `kubectl` pro připojení ke clusteru Kubernetes pomocí příkazu [AZ AKS Get-Credentials][az-aks-get-credentials] . Tento příkaz stáhne pověření a nakonfiguruje rozhraní příkazového řádku Kubernetes pro jejich použití.

    ```azurecli-interactive
    az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
    ```

3. Pomocí příkazu [kubectl Get][kubectl-get] ověřte připojení ke svému clusteru. Tento příkaz vrátí seznam uzlů clusteru.

    ```console
    kubectl get nodes
    ```

    Výstup zobrazuje uzly vytvořené v předchozích krocích. Ujistěte se, že stav všech uzlů je *připravený*:

    ```output
    NAME                       STATUS   ROLES   AGE     VERSION
    aks-agentpool-41324942-0   Ready    agent   6m44s   v1.12.6    
    aks-agentpool-41324942-1   Ready    agent   6m46s   v1.12.6
    aks-agentpool-41324942-2   Ready    agent   6m45s   v1.12.6
    ```

### <a name="run-the-application"></a>Spuštění aplikace

[Soubor manifestu Kubernetes][kubernetes-deployment] definuje požadovaný stav clusteru, například které image kontejnerů se mají spustit. 

V tomto rychlém startu použijete manifest k vytvoření všech objektů potřebných ke spuštění [aplikace hlasování Azure][azure-vote-app]. Tento manifest obsahuje dvě [Kubernetes nasazení][kubernetes-deployment]:
* Ukázková aplikace Pythonu pro Azure
* Instance Redis 

Vytvoří se také dvě [služby Kubernetes][kubernetes-service] :
* Interní služba pro instanci Redis
* Externí služba pro přístup k aplikaci hlasování Azure z Internetu

1. Vytvořte soubor s názvem `azure-vote.yaml`.
    * Pokud Azure Cloud Shell použijete, můžete tento soubor vytvořit pomocí `vi` nebo `nano` jako při práci na virtuálním nebo fyzickém systému.
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

### <a name="test-the-application"></a>Testování aplikace

Když je aplikace spuštěná, služba Kubernetes zpřístupňuje front-end aplikace na internetu. Dokončení tohoto procesu může trvat několik minut.

Sledujte průběh pomocí příkazu [kubectl Get Service][kubectl-get] s `--watch` argumentem.

```console
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

![Obrázek přechodu na aplikaci Azure Vote](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

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
[azure-dev-spaces]: ../dev-spaces/index.yml
[aks-quickstart-templates]: https://azure.microsoft.com/resources/templates/?term=Azure+Kubernetes+Service

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
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[azure-portal]: https://portal.azure.com
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[kubernetes-dashboard]: kubernetes-dashboard.md
[ssh-keys]: ../virtual-machines/linux/create-ssh-keys-detailed.md
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
