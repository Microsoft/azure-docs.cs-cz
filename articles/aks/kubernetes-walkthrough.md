---
title: Rychlý start – vytvoření clusteru Azure Kubernetes Service (AKS)
description: Zjistěte, jak rychle vytvořit Kubernetes cluster, nasazení aplikace a sledování výkonu ve službě Azure Kubernetes Service (AKS) pomocí Azure CLI.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: quickstart
ms.date: 12/18/2018
ms.author: iainfou
ms.custom: H1Hack27Feb2017, mvc, devcenter
ms.openlocfilehash: 7c7881f08828572c01a5e0decb270c653416ef9b
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54024259"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-using-the-azure-cli"></a>Rychlý start: Nasazení clusteru služby Azure Kubernetes Service (AKS) pomocí rozhraní příkazového řádku Azure

Azure Kubernetes Service (AKS) je spravovaná služba, která vám umožní rychle nasadit a spravovat clustery Kubernetes. V tomto rychlém startu nasadíte cluster AKS pomocí Azure CLI. V clusteru je spustí vícekontejnerová aplikace, která obsahuje webový front-end a Redis instance. Zobrazí se postup sledování stavu clusteru a podů, na kterých běží vaše aplikace.

![Obrázek přechodu na aplikaci Azure Vote](media/container-service-kubernetes-walkthrough/azure-vote.png)

Tento rychlý start předpokládá základní znalosti konceptů Kubernetes. Další informace najdete v tématu [Kubernetes pro Azure Kubernetes Service (AKS) základní koncepty][kubernetes-concepts].

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, tento rychlý start vyžaduje použití Azure CLI verze 2.0.52 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli-install].

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků Azure je logická skupina, ve které se nasazují a spravují prostředky Azure. Při vytváření skupiny prostředků se zobrazí výzva k zadání umístění. V tomto umístění se spouští vaše prostředky v Azure. Abyste vytvořili skupinu prostředků pomocí [vytvořit skupiny az] [ az-group-create] příkazu.

Následující příklad vytvoří skupinu prostředků *myAKSCluster* v umístění *eastus* (USA – východ).

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Následující příklad výstupu ukazuje úspěšně vytvořili skupinu prostředků:

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

Pomocí příkazu [az aks create][az-aks-create] vytvořte cluster AKS. Následující příklad vytvoří cluster *myAKSCluster* s jedním uzlem. Kromě toho se dá pomocí parametru *--enable-addons monitoring* povolit Azure Monitor pro kontejnery.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --enable-addons monitoring \
    --generate-ssh-keys
```

Po několika minutách se příkaz dokončí a vrátí hodnotu ve formátu JSON informace o clusteru.

## <a name="connect-to-the-cluster"></a>Připojení ke clusteru

Ke správě clusteru Kubernetes použijete [kubectl][kubectl], klienta příkazového řádku Kubernetes. Pokud používáte Azure Cloud Shell, `kubectl` je již nainstalována. Chcete-li nainstalovat `kubectl` místně, použijte [az aks install-cli] [ az-aks-install-cli] příkaz:

```azurecli
az aks install-cli
```

Pomocí příkazu [az aks get-credentials][az-aks-get-credentials] nakonfigurujte klienta `kubectl` pro připojení k vašemu clusteru Kubernetes. Tento příkaz stáhne přihlašovací údaje a nakonfiguruje rozhraní příkazového řádku Kubernetes pro jejich použití.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Pokud chcete ověřit připojení ke clusteru, použijte příkaz [kubectl get][kubectl-get], který vrátí seznam uzlů clusteru.

```azurecli-interactive
kubectl get nodes
```

Následující příklad výstupu ukazuje jeden uzel vytvořený v předchozích krocích. Ujistěte se, zda je stav uzlu *připravené*:

```
NAME                       STATUS   ROLES   AGE     VERSION
aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.9.11
```

## <a name="run-the-application"></a>Spuštění aplikace

Soubor manifestu Kubernetes definuje požadovaný stav clusteru, například jaké kontejneru obrázků ke spuštění. V tomto rychlém startu manifest slouží k vytvoření všech objektů potřebných ke spuštění aplikace Azure Vote. Tento manifest zahrnuje dva [nasazení Kubernetes] [ kubernetes-deployment] – jednu pro ukázkové aplikace Azure Vote Python a druhé pro instanci Redis. Dvě [služby Kubernetes] [ kubernetes-service] také vytvářejí – interní služba pro instanci Redis a externí služby pro přístup k aplikaci Azure Vote z Internetu.

> [!TIP]
> V tomto rychlém startu ručně vytvoříte manifest aplikace a nasadíte ho do clusteru AKS. V reálnějších situacích můžete k rychlé iteraci a ladění kódu přímo v clusteru AKS použít [Azure Dev Spaces][azure-dev-spaces]. Dev Spaces můžete používat na různých platformách operačních systémů a v různých vývojových prostředích a spolupracovat s ostatními členy vašeho týmu.

Vytvořte soubor s názvem `azure-vote.yaml` a zkopírujte do následující definice YAML. Pokud používáte Azure Cloud Shell, můžete tento soubor vytvořit pomocí `vi` nebo `nano` stejně jako kdybyste pracovali na virtuálním nebo fyzickém systému:

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

![Obrázek přechodu na aplikaci Azure Vote](media/container-service-kubernetes-walkthrough/azure-vote.png)

## <a name="monitor-health-and-logs"></a>Monitorování stavu a protokolů

Při vytvoření clusteru AKS, monitorování Azure pro kontejnery bylo povoleno zachycení stavu metriky pro uzly clusteru a podů. Tyto metriky stavu jsou k dispozici na webu Azure Portal.

Pokud chcete zobrazit aktuální stav, dobu provozu a využití prostředků pro pody Azure Vote, proveďte následující kroky:

1. Otevřete webový prohlížeč a přejděte na web Azure Portal na adrese [https://portal.azure.com][azure-portal].
1. Vyberte vaši skupinu prostředků, například *myResourceGroup*, a pak váš cluster AKS, například *myAKSCluster*.
1. V části **monitorování** na levé straně zvolte **Insights**
1. V horní části zvolte **+ Přidat filtr**
1. Jako vlastnost vyberte *Obor názvů* a potom zvolte *\<Všechny kromě kube-system\>*.
1. Vyberte zobrazení **Kontejnery**.

Zobrazí se kontejnery *azure-vote-back* a *azure-vote-front*, jak ukazuje následující příklad:

![Zobrazení stavu spuštěných kontejnerů v AKS](media/kubernetes-walkthrough/monitor-containers.png)

Pokud chcete zobrazit protokoly pro pod `azure-vote-front`, vyberte odkaz **Zobrazit protokoly kontejnerů** na pravé straně seznamu kontejnerů. Tyto protokoly obsahují streamy výstupů *stdout* a *stderr* z kontejneru.

![Zobrazení protokolů kontejneru v AKS](media/kubernetes-walkthrough/monitor-container-logs.png)

## <a name="delete-cluster"></a>Odstranění clusteru

Pokud už cluster nepotřebujete, použijte k odebrání skupiny prostředků, služby kontejneru a všech souvisejících prostředků příkaz [az group delete][az-group-delete].

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
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
