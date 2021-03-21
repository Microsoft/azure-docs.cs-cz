---
title: 'Rychlý Start: nasazení clusteru AKS pomocí prostředí PowerShell'
description: Naučte se rychle vytvořit cluster Kubernetes, nasadit aplikaci a monitorovat výkon ve službě Azure Kubernetes Service (AKS) pomocí prostředí PowerShell.
services: container-service
ms.topic: quickstart
ms.date: 03/15/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2b61c791390200beac4a18422a4de58dd94fa711
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103492893"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-cluster-using-powershell"></a>Rychlý Start: nasazení clusteru služby Azure Kubernetes pomocí prostředí PowerShell

Služba Azure Kubernetes Service (AKS) je spravovaná služba Kubernetes, která umožňuje rychle nasadit a spravovat clustery. V tomto rychlém startu budete:
* Nasaďte cluster AKS pomocí prostředí PowerShell. 
* Spusťte aplikaci s více kontejnery s webovým front-end a instancí Redis v clusteru. 
* Monitorujte stav clusteru a lusky, které spouští vaši aplikaci.

Další informace o vytváření fondu uzlů Windows serveru najdete v tématu [Vytvoření clusteru AKS, který podporuje kontejnery Windows serveru][windows-container-powershell].

![Hlasovací aplikace nasazená ve službě Azure Kubernetes](./media/kubernetes-walkthrough-powershell/voting-app-deployed-in-azure-kubernetes-service.png)

Tento rychlý start předpokládá základní znalosti konceptů Kubernetes. Další informace najdete v tématu [základní koncepty Kubernetes pro Azure Kubernetes Service (AKS)][kubernetes-concepts].

## <a name="prerequisites"></a>Předpoklady

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

Pokud používáte PowerShell místně, nainstalujte modul AZ PowerShell a připojte se k účtu Azure pomocí rutiny [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) . Další informace o instalaci modulu AZ PowerShell najdete v tématu [Install Azure PowerShell][install-azure-powershell].

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Pokud máte více předplatných Azure, vyberte odpovídající ID předplatného, ve kterém se prostředky mají fakturovat pomocí rutiny [set-AzContext](/powershell/module/az.accounts/set-azcontext) .

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

[Skupina prostředků Azure](../azure-resource-manager/management/overview.md) je logická skupina, ve které se nasazují a spravují prostředky Azure. Při vytváření skupiny prostředků se zobrazí výzva k zadání umístění. Toto umístění je: 
* Umístění úložiště metadat skupiny prostředků.
* Kde se vaše prostředky spustí v Azure, pokud při vytváření prostředků nezadáte jinou oblast. 

Následující příklad vytvoří skupinu prostředků s názvem **myResourceGroup** v oblasti **eastus** .

Vytvořte skupinu prostředků pomocí rutiny [New-AzResourceGroup][new-azresourcegroup] .

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location eastus
```

Výstup pro úspěšně vytvořenou skupinu prostředků:

```plaintext
ResourceGroupName : myResourceGroup
Location          : eastus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup
```

## <a name="create-aks-cluster"></a>Vytvoření clusteru AKS

1. Vygenerujte pár klíčů SSH pomocí `ssh-keygen` nástroje příkazového řádku. 
    * Další podrobnosti najdete v tématu [rychlé kroky: vytvoření a použití páru veřejných privátních klíčů ssh pro virtuální počítače se systémem Linux v Azure](../virtual-machines/linux/mac-create-ssh-keys.md).

1. Vytvořte cluster AKS pomocí rutiny [New-AzAks][new-azaks] . Ve výchozím nastavení je povoleno Azure Monitor pro kontejnery.

    Následující příklad vytvoří cluster **myAKSCluster** s jedním uzlem. 

    ```azurepowershell-interactive
    New-AzAksCluster -ResourceGroupName myResourceGroup -Name myAKSCluster -NodeCount 1
    ```

Po několika minutách se příkaz dokončí a vrátí informace o clusteru.

> [!NOTE]
> Při vytváření clusteru AKS se automaticky vytvoří druhá skupina prostředků pro ukládání prostředků AKS. Další informace najdete v tématu [Proč jsou dvě skupiny prostředků vytvořené pomocí AKS?](./faq.md#why-are-two-resource-groups-created-with-aks)

## <a name="connect-to-the-cluster"></a>Připojení ke clusteru

Ke správě clusteru Kubernetes použijte klienta příkazového řádku Kubernetes [kubectl][kubectl]. `kubectl` je již nainstalován, pokud používáte Azure Cloud Shell. 

1. `kubectl`Místní instalace pomocí `Install-AzAksKubectl` rutiny:

    ```azurepowershell
    Install-AzAksKubectl
    ```

2. Nakonfigurujte `kubectl` pro připojení ke clusteru Kubernetes pomocí rutiny [Import-AzAksCredential][import-azakscredential] . Následující rutina stáhne pověření a nakonfiguruje rozhraní příkazového řádku Kubernetes pro jejich použití.

    ```azurepowershell-interactive
    Import-AzAksCredential -ResourceGroupName myResourceGroup -Name myAKSCluster
    ```

3. Pomocí příkazu [kubectl Get][kubectl-get] ověřte připojení ke svému clusteru. Tento příkaz vrátí seznam uzlů clusteru.

    ```azurepowershell-interactive
    .\kubectl get nodes
    ```

    Výstup zobrazuje jeden uzel vytvořený v předchozích krocích. Přesvědčte se, zda je stav uzlu *připraveno*:

    ```plaintext
    NAME                       STATUS   ROLES   AGE     VERSION
    aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.15.10
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

    ```azurepowershell-interactive
    .\kubectl apply -f azure-vote.yaml
    ```

    Výstup zobrazuje úspěšně vytvořená nasazení a služby:

    ```plaintext
    deployment.apps/azure-vote-back created
    service/azure-vote-back created
    deployment.apps/azure-vote-front created
    service/azure-vote-front created
    ```

## <a name="test-the-application"></a>Testování aplikace

Když je aplikace spuštěná, služba Kubernetes zpřístupňuje front-end aplikace na internetu. Dokončení tohoto procesu může trvat několik minut.

Sledujte průběh pomocí příkazu [kubectl Get Service][kubectl-get] s `--watch` argumentem.

```azurepowershell-interactive
.\kubectl get service azure-vote-front --watch
```

Výstup **externích IP adres** pro `azure-vote-front` službu se zpočátku zobrazí jako *čeká na vyřízení*.

```plaintext
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Jakmile se adresa **External-IP** změní ze *stavu čeká* na skutečnou veřejnou IP adresu, použijte `CTRL-C` k zastavení `kubectl` procesu sledování. Následující příklad výstupu ukazuje platnou veřejnou IP adresu přiřazenou ke službě:

```plaintext
azure-vote-front   LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Pokud chcete zobrazit hlasovou aplikaci Azure v akci, otevřete webový prohlížeč na externí IP adresu vaší služby.

![Hlasovací aplikace nasazená ve službě Azure Kubernetes](./media/kubernetes-walkthrough-powershell/voting-app-deployed-in-azure-kubernetes-service.png)

Zobrazuje metriky stavu uzlů clusteru a lusky zachycené Azure Monitor pro kontejnery v Azure Portal. 

## <a name="delete-the-cluster"></a>Odstranění clusteru

Pokud se chcete vyhnout poplatkům za Azure, vyčistěte nepotřebné prostředky. Pomocí rutiny [Remove-AzResourceGroup][remove-azresourcegroup] odeberte skupinu prostředků, službu kontejneru a všechny související prostředky.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
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
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[azure-dev-spaces]: ../dev-spaces/index.yml
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[azure-vote-app]: https://github.com/Azure-Samples/azure-voting-app-redis.git

<!-- LINKS - internal -->
[windows-container-powershell]: windows-container-powershell.md
[kubernetes-concepts]: concepts-clusters-workloads.md
[install-azure-powershell]: /powershell/azure/install-az-ps
[new-azresourcegroup]: /powershell/module/az.resources/new-azresourcegroup
[new-azaks]: /powershell/module/az.aks/new-azaks
[import-azakscredential]: /powershell/module/az.aks/import-azakscredential
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[remove-azresourcegroup]: /powershell/module/az.resources/remove-azresourcegroup
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[kubernetes-dashboard]: kubernetes-dashboard.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
