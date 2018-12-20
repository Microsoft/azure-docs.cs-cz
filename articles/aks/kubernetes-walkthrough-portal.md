---
title: Rychlý start – vytvoření clusteru Azure Kubernetes Service (AKS) na portálu
description: Zjistěte, jak rychle vytvořit Kubernetes cluster, nasazení aplikace a sledování výkonu ve službě Azure Kubernetes Service (AKS) pomocí webu Azure portal.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: quickstart
ms.date: 12/18/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 39d267c1d113c2f1950650416c18bfd8ad8caa9c
ms.sourcegitcommit: e68df5b9c04b11c8f24d616f4e687fe4e773253c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/20/2018
ms.locfileid: "53653590"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-using-the-azure-portal"></a>Rychlý start: Nasazení clusteru služby Azure Kubernetes Service (AKS) pomocí webu Azure portal

Azure Kubernetes Service (AKS) je spravovaná služba, která vám umožní rychle nasadit a spravovat clustery Kubernetes. V tomto rychlém startu nasadíte cluster AKS pomocí portálu Azure. V clusteru je spustí vícekontejnerová aplikace, která obsahuje webový front-end a Redis instance. Zobrazí se postup sledování stavu clusteru a podů, na kterých běží vaše aplikace.

![Obrázek přechodu na ukázkovou aplikaci Azure Vote](media/container-service-kubernetes-walkthrough/azure-vote.png)

Tento rychlý start předpokládá základní znalosti konceptů Kubernetes. Další informace najdete v tématu [Kubernetes pro Azure Kubernetes Service (AKS) základní koncepty][kubernetes-concepts].

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="create-an-aks-cluster"></a>Vytvoření clusteru AKS

V levém horním rohu webu Azure portal vyberte **+ vytvořit prostředek** > **služby Kubernetes**.

Pokud chcete vytvořit cluster AKS, proveďte následující kroky:

1. **Základy:** Nakonfigurujte následující možnosti:
    - *PODROBNOSTI O PROJEKTU*: Vyberte předplatné Azure, pak vyberte nebo vytvořte skupinu prostředků Azure, jako *myResourceGroup*. Zadejte **Název clusteru Kubernetes**, například *myAKSCluster*.
    - *PODROBNOSTI O CLUSTERU*: Vyberte oblast, verze Kubernetes a předpona názvu DNS pro AKS cluster.
    - *ŠKÁLOVÁNÍ*: Vyberte velikost virtuálního počítače pro uzly AKS. Velikost virtuálního počítače **nejde** změnit po nasazení clusteru AKS.
        - Vyberte počet uzlů, které se mají do clusteru nasadit. Pro účely tohoto rychlého startu nastavte **Počet uzlů** na hodnotu *1*. Počet uzlů **jde** upravit po nasazení clusteru.
    
    ![Vytvoření clusteru AKS – zadání základních informací](media/kubernetes-walkthrough-portal/create-cluster-basics.png)

    Vyberte **Další: Ověřování** po dokončení.

1. **Ověřování**: Nakonfigurujte následující možnosti:
    - Vytvořte nový instanční objekt nebo *nakonfigurujte* použití existujícího. Pokud použijete stávající hlavní název služby (SPN), je potřeba zadat ID klienta a tajný klíč SPN.
    - Povolte možnost řízení přístupu na základě role (RBAC) v Kubernetes. Tyto ovládací prvky poskytují podrobnější řízení přístupu k prostředkům Kubernetes nasazeným ve vašem clusteru AKS.

    Ve výchozím nastavení *základní* sítí se používá a je povoleno monitorování Azure pro kontejnery. Vyberte **Zkontrolovat a vytvořit** a jakmile budete připraveni, vyberte **Vytvořit**.

Vytvoření clusteru AKS a jeho příprava k použití trvá několik minut. Až budete hotovi, přejděte na skupinu prostředků clusteru AKS, jako například *myResourceGroup*a vyberte prostředek AKS, jako je například *myAKSCluster*. Zobrazí se řídicí panel clusteru AKS, podobně jako na následujícím ukázkovém snímku obrazovky:

![Příklad řídicího panelu AKS na webu Azure Portal](media/kubernetes-walkthrough-portal/aks-portal-dashboard.png)

## <a name="connect-to-the-cluster"></a>Připojení ke clusteru

Ke správě clusteru Kubernetes použijete [kubectl][kubectl], klienta příkazového řádku Kubernetes. Klient `kubectl` je předinstalovaný ve službě Azure Cloud Shell.

Otevřete službu Cloud Shell pomocí tlačítka v pravém horním rohu portálu Azure.

![Portál s otevřenou službou Azure Cloud Shell](media/kubernetes-walkthrough-portal/aks-cloud-shell.png)

Pomocí příkazu [az aks get-credentials][az-aks-get-credentials] nakonfigurujte klienta `kubectl` pro připojení k vašemu clusteru Kubernetes. Tento příkaz stáhne přihlašovací údaje a nakonfiguruje rozhraní příkazového řádku Kubernetes pro jejich použití. Následující příklad získá přihlašovací údaje pro název clusteru *myAKSCluster* ve skupině prostředků *myResourceGroup*:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Pokud chcete ověřit připojení ke clusteru, použijte příkaz [kubectl get][kubectl-get], který vrátí seznam uzlů clusteru.

```azurecli-interactive
kubectl get nodes
```

Následující příklad výstupu ukazuje jeden uzel vytvořený v předchozích krocích. Ujistěte se, zda je stav uzlu *připravené*:

```
NAME                       STATUS    ROLES     AGE       VERSION
aks-agentpool-14693408-0   Ready     agent     15m       v1.11.5
```

## <a name="run-the-application"></a>Spuštění aplikace

Soubor manifestu Kubernetes definuje požadovaný stav clusteru, například jaké kontejneru obrázků ke spuštění. V tomto rychlém startu manifest slouží k vytvoření všech objektů potřebných ke spuštění aplikace Azure Vote. Tento manifest zahrnuje dva [nasazení Kubernetes] [ kubernetes-deployment] – jednu pro ukázkové aplikace Azure Vote Python a druhé pro instanci Redis. Dvě [služby Kubernetes] [ kubernetes-service] také vytvářejí – interní služba pro instanci Redis a externí služby pro přístup k aplikaci Azure Vote z Internetu.

> [!TIP]
> V tomto rychlém startu ručně vytvoříte manifest aplikace a nasadíte ho do clusteru AKS. V reálnějších situacích můžete k rychlé iteraci a ladění kódu přímo v clusteru AKS použít [Azure Dev Spaces][azure-dev-spaces]. Dev Spaces můžete používat na různých platformách operačních systémů a v různých vývojových prostředích a spolupracovat s ostatními členy vašeho týmu.

Vytvořte soubor s názvem `azure-vote.yaml` a zkopírujte do následující definice YAML. Ve službě Azure Cloud Shell vytvořte pomocí souboru `vi` nebo `Nano`, stejně jako kdybyste pracovali na virtuálním nebo fyzickém systému:

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

Nasazení aplikace pomocí [použití kubectl] [ kubectl-apply] příkaz a zadejte název vašeho YAML manifestu:

```azurecli-interactive
kubectl apply -f azure-vote.yaml
```

Následující příklad výstupu ukazuje nasazení a služby se úspěšně vytvořil:

```
deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-the-application"></a>Testování aplikace

Při spuštění aplikace, služba Kubernetes poskytuje front-endu aplikace k Internetu. Dokončení tohoto procesu může trvat několik minut.

Pomocí příkazu [kubectl get service][kubectl-get] s argumentem `--watch` můžete sledovat průběh.

```azurecli-interactive
kubectl get service azure-vote-front --watch
```

Zpočátku *EXTERNAL-IP* pro *azure-vote-front* služby se zobrazuje jako *čekající*.

```
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Když *EXTERNAL-IP* adresa se změní z *čekající* skutečné veřejnou IP adresu, použijte `CTRL-C` Zastavit `kubectl` sledujte proces. Následující příklad výstupu ukazuje platnou veřejnou IP adresu přiřazené příslušné službě:

```
azure-vote-front   LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Pokud chcete zobrazit aplikaci Azure Vote v akci, otevřete webový prohlížeč na externí IP adresu vaší služby.

![Obrázek přechodu na ukázkovou aplikaci Azure Vote](media/container-service-kubernetes-walkthrough/azure-vote.png)

## <a name="monitor-health-and-logs"></a>Monitorování stavu a protokolů

Při vytváření clusteru byl povolen monitorování Azure pro kontejnery. Tato funkce monitorování poskytuje metriky stavu clusteru AKS i podů spuštěných na tomto clusteru.

Naplnění těchto dat na webu Azure Portal může trvat několik minut. Pokud chcete zobrazit aktuální stav, dobu provozu a využití prostředků pro pody Azure Vote, na webu Azure Portal přejděte zpět k prostředku AKS, například *myAKSCluster*. Přístup ke stavu potom získáte následujícím způsobem:

1. V části **monitorování** na levé straně zvolte **Insights**
1. V horní části zvolte **+ Přidat filtr**
1. Jako vlastnost vyberte *Obor názvů* a potom zvolte *\<Všechny kromě kube-system\>*.
1. Vyberte zobrazení **Kontejnery**.

Zobrazí se kontejnery *azure-vote-back* a *azure-vote-front*, jak ukazuje následující příklad:

![Zobrazení stavu spuštěných kontejnerů v AKS](media/kubernetes-walkthrough-portal/monitor-containers.png)

Pokud chcete zobrazit protokoly pro pod `azure-vote-front`, vyberte odkaz **Zobrazit protokoly kontejnerů** na pravé straně seznamu kontejnerů. Tyto protokoly obsahují streamy výstupů *stdout* a *stderr* z kontejneru.

![Zobrazení protokolů kontejneru v AKS](media/kubernetes-walkthrough-portal/monitor-container-logs.png)

## <a name="delete-cluster"></a>Odstranění clusteru

Když už cluster nepotřebujete, odstraňte prostředek clusteru, čímž odstraníte všechny související prostředky. Tuto operaci můžete provést na webu Azure Portal výběrem tlačítka **Odstranit** na řídicím panelu clusteru AKS. Případně můžete použít příkaz [az aks delete][az-aks-delete] ve službě Cloud Shell:

```azurecli-interactive
az aks delete --resource-group myResourceGroup --name myAKSCluster --no-wait
```

> [!NOTE]
> Při odstranění clusteru se neodebere instanční objekt služby Azure Active Directory používaný clusterem AKS. Postup odebrání instančního objektu najdete v tématu věnovaném [aspektům instančního objektu AKS a jeho odstranění][sp-delete].

## <a name="get-the-code"></a>Získání kódu

V tomto rychlém startu předem vytvořené Image kontejnerů byly použity k vytvoření nasazení Kubernetes. Související kód aplikace, soubor Dockerfile a soubor manifestu Kubernetes jsou k dispozici na GitHubu.

[https://github.com/Azure-Samples/azure-voting-app-redis][azure-vote-app]

## <a name="next-steps"></a>Další postup

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
