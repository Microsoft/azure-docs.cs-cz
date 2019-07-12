---
title: Vytvořit virtuální uzly pomocí portálu ve službě Azure Kubernetes služby (AKS)
description: Zjistěte, jak pomocí webu Azure portal k vytvoření clusteru služby Azure Kubernetes (AKS), který používá ke spuštění podů virtuální uzly.
services: container-service
author: mlearned
ms.topic: conceptual
ms.service: container-service
ms.date: 05/06/2019
ms.author: mlearned
ms.openlocfilehash: 8752d888e24e7135d488be6d1b377070a30fe4eb
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2019
ms.locfileid: "67613829"
---
# <a name="create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes-in-the-azure-portal"></a>Vytvoření a konfigurace clusteru služby Azure Kubernetes služby (AKS) používat virtuální uzly na webu Azure Portal

Rychlé nasazení úloh v clusteru služby Azure Kubernetes Service (AKS), můžete použít virtuální uzly. S virtuální uzly mají rychlé zřízení podů a platíte jenom za sekundu pro jejich spuštění. V případě změny velikosti v nemusíte čekat automatického škálování clusteru Kubernetes nasadit virtuální počítač výpočetních uzlů pro spouštění budou další pody. Virtuální uzly jsou podporovaná jenom s uzly a podů Linux.

Tento článek ukazuje, jak vytvořit a konfigurovat prostředky virtuální sítě a clusteru AKS pomocí virtuální uzly povolena.

## <a name="before-you-begin"></a>Před zahájením

Virtuální uzly povolit síťovou komunikaci mezi pody spuštěné v ACI a AKS clusteru. Pro tuto komunikaci, se vytvoří podsíť virtuální sítě a jsou přiřazeny delegovaná oprávnění. Virtuální uzly fungovat jenom s clustery AKS vytvořeného *pokročilé* sítě. Ve výchozím nastavení, AKS clustery jsou vytvořeny pomocí *základní* sítě. Tento článek ukazuje, jak vytvořit virtuální síť a podsítě a pak Nasaďte cluster AKS, který používá rozšířeného sítě.

Pokud jste dříve nepoužili ACI, zaregistrujte poskytovatele služeb s vaším předplatným. Můžete zkontrolovat stav registrace poskytovatele ACI pomocí [az provider list][az-provider-list] příkaz, jak je znázorněno v následujícím příkladu:

```azurecli-interactive
az provider list --query "[?contains(namespace,'Microsoft.ContainerInstance')]" -o table
```

*Microsoft.ContainerInstance* poskytovatele hlásit jako *registrované*, jak je znázorněno v následujícím příkladu výstupu:

```
Namespace                    RegistrationState
---------------------------  -------------------
Microsoft.ContainerInstance  Registered
```

Pokud poskytovatel zobrazí jako *NotRegistered*, zaregistrujte poskytovatele pomocí [az registrovat poskytovatele] [az provider register], jak je znázorněno v následujícím příkladu:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerInstance
```

## <a name="regional-availability"></a>Regionální dostupnost

Tyto oblasti jsou podporovány pro nasazení virtuálního uzlu:

* Austrálie – východ (australiaeast)
* Střed USA (centralus)
* USA – východ (eastus)
* East US 2 (eastus2)
* Japonsko – východ (japaneast)
* Severní Evropa (northeurope)
* Jihovýchodní Asie (southeastasia)
* Střed USA – západ (westcentralus)
* Západní Evropa (westeurope)
* USA – západ (westus)
* Západní USA 2 (westus2)

## <a name="known-limitations"></a>Známá omezení
Virtuální funkce uzlů je silně závisí na sadě funkcí v ACI. Následující scénáře nejsou ještě podporované s virtuální uzly

* Pomocí instančního objektu pro Image ACR o přijetí změn. [Alternativní řešení](https://github.com/virtual-kubelet/virtual-kubelet/blob/master/providers/azure/README.md#Private-registry) je použití [tajné klíče Kubernetes](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/#create-a-secret-by-providing-credentials-on-the-command-line)
* [Omezení virtuální sítě](../container-instances/container-instances-vnet.md) včetně VNet peering, Kubernetes síťové zásady a odchozí provoz do Internetu s použitím skupin zabezpečení sítě.
* Init kontejnery
* [Aliasy hostitelů](https://kubernetes.io/docs/concepts/services-networking/add-entries-to-pod-etc-hosts-with-host-aliases/)
* [Argumenty](../container-instances/container-instances-exec.md#restrictions) pro spuštění v ACI
* [Daemonsets](concepts-clusters-workloads.md#statefulsets-and-daemonsets) nenasadí podů na virtuální uzel
* [Uzly Windows serveru (aktuálně ve verzi preview ve službě AKS)](windows-container-cli.md) virtuálních uzlů se nepodporují. Virtuální uzly můžete plánovat kontejnery Windows serveru bez nutnosti uzly Windows serveru v clusteru AKS.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="create-an-aks-cluster"></a>Vytvoření clusteru AKS

V levém horním rohu webu Azure Portal vyberte **Vytvořit prostředek** > **Kubernetes Service**.

Na **Základy** stránce, nakonfigurujte následující možnosti:

- *PODROBNOSTI O PROJEKTU*: Vyberte předplatné Azure, pak vyberte nebo vytvořte skupinu prostředků Azure, jako *myResourceGroup*. Zadejte **Název clusteru Kubernetes**, například *myAKSCluster*.
- *PODROBNOSTI O CLUSTERU*: Vyberte oblast, verze Kubernetes a předpona názvu DNS pro AKS cluster.
- *FONDU PRIMÁRNÍHO UZLU*: Vyberte velikost virtuálního počítače pro uzly AKS. Velikost virtuálního počítače **nejde** změnit po nasazení clusteru AKS.
     - Vyberte počet uzlů, které se mají do clusteru nasadit. Pro účely tohoto článku nastavte **počet uzlů** k *1*. Počet uzlů **jde** upravit po nasazení clusteru.

Klikněte na tlačítko **Další: Škálování**.

Na **škálování** stránce *povoleno* pod **virtuální uzly**.

![Vytvoření clusteru AKS a povolení virtuální uzly](media/virtual-nodes-portal/enable-virtual-nodes.png)

Ve výchozím nastavení je vytvoření instančního objektu služby Azure Active Directory. Tento instanční objekt se používá pro komunikaci s clustery a integrace s dalšími službami Azure.

Cluster je také nakonfigurovaný pro rozšířeného sítě. Virtuální uzly jsou nakonfigurovány pro použití vlastní podsíti virtuální sítě Azure. Tato podsíť obsahuje delegovaná oprávnění k připojení prostředků Azure mezi clusterem AKS. Pokud ještě nemáte delegované podsíť, na webu Azure portal vytvoří a nakonfiguruje virtuální sítí Azure a podsítě pro použití s virtuálními uzly.

Vyberte **Zkontrolovat a vytvořit**. Po dokončení ověřování vyberte **vytvořit**.

Vytvoření clusteru AKS a jeho příprava k použití trvá několik minut.

## <a name="connect-to-the-cluster"></a>Připojení ke clusteru

Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. Ke správě clusteru Kubernetes použijte klienta příkazového řádku Kubernetes [kubectl][kubectl]. Klient `kubectl` je předinstalovaný ve službě Azure Cloud Shell.

Chcete-li spustit Cloud Shell, vyberte **vyzkoušet** v pravém horním rohu bloku kódu. Cloud Shell můžete spustit také na samostatné kartě prohlížeče na adrese [https://shell.azure.com/bash](https://shell.azure.com/bash). Zkopírujte bloky kódu výběrem možnosti **Kopírovat**, vložte je do služby Cloud Shell a potom je spusťte stisknutím klávesy Enter.

Použití [az aks get-credentials][az-aks-get-credentials] příkaz, který konfiguruje `kubectl` pro připojení k vašemu clusteru Kubernetes. Následující příklad získá přihlašovací údaje pro název clusteru *myAKSCluster* ve skupině prostředků *myResourceGroup*:

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

Ve službě Azure Cloud Shell, vytvořte soubor s názvem `virtual-node.yaml` a zkopírujte do následující kód YAML. Naplánování kontejneru na uzlu, [nodeSelector][node-selector] and [toleration][toleration] jsou definovány. Tato nastavení umožňují pod naplánovat na virtuální uzel a potvrďte, že tato funkce se úspěšně povolilo.

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

Spusťte aplikaci [použití kubectl][kubectl-apply] příkazu.

```azurecli-interactive
kubectl apply -f virtual-node.yaml
```

Použití [kubectl get pods][kubectl-get] příkazů `-o wide` argument do výstupního seznam podů a plánované uzlu. Všimněte si, že `virtual-node-helloworld` pod byla naplánována na `virtual-node-linux` uzlu.

```
$ kubectl get pods -o wide

NAME                                     READY     STATUS    RESTARTS   AGE       IP           NODE
virtual-node-helloworld-9b55975f-bnmfl   1/1       Running   0          4m        10.241.0.4   virtual-node-aci-linux
```

Pod přiřazena vnitřní IP adresu z podsítě virtuální sítě Azure delegované pro použití s virtuálními uzly.

> [!NOTE]
> Pokud používáte Image uložená ve službě Azure Container Registry, [nakonfigurovat a používat tajného kódu Kubernetes][acr-aks-secrets]. Aktuální omezení virtuální uzly je, že nemůžete použít Azure integrované ověřování instančních objektů služby AD. Pokud nepoužíváte tajného klíče, pody naplánována na virtuální uzly nepodaří spustit a nahlaste jim chybu `HTTP response status code 400 error code "InaccessibleImage"`.

## <a name="test-the-virtual-node-pod"></a>Testovat virtuální uzel pod

K otestování spuštěný na virtuální uzel pod, přejděte na ukázkovou aplikaci pomocí webového klienta. Jak chcete pod přiřazena vnitřní IP adresu, můžete rychle otestovat toto připojení z jiného podu na clusteru AKS. Vytvoření testů pod a připojit se k němu Terminálové relaci:

```azurecli-interactive
kubectl run -it --rm virtual-node-test --image=debian
```

Nainstalujte `curl` v podu pomocí `apt-get`:

```azurecli-interactive
apt-get update && apt-get install -y curl
```

Nyní přístup k adrese pod pomocí `curl`, jako například *http://10.241.0.4* . Zadejte vlastní interní IP adresa je znázorněno v předchozí `kubectl get pods` příkaz:

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
- [Projděte si ukázku automatického škálování pro virtuální uzly][virtual-node-autoscale]
- [Další informace o Virtual Kubelet opensourcovou knihovnu][virtual-kubelet-repo]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[node-selector]:https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[toleration]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[aks-github]: https://github.com/azure/aks/issues]
[virtual-node-autoscale]: https://github.com/Azure-Samples/virtual-node-autoscale
[virtual-kubelet-repo]: https://github.com/virtual-kubelet/virtual-kubelet

<!-- LINKS - internal -->
[aks-network]: ./networking-overview.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[aks-hpa]: tutorial-kubernetes-scale.md
[aks-cluster-autoscaler]: cluster-autoscaler.md
[aks-basic-ingress]: ingress-basic.md
[acr-aks-secrets]: ../container-registry/container-registry-auth-aks.md#access-with-kubernetes-secret
[az-provider-list]: /cli/azure/provider#az-provider-list