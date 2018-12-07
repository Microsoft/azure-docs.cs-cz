---
title: Vytvořit virtuální uzly pomocí portálu ve službě Azure Kubernetes služby (AKS)
description: Zjistěte, jak pomocí webu Azure portal k vytvoření clusteru služby Azure Kubernetes (AKS), který používá ke spuštění podů virtuální uzly.
services: container-service
author: iainfoulds
ms.service: container-service
ms.date: 12/03/2018
ms.author: iainfou
ms.openlocfilehash: 3b99afe82f77b6bd89b5afa458179abee4c98e4f
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52999133"
---
# <a name="create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes-in-the-azure-portal"></a>Vytvoření a konfigurace clusteru služby Azure Kubernetes služby (AKS) používat virtuální uzly na webu Azure Portal

Rychlé nasazení úloh v clusteru služby Azure Kubernetes Service (AKS), můžete použít virtuální uzly. S virtuální uzly mají rychlé zřízení podů a platíte jenom za sekundu pro jejich spuštění. V případě změny velikosti v nemusíte čekat automatického škálování clusteru Kubernetes nasadit virtuální počítač výpočetních uzlů pro spouštění budou další pody. Tento článek ukazuje, jak vytvořit a konfigurovat prostředky virtuální sítě a clusteru AKS pomocí virtuální uzly povolena.

> [!IMPORTANT]
> Virtuální uzly pro AKS jsou nyní v **ve verzi preview**. Verze Preview vám zpřístupňujeme pod podmínkou, že budete souhlasit s [dodatečnými podmínkami použití](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Některé aspekty této funkce se můžou před zveřejněním změnit.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="create-an-aks-cluster"></a>Vytvoření clusteru AKS

V levém horním rohu webu Azure Portal vyberte **Vytvořit prostředek** > **Kubernetes Service**.

Na **Základy** stránce, nakonfigurujte následující možnosti:

- *PODROBNOSTI O PROJEKTU:* Vyberte předplatné Azure a pak vyberte nebo vytvořte skupinu prostředků Azure, například *myResourceGroup*. Zadejte **Název clusteru Kubernetes**, například *myAKSCluster*.
- *PODROBNOSTI O CLUSTERU:* Vyberte oblast, verzi Kubernetes a předponu názvu DNS pro cluster AKS.
- *ŠKÁLOVÁNÍ:* Vyberte velikost virtuálního počítače pro uzly AKS. Velikost virtuálního počítače **nejde** změnit po nasazení clusteru AKS.
    - Vyberte počet uzlů, které se mají do clusteru nasadit. Pro účely tohoto článku nastavte **počet uzlů** k *1*. Počet uzlů **jde** upravit po nasazení clusteru.
    - V části **virtuální uzly**vyberte *povoleno*.

![Vytvoření clusteru AKS a povolení virtuální uzly](media/virtual-nodes-portal/enable-virtual-nodes.png)

Ve výchozím nastavení je vytvoření instančního objektu služby Azure Active Directory. Tento instanční objekt se používá pro komunikaci s clustery a integrace s dalšími službami Azure.

Cluster je také nakonfigurovaný pro rozšířeného sítě. Virtuální uzly jsou nakonfigurovány pro použití vlastní podsíti virtuální sítě Azure. Tato podsíť obsahuje delegovaná oprávnění k připojení prostředků Azure mezi clusterem AKS. Pokud ještě nemáte delegované podsíť, na webu Azure portal vytvoří a nakonfiguruje virtuální sítí Azure a podsítě pro použití s virtuálními uzly.

Vyberte **Zkontrolovat a vytvořit**. Po dokončení ověřování vyberte **vytvořit**.

Vytvoření clusteru AKS a jeho příprava k použití trvá několik minut.

## <a name="connect-to-the-cluster"></a>Připojení ke clusteru

Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. Ke správě clusteru Kubernetes použijte klienta příkazového řádku Kubernetes [kubectl][kubectl]. Klient `kubectl` je předinstalovaný ve službě Azure Cloud Shell.

Chcete-li spustit Cloud Shell, vyberte **vyzkoušet** v pravém horním rohu bloku kódu. Cloud Shell můžete spustit také na samostatné kartě prohlížeče na adrese [https://shell.azure.com/bash](https://shell.azure.com/bash). Zkopírujte bloky kódu výběrem možnosti **Kopírovat**, vložte je do služby Cloud Shell a potom je spusťte stisknutím klávesy Enter.

Pomocí příkazu [az aks get-credentials][az-aks-get-credentials] nakonfigurujte klienta `kubectl` pro připojení k vašemu clusteru Kubernetes. Následující příklad získá přihlašovací údaje pro název clusteru *myAKSCluster* ve skupině prostředků *myResourceGroup*:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Pokud chcete ověřit připojení ke clusteru, použijte příkaz [kubectl get][kubectl-get], který vrátí seznam uzlů clusteru.

```azurecli-interactive
kubectl get nodes
```

Následující příklad výstupu ukazuje jediného uzlu virtuálního počítače vytvořena a pak virtuální uzel pro Linux, *virtuální node-aci-linux*:

```
$ kubectl get nodes

NAME                           STATUS    ROLES     AGE       VERSION
virtual-node-aci-linux         Ready     agent     28m       v1.11.2
aks-agentpool-14693408-0       Ready     agent     32m       v1.11.2
```

## <a name="deploy-a-sample-app"></a>Nasaďte ukázkovou aplikaci

Ve službě Azure Cloud Shell, vytvořte soubor s názvem `virtual-node.yaml` a zkopírujte do následující kód YAML. Naplánování kontejneru na uzlu, [nodeSelector] [ node-selector] a [toleration] [ toleration] jsou definovány. Tato nastavení umožňují pod naplánovat na virtuální uzel a potvrďte, že tato funkce se úspěšně povolilo.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aci-helloworld
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aci-helloworld
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
        kubernetes.io/role: agent
        beta.kubernetes.io/os: linux
        type: virtual-kubelet
      tolerations:
      - key: virtual-kubelet.io/provider
        operator: Exists
      - key: azure.com/aci
        effect: NoSchedule
```

Spusťte aplikaci [použití kubectl] [ kubectl-apply] příkazu.

```azurecli-interactive
kubectl apply -f virtual-node.yaml
```

Použití [kubectl get pods] [ kubectl-get] příkazů `-o wide` argument do výstupního seznam podů a plánované uzlu. Všimněte si, že `virtual-node-helloworld` pod byla naplánována na `virtual-node-linux` uzlu.

```
$ kubectl get pods -o wide

NAME                                     READY     STATUS    RESTARTS   AGE       IP           NODE
virtual-node-helloworld-9b55975f-bnmfl   1/1       Running   0          4m        10.241.0.4   virtual-node-aci-linux
```

Pod přiřazena vnitřní IP adresu z podsítě virtuální sítě Azure delegované pro použití s virtuálními uzly.

## <a name="test-the-virtual-node-pod"></a>Testovat virtuální uzel pod

K otestování spuštěný na virtuální uzel pod, přejděte na ukázkovou aplikaci pomocí webového klienta. Jak chcete pod přiřazena vnitřní IP adresu, můžete rychle otestovat toto připojení z jiného podu na clusteru AKS. Vytvoření testů pod a připojit se k němu Terminálové relaci:

```azurecli-interactive
kubectl run -it --rm virtual-node-test --image=debian
```

Nainstalujte `curl` v podu pomocí `apt-get`:

```azurecli-interactive
apt-get update && apt-get install -y curl
```

Nyní přístup k adrese pod pomocí `curl`, jako například *http://10.241.0.4*. Zadejte vlastní interní IP adresa je znázorněno v předchozí `kubectl get pods` příkaz:

```azurecli-interactive
curl -L http://10.241.0.4
```

Ukázkové aplikace se zobrazí, jak je znázorněno v následujícím výstupu zhuštěnému příkladu:

```
$ curl -L 10.241.0.4

<html>
<head>
  <title>Welcome to Azure Container Instances!</title>
</head>
[...]
```

Zavřete relaci Terminálové služby pro váš test pod s `exit`. Po ukončení relace je pod odstraněným.

## <a name="next-steps"></a>Další postup

V tomto článku pod byla naplánována na virtuální uzel a přiřazenou IP adresu privátní a interní. Místo toho byste třeba vytvořit nasazení služby a směrovat provoz do podu prostřednictvím nástroje pro vyrovnávání zatížení nebo kontroler příchozího přenosu dat. Další informace najdete v tématu [vytvoříte řadič základního příchozího přenosu dat ve službě AKS][aks-basic-ingress].

Virtuální uzly jsou jedna komponenta škálování řešení ve službě AKS. Další informace o škálování řešení najdete v následujících článcích:

- [Použití automatického škálování vodorovné podů Kubernetes][aks-hpa]
- [Použití automatického škálování clusteru Kubernetes][aks-cluster-autoscaler]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[node-selector]:https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[toleration]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md

<!-- LINKS - internal -->
[aks-network]: ./networking-overview.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[aks-hpa]: tutorial-kubernetes-scale.md
[aks-cluster-autoscaler]: autoscaler.md
[aks-basic-ingress]: ingress-basic.md
