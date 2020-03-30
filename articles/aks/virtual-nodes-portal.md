---
title: Vytváření virtuálních uzlů pomocí portálu ve službách Azure Kubernetes Services (AKS)
description: Zjistěte, jak pomocí portálu Azure vytvořit cluster Služeb Azure Kubernetes (AKS), který ke spouštění podů používá virtuální uzly.
services: container-service
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: ea93ea4a68fad213fe5bd1dc61abcb2deaef2c9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473587"
---
# <a name="create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes-in-the-azure-portal"></a>Vytvoření a konfigurace clusteru Služeb Azure Kubernetes (AKS) pro použití virtuálních uzlů na webu Azure Portal

Chcete-li rychle nasadit úlohy v clusteru Služby Azure Kubernetes (AKS), můžete použít virtuální uzly. S virtuálníu uzly máte rychlé zřizování podů a platí pouze za sekundu pro jejich spuštění. Ve scénáři škálování není nutné čekat na automatické škálování clusteru Kubernetes nasadit výpočetní uzly virtuálních počítače ke spuštění dalšípody. Virtuální uzly jsou podporovány pouze s pody Linuxa a uzly.

Tento článek ukazuje, jak vytvořit a nakonfigurovat prostředky virtuální sítě a cluster AKS s povolenými virtuálními uzly.

## <a name="before-you-begin"></a>Než začnete

Virtuální uzly umožňují síťovou komunikaci mezi pody, které běží v Azure Container Instances (ACI) a clusteru AKS. Pro poskytnutí této komunikace je vytvořena podsíť virtuální sítě a jsou přiřazena delegovaná oprávnění. Virtuální uzly fungují pouze s clustery AKS vytvořenými pomocí *pokročilých* sítí. Ve výchozím nastavení jsou clustery AKS vytvářeny se *základní* sítí. Tento článek ukazuje, jak vytvořit virtuální síť a podsítě a potom nasadit cluster AKS, který používá rozšířené sítě.

Pokud jste dříve aci nepoužívali, zaregistrujte poskytovatele služeb pomocí předplatného. Stav registrace zprostředkovatele ACI můžete zkontrolovat pomocí příkazu [seznamu zprostředkovatele az,][az-provider-list] jak je znázorněno v následujícím příkladu:

```azurecli-interactive
az provider list --query "[?contains(namespace,'Microsoft.ContainerInstance')]" -o table
```

Zprostředkovatel *Microsoft.ContainerInstance* by měl hlásit jako *Registrovaný*, jak je znázorněno na následujícím příkladu výstupu:

```output
Namespace                    RegistrationState
---------------------------  -------------------
Microsoft.ContainerInstance  Registered
```

Pokud se poskytovatel zobrazí jako *NotRegistered*, zaregistrujte zprostředkovatele pomocí [registru zprostředkovatele az,][az-provider-register] jak je znázorněno v následujícím příkladu:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerInstance
```

## <a name="regional-availability"></a>Regionální dostupnost

Pro nasazení virtuálních uzlů jsou podporovány následující oblasti:

* Austrálie východ (australiaeast)
* Centrální USA (centralus)
* Východní USA (eastus)
* Východní USA 2 (eastus2)
* Japonsko východ (japaneast)
* Severní Evropa (severní Evropa)
* Jihovýchodní Asie (jihovýchodní Asie)
* Západní střed USA (westcentralus)
* Západní Evropa (západní Evropa)
* Západní USA (westus)
* Západní USA 2 (westus2)

## <a name="known-limitations"></a>Známá omezení
Funkce virtuálních uzlů je silně závislá na sadě funkcí ACI. Následující scénáře ještě nejsou podporovány s virtuálními uzly

* Použití instančního objektu k vytahovat obrazy ACR. [Řešení](https://github.com/virtual-kubelet/virtual-kubelet/blob/master/providers/azure/README.md#Private-registry) je použití [tajných kódů Kubernetes](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/#create-a-secret-by-providing-credentials-on-the-command-line)
* [Omezení virtuální sítě,](../container-instances/container-instances-vnet.md) včetně partnerského vztahu virtuální sítě, zásady sítě Kubernetes a odchozího provozu na Internet se skupinami zabezpečení sítě.
* Init kontejnery
* [Hostitelské aliasy](https://kubernetes.io/docs/concepts/services-networking/add-entries-to-pod-etc-hosts-with-host-aliases/)
* [Argumenty](../container-instances/container-instances-exec.md#restrictions) pro exec v ACI
* [DaemonSets](concepts-clusters-workloads.md#statefulsets-and-daemonsets) nebude nasazovat pody do virtuálního uzlu
* [Uzly systému Windows Server (aktuálně ve verzi preview v AKS)](windows-container-cli.md) nejsou podporovány vedle virtuálních uzlů. Virtuální uzly můžete použít k plánování kontejnerů systému Windows Server bez nutnosti uzlů Systému Windows Server v clusteru AKS.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="create-an-aks-cluster"></a>Vytvoření clusteru AKS

V levém horním rohu portálu Azure vyberte **Vytvořit prostředek** > **Kubernetes Service**.

Na stránce **Základy** nakonfigurujte následující možnosti:

- *PODROBNOSTI O PROJEKTU:* Vyberte předplatné Azure a pak vyberte nebo vytvořte skupinu prostředků Azure, například *myResourceGroup*. Zadejte **Název clusteru Kubernetes**, například *myAKSCluster*.
- *PODROBNOSTI O CLUSTERU:* Vyberte oblast, verzi Kubernetes a předponu názvu DNS pro cluster AKS.
- *FOND PRIMÁRNÍCH UZLŮ:* Vyberte velikost virtuálního počítače pro uzly AKS. Velikost virtuálního počítače **nejde** změnit po nasazení clusteru AKS.
     - Vyberte počet uzlů, které se mají do clusteru nasadit. Pro tento článek nastavte **počet uzlů** na *1*. Počet uzlů **jde** upravit po nasazení clusteru.

Klikněte na **Další: Měřítko**.

Na stránce **Měřítko** vyberte *Povoleno v* části **Virtuální uzly**.

![Vytvoření clusteru AKS a povolení virtuálních uzlů](media/virtual-nodes-portal/enable-virtual-nodes.png)

Ve výchozím nastavení se vytvoří zaregistrovaný objekt služby Azure Active Directory. Tento instanční objekt služby se používá pro komunikaci clusteru a integraci s jinými službami Azure.

Cluster je také nakonfigurován pro pokročilou síť. Virtuální uzly jsou nakonfigurované tak, aby používaly vlastní podsíť virtuální sítě Azure. Tato podsíť má delegovaná oprávnění k propojení prostředků Azure mezi clusterem AKS. Pokud ještě nemáte delegovanou podsíť, portál Azure vytvoří a nakonfiguruje virtuální síť Azure a podsíť pro použití s virtuálními uzly.

Vyberte **Zkontrolovat a vytvořit**. Po dokončení ověření vyberte **Vytvořit**.

Vytvoření clusteru AKS a jeho příprava k použití trvá několik minut.

## <a name="connect-to-the-cluster"></a>Připojení ke clusteru

Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. Ke správě clusteru Kubernetes použijte klienta příkazového řádku Kubernetes [kubectl][kubectl]. Klient `kubectl` je předinstalovaný ve službě Azure Cloud Shell.

Chcete-li otevřít prostředí Cloud Shell, vyberte **Vyzkoušet** v pravém horním rohu bloku kódu. Cloud Shell můžete spustit také na samostatné [https://shell.azure.com/bash](https://shell.azure.com/bash)kartě prohlížeče tak, že přejdete na . Zkopírujte bloky kódu výběrem možnosti **Kopírovat**, vložte je do služby Cloud Shell a potom je spusťte stisknutím klávesy Enter.

Pomocí příkazu [az aks get-credentials][az-aks-get-credentials] nakonfigurujte klienta `kubectl` pro připojení k vašemu clusteru Kubernetes. Následující příklad získá přihlašovací údaje pro název clusteru *myAKSCluster* ve skupině prostředků *myResourceGroup*:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Pokud chcete ověřit připojení ke clusteru, použijte příkaz [kubectl get][kubectl-get], který vrátí seznam uzlů clusteru.

```console
kubectl get nodes
```

Následující příklad výstupu ukazuje jeden uzel virtuálního počítače vytvořené a pak virtuální uzel pro Linux, *virtual-node-aci-linux*:

```output
NAME                           STATUS    ROLES     AGE       VERSION
virtual-node-aci-linux         Ready     agent     28m       v1.11.2
aks-agentpool-14693408-0       Ready     agent     32m       v1.11.2
```

## <a name="deploy-a-sample-app"></a>Nasazení ukázkové aplikace

V prostředí Azure Cloud Shell `virtual-node.yaml` vytvořte soubor s názvem a zkopírujte v následujícím yaml. Chcete-li naplánovat kontejner na uzlu, jsou [definovány nodeSelector][node-selector] a [tolerace.][toleration] Tato nastavení umožňují pod naplánovat na virtuální uzel a potvrďte, že funkce je úspěšně povolena.

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

Spusťte aplikaci pomocí příkazu [kubectl apply.][kubectl-apply]

```azurecli-interactive
kubectl apply -f virtual-node.yaml
```

Pomocí příkazu [get pods kubectl][kubectl-get] s argumentem `-o wide` vyjádřujte seznam podů a naplánovaný uzel. Všimněte `virtual-node-helloworld` si, že pod `virtual-node-linux` byl naplánován na uzlu.

```console
kubectl get pods -o wide
```

```output
NAME                                     READY     STATUS    RESTARTS   AGE       IP           NODE
virtual-node-helloworld-9b55975f-bnmfl   1/1       Running   0          4m        10.241.0.4   virtual-node-aci-linux
```

Podu je přiřazena interní IP adresa z podsítě virtuální sítě Azure delegovaná pro použití s virtuálními uzly.

> [!NOTE]
> Pokud používáte image uložené v Azure Container Registry, [nakonfigurujte a použijte tajný klíč Kubernetes][acr-aks-secrets]. Aktuální omezení virtuálních uzlů je, že nelze použít integrované ověřování instančního objektu Azure AD. Pokud nepoužíváte tajný klíč, pody naplánované na virtuálních uzlech se nespustí a ohlásí chybu `HTTP response status code 400 error code "InaccessibleImage"`.

## <a name="test-the-virtual-node-pod"></a>Testování podu virtuálního uzlu

Chcete-li otestovat pod spuštěný na virtuálním uzlu, přejděte na ukázkovou aplikaci s webovým klientem. Vzhledem k tomu, že podu je přiřazena interní IP adresa, můžete toto připojení rychle otestovat z jiného podu v clusteru AKS. Vytvořte testovací modul a připojte k němu terminálovou relaci:

```console
kubectl run -it --rm virtual-node-test --image=debian
```

Nainstalujte `curl` do `apt-get`modulu pomocí :

```console
apt-get update && apt-get install -y curl
```

Nyní přístup k adrese `curl`pod pomocí *http://10.241.0.4*, například . Zadejte vlastní interní IP adresu `kubectl get pods` uvedenou v předchozím příkazu:

```console
curl -L http://10.241.0.4
```

Ukázková aplikace je zobrazena, jak je znázorněno na následujícím kondenzovaném příkladu výstupu:

```output
<html>
<head>
  <title>Welcome to Azure Container Instances!</title>
</head>
[...]
```

Zavřete terminálovou relaci testovacího modulu pomocí aplikace `exit`. Po ukončení relace je pod odstraněn.

## <a name="next-steps"></a>Další kroky

V tomto článku pod byl naplánován na virtuální uzel a přiřazena soukromá, interní IP adresa. Místo toho můžete vytvořit nasazení služby a směrovat provoz do podu prostřednictvím správce zatížení nebo řadiče příchozího přenosu dat. Další informace naleznete [v tématu Vytvoření základního řadiče příchozího přenosu dat v AKS][aks-basic-ingress].

Virtuální uzly jsou jednou součástí škálovacího řešení v AKS. Další informace o škálovacích řešeních naleznete v následujících článcích:

- [Použití automatického měřítka kubernetesových horizontálních podů][aks-hpa]
- [Použití automatického škálování clusteru Kubernetes][aks-cluster-autoscaler]
- [Podívejte se na ukázku automatického škálování pro virtuální uzly][virtual-node-autoscale]
- [Přečtěte si více o open source knihovně Virtual Kubelet][virtual-kubelet-repo]

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
[acr-aks-secrets]: https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/

<!-- LINKS - internal -->
[aks-network]: ./networking-overview.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[aks-hpa]: tutorial-kubernetes-scale.md
[aks-cluster-autoscaler]: cluster-autoscaler.md
[aks-basic-ingress]: ingress-basic.md
[az-provider-list]: /cli/azure/provider#az-provider-list
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register
