---
title: Rychlý Start – vytvoření clusteru Azure Kubernetes Service (AKS) na portálu
description: Naučte se rychle vytvořit cluster Kubernetes, nasadit aplikaci a monitorovat výkon ve službě Azure Kubernetes Service (AKS) pomocí Azure Portal.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: quickstart
ms.date: 5/31/2019
ms.author: mlearned
ms.custom: mvc, seo-javascript-october2019
ms.openlocfilehash: 86d7f74a5a7260c5feb9a41c6b9c3c93d61388a6
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2019
ms.locfileid: "72255535"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-using-the-azure-portal"></a>Rychlý Start: nasazení clusteru služby Azure Kubernetes (AKS) pomocí Azure Portal

Služba Azure Kubernetes Service (AKS) je spravovaná služba Kubernetes, která umožňuje rychle nasadit a spravovat clustery. V tomto rychlém startu nasadíte cluster AKS pomocí Azure Portal. Aplikace s více kontejnery, která zahrnuje webový front-end a instanci Redis, se spouští v clusteru. Pak uvidíte, jak monitorovat stav clusteru a lusky, které spouští vaši aplikaci.

![Obrázek přechodu na ukázkovou aplikaci pro hlasování v Azure](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

V tomto rychlém startu se předpokládá základní znalost konceptů Kubernetes. Další informace najdete v tématu [základní koncepty Kubernetes pro Azure Kubernetes Service (AKS)][kubernetes-concepts].

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="sign-in-to-azure"></a>Přihlaste se k Azure

Přihlaste se k Azure Portal v https://portal.azure.com.

## <a name="create-an-aks-cluster"></a>Vytvoření clusteru AKS

V levém horním rohu Azure Portal vyberte **+ vytvořit prostředek** > **kontejnery** >  **Kubernetes služba**.

Pokud chcete vytvořit cluster AKS, proveďte následující kroky:

1. Na stránce **základy** nakonfigurujte následující možnosti:
   - *Podrobnosti o projektu*: vyberte předplatné Azure a pak vyberte nebo vytvořte skupinu prostředků Azure, například *myResourceGroup*. Zadejte **název clusteru Kubernetes**, například *myAKSCluster*.
   - *Podrobnosti o clusteru*: Vyberte oblast, verzi Kubernetes a předponu názvu DNS pro cluster AKS.
   - **Fond primárních uzlů**: vyberte velikost virtuálního počítače pro uzly AKS. Po nasazení clusteru AKS **nejde** změnit velikost virtuálního počítače. 
       - Vyberte počet uzlů, které mají být do clusteru nasazeny. V tomto rychlém startu nastavte **počet uzlů** na *1*. Počet uzlů se **dá** upravit po nasazení clusteru.
    
     ![Vytvoření clusteru AKS – poskytnutí základních informací](media/kubernetes-walkthrough-portal/create-cluster-basics.png)

     Po dokončení vyberte **Další: škálovat** .

2. Na stránce **škálování** ponechte výchozí možnosti. V dolní části obrazovky klikněte na **Další: ověřování**.
> [!CAUTION]
> Vytváření nových objektů služby AAD může trvat několik minut, než se rozšíří a stane se k dispozici, což způsobí, že se instanční objekt nenalezne chyby a chyby ověřování v Azure Portal. Pokud [k tomu](troubleshooting.md#im-receiving-errors-that-my-service-principal-was-not-found-when-i-try-to-create-a-new-cluster-without-passing-in-an-existing-one) dojde, navštivte prosím tuto skutečnost pro zmírnění rizik.
3. Na stránce **ověřování** nakonfigurujte následující možnosti:
   - Vytvořte nový instanční objekt tím, že zachováte pole **instanční objekt** s **výchozím instančním objektem (nový)** . Případně můžete zvolit možnost *Konfigurovat instanční objekt* pro použití existujícího objektu. Pokud použijete existující, budete muset zadat ID a tajný klíč klienta SPN.
   - Povolte možnost pro Kubernetes řízení přístupu na základě role (RBAC). Tím zajistíte podrobnější kontrolu nad přístupem k prostředkům Kubernetes nasazeným v clusteru AKS.

Ve výchozím nastavení se používá *základní* sítě a Azure monitor pro kontejnery jsou povoleny. Po dokončení ověřování klikněte na **zkontrolovat + vytvořit** a pak **vytvořit** .

Vytvoření clusteru AKS trvá několik minut. Po dokončení nasazení klikněte na **Přejít k prostředku**nebo přejděte do skupiny prostředků clusteru AKS, jako je třeba *myResourceGroup*, a vyberte prostředek AKS, jako je třeba *myAKSCluster*. Zobrazí se řídicí panel clusteru AKS, jako v tomto příkladu:

![Příklad řídicího panelu AKS v Azure Portal](media/kubernetes-walkthrough-portal/aks-portal-dashboard.png)

## <a name="connect-to-the-cluster"></a>Připojit ke clusteru

Ke správě clusteru Kubernetes použijete klienta příkazového řádku Kubernetes [kubectl][kubectl]. Klient `kubectl` je předem nainstalován v Azure Cloud Shell.

Otevřete Cloud Shell pomocí tlačítka `>_` v horní části Azure Portal.

![Otevřete Azure Cloud Shell na portálu.](media/kubernetes-walkthrough-portal/aks-cloud-shell.png)

Pokud chcete nakonfigurovat `kubectl` pro připojení ke clusteru Kubernetes, použijte příkaz [AZ AKS Get-Credentials][az-aks-get-credentials] . Tento příkaz stáhne pověření a nakonfiguruje rozhraní příkazového řádku Kubernetes pro jejich použití. Následující příklad vrátí pověření pro název clusteru *myAKSCluster* ve skupině prostředků s názvem *myResourceGroup*:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Pokud chcete ověřit připojení ke clusteru, použijte příkaz [kubectl Get][kubectl-get] , který vrátí seznam uzlů clusteru.

```azurecli-interactive
kubectl get nodes
```

Následující příklad výstupu ukazuje jeden uzel vytvořený v předchozích krocích. Ujistěte se, že stav uzlu je *připravený*:

```
NAME                       STATUS    ROLES     AGE       VERSION
aks-agentpool-14693408-0   Ready     agent     15m       v1.11.5
```

## <a name="run-the-application"></a>Spuštění aplikace

Soubor manifestu Kubernetes definuje požadovaný stav clusteru, například jaké image kontejnerů se mají spustit. V tomto rychlém startu se manifest používá k vytvoření všech objektů potřebných ke spuštění aplikace hlasování Azure. Tento manifest obsahuje dvě [Kubernetes nasazení][kubernetes-deployment] – jeden pro ukázkové aplikace v Pythonu pro Azure a druhý pro instanci Redis. Vytvoří se také dvě [služby Kubernetes Services][kubernetes-service] – interní služba pro instanci Redis a externí služba pro přístup k aplikaci hlasování Azure z Internetu.

> [!TIP]
> V tomto rychlém startu ručně vytvoříte a nasadíte manifesty aplikací do clusteru AKS. V dalších scénářích reálného světa můžete použít [Azure dev Spaces][azure-dev-spaces] k rychlému iterování a ladění kódu přímo v clusteru AKS. Můžete použít vývojové prostory napříč platformami operačních systémů a vývojovým prostředím a spolupracovat s ostatními uživateli v týmu.

Ve službě cloud Shell pomocí `nano` nebo `vi` vytvořte soubor s názvem `azure-vote.yaml` a zkopírujte následující definici YAML:

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
        image: redis
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
        image: microsoft/azure-vote-front:v1
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

Nasaďte aplikaci pomocí příkazu [kubectl Apply][kubectl-apply] a zadejte název manifestu YAML:

```azurecli-interactive
kubectl apply -f azure-vote.yaml
```

Následující příklad výstupu ukazuje, že se nasazení a služby úspěšně vytvořily:

```
deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-the-application"></a>Testování aplikace

Když je aplikace spuštěná, služba Kubernetes zpřístupňuje front-end aplikace na internetu. Dokončení tohoto procesu může trvat několik minut.

Pokud chcete sledovat průběh, použijte příkaz [kubectl Get Service][kubectl-get] s argumentem `--watch`.

```azurecli-interactive
kubectl get service azure-vote-front --watch
```

Zpočátku je *externí IP adresa* pro službu *Azure-hlas-front-end* zobrazená jako *nevyřízená*.

```
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Pokud se *IP* adresa změní z *čeká* na skutečnou veřejnou ip adresu, použijte `CTRL-C` a zastavte tak proces kukátka `kubectl`. Následující příklad výstupu ukazuje platnou veřejnou IP adresu přiřazenou ke službě:

```
azure-vote-front   LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Pokud chcete zobrazit hlasovou aplikaci Azure v akci, otevřete webový prohlížeč na externí IP adresu vaší služby.

![Obrázek přechodu na ukázkovou aplikaci pro hlasování v Azure](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

## <a name="monitor-health-and-logs"></a>Monitorování stavu a protokolů

Při vytváření clusteru byla povolena Azure Monitor pro kontejnery. Tato funkce monitorování poskytuje metriky stavu pro cluster AKS i pro lusky spuštěné v clusteru.

Naplnění těchto dat v Azure Portal může trvat několik minut. Pokud chcete zobrazit aktuální stav, dobu provozu a využití prostředků pro hlasování Azure, přejděte zpátky na prostředek AKS v Azure Portal, jako je třeba *myAKSCluster*. Pak můžete získat přístup ke stavu následujícím způsobem:

1. V části **monitorování** na levé straně vyberte **přehledy** .
1. V horní části vyberte možnost **+ Přidat filtr** .
1. Jako vlastnost vyberte *obor názvů* a pak zvolte *\<All, ale Kube-System @ no__t-3* .
1. Vyberte zobrazení **kontejnerů**.

Zobrazí se kontejnery Azure *-back* - *end a Azure-hlasování* , jak je znázorněno v následujícím příkladu:

![Zobrazení stavu spuštěných kontejnerů v AKS](media/kubernetes-walkthrough-portal/monitor-containers.png)

Pokud chcete zobrazit protokoly pro `azure-vote-front` pod, vyberte v rozevíracím seznamu kontejnery možnost **Zobrazit protokoly kontejnerů** . Tyto protokoly zahrnují streamy *stdout* a *stderr* z kontejneru.

![Zobrazení protokolů kontejnerů v AKS](media/kubernetes-walkthrough-portal/monitor-container-logs.png)

## <a name="delete-cluster"></a>Odstranit cluster

Pokud už cluster nepotřebujete, odstraňte prostředek clusteru, čímž odstraníte všechny přidružené prostředky. Tuto operaci můžete dokončit v Azure Portal tak, že na řídicím panelu clusteru AKS vyberete tlačítko **Odstranit** . Alternativně lze použít příkaz [AZ AKS Delete][az-aks-delete] v Cloud Shell:

```azurecli-interactive
az aks delete --resource-group myResourceGroup --name myAKSCluster --no-wait
```

> [!NOTE]
> Při odstranění clusteru se neodebere objekt Azure Active Directory služby používaný clusterem AKS. Postup odebrání instančního objektu najdete v tématu [AKS a informace o instančním objektu a jejich odstranění][sp-delete].

## <a name="get-the-code"></a>Získat kód

V tomto rychlém startu se k vytvoření nasazení Kubernetes použily předem vytvořené image kontejneru. Související soubor manifestu Application Code, souboru Dockerfile a Kubernetes jsou k dispozici na GitHubu.

[https://github.com/Azure-Samples/azure-voting-app-redis][azure-vote-app]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste nasadili cluster Kubernetes a do něj jste nasadili aplikaci s více kontejnery.

Pokud chcete získat další informace o AKS a projít si kompletní kód pro nasazení, přejděte ke kurzu clusteru Kubernetes.

> [!div class="nextstepaction"]
> [Kurz k AKS][aks-tutorial]

<!-- LINKS - external -->
[azure-vote-app]: https://github.com/Azure-Samples/azure-voting-app-redis.git
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-documentation]: https://kubernetes.io/docs/home/

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-aks-delete]: /cli/azure/aks#az-aks-delete
[aks-monitor]: ../monitoring/monitoring-container-health.md
[aks-network]: ./concepts-network.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[http-routing]: ./http-application-routing.md
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[azure-dev-spaces]: https://docs.microsoft.com/azure/dev-spaces/
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
