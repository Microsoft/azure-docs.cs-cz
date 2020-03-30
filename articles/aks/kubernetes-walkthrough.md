---
title: 'Úvodní příručka: Nasazení clusteru služby Azure Kubernetes'
description: Zjistěte, jak rychle vytvořit cluster Kubernetes, nasadit aplikaci a monitorovat výkon ve službě Azure Kubernetes Service (AKS) pomocí příkazového příkazového příkazu k síti Azure.
services: container-service
ms.topic: quickstart
ms.date: 09/13/2019
ms.custom:
- H1Hack27Feb2017
- mvc
- devcenter
- seo-javascript-september2019
- seo-javascript-october2019
- seo-python-october2019
ms.openlocfilehash: 0317be6652ff5f03c4c095788ecdde7cba3d1d98
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240461"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-cluster-using-the-azure-cli"></a>Úvodní příručka: Nasazení clusteru služby Azure Kubernetes pomocí příkazového příkazového příkazu Azure

V tomto rychlém startu nasadíte cluster služby Azure Kubernetes Service (AKS) pomocí příkazového příkazového příkazu k síti Azure. AKS je spravovaná služba Kubernetes, která umožňuje rychlé nasazení a správu clusterů. V clusteru je spuštěna aplikace s více kontejnery, která obsahuje webový front-end a instanci Redis. Potom uvidíte, jak sledovat stav clusteru a podů, které spouštějí aplikaci.

Pokud chcete používat kontejnery Windows Server (aktuálně ve verzi Preview v AKS), přečtěte si informace [o vytvoření clusteru AKS, který podporuje kontejnery Windows Server][windows-container-cli].

![Hlasovací aplikace nasazená ve službě Azure Kubernetes Service](./media/container-service-kubernetes-walkthrough/voting-app-deployed-in-azure-kubernetes-service.png)

Tento rychlý start předpokládá základní znalosti konceptů Kubernetes. Další informace najdete v tématu [Kubernetes základní koncepty pro službu Azure Kubernetes Service (AKS)][kubernetes-concepts].

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat příkaz cli místně, tento rychlý start vyžaduje, abyste spustili Azure CLI verze 2.0.64 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli-install].

> [!NOTE]
> Pokud spuštění příkazů v tomto rychlém startu místně (namísto Azure Cloud Shell), ujistěte se, že spustíte příkazy jako správce.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků Azure je logická skupina, ve které se nasazují a spravují prostředky Azure. Při vytváření skupiny prostředků se zobrazí výzva k zadání umístění. Toto umístění je místo, kde se ukládají metadata skupiny prostředků, je také místo, kde vaše prostředky spustit v Azure, pokud nezadáte jinou oblast během vytváření prostředků. Vytvořte skupinu prostředků pomocí příkazu [vytvořit skupinu az.][az-group-create]

Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Následující ukázkový výstup ukazuje skupinu prostředků, která byla úspěšně vytvořena:

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

## <a name="create-aks-cluster"></a>Vytvoření clusteru AKS

Pomocí příkazu [az aks create][az-aks-create] vytvořte cluster AKS. Následující příklad vytvoří cluster *myAKSCluster* s jedním uzlem. Kromě toho se dá pomocí parametru *--enable-addons monitoring* povolit Azure Monitor pro kontejnery.  Dokončení tohoto počtu bude trvat několik minut.

> [!NOTE]
> Při vytváření clusteru AKS je automaticky vytvořena druhá skupina prostředků pro uložení prostředků AKS. Další informace naleznete [v tématu Proč jsou dvě skupiny prostředků vytvořeny pomocí AKS?](https://docs.microsoft.com/azure/aks/faq#why-are-two-resource-groups-created-with-aks)

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster --node-count 1 --enable-addons monitoring --generate-ssh-keys
```

Po několika minutách příkaz dokončí a vrátí informace o clusteru ve formátu JSON.

## <a name="connect-to-the-cluster"></a>Připojení ke clusteru

Chcete-li spravovat cluster Kubernetes, použijte [kubectl][kubectl], klientpříkazového řádku Kubernetes. Pokud používáte Azure `kubectl` Cloud Shell, je už nainstalovaný. Chcete-li nainstalovat `kubectl` místně, použijte příkaz [az aks install-cli:][az-aks-install-cli]

```azurecli
az aks install-cli
```

Pomocí příkazu [az aks get-credentials][az-aks-get-credentials] nakonfigurujte klienta `kubectl` pro připojení k vašemu clusteru Kubernetes. Tento příkaz stáhne pověření a nakonfiguruje rozhraní příkazového příkazu Kubernetes tak, aby je používalo.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Pokud chcete ověřit připojení ke clusteru, použijte příkaz [kubectl get][kubectl-get], který vrátí seznam uzlů clusteru.

```azurecli-interactive
kubectl get nodes
```

Následující příklad výstupu ukazuje jeden uzel vytvořený v předchozích krocích. Ujistěte se, že stav uzlu je *připraven*:

```output
NAME                       STATUS   ROLES   AGE     VERSION
aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.12.8
```

## <a name="run-the-application"></a>Spuštění aplikace

Soubor manifestu Kubernetes definuje požadovaný stav clusteru, například jaké image kontejneru mají být spuštěny. V tomto rychlém startu manifest slouží k vytvoření všech objektů potřebných ke spuštění aplikace Azure Vote. Tento manifest zahrnuje dvě [nasazení Kubernetes][kubernetes-deployment] – jedno pro ukázkové aplikace Azure Vote Python a druhé pro instanci Redis. Jsou také vytvořeny dvě [služby Kubernetes][kubernetes-service] – interní služba pro instanci Redis a externí služba pro přístup k aplikaci Azure Vote z internetu.

> [!TIP]
> V tomto rychlém startu ručně vytvoříte manifest aplikace a nasadíte ho do clusteru AKS. V reálnějších situacích můžete k rychlé iteraci a ladění kódu přímo v clusteru AKS použít [Azure Dev Spaces][azure-dev-spaces]. Dev Spaces můžete používat na různých platformách operačních systémů a v různých vývojových prostředích a spolupracovat s ostatními členy vašeho týmu.

Vytvořte soubor `azure-vote.yaml` s názvem a zkopírujte v následující definici YAML. Pokud používáte Azure Cloud Shell, tento `vi` soubor `nano` lze vytvořit pomocí nebo jako by pracovat na virtuální nebo fyzický systém:

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

```azurecli-interactive
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

![Hlasovací aplikace nasazená ve službě Azure Kubernetes Service](./media/container-service-kubernetes-walkthrough/voting-app-deployed-in-azure-kubernetes-service.png)

Když byl vytvořen cluster AKS, [Azure Monitor pro kontejnery](../azure-monitor/insights/container-insights-overview.md) bylo povoleno zachytit metriky stavu pro uzly clusteru a pody. Tyto metriky stavu jsou k dispozici na webu Azure Portal.

## <a name="delete-the-cluster"></a>Odstranění clusteru

Chcete-li se vyhnout poplatkům Azure, měli byste vyčistit nepotřebné prostředky.  Pokud už cluster nepotřebujete, použijte k odebrání skupiny prostředků, služby kontejneru a všech souvisejících prostředků příkaz [az group delete][az-group-delete].

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> Při odstranění clusteru se neodebere instanční objekt služby Azure Active Directory používaný clusterem AKS. Postup odebrání instančního objektu najdete v tématu věnovaném [aspektům instančního objektu AKS a jeho odstranění][sp-delete].

## <a name="get-the-code"></a>Získání kódu

V tomto rychlém startu byly k vytvoření nasazení Kubernetes použity předem vytvořené image kontejneru. Související kód aplikace, soubor Dockerfile a soubor manifestu Kubernetes jsou k dispozici na GitHubu.

[https://github.com/Azure-Samples/azure-voting-app-redis][azure-vote-app]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste nasadili cluster Kubernetes a do něj jste nasadili vícekontejnerovou aplikaci. Můžete také [přistupovat k webovému řídicímu panelu Kubernetes][kubernetes-dashboard] pro váš cluster AKS.

Další informace o službě AKS a podrobné vysvětlení kompletního příkladu od kódu až po nasazení najdete v kurzu clusteru Kubernetes.

> [!div class="nextstepaction"]
> [Kurz AKS][aks-tutorial]

<!-- LINKS - external -->
[azure-vote-app]: https://github.com/Azure-Samples/azure-voting-app-redis.git
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[azure-dev-spaces]: https://docs.microsoft.com/azure/dev-spaces/

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[aks-monitor]: https://aka.ms/coingfonboarding
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-browse]: /cli/azure/aks?view=azure-cli-latest#az-aks-browse
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[azure-cli-install]: /cli/azure/install-azure-cli
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[azure-portal]: https://portal.azure.com
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[kubernetes-dashboard]: kubernetes-dashboard.md
[windows-container-cli]: windows-container-cli.md
