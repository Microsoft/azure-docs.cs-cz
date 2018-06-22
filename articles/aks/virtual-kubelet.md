---
title: Spustit virtuální kubelet v clusteru služby Azure Kubernetes služby (AKS)
description: Použijte virtuální kubelet ke spuštění Kubernetes kontejnery v instancích kontejner Azure.
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/12/2018
ms.author: nepeters
ms.openlocfilehash: 0d046970b40f5253b07005ab578035c7c179e7df
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/21/2018
ms.locfileid: "36304441"
---
# <a name="virtual-kubelet-with-aks"></a>Virtuální Kubelet s AKS

Instance Azure kontejneru (ACI) zadejte hostované prostředí pro spuštění kontejnerů v Azure. Pokud používáte ACI, není nutné ke správě základní výpočetní infrastrukturu, Azure zpracovává tento správy pro vás. Když spustíte kontejnery v ACI, budou se účtovat druhou pro každý kontejner spuštěné.

Při použití zprostředkovatele virtuální Kubelet pro instance kontejner Azure, Linux a Windows kontejnery můžete naplánovat na instanci kontejneru, pokud je standardní Kubernetes uzlu. Tato konfigurace umožňuje využít výhod možností Kubernetes a výhody hodnotu a náklady na správu instancí kontejnerů.

> [!NOTE]
> Virtuální Kubelet je experimentální opensourcový projekt a jako takový slouží. Můžete přispívat, najdete v souboru problémů a číst informace o virtuální kubelet [projektu virtuální Githubu Kubelet][vk-github].

Tento dokument podrobnosti konfigurace virtuální Kubelet pro instance služby kontejneru na AKS.

## <a name="prerequisite"></a>Požadavek

Tento dokument předpokládá, že máte AKS cluster. Pokud potřebujete clusteru služby AKS, přečtěte si [rychlý start Azure Kubernetes služby (AKS)][aks-quick-start].

Musíte taky Azure CLI verze **2.0.33** nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli).

[Helm](https://docs.helm.sh/using_helm/#installing-helm) je taky požadovat, aby bylo možné nainstalovat virtuální Kubelet.

## <a name="installation"></a>Instalace

Použití [az aks install konektor] [ aks-install-connector] příkaz pro instalaci virtuální Kubelet. Následující příklad nasadí konektor Linuxu i Windows.

```azurecli-interactive
az aks install-connector --resource-group myAKSCluster --name myAKSCluster --connector-name virtual-kubelet --os-type Both
```

Jsou k dispozici pro tyto argumenty `aks install-connector` příkaz.

| Argument: | Popis | Požaduje se |
|---|---|:---:|
| `--connector-name` | Název konektoru ACI.| Ano |
| `--name` `-n` | Název spravovaného clusteru. | Ano |
| `--resource-group` `-g` | Název skupiny prostředků. | Ano |
| `--os-type` | Kontejner instancí typ operačního systému. Povolené hodnoty: obě, Linux, Windows. Výchozí: Linux. | Ne |
| `--aci-resource-group` | Skupinu prostředků, ve které chcete vytvořit skupiny ACI kontejnerů. | Ne |
| `--location` `-l` | Umístění pro vytvoření skupiny ACI kontejnerů. | Ne |
| `--service-principal` | Objekt zabezpečení služby použít k ověření na rozhraní API služby Azure. | Ne |
| `--client-secret` | Tajný klíč přidružený k hlavní službě. | Ne |
| `--chart-url` | Adresa URL Helm graf, který nainstaluje konektor ACI. | Ne |
| `--image-tag` | Značka obrázku bitové kopie virtuálního kubelet kontejneru. | Ne |

## <a name="validate-virtual-kubelet"></a>Ověření virtuální Kubelet

K ověření, že byla nainstalována virtuální Kubelet, vrácení seznamu Kubernetes uzlů pomocí [kubectl získat uzly] [ kubectl-get] příkaz.

```console
$ kubectl get nodes

NAME                                    STATUS    ROLES     AGE       VERSION
aks-nodepool1-23443254-0                Ready     agent     16d       v1.9.6
aks-nodepool1-23443254-1                Ready     agent     16d       v1.9.6
aks-nodepool1-23443254-2                Ready     agent     16d       v1.9.6
virtual-kubelet-virtual-kubelet-linux   Ready     agent     4m        v1.8.3
virtual-kubelet-virtual-kubelet-win     Ready     agent     4m        v1.8.3
```

## <a name="run-linux-container"></a>Spusťte kontejner Linux

Vytvořte soubor s názvem `virtual-kubelet-linux.yaml` a zkopírujte následující YAML. Nahraďte `kubernetes.io/hostname` hodnotu s názvem virtuální Kubelet Linux uzlu. Všimněte si, [nodeSelector] [ node-selector] a [toleration] [ toleration] se používají k plánování kontejneru na uzlu.

```yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: aci-helloworld
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: aci-helloworld
    spec:
      containers:
      - name: aci-helloworld
        image: microsoft/aci-helloworld
        ports:
        - containerPort: 80
      nodeSelector:
        kubernetes.io/hostname: virtual-kubelet-virtual-kubelet-linux
      tolerations:
      - key: azure.com/aci
        effect: NoSchedule
```

Spusťte aplikaci klávesou [kubectl vytvořit] [ kubectl-create] příkaz.

```azurecli-interactive
kubectl create -f virtual-kubelet-linux.yaml
```

Použití [kubectl získat pracovními stanicemi soustředěnými kolem] [ kubectl-get] s `-o wide` argument k vypsání seznamu pracovními stanicemi soustředěnými kolem s uzlu naplánované. Všimněte si, že `aci-helloworld` pod bylo naplánováno na `virtual-kubelet-virtual-kubelet-linux` uzlu.

```console
$ kubectl get pods -o wide

NAME                                READY     STATUS    RESTARTS   AGE       IP             NODE
aci-helloworld-2559879000-8vmjw     1/1       Running   0          39s       52.179.3.180   virtual-kubelet-virtual-kubelet-linux
```

## <a name="run-windows-container"></a>Spusťte kontejner Windows

Vytvořte soubor s názvem `virtual-kubelet-windows.yaml` a zkopírujte následující YAML. Nahraďte `kubernetes.io/hostname` hodnotu s názvem Windows virtuální Kubelet uzlu. Všimněte si, [nodeSelector] [ node-selector] a [toleration] [ toleration] se používají k plánování kontejneru na uzlu.

```yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: nanoserver-iis
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: nanoserver-iis
    spec:
      containers:
      - name: nanoserver-iis
        image: nanoserver/iis
        ports:
        - containerPort: 80
      nodeSelector:
        kubernetes.io/hostname: virtual-kubelet-virtual-kubelet-win
      tolerations:
      - key: azure.com/aci
        effect: NoSchedule
```

Spusťte aplikaci klávesou [kubectl vytvořit] [ kubectl-create] příkaz.

```azurecli-interactive
kubectl create -f virtual-kubelet-windows.yaml
```

Použití [kubectl získat pracovními stanicemi soustředěnými kolem] [ kubectl-get] s `-o wide` argument k vypsání seznamu pracovními stanicemi soustředěnými kolem s uzlu naplánované. Všimněte si, že `nanoserver-iis` pod bylo naplánováno na `virtual-kubelet-virtual-kubelet-win` uzlu.

```console
$ kubectl get pods -o wide

NAME                                READY     STATUS    RESTARTS   AGE       IP             NODE
nanoserver-iis-868bc8d489-tq4st     1/1       Running   8         21m       138.91.121.91   virtual-kubelet-virtual-kubelet-win
```

## <a name="remove-virtual-kubelet"></a>Odebrat virtuální Kubelet

Použití [az aks remove konektor] [ aks-remove-connector] příkaz k odebrání virtuálního Kubelet. Nahraďte název konektoru, AKS clusteru a skupinu prostředků clusteru AKS hodnot argumentů.

```azurecli-interactive
az aks remove-connector --resource-group myAKSCluster --name myAKSCluster --connector-name virtual-kubelet
```

## <a name="next-steps"></a>Další postup

Další informace o virtuální Kubelet na [virtuální Kubelet Githubu projektu][vk-github].

<!-- LINKS - internal -->
[aks-quick-start]: ./kubernetes-walkthrough.md
[aks-remove-connector]: /cli/azure/aks#az-aks-remove-connector
[az-container-list]: /cli/azure/aks#az_aks_list
[aks-install-connector]: /cli/azure/aks#az-aks-install-connector

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/user-guide/kubectl/v1.6/#create
[kubectl-get]: https://kubernetes.io/docs/user-guide/kubectl/v1.8/#get
[node-selector]:https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[toleration]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[vk-github]: https://github.com/virtual-kubelet/virtual-kubelet
