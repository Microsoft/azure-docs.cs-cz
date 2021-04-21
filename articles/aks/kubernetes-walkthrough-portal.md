---
title: 'Rychlý Start: nasazení clusteru AKS pomocí Azure Portal'
titleSuffix: Azure Kubernetes Service
description: Naučte se rychle vytvořit cluster Kubernetes, nasadit aplikaci a monitorovat výkon ve službě Azure Kubernetes Service (AKS) pomocí Azure Portal.
services: container-service
ms.topic: quickstart
ms.date: 03/15/2021
ms.custom: mvc, seo-javascript-october2019, contperf-fy21q3
ms.openlocfilehash: 28ba2ffd2007aeb45081cf66b05395a2b8456bf7
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779700"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-using-the-azure-portal"></a>Rychlý Start: nasazení clusteru služby Azure Kubernetes (AKS) pomocí Azure Portal

Služba Azure Kubernetes Service (AKS) je spravovaná služba Kubernetes, která umožňuje rychle nasadit a spravovat clustery. V tomto rychlém startu budete:
* Nasaďte cluster AKS pomocí Azure Portal. 
* Spusťte aplikaci s více kontejnery s webovým front-end a instancí Redis v clusteru. 
* Monitorujte stav clusteru a lusky, které spouští vaši aplikaci.

![Obrázek přechodu na ukázkovou aplikaci Azure Vote](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

Tento rychlý start předpokládá základní znalosti konceptů Kubernetes. Další informace najdete v tématu [základní koncepty Kubernetes pro Azure Kubernetes Service (AKS)][kubernetes-concepts].

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Požadavky

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="create-an-aks-cluster"></a>Vytvoření clusteru AKS

1. V nabídce webu Azure Portal nebo na **domovské stránce** vyberte **Create a resource** (Vytvořit prostředek).

2. Vyberte **Containers** (Kontejnery)  >  **Kubernetes Service**.

3. Na kartě **Basics** (Základy) nakonfigurujte následující možnosti:
    - **Podrobnosti o projektu**: 
        * Vyberte **předplatné** Azure.
        * Vyberte nebo vytvořte **skupinu prostředků** Azure, například *myResourceGroup*.
    - **Podrobnosti o clusteru**: 
        * Zadejte **Název clusteru Kubernetes**, například *myAKSCluster*. 
        * Vyberte **oblast** a **verzi KUBERNETES** pro cluster AKS.
    - **Fond primárních uzlů**: 
        * Vyberte **velikosti uzlu** virtuálního počítače pro uzly AKS. Velikost virtuálního počítače *nejde* změnit po nasazení clusteru AKS.
        * Vyberte počet uzlů, které se mají do clusteru nasadit. Pro účely tohoto rychlého startu nastavte **Počet uzlů** na hodnotu *1*. Počet uzlů *jde* upravit po nasazení clusteru.
    
    ![Vytvoření clusteru AKS – zadání základních informací](media/kubernetes-walkthrough-portal/create-cluster-basics.png)

4. Po dokončení vyberte **Další: fondy uzlů** .

5. Ponechejte výchozí možnosti **fondů uzlů** . V dolní části obrazovky klikněte na **Další: ověřování**.
    > [!CAUTION]
    > Nově vytvořené objekty služby Azure AD může trvat několik minut, než se rozšíří a bude k dispozici, což způsobí, že se v Azure Portal nenalezený instanční objekt nenašl a chyby ověřování. Pokud se dostanete k tomuto nárazníku, navštivte [náš článek věnované řešení potíží](troubleshooting.md#received-an-error-saying-my-service-principal-wasnt-found-or-is-invalid-when-i-try-to-create-a-new-cluster) , který vám umožní zmírnit.

6. Na kartě **Authentication** (Ověřování) nakonfigurujte následující možnosti:
    - Pomocí těchto akcí vytvořte novou identitu clusteru:
        * Ponechte pole **ověřování** pomocí **spravované identity System-assinged** nebo
        * Výběr **instančního objektu** pro použití instančního objektu. 
            * Vyberte *(nový) výchozí instanční objekt* pro vytvoření výchozího instančního objektu nebo
            * Vyberte *Konfigurovat instanční objekt* pro použití existujícího objektu Service. Budete muset zadat ID a tajný klíč stávajícího hlavního názvu služby (SPN).
    - Chcete-li zajistit přesnější kontrolu přístupu k prostředkům Kubernetes nasazeným v clusteru AKS, povolte možnost Kubernetes řízení přístupu na základě role (Kubernetes RBAC).

    Ve výchozím nastavení se používá *základní* sítě a Azure monitor pro kontejnery jsou povoleny. 

7. Klikněte na **Review + create** (Zkontrolovat a vytvořit) a po dokončení ověření na **Create** (Vytvořit). 


8. Vytvoření clusteru AKS bude trvat několik minut. Po dokončení nasazení přejděte k prostředku pomocí těchto kroků:
    * Klikněte na **Přejít k prostředku** nebo
    * Přejděte do skupiny prostředků clusteru AKS a vyberte prostředek AKS. 
        * Na příklad řídicího panelu clusteru níže: procházení pro *myResourceGroup* a výběr prostředku *myAKSCluster* .

        ![Příklad řídicího panelu AKS na webu Azure Portal](media/kubernetes-walkthrough-portal/aks-portal-dashboard.png)

## <a name="connect-to-the-cluster"></a>Připojení ke clusteru

Ke správě clusteru Kubernetes použijte klienta příkazového řádku Kubernetes [kubectl][kubectl]. `kubectl` je již nainstalován, pokud používáte Azure Cloud Shell. 

1. Pomocí `>_` tlačítka v horní části Azure Portal otevřete Cloud Shell.

    ![Portál s otevřenou službou Azure Cloud Shell](media/kubernetes-walkthrough-portal/aks-cloud-shell.png)

    > [!NOTE]
    > Postup při provádění těchto operací v instalaci místního prostředí:
    > 1. Ověřte, že je nainstalované rozhraní příkazového řádku Azure.
    > 2. Pomocí příkazu se připojte k Azure `az login` .

2. Nakonfigurujte `kubectl` pro připojení ke clusteru Kubernetes pomocí příkazu [AZ AKS Get-Credentials][az-aks-get-credentials] . Následující příkaz stáhne pověření a nakonfiguruje rozhraní příkazového řádku Kubernetes pro jejich použití.

    ```azurecli
    az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
    ```

3. Ověřte připojení ke clusteru pomocí `kubectl get` a vraťte seznam uzlů clusteru.

    ```console
    kubectl get nodes
    ```

    Výstup zobrazuje jeden uzel vytvořený v předchozích krocích. Přesvědčte se, zda je stav uzlu *připraveno*:

    ```output
    NAME                       STATUS    ROLES     AGE       VERSION
    aks-agentpool-14693408-0   Ready     agent     15m       v1.11.5
    ```

## <a name="run-the-application"></a>Spuštění aplikace

Soubor manifestu Kubernetes definuje požadovaný stav clusteru, například které image kontejnerů se mají spustit. 

V tomto rychlém startu použijete manifest k vytvoření všech objektů potřebných ke spuštění aplikace hlasování Azure. Tento manifest obsahuje dvě Kubernetes nasazení:
* Ukázková aplikace Pythonu pro Azure
* Instance Redis 

Vytvoří se také dvě služby Kubernetes:
* Interní služba pro instanci Redis
* Externí služba pro přístup k aplikaci hlasování Azure z Internetu

1. V Cloud Shell pomocí editoru vytvořte soubor s názvem `azure-vote.yaml` , například:
    * `code azure-vote.yaml`
    * `nano azure-vote.yaml`, nebo 
    * `vi azure-vote.yaml`. 

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

1. Nasaďte aplikaci pomocí `kubectl apply` příkazu a zadejte název vašeho MANIFESTU YAML:

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

Chcete-li sledovat průběh, použijte `kubectl get service` příkaz s `--watch` argumentem.

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

![Obrázek přechodu na ukázkovou aplikaci Azure Vote](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

## <a name="monitor-health-and-logs"></a>Monitorování stavu a protokolů

Při vytváření clusteru byla povolena Azure Monitor pro kontejnery. Azure Monitor for Containers poskytuje metriky stavu pro cluster AKS i pro lusky spuštěné v clusteru.

Naplnění dat metriky v Azure Portal trvá několik minut. Pokud chcete zobrazit aktuální stav, dobu provozu a využití prostředků pro hlasování Azure, postupujte takto:

1. V Azure Portal přejděte zpět na prostředek AKS.
1. V části **monitorování** na levé straně vyberte **přehledy**.
1. V horní části vyberte možnost **+ Přidat filtr**.
1. Jako vlastnost vyberte **obor názvů** a pak zvolte *\<All but kube-system\>* .
1. Vyberte **kontejnery** , které chcete zobrazit.

`azure-vote-back`Zobrazí se `azure-vote-front` kontejnery a, jak je znázorněno v následujícím příkladu:

![Zobrazení stavu spuštěných kontejnerů v AKS](media/kubernetes-walkthrough-portal/monitor-containers.png)

Chcete-li zobrazit protokoly pro `azure-vote-front` pod, vyberte možnost **Zobrazit protokoly kontejnerů** v rozevíracím seznamu Seznam kontejnerů. Tyto protokoly obsahují streamy výstupů *stdout* a *stderr* z kontejneru.

![Zobrazení protokolů kontejneru v AKS](media/kubernetes-walkthrough-portal/monitor-container-logs.png)

## <a name="delete-cluster"></a>Odstranění clusteru

Pokud se chcete vyhnout poplatkům za Azure, vyčistěte nepotřebné prostředky. Vyberte tlačítko **Odstranit** na řídicím panelu clusteru AKS. Můžete také použít příkaz [AZ AKS Delete][az-aks-delete] v Cloud Shell:

```azurecli
az aks delete --resource-group myResourceGroup --name myAKSCluster --no-wait
```
> [!NOTE]
> Při odstranění clusteru se neodebere instanční objekt služby Azure Active Directory používaný clusterem AKS. Postup odebrání instančního objektu najdete v tématu věnovaném [aspektům instančního objektu AKS a jeho odstranění][sp-delete].
> 
> Pokud jste použili spravovanou identitu, tato identita je spravovaná platformou a nevyžaduje odebrání.

## <a name="get-the-code"></a>Získání kódu

V tomto rychlém startu se použily již existující image kontejnerů k vytvoření nasazení Kubernetes. Související soubor manifestu Application Code, souboru Dockerfile a Kubernetes jsou [k dispozici na GitHubu.][azure-vote-app]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste nasadili cluster Kubernetes a potom do něj nasadili aplikaci s více kontejnery. Přístup k webovému řídicímu panelu Kubernetes pro váš cluster AKS.


Pokud chcete získat další informace o AKS, Projděte si kompletní příklad, včetně sestavování aplikace, nasazení z Azure Container Registry, aktualizaci spuštěné aplikace a škálování a upgrade clusteru, a pokračujte do kurzu clusteru Kubernetes.

> [!div class="nextstepaction"]
> [Kurz AKS][aks-tutorial]

<!-- LINKS - external -->
[azure-vote-app]: https://github.com/Azure-Samples/azure-voting-app-redis.git
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-documentation]: https://kubernetes.io/docs/home/

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-aks-delete]: /cli/azure/aks#az_aks_delete
[aks-monitor]: ../azure-monitor/containers/container-insights-overview.md
[aks-network]: ./concepts-network.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[http-routing]: ./http-application-routing.md
[sp-delete]: kubernetes-service-principal.md#additional-considerations