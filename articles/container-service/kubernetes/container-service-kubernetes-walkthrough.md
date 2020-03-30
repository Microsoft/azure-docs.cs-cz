---
title: (ZASTARALÉ) Úvodní příručka – cluster Azure Kubernetes pro Linux
description: Rychle se naučíte, jak pomocí rozhraní příkazového řádku Azure vytvářet cluster Kubernetes pro linuxové kontejnery ve službě Azure Container Service.
author: iainfoulds
ms.service: container-service
ms.topic: quickstart
ms.date: 02/26/2018
ms.author: iainfou
ms.custom: H1Hack27Feb2017, mvc, devcenter
ms.openlocfilehash: cfd0f8a9a3180b14b4da9dc61e252054fe06628c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "78274172"
---
# <a name="deprecated-deploy-kubernetes-cluster-for-linux-containers"></a>(ZASTARALÉ) Nasazení clusteru Kubernetes pro linuxové kontejnery

> [!TIP]
> Aktualizovanou verzi tohoto rychlého startu, který používá službu Azure Kubernetes Service, najdete v [tématu Úvodní příručka: Nasazení clusteru služby Azure Kubernetes Service (AKS).](../../aks/kubernetes-walkthrough.md)

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

V tomto rychlém startu clusteru Kubernetes se nasadí pomocí azure CLI. Následně se na tomto clusteru nasadí a spustí vícekontejnerová aplikace skládající se z webu front-end a instance Redis. Po dokončení bude aplikace přístupná přes internet. 

Ukázková aplikace použitá v tomto dokumentu je napsaná v Pythonu. Pomocí zde podrobně popsaných konceptů a postupů je možné do clusteru Kubernetes nasadit jakoukoli image kontejneru. Kód, soubor Dockerfile a předem vytvořené soubory manifestu Kubernetes související s tímto projektem jsou k dispozici na [GitHubu](https://github.com/Azure-Samples/azure-voting-app-redis.git).

![Obrázek přechodu na aplikaci Azure Vote](media/container-service-kubernetes-walkthrough/azure-vote.png)

V tomto rychlém startu se předpokládá základní znalost konceptů Kubernetes. Podrobné informace o Kubernetes najdete v [dokumentaci ke Kubernetes]( https://kubernetes.io/docs/home/).

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít rozhraní příkazového řádku Azure ve verzi 2.0.4 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI]( /cli/azure/install-azure-cli). 

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az-group-create). Skupina prostředků Azure je logická skupina, ve které se nasazují a spravují prostředky Azure. 

Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *westeurope*.

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```

Výstup:

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup",
  "location": "westeurope",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-kubernetes-cluster"></a>Vytvoření clusteru Kubernetes

Vytvořte cluster Kubernetes ve službě Azure Container Service pomocí příkazu [az acs create](/cli/azure/acs#az-acs-create). Následující příklad vytvoří cluster s názvem *myK8sCluster* s jedním hlavním linuxovým uzlem a třemi agentskými linuxovými uzly.

```azurecli-interactive
az acs create --orchestrator-type kubernetes --resource-group myResourceGroup --name myK8sCluster --generate-ssh-keys
```

V některých případech, například s omezenou zkušební verzí, má předplatné Azure omezený přístup k prostředkům Azure. Pokud se nasazení nezdaří kvůli omezenému počtu dostupných jader, snižte výchozí počet agentů přidáním možnosti `--agent-count 1` do příkazu [az acs create](/cli/azure/acs#az-acs-create). 

Po několika minutách se příkaz dokončí a vrátí informace o clusteru ve formátu JSON. 

## <a name="connect-to-the-cluster"></a>Připojení ke clusteru

Ke správě clusteru Kubernetes použijte klienta příkazového řádku Kubernetes [kubectl](https://kubernetes.io/docs/user-guide/kubectl/). 

Pokud používáte Azure Cloud Shell, kubectl je už nainstalován. Pokud ho chcete nainstalovat místně, můžete použít příkaz [az acs kubernetes install-cli](/cli/azure/acs/kubernetes).

Abyste nakonfigurovali kubectl pro připojení ke svému clusteru Kubernetes, spusťte příkaz [az acs kubernetes get-credentials](/cli/azure/acs/kubernetes). Tímto krokem se stáhnou přihlašovací údaje a nakonfiguruje rozhraní příkazového řádku Kubernetes pro jejich použití.

```azurecli-interactive
az acs kubernetes get-credentials --resource-group=myResourceGroup --name=myK8sCluster
```

Pokud chcete ověřit připojení ke clusteru, použijte příkaz [kubectl get](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get), který vrátí seznam uzlů clusteru.

```console
kubectl get nodes
```

Výstup:

```output
NAME                    STATUS                     AGE       VERSION
k8s-agent-14ad53a1-0    Ready                      10m       v1.6.6
k8s-agent-14ad53a1-1    Ready                      10m       v1.6.6
k8s-agent-14ad53a1-2    Ready                      10m       v1.6.6
k8s-master-14ad53a1-0   Ready,SchedulingDisabled   10m       v1.6.6
```

## <a name="run-the-application"></a>Spuštění aplikace

Soubor manifestu Kubernetes definuje požadovaný stav clusteru, včetně toho, jaké image kontejnerů mají být spuštěné. V tomto příkladu manifest slouží k vytvoření všech objektů potřebných ke spuštění aplikace Azure Vote. 

Vytvořte soubor `azure-vote.yml` a zkopírujte do něj následující kód YAML. Pokud pracujete ve službě Azure Cloud Shell, můžete tento soubor vytvořit pomocí editoru vi nebo Nano stejně, jako kdybyste pracovali na virtuálním nebo fyzickém systému.

```yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: azure-vote-back
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: azure-vote-back
    spec:
      containers:
      - name: azure-vote-back
        image: redis
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
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: azure-vote-front
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: azure-vote-front
    spec:
      containers:
      - name: azure-vote-front
        image: microsoft/azure-vote-front:v1
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

Pomocí příkazu [kubectl create](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create) spusťte aplikaci.

```console
kubectl create -f azure-vote.yml
```

Výstup:

```output
deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-the-application"></a>Testování aplikace

Při spuštění aplikace se vytvoří [služba Kubernetes](https://kubernetes.io/docs/concepts/services-networking/service/), která zveřejní front-end aplikace na internetu. Dokončení tohoto procesu může trvat několik minut. 

Pomocí příkazu [kubectl get service](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) s argumentem `--watch` můžete sledovat průběh.

```console
kubectl get service azure-vote-front --watch
```

Zpočátku **EXTERNAL-IP** pro *azure-hlasování front* služby se zobrazí jako *čekající*. Jakmile se stav adresy EXTERNAL-IP změní ze stavu *Probíhá* na hodnotu *IP adresa*, pomocí klávesové zkratky `CTRL-C` zastavte sledovací proces kubectl. 
  
```output
azure-vote-front   10.0.34.242   <pending>     80:30676/TCP   7s
azure-vote-front   10.0.34.242   52.179.23.131   80:30676/TCP   2m
```

Teď můžete přejít na externí IP adresu a zobrazit aplikaci Azure Vote.

![Obrázek přechodu na aplikaci Azure Vote](media/container-service-kubernetes-walkthrough/azure-vote.png)  

## <a name="delete-cluster"></a>Odstranění clusteru
Pokud už cluster nepotřebujete, můžete k odebrání skupiny prostředků, služby kontejneru a všech souvisejících prostředků použít příkaz [az group delete](/cli/azure/group#az-group-delete).

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="get-the-code"></a>Získání kódu

V tomto rychlém startu se k vytvoření nasazení Kubernetes použily předem vytvořené image kontejnerů. Související kód aplikace, soubor Dockerfile a soubor manifestu Kubernetes jsou k dispozici na GitHubu.

[https://github.com/Azure-Samples/azure-voting-app-redis](https://github.com/Azure-Samples/azure-voting-app-redis.git)

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste nasadili cluster Kubernetes a do něj jste nasadili vícekontejnerovou aplikaci. 

Další informace o službě Azure Container Service a podrobné vysvětlení kompletního příkladu od kódu až po nasazení najdete v kurzu clusteru Kubernetes.

> [!div class="nextstepaction"]
> [Správa clusteru ACS Kubernetes](./container-service-tutorial-kubernetes-prepare-app.md)
