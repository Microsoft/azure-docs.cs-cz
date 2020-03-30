---
title: Úvodní příručka – vytvoření clusteru služby Azure Kubernetes Service (AKS) na portálu
description: Zjistěte, jak rychle vytvořit cluster Kubernetes, nasadit aplikaci a monitorovat výkon ve službě Azure Kubernetes Service (AKS) pomocí portálu Azure.
services: container-service
ms.topic: quickstart
ms.date: 01/21/2020
ms.custom: mvc, seo-javascript-october2019
ms.openlocfilehash: f4885bea686267ce0397e9ca6f3e2c0ac8640971
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240615"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-using-the-azure-portal"></a>Úvodní příručka: Nasazení clusteru služby Azure Kubernetes Service (AKS) pomocí portálu Azure

Azure Kubernetes Service (AKS) je spravovaná služba Kubernetes, která umožňuje rychle nasadit a spravovat clustery. V tomto rychlém startu nasadíte cluster AKS pomocí portálu Azure. V clusteru je spuštěna aplikace s více kontejnery, která obsahuje webový front-end a instanci Redis. Potom uvidíte, jak sledovat stav clusteru a podů, které spouštějí aplikaci.

![Obrázek přechodu na ukázkovou aplikaci Azure Vote](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

Tento rychlý start předpokládá základní znalosti konceptů Kubernetes. Další informace najdete v tématu [Kubernetes základní koncepty pro službu Azure Kubernetes Service (AKS)][kubernetes-concepts].

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k [https://portal.azure.com](https://portal.azure.com)portálu Azure na adrese .

## <a name="create-an-aks-cluster"></a>Vytvoření clusteru AKS

Pokud chcete vytvořit cluster AKS, proveďte následující kroky:

1. V nabídce webu Azure Portal nebo na **domovské stránce** vyberte **Vytvořit prostředek**.

2. Vyberte **kontejnery** >  **Kubernetes Service**.

3. Na stránce **Základy** nakonfigurujte následující možnosti:
    - **Podrobnosti o projektu**: Vyberte **předplatné**Azure a vyberte nebo vytvořte **skupinu prostředků**Azure , například *myResourceGroup*.
    - **Podrobnosti clusteru**: Zadejte **název clusteru Kubernetes**, například *myAKSCluster*. Vyberte **předponu Výraz**, **Kubernetes ova cenáa**a **předponu názvu DNS** pro cluster AKS.
    - **Fond primárních uzlů**: Vyberte **velikost uzlu virtuálního** počítače pro uzly AKS. Velikost virtuálního počítače *nelze* změnit po nasazení clusteru AKS. 
            - Vyberte počet uzlů, které chcete nasadit do clusteru. Pro účely tohoto rychlého startu nastavte **Počet uzlů** na hodnotu *1*. Počet uzlů *jde* upravit po nasazení clusteru.
    
    ![Vytvoření clusteru AKS – zadání základních informací](media/kubernetes-walkthrough-portal/create-cluster-basics.png)

    Vyberte další: Po dokončení **změnit měřítko.**

4. Na stránce **Měřítko** zachováte výchozí volby. V dolní části obrazovky klikněte na **Další: Ověřování**.
    > [!CAUTION]
    > Vytvoření nových objektů zabezpečení služeb AAD může trvat několik minut k šíření a k dispozici způsobuje instanční nebyly nalezeny chyby a selhání ověření na webu Azure Portal. Pokud narazíte na to, navštivte [zde](troubleshooting.md#im-receiving-errors-that-my-service-principal-was-not-found-when-i-try-to-create-a-new-cluster-without-passing-in-an-existing-one) pro zmírnění.

5. Na stránce **Ověřování** nakonfigurujte následující možnosti:
    - Vytvořte nový instanční objekt tak, že ponecháte pole **Zaregistrovaný objekt servisu** s **(novým) výchozím instančním objektem .** Nebo můžete zvolit *Konfigurovat instanční objekt* pro použití existujícího objektu. Pokud používáte existující, budete muset zadat ID klienta SPN a tajný klíč.
    - Povolte možnost řízení přístupu na základě role (RBAC) v Kubernetes. To zajistí podrobnější kontrolu nad přístupem k prostředkům Kubernetes nasazeným v clusteru AKS.

Ve výchozím nastavení se používá *základní* sítě a Azure Monitor pro kontejnery je povolená. Klikněte na **Revize + vytvořit** a potom **vytvořit** po dokončení ověření.

Vytvoření clusteru AKS trvá několik minut. Po dokončení nasazení klikněte na **Přejít na prostředek**nebo přejděte do skupiny prostředků clusteru AKS, například *myResourceGroup*, a vyberte prostředek AKS, například *myAKSCluster*. Řídicí panel clusteru AKS je zobrazen, jako v tomto příkladu:

![Příklad řídicího panelu AKS na webu Azure Portal](media/kubernetes-walkthrough-portal/aks-portal-dashboard.png)

## <a name="connect-to-the-cluster"></a>Připojení ke clusteru

Chcete-li spravovat cluster Kubernetes, použijte [kubectl][kubectl], klientpříkazového řádku Kubernetes. Klient `kubectl` je předinstalovaný ve službě Azure Cloud Shell.

Otevřete Cloud `>_` Shell pomocí tlačítka v horní části portálu Azure.

![Portál s otevřenou službou Azure Cloud Shell](media/kubernetes-walkthrough-portal/aks-cloud-shell.png)

Pomocí příkazu [az aks get-credentials][az-aks-get-credentials] nakonfigurujte klienta `kubectl` pro připojení k vašemu clusteru Kubernetes. Tento příkaz stáhne pověření a nakonfiguruje rozhraní příkazového příkazu Kubernetes tak, aby je používalo. Následující příklad získá přihlašovací údaje pro název clusteru *myAKSCluster* ve skupině prostředků *myResourceGroup*:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Pokud chcete ověřit připojení ke clusteru, použijte příkaz [kubectl get][kubectl-get], který vrátí seznam uzlů clusteru.

```console
kubectl get nodes
```

Následující příklad výstupu ukazuje jeden uzel vytvořený v předchozích krocích. Ujistěte se, že stav uzlu je *připraven*:

```output
NAME                       STATUS    ROLES     AGE       VERSION
aks-agentpool-14693408-0   Ready     agent     15m       v1.11.5
```

## <a name="run-the-application"></a>Spuštění aplikace

Soubor manifestu Kubernetes definuje požadovaný stav clusteru, například jaké image kontejneru mají být spuštěny. V tomto rychlém startu manifest slouží k vytvoření všech objektů potřebných ke spuštění aplikace Azure Vote. Tento manifest zahrnuje dvě [nasazení Kubernetes][kubernetes-deployment] – jedno pro ukázkové aplikace Azure Vote Python a druhé pro instanci Redis. Jsou také vytvořeny dvě [služby Kubernetes][kubernetes-service] – interní služba pro instanci Redis a externí služba pro přístup k aplikaci Azure Vote z internetu.

> [!TIP]
> V tomto rychlém startu ručně vytvoříte manifest aplikace a nasadíte ho do clusteru AKS. V reálnějších situacích můžete k rychlé iteraci a ladění kódu přímo v clusteru AKS použít [Azure Dev Spaces][azure-dev-spaces]. Dev Spaces můžete používat na různých platformách operačních systémů a v různých vývojových prostředích a spolupracovat s ostatními členy vašeho týmu.

V prostředí Cloud Shell `nano azure-vote.yaml` vytvořte pomocí příkazu nebo `vi azure-vote.yaml` soubor s názvem `azure-vote.yaml`. Potom zkopírujte v následující definici YAML:

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

Nasazení aplikace pomocí příkazu [kubectl apply][kubectl-apply] a zadejte název manifestu YAML:

```console
kubectl apply -f azure-vote.yaml
```

Následující ukázkový výstup ukazuje úspěšně vytvořené nasazení a služby:

```output
deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-the-application"></a>Testování aplikace

Při spuštění aplikace služba Kubernetes zpřístupňuje front-end aplikace k internetu. Dokončení tohoto procesu může trvat několik minut.

Pomocí příkazu [kubectl get service][kubectl-get] s argumentem `--watch` můžete sledovat průběh.

```console
kubectl get service azure-vote-front --watch
```

Zpočátku *EXTERNAL-IP* pro *azure-hlasování front* služby se zobrazí jako *čekající*.

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Když se adresa *EXTERNAL-IP* změní z *čekající* na `CTRL-C` skutečnou `kubectl` veřejnou IP adresu, použijte k zastavení procesu sledování. Následující ukázkový výstup ukazuje platnou veřejnou IP adresu přiřazenou službě:

```output
azure-vote-front   LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Chcete-li zobrazit aplikaci Azure Vote v akci, otevřete webový prohlížeč na externí IP adresu vaší služby.

![Obrázek přechodu na ukázkovou aplikaci Azure Vote](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

## <a name="monitor-health-and-logs"></a>Monitorování stavu a protokolů

Když jste vytvořili cluster, Azure Monitor pro kontejnery byla povolena. Tato funkce monitorování poskytuje metriky stavu clusteru AKS i podů spuštěných na tomto clusteru.

Naplnění těchto dat na webu Azure Portal může trvat několik minut. Pokud chcete zobrazit aktuální stav, dobu provozu a využití prostředků pro pody Azure Vote, na webu Azure Portal přejděte zpět k prostředku AKS, například *myAKSCluster*. Přístup ke stavu potom získáte následujícím způsobem:

1. V části **Monitorování** na levé straně zvolte **Přehledy**
1. V horní části zvolte **+ Přidat filtr**
1. Jako vlastnost vyberte *Obor názvů* a potom zvolte *\<Všechny kromě kube-system\>*.
1. Vyberte zobrazení **Kontejnery**.

Zobrazí se kontejnery *azure-vote-back* a *azure-vote-front*, jak ukazuje následující příklad:

![Zobrazení stavu spuštěných kontejnerů v AKS](media/kubernetes-walkthrough-portal/monitor-containers.png)

Chcete-li zobrazit `azure-vote-front` protokoly pro pod, vyberte **zobrazit protokoly kontejnerů** z rozevíracího seznamu kontejnerů. Tyto protokoly obsahují streamy výstupů *stdout* a *stderr* z kontejneru.

![Zobrazení protokolů kontejneru v AKS](media/kubernetes-walkthrough-portal/monitor-container-logs.png)

## <a name="delete-cluster"></a>Odstranění clusteru

Když už cluster nepotřebujete, odstraňte prostředek clusteru, čímž odstraníte všechny související prostředky. Tuto operaci lze dokončit na portálu Azure výběrem tlačítka **Odstranit** na řídicím panelu clusteru AKS. Případně lze příkaz [az aks delete][az-aks-delete] použít v prostředí Cloud Shell:

```azurecli-interactive
az aks delete --resource-group myResourceGroup --name myAKSCluster --no-wait
```

> [!NOTE]
> Při odstranění clusteru se neodebere instanční objekt služby Azure Active Directory používaný clusterem AKS. Postup odebrání instančního objektu najdete v tématu věnovaném [aspektům instančního objektu AKS a jeho odstranění][sp-delete].

## <a name="get-the-code"></a>Získání kódu

V tomto rychlém startu byly k vytvoření nasazení Kubernetes použity předem vytvořené image kontejneru. Související kód aplikace, soubor Dockerfile a soubor manifestu Kubernetes jsou k dispozici na GitHubu.

[https://github.com/Azure-Samples/azure-voting-app-redis][azure-vote-app]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste nasadili cluster Kubernetes a do něj jste nasadili vícekontejnerovou aplikaci.

Další informace o službě AKS a podrobné vysvětlení kompletního příkladu od kódu až po nasazení najdete v kurzu clusteru Kubernetes.

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
